clear all
set more off
log using Emil,replace
cap log close 
cd "C:\Users\User\Desktop\Bagadii\ITNs"
dir
use SLIR7AFL.DTA 

* creating weighting variable
fre v005
gen wt=v005/1000000
fre wt

fre v021
fre v023
svyset v021 [pweight=wt], strata(v023)

*Using only women currently pregnant*
codebook v213
fre v213
keep if v213==1
fre v213

//outcome variables //

*Ownership of ITNs*
codebook v459
fre v459
rename v459 ownership
label var ownership "Ownership of ITNs"
tab ownership

*Usage of ITNs
codebook v461
fre v461
rename v461 usage
label var usage "utilidation of ITNs"
tab usage

//independent variables//

*Age*
codebook v013
fre v013
rename v013 agecat
label var agecat "age of respondents"
tab agecat

*Education*
codebook v106
fre v106
rename v106 edu
label var edu "educational level"
tab edu

*Wealth*
codebook v190
fre v190
rename v190 wealth
label var wealth "wealth status"
tab wealth

*Religion*
codebook v130
fre v130
recode  v130 (1=1 "christianity") (2=2 "Islam") (5=3 "traditional/other"), gen (rel)
label var rel "religion"
tab rel

*Marital* 
codebook v501
fre v501
recode v501 (1=1 "married")  (0 2/5=2 "cohabitaion"), gen(marital)
label var marital "marital status"
tab marital

*Health decision*
codebook v743a
fre v743a
recode v743a (1=1 "alone" ) (2=3 "with partner" ) (4=2 "partner alone") (5/6 .=4 "other"), gen(dhealth)
label var dhealth "health decision making"
tab dhealth

*Wanted Pregnancy*
codebook m10_1
fre m10_1
recode m10_1 (3 .=1 "not at all") (1=2 "then") (2=3 "later"), gen (wanted)
label var wanted "wanted pregnancy"
tab wanted

*ANC attendance*
codebook m14_1
fre m14_1
recode m14_1 (0 98 .=0 "no anc") (1/3= 2 "1-3") (4/max=3 "4 or more"), gen (anc)
label var anc "ANC attendance"
tab anc

*Radio*
codebook v158
fre v158
rename v158 radio
label var radio "frequency of listening to radio"
tab radio

*Televion*
codebook v159
fre v159
rename v159 tv
label var tv "frequency of watching television"
tab tv

*Newspaper*
codebook v157
fre v157
recode v157 (0=0 "not at all") (1=1 "less than once a week") (2/.=3 "atleast once a week"), gen(news)
label var news "frequency of reading newspaper"
tab news

*Residence*
codebook v025
fre v025
rename v025 residence
label var residence "place of residence"
tab residence

*Region*
codebook v024
fre v024
rename v024 reg
label var reg "region"
tab reg

keep v005 wt v021 v023 v213 ownership usage agecat edu wealth rel  marital dhealth wanted radio tv news residence reg    anc

*Desreiptive analysis*

tab ownership 
tab ownership [aw=wt]

tab usage
tab usage [aw=wt]

*Checking multicollinearity*
collin agecat edu wealth rel  marital dhealth wanted  radio tv news residence reg anc

fre ownership
fre usage 
fre agecat
fre edu
fre wealth
fre rel
fre marital
fre dhealth
fre wanted
fre anc
fre radio
fre tv
fre news
fre residence
fre reg

asdoc tab1 agecat edu wealth rel marital dhealth wanted anc radio tv news residence reg

///analysis 
///background xtics 

tab agecat [aw=wt]
tab edu [aw=wt]
tab wealth [aw=wt]
tab rel [aw=wt]
tab marital [aw=wt]
tab dhealth [aw=wt]
tab wanted [aw=wt]
tab anc [aw=wt]
tab radio [aw=wt]
tab tv [aw=wt]
tab news [aw=wt]
tab residence [aw=wt]
tab reg [aw=wt]

*Proportion of ownership of ITNs*
tab ownership 
tab ownership [aw=wt]

*Proportion of usage of ITNs*
tab usage if ownership==1
tab  usage [aw=wt] if ownership==1

*Bivariate for ownership of ITNs* 
tab agecat ownership, row chi
tab edu ownership, row chi
tab wealth ownership, row chi
tab rel ownership, row chi
tab marital ownership, row chi
tab dhealth ownership, row chi
tab wanted ownership, row chi
tab anc ownership, row chi
tab radio ownership, row chi
tab tv ownership, row chi
tab news ownership, row chi
tab residence ownership, row chi
tab reg ownership, row chi

*Bivariate for utilisation of ITNs*
tab agecat usage if ownership==1, row chi
tab edu usage if ownership==1, row chi
tab wealth usage if ownership==1, row chi
tab rel usage if ownership==1, row chi
tab marital usage if ownership==1, row chi
tab dhealth usage if ownership==1, row chi
tab wanted usage if ownership==1, row chi
tab anc usage if ownership==1, row chi
tab radio usage if ownership==1, row chi
tab tv usage if ownership==1, row chi
tab news usage if ownership==1, row chi
tab residence usage if ownership==1, row chi
tab reg usage if ownership==1, row chi

*Logistic regression for Ownership of ITNs*
logit ownership b(7).agecat b(0).edu b(1).wealth b(1).rel b(2).marital b(2).dhealth b(2).wanted b(0).anc b(0).radio b(0).tv  b(0).new b(1).residence b(2).reg, or

eststo model2
esttab model2, eform ci pr2 nogaps

*Logistic regression for Utilisation of ITNs*
logit usage b(1).agecat b(0).edu b(1).wealth b(1).rel b(2).marital b(2).dhealth b(2).wanted b(0).anc b(0).radio b(0).tv  b(0).news  b(1).ownership b(1).residence b(2).reg if ownership==1, or

eststo model3
esttab model3, eform ci pr2 nogaps



