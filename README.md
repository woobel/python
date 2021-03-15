# python
       from selenium import webdriver #Selenium
from bs4 import BeautifulSoup   #BeautifulSoup
import time
import re
import json
import os

#뷰티풀수프 -> https://medium.com/@nsh235482/python-selenium%EC%9C%BC%EB%A1%9C-%EC%9B%B9%EC%82%AC%EC%9D%B4%ED%8A%B8-%ED%81%AC%EB%A1%A4%EB%A7%81%ED%95%98%EA%B8%B0-3-%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%8C%8C%EC%8B%B1-383f2f72e8f4
#셀레니움 -> https://beomi.github.io/2017/02/27/HowToMakeWebCrawler-With-Selenium/


driver = webdriver.Chrome("C:\\Users\\Youngdae\\Desktop\\새 폴더 (6)\\chromedriver.exe")
url = 'https://finance.daum.net/domestic/market_cap?view=pc' #갖고 올 URL (다음 금융)
driver.implicitly_wait(3) #자원 로드 딜레이
driver.get(url) #url 오픈

stock = {} #딕셔너리 저장
success = 0 #성공여부 개수

file = open("Stock.json","w")#파일 백지화
file.write("")

#while reset == 1 :
#    try :
for page in range(1, 11) : #페이지 만큼 반복
    html = driver.page_source #바뀔때 마다 반복 URL 모든 소스 갖고오기
    soup = BeautifulSoup(html, 'html.parser')

    for x in range(0, 30) :
        stock['stock'] = driver.find_element_by_xpath(f'/html/body/div/div[4]/div[2]/div[1]/div[2]/div[2]/div/table/tbody/tr[{x+1}]/td[2]/a').text #(종목)딕셔 너리 제작
        stock['price'] = driver.find_element_by_xpath(f'/html/body/div[1]/div[4]/div[2]/div[1]/div[2]/div[2]/div/table/tbody/tr[{x+1}]/td[3]/span').text #항목별 XML 경로 데이터 쌓기
        stock['previous'] = driver.find_element_by_xpath(f'/html/body/div[1]/div[4]/div[2]/div[1]/div[2]/div[2]/div/table/tbody/tr[{x+1}]/td[4]/span').text
        stock['fluctuation'] = driver.find_element_by_xpath(f'/html/body/div[1]/div[4]/div[2]/div[1]/div[2]/div[2]/div/table/tbody/tr[{x+1}]/td[5]/span').text
        stock['total'] = driver.find_element_by_xpath(f'/html/body/div[1]/div[4]/div[2]/div[1]/div[2]/div[2]/div/table/tbody/tr[{x+1}]/td[6]/span').text
        stock['number'] = driver.find_element_by_xpath(f'/html/body/div[1]/div[4]/div[2]/div[1]/div[2]/div[2]/div/table/tbody/tr[{x+1}]/td[7]/span').text
        stock['foreigner'] = driver.find_element_by_xpath(f'/html/body/div[1]/div[4]/div[2]/div[1]/div[2]/div[2]/div/table/tbody/tr[{x+1}]/td[8]/span').text


        # JSON 인코딩
        jsonString = json.dumps(stock, ensure_ascii=False) #한글깨짐 해결
        # JSON 파일로 저장
        print(jsonString + " 추가 완료")#추가완료 여부
        success += 1

        file = open("Stock.json","a") #원래 파일에 Append
        file.write(jsonString)
        if x != 29 : file.write(',\n') #마지막은 빼고 딕셔너리 구분 <- 수정필요

    driver.find_element_by_xpath(f'/html/body/div/div[4]/div[2]/div[1]/div[2]/div[2]/div/div/a[{page}]').click() #다음 페이지 이동 /html/body/div/div[4]/div[2]/div[3]/div[1]/div[2]/div[2]/div[1]/div[4]/div/div[2]/div/div/a[1]
    time.sleep(0.3) #페이지 바꾸고 텀 두기

file.close()

print(success ,end="개 성공")
#    except :
#        print("오류 발생 프로그램 다시시작")
