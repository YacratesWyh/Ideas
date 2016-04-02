http://www.cnblogs.com/xianghang123/archive/2011/12/06/2277602.html

【Matlab】 读取文件各种方法

本技术支持指南主要处理：ASCII, binary, and MAT files.
要得到MATLAB中可用来读写各种文件格式的完全函数列表，可以键入以下命令：
help iofun

MATLAB中有两种文件I/O程序：high level and low level.
High level routines: 包括现成的函数，可以用来读写特殊格式的数据，并且只需要少量的编程。
Low level routines: 可以更加灵活的完成相对特殊的任务，需要较多的额外编程。


High level routines 包括现成的函数，可以用来读写特殊格式的数据，并且只需要少量的编程。

 

举个例子，如果你有一个包含数值和字母的文本文件（text file）想导入MATLAB，你可以调用一些low level routines自己写一个函数，或者是简单的用TEXTREAD函数。

 

使用high level routines的关键是：文件必须是相似的（homogeneous），换句话说，文件必须有一致的格式。下面的段落描述一些high level file I/O routines并给出一些例子帮助理解概念。

 

LOAD/SAVE

 

主要的high level file I/O routines 是LOAD 和 SAVE函数。LOAD
可以读MAT-file data或者用空格间隔的格式相似的ASCII data. SAVE可以将MATLAB变量写入MAT-file格式或者空格间隔的ASCII data。大多数情况下，语法相当简单。下面的例子用到数值由空格间隔的ASCII file sample_file.txt ：

 

1 5 4 16 8

5 43 2 6 8

6 8 4 32 1

90 7 8 7 6

5 9 81 2 3

 

Example:
用 LOAD and SAVE 读写数据


CODE:

% Load the file to the matrix, M :
M = load('sample_file.txt') 

% Add 5 to M :
M = M +5 

% Save M to a .mat file called 'sample_file_plus5.mat':
save sample_file_plus5 M

% Save M to an ASCII .txt file called 'sample_file_plus5.txt' :
save sample_file_plus5.txt M -ascii 


UIGETFILE/UIPUTFILE

 

UIGETFILE/UIPUTFILE是基于图形用户界面（GUI）的。会弹出对话框，列出当前目录的文件和目录，提示你选择一个文件。UIGETFILE让你选择一个文件来写（类似Windows ‘另存为’选项？）。用UIGETFILE，可以选择已存在的文件改写，也可以输入新的文件名。两个函数的返回值是所选文件名和路径。

 

Example:
用 UIGETFILE 从当前目录选择一个 M-file


CODE:

% This command lists all the M-files in the current directory and
% returns the name and path of the selected file

[fname,pname] = uigetfile('*.m','Sample Dialog Box') 



注意: UIGETFILE 一次只能选择一个文件。

 

UIIMPORT/IMPORTDATA

 

UIIMPORT是一个功能强大，易于使用的基于GUI的high level routine，用于读complex data files。文件也必须是homogeneous。

 

IMPORTDATA形成UIIMPORT的功能，不打开GUI。可以将IMPORTDATA用于函数或者脚本中，因为在函数或者脚本中基于GUI的文件导入机制并不理想。下面的例子用到包含几行文件头和文本、数值数据的文件'sample_file2.txt' ：

 

This is a file header.

This is file is an example.

col1 col2 col3 col4

A    1   4    612.000

B    1   4    613.000

C    1   4    614.000

D    1   4    615.000

 

Example: Using IMPORTDATA to read in a file with headers, text, and numeric data


CODE:

% This reads in the file 'sample_file2.txt' and creates a
% structure D that contains both data and text data.
% Note the IMPORTDATA command specifies a white space 
% as the delimiter of the file, but IMPORTDATA can usually 
% detect this on its own 

D = importdata('sample_file2.txt','')  % 原文有误？
D = importdata('sample_file2.txt')


可以通过访问结构D的数据和文本域，来看结构D中的真实值，例如输入：

data = D.data

text = D.textdata

 

可以用UIIMPORT读同一个文件并得到同样的结构.

 

注意: 对于 ASCII data, 你必须检验导入向导正确的识别了列分隔符。

 

