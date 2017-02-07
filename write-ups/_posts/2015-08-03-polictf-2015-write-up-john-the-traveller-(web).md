---
layout: post
title: POLICTF 2015 Write Up - John the Traveller (Web)
author: junhao
description: POLICTF 2015 Write Up - John the Traveller (Web)
tags: [POLICTF, CTF, WEB, QRCODE]
category: [CTF, WEB, QRCODE]
excerpt: ""
comments: true
share: true
---

<div class="holder">   
				<p>
					Holidays are here! But John still hasn't decided where to spend them and time is running out: flights are overbooked and prices are rising every second. Fortunately, John just discovered a website where he can book last second flight to all the European capitals; however, there's no time to waste, so he just grabs his suitcase and thanks to his new smartphone he looks the city of his choice up while rushing to the airport. There he goes! Flight is booked so... hauskaa lomaa! <br><br>
					<a href="http://traveller.polictf.it/">http://traveller.polictf.it/</a>
				</p>
				<hr/>
	
				<p>
					We visited the site and saw the following page. <br><br>
					</p><center><img src="/write-ups/resources/images/polictf/travellersite.JPG" width="50%" height="50%"></center>
				<p></p>
				<br>
				<p>
				We tried to key into some arbitrary city, however, it returns back the same page. Looking back at the challenge description, we noticed something that stands out - <b>hauskaa lomaa</b>. A search revealed that these two words is actually Finnish. We also found out that the capital of Finland is <b>Helsinki</b>. Entering that into the search field and we got ....
					<br><br>
					</p><center><img src="/write-ups/resources/images/polictf/helsinki.JPG" width="75%" height="75%"></center>
				<p></p>
				
				<br>
				<p>
					Great. So the next step is to try and book the ticket. But how do we do so? The price is in px. Does it mean pixels?  The challenge also mentioned "he just grabs his suitcase and thanks to his new smartphone". Let's try to adjust our browser size to one of the pixel size using Chrome browser dev tools. 
					<br><br>
					</p><center><img src="/write-ups/resources/images/polictf/devtools.JPG" width="25%" height="25%"></center>
				<p></p>

				<br>
				<p>
					Bingo!! Decoding from the QR Code and we got the flag
				</p>
				
				<br>
				<p align="center">
					<b>flag{run_to_the_hills_run_for_your_life}</b>
				</p>
			</div>