# try语句块和异常处理

## 标准异常


<table border="1">
<tr>
    <th colspan="2">表5.1：stdexcept定义的异常类</th>
</tr>
<tr>
    <td align="left">exception</th>
    <td align="left">最常见的问题</th>
</tr>
<tr >
    <td align="left">runtime_error</th>
    <td align="left">只有在运行时才能检测出的问题</th>
</tr>
<tr >
    <td align="left">range_error</th>
    <td align="left">运行时错误：生成的结果超出了有意义的值域范围</th>
</tr>
<tr >
    <td align="left">overflow_error</th>
    <td align="left">运行时错误：计算上溢</th>
</tr>
<tr >
    <td align="left">underflow_error</th>
    <td align="left">运行时错误：计算下溢</th>
</tr>
<tr >
    <td align="left">logical_error</th>
    <td align="left">程序逻辑错误</th>
</tr>
<tr >
    <td align="left">domain_error</th>
    <td align="left">逻辑错误：参数对应的结果值不存在</th>
</tr>
<tr >
    <td align="left">invalid_error</th>
    <td align="left">逻辑错误：无效参数</th>
</tr>
<tr >
    <td align="left">length_error</th>
    <td align="left">逻辑错误：试图创建一个超过该类型最大长度的对象</th>
</tr>
<tr >
    <td align="left">out_of_error</th>
    <td align="left">逻辑错误：使用一个超出有效范围的值</th>
</tr>
</table>