TEXTREAD/STRREAD

 

TEXTREAD 是一个强大的动态high level routine，设计用来读ASCII格式的文本和/或数值数据文件。STRREAD除是从字符串而不是文件读以外，类似于TEXTREAD。

 

两个函数可以用许多参数来改变其具体的工作方式，他们返回读入指定输出的数据。他们有效的提供给你一个
“两全其美”的方法，因为他们可以用一个命令读入混合的ASCII和数值数据（high level routines的做法），并且你可以改变他们以匹配你特定的应用（如同low level routines做到的）。例子：


CODE:
Example 1: Using TEXTREAD to read in an entire file into a cell array


% This command reads in the file fft.m into the cell array, file 
file = textread('fft.m','%s','delimiter','\n','whitespace','');

 


CODE:
Example 2: Using STRREAD to read the words in a line

% This command uses the cell array created in Example 1 to 
% read in each word of line 28 in 'file' to a cell array, words

words = strread(file{28},'%s','delimiter','')


CODE:
Example 3: Using TEXTREAD to read in text and numeric data from a file with headers

% This command skips the 2 header lines at the top of the file
% and reads in each column to the 4 specified outputs

[c1 c2 c3 c4] = textread('sample_file2.txt','%s %s %s %s','headerlines',2) 


CODE:
Example 4: Using TEXTREAD to read in specific rows of text and numeric data from a file

% This command reads in rows B and C of the file. The 'headerlines'
% property is used to move down to the desired starting row and the 
% read operation is performed 2 times 

[c1 c2 c3 c4] = textread('sample_file2.txt',... 
'%s %s %s %s',2,'headerlines',4) 


CODE:
Example 5: Using TEXTREAD to read in only the numeric data from a file containing text and numbers

% This command reads in only the numeric data in the file. The
% 'headerlines' property is used to move down to the first row 
% of interest and the first column of text is ignored with the 
% '*'  operator 

[c2 c3 c4] = textread('sample_file2.txt','%*s %d %d %f','headerlines',3) 


DLMREAD/DLMWRITE/CSVREAD

 

DLMREAD 和 DLMWRITE函数能够读写分隔的ASCII data，而不是用low level routines。他们比low level routines容易使用，Low level routines用几行代码实现的功能可以用DLMREAD/DLMWRITE简化成一行。

 

CSVREAD用来读分隔符是逗号的文件，是DLMREAD的特殊情况。当读空格和Tab分隔的电子数据表文件时，DLMREAD特别有用。以'sample_file.txt'为例：

 


CODE:
Example 1: Using DLMREAD to read in a file with headers, text, and numeric data 

% This reads in the file 'sample_file2.txt' and creates a matrix, D,
% with the numeric data this command specifies a white space as the
% delimiter of the file 

D = dlmread('sample_file.txt','') 


CODE:
Example 2: Using DLMREAD to extract the first 3 columns of the last 3 rows

% This reads in the first 3 columns of the last 3 rows of
% the data file 'sample_file.txt'into the matrix, D_partial.
% 读文件 'sample_file.txt' 前3列后3行，到矩阵D_partial.

D_partial = dlmread('sample_file.txt','',[2 0 4 2]) 



CODE:
Example 3: Using DLMWRITE to write a comma delimited file

% This creates a file called 'partialD.txt' that consists of 
% the first 3 columns of the last 3 rows of data where each
% element is separated by a comma 

dlmwrite('partialD.txt',D_partial,',') 



注意: 保证DLMREAD and DLMWRITE指定范围的指标从0开始，而不是从1开始。

 

WK1READ/WK1WRITE

 

WK1READ 用来读Lotus123 电子数据表文件的数据；WK1WRITE用来写矩阵到Lotus123 电子数据表文件。

 

XLSREAD

 

XLSREAD用来读Excel的数值和文本数据。

 

 

三. 具体例子分析：
Matlab网站用两个例子非常详尽地介绍了各个命令的基本用法，实际中，面对手头上的数据，如何选用合适的命令呢？以下结合几个示例给出一些总结，大家举一反三就可以了：

