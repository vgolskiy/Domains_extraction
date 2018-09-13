# Domains_extraction
##Yandex application task


from pandas import DataFrame
from urllib.parse import urlparse
import re
import idna

netlock_tmp = []
domains_tmp = []
domains = []

file_obj = open("urls_set.csv", 'r', encoding='utf-8', errors='strict')
for i, line in enumerate(file_obj):
    data = urlparse(line.strip()) #line without unnecessary symbols + parsing
    netlock_tmp.append(data.netloc)
    
for i in range(len(netlock_tmp)): #encoding the net location
    if re.search('xn--p1ai', netlock_tmp[i]):
        domains_tmp.append(idna.decode(netlock_tmp[i]))
    else:
        domains_tmp.append(netlock_tmp[i]) 

netlock_tmp = [] #clearing buffer

for i in range(len(domains_tmp)):
    domains.append(domains_tmp.pop().rsplit('.', 1)) #moving elements from one list to another with simultaneously deleting to save memory

domains = DataFrame(domains, columns = ['A', 'B'])
domains.drop(['A'], axis=1, inplace=True) #removing unnecessary data

domains = domains['B'].str.split('\:').str[0] #removing ports data

domains = domains.drop_duplicates() #removing duplicates

domains.to_csv('domains_list.csv', sep='\n', encoding='utf-16', header = False, index = False) #writing to file
