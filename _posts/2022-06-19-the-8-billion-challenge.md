---
title: "The 8 Billion Challenge"
date: 2022-06-19
tags:
  - Random
---

Are you a curious person? Are you always eager to meet new people, to hear their stories? Yes, you say, that's why you go to many networking events and social activities. But no matter how often you go out or how many events you attend, there always are more people out there to meet. There will always be a silent majority of people you will never encounter. But what if you were to meet every person alive? And not just meet them, but to talk to them; to find out what their life is like, what their story is. Would it be possible? Let's find out. 

As of this writing there are 7.9 billion people walking around (or sitting/sleeping/whatever) on this Earth. Your goal is now to meet every single one of them, and to have a night of conversation with them so you can get to know them. Let's assume that you make this your life's priority, your grand mission. Since spending the rest of your life on this requires an extreme amount of dedication this has to mean you are extremely passionate about it, you therefore need no holiday. 7.9 billion people divided by 365 days = 21.643.835. So I'm 30 now, and I thought that was old, but when this little quest finishes you would be 21 *million*. I already imagine how conversations would go. "Hey how old are you by the way?" "Oh I'm 21 million". "Aah 21 million? Well, they say 21 million is the new 20 million 999 thousand and 990!" So, this is not feasible obviously. You need to be quicker. 

Since talking an entire evening to each person takes too long, you decide to just have a short conversation, lets say 15 minutes, so you are still able to find out what makes the other tick if you ask the right questions. And you decide that doing it at night is not enough, you'll do it the entire day. Since talking to people for the whole day means that you cannot work, you convince some institution to back you financially - say a university - by telling them it could be interesting to research the effect of knowing 8 billion people on the human psyche. They would pay you to talk to these people every day, and they would pay for their transportation as they have to be flown in from all over the world.

The less time you spend talking the longer it takes, so you want to minimize downtime. No downtime means that the next person to talk to has to always be ready, which means that some sort of queue will have to be formed in front of your house. A private airfield will probably have to be constructed to avoid aviatory delays. Some people will definitely need an overnight stay in the city, which means that accomodations have to be booked and maybe even built. Restaurants need to be opened to feed everyone, plus extra activities organized to keep them entertained while they wait for their turn. It would be a monumental logistical challenge, the biggest one ever. At no point in history has the entire global population been mobilized, until now.

So after this infrastructure has been set up, you decide to talk to people from 9AM to 8PM, so that you can still relax at night and process it all. You wouldn't need a lunch break, since you can talk while eating (and if everyone would bring some local food you could at least have an immersive culinary experience!). 11 hours / 15 minutes = 44 people per day. 7.9 billion / 44 ~= 492 000 years. Still too much. That's it, you think. Impossible. 

You're about to throw the towel in the ring - as the Dutch are wont to say - and are about to schedule the meeting with your university contact to tell them that it's not possible. But then an idea strikes you. So you contact your contact as planned, but you say they must construct an assembly line. An assembly line that will carry all the people you have to speak to past your couch, because that would be the only way to reach the goal. You may not be able to speak to everyone, but at least you'll have *seen* everyone. Hell, if everyone mentions their name while they move past you on the assembly line and you greet them back, technically this will have constituted a interaction! It would be interesting to see how many names you remember afterward. 

Let's assume you have 50 years left. 50 years * 365 days = 18,250 days. This means you have to see 432,876 people every day. Assuming you are awake from 9 to 8, and your lunch is done in front of the assembly line, you have to see 432,876 people / 11 hours / 3600 seconds per hour = 11 people per second. Assuming every person takes 1 meter of space on the assembly line, the speed of this assembly line would be 11 meters per second * 3600 seconds / 1000 m/km = 40 km/h. So there you have it! It would be possible to talk to 7.9 billion people in your lifetime. All it requires is an elaborately constructed assembly line, a logistical miracle, a shit-ton of money, and the rest of your life. 

But wait a minute... there is a problem. 

This means you have "just" talked to 7.9 billion people. But in 50 years some of those people that you talked to are dead, and new ones will have been born. So, to be able to say that you have talked to every person *alive*, you have to take that into account. 

So you decide to aim for a point at which you have talked to the entire population alive. 

