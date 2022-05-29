import numpy as np
import selenium
import pandas as pd
from pandas import DataFrame
# need to install chromedriver here first
from selenium.webdriver.common.by import By
#chromedriver.exe is needed in path to run the script
PATH = "C:\Program Files (x86)\chromedriver.exe"
from selenium import webdriver
import os
from selenium.webdriver.chrome.options import Options
def scrape(iterations):
    options = Options()

    link = "https://www.autotrader.co.uk/car-search?sort=relevance&postcode=sa18ep&radius=1500&include-delivery-option=on&price-from=250000&year-to=2022&ulez-compliant=on&keywords=owner&page=1"
    driver = webdriver.Chrome(PATH)

    #driver.get('https://www.autotrader.co.uk/car-search?postcode=sa18ep&year-to=2022&include-delivery-option=on&advertising-location=at_cars&page=1')

    # IF on default chrome profile without adblock ACCEPT COOKIE POPUP upon page load, OTHERWISE ALL VARIBALES WILL BE ""
    driver.get(link)
    # driver.find_element_by_xpath('/html/body/div/div[2]/div[3]/div[2]/button[2]')
    index = 0
    df = pd.DataFrame(columns=["Price", "Make", "Model", "Reg-Year", "Configuration", "Mileage(Miles)", "EngSize(Litres)", "Power(BHP)","Control", "Fuel-Type", "Owners"])

    for i in range(iterations):

        listings = driver.find_elements_by_css_selector('li.search-page__result')

        for listing in listings:
            boolOwn = True
            index = index +1
            price = listing.find_elements(by=By.CLASS_NAME,value="product-card-pricing")[0].text
            price = price.partition('£')[2]
            makeModel = listing.find_elements(by=By.CLASS_NAME,value='product-card-details__title')[0].text
            make = makeModel.partition(' ')[0]
            try:
                model = makeModel.split(' ')[1]
            except:
                model = "ns"
            regYear = listing.find_elements(by=By.CLASS_NAME,value='atc-type-picanto--medium')[0].text
            regYear = regYear.partition(' ')[0]
            configuration = listing.find_elements(by=By.CLASS_NAME,value='atc-type-picanto--medium')[1].text
            mileage = listing.find_elements(by=By.CLASS_NAME,value='atc-type-picanto--medium')[2].text
            mileage = mileage.partition(' ')[0]

            engineSize = listing.find_elements(by=By.CLASS_NAME,value='atc-type-picanto--medium')[3].text
            engineSize = engineSize.partition('L')[0]

            #convert ps to bhp
            enginePower = listing.find_elements(by=By.CLASS_NAME,value='atc-type-picanto--medium')[4].text
            if(enginePower.__contains__("BHP")):
                enginePower = enginePower.partition('B')[0]
            elif(enginePower.__contains__("PS")):
                enginePower = enginePower.partition('P')[0]
                enginePower = round(int(enginePower)/(1.014))

            controlType = listing.find_elements(by=By.CLASS_NAME,value='atc-type-picanto--medium')[5].text
            try:
                fuelType = listing.find_elements(by=By.CLASS_NAME,value='atc-type-picanto--medium')[6].text
            except:
                fuelType = "ns"
            try:
                owners = listing.find_elements(by=By.CLASS_NAME,value='atc-type-picanto--medium')[7].text
                owners = owners.partition(' ')[0]
            except:
                owners = "ns"
                boolOwn = False
            if ((owners == "ULEZ") or (owners == "")or (owners.__contains__("Full")) or (owners.__contains__("Part"))):
                owners = "ns"
                boolOwn = False

            #if any of them null then dont append and only take first strings and numbers from each before appendation
            if (boolOwn == True):
                print( str(index) + ": " + "price(£): " + price + " make: " + make + " model: " + model + " regyear: " + regYear + " config:" + configuration + " mileage(miles):" + mileage + " size(L): " + engineSize + " power(BHP): " + str(enginePower) + " control: " + controlType + " fueltype: " + fuelType + " owners: " + owners)
                newRow = [price,make,model,regYear,configuration,mileage,engineSize,enginePower,controlType,fuelType,owners]
                df.loc[len(df)] = newRow


        # click next page button
        print("page: "+  str(i+1))
        driver.get(driver.find_element(by= By.XPATH,value="//*[@id='content']/div/header/nav/ul/li[2]/a").get_attribute("href"))

        #driver.get(driver.find_element_by_xpath("//*[@id='content']/div/header/nav/ul/li[2]/a").text)
        #driver.get(driver.find_element_by_xpath("//*[@id='content']/div/div/header/nav/ul/li[2]/a").text)
    df = df.set_index("Price")
    print(df)
    df.to_csv("C:/Users/cdusi/projdata/scrapedCars.csv",mode = 'a',header = False)


scrape(100)

