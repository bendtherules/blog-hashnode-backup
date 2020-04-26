## Flipkart UI Engineer 1 interview - My experience


🎙️ Hi, I am Abhas. I interviewed at Flipkart for UI engineer 1 role in the beginning of 2019.  

It's already been one year from then, but I never publicly wrote about it. Over time, some people have asked me about the UI interview process at Flipkart. I have probably said that I will write back in some time, but never have. (Really sorry for that!) I finally got around to it today.

**This is my effort to document the general process, while abstracting away the actual questions.**

## 📞 The call
A recruiting agency (Interviewbit) contacted me for UI Engineer 1 opening in Flipkart and setup the onsite. There was no screening round, it was directly onsite. About 10-12 candidates were invited for the same role, on a weekend.  
(Flipkart loves this group interview format, where agencies shortlist a few candidates and send them for interview. Going through agencies is your best shot at getting to the door. It's sad, but referral is almost useless here and gets ignored most of the time.) 

## 📅 About the day
The interview was mostly a one-day process - from morning to late evening. I actually liked how the whole process got over in the same day. First, they asked us to gather in a meeting room and explained the machine coding task (1st round). Next rounds were ps/ds (problem solving + data structure), UI + Javascript round and finally, manager round. There is probably one more system design round for UI 2 and above.

There was a lot of waiting in between every round. It wasn't clear when your name is going to come up, or which round is going on currently. Some people legit started watching movies🍿 in between.  
Lunch and snack was arranged by HR. You can go the cafeteria (not free) if you want to.

## 🛠 Machine coding (MC) 

This is a UI you have to build using **plain html/css/js** - no ui library like react or vue is allowed. They will give you a sheet with all the details - like required and good to have features, api endpoints, mockup. 
 
Complete atleast some of the reqd. features. But focus more on clean coding, best practices and logic, rather than on the ui look. You'll have 1-2 hour to complete this task. But don't worry - it's not supposed to be complete-able in that timeframe. I was able to finish only half of the required features and still got through. So, prioritize the imp tasks and explain how you'll do the rest during review.

The solution is reviewed in a few hours by a interviewer. There is some point for each small feature and edge case (objective), and also some point for - clean coding, separation of concerns (multiple components or files) and good architecture (believe me, you'll end up with some sort of render+update approach).

🌟 Think beforehand how you want to build using plain js, keeping it modular and everything. Have a scaffolding with import/export support ready (webpack).

Example task - Build a todo app. You can add, edit, remove items. Allow sub-tasks within a task. There will be some form validation or api call. Also, persist/load todo items on reload. 

## ☯️ Problem solving (ps/ds) 

There was -  
a. 1 problem solving question (leetcode easy problem - search from both sides, basic dynamic programming, etc) and  
b. 1 data structure question (ex. tree traversal in various orders, stack, etc).  

Total time to complete both is about 40-45min. You'll have to first explain your approach and then write the code on paper.

This round is usually not so hard for UI roles. But in some cases, interviewer might ask full-blown sde level questions like red-black tree, etc. There is officially no distinction for psds round between ui and sde roles, but practically there is.

## 💛 UI+Javascript 

I felt this was the most important round. It went relatively smooth for me and lasted for about an hour. Expect moderate to advanced Javascript and some browser related questions.

Ex - debounce + throttling (very common nowadays), event handling with delegation, event capturing phase, scope, let/var, promise + promise.all, convert setTimeout to promise, async, fetch/XHR, REST methods (when to use which), how does a webpage load from start, layout, rendering, etc.

Most of these are standard questions. Some questions were asked indirectly through a UI scenario.  

## 😎 Hiring manager round

If you have reached till this round, that's good news. HR will only present 2-3 potential candidates to the manager to save his/her time.  
The manager can technically ask anything they need to make a final decision. Expect some -  
a. HR questions (why do you want to move, what are you looking for in new role), and  
b. some tech questions (authentication, cookies in details, localstorage, new react features, some repeat questions from prev round, etc).  

Don't read too much into the HR questions - they are pretty standard and nothing personal about you. 

## 🧶 In summary

- Apply through recruitment agencies - that's your best chance of getting a interview.
- Have patience during the onsite - it's not super organised.
- Prepare basic leetcode questions for algo/ds. The questions are not going to build on top of popular algorithms. Interviewers will explain the problem and expect you to come up with atleast some naive solution and then optimise it (with hints).
- UI+JS round is quite important. Understand javascript and browser well. Explain things through UI usecases.
- Hiring manager will check that you are a good fit and technically strong. Be convincing.
- Interviewers are mostly unaware of which team you are interviewing for. They'll probably check your resume in the last 30 seconds before calling you in.

The exact interview process and experience depends on the team you're applying for. Due to the current situation, some of these rounds might have changed slightly (like less interaction with interviewers, more hackerrank-ish rounds, etc.).  
The general structure and advice should still hold true.

### If you liked this post, subscribe to my Javascript newsletter - https://buttondown.email/bendtherules.  
### I occasionally post on Youtube at https://studio.youtube.com/channel/UC3LLuQ-6j1OQpKKTKwHT_MA. And my personal website is https://bendtherul.es