For that, we need to know how many people are alive at any point, to know how many people have been alive during a certain period. To do that, we fit a simple model to predict the world population at time *t*. This model is dependent on the current population, how many people are born each year (the *fertility rate*), and how many people die (the *mortality rate*). We construct it as per below, where each year the population increases by the amount of people that are born and decreases by the amount of people that pass.
```ruby
class World:
    
    def __init__(
      self, 
      population, 
      fertility_rate, 
      mortality_rate
    ):
        self.population = population
        self.fertility_rate = fertility_rate
        self.mortality_rate = mortality_rate
        
    def evolve(
      self, 
      years
    ):
        for year in range(
          1, 
          years + 1
        ):
            self.population += (
                self._transform_rate(
                  self.fertility_rate
                ) - 
                self._transform_rate(
                  self.mortality_rate
                )
            )
        return self.population
        
    def _transform_rate(
      self, 
      rate
    ):
      return (
        self.population/1000
      ) * rate
```
Of course this model is quite rudimentary. This assumes that the fertility and mortality rates stay constant, which they of course do not. The advent of progress means that mortality rates go down when life quality goes up, and fertility rates go down since progress means less children on average. But I'll make that assumption, otherwise this exercise would turn into 'create an accurate model for world population' which is not the goal here. 

We can then make a yearly prediction of the population by instantiating a class, which we initialize with the current population, and the global fertility and mortality rates per thousand people as given by the <a href="https://data.worldbank.org/indicator/SP.DYN.CBRT.IN">World</a> <a href="https://data.worldbank.org/indicator/SP.DYN.CDRT.IN">Bank</a>.
```python
w = World(
  7.9E9, 
  18.1, 
  7.70
)
```
We can then get a prediction per year for the next 50 years. 
```python
w.evolve(
  50
)
```
We now know how many people are alive in any year. Since achieving our goal will take 50 years, we need to know how many people have been alive during those 50 years. What we can then do, is to take the current population, and then each year add the new people that are born and subtract the people that die. So now there's 7.9 billion people, and the model predicts that next year there will be 7.98 billion. So that means next year we have to get to know 80 million people more (= new births - deaths). 

In the beginning we can just add the population difference to our initial number. This is because in the beginning you will not have spoken to anyone, which means all the people that die will be in your set of people that you still have to speak to (so you no longer have to), whereas at the end you will have spoken to almost everyone, so all the people that die will most probably come out of your set of people that you have already spoken to. So as time progresses the extra people you have to speak to each year converges to all the newly-borns instead of the newly-borns minus all the newly-deads. A more accurate model would take this into account, but again, I just want to get a decent estimate instead of the most accurate one, so we will forget this for now. 

Adding the population change for the next 50 years to our initial number, we get... 13.170 billion. So dividing this number by how many days we had left (18,250) we get 721,656 people we have to see each day, resulting in an assembly line speed of ~66 km/h. 

So, to reach a point in your lifetime where you have spoken to every person alive, you would have to be sitting on your couch for the next 50 years, from 9 in the morning to 8 in the evening watching people fly by on an assembly line going at the speed of 66 km/h. Which is basically the maximum speed of a rural road in the Netherlands, so kind of like standing on the side of the road watching cars pass by but instead of cars it would be people.

So that's it. Even though it will not last long - since every second four new people are born and two die - for a moment (literally) you will have known... everyone. "Do you know person X?" will be a pointless question to ask you, because you do. "Do you know..." "Yes", you interrupt. "But I didn't finish my sentence!", the other replies. "Doesn't matter, I know them". Every time you hear a gossip, from anyone, you'll be like... "Ah I know this person!" Everytime someone has a problem, you'll know the perfect person to solve it for them. Your network will be huge. Not just huge, literally all-encompassing. Some people have a huge network. Well, now you are a step above that. How would you call that? Your network is full? Complete? All-encompassing? You would be able to meet your theoretical perfect match on this planet. Assuming that person is not already accounted for. You would know who could be your best friends. Your best matches. It would be interesting. 

But it would also be a strange experience, to know literally everyone. And to have everyone know you. You would be a supercelebrity, a hyperstar, more so than current celebrities who are recognized only by the developed world, because literally everyone on this planet would know who you are. Everytime you go shopping, or for a walk, you'd see acquaintances everywhere. No, everywhere there would *be* acquaintances, behind every corner, behind every house. No matter where you go on this Earth, to the outskirts of civilization, to the edges of the farthest reachest of the end of the world, you would be recognized. From the driest desert to the highest mountain to the deepest jungle, where there would be people there would be recognition. Or if you venture to the biggest gathering of people possible, a stadium housing tens of thousands, *everyone* would know you. You'd be like, are they watching me or the match? Imagine all those people looking at you. You better not have any stage fright then. 

It would also be quite tiring. If you meet someone you know on the street, you have a quick chat. But to have to do this with literally everyone you pass? Quite difficult. Or that feeling when you get on a bus and you recognize someone, but you have no desire to talk to that person so you pretend to not notice them and go sit somewhere else? Well good luck, wherever you're going to sit you're going to know them. So superfame would not actually be a nice thing. Anonymity is valuable, it turns out.

So what did we learn from this? I have no idea; I just thought it would be fun to think through, to see what would be required to accomplish such a task. 

And perhaps we learned something about the blessing of anonimity - about not everyone knowing you, about being able to live the life you want to live in unknowningness without people bothering you - along the way.
