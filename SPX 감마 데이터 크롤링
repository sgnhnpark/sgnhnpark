from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.options import Options
from selenium.webdriver.common.keys import Keys
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.support.ui import WebDriverWait
from selenium.webdriver.support import expected_conditions as EC
import base64
import time
import datetime

def get_file_content_chrome(driver, uri):
  result = driver.execute_async_script("""
    var uri = arguments[0];
    var callback = arguments[1];
    var toBase64 = function(buffer){for(var r,n=new Uint8Array(buffer),t=n.length,a=new Uint8Array(4*Math.ceil(t/3)),i=new Uint8Array(64),o=0,c=0;64>c;++c)i[c]="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/".charCodeAt(c);for(c=0;t-t%3>c;c+=3,o+=4)r=n[c]<<16|n[c+1]<<8|n[c+2],a[o]=i[r>>18],a[o+1]=i[r>>12&63],a[o+2]=i[r>>6&63],a[o+3]=i[63&r];return t%3===1?(r=n[t-1],a[o]=i[r>>2],a[o+1]=i[r<<4&63],a[o+2]=61,a[o+3]=61):t%3===2&&(r=(n[t-2]<<8)+n[t-1],a[o]=i[r>>10],a[o+1]=i[r>>4&63],a[o+2]=i[r<<2&63],a[o+3]=61),new TextDecoder("ascii").decode(a)};
    var xhr = new XMLHttpRequest();
    xhr.responseType = 'arraybuffer';
    xhr.onload = function(){ callback(toBase64(xhr.response)) };
    xhr.onerror = function(){ callback(xhr.status) };
    xhr.open('GET', uri);
    xhr.send();
    """, uri)
  if type(result) == int :
    raise Exception("Request failed with status %s" % result)
  return base64.b64decode(result)


today = datetime.datetime.today().strftime('%Y%m%d')

options = Options()
options.add_experimental_option('detach', True)
options.add_experimental_option('excludeSwitches', ['enable-logging'])
options.add_argument("headless")
driver = webdriver.Chrome('chromedriver', options=options)
actions = ActionChains(driver)
# wait = driver.implicitly_wait(10)

url = 'https://www.cboe.com/delayed_quotes/spx/quote_table'
driver.get(url)

time.sleep(10)
# button = WebDriverWait(driver, 10).until(EC.presence_of_element_located(By.XPATH, "/html/body/main/section[1]/div/div/div/div/div[2]/div[2]/div[2]/div[2]/div[3]/div/div[2]/div/div/div/div[1]/div[1]"))

option_range_button = driver.find_element(By.XPATH, "/html/body/main/section[1]/div/div/div/div/div[2]/div[2]/div[2]/div[2]/div[3]/div/div[2]/div/div/div/div[1]/div[1]")
# option_range_button.click()
# actions.click(option_range_button).perform()
# option_range_all = driver.find_element(By.XPATH, "//*[text()='All']")
# driver.execute_script("arguments[0].scrollIntoView();", option_range_all)
# actions.move_to_element(option_range_button).perform()
driver.execute_script("arguments[0].scrollIntoView({block: 'center', inline: 'nearest'})", option_range_button);
actions.click(option_range_button).send_keys(Keys.UP).send_keys(Keys.ENTER).perform()

expiration_button = driver.find_element(By.XPATH, "/html/body/main/section[1]/div/div/div/div/div[2]/div[2]/div[2]/div[2]/div[4]/div/div[2]/div/div/div/div[1]")
# actions.click(expiration_button).perform()
# expiration_all = driver.find_element(By.XPATH, "//*[text()='All']")
# driver.execute_script("arguments[0].scrollIntoView();", expiration_all)
actions.click(expiration_button).send_keys(Keys.UP).send_keys(Keys.ENTER).perform()

view_button = driver.find_element(By.XPATH, "/html/body/main/section[1]/div/div/div/div/div[2]/div[2]/div[2]/div[2]/div[5]/div/button/span")
view_button.click()

# time.sleep(5)

source = driver.page_source
download_blob = "blob:" + source.split("blob:")[1].split("\"")[0]
spx_option_csv = get_file_content_chrome(driver, download_blob)
spx_option_csv_fpath = f"option/spx_{today}.csv"

with open(spx_option_csv_fpath, 'wb') as f:
    f.write(spx_option_csv)
