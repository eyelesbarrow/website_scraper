from bs4 import BeautifulSoup
import urllib.request
import re
import csv
import requests
import os
import logging



path = os.chdir(r"C:\Users\k.alave\Documents" )
website = "https://www.project-a.com/en/portfolio"
html_page = urllib.request.urlopen(website)
main_page = BeautifulSoup(html_page, 'html.parser')
all_urls = []



def get_portfolio_links():
    """opens the website and captures all the links of the companies to a list"""

    for link in main_page.findAll('a', attrs={'href': re.compile("^https://www.project-a.com/en/portfolio/")}):
        all_urls.append((link.get('href')))
    return all_urls


def company_profiles(all_urls):
    """function that scrapes the project-a website and creates a csv table with portfolio company details"""
    with_headers = False

    for i, each_link in enumerate(all_urls, 1): 

        try:

            portfolio_page = requests.get(each_link)
            portfolio_text = portfolio_page.text
            soup = BeautifulSoup(portfolio_text, 'html.parser')

            results_dict = {}
            
            #gets the website name and puts it in a dictionary
            portfolio_name = soup.find('h1', {'class' : 'Headline js-Headline PortfolioDetailTemplate__headline'})
            results_dict['Name']= portfolio_name.get_text().strip() 
            
            #gets the website name from the link in case it is not read on the page
            results_dict = {k: each_link.split("/")[-1] if not v else v for k, v in results_dict.items() } 
            
            #creates a dictionary for description
            portfolio_description = soup.find('h2', {'class' : 'Subhead js-Subhead PortfolioDetailTemplate__subhead'})
            results_dict['Description']= portfolio_description.get_text().strip() 
            
            portfolio_facts = soup.find_all('div', {'class' : "Group__tile Group__tile--columns2"})

             #extracts other info on the company
            for group in portfolio_facts: 
                col_name = group.find('div', {'class': "KeyValue__key"}).get_text().strip()
                val = group.find('div', {'class': "KeyValue__value"}).get_text().strip()

                if col_name in results_dict:
                    results_dict[f'{col_name}_'] = val #in case the tag on the info is identical
                else:
                    results_dict[col_name] = val

                if 'Website' not in results_dict: #for companies without a website, creates a new key:value pair
                    results_dict['Website_'] = 'NaN'
                if 'Founded_' in results_dict: 
                    results_dict['Management'] = results_dict['Founded_']

                results_dict['Website'] = results_dict['Website_'] #reassigns website value to the proper key
            
            #gets the summary text and creates a dictionary for it
            portfolio_summary = soup.find('div', {'class' : "Wysiwyg js-Wysiwyg PortfolioDetailTemplate__wysiwyg"})
            results_dict['Summary'] = portfolio_summary.get_text() 
            print(i, each_link)
    

        except KeyError: 
            print(i, each_link)
            pass


        with open('portfolios_list.csv', 'a+', newline='', encoding='utf-8' ) as filename:


            try:
                column_names = ["company_name", "description", "date_founded", "location", "segment", "investment", "management", "website", "info"]
                writer = csv.DictWriter(filename, fieldnames=column_names, delimiter=";", skipinitialspace=True, restval='NaN')

                if not with_headers:
                    writer.writeheader() 
                    with_headers=True

                writer.writerow({'company_name':results_dict['Name'], 'description': results_dict['Description'], 'date_founded':results_dict['Founded'], 
                            'location':results_dict['Location'],'management': results_dict['Management'], 
                            'investment' : results_dict['Investment'], 'website': results_dict['Website'], 
                            'segment': results_dict['Segment'],  'info': results_dict['Summary']})
                
            

            except KeyError:
                pass

        filename.close()



if__name__ == "__main__":

get_portfolio_links()        
company_profiles(all_urls)