1. 纯数据（列数相同）：
源文件：



CODE:
0 3866.162 2198.938 141.140
1 3741.139 2208.475 141.252
2 3866.200 2198.936 141.156
3 3678.048 2199.191 141.230
4 3685.453 2213.726 141.261
5 3728.769 2212.433 141.277
6 3738.785 2214.381 141.256
7 3728.759 2214.261 141.228
8 3748.886 2214.299 141.243
9 3748.935 2212.417 141.253
10 3733.612 2226.653 141.236
11 3733.583 2229.248 141.223
12 3729.229 2229.118 141.186




解答：对于这个txt文件，由于各行列数相同，故简单地使用load,importdata均可。


2.字段名（中、英文字段均可）+数据：
源文件：


CODE:
CH0 CH1 CH2 CH3
0.000123 0.000325 0.000378 0.000598
0.000986 0.000256 0.000245 0.000698


解答：由于是记录的形式，因此各行列数必相同（缺少部分列时请自行在文件中补上 Inf 或 NaN），故直接使用 importdata 便可。

3.注释（含有独立的数字串）+数据（列数相同）：
问题：这个文件有4列,但前6行是文字说明,4列数字是从第8行开始的.现在我想把这个文件的前2列和文字说明提出来组成一个新的dat文件

源文件：


CODE:
Group 2  12.02.2006   Limei
Samples of datas: 50000

CH0  CH1  CH2  CH3
0.000123  0.000325   0.000378   0.000598
0.000986  0.000256   0.000245   0.000698


目标文件：


CODE:
Group 2 12.02.2006 Limei
Samples of datas: 50000

CH0 CH1
0.000123 0.000325
0.000986 0.000256


解答：由于注释中含有独立的数字串，且注释部分没有明显的格式， 这时候用importdata, load等高级命令直接读取会失败，用 textread, dlmwrite 等格式化命令也不太合适，因此只能使用低级命令进行读取。（当然了，可以跳过注释部分直接用高级命令读取数据，即：[a b c d] = textread(filename,'%f %f %f %f','headerlines',4); ）。一个简单的、非通用的包含注释的读取方法如下：
-------------------------------------转 ---------------------------------------------------------------------------------------

CODE:
clc;clear;
fid = fopen('exp.txt', 'r');
fid_n=fopen('ex.dat','w');
while ~feof(fid)
    tline=fgetl(fid);
    if ~isempty(tline)
        if double(tline(1))>=48 && double(tline(1))<=57  %数值开始
            a=strread(tline);
            a(3:4)=[];
            fprintf(fid_n,'%f %f\n',a);
            clear a;
        elseif double(tline(1))==67   %字母C开始
           [b1,b2,b3,b4]=strread(tline,'%s %s %s %s');
           b=[b1{1},'  ',b2{1}];
            fprintf(fid_n,'%s\n',b);
            clear b b1 b2 b3 b4;
        else
            fprintf(fid_n,'%s\n',tline);
        end
    else
        fprintf(fid_n,'%s\n',tline);
    end
end
fclose(fid);
fclose(fid_n);


---------------------------------------------------------------------------------

4. 注释（不含独立的数字串）+数据（列数相同）：
源文件：

CODE:
你好 abc
欢迎来到 我们
振动论坛
vib.hit.edu.cn
1 11 111 1111
2 22 222 2222
3 33 333 3333
4 44 444 4444
5 55 555 5555


解答：直接用 importdata 便可

注：有时候注释中含有独立的数字串也可以 importdata 成功，不过得到的结果有可能不正确，建议这时候使用第3种情形的读取方式。

5. 注释与数据混排：
对此当然只能自己编程，举例：

源文件：

CODE:
1 11 111 1111
你好
2 22 222 2222
欢迎来到
3 33 333 3333
振动论坛
4 44 444 4444
vib.hit.edu.cn
5 55 555 5555


解答：
--------------------------------------------转--------------------------------------


CODE:

function [data]=distilldata(infile)
%功能说明：
%将保存数据的原始文件中的数值数据读入到一个data变量中
%使用说明：
% infile——原始数据文件名;
% data=数据变量

