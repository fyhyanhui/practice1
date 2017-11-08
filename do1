*******************************************************************************/
*						2016农户调查医疗卫生部分合并和数据检查（前期数据清理由常皓、杨帆和李夏琳完成）

*	Goal: 数据检查、合并和进一步清理			  
*   Input Data : PPIRD2016_K0_cleaned_29Aug2016_lixialin.dta
*				 merge+k2-1+k2-2

*	Output Data: HHS2016_HEALTH_CHECK.do
*    			 HHS2016_HEALTH_K0_CLEAN_28MAR2017.dta            

*   Author(s): 易红梅
*	Created: 2016-08-29	
*   Last Modified: 2017-03-28 易红梅
*******************************************************************************/
clear
set more off
set scrollbufsize 2048000
capture log close

global workingdir     "D:\Clean Data\100 Villages\Data2016\Household\CHECK\"
cd "$workingdir" // change directory

********************************************************************************
***PART 1. K0
***PART 2. K2-1&K2-2
***PART 3. merge k0, 2-1, and 2-2
***PART 4. save cleaned data
********************************************************************************
use "D:\Clean Data\100 Villages\Data2016\Household\2016农户问卷K0\dta\PIRD2016_K0_cleaned_29Aug2016_lixialin", clear

//2017 observations

//居民健康档案和健康教育
tab1	k0pid k001
recode	k001 (2=0)
label	define yesno 0 "no" 1 "yes"
label	values k001 yesno
tab 	k001 k002,mi
replace	k002 = . if k001 == 0
tab1 k003 k004
list	hhid k001 k003 k004 if k003 == 3
replace	k003 = 2 if k003 == 3
label	define participation 0 "没参加过" 1 "参加过"
label	values k003 participation
label	values k004 participation

//孕妇及产妇健康管理
tab1	k005
recode	k005 (2=0)
label	values k005 yesno
tab1 	k006_1 k006_2,mi
npresent k006_2 k007_2 k008_2 k009_2 k009_2a k010_2 k011_2 k012_2 k013_2 k014_2 k014_2a k015_2 k016_2
drop	k006_2 k007_2 k008_2 k009_2 k009_2a k010_2 k011_2 k012_2 k013_2 k014_2 k014_2a k015_2 k016_2  //each family has at most one pregnant women
foreach num of numlist 6(1)9 {
	rename	k00`num'_1 k00`num'
	}
foreach num of numlist 10(1)16 {
	rename	k0`num'_1 k0`num'
	}
ren	k009_1a k009a
ren	k014_1a k014a
foreach num of numlist 1(1)6 {
	rename	k014_1_`num' k014_`num'
	}

tab1	k006-k016 if k005 == 0
for x in varlist k014_* : replace	x = . if k005 == 0
tab1	k006-k007 if k005 == 1,mi
recode	k007 (1=0) (2=1)
tab		k007
label	values k007 yesno
tab1	k008 if k007 == 1,mi
replace	k008 = . if k008 == 999
tab1	k009 if k008 >0 & k008 != .,mi
replace	k009 = . if k009 == 999
tab1	k008 k009 if k007 == 0,mi
replace	k009 = . if k007 == 0

tab 	k010 if k007 == 0,mi
replace	k010 = . if k010 == 999
list 	hhid k008 k009 k010 if k007 == 1 & k010 ! = .  // 查看农户表原件A表，看这几户家里是否有新生儿及其年龄，如果是在2016年之前出生，K010就应该空着；如果之后出生，k008和k009应该空着

replace k010 = . if hhid == "612107"  //此户2015年已生。
replace k010 = . if hhid == "351101"  //此户2015年已生。
replace k010 = . if hhid == "351205"  //此户2015年已生。
replace k010 = . if hhid == "331223"  //此户2015年已生。
replace k010 = . if hhid == "532225"  //此户2015年已生。
replace k010 = . if hhid == "142206"  //此户2015年已生。

tab1 	k011 k012 k013 if k005 == 1,mi
recode	k011 2=0
label	values k011 yesno
replace	k012 = "1" if k012 == "1、住在市里"
replace	k012 = "" if k012 == "999"
destring k012,replace
replace	k013 = . if k013 == 999

tab1	k015 k016 if k013 != 0 & k013!= .,mi
replace	k015 = . if k015 == 999 |k015 == 9999
replace	k016 = . if k016 == 999 |k016 == 9999
count	if k016>k015 & k016 != .

