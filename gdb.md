# gdb commands

Run gdb = `gdb ./debug/arrow-substrait-substrait-test`

We may want to run, e.g. `r --gtest_filter=Substrait.CallCastNonNullableFails --gtest_break_on_failure` - this runs but just that function and steps out when there's a failure


r = rerun the test
si = step into the function
n = next line
cont = continue, as in after a breakpoint
enter = repeat the last command
b 270 = set a breakpoint at line 270
b MakeStructFieldReference = set a breakpoint in this function
p expr.type() = print the output of calling expr.type()
list = show the nearby code
bt = backtrace

make sure always have the latest build so line numbers are correct


This is what a failure might look like:

```
/home/nic2/arrow_cpp_dev/cpp/src/arrow/engine/substrait/serde_test.cc:762: Failure
Failed
'_error_or_value79.status()' failed with NotImplemented: No conversion function exists to convert the Arrow function cast to a Substrait call
/home/nic2/arrow_cpp_dev/cpp/src/arrow/engine/substrait/expression_internal.cc:1118  ext_set->registry()->GetArrowToSubstraitCall(call->function_name)
/home/nic2/arrow_cpp_dev/cpp/src/arrow/engine/substrait/serde.cc:309  ToProto(expr, ext_set, conversion_options)

Thread 1 "arrow-substrait" received signal SIGTRAP, Trace/breakpoint trap.
0x00007ffff76c6876 in testing::UnitTest::AddTestPartResult(testing::TestPartResult::Type, char const*, int, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&, std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > const&) () from /home/nic2/anaconda3/envs/arrow-dev/lib/libgtest.so.1.12.1
```
