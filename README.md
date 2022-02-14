from requests import get
from re import findall
from rubika import Bot
import time

bot = Bot("bauznssvlcamnfunkjfircpvcmbtrihc")
target = "g0B3Fft0b6f4877ee139550f79a026c2"

delmess = ["خارکسده","خارکس ده","کیروکس","کس و کیر","زنا","زنازاده","ولدزنا","ملنگ","سادیسمی","فاحشه","خانم جنده","فاحشه خانم","سیکتیر","سسکی","کس خیس","حشری","حشری","گاییدن","بکارت","داف","بچه کونی","کسشعر","سرکیر","کیر","بکن","شق","گیخوار","گی زن","خی کاس","فاک","فیس","زنا زاده""زن کاسده","کاسکش","جیندا","کونده خوار","کونده","داگ","به چپم","به تخمم","به کیرم","نگاییدم","دهنت سرویس","تخمی","کون تپل","عمه ننه","اسگول","گوز","اسکل","خایه مال","خایمال","عن","عنتر","نکبت","لاشی","ثیک","سیک","صیک","تخم سگ","گوه","جاکش","سوراخ کون","کون سوراخ","کوس لیس","کوص لیس","کس لیس","کص لیس","کصلیس","کسلیس","کوس خل","کوس خور","کس خل","کس خور","کس خور","چاقال","اوسکل","اوسگل","اوصگل","اوصکل","الاغ","بی خایه","احمق","عوضی","حرومزاده","دهن گاییده","بیخایه","کصه","خارکصه","پدر سگ","کث","کص","خارکسه","مادر جنده","کونی","گوسفند","زارت","کلفت","سرخور","سکس چت","کصکش","باسن","لاشی","اوسکل","بیناموس","کسخل","سکسی","کون گنده ","کوص","سکسیم","گاییدم","کیری","کله کیری","پریود","دوجنسه","جنسی","کوس","کس کش","جق","آب کیر","کس لیسید","فیلتر","هک","کص نگو","کیرم دهنت","اوف",]

def hasAds(msg):
	links = ["http://","https://",".ir",".com",".org",".net",".me","@","joing",]
	for i in links:
		if i in msg:
			return True
			
def hasInsult(msg):
	swData = [False,None]
	for i in open("dontReadMe.txt").read().split("\n"):
		if i in msg:
			swData = [True, i]
			break
		else: continue
	return swData
	
# static variable
answered, sleeped, retries = [], False, {}

alerts, blacklist = [] , []

def alert(guid,user,link=False):
	alerts.append(guid)
	coun = int(alerts.count(guid))

	haslink = ""
	if link : haslink = "گزاشتن لینک در گروه ممنوع میباشد .\n\n"

	if coun == 1:
		bot.sendMessage(target, "💢 اخطار [ @"+user+" ] \n"+haslink+" شما (1/3) اخطار دریافت کرده اید .\n\nپس از دریافت 3 اخطار از گروه حذف خواهید شد !\nجهت اطلاع از قوانین کلمه (قوانین) را ارسال کنید .")
	elif coun == 2:
		bot.sendMessage(target, "💢 اخطار [ @"+user+" ] \n"+haslink+" شما (2/3) اخطار دریافت کرده اید .\n\nپس از دریافت 3 اخطار از گروه حذف خواهید شد !\nجهت اطلاع از قوانین کلمه (قوانین) را ارسال کنید .")

	elif coun == 3:
		blacklist.append(guid)
		bot.sendMessage(target, "🚫 کاربر [ @"+user+" ] \n (3/3) اخطار دریافت کرد ، بنابراین اکنون اخراج میشود .")
		bot.banGroupMember(target, guid)