tab	k017,mi
recode	k017 (2=0)
label	values k017 yesno

//儿童免疫
tab		k018,mi
recode	k018 (2=0)
npresent	k019_4 k020_4 k021_4 k021_4a k022_4 k022_4a k023_4 k024_4	// one family has four children udner 6

foreach num of numlist 1(1)4 {
	order k020_`num' k021_`num' k021_`num'a k022_`num' k022_`num'a k023_`num' k024_`num',after(k019_`num')
	}
*first children in the family
tab k019_1 if k018 == 0,mi
list	hhid k019_1-k024_1 if k018 == 0 &k019_1 != ""
replace	k018 = 1 if hhid == "411224"
tab 	k019_1 if k018 == 1,mi
replace	k019_1 = "105" if k019_1 == "105、下半年满7周岁"
destring k019_1,replace
list	hhid k019* k020_* k022_*  if k018 == 1 & k019_1 == .	// 这四个家庭报告说利用了儿童免疫服务但是没有利用人的具体信息，需要查表A看该户是否真的有0-6岁儿童
                                                                // 541327，541302无0-6岁儿童；351201有一个2010年出生的儿童；621108有一个2009年出生的儿童。
																
replace	k019_1 = 107 if hhid == "351201"							//把上面农户家里的那个儿童的编码填写这里
replace	k019_1 = 106 if hhid == "621108"


replace	k020_1 = . if k018 == 1 & k019_1 == . & k020_1 == 2
tab		k020_1 if k019_1 != .,mi
recode	k020_1 (2=0)
label	values k020_1 yesno
tab 	k020_1 k021_1,mi
tab		k020_1 k022_1,mi
tab		k020_1 k023_1,mi
replace	k023_1 = . if k023_1 == 999
recode	k023_1 (2=0)
label	values k023_1 yesno
tab		k024_1 if k023_1 == 1,mi
tab		k024_1 if k023_1 == 0,mi
replace	k024_1 = "" if k024_1 == "999"
destring	k014_1,replace
*second children
tab		k019_2,mi  //90 children
tab		k020_2 if k019_2 != .
recode	k020_2 (2=0)
tab		k020_2,mi
label	values k020_2 yesno
tab		k020_2 k021_2 if k019_2!= .
replace	k021_2 = "" if k021_2 == "999"
tab		k020_2 k022_2 if k019_2!= .
tab		k020_2 k023_2 if k019_2!= .
replace	k023_2 = . if k023_2 == 999
recode	k023_2 (2=0)
label	values k023_2 yesno
tab		k024_2 if k023_2 == 1,mi
replace	k023_2 = . if k023_2 == 999
*third children
tab		k019_3,mi  //6 children
tab		k020_3 if k019_3 != .
recode	k020_3 (2=0)
tab		k020_3,mi
label	values k020_3 yesno
tab		k020_3 k021_3 if k019_3!= .
replace	k021_3 = "" if k021_3 == "999"
tab		k020_3 k022_3 if k019_3!= .
tab		k020_3 k023_3 if k019_3!= .
recode	k023_3 (2=0)
label	values k023_3 yesno
tab		k024_3 if k023_3 == 1,mi
*fourth children
tab		k019_4,mi  //1 children
tab		k020_4 if k019_4 != .
recode	k020_4 (2=0)
tab		k020_4,mi
label	values k020_4 yesno
tab		k020_4 k021_4 if k019_4!= .
tab		k020_4 k022_4 if k019_4!= .
tab		k020_4 k023_4 if k019_4!= .
recode	k023_4 (2=0)
label	values k023_4 yesno
tab		k024_4 if k023_4 == 1,mi

tab1 	k025 k026 k027 k028,mi
replace	k027 = . if k027 == 999
replace	k028 = . if k028 == 999
foreach var of varlist k025 k026 k027 k028 {
	recode	`var' (2=0)
	label	values `var' yesno
	}

