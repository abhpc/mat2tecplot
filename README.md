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
outputfile=$inputfile.tp
tmpout=$outputfile.tmp

M=`cat $inputfile|grep -v "#"|wc -l`
N=`head -2 $inputfile|tail -1| awk '{print NF}'`

dY=`echo |awk '{print ('$ymax'-1.0*'$ymin')/('$N'-1.0)}'`
dX=`echo |awk '{print ('$xmax'-1.0*'$xmin')/('$M'-1.0)}'`

#echo "M=$M N=$N dX=$dX dY=$dY"
#sleep 20

rm -rf $outputfile $tmpout

MAT=`cat $inputfile|grep -v "#"`

echo "TITLE = \"Tecplot Data Format\"" >> $outputfile
echo "VARIABLES = \"X\", \"Y\", \"$colorlabel\"" >> $outputfile
echo "ZONE I=$M, J=$N, F=POINT" >> $outputfile

i=1;

for k in $MAT
do
	m=$[($i-1)/$N+1];
	n=$[$i-$m*$N+$N];
	echo "$m $n $k" >> $tmpout
	i=$[$i+1];
done

awk -v a0="$xmin" -v a1="$dX" -v b0="$ymin" -v b1="$dY" '{print a0+($1-1)*a1, b0+($2-1)*b1, $3}' $tmpout >> $outputfile
rm -rf $tmpout
```
