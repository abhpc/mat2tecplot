# mat2tecplot
将m*n矩阵转化为tecplot数据格式，输入格式比如“./mat2tecplot.sh -2000 2000 -500 500 c_keatom test.txt”，其中mat2tecplot.sh文件内容如下：
```
#! /bin/bash

if [ ! $# == 6 ]; then
echo "Need parameters! Usage: $0 number_of_row number_of_column min_of_x max_of_x min_of_y max_of_y colorlabel input_file output_file"
exit
fi

xmin="$1"
xmax="$2"
ymin="$3"
ymax="$4"
colorlabel="$5"
inputfile="$6"
outputfile=$inputfile.tecplot.dat

M=`cat $inputfile|wc -l`
N="head -1 $inputfile | awk '{print NF}'"

rm -rf $outputfile

echo "TITLE = Tecplot Data Format" >> $outputfile
echo "VARIABLES = "X", "Y", "$colorlabel"" >> $outputfile
echo "ZONE I=$M, J=$N, F=POINT" >> $outputfile

for ((i=1;i<$M+1;i++))
{
   for ((j=1;j<$N+1;j++))
   {
      data=$(awk 'NR=='$i'{print $'$j'}' $inputfile)
      yy=$[$ymin+($i-1)*($ymax-$ymin)/($N-1)|bc]
      xx=$[$xmin+($j-1)*($xmax-$xmin)/($M-1)|bc]
      echo "$xx,$yy,$data" >> $outputfile
   }
}
```