///慢性病管理
//高血压
tab k030		//920 individual with hypertension
recode	k030 (2=0)
label	values k030 yesno
//first member
foreach num of numlist 1(1)3 {
	order	k032_`num' k033_`num' k033_`num'a k034_`num' k035_`num' k036_`num',after(k031_`num')
	}

tab	k031_1 //915 individuals
list	hhid k031_2 k032_1 k033_1 k031_3 k032_2 k033_2 k034_2 k035_2 k036_2 if k030== 1 & k031_1 == . //这五户报告说（k030）家里有慢性病患者，但是有没有列出是谁，需要查原件
 
 //串列 except 521107
tab1		k03*_1 k03*_2
destring	k03*_1 k03*_2,replace
foreach	num1 of numlist 152222 132103 641121 341118 622114 {
	foreach num2 of numlist 1(1)6 {
		replace	k03`num2'_1 = k03`num2'_2 if hhid == "`num1'"
		replace	k03`num2'_2 = . if hhid == "`num1'"
		}
		}
		                                                               
tab k032_1 if k031_1 != . 
replace	k032_1 = . if k032_1 == 999
replace k032_1 = . if k032_1 == 18 //问卷上填错了
tab 	k033_1 if k031_1 == .
tab 	k033_1 if k031_1 != .
tab 	k033_1a if k033_1 == 6  //需要对其他中的医院做进一步的归类。例如“成都市第一人民医院”、“宁波市医院"都应该是选项“5”；县上的医院都应该是“4”.。


duplicates examples k033_1a
replace k033_1 = 1 if k033_1a == "本村私人诊所" 
replace k033_1 = 2 if k033_1a == "县上中医私人诊所" | k033_1a == "县上诊所" | k033_1a == "成都龙泉私人诊所"  | k033_1a == "陕西延家煤矿诊所"
replace k033_1 = 3 if k033_1a == "别的乡镇卫生院距离近" 
replace k033_1 = 4 if k033_1a == "903医院" | k033_1a == "单位体检" | k033_1a == "县中医院" | k033_1a ==  "西峡县医院"
replace k033_1 = 5 if k033_1a == "南充医院" | k033_1a == "四川省人民医院" | k033_1a == "宁波的医院" | k033_1a == " 山西医院" | k033_1a == "市医院" | k033_1a == "广安市人民医院" ///
                               | k033_1a == "成都华西医院" | k033_1a == "成都第一人民医院" | k033_1a == "深圳市医院" | k033_1a == "衡水二院" | k033_1a == "衡水市医院" ///
							   | k033_1a == "衡水第二人民医院" | k033_1a == "辽源市矿医院" | k033_1a == "长春" | k033_1a == "雅安某医院" | k033_1a == "集安中医院"
						   

tab		k034_1 if k031_1 != .
tab		k035_1 if k034_1 == 2|k034_1 == 3|k034_1 == 4,mi
tab		k036_1 if k031_1 != .
destring	k036_1,replace
recode	k036_1 (2=0)
label	values k036_1 yesno
*second member
tab		k031_2 if k030 == 0
tab		k031_2 // 199 individuals
tab k032_2 if k031_2 != . 
tab 	k033_2 if k031_2 == .
tab 	k033_2 if k031_2 != .
replace	k033_2 = . if k033_2 == 999
list	hhid k031_2-k036_2 if k033_2 == 2005
replace	k033_2 = . if k033_2 == 2005 //问卷上就填错了
tab 	k033_2a if k033_2 == 6  //需要对其他中的医院做进一步的归类。例如“成都市第一人民医院”、“宁波市医院"都应该是选项“5”；县上的医院都应该是“4”.。

replace k033_2 = 3 if k033_2a == "清凉镇卫生院" 
replace k033_2 = 4 if k033_2a == "县中医院" | k033_2a == "西峡县医院" | k033_2a == " 集安中医院"
replace k033_2 = 2 if k033_2a == "县城一家诊所"
replace k033_2 = 5 if k033_2a == "市医院" | k033_2a == "辽源市中医院"

tab		k034_2 if k031_2 != .
tab		k035_2 if k034_2 == 2|k034_2 == 3|k034_2 == 4,mi
tab		k036_2 if k031_2 != .
recode	k036_2 (2=0)
label	values k036_2 yesno
*third member
tab		k031_3 if k030 == 0
tab		k031_3 // 7 individuals
tab k032_3 if k031_3 != . 
tab 	k033_3 if k031_3 == .
tab 	k033_3 if k031_3 != .
tab 	k033_3a if k033_3 == 6  //需要对其他中的医院做进一步的归类。例如“成都市第一人民医院”、“宁波市医院"都应该是选项“5”；县上的医院都应该是“4”.。
replace	k033_3 = 5 if k033_3 == 6 & k033_3a == "深圳"
tab		k034_3 if k031_3 != .
tab		k035_3 if k034_3 == 2|k034_3 == 3|k034_3 == 4,mi
tab		k036_3 if k031_3 != .
recode	k036_3 (2=0)
label	values k036_3 yesno


