* [back](rust-tutorial/docs//)

* [简介](rust-tutorial/docs/index.md)

* [Hello World](rust-tutorial/docs/hello.md)

  * [注释](rust-tutorial/docs/hello/comment.md)

  * [格式化输出](rust-tutorial/docs/hello/print.md)

    * [调试（debug）](rust-tutorial/docs/hello/print/print_debug.md)

    * [显示（display）](rust-tutorial/docs/hello/print/print_display.md)

    * [测试实例: List](rust-tutorial/docs/hello/print/print_display/testcase_list.md)

    * [格式化](rust-tutorial/docs/hello/print/fmt.md)

* [原生类型](rust-tutorial/docs/primitives.md)

  * [字面量和运算符](rust-tutorial/docs/primitives/literals.md)

  * [元组](rust-tutorial/docs/primitives/tuples.md)

  * [数组和切片](rust-tutorial/docs/primitives/array.md)

* [自定义类型](rust-tutorial/docs/custom_types.md)

  * [结构体](rust-tutorial/docs/custom_types/structs.md)

  * [枚举](rust-tutorial/docs/custom_types/enum.md)

    * [使用 use](rust-tutorial/docs/custom_types/enum/enum_use.md)

    * [C 风格用法](rust-tutorial/docs/custom_types/enum/c_like.md)

    * [测试实例: 链表](rust-tutorial/docs/custom_types/enum/testcase_linked_list.md)

  * [常量](rust-tutorial/docs/custom_types/constants.md)

* [变量绑定](rust-tutorial/docs/variable_bindings.md)

  * [可变变量](rust-tutorial/docs/variable_bindings/mut.md)

  * [作用域和遮蔽](rust-tutorial/docs/variable_bindings/scope.md)

  * [变量先声明](rust-tutorial/docs/variable_bindings/declare.md)

  * [冻结](rust-tutorial/docs/variable_bindings/freeze.md)

* [类型系统](rust-tutorial/docs/types.md)

  * [类型转换](rust-tutorial/docs/types/cast.md)

  * [字面量](rust-tutorial/docs/types/literals.md)

  * [类型推断](rust-tutorial/docs/types/inference.md)

  * [别名](rust-tutorial/docs/types/alias.md)

* [类型转换](rust-tutorial/docs/conversion.md)

  * [From 和 Into](rust-tutorial/docs/conversion/from_into.md)

  * [TryFrom 和 TryInto](rust-tutorial/docs/conversion/try_from_try_into.md)

  * [ToString 和 FromStr](rust-tutorial/docs/conversion/string.md)

* [表达式](rust-tutorial/docs/expression.md)

* [流程控制](rust-tutorial/docs/flow_control.md)

  * [if/else](rust-tutorial/docs/flow_control/if_else.md)

  * [loop 循环](rust-tutorial/docs/flow_control/loop.md)

    * [嵌套循环和标签](rust-tutorial/docs/flow_control/loop/nested.md)

    * [从 loop 循环返回](rust-tutorial/docs/flow_control/loop/return.md)

  * [while 循环](rust-tutorial/docs/flow_control/while.md)

  * [for 循环和区间](rust-tutorial/docs/flow_control/for.md)

  * [match 匹配](rust-tutorial/docs/flow_control/match.md)

    * [解构](rust-tutorial/docs/flow_control/match/destructuring.md)

      * [元组](rust-tutorial/docs/flow_control/match/destructuring/destructure_tuple.md)

      * [枚举](rust-tutorial/docs/flow_control/match/destructuring/destructure_enum.md)

      * [指针和引用](rust-tutorial/docs/flow_control/match/destructuring/destructure_pointers.md)

      * [结构体](rust-tutorial/docs/flow_control/match/destructuring/destructure_structures.md)

    * [卫语句](rust-tutorial/docs/flow_control/match/guard.md)

    * [绑定](rust-tutorial/docs/flow_control/match/binding.md)

  * [if let](rust-tutorial/docs/flow_control/if_let.md)

  * [while let](rust-tutorial/docs/flow_control/while_let.md)

* [函数](rust-tutorial/docs/fn.md)

  * [方法](rust-tutorial/docs/fn/methods.md)

  * [闭包](rust-tutorial/docs/fn/closures.md)

    * [捕获](rust-tutorial/docs/fn/closures/capture.md)

    * [作为输入参数](rust-tutorial/docs/fn/closures/input_parameters.md)

    * [类型匿名](rust-tutorial/docs/fn/closures/anonymity.md)

    * [输入函数](rust-tutorial/docs/fn/closures/input_functions.md)

    * [作为输出参数](rust-tutorial/docs/fn/closures/output_parameters.md)

    * [std 中的例子](rust-tutorial/docs/fn/closures/closure_examples.md)

      * [Iterator::any](rust-tutorial/docs/fn/closures/closure_examples/iter_any.md)

      * [Iterator::find](rust-tutorial/docs/fn/closures/closure_examples/iter_find.md)

  * [高阶函数](rust-tutorial/docs/fn/hof.md)

  * [发散函数](rust-tutorial/docs/fn/diverging.md)

* [模块](rust-tutorial/docs/mod.md)

  * [可见性](rust-tutorial/docs/mod/visibility.md)

  * [结构体的可见性](rust-tutorial/docs/mod/struct_visibility.md)

  * [use 声明](rust-tutorial/docs/mod/use.md)

  * [super 和 self](rust-tutorial/docs/mod/super.md)

  * [文件分层](rust-tutorial/docs/mod/split.md)

* [crate](rust-tutorial/docs/crates.md)

  * [库](rust-tutorial/docs/crates/lib.md)

  * [使用库](rust-tutorial/docs/crates/using_lib.md)

* [cargo](rust-tutorial/docs/cargo.md)

  * [依赖](rust-tutorial/docs/cargo/deps.md)

  * [约定规范](rust-tutorial/docs/cargo/conventions.md)

  * [测试](rust-tutorial/docs/cargo/test.md)

  * [构建脚本](rust-tutorial/docs/cargo/build_scripts.md)

* [属性](rust-tutorial/docs/attribute.md)

  * [死代码 dead_code](rust-tutorial/docs/attribute/unused.md)

  * [crate](rust-tutorial/docs/attribute/crate.md)

  * [cfg](rust-tutorial/docs/attribute/cfg.md)

    * [自定义条件](rust-tutorial/docs/attribute/cfg/custom.md)

* [泛型](rust-tutorial/docs/generics.md)

  * [函数](rust-tutorial/docs/generics/gen_fn.md)

  * [实现](rust-tutorial/docs/generics/impl.md)

  * [trait](rust-tutorial/docs/generics/gen_trait.md)

  * [约束](rust-tutorial/docs/generics/bounds.md)

    * [测试实例: 空约束](rust-tutorial/docs/generics/bounds/testcase_empty.md)

  * [多重约束](rust-tutorial/docs/generics/multi_bounds.md)

  * [where 子句](rust-tutorial/docs/generics/where.md)

  * [newtype 惯用法](rust-tutorial/docs/generics/new_types.md)

  * [关联项](rust-tutorial/docs/generics/assoc_items.md)

    * [存在问题](rust-tutorial/docs/generics/assoc_items/the_problem.md)

    * [关联类型](rust-tutorial/docs/generics/assoc_items/types.md)

  * [虚类型参数](rust-tutorial/docs/generics/phantom.md)

    * [测试实例: 单位检查](rust-tutorial/docs/generics/phantom/testcase_units.md)

* [作用域规则](rust-tutorial/docs/scope.md)

  * [RAII](rust-tutorial/docs/scope/raii.md)

  * [所有权和移动](rust-tutorial/docs/scope/move.md)

    * [可变性](rust-tutorial/docs/scope/move/mut.md)

    * [部分移动](rust-tutorial/docs/scope/move/partial_move.md)

  * [借用](rust-tutorial/docs/scope/borrow.md)

    * [可变性](rust-tutorial/docs/scope/borrow/mut.md)

    * [别名使用](rust-tutorial/docs/scope/borrow/alias.md)

    * [ref 模式](rust-tutorial/docs/scope/borrow/ref.md)

  * [生命周期](rust-tutorial/docs/scope/lifetime.md)

    * [显式标注](rust-tutorial/docs/scope/lifetime/explicit.md)

    * [函数](rust-tutorial/docs/scope/lifetime/fn.md)

    * [方法](rust-tutorial/docs/scope/lifetime/methods.md)

    * [结构体](rust-tutorial/docs/scope/lifetime/struct.md)

    * [trait](rust-tutorial/docs/scope/lifetime/trait.md)

    * [约束](rust-tutorial/docs/scope/lifetime/lifetime_bounds.md)

    * [强制转换](rust-tutorial/docs/scope/lifetime/lifetime_coercion.md)

    * [static](rust-tutorial/docs/scope/lifetime/static_lifetime.md)

    * [省略](rust-tutorial/docs/scope/lifetime/elision.md)

* [特质 trait](rust-tutorial/docs/trait.md)

  * [派生](rust-tutorial/docs/trait/derive.md)

  * [使用 dyn 返回 trait](rust-tutorial/docs/trait/dyn.md)

  * [运算符重载](rust-tutorial/docs/trait/ops.md)

  * [Drop](rust-tutorial/docs/trait/drop.md)

  * [Iterator](rust-tutorial/docs/trait/iter.md)

  * [impl Trait](rust-tutorial/docs/trait/impl_trait.md)

  * [Clone](rust-tutorial/docs/trait/clone.md)

  * [父 trait](rust-tutorial/docs/trait/supertraits.md)

  * [消除重叠 trait](rust-tutorial/docs/trait/disambiguating.md)

* [使用 macro_rules! 来创建宏](rust-tutorial/docs/macros.md)

  * [语法](rust-tutorial/docs/macros/syntax.md)

    * [指示符](rust-tutorial/docs/macros/designators.md)

    * [重载](rust-tutorial/docs/macros/overload.md)

    * [重复](rust-tutorial/docs/macros/repeat.md)

  * [DRY (不写重复代码)](rust-tutorial/docs/macros/dry.md)

  * [DSL (领域专用语言)](rust-tutorial/docs/macros/dsl.md)

  * [可变参数接口](rust-tutorial/docs/macros/variadics.md)

* [错误处理](rust-tutorial/docs/error.md)

  * [panic](rust-tutorial/docs/error/panic.md)

  * [Option 和 unwrap](rust-tutorial/docs/error/option_unwrap.md)

    * [使用 ? 解开 Option](rust-tutorial/docs/error/option_unwrap/question_mark.md)

    * [组合算子: map](rust-tutorial/docs/error/option_unwrap/map.md)

    * [组合算子: and_then](rust-tutorial/docs/error/option_unwrap/and_then.md)

  * [结果 Result](rust-tutorial/docs/error/result.md)

    * [Result 的 map](rust-tutorial/docs/error/result/result_map.md)

    * [给 Result 取别名](rust-tutorial/docs/error/result/result_alias.md)

    * [提前返回](rust-tutorial/docs/error/result/early_returns.md)

    * [引入 ?](rust-tutorial/docs/error/result/enter_question_mark.md)

  * [处理多种错误类型](rust-tutorial/docs/error/multiple_error_types.md)

    * [从 Option 中取出 Result](rust-tutorial/docs/error/multiple_error_types/option_result.md)

    * [定义一种错误类型](rust-tutorial/docs/error/multiple_error_types/define_error_type.md)

    * [把错误 “装箱”](rust-tutorial/docs/error/multiple_error_types/boxing_errors.md)

    * [? 的其他用法](rust-tutorial/docs/error/multiple_error_types/reenter_question_mark.md)

    * [包裹错误](rust-tutorial/docs/error/multiple_error_types/wrap_error.md)

  * [遍历 Result](rust-tutorial/docs/error/iter_result.md)

* [标准库类型](rust-tutorial/docs/std.md)

  * [箱子, 栈和堆](rust-tutorial/docs/std/box.md)

  * [动态数组 vector](rust-tutorial/docs/std/vec.md)

  * [字符串 String](rust-tutorial/docs/std/str.md)

  * [选项 Option](rust-tutorial/docs/std/option.md)

  * [结果 Result](rust-tutorial/docs/std/result.md)

    * [? 用法](rust-tutorial/docs/std/result/question_mark.md)

  * [panic!](rust-tutorial/docs/std/panic.md)

  * [散列表 HashMap](rust-tutorial/docs/std/hash.md)

    * [更改或自定义关键字类型](rust-tutorial/docs/std/hash/alt_key_types.md)

    * [散列集 HashSet](rust-tutorial/docs/std/hash/hashset.md)

* [标准库更多介绍](rust-tutorial/docs/std_misc.md)

  * [线程](rust-tutorial/docs/std_misc/threads.md)

    * [测试实例: map-reduce](rust-tutorial/docs/std_misc/threads/testcase_mapreduce.md)

  * [通道](rust-tutorial/docs/std_misc/channels.md)

  * [路径](rust-tutorial/docs/std_misc/path.md)

  * [文件输入输出（I/O）](rust-tutorial/docs/std_misc/file.md)

    * [打开文件 open](rust-tutorial/docs/std_misc/file/open.md)

    * [创建文件 create](rust-tutorial/docs/std_misc/file/create.md)

    * [读取行 read lines](rust-tutorial/docs/std_misc/file/read_lines.md)

  * [子进程](rust-tutorial/docs/std_misc/process.md)

    * [管道](rust-tutorial/docs/std_misc/process/pipe.md)

    * [等待](rust-tutorial/docs/std_misc/process/wait.md)

  * [文件系统操作](rust-tutorial/docs/std_misc/fs.md)

  * [程序参数](rust-tutorial/docs/std_misc/arg.md)

    * [参数解析](rust-tutorial/docs/std_misc/arg/matching.md)

  * [外部语言函数接口](rust-tutorial/docs/std_misc/ffi.md)

* [测试](rust-tutorial/docs/testing.md)

  * [单元测试](rust-tutorial/docs/testing/unit_testing.md)

  * [文档测试](rust-tutorial/docs/testing/doc_testing.md)

  * [集成测试](rust-tutorial/docs/testing/integration_testing.md)

  * [开发依赖](rust-tutorial/docs/testing/dev_dependencies.md)

* [不安全操作](rust-tutorial/docs/unsafe.md)

* [兼容性](rust-tutorial/docs/compatibility.md)

  * [原始标志符](rust-tutorial/docs/compatibility/raw_identifiers.md)

* [补充](rust-tutorial/docs/meta.md)

  * [文档](rust-tutorial/docs/meta/doc.md)

  * [Playpen](rust-tutorial/docs/meta/playpen.md)
