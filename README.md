from bs4 import BeautifulSoup
from math import floor
print('inputs are not case sensetive, make sure the names are exactly the same as in the logs, order in which you enter html of the logs doesn\'t matter')
d={}
fails={}
totaldmg=0
leftover=0
hostercut=0.1                   #can change the cut here
healcut=0.0453488372093023      #can change the cut here
tankcut=0.0515988372093023      #can change the cut here
tanks=[i.capitalize() for i in input('ENTER THE NAMES OF TANKS (separated by space):').split()]
healers=[i.capitalize() for i in input('ENTER THE NAMES OF HEALERS (separated by space):').split()]
hosters=[i.capitalize() for i in input('ENTER THE NAMES OF HOSTERS (separated by space):').split()]
thirdtank=input('ENTER THE NAME OF 3RD TANK (input "1" without quotations if there is none):').capitalize()
pot=float(input('ENTER THE TOTAL POT:'))
if thirdtank=='1':
    thirdtankcut=0
else:
    thirdtankcut=0.01         #can change the additional cut for the 3rd tank here
for i in [i.capitalize() for i in input('ENTER THE NAMES OF PLAYERS WHO FAILED, IMMIDIATELY FOLLOWED BY NUMBER OF FAILS (separated by space, such as:erien1 amarelo2):').split()]:
    for k in i:
        if k.isdigit():
            break
    fails[i[:i.index(k)]]=i[i.index(k):]
page_html3=input('ENTER HTML FROM 1ST LOG:')
page_html1=input('ENTER HTML FROM 2ST LOG:')
page_html2=input('ENTER HTML FROM 3ST LOG:')
dpscut=(1-(len(hosters)*hostercut+len(healers)*healcut+len(tanks)*tankcut+thirdtankcut))*pot
page_data=BeautifulSoup(page_html1, 'lxml')
left=page_data.find_all('div',{'class':'bar_label_left'})
for k in range(len(left)):
    name=(str(left[k].find_all('span')[1])[28:-7])
    if name=='Unknown' or name in tanks or name in healers:
        None
    else:
        fix= float(''.join([i for i in str((page_data.find_all('div',{'class':'bar_label_right'})[k].find('span')))[28:-7] if i!=',']))
        d[name]=(fix)
        totaldmg+=fix
def rest(page_html):
    page_data=BeautifulSoup(page_html, 'lxml')
    left=page_data.find_all('div',{'class':'bar_label_left'})
    for k in range(len(left)):
        check=True
        name=(str(left[k].find_all('span')[1])[28:-7])
        if name=='Unknown' or name in tanks or name in healers:
            check=False
        elif name not in d:
            d[name]=0
        if check:
            fix= float(''.join([i for i in str((page_data.find_all('div',{'class':'bar_label_right'})[k].find('span')))[28:-7] if i!=',']))
            d[name]+=(fix)
            global totaldmg
            totaldmg+=fix
rest(page_html2)
rest(page_html3)
for i in d:
    d[i]=(d[i]/totaldmg)*dpscut
    if i==thirdtank:
        d[i]+=pot*thirdtankcut
for i in tanks:
    d[i]=pot*tankcut
for i in healers:
    d[i]=pot*healcut
for i in hosters:
    d[i]+=pot*hostercut
for i in fails:
    leftover+=d[i]*0.05**int(fails[i])
    d[i]=d[i]*0.95**int(fails[i])
leftover=leftover/len(d)
for i in d:
    print(f'{i}: {floor(d[i]+leftover)}')