tmpfile='tmp2.mat';

fidin=fopen(infile,'r'); % 打开原始数据文件（.list）

fidtmp=fopen(tmpfile,'w'); % 创建保存数据文件（不含说明文字）

while ~feof(fidin) % 判断是否为文件末尾
  tline=fgetl(fidin); % 从文件读入一行文本（不含回车键）
  if ~isempty(tline) % 判断是否空行
    [m,n]=size(tline);
    flag=1;
    for i=1:n %判断一行中有没有字符（＋－.Ee和空格键除外）
      if ~(tline(i)==' '|tline(i)=='-'|tline(i)=='.'|tline(i)=='E'...
          |tline(i)=='e'|tline(i)=='+'...
          |(double(tline(i))>=48&&double(tline(i))<=57))
        flag=0;
        break;
      end
    end
    if flag==1 % 如果是数字行，把此行数据写入文件
      fprintf(fidtmp,'%s\n',tline);
    end
  end
end

fclose(fidin);

fclose(fidtmp);

data=textread(tmpfile);

delete(tmpfile);



---------------------------------------------------------------------------------------------------------
另外，如果要求不高，也可以使用 textread 函数跳过注释部分进行读取，不过前提是需要事先知道文件内容的结构（即哪行是数据、哪行是注释）

6.各列数据的分离：
源文件：


CODE:
           0 +  47038.7   1.05  09:26:07  C
           2 +  46477.7   1.03  09:28:38  C  
           4 +  44865.7   1.04  09:28:48  C  
           6 +  41786.4   1.03  09:28:56  C  
           8 +  39896.0   0.97  09:29:03  C  
          10 +  37518.4   0.93  09:29:15  C  
          12 +  35858.5   0.92  09:29:30  C  
          14 +  46105.0   1.03  09:30:21  C  
          16 +  46168.6   6.89  09:30:30  C  
          18 +  48672.3   4.33  09:30:40  C  
          20 +  49565.7   0.49  09:30:48  C  
          22 +  49580.7   0.53  09:30:55  C  
          24 +  49602.3   0.84  09:31:03  C  
          26 +  49582.5   1.51  09:31:11  C  
          28 +  49577.0   1.39  09:31:19  C  
          30 +  49589.3   0.61  09:31:27  C  
          32 +  49578.3   1.06  09:31:29  C  
          34 +  49512.5   1.77  09:31:38  C 




解答：直接用 [a,b,c,d,e,f]=textread(yourfilename,'%d %c %f %f %s %c'); 便可


四. 注意事项：



1. 请在 matlab 中保持当前路径在该数据文件对应的目录下进行存取，否则，存取时请给出该数据文件的具体路径。


 

2. 存取时，请给出该数据文件的全称（包括后缀名，读取mat文件时可省略）


 

3. load data.txt和A=load(‘data.txt’)的区别请参阅精华贴：


 

4. 请根据读写需要来打开文件，即根据你的需要来指定 fopen 的 permission 属性为读或写。如果只用 a 进行写入，就不能用 fread 读取。此时应该写完关闭文件，然后用 r 打开读取，或者直接用 a+ 进行同时读写操作。否则，会产生莫名其妙的问题！以下代码是一个错误的例子：




CODE:

filename='e.dat';
fid=fopen(filename,'a');
if fid<0
    error('fopen error');
end
s=[1 2 3 4;5 6 7 8];
fwrite(fid,s,'float32')
[dd ll]=fread(fid,inf,'float32');％把t中的数据全部读出，即s矩阵。
fclose(fid);






此时得到的dd, ll 是错误且无意义的！


五. 其他相关问题：

1. 连续读取多个文件的数据，并存放在一个矩阵中：
(1) 首先是如何读取文件名：
方法一：
filename=dir(‘*.jpg’);
那么第i个文件的文件名就可以表示为
filename(i).name
文件数量为：length(filename)

方法二：
先在Windows的 MSDOS（命令行）中使用以下命令生成一个list.txt文件：



dir path\folder /on /b /s > path\list.txt


 

举例：dir d:\test /on /b /s > d:\list.txt


 