//糖尿病
tab 	k037,mi
recode	k037 (2=0)
label	values k037 yesno 
tab		k038,mi
recode	k038 (2=0)
label	values k038 yesno //173 hhs
*first member
order k040_1 k041_1 k041_1a k042_1 k043_1 k044_1,after(k039_1)
tab		k038 k039_1,mi //173 individual
tab 	k040_1 if k039_1 == 0|k039_1 == .
tab 	k040_1 if k038 == 1,mi
tab 	k041_1 if k038 == 1,mi
tab 	k041_1a if k041_1 == 6 //需要对其他中的医院做进一步的归类。例如“成都市第一人民医院”、“宁波市医院"都应该是选项“5”；县上的医院都应该是“4”.。

replace k041_1 = 3 if k041_1a == "藁城常安镇卫生院"
replace k041_1 = 4 if k041_1a == "中医院" 
replace k041_1 = 5 if k041_1a == "市医院" | k041_1a == " 延安市博爱医院" | k041_1a == "成都" | k041_1a ==  "衡水二院" | k041_1a == "集安市医院"
           


tab		k042_1 if k038 == 1,mi
destring	k042_1,replace
tab		k043_1 if k038 == 1& k042_1 != 1,mi
replace	k042_1 = 1 if k043_1 == 0
tab 	k044_1 if k038 == 1,mi 
recode	k044_1 (2=0)
label	values k044_1 yesno
//second member
npresent	k039_2 k040_2 k041_2 k042_2 k043_2 k044_2  // 14 individual
tab		k038 k039_2,mi //14 individual
tab 	k040_2 if k039_2 == .
tab 	k040_2 if k038 == 1,mi
tab 	k041_2 if k038 == 1,mi
tab 	k041_2a if k041_2 == 6 //需要对其他中的医院做进一步的归类。例如“成都市第一人民医院”、“宁波市医院"都应该是选项“5”；县上的医院都应该是“4”.。
replace k041_2 = 5 if k041_2a == "邯郸"
tab		k042_2 if k038 == 1,mi
destring	k042_2,replace
tab		k043_2 if k038 == 1& k042_2 != 1,mi
tab 	k044_2 if k038 == 1,mi 
recode	k044_2 (2=0)
label	values k044_2 yesno
//patient flow
tab1	k045 k046 k047,mi
recode	k045 (2=0)
label	values k045 yesno
recode	k046 (2=0)
label	values k046 yesno
list	k046-k050 if k046 == 0&k047 !=.
replace	k047 = . if k046 == 0&k047 !=.
tab		k047a if k047 == 6 //需要对其他中的医院做进一步的归类。例如“成都市第一人民医院”、“宁波市医院"都应该是选项“5”；县上的医院都应该是“4”.。
replace k047 = 1 if k047a == "村内个人诊所" | k047a == "村里的医生"
replace k047 = 3 if k047a == "窦庄卫生室" | k047a == "窦庄诊所" | k047a == "附近外乡镇卫生院"
replace k047 = 4 if k047a == "903医院" | k047a ==  "中医院"
replace k047 = 5 if k047a == "市医院" | k047a == "延吉的医院" | k047a == " 衡水第四人民医院"

tab1 	k048 k049 k050,mi
recode	k048 (2=0)
label	values k048 yesno
recode	k049 (2=0)
label	values k049 yesno
list	hhid k047-k049 k050 if k050 != "" & k049 == .
replace	k048 = 1 if k050 != "" & k049 == .
replace	k049 = 1 if k050 != "" & k049 == .
tab		k050a if k050 == "6" //需要对其他中的医院做进一步的归类。例如“成都市第一人民医院”、“宁波市医院"都应该是选项“5”；县上的医院都应该是“4”.。
replace k050 = "5" if k050a == "市医院" |k050a == "扬州" | k050a == "郑州人民医院"
replace k050 = "3" if k050a == "藁城常安镇医院"
 
save	"D:\Clean Data\100 Villages\Data2016\Household\CleanData\HHS2016_HEALTH_K0_CLEAN_28MAR2017",replace
