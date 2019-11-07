# mat2tecplot
将m*n矩阵转化为tecplot数据格式，输入格式比如“./mat2tecplot.sh -2000 2000 -500 500 c_keatom test.txt”，输出文件为输入文件加后缀".tecplot.dat",其中mat2tecplot.sh文件内容如下：
```
#! /bin/bash

if [ ! $# == 6 ]; then
echo "Need parameters! Usage: $0 Xmin Xmax Ymin Ymax Label input_file"
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
N=`head -1 $inputfile | awk '{print NF}'`

rm -rf $outputfile

MAT=`cat $inputfile`

echo "TITLE = Tecplot Data Format" >> $outputfile
echo "VARIABLES = "X", "Y", "$colorlabel"" >> $outputfile
echo "ZONE I=$M, J=$N, F=POINT" >> $outputfile

i=1;

for k in $MAT
do
	m=$[$i/$N+1];
	n=$[$i-$m*$N+$N];
	#echo i=$i m=$m n=$n
	#sleep 1
	yy=$[$ymin+($m-1)*($ymax-$ymin)/($N-1)|bc]
	xx=$[$xmin+($n-1)*($xmax-$xmin)/($M-1)|bc]
	echo "$xx $yy $k" >> $outputfile
	i=$[$i+1];
done
```