然后在 matlab 中使用：


 

filename = textread(sFileFullName,'%s');


 

把所有文件名读取到list细胞矩阵中，最后对filename{i}便可得到各文件名。


(2) 然后是读取文件名的数据并存储：
假设每个文件对应的数据是m*n的，则：

CODE:
k = length(filename);

Data = zeros(m,n,k);

for ii = 1:k
  Data(:,:,ii) = yourreadstyle(filename{ii}); %yourreadstyle是对应的文件读取方式的函数
end




2. 连续读取多个文件的数据，并存放在多个矩阵（以文件名命名）中：
假设每个文件对应的数据是m*n的，则以上述第二种文件名读取方法为例：

CODE:
k = length(filename);
for ii = 1:k
  D = yourreadstyle(filename{ii});
eval([‘Data_’, num2str(ii), ‘ = D;’]);
end



3. 文件名命名问题：
文件名为 abc00001,abc00002,... abc00009,abc00010,... abc00099,abc00100,...abc00879.  准备把这些文件名给放到一个数组里面去。

解答：

CODE:
a=cell(879,1);
for k=1:879
     a{k} = sprintf('%.5d',k);
end


4. 上述各种文件格式、类型自动识别问题：可以利用正则表达式来处理，使之通用性较强。例如使用以下代码可以自动处理上面提到了例1到例5各种情形，不过由于存在自动判断，对某些例子（如例1）效率自然要低一点，而对于另外的例子（如例3、例5）效率估计要高一点（少用了一个循环）。


CODE:

function [data]=distilldata_eight(infile)
%功能说明：
%将保存数据的原始文件中的数值数据读入到一个data变量中（自动判断数据行）
%使用说明：
% infile——原始数据文件名;
% data=数据变量

tmpfile='tmp2.mat';

fidin=fopen(infile,'r'); % 打开原始数据文件（.list）

fidtmp=fopen(tmpfile,'w'); % 创建保存数据文件（不含说明文字）

while ~feof(fidin) % 判断是否为文件末尾
  tline=fgetl(fidin); % 从文件读入一行文本（不含回车键）
  if ~isempty(tline) % 判断是否空行
    str = '[^0-9 | \. | \- | \s | e | E]'; %正则表达式为：该行中是否包含除 - . E e 数字 和 空白字符 外的其他字符
    start = regexp(tline,str, 'once');
    if isempty(start)
      fprintf(fidtmp,'%s\n',tline);
    end
  end
end

fclose(fidin);

fclose(fidtmp);

data=textread(tmpfile);

delete(tmpfile)



5. 大量数据的读取问题：
可以考虑使用循环分批读取（特别是在各数据是独立的时候），或者使用稀疏矩阵来实现。另外，也可参考《深入浅出MATLAB 7_X混合编程》一书第一章

6. 读取整个txt文件的内容（获得文件中的所有字符）：

CODE:

f = fopen('yourfilename.txt','rt'); % t 属性根据需要可省略
x = fread(f,'*char');
fclose(f);


7. 把维数不同的矩阵及其变量名保存到一个 txt 文件中，例如 a1 = 123; a2 = [1 2 3;4 5 6] ，希望得到的 txt 文件如下：


QUOTE:

a1：
123
a2：
1 2 3
4 5 6





如果写入的时候简单一点，则可以采用以下方式，不过读取的时候比较麻烦：

CODE:

a1=123;
a2=[1 2 3;4 5 6];
fid = fopen('myfile.txt', 'wt');
for i=1:2
    fprintf(fid, '%s: \n %s\n', ['a',int2str(i)], mat2str(eval(['a',int2str(i)])));
end
fclose(fid);


相反，如果写入的时候复杂一点，则读取的时候会简单一点：

CODE:

a1=123;
a2=[1 2 3;4 5 6];
fid = fopen('myfile.txt', 'wt');
for i=1:2
    fprintf(fid, '%s: \n', ['a',int2str(i)]); 
    b = eval(['a',int2str(i)]);
    fprintf(fid, [repmat('%d ', 1, size(b,2)), '\n'], b');
end
