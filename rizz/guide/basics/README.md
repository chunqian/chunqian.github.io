# **_rizz_ Architecture and Basic usage**  

<style type="text/css">
img.vw_45 {
    width: 45vw;
}
img.vw_30 {
    width: 30vw;
}
</style>

This document is subject to change  
Revision 1.16: 10 December 2020  

**Github Page**: [github.com/septag/rizz](https://github.com/septag/rizz)

In this document, I will give you an overview of _rizz_ architecture, it's design philosophy and basic guidelines and rules on how you can start creating applications and write plugins for it.

_rizz_ is a small C framework to give the developers a head start in multi-platform applications. It is tailored towards flexibility and performance, especially for game development and high-performance applications.

# Design Principles
The design principles are pretty much same as [OurMachinery](https://ourmachinery.com/files/guidebook.md.html#omg-design:designprinciples), so I basically copy/paste the main bullet points here, with some minor exceptions:

- **Code must be easy to change:** In order to be able to adapt to change quickly, I assumed that, there is no specific use case for the framework, instead, the programmer is responsible to make his building blocks on top of the _rizz_ framework from scratch. This keeps the core of the engine small and flexible enough and changes will mostly come in user side instead of the engine itself.
- **Less is more:** I take special care keeping the code size of the project small. As a single solo developer, it's pretty hard to maintain a big code-base especially on multiple platforms. So, _rizz_ has minimum amount of 3rdparty libraries, and the core of the engine is kept small and flexible enough for developers to add extra functionality when they need it.
- **Keep it simple:** C language being minimal and small helps a lot in this regard. Care is taken to keep the source code simple and have less abstractions than most C++ counterparts. Most data structures consist of simple dynamic arrays and tricky optimizations will only be applied if it's proven to be a bottleneck.
- **Explicit is better than implicit:** Fortunately, there are no C++ idioms to make this matter worse, but there are some macros here and there to make things simple for developers, but I wouldn't go overboard with this and will keep the code as much explicit as possible.
- **Design with performance in mind:** As I mentioned before, _rizz_ is designed to be performant, the performance will be definitely improved on next iterations with more profiling in real-world tests. But the idea is to prefer data-oriented practices and performance to beginner friendliness and ease of use.
- **Design for fast iterations:** Build times are kept at minimum due to the code being small and not dependent on many 3rdparty libraries. C/C++ live code reloading is supported with the plugin system and all assets can be hot-reloaded on demand. It's an important part of _rizz_ development to lessen the iteration times and it will continue improve upon this.
- ~~**Deliver changes quickly**~~: I'm a solo developer and this project is part time, so I won't be able to deliver changes quickly as expected, but changes and bug fixes will mostly come in _master_ branch immediately after they are fixed and all of the examples would be working correctly after a little while. 
- **Avoid Coupling:** Almost every part of the engine can be abstracted and ripped out on demand because the most basic parts of it are abstracted away and presented through a middle API, like graphics backends or task manager. Most of other features are implemented as plugins anyways, so there would be no coupling with other systems.
- **Everything is a plugin:** This is actually a solution to many problems, keeping the code small and the code being easy to change. So, every feature in the engine will be implemented as a plugin, like different sound engines, physics, renderers, etc. This approach makes the engine small and flexible as possible for different use cases and projects.
- **Follow Data oriented design principles:** Many of the APIs and internal data is designed around being as hardware friendly as possible, especially regarding memory access and multi-threading. For example graphics and sound APIs are designed to be running on multiple threads as a default behavior.
- ~~**Take advantage of modern rendering architecture features**~~: Unfortunately, as a solo indie developer, this is a pretty hard thing to do. Also I'm using the lightweight sokol_gfx for each platform's backend, and it doesn't support the most modern GPU features and APIs, which is not bad at all for small/indie-ish projects. However, some useful and somewhat essential modern features like compute-shaders and multi-threaded command buffers are currently being done.
- ~~**Shared responsibility**~~: Currently, I'm the only programmer on this project and obviously it's my responsibility to do all the stuff. If the project grows and more developers want to join or contribute, then we will try to implement this more seriously.
- **Write for readability:** Well, In my opinion, C language helps a lot here (although it's very much debatable by C++ fans), but overall, we basically have functions and structs everywhere. There are no fancy syntaxes and idioms, there are no multiple abstraction layers that you have to jump to, naming and coding is kept consistent and number of source files are kept to minimum. 

So, to sum things up, _rizz_ is not meant to be a full featured engine, and using it is not going to be easy for people that are not interested or not familiar with the inner workings of game engines, there are much better hugely successful commercial and polished game engines for that purpose like [Unreal](https://www.unrealengine.com/en-US/) and [Unity](https://unity.com). 

Instead, _rizz_ targets mainly C/C++ programmers who are interested in having more control over the technology and want to work with small and manageable code bases. It basically provides the skeleton and main building blocks of multi-platform high-performance application development and presents extra functionality through plugins which are all optional.

# Builds
Much of the build instructions are described in [README.md](https://github.com/septag/rizz/blob/master/README.md#build). But I will describe more details on some build internals.

First of all, the project uses [_cmake_](https://cmake.org) as it's primary build system, so make sure you have the latest _cmake_ version installed on your development system.

_rizz_ is divided into five main groups, which can be seen in the figure below:

<!-- ![Figure [build-chart]: build chart](https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-build-chart.png) -->
<img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-build-chart.png" class="vw_45" />

- **sx (foundation library)**: built as static library. This is the foundation library that abstracts system level functionality, like threads, basic vector math, OS, memory management, etc. This library is almost linked with every other project directly. It's relationship with other modules is shown with straight arrow lines in figure #1.
- **rizz**: Can be built as stand-alone executable or static library based on wither we are building in BUNDLE mode or not. I will get to that later in this section. Other plugins and games usually depend on this module and uses it's various APIs for their purposes. The dotted lines in figure #1 indicates module depends on this but linking is not necessary.
- **plugins**: Additional plugins that provide extra functionality. These plugins can be either made by 3rdparty developers or included in the _rizz_ project. They usually link to _sx_ library and depends on _rizz_ APIs.
- **tools**: These are mostly python scripts and independent small tools that are integrated into project. Currently, one example of tools are build configuration scripts that facilitate _iOS_ and _android_ project preparation and build.
- **examples/games**: There are multiple examples included with the project. Also new apps or games goes into this category. They can either be built as stand-alone executable or dynamic libraries, depending on BUNDLE flag.

## "Host" vs. "BUNDLE" build
**Host build**, which is the default mode on desktop OSes (Windows/Linux/MacOS), builds _rizz_ project as an executable host program. It hosts other plugins and the application itself as dynamic modules, so they can all be reloaded on change. This is default on desktop systems because it provides live C/C++ module reloading and is better suited for development setup.

<!-- ![Figure [build-host]: host build model](https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-host-model.png) -->
<img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-host-model.png" class="vw_30" />

**BUNDLE build**, which is the default mode on mobile OSes (android/iOS), builds _rizz_ and all plugins as static libraries, then links them to android's game dynamic library or iOS game bundle. This is mostly suitable for deployment, because it provides a single stand-alone executable. To enable this mode, you have to set `BUNDLE=1` and define bundle properties like the example below:

```
cmake .. -DBUNDLE=1 -DBUNDLE_TARGET_NAME=your_project -DBUNDLE_PLUGINS="imgui;sound"
```

In this example, we set our project (name is `your_project`) to build as _BUNDLE_ mode and indicates that the project requires _imgui_ and _sound_ plugins.

<!-- ![Figure [build-bundle]: _BUNDLE_ build model](https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-bundle-model.png) -->
<img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-bundle-model.png" class="vw_30" />

## pre-built binary tools
There are bunch of command line utilities included in the project as a pre-built binary. Of course they are all open source projects and can be built by fetching their source code independently. But I included their binaries for convenience and not to clutter the project. These tools reside in `tools/${platform}` directory.

- **[glslcc](https://github.com/septag/glslcc):** GLSL cross-compiler tool. All the shaders are built with this tool.
- **[atlasc](https://github.com/septag/atlasc):** Builds atlas texture from a bunch of input images. In case you are using _sprite_ feature, it can be a useful tool to put multiple images into a bigger atlas and make sprite meshes from them.
- **[basisu](https://github.com/BinomialLLC/basis_universal):** Basis Universal GPU Texture Codec. This tool can be used to create *.basis* texture files that the engine supports. *.basis* textures are super small and can be transcoded to any native compression format on demand. So they are almost a perfect choice for multiplatform image deployment.

## ISPC (high-level SIMD programming)
_rizz_ provides a [cmake module](https://github.com/septag/rizz/blob/master/cmake/ispc.cmake) to integrate ispc files into your projects. ispc compiler is not included in the project, so you have to download it.

The cmake module will try to find the compiler binary if it's in the PATH, if it's not, you should provide the path to cmake with ISPC_PATH variable: 

```
cmake .. -DISPC_PATH=/path/to/ispc 
```

ISPC files can be added to project with `ispc_target_add_files` function. Also there are other parameters that are described in the module in more detail. Here's an example:

```py
# set global variables
set(ISPC_OUTPUT_DIRECTORY /path/to/object/files)
set(ISPC_INCLUDE_DIRS /path/to/ispc/includes)
set_source_files_properties(test.ispc PROPERTIES ISPC_COMPILE_DEFINITIONS "USE_METHOD_2")
ispc_target_add_files(target-name test.ispc)
```

In this example, we have added our `test.ispc` to the project *(target-name)*, the source will have *USE_METHOD_2* definition. The output directory of compiled object files will be in ISPC_OUTPUT_DIRECTORY variable and include search directory extracted from ISPC_INCLUDE_DIRS variable.

## Cross build scripts

There are currently two python build scripts that facilitate cross builds:

- **[android.py](https://github.com/septag/rizz/blob/master/scripts/build-tools/android.py)**: Android development is actually very frustrating, so I provided a python scripts that configures the project from ground up, builds, makes APK and deploys it on the device for testing. more detailed documentation is provided inside the file.
- **[ios.py](https://github.com/septag/rizz/blob/master/scripts/build-tools/ios.py)**: This scripts essentially sets up an iOS from ground up, provides placeholder plist files, icons and launch images and Xcode project. After that you can just replace the images, edit the plist, and build or debug your app in xcode.

# Memory management
Every dynamic memory allocation in _rizz_ framework is passed through custom allocator provided by the framework itself. So it is recommended that you never use _malloc_ or C++ _new_ operator, unless it's using the allocated memory from the custom allocators like the example below:

```cpp
void* buffer = sx_malloc(the_core->heap_alloc(), sizeof(MyClass));
// construction
MyClass* clz = new(buffer) MyClass();
// destruction
clz->~MyClass();
sx_free(the_core->heap_alloc(), buffer);
```

There are multiple functions that facilitate memory allocators, along with various allocators provided by the framework. 

To allocate memory, there are multiple macros that help you with this, and they all require custom allocator pointer, which I will describe below. See [sx/allocator.h](https://github.com/septag/rizz/blob/master/include/sx/allocator.h) for details. Overall, you should use `sx_malloc`/`sx_free`/`sx_realloc` for normal allocations, and `sx_aligned_malloc`/`sx_aligned_free`/`sx_aligned_realloc` for aligned allocations.

Here are built-in allocators, available through **core** API:
- **heap_alloc()**: Thread-safe general allocator, detects leaks when `rizz_config.core_flags` has RIZZ_CORE_FLAG_DETECT_LEAKS flag. Otherwise, it's essentially malloc.
- **alloc(id)**: Gets _tagged_ allocators. Every allocation can be tagged and monitored with imgui plugin's *memory_debugger*. For example, graphics, sound and physics can go through different allocators so you can trace each subsystem memory. To see current pre-defined Ids, see `rizz_mem_id` in [core.h](https://github.com/septag/rizz/blob/master/include/rizz/core.h)

## Temp allocations
Temp allocators are a bit tricky. They are simple linear based allocators, which is very fast with minimal defragmentation. Also, they are multi-threaded if you use them in the framework's job dispatcher threads.  

To use temp allocations, you should use **tmp_alloc_push** and **tmp_alloc_pop** in stack like order. **tmp_alloc_push** pushes new item in temp allocator stack, and **tmp_alloc_pop** pops it back. The reason behind this is that the amount of temp memory is limited (5mb default) and can be set on app config, so we have to reuse the memory to prevent buffer overflow. 

Another important note is that you can not use memory across frames. On the start of each frame, all temp allocators will be reset.

The tricky part is that you have to be careful to call _pop_ for every _push_ in your functions, and also never reuse the memory you allocated after the _pop_ call.

Here's an example of temp allocator usage:

```c
static rizz_api_core* the_core;     // core API

static void foo()
{
    const sx_alloc* alloc = the_core->tmp_alloc_push();
    void* buffer = sx_malloc(alloc, 1024);     
    // use buffer and get done with it
    the_core->tmp_alloc_pop();
}

int main()
{
    const sx_alloc* alloc = the_core->tmp_alloc_push();
    void* buffer = sx_malloc(alloc, 4096);
    while (!quit) {
        foo();
    }
    the_core->tmp_alloc_pop();  
    return 0;
}
```    

# Subsystems
_rizz_ contains several sub-systems, they are kept minimal and extra sub-systems will are presented out of rizz library as plugins. Each sub-system will likely have an API interface, they are essentially _structs_ with function pointers:

```
typedef struct rizz_api_core {
    void (*get_mem_info)(rizz_mem_info* info);
} rizz_api_core;
```

These APIs can be fetched in main entry of the plugin/app with `get_api` function, you can inspect examples to see it's usage in the program. It is very much like the design described in _OurMachinery_.

All the sub-systems and their relationships are shown in the figure below:

<!-- ![Figure [systems]: sub-systems](https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-systems.png) -->
<img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-systems.png" class="vw_45" />

- **core (rizz_api_core)**: core handles many primary functionality, like job dispatching, memory allocators, logging, profiling, etc. Almost all of other modules and sub-systems use this module.
- **app (rizz_api_app)**: This is the main entry of the program, creates the app and it's window, handles message loop, GPU device contexts and stuff like that. This process is invisible to user, but there is an API which you can query or control certain aspects of the program, like getting window dimensions, showing mouse pointer, quit the application and functions related to the main app.
- **graphics (rizz_api_gfx)**: This is the main graphic subsystem and provides lowest level of functionality for graphics, which it's API, you can submit commands to GPU. It also registers loading several graphic assets with the asset manager.
- **asset (rizz_api_asset)**: Handles managing and loading assets and hot-reloads assets on devel mode, it works asynchronously by default, however, you can set a flag and load assets in blocking mode. We will get to that later in this document.
- **plugin (rizz_api_plugin)**: Plugin system, handles and reloads all plugins. This include the game module itself.
- **reflection (rizz_api_refl)**: There is a simple reflection sub-system that you can register your data types with it and enumerate them later.

There are also external libraries that are independent from the _rizz_ and you can use them wherever in your code. The main one is **sx**, the foundation library that I described earlier. It is linked to all child projects by default.

# Plugins and Apps
Plugins (and apps) are common dynamic library (.so/.dylib/.dll) files that have specific descriptor functions that can be detected by _rizz_ plugin system. I will describe in more detail how to implement new plugins in the below section. Basically, the main game or app is just another _special_ plugin and almost behaves like one. Also, all plugins are auto reloaded when they are being overwritten on disk in non _BUNDLE_ mode. 

Plugins have various events which is triggered by the host (_rizz_) through a callback function:
- **STEP**: This is essentially where _update_ happens, and is triggered on every frame.
- **INIT**: Triggers the _first_ time the plugin is initialized. This is where you can initialize your objects.
- **LOAD**: Triggers on plugin reloads. The host is running, but plugin is overwritten, so this event is triggered to do any house keeping or renewing lost stuff during reload.
- **UNLOAD**: Triggers right before plugin wants to reload. _LOAD_ event complements this.
- **SHUTDOWN**: Triggers when host (_rizz_) and the application is being shutdown completely. You can do your main cleanup here.

The plugin system accepts a base directory where all of the plugins should be located. Then the game or other plugins define the plugins they would need. After this, a dependency graph will be created in the plugin system in order to update, load and unload each plugin in order considering their parent/child relationship. Then, the main loop will update game/application after all plugins are updated.

<!-- ![Figure [plugins]: Plugin dependency graph example. Order of execution is shown with arrows for each event](https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-plugins.png) -->
<img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-plugins.png" class="vw_45" />

As you can see in the example above, our game uses _sound_, _input_ and _sprite_ plugins, and every one of those plugins, depend on _imgui_ for their debugging functionality, so the blue squares shows plugin dependency graph. When _update_ and _init_ events happen, they are triggered by the host and goes from top to bottom of the hierarchy. But _shutdown_ event will execute in reverse order and starts with the game itself then goes up to the hierarchy.

## Writing plugins
To make plugins for _rizz_, you can start by creating an entry-point source file and implement required functions for it. There are macros to facilitate the creation of such functions:

- `rizz_plugin_implement_info(PLUGIN_NAME, VERSION, DESCRIPTION, DEPENDENCIES, NUMBER_OF_DEPENDENCIES)`: This basically defines the plugin info function. The host, calls this function to gather essential information about the plugin, such as dependencies and version info.
    - PLUGIN_NAME: name of the plugin. it should be without double quotes, with no spaces and actually the name of the plugin's root source folder for convenience.
    - VERSION: version integer. The format is like 'MNNF', 'M' is for major version, 'NN' is for minor version and F is for fix updates. (example: 1012 = version 1.1.2)
    - DESCRIPTION: a description string 
    - DEPENDENCIES: a string array of the names of plugin dependencies. 
    - NUMBER_OF_DEPENDENCIES: number of items in the array
- `rizz_plugin_decl_main(PLUGIN_NAME, PLUGIN_PARAM_NAME, EVENT_PARAM_NAME)`: This is the main loop for the plugin which receives program events such as INIT and SHUTDOWN (as described above). 
- `rizz_plugin_decl_event_handler(PLUGIN_NAME, EVENT_PARAM_NAME)`: (optional) This is additional event handler that receives window/app level events, such as SUSPEND/MOUSE/KEYBOARD/RESIZE/etc in case your plugin needs them. 

Here's an example of the minimal entry points for a plugin named 'box2d':

```cpp
typedef struct box2d_api {
    b2World* (*createWorld)(void);
    void (*destroyWorld)(b2World* world);
} box2d_api;

static box2d_api the__box2d;

rizz_plugin_decl_main(box2d, plugin, e)
{
    // 'plugin' = rizz_plugin*
    // 'e' = rizz_plugin_event
    switch (e) {
        case RIZZ_PLUGIN_EVENT_STEP:
            updateBox2d();
            break;
        case RIZZ_PLUGIN_EVENT_INIT:
            // Initialize APIs
            the_plugin = plugin->api;
            the_core = plugin->api->get_api(RIZZ_API_CORE, 0);
            the_imgui = the_plugin->get_api_byname("imgui", 0);

            if (!initBox2d()) {
                return -1;
            }

            // Inject physics API into the engine. You can inject multiple APIs
            plugin->api->inject_api("box2d", 0, &the__box2d);
            break;
        case RIZZ_PLUGIN_EVENT_LOAD:
            // re-inject physics API into the engine. 
            plugin->api->inject_api("box2d", 0, &the__box2d);
            break;
        case RIZZ_PLUGIN_EVENT_SHUTDOWN:
            plugin->api->remove_api("box2d", 0);
            releaseBox2d();
            break;
    }

    return 0;   // success
}


rizz_plugin_decl_event_handler(box2d, e)
{
    // e = const rizz_app_event* 
    if (e->type == RIZZ_APP_EVENTTYPE_UPDATE_APIS) {
        // refresh the 'imgui' plugin API, because it is not built-in and maybe reloaded while the program is running
        the_imgui = the_plugin->get_api_byname("imgui", 0);
    }
}

static const char* box2d_deps[] = { "imgui" };
rizz_plugin_implement_info(box2d, 1000, "box2d physics plugin", box2d_deps, 1);
```

### Application/Game
Games and Programs hosted by _rizz_ are actually another type of plugins. They need the same boilerplate code that the plugin needs, but instead of `rizz_plugin_implement_info`, we would need a *game config* function. The function can be defined by using `rizz_gamd_decl_config` helper macro:

```
rizz_game_decl_config(conf)
{
    // conf = rizz_config*
    // leave any field to initialize them with default values. see rizz_config struct
    conf->app_name = "my_game";
    conf->app_version = 1000;
    conf->app_title = "My First Game!";
    conf->window_width = 800;
    conf->window_height = 600;
    // fill plugins array with the name of the plugins that your program requires
    conf->plugins[0] = "imgui"; 
}
```

*Note*: Alternatively, you can also use *rizz_game_decl_config_cmdline* macro to get register_cmdline_arg function. This can be used for registering custom command-line arguments with your application. (see [User-defined arguments](#profiling/commandlinearguments/user-definedarguments))


### Deployment 
Recommended deployment method for plugins is obviously open-source and on GIT repos. First, consider naming your plugins as the same folder that you put their source code in. So for example if your plugin name is "box2d", then name your source folder to "box2d". Secondly, always put `CMakeLists.txt` file on the root folder of your plugin source folder, so the build system can find it without problems.

You can use the little bash command line script `plugin.sh` to add and remove external plugins to your rizz source repo. It works buy creating a git submodule from the url that you provide, in the `src/[your_plugin_name]` directory, then adds the name to `plugins` text file for the *cmake* to add it to build tree. Example:

```
./plugin.sh add box2d git@github.com:septag/box2d.git 
``` 

To remove the box2d, you can run:

```
./plugin.sh remove box2d
```

After this, all other programs and plugins will be able to recognize a plugin named "box2d" and use it's APIs or whatever.

## Live C/C++ reloading
All plugins and the game/program itself, can be hot-reloaded upon new builds. Only **Host** builds support reloading (see [Host vs. Bundle build](#builds/“host”vs.“bundle”build)). 

The way it works is actually pretty simple, the host application (_rizz_), monitors the plugins and game itself for changes, upon file modification, it immediately unloads and reloads the plugin and restores the program state variables from the previous runtime. You just have to tag the variables that you want to be restored upon reload with `RIZZ_STATE` macro definition:

```cpp
RIZZ_STATE my_game_state g_state;   // g_state will be refreshed upon reload
```

Other rules that you have to keep in mind:
- Do not save objects that have pointers to anything that is not in the heap. 
- Always allocate heap memory from the host _rizz_. Use `the_core->heap_alloc` and `the_core->alloc` to allocate memory from heap
- Do not save objects that have non trivial constructors and destructors, they may or may not work
- You have to re-inject current plugin APIs upon `RIZZ_PLUGIN_EVENT_LOAD` event. (see [Writing plugins example](#plugins/writingplugins))
- If the plugin implements asset callbacks (see [Implementing new asset types](#assetmanager/implementingnewassettypes)), you have to update the callbacks upon `RIZZ_PLUGIN_EVENT_LOAD` event with `the_core->update_asset_callbacks` function.
    
# Job dispatcher
Job dispatcher in _rizz_ framework is *fiber based*. This system has it's own ups and downs. The major reason that it's chosen for _rizz_, is it's *ease of use* and *noble handling of task dependencies and task stealing*. 

It works by creating a pool of threads and dispatch tasks to them in form of *fibers*. *Fiber* is a system-level construct and is a set of saved CPU registers and a stack memory. It happens in user-space, so it's pretty fast to switch between them at runtime. For more details, read this [slides](http://twvideo01.ubm-us.net/o1/vault/gdc2015/presentations/Gyrling_Christian_Parallelizing_The_Naughty.pdf) from "Parallelizing the Naughty Dog engine using fibers" presentation. 

With this system, there is no need to define dependency graph before spawning them. User can easily spawn a job and in the middle of it, spawn new jobs and wait for them, the job system will handle it gracefully by itself and keep all threads busy (including the "waiting" thread).

```cpp
static void my_raycast_callback(int start, int end, int thrd_index, void* user)
{
    my_raycast_data* data = (my_raycast_data*)user;
    // raycast the object with the scene
    for (int i = start; i < end; i++) {
        raycast(data->cells[i], data->obj);
    }
}

static void my_job_callback(int start, int end, int thrd_index, void* user)
{
    // start = first index of dispatched sub-items
    // end = end index of dispatched sub-items
    // thrd_index = for your information (0..max_threads-1)
    // user = user_data that is passed on dispatch

    my_objects* objs = (my_objects*)user;
    for (int i = start; i < end; i++) {
        my_raycast_data = {.obj = objs[i], .cells = my_scene->cells};
        sx_job_t raycast_job = the_core->job_dispatch(my_scene->num_cells, my_raycast_callback, &my_raycast_data, SX_JOB_PRIORITY_NORMAL, 0);
        the_core->job_wait_and_del(raycast_job);
        update_object(objs[i]);
    }
}

my_objects objs[16];
sx_job_t myjob = the_core->job_dispatch(16, my_job_callback, objs, SX_JOB_PRIORITY_NORMAL, 0);
// do some stuff
the_core->job_wait_and_del(myjob);  // waits for the job to and deletes the handle when finished (meanwhile can run tasks)
```

In this example, user dispatches an "update objects" job, providing the dispatcher with number of total objects to be processed. The system will automatically divide them up to the all (eligible ones, see 'tags' in `jobs.h` comments) threads in the pool and execute them by calling `my_job_callback`. The callback receives a range index which is between 0 and count of total objects given in the arguments. `thrd_index` is a hint index (range is between 0..num_threads-1) for the user to fetch extra thread-local data and avoid TLS fetching. You will notice that inside the `my_job_callback`, there is another _raycast_ job spawned, then it waits for the _raycast_ to finish and continue. The current job will be removed from the worker thread and replaced by raycast jobs until it's finished. In this system, user doesn't need to allocate and manage *job-data* because they are stored in the fiber's stack, and also the system is much easier for higher-level programmers to use it.

For more information about the API, checkout the comments in header file `include/sx/jobs.h`. 

But please **note** that, _rizz_ basically wraps the base _sx_ library's job system. So, the interface is the same, but you should use _rizz_ core api instead of the _sx_ one. Internally, it creates a job-system context with the parameters that are set in `rizz_config` structure (`job_num_threads`, `job_max_fibers`, `job_stack_size`). In addition to this, it saves extra data for each thread to be accessed by memory manager, log system and graphics sub-system. You can always create an additional job-system context in your program using *sx* API, however, you must handle all things by yourself, for example, you can not issue log calls, graphics APIs or get temp allocations inside these threads anymore. Basically, you can't use any multi-threaded API from the _rizz_ in your additional job-system.

But this system, isn't without drawbacks:

**Multi-threading primitives issue**: Using system mutexes are tricky and possibly dangerous. You can not lock a mutex and _wait_ for a new task before unlocking it. Here's an example:

```cpp
static void my_task(int start, int end, int thrd_index, void* user)
{
    void* additional_data = sx_tls_get();
    sx_mutex_lock(&mtx);
    the_core->job_wait_and_del(myjob); // possible thread switch after this
    sx_mutex_unlock(&mtx);
    // note: 'additional_data' maybe from another thread now!
}
```

In this scenario, on `job_wait_and_del` call, the current running fiber will be replaced by another one and may end up running in another thread after *wait* is continued. This causes the mutex being lost between threads and causes many syncing problems. This also can happen to TLS variables. The current workaround is either the user have the knowledge about this and consider this in his implementation, or use *spinlocks* or use `the_core->job_thread_index()` function to get the index index and update the per-thread data.

**Becareful of multi-threaded framework APIs**: The irony here is that any thread-safe _rizz_ api would be problematic in a case you are not careful and put them around another `wait` operation. Example:

```cpp
the_core->tmp_alloc_push()
// do some stuff
the_core->job_wait_and_del(myjob); //possible thread switch after this
the_core->tmp_alloc_pop(); // ERROR
```

In this example, because the *tmp_alloc* functions work with TLS data internally, it is possible that after `wait`, another thread takes the fiber and continue the execution, but *tmp_alloc_pop* assumed that we are on the same thread. Which is wrong. Proper implementation would be:

```cpp
the_core->tmp_alloc_push()
// do some stuff
the_core->tmp_alloc_pop();
the_core->job_wait_and_del(myjob); //possible thread switch after this
the_core->tmp_alloc_push();
// do some other stuff
the_core->tmp_alloc_pop(); 
``` 

So to summerize, you just have to be careful about the spots that you "`wait`" for a task to finish. Because that are the points that fibers get switched and may end up running in another thread.

# Co-routines
In addition to multi-threaded job system, _rizz_ also provides the user with *coroutines*. *coroutines* are fibers at their system core just like the fibers in the job dispatcher, but with the main exception that they **must run in main  thread only**. They are very useful to implement certain algorithms and gameplay elements. For example, iterative algorithms and timers can make use of *coroutines*. 

How they are defined in your application is very much like a simple callback function. Currently, you can do two main operations with *coroutines*:

- **Wait(N)**: Wait for N milliseconds, then continue execution
- **yield**: Yield execution in the current frame and continue execution on the next frame
    - **yield(N)**: Yield N frames and continue execution after that

For convenience, it is recommended that you use these provided set of macros to use *coroutines*: 
- **rizz_coro_declare(_name)**: Declare a coroutine. After this you should open a code block to write your `coroutine` function. *_name* argument should be *coroutine* name. Example: `rizz_coro_declare(my_coro) { // code goes here }`
- **rizz_coro_userdata()**: Get user data from the *coroutine* function, see `rizz_coro_invoke`.
- **rizz_coro_yield()**: Yields the current `coroutine`. Only must be called within the `coroutine` function.
- **rizz_coro_yield(N)**: Yields the current `coroutine` for N frames. Only must be called within the `coroutine` function.
- **rizz_coro_wait(N)**: Yields the current `coroutine` and continues after N millisconds. Only must be called within the `coroutine` function.
- **rizz_coro_end()**: Must always come on the return point of the *coroutine* function.
- **rizz_coro_invoke(_name, _user)**: Invokes (runs) the 'coroutine' by it's name and a user data void* pointer

Example:
```cpp
rizz_coro_declare(my_coro) 
{
    // some long iterative function
    // ...

    rizz_coro_yield();  // yield the coroutine, execution will continue after this point on the next frame

    // Continue with your code

    rizz_coro_end();    // always put this at the return point of the function
}

// run the co-routine 
rizz_coro_invoke(my_coro, NULL);
```

# Asset manager
Asset manager in _rizz_ is designed to be flexible so the 3rdparty user can extend asset types and add extra asset formats to the system.

## Design
Internally, it works by issuing file requests to the *virtual file-system* (`src/rizz/vfs.c`) which is an async call by default. After the file is loaded from the IO, it will load the actual asset data by spawning a new job for the job-system. You can see the overview of the design in the image below:

<!-- ![Figure [asset-design]: asset system data flow (from left to right): load process](https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-asset-design.png) -->
<img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-asset-design.png" class="vw_45" />

- When a `load` request comes in from the API, the *asset manager* returns a newly created handle back to the user, so it can be saved. But the handle points to a temporary *async* object, which is just a placeholder and not the actual asset being loaded. 
- Internally, *asset manager* requests the *virtual file-system* the file data, which runs in async mode (for example, for files on disk, it loads them in another thread). 
- Loaded data, will be returned to the asset system and it goes into a *prepare* process, which can be anything like for example creating graphics objects for a texture and allocate it's memory.
- After data is prepared, it dispatches a job (through the _rizz_ job-system), which calls *load* process on each asset. Here, the implementation can use the prepared data from the previous stage to actually parse or load the asset into the memory.
- All the results will be passed back to the *asset-manager* in the main thread and run the *finalize* process, so the implementation can cleanup un-needed memory, finalize device objects or whatever. At this point, the handle will now point to the actual asset.

## Multi-threading
Asset manager is partially multi-threaded, because the core of it uses async operations internally, it is not designed for the whole API to be used in multiple threads. Here are some rules that you can use it in a multi-threaded environment:

- Load all your assets in the main thread because it's an async operation anyway.
- You can only use get data specific function (`the_asset->obj_threadsafe(asset)`) inside worker threads. *note* that `the_asset->obj(asset)` is cheaper than the thread-safe version
- Load calls also can be performed while other threads are fetching previously loaded asset data 
- Unload calls is *NOT* permitted while working threads are using the `obj_threadsafe` call
- Never use asset objects across multiple frames inside worker-threads, because they may be invalidated by the main-thread

So basically, the usage pattern is always load your stuff in main-thread before any updates or running tasks. In the update loop, fetch assets or whatever in the main or other threads. When cleaning up the scene, make sure, the game update loop or tasks that are using those assets are not running. 

The reason behind this is simple. It's because the asset database is global and it's not worth it to lock everything constantly just to make it super thread-safe. So we assume that the database can only be modified in a single-thread which are basically the `load` and `unload` calls. But the exception here is that when you pass an asset handle to a worker thread, it can work, even if other assets are being loaded, but it's your responsibility to make sure that the handle is not unloaded while other threads are using it.

## Implementing new asset types
Adding new asset types can be acheived by implementing `rizz_asset_callback` functions and pass them to `the_core->register_asset_type` function:

```cpp
the_asset->register_asset_type("my_asset", (rizz_asset_callbacks) {
    .on_prepare = my_asset_on_prepare,
    .on_load = my_asset_on_load,
    .on_finalize = my_asset_on_finalize,
    .on_reload = my_asset_on_reload,
    .on_release = my_asset_on_release }, 
    "my_asset_load_params_struct", sizeof(my_asset_load_params_struct),
    my_asset_failed_obj, my_asset_async_obj, my_asset_forced_flags);
```

The first argument is your asset type name. You can load your asset by specifying the name of it (`the_core->load("my_asset", filepath, ...)`). Examples are built-in types like "texture", "font", "atlas", "sound", etc. You must choose a unique name for each asset type or the system will complain upon registration. 

The second argument is callbacks that are used to implement loading, unloading and reloading your asset type:
- **on_prepare**: This callbacks runs on the same thread as asset-manager and is used to prepare any objects or memory buffers to fill asset data (see Figure 6).
- **on_load**: Runs on job threads. The prepared data is passed with `rizz_asset_load_data` to this function for loading (or parsing) the actual asset data. You should put any time consuming loading code here. For example, decoding JPEG image or decoding MP3 samples. (see Figure 6)
- **on_finalize**: Runs on the same thread as asset-manager. This is a cleanup/finalize function where you can cleanup any temporary data previously allocated by prepare or load stages, or finalize device objects. After this callback, the actual data behind the asset handle will be replaced with the new asset. (see Figure 6)
- **on_reload**: Here's how the reload process is being done: when the virtual file-system notifies the asset-manager that a specific asset is changed on disk or whatever, the new temporary asset will be loaded in blocking mode, by executing all the previous callbacks in a row, after successful load, it will unload the current asset and replace it with the temporary one. But the catch here is that some objects may derive their data from the asset that is reloaded. What we need to do is to provide a custom mechanism, so we can update all possible dependent objects with the new asset. 
- **on_release**: This is also called in the same thread as asset-manager, which should unload the asset data and any device objects connected to it.

As you can see in the figure 6, all the parts in orange, needs to be done by an external source through the callbacks, in order to be able to successfully load an asset. There are built-in examples of how it is implemented in `graphics.c` for texture/image types.

The third and fourth parameters are the name and the size of the load struct that is passed to `the_asset->load` API call. This can be any custom types that you create for your implementation. For example, built-in `rizz_texture_load_params` parameter is used for loading "texture" types and can define the first MIP index, desired pixel format and so on.

The fifth and sixth parameters can be static objects, created and saved by a specific asset implementation, to provide asset-manager with default objects, like when it's still loading (async_obj) and when it fails to load (failed_obj). As an example, you can provide a single 1x1 purple texture for textures that fails to load, and a 1x1 white texture for textures that are not fully loaded yet.

The seventh and final parameter is *forced_flags*, which can be a combination of `rizz_asset_load_flags`. If you define any flags here, they will be added to the load flags passed to `the_asset->load` function. For example, _rizz_ always loads shaders in blocking mode, so no matter what, the *RIZZ_ASSET_LOAD_FLAG_WAIT_ON_LOAD* flag should always be added to the loading flags.

## Built-in asset types
The names listed below are currently supported types that can be used to load assets from `the_asset->load` function. The function will give you the handle to the asset. With `the_asset->obj` or `the_asset->obj_unsafe` functions, you can get the underlying object (pointer or id based on your asset object). Also most of the assets will need a pointer to *load_param* struct, which is different for each asset.

- **texture**: supported image types are `.dds/.ktx/.png/.jpg/.tga/.basis`. Load params must be provided with an instance of *rizz_texture_load_params* struct. object is a pointer and is an instance of *rizz_texture*.
    - It is recommended that you use basis textures especially for multiplatform projects. But you should provide `.fmt` flag in `rizz_texture_load_params` struct when loading these files, because this format should be transcoded to any platform specific formats on load.  To make .basis files, `basisu` binary is provided in `tools` directory. You can also provide your own binary by fetching the source from it's [repo](https://github.com/BinomialLLC/basis_universal) and build it.
- **shaders**: `.sgs` files that are built with `glslcc` tool. load params is NULL. object is a pointer to *rizz_shader*.
- **sound**: (Supported by 'sound' plugin) `.wav/.ogg`. OGG is a lossy compressed audio format which is decoded to normal wave samples upon load. Load params must be provided with an instance of *rizz_snd_load_params* struct. object is a handle Id for *rizz_snd_source*.
- **atlas**: (Supported by '2dtools' plugin) sprite atlas files with `.json` extension. They are built with `atlasc` command-line tool. These assets are always loaded without async. Load params must be provided with an instance of *rizz_atlas_load_params*. underlying rizz_asset_obj is a pointer to *rizz_atlas*.
- **font**: (Supported by '2dtools' plugin) true-type fone files `.ttf`. Load params must be provided with an instance of *rizz_font_load_params*. underlying rizz_asset_obj is a pointer to *rizz_font*. 
- **json**: (Implemented in core) Loads json data, the result is a *cj5_result*. Load params must be provided with an instance of *rizz_json_load_params*. underlying rizz_asset_obj is a pointer to cj5_result, which is all you need to read json data. Note that the source data for json will remain in memory until you unload the asset.

# Graphics
The graphics subsystem is rather low-level. Providing the user with a thin layer of graphics API abstraction, leaving any graphics technique implementations to the user.

It uses [sokol_gfx](https://github.com/floooh/sokol) as it's graphics backend. Uses different APIs under different platforms:

- Windows: D3d11
- Linux: OpenGL 3.3
- MacOS: Metal
- iOS: Metal
- Android: OpenGL-ES 3
- RaspberryPI: OpenGL-ES 2

Graphics subsystem, has two modes of drawing APIs. *Immediate mode* and *Staged* mode. *Immediate mode* is somehow direct calls to device graphics backend API. It is translated immediately to underlying graphics API and executed. For example, running `the_gfx->imm.draw`, calls D3d11::DrawPrimitives directly.

The *Staged* mode has a different behavior. First of all, it is multi-threaded. And it Uses the concept of *stages* to group draw calls and execute them in the order of their dependencies. Before drawing the scene, you create a set of *stages* (`the_gfx->stage_register`), for each stage, you define a name and the *parent_stage*, which is the stage the must always be done before the current stage. Then you can begin drawing by putting your draw calls between `begin` and `end` calls:

```cpp
rizz_api_gfx_draw* api = &the_gfx->staged;
api->begin(stage);
api->begin_default_pass(&pass_action, 800, 600);
api->apply_viewport(0, 0, 800, 600);
// issue other API calls...
api->draw(0, 100, 1);
api->end();
```

All graphics commands will be queued in command-lists, but not submitted to the backend API immediately. After the frame is finished, all of them are sorted by exeution order and their stage dependencies, and then submitted to the graphics backend API in bulk! This decision is made to lower the latency of the drawing calls. 

<!-- ![Figure [stages-example]: A simple example of stages and their dependencies (showed with arrows), the execution order of the calls will be from left to right](https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-stages-example.png) -->
<img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-stages-example.png" class="vw_45" />

All of the *staged* draw API functions (`the_gfx->staged`), can be called in job-system's worker threads. However, there are certain rules that you should follow:

- As said above in the [Job dispatcher notes](#architecture/jobdispatcher), you should be careful not to issue any `wait` operation between graphic calls. Always end the calls with `end_stage` before waiting for a child-job to finish.
- Do not destroy graphics objects during rendering work (in case you are rendering in worker threads). So basically, it is your responsibility to cleanup graphics objects only when they are not used.

## Advanced usage and more on Multi-threading
By default, all queued commands in the *staged* mode will be flushed and executed at the end of the frame automatically. We could run queued commands at the begining of the next frame, like "do rendering in another thread" approach, and make the driver do it's stuff while the next frame is being updated. but I wanted to keep things simple by flushing them at the end of the current frame, this also allows more low latency drawing, because the draw calls are drawn on the same frame as they are called.

However, For more complex scenes, early draw command submission can be implemented with the addition of two functions `the_gfx->present_commands` and `the_gfx->commit_commands`.
You can implement your pipeline, like the model shown below. With this approach, you can fetch the command-queues in the middle of the frame (`the_gfx->present_commands`) and submit them to the backend graphics API with `the_gfx->commit_commands` function, thus keeping the GPU busy while you are still doing rendering/culling or whatever. 

The catch here is to be careful about the `the_gfx->present_commands` call. It should be called in the main thread and without any worker threads submitting graphic commands in the background. Also, for `the_gfx->commit_commands`, because you are issuing it in the middle of frame, the full pipeline may not be met, so it does a validation check and see if you have already drawn all the stages that are dependent to each other.

Here's an example of how you can implement this functionality:

<!-- ![Figure [draw-mt]: Example of a multi-threading scenario and submitting command-queues to the GPU in the middle of the frame](https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-draw-mt.png) -->
<img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/rizz-basics-draw-mt.png" class="vw_45" />

As you can see in the image below, **Present** (`the_gfx->presend_commands`) call should only happen when you are not drawing on job threads, after a successful present call, you can safely call **Commit** (`the_gfx->commit_commands`) even if other threads are drawing. Because internally, the graphics system, stores two command-queue buffers, and switches between them after you call present. Basically, one buffer is meant to be fed by draw commands, another buffer is meant to sort and submit calls to the GPU. **Note** that on "Frame end", both command-queue buffers (feed and render) will be flushed and submitted to the GPU.

Additionally, you can debug all draw calls and other graphics resources with `rizz_api_imgui_extra->graphics_debugger(the_gfx->trace_info())` (*imgui* plugin) function call.

## Shaders
Shaders in _rizz_ are in GLSL 4.0 language and they are translated to other API specific shaders with a command-line tool called [glslcc](https://github.com/septag/glslcc) and a prebuilt binary of it exists in `tools/${platform}` directory. The *cmake* build system of _rizz_ finds it on *configure*. You can use either `cmake/glslcc.cmake` scripts to add shaders to your game or plugin at build time, or use `glslcc.exe` tool to directly build shaders yourself.

To add shaders to your cmake program, you can use one of these functions:

- **glslcc_target_compile_shaders(target source_files)**: compiles each .glsl (or .vert/.frag) files to their platform specific equivalents. 
- **glslcc_target_compile_shaders_h(target source_files)**: compiles each .glsl (or .vert/.frag) files to their platform specific language and outputs a C header file containing translated shader code that you can directly embed them into your program.
- **glslcc_target_compile_shaders_sgs(target source_files)**: compiles a complete shader pipeline (.vert + .frag) to a native SGS file format. For this function, you should provide both vertex and fragment shader and they will combined into a single SGS file.

There are bunch of properties that you can set globaly in *cmake* or individually for each source file:
- **GLSLCC_OUTPUT_DIRECTORY**: Sets the output directory. You will notice that the cmake script, also adds another folder to the end of this path, which is shader language name. For example, if the shader is being built for windows, the actual output directory would be `${GLSLCC_OUTPUT_DIRECTORY}/hlsl`.
- **GLSLCC_COMPILE_DEFINITIONS**: String containing compile definitions. Defines can be separated with each other with semicolon.
- **GLSLCC_INCLUDE_DIRS**: String containing include directories. Each include directory can be separated with semicolon.
- **GLSLCC_SHADER_LANG**: If provided, the shader language will be enforced, or else, the default shader language will be used based on the platform you are building. Valid options are:
    - *hlsl*: default on windows.
    - *gles*: default on android and raspberryPI
    - *msl*: default on iOS/MacOS
    - *glsl*: default on linux
- **GLSLCC_SHADER_VERSION**: If provided, shader profile version will be enforced, or else, default shader version will be built:
    - *HLSL*: 5.0 
    - *GLES*: version 3.0 for android, and version 2.0 for raspberryPI
    - *GLSL*: version 3.3 for Linux
    - *MSL*: version 1.0
- **GLSLCC_OUTPUT_FILENAME**: Override filename, default value is the same as input file name. For SGS files which embed multiple shaders in them, the first given filename will be used as default value.
- **GLSLCC_COMPILE_FLAGS**: Additional compile flags, in case you want to override `glslcc` default arguments. See the `glslcc` project page for more info on this.
- **GLSLCC_SOURCE_GROUP**: Source group name for IDEs like visual studio

Here's an example of cmake script compiling SGS shader from two vertex/fragment shaders, and compiling the same shaders, but output .h files (one for vertex-shader, one for fragment-shader) to embed in your executable. Also sets global output directory to "shaders".

```
set(GLSLCC_OUTPUT_DIRECTORY "shaders")
glslcc_target_compile_shaders_h(project_name "my_shader.vert;my_shader.frag")
glslcc_target_compile_shaders_sgs(project_name "my_shader.vert;my_shader.frag")
```

Another example of compiling the same shaders, but adding compile definitions and a different name:

```
set(GLSLCC_OUTPUT_DIRECTORY "shaders")
set_source_files_properties(my_shader.vert my_shader.frag PROPERTIES GLSLCC_COMPILE_DEFINITIONS "LIGHT=1" 
                                                                     GLSLCC_OUTPUT_FILENAME "my_shader_light")
glslcc_target_compile_shaders_h(project_name "my_shader.vert;my_shader.frag")
```

All shaders can be implemented with normal GLSL language. But in order for _rizz_ to be able to detect proper vertex input annotations, and pass correct values to all platforms (D3d11 to be specific), you should provide annotations for input and output values of shaders by setting **location** to predefined values:

```glsl
// vertex-shader
layout (location = POSITION) in vec2 a_pos;
layout (location = TEXCOORD0) in vec2 a_uv;
layout (location = COLOR0) in vec4 a_color;

layout (location = COLOR0) flat out vec4 f_color;
layout (location = TEXCOORD0) out vec2 f_uv;    

// fragment-shader
layout (location = COLOR0) flat in vec4 f_color;
layout (location = TEXCOORD0) in vec2 f_uv;

layout (location = SV_Target0) out vec4 frag_color;
```

As you can see in the example, all annotations are like what defined in HLSL standard. They are predefined by `glslcc` before  

### Default glslcc arguments
To see all arguments to [glslcc](https://github.com/septag/glslcc) just type `glslcc --help` in the command-line. But here, we list the common default arguments for each platform, which is also used in `glslcc.cmake`:

- **Windows**: `glslcc --lang=hlsl --reflect --profile=50`
- **Linux**: `glslcc --lang=glsl --reflect --profile=330` --flatten-ubos`
- **MacOS/iOS**: `glslcc --lang=metal --reflect`
- **Android**: `glslcc --lang=gles --reflect --profile=300 --flatten-ubos`
- **RaspberryPI**: `glslcc --lang=gles --reflect --profile=200 --flatten-ubos`

### Output formats
There are three ways to output shaders:

- Plain language file: This is the default behavior for glslcc. It outputs the plain shader language text. if `--reflect` argument is provided, it also outputs reflection information in _json_ format to another file. For example, compiling a vertex-shader `test.vert` to hlsl with `--reflect` outputs `test.vert.hlsl` which is the translated hlsl shader, and `test.vert.hlsl.json` which is the reflection information.
- SGS file: This is an internal binary format. It can embed multiple shaders with their reflection info in it. This is recommended for final game assets and can be triggered with `--sgs` argument. So with this option, you can input multiple shader stages like `-i test.vert -i test.frag`, but the output will be a single .sgs file.
- C header file: The is triggered with `--cvar={name}` argument, and `{name}` would be the prefix name of the C variable in the header. This is useful to include shaders in your build in case you have small amount of shaders. If you provide `--reflect` variable, the header will also include reflection information in form of `json` format. For example, if you compile with `-i test.vert --cvar=test --reflect`, it will automatically detect that it's a vertex-shader (by extension), and output a header file with constant variables `test_vs_size/test_vs_data/test_vs_refl_size/test_vs_refl_data`. The `_size` variables are obviously the size of the actual data in the variable, because they are uint32 aligned, you can't use `sizeof` to determine the actual data size.

# Profiling
Currently, the profiling tools are pretty basic and minimal. It's using [Remotery](https://github.com/Celtoys/Remotery) backend. I may change it to a more advanced and customizable profiling system in case if such functionality is really needed.

So, basically there are two main macros that is used to sample parts of the code. `rizz_profile_begin` and `rizz_profile_end`. The samples will be sent to the profiler's http server, and can be viewed by opening `index.html` in `3rdparty/remotery/vis` directory.

```cpp
rizz_profile_begin(SECTION_NAME);
// run some code here
rizz_profile_end(SECTION_NAME);
```

By default, main parts of the frame will be profiled. Like the whole frame and the game update itself.

## GPU Profiler
A very basic GPU sample profiling is also supported. It's almost as same cpu profiling, but with the exception that profiling samples must come between stages. To activate GPU profiling, add `RIZZ_CORE_FLAG_PROFILE_GPU` flag to `rizz_config.core_flags` field on initiaization.   
Also, the rendering _stages_ themselves profile themselves by default. Each rendering _stage_ will be profiled with the same label as the stage's name.

```cpp
rizz_gfx_profile_begin(&the_gfx.staged, SECTION_NAME);
// issue gpu commands
rizz_gfx_profile_end(SECTION_NAME);
```

**Note** that currently, gpu profiling support is limited to D3d11 and OpenGL backends, Metal profiling is in the works, but currently turned off because of some existing issues.

## Command console
There is also a simple console command interface. Commands can be executed with either `the_core->execute_command` function or through profiler web interface, or _log window_ which will describe later in "Logging" section.  
To add your own commands to the console commands, use `the_core->register_console_command`.

```cpp
the_core->register_console_command("my_command", [](int argc, char* argv[], void* user) {
    // argc = number of args
    // argv[0] = command
    // argv[1], ... = arguments seperated by space
}, NULL, NULL);
```

**Shotcut keys**: Note that, the 3rd argument of `register_console_command` is _shortcut_ string. Shortcuts can be assigned to simple console commands, they are useful for commands that have default arguments or require none, like "trigger" types. So pressing the shortcut key button will only run the command without any arguments. The shortcut string is a human friendly key + modifier combination. In which "CTRL"/"SHIFT"/"ALT" stand to modifier keys. Examples are "F1", "CTRL+A", "SHIFT+CTRL+O"

## Command line arguments
There are two types of command-line arguments, ones that are pre-defined by _rizz_ itself, and user-defined ones.

### _rizz_ arguements
```
-V --version                        - Print version
-r --run=<filepath>                 - Game/App module to run
-g --profile-gpu                    - Enable gpu profiler
-c --cwd=<(null)>                   - Change current directory after initialization
-U --dump-unused-assets             - Dump unused assets into `unused-assets.json`
-h --help                           - Show this help message
```

For none _BUNDLE_ builds, you should always provide *--run* argument with a valid filename of the game module you are trying to launch.

### User-defined arguments
User command-line arguments can be declared inside *rizz_game_decl_config_cmdline* (rizz_game_config function with extra argument) upon program initialization. you should use the function pointer provided by the second argument of the macro, to add new command arguments. After that, the command line will be reparsed and the arguements along with their values cached for future access.

```c
rizz_game_decl_config_cmdline(conf, register_arg)
{
    // set config fields and whatnot ...
    // register custom command arguments
    register_arg("hidehud", 'H', RIZZ_CMDLINE_ARGTYPE_OPTIONAL, "hides game HUD", "can be 1 or 0 (default:0)");
}
```

To use them, anywhere in your application, you can use `the_app->cmdline_arg_value` or `the_app->cmdline_arg_exists` API functions to check or get the value of the arguments.

# Logging
Logging can be done with these macros provided in `rizz.h`. You can also use `rizz_api_core` set of logging functions directly (`print_xxx` functions), but it is recommended that you use the macros because they are just simpler:

- **rizz_log_error**: Outputs error message (type = RIZZ_LOG_LEVEL_ERROR)
- **rizz_log_warn**: Outputs warning message (type = RIZZ_LOG_LEVEL_WARN)
- **rizz_log_info**: Output info message (type = RIZZ_LOG_LEVEL_INFO)
- **rizz_log_verbose**: Output verbose message (type = RIZZ_LOG_LEVEL_VERBOSE)
- **rizz_log_debug**: Output debug message, will be disabled on builds other than _DEBUG. (type = RIZZ_LOG_LEVEL_DEBUG)

You can control logging level with either setting `rizz_config.log_level` field on initialize, or use `the_core->set_log_level` function.

Besides logging level, you have another filter to categorize log entries called channels which is just a 32bit bitset variable, you can use the `rizz_log_xxxx_channels` macro variants to provide channels for log entries.

```cpp
#define ENTITY_SPAWN_LOG 0x1
#define ENTITY_KILL_LOG 0x2
#define ENTITY_LOGS (ENTITY_SPAWN_LOG|ENTITY_KILL_LOG)
rizz_log_debug_channels(ENTITY_SPAWN_LOG, "Entity spawned");
```

## Log backends
There are several built-in backends, however you can implement additional logging backends by registering them with `the_core->register_log_backend` function and provide a callback to receive log entries.

When you call any `rizz_log_xxx` macros on any engine thread (main or job dispatcher threads), it will be queued and sorted by calling timestamp at the end of the frame. Then it will be dispatched to the registered backends. By doing this, we can be sure that the callbacks always happen in the _main_ thread, so the implementation doesn't have to worry about data race issues and whatnot. 

However, there are exceptions to this. Some built-in logging backends, like **Terminal**/**Debugger window** and **Android native log** outputs the log immediately after calling `rizz_log_xxx` functions and doesn't wait for queued results, because by nature they are thread-safe and we want to have some immediate log feedback in case something goes wrong.  

## Log window
When using `imgui` plugin, you will get a log window along with console command. By default, pressing "~" shortcut key will bring it up. 

<!-- ![log-console-window](https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/log-console-window.png) -->
<img src="https://raw.githubusercontent.com/chunqian/chunqian.github.io/main/rizz/assets/img/log-console-window.png" class="vw_45" />

You can issue commands, filter out entries based on their level and channel bits (0-31). Also, double clicking on each entry will copy it to the clipboard.

## Configuration
When registering application to the engine, we define a _config_ function (see [Application/Game](#pluginsandapps/writingplugins/application/game)). This function receives a conf (*rizz_config* struct) that is filled with default values. You can override those fields in your code before initialization happens. 

The steps that configuration data is filled and overrided are as follows:

```
    .--------------------.      .------------.       .------------------------------.
    | hardcoded default  +----->| rizz.ini   +------>|  rizz_game_decl_config(conf) |
    |       values       |      '------------'       '------------------------------'
    '--------------------'                                                           
```

So, first it is filled with hardcoded default values by the engine, then it tries to read *rizz.ini* from current directory, reads it's values and override *rizz_config* fields. At last, it is passed to the program's config function (rizz_game_decl_config flag) for final override.

**rizz.ini**: It's the common _ini_ format, and can include different sections, the main one is `[rizz]` which can include all the fields in the `rizz_config` struct:

rizz.h - see `rizz_config` for all members: 
```c
typedef struct rizz_config {
    const char* app_name;
    int window_width;
    int window_height;
    rizz_core_flags core_flags; 
} rizz_config;
```

rizz.ini:
```ini
[rizz]
app_name = my_app_name
window_width = 1980
window_height = 1080
core_flags = LOG_TO_FILE|PROFILE_GPU
```

you can also include extra configurations in different sections and fetch them with `the_app->config_meta_value` function, this is useful to include configuration for your plugins or the game itself:

rizz.ini:
```ini
; main rizz config
[rizz]
app_name = my_app_name
window_width = 1980
window_height = 1080
core_flags = LOG_TO_FILE|PROFILE_GPU

; imgui plugin
[imgui]
log_buff_size = 256

; your game settings
[my_game]
god_mode = true
```

your_game.c:
```c
// first argument is the section, second is the property
const char* s_god_mode = the_app->config_meta_value("my_game", "god_mode");
if (god_mode) {
    the_game.god_mode = sx_tobool(s_god_mode);
}
```

# Reflection/Serialization
_rizz_ includes a simple built-in reflection system that also implements _json_ serialization.  
It doesn't do any fancy stuff like magic macro or any kind of code-gen, you just have to register all the types up front.
However there are `rizz_refl_reg_XXX` family of macros  to facilitate this process:

- `rizz_refl_reg_enum(context, type, name, meta)`: register enum type along with it's value
    - **context**: reflection context that is created with `create_context`
    - **type**: type name of the enum (typedef enum **my_enum**)
    - **name**: name of each enumerator. (typedef enum my_enum { **MY_FIRST_ENUM** })
    - **meta**: you can provide a custom static pointer that you can get in serializer for extra functionality (like GUI state/presets)
- `rizz_refl_reg_func(ctx, type, name, desc, meta)`: register a function pointer
    - **context**: reflection context that is created with `create_context`
    - **type**: type of the function `(typedef void (*foo)())`
    - **name**: name of the function instance.
    - **meta**: you can provide a custom static pointer that you can get in serializer for extra functionality (like GUI state/presets)
- `rizz_refl_reg_field(ctx, struct, type, name, desc, meta)`: register a struct field, you can also use already registered struct types as a field for other structs.
    - **context**: reflection context that is created with `create_context`
    - **struct**: parent _struct_ type name. for example in `struct A { int x; int y; }` example, _A_ would be struct for member 'x' and 'y'
    - **type**: type name of the field. in the example above, typename would be _int_. For arrays *(only static arrays are supported)*, you should include it here. for example, 32-byte char type would be _char[32]_
    - **name**: name of the field in the struct.
    - **meta**: you can provide a custom static pointer that you can get in serializer for extra functionality (like GUI state/presets)

Here's an example of registering all fields of `rizz_shader_info` POD type. _Note_ that before we register the fields of `rizz_shader_info` itself, we registered all child types and enums that are used in it. 

```cpp
rizz_refl_context* ctx = the_refl->create_context(the_core->heap_alloc());

rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_FLOAT, NULL);
rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_FLOAT2, NULL);
rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_FLOAT3, NULL);
rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_FLOAT4, NULL);
rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_BYTE4, NULL);
rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_BYTE4N, NULL);
rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_UBYTE4, NULL);
rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_UBYTE4N, NULL);
rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_SHORT2, NULL);
rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_SHORT2N, NULL);
rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_SHORT4, NULL);
rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_SHORT4N, NULL);
rizz_refl_reg_enum(ctx, sg_vertex_format, SG_VERTEXFORMAT_UINT10_N2, NULL);

rizz_refl_reg_field(ctx, rizz_shader_refl_input, char[32], name, "shader input name", NULL);
rizz_refl_reg_field(ctx, rizz_shader_refl_input, char[32], semantic, "shader semantic name", NULL);
rizz_refl_reg_field(ctx, rizz_shader_refl_input, int, semantic_index, "shader semantic index", NULL);
rizz_refl_reg_field(ctx, rizz_shader_refl_input, sg_vertex_format, type, "shader input type", NULL);

rizz_refl_reg_field(ctx, rizz_shader_info, rizz_shader_refl_input[SG_MAX_VERTEX_ATTRIBUTES], inputs, "shader inputs", NULL);
rizz_refl_reg_field(ctx, rizz_shader_info, int, num_inputs, "shader input count", NULL);    
```

Now you have three registered types: "sg_vertex_format", "rizz_shader_refl_input" and "rizz_shader_info". After registration, you can serialize/deserialize struct instance. Currently, _rizz_ includes json serializer, so you can use `serialize_json` to serialize the data to json file and use `deserialize_json` to deserialize json data to memory.

## Custom Serializers/Deserializers
Besides included _JSON_, you can make your own custom serializer by using `serialize`/`deserialize` functions and override callbacks for each. 

- **rizz_refl_serialize_callbacks**: callbacks for serialization (memory to disk/storage)
- **rizz_refl_deserialize_callbacks**: callbacks for deserialization (disk/storage to memory)

If you look at the callbacks, there are two callbacks for *builtin* types, built-in types does not need to be registered, and are supported by reflection system. They include opaque types like int/float/int64_t/etc (just make sure you use the stddef/standard definitions when registering and not typedefs) plus some math types like sx_vec2/3/4/matrix. See `rizz_refl_variant_type` enums for all supported built-in types.

Also, writing a full serializer is beyond the scope of this document, but it's pretty easy and you can look at json serializer code in reflect.c as a working example. (`rizz__refl_serialize_json`/`rizz__refl_deserialize_json` functions)


# Changelist
- Version 1.16 (December 2020): Added ini config
- Version 1.15 (November 2020): Added reflection section
- Version 1.14 (October 2020): Logging improvements, added shortcut
- Version 1.13 (October 2020): Added command-line section
- Version 1.12 (July 2020): Better descriptions for built-in asset types
- Version 1.1 (May 2020): Added Gpu Profiling, Tweaked shader docs, Added logging
- Version 1.0 (April 2020): Initial version