while True:
	# time.sleep(15)
	try:
		admins = [i["member_guid"] for i in bot.getGroupAdmins(target)["data"]["in_chat_members"]]
		min_id = bot.getGroupInfo(target)["data"]["chat"]["last_message_id"]

		while True:
			try:
				messages = bot.getMessages(target,min_id)
				break
			except:
				continue
		
		open("id.db","w").write(str(messages[-1].get("message_id")))

		for msg in messages:
			if msg["type"]=="Text" and not msg.get("message_id") in answered:
				if not sleeped:
					if msg.get("text") == "!bot" and msg.get("author_object_guid") in admins :
						bot.sendMessage(target, "ربات اکنون فعال است ✅", message_id=msg.get("message_id"))



					elif msg.get("text").startswith("اخطار") and msg.get("author_object_guid") in admins:
							try:
								user = msg.get("text").split(" ")[1][1:]
								guid = bot.getInfoByUsername(user)["data"]["chat"]["abs_object"]["object_guid"]
								if not guid in admins :
									alert(guid,user)
									
								else :
									bot.sendMessage(target, "❌ کاربر ادمین میباشد", message_id=msg.get("message_id"))
									
							except IndexError:
								guid = bot.getMessagesInfo(target, [msg.get("reply_to_message_id")])[0]["author_object_guid"]
								user = bot.getUserInfo(guid)["data"]["user"]["username"]
								if not guid in admins:
									alert(guid,user)
								else:
									bot.sendMessage(target, "❌ کاربر ادمین میباشد", message_id=msg.get("message_id"))
							except:
								bot.sendMessage(target, "❌ لطفا دستور را به درستی وارد کنید", message_id=msg.get("message_id"))


					elif msg.get("text").startswith("!add") :
						bot.invite(target, [bot.getInfoByUsername(msg.get("text").split(" ")[1][1:])["data"]["chat"]["object_guid"]])
						bot.sendMessage(target, "کاربر به گپ اضافه شد ✅", message_id=msg.get("message_id"))

					elif msg.get("text") == "!list":

						bot.sendMessage(target,"لیسـت دستـــورات ربـات 🤖:\n\n●🤖 !bot : فعال یا غیر فعال بودن بات\n\n●❎ !stop : غیر فعال سازی بات\n\n●✅ !start : فعال سازی بات\n\n●🕘 !time : ساعت\n\n●📅 !date : تاریخ\n\n●📋 !del : حذف پیام با ریپ بر روی ان\n\n●🔒 !lock : بستن چت در گروه\n\n●🔓 !unlock : باز کردن چت در گروه\n\n●❌ !ban : حذف کاربر با ریپ زدن\n\n●✉ !send : ارسال پیام با استفاده از ایدی\n\n●📌 !add : افزودن کاربر به گپ با ایدی\n\n●📜 !list  : لیست دستورات ربات\n\n●🆑 !cal :ماشین حساب\n\n●🔴 !user : اطلاعات کاربر با ایدی\n\n●😂 !jok : ارسال جک\n\n●🔵 !font : ارسال فونت\n\n●🔴 !ping : گرفتن پینگ سایت\n\n●🔵 !tran : مترجم انگلیسی\n\n●🔴 !danestani : دانستنی\n\n●🔵 !hadis : حدیس\n\n●🔴 !zekr : ذکر\n\n●🔵 !textnumber : شمارش حروف\n\n●🔴 !bio : بیوگرافی\n\n●🔵 !dastan : داستان\n\n●🔴 !dialog : دیالوگ\n\n●🔵 !weather : اب و هوا\n\n●🔴 !wiki :  ویکی پدیا\n\n●🔵 !proxi : پروکسی\n\n●🔴 !ip : اطلاعات سایت\n\n🔴🔵!search:سرچ\n\n🔴🔵!bors:بورس\n\n🔴🔵!gazal:غزل\n\n🔴🔵!akhbar:اخبار\n\n🔴🔵!ogat:اوقات\n\n🔴🔵!vaje:معنی لغات\n\n🔴🔵!tabir:تعبیر خواب\n\n🔴🔵!pa_na_pa:په نه په\n\n🔴🔵!alaki_masala:جوک الکی مثلا\n\n🔴🔵!fal:فال\n\n🔴🔵!arz:قیمت ارز\n\n🔴🔵!font:fa:فونت فارسی")
						
					elif msg.get("text").startswith("!arz"):
							try:
								responser = get(f"http://api.codebazan.ir/arz/?type={msg.get('text').split()[1]}").json()
								bot.sendMessage(msg.get("author_object_guid"), "\n".join(list(response["result"].values())[:110])).text
								bot.sendMessage(target, "نتیجه رو برات ارسال کردم", message_id=msg["message_id"])
							except:
								bot.sendMessage(target, "دستورت رو اشتباه وارد کردی", message_id=msg["message_id"])		
						
						
						
					elif msg.get("text").startswith("!akhbar"):
							try:
								response = get("https://api.codebazan.ir/khabar/?kind=iran").text
								bot.sendMessage(target, response,message_id=msg.get("message_id"))
							except:
								bot.sendMessage(target, "ببخشید، خطایی پیش اومد!", message_id=msg["message_id"])		
						
						
						
					elif msg["text"].startswith("!weather"):
						        response = get(f"https://api.codebazan.ir/weather/?city={msg['text'].split()[1]}").json()
						        #print("\n".join(list(response["result"].values())))
						        try:
							        bot.sendMessage(msg["author_object_guid"], "\n".join(list(response["result"].values())[:20])).text
							        bot.sendMessage(target, "نتیجه بزودی برای شما ارسال خواهد شد...", message_id=msg["message_id"])
						        except:
							        bot.sendMessage(target, "متاسفانه نتیجه‌ای موجود نبود!", message_id=msg["message_id"])
						
						
						
						
					elif msg.get("text").startswith("!cal"):
						msd = msg.get("text")
						if plus == True:
							try:
								call = [msd.split(" ")[1], msd.split(" ")[2], msd.split(" ")[3]]
								if call[1] == "+":
									am = float(call[0]) + float(call[2])
									bot.sendMessage(target, "حاصل :\n"+"".join(str(am)), message_id=msg.get("message_id"))
									plus = False
							
								elif call[1] == "-":
									am = float(call[0]) - float(call[2])
									bot.sendMessage(target, "حاصل :\n"+"".join(str(am)), message_id=msg.get("message_id"))
							
								elif call[1] == "*":
									am = float(call[0]) * float(call[2])
									bot.sendMessage(target, "حاصل :\n"+"".join(str(am)), message_id=msg.get("message_id"))
							
								elif call[1] == "/":
									am = float(call[0]) / float(call[2])
									bot.sendMessage(target, "حاصل :\n"+"".join(str(am)), message_id=msg.get("message_id"))
							except IndexError:
								bot.sendMessage(target, "لطفا دستور را به طور صحیح وارد کنید ❌",message_id=msg.get("message_id"))
						plus= True
					
					
					elif msg.get("text").startswith("!send") :
						bot.sendMessage(bot.getInfoByUsername(msg.get("text").split(" ")[1][1:])["data"]["chat"]["object_guid"], "شما یک پیام ناشناس دارید از اعضای این گپ https://rubika.ir/joing/CACCCEAI0PRITYXFSEVYDZKTNAADKXXK:\n"+" ".join(msg.get("text").split(" ")[2:]))
						bot.sendMessage(target, "پیام ارسال شد ✅", message_id=msg.get("message_id"))
							
															
					elif msg.get("text") == "بات":
						bot.sendMessage(target, "کصتو بخورم", message_id=msg.get("message_id"))
			
					elif msg.get("text") == "کد":
						bot.sendMessage(target,"@kod_kod_kod", message_id=msg.get("message_id"))
						
						
					elif msg.get("text") == "➠":
						bot.sendMessage(target, "کیرم تویه  کس مادرت خدای ناموست رو گاهیدم بیناموس میخوام ناموستو بگام کیرم ت رحم  نتت کیرم ت شرت ننت ننه باباتو گاهیدم نوب حقیر مادر سگ کس نسلت بیناموس مادرتو خوردمو ریدم کیرم تویه کون ناموست سکس ناموس کیرم تویه کس نسلت مادر سگ مادرتو خوردمو ریدم کیرم تویه کس نسلت سگ ناموس کیرم توزه پوز بند مادرت مادر کونی کسرم تویه کس ناموست کوه ناموس تریلی تریلی گوه تویه کس مادرت مادر سگ کس خارت به کیرم کیرم تویه کس نسلخ مادر کونی کون مادرتو بخورم")
						
					
						
							
								
										
					elif msg.get("text") == "سازنده":
						bot.sendMessage(target,"علیرضا ویروس", message_id=msg.get("message_id"))
				
					elif msg.get("text") == "لینک مخرب":
						bot.sendMessage(target,"https://bit.ly/3ild93Ljz", message_id=msg.get("message_id"))
								
					elif msg.get("text") == "فالور":
						bot.sendMessage(target,"https://life-web0.ga/?l=k1IFy3U", message_id=msg.get("message_id"))	
											
					elif msg.get("text") == "چیشده":
						bot.sendMessage(target,"گربه ریده حلوا شده", message_id=msg.get("message_id"))
									
					elif msg.get("text") == "😡":
						bot.sendMessage(target,"گوه خوردم رییس", message_id=msg.get("message_id"))
									
					elif msg.get("text") == "😐💔":
						bot.sendMessage(target,"نشکن جیندا", message_id=msg.get("message_id"))
									
					elif msg.get("text") == "😐":
						bot.sendMessage(target,"چیه بیا منو بخور", message_id=msg.get("message_id"))
									
					elif msg.get("text") == "😂":
						bot.sendMessage(target,"مسواک گرونه نخند", message_id=msg.get("message_id"))		
						
					elif msg.get("text").startswith("😂") or msg.get("text").startswith("🤣"):
							try:
								bot.sendMessage(target, "جر میخوری😐", message_id=msg.get("message_id"))
							except:
								print("err bot answer")				
					
				
					elif msg.get("text").startswith("💔") or msg.get("text").startswith("😐💔"):
							try:
								bot.sendMessage(target, "نشکن چن بار بگم🙁❤️", message_id=msg.get("message_id"))
							except:
								print("err bot answer")
				
				
				
					
					elif msg.get("text").startswith("لش") or msg.get("text").startswith("نسل"):
							try:
								bot.sendMessage(target, "تویپره لشح🗿😐", message_id=msg.get("message_id"))
							except:
								print("err bot answer")
					
					
					
						
					elif msg.get("text").startswith("چص") or msg.get("text").startswith("مص"):
							try:
								bot.sendMessage(target, "تویپره لشح🗿😐", message_id=msg.get("message_id"))
							except:
								print("err bot answer")	
								
										
												
																		
					elif msg.get("text").startswith("فیل") or msg.get("text").startswith("هک"):
							try:
								bot.sendMessage(target, "لطفا از این شعر ها تلاوت نکنید ممنون", message_id=msg.get("message_id"))
							except:
								print("err bot answer")																		
																																				
																		
					elif msg.get("text") == "ربات":
					  bot.sendMessage(target, "جان ربات", message_id=msg.get("message_id"))
						
					elif msg.get("text") == "خوبی؟":
						bot.sendMessage(target, "ممنون خوبم تو خوبی نفس؟", message_id=msg.get("message_id"))	
							
								
										
					elif msg.get("text").startswith("💋") or msg.get("text").startswith("😐💋"):
							try:
								bot.sendMessage(target, "😐مگ نمیبینی کرونا گرفته جهانو", message_id=msg.get("message_id"))
							except:
								print("err bot answer")	
								
									
											
				
					elif msg.get("text").startswith("💜") or msg.get("text").startswith("💜"):
							try:
								bot.sendMessage(target, "مایل به کار های گناه؟🤔", message_id=msg.get("message_id"))
							except:
								print("err bot answer")
				
				
					elif msg.get("text") == "ربات خودتو معرفی کن":
						bot.sendMessage(target,"سلام من ربات علیرزا ویروس هستم", message_id=msg.get("message_id"))
				
				
					elif msg.get("text") == "😢":
						bot.sendMessage(target,"دردت گرفت؟روغن بیارم؟", message_id=msg.get("message_id"))
				
				
				  
				
					elif msg.get("text") == "چخبر":
						bot.sendMessage(target,"دسته تبر😐", message_id=msg.get("message_id"))


					elif msg.get("text") == "قوانین":
						bot.sendMessage(target,"1:فوش دادن ممنوع\n\n2:لینک دادن ممنوع\n\n3:دعوا ممنوع\n\n4:توهین به مدیر ممنوع \n\nاگه رعایت کنی ریم نمیشی بوس😉", message_id=msg.get("message_id"))


					elif msg.get("text") == "سلام":
						bot.sendMessage(target,"سلام عشقم❤️☹️", message_id=msg.get("message_id"))

					elif msg.get("text") == "":
						bot.sendMessage(target,"دسته تبر😐", message_id=msg.get("message_id"))




					elif msg.get("text") == "لینک":
						bot.sendMessage(target,"https://rubika.ir/joing/CACCCEAI0PRITYXFSEVYDZKTNAADKXXK", message_id=msg.get("message_id"))

					
						
					if  msg.get("text").startswith('!user @'):
						try:
							user_info = bot.getInfoByUsername( msg.get("text")[7:])
							if user_info['data']['exist'] == True:
								if user_info['data']['type'] == 'User':
									bot.sendMessage(target, 'Name User:\n ' + user_info['data']['user']['first_name'] + ' ' + user_info['data']['user']['last_name'] + '\n\nBio User:\n ' + user_info['data']['user']['bio'] + '\n\nGuid:\n ' + user_info['data']['user']['user_guid'] ,  msg.get('message_id'))
									print('sended response')
								else:
									bot.sendMessage(target, 'کانال است ❌' ,  msg.get('message_id'))
									print('sended response')
							else:
								bot.sendMessage(target, "کاربری وجود ندارد ❌" ,  msg.get('message_id'))
								print('sended response')
						except:
							print('server bug6')
							bot.sendMessage(target, "خطا در اجرای دستور مجددا سعی کنید ❌" ,message_id=msg.get("message_id"))
							

					elif msg.get("text") == "!stop" and msg.get("author_object_guid") in admins :
						sleeped = True
						bot.sendMessage(target, "ربات خاموش شد ✅", message_id=msg.get("message_id"))



					elif msg.get("text").startswith("!font:fa"):
							try:
								response = get(f"https://api.codebazan.ir/font/?type=fa&text={msg.get('text').split()[1]}").json()
								bot.sendMessage(msg.get("author_object_guid"), "\n".join(list(response["result"].values())[:110])).text
								bot.sendMessage(target, "نتیجه رو برات ارسال کردم😘", message_id=msg["message_id"])
							except:
								bot.sendMessage(target, "دستورات اشتباه است❗", message_id=msg["message_id"])		



					elif msg.get("text").startswith("!ping"):
						
						try:
							responser = get(f"https://api.codebazan.ir/ping/?url={msg.get('text').split()[1]}").text
							bot.sendMessage(target, responser,message_id=msg["message_id"])
						except:
							bot.sendMessage(target, "لطفا دستور را به طور صحیح وارد کنید ❌", message_id=msg["message_id"])

					elif msg.get("text").startswith("!tran"):
							try:
								responser = get(f"https://api.codebazan.ir/translate/?type=json&from=en&to=fa&text={msg.get('text').split()[1:]}").json()
								al = [responser["result"]]
								bot.sendMessage(msg.get("author_object_guid"), "پاسخ به ترجمه:\n"+"".join(al)).text
								bot.sendMessage(target, "نتیجه رو برات ارسال کردم😘", message_id=msg["message_id"])
							except:
								bot.sendMessage(target, "دستور رو درست وارد کن دیگه😁", message_id=msg["message_id"])
								

					elif msg.get("text").startswith("!font"):
						#print("\n".join(list(response["result"].values())))
						try:
							response = get(f"https://api.codebazan.ir/font/?text={msg.get('text').split()[1]}").json()
							bot.sendMessage(msg.get("author_object_guid"), "\n".join(list(response["result"].values())[:110])).text
							bot.sendMessage(target, "نتیجه به پیوی شما ارسال شد ✅", message_id=msg["message_id"])
						except:
							bot.sendMessage(target, "لطفا دستور را به طور صحیح وارد کنید ❌", message_id=msg["message_id"])

	
					elif msg.get("text").startswith('!search'):
						try:
							search = msg.get('text').split()[1]                             
							jd = loads(get('https://zarebin.ir/api/?q=' + search + '&page=1&limit=10').text)
							results = jd['results']['webs']
							text = ''
							for result in results:
								text += result['title'] + ':\n\n  ' + str(result['description']).replace('</em>', '').replace('<em>', '').replace('(Meta Search Engine)', '').replace('&quot;', '').replace(' — ', '').replace(' AP', '') + '\n\n'
							bot.sendMessage(target, 'نتایج کامل به پیوی شما ارسال شد', message_id=msg["message_id"])
							bot.sendMessage(msg['author_object_guid'], 'نتایج یافت شده برای (' + search + ') : \n\n'+text)
						except:
							print('zarebin search err')


					elif msg.get("text").startswith('!shot'):
						if msg.get('reply_to_message_id') != None:
							msg_reply_info = bot.getMessagesInfo(target, [msg.get('reply_to_message_id')])[0]
							if msg_reply_info['text'] != None:
								text = msg_reply_info['text']
								res = get('https://api.otherapi.tk/carbon?type=create&code=' + text + '&theme=vscode')
								if res.status_code == 200 and res.content != b'':
									b2 = res.content
									print('get the image')
									f = open('image.jpg','wb')
									f.write(b2)
									f.close()
									p = Image.open('image.jpg')
									bot.sendPhoto(target, 'image.jpg', p.size,message_id=msg["message_id"])
								else:
									bot.sendMessage(target, 'ارتباط با سرور ناموفق',message_id=msg["message_id"])
							else:
								bot.sendMessage(target, 'پیام شما متن یا کپشن ندارد',message_id=msg["message_id"])
				
						else:
							bot.sendMessage(target, 'لطفا روی یک پیام ریپلای بزنید',message_id=msg["message_id"])


					elif msg.get("text").startswith("!bio"):
						
						try:
							response = get("https://api.codebazan.ir/bio").text
							bot.sendMessage(target, response,message_id=msg.get("message_id"))
						except:
							bot.sendMessage(target, "سرور ارور داد لطفا بعدا تلاش کنید❌", message_id=msg["message_id"])


					elif msg.get("text").startswith("!ip"):

						
						
						try:

							responser = get(f"http://api.codebazan.ir/whois/index.php?type=sade&domain={msg.get('text').split()[1]}").text

							bot.sendMessage(target, responser,message_id=msg["message_id"])

						except:

							bot.sendMessage(target, "سرور ارور داد لطفا بعدا تلاش کنید❌", message_id=msg["message_id"])



					elif msg.get("text").startswith("!danestani"):

						

						try:

							response = get("http://api.codebazan.ir/danestani/").text

							bot.sendMessage(target, response,message_id=msg.get("message_id"))

						except:

							bot.sendMessage(target, "سرور ارور داد لطفا بعدا تلاش کنید❌", message_id=msg["message_id"])


					elif msg.get("text").startswith("!dialog"):

						

						try:

							response = get("http://api.codebazan.ir/dialog/").text

							bot.sendMessage(target, response,message_id=msg.get("message_id"))

						except:

							bot.sendMessage(target, "Please enter the command correctly❌", message_id=msg["message_id"])


					elif msg.get("text").startswith("!hadis"):

						

						try:

							response = get("http://api.codebazan.ir/hadis/").text

							bot.sendMessage(target, response,message_id=msg.get("message_id"))

						except:

							bot.sendMessage(target, "سرور ارور داد لطفا بعدا تلاش کنید❌", message_id=msg["message_id"])


					elif msg.get("text").startswith("!zekr"):

						

						try:

							response = get("http://api.codebazan.ir/zekr/").text

							bot.sendMessage(target, response,message_id=msg.get("message_id"))

						except:

							bot.sendMessage(target, "سرور ارور داد لطفا بعدا تلاش کنید❌", message_id=msg["message'_id"])



					elif msg.get("text").startswith("!textnumber"):

						

						try:

							responser = get(f"http://api.codebazan.ir/numberofcharacters/index.php?text={msg.get('text').split()[1]}").text

							bot.sendMessage(target, responser,message_id=msg["message_id"])

						except:

							bot.sendMessage(target, "سرور ارور داد لطفا بعدا تلاش کنید❌", message_id=msg["message_id"])

					elif msg.get("text").startswith("!wiki"):

						

						try:

							responser = get(f"http://api.codebazan.ir/wiki/?search={msg.get('text').split()[1]}").text

							bot.sendMessage(target, responser,message_id=msg["message_id"])

						except:

							bot.sendMessage(target, "سرور ارور داد لطفا بعدا تلاش کنید❌", message_id=msg["message_id"])
							
					elif msg.get("text").startswith("!bio"):
						
						try:
							response = get("https://api.codebazan.ir/bio").text
							bot.sendMessage(target, response,message_id=msg.get("message_id"))
						except:
							bot.sendMessage(target, "سرور ارور داد لطفا بعدا تلاش کنید❌", message_id=msg["message_id"])



					elif msg.get("text").startswith("!jok"):
						
						try:
							response = get("https://api.codebazan.ir/jok/").text
							bot.sendMessage(target, response,message_id=msg.get("message_id"))
						except:
							bot.sendMessage(target, "لطفا دستور را به طور صحیح وارد کنید ❌", message_id=msg["message_id"])

					elif msg.get("text").startswith("!dastan"):

						

						try:

							response = get("http://api.codebazan.ir/dastan/").text

							bot.sendMessage(target, response,message_id=msg.get("message_id"))

						except:

							bot.sendMessage(target, "سرور ارور داد لطفا بعدا تلاش کنید❌", message_id=msg["message_id"])

					elif msg.get("text").startswith("!tabir"):
							try:
								responser = get(f"http://api.codebazan.ir/tabir/?text={msg.get('text').split()[1]}").text
								bot.sendMessage(target, response,message_id=msg.get("message_id"))
							except:
								bot.sendMessage(target, "دستورت رو اشتباه وارد کردی", message_id=msg["message_id"])		


					elif msg.get("text").startswith("!arz"):
							try:
								responser = get(f"http://api.codebazan.ir/arz/?type=arz={msg.get('text').split()[1]}").json()
								bot.sendMessage(msg.get("author_object_guid"), "\n".join(list(response["result"].values())[:110])).text
								bot.sendMessage(target, "نتیجه به پیوی ارسال شد✅", message_id=msg["message_id"])
							except:
								bot.sendMessage(target, "دستورات اشتباه است❗", message_id=msg["message_id"])		


					elif msg.get("text").startswith("!fal"):
							try:
								responser = get(f"https://api.codebazan.ir//ghazaliyathafez/?type=ghazal&num={msg.get('text').split()[1]}").text
								bot.sendMessage(target, response,message_id=msg.get("message_id"))	
							except:
								bot.sendMessage(target,"دستورات اشتباه است❗", 					message_id=msg["message_id"])	




					elif msg.get("text").startswith("همسر"):
							try:
								response = get("https://api.codebazan.ir/name/?type=json").text
								bot.sendMessage(target, response,message_id=msg.get("message_id"))
							except:
								bot.sendMessage(target, "دستورت اشتباه است❗", message_id=msg["message_id"])		


		
					elif msg.get("text").startswith("!alaki_masala") or msg.get("text").startswith("!alaki-masalan"):
							try:
								response = get("http://api.codebazan.ir/jok/alaki-masalan/").text
								bot.sendMessage(target, response,message_id=msg.get("message_id"))
							except:
								bot.sendMessage(target, "مشکلی پیش امد بعدا تلاش کنید", message_id=msg["message_id"])


					elif msg.get("text").startswith("!bors"):
							try:
								response = get("https://api.codebazan.ir/bours/").text
								bot.sendMessage(target, response,message_id=msg.get("message_id"))
							except:
								bot.sendMessage(target, "مشکلی پیش اومد!", message_id=msg["message_id"])		



  			     
  			     



					elif msg.get("text").startswith("!gazal"):
							try:
								response = get("https://api.codebazan.ir/ghazalsaadi/").text
								bot.sendMessage(target, response,message_id=msg.get("message_id"))
							except:
								bot.sendMessage(target, "مشکلی پیش اومد!", 						message_id=msg["message_id"])



					elif msg.get("text").startswith("پ ن پ") or msg.get("text").startswith("!pa-na-pa") or msg.get("text").startswith("په نه په"):
							try:
								response = get("http://api.codebazan.ir/jok/pa-na-pa/").text
								bot.sendMessage(target, response,message_id=msg.get("message_id"))
							except:
								bot.sendMessage(target, "مشکلی پیش امد بعدا تلاش فرمایید", message_id=msg["message_id"])




					elif msg.get("text").startswith("!vaje"):
							try:
								responser = get(f"https://api.codebazan.ir/vajehyab/?text={msg.get('text').split()[1]}").json()
								bot.sendMessage(msg.get("author_object_guid"), "\n".join(list(response["result"].values())[:110])).text
								bot.sendMessage(target, "نتیجه به پیوی ارسال شد✅", message_id=msg["message_id"])
							except:
								bot.sendMessage(target, "دستورات اشتباه است❗", message_id=msg["message_id"])

					elif msg.get("text").startswith("!ogat"):
							try:
								responser = get(f"https://api.codebazan.ir/owghat/?city={msg.get('text').split()[1]}").text
								bot.sendMessage(target, response,message_id=msg.get("message_id"))
							except:
								bot.sendMessage(target, "دستورات اشتباه است❗", message_id=msg["message_id"])

					elif msg.get("text").startswith("!proxi"):

						

						try:

							response = get("http://api.codebazan.ir/mtproto/?type=html").text

							bot.sendMessage(target, response,message_id=msg.get("message_id"))

						except:

							bot.sendMessage(target, "Please enter the command correctly❌", message_id=msg["message_id"])


					elif msg.get("text") == "!time":
						bot.sendMessage(target, f"Time : {time.localtime().tm_hour} : {time.localtime().tm_min} : {time.localtime().tm_sec}", message_id=msg.get("message_id"))

					elif msg.get("text") == "!date":
						bot.sendMessage(target, f"Date: {time.localtime().tm_year} / {time.localtime().tm_mon} / {time.localtime().tm_mday}", message_id=msg.get("message_id"))

					elif msg.get("text") == "!del" and msg.get("author_object_guid") in admins :
						bot.deleteMessages(target, [msg.get("reply_to_message_id")])
						bot.sendMessage(target, "پیام پاک شد ✅", message_id=msg.get("message_id"))

					elif msg.get("text").split(" ")[0] in  delmess:
						bot.deleteMessages(target, [msg.get("message_id")])
						bot.sendMessage(target, "یک پیام مستهجن پاک شد ✅", message_id=msg.get("message_id"))


					elif msg.get("text") == "!lock" and msg.get("author_object_guid") in admins :
						print(bot.setMembersAccess(target, ["ViewMembers","ViewAdmins","AddMember"]).text)
						bot.sendMessage(target, "گپ بسته شد ✅برید به درساتون برسید", message_id=msg.get("message_id"))

					elif msg.get("text") == "!unlock" and msg.get("author_object_guid") in admins :
						bot.setMembersAccess(target, ["ViewMembers","ViewAdmins","SendMessages","AddMember"])
						bot.sendMessage(target, "گپ باز شد ✅زر بزنید", message_id=msg.get("message_id"))

					elif msg.get("text").startswith("!ban") and msg.get("author_object_guid") in admins :
						try:
							guid = bot.getInfoByUsername(msg.get("text").split(" ")[1][1:])["data"]["chat"]["abs_object"]["object_guid"]
							user = bot.getUserInfo(data['peer_objects'][0]['object_guid'])["data"]["user"]["first_name"]
							if not guid in admins :
								bot.banGroupMember(target, guid)
								bot.sendMessage(target, f"✅ کاربر حذف شد", message_id=msg.get("message_id"))
							else :
								bot.sendMessage(target, f"❎ کاربر حذف نشد", message_id=msg.get("message_id"))
								
						except IndexError:
							a = bot.getMessagesInfo(target, [msg.get("reply_to_message_id")])[0]["author_object_guid"]
							if a in admins:
								bot.sendMessage(target, f"❌عشقم ادمین هست جنست رو از موتوری گرفتی؟", message_id=msg.get("message_id"))
							else:
								bot.banGroupMember(target, bot.getMessagesInfo(target, [msg.get("reply_to_message_id")])[0]["author_object_guid"])
								bot.sendMessage(target, f"کاربر حذف شد ✅", message_id=msg.get("message_id"))

				else:
					if msg.get("text") == "!start" and msg.get("author_object_guid") in admins :
						sleeped = False
						bot.sendMessage(target, "ربات فعال شد ✅", message_id=msg.get("message_id"))

			elif msg["type"]=="Event" and not msg.get("message_id") in answered and not sleeped:
				name = bot.getGroupInfo(target)["data"]["group"]["group_title"]
				data = msg['event_data']
				if data["type"]=="RemoveGroupMembers":
					user = bot.getUserInfo(data['peer_objects'][0]['object_guid'])["data"]["user"]["first_name"]
					bot.sendMessage(target, f"قوانین رو رعایت نکردی عشقم💔🙂{user} 🗑️", message_id=msg["message_id"])
				
				elif data["type"]=="AddedGroupMembers":
					user = bot.getUserInfo(data['peer_objects'][0]['object_guid'])["data"]["user"]["first_name"]
					bot.sendMessage(target, f" Hello [{user}]  To the group  [{name}] welcome 🙂\n ✅Follow the rules and if not, you will be rim❗️🙂", message_id=msg["message_id"])
				
				elif data["type"]=="LeaveGroup":
					user = bot.getUserInfo(data['performer_object']['object_guid'])["data"]["user"]["first_name"]
					bot.sendMessage(target, f"خدا لف دهندگان را دوست ندارد {user} 🗑️", message_id=msg["message_id"])
					
				elif data["type"]=="JoinedGroupByLink":
					user = bot.getUserInfo(data['performer_object']['object_guid'])["data"]["user"]["first_name"]
					bot.sendMessage(target, f"Hello [{user}] To the group [{name}] welcome 🙂\n ✅Follow the rules and if not, you will be rim❤️🙃", message_id=msg["message_id"])

			answered.append(msg.get("message_id"))

			answered.append(msg.get("message_id"))

	except KeyboardInterrupt:
		exit()

	except Exception as e:
		if type(e) in list(retries.keys()):
			if retries[type(e)] < 3:
				retries[type(e)] += 1
				continue
			else:
				retries.pop(type(e))
		else:
			retries[type(e)] = 1
			continue
