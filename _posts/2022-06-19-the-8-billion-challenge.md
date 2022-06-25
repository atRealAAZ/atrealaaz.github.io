---
title: "The 8 Billion Challenge"
date: 2022-06-19
tags:
  - Random
---

Are you a curious person? Are you always eager to meet new people, to hear their stories? There is always more people out there to meet, no matter how often you go out or how many events you attend. There always is a silent majority of people you will never meet. But what if you were to meet every person alive? And not just meet them, but have a night of conversation with them, to find out what their life is like, what their story is. Would it be possible? Let's find out. 

As of this writing there are 7.9 billion walking around (or sitting/sleeping/whatever) on this Earth Let's assume that this is now your life goal so you're willing to spend the rest of your life on it. Since this is your life goal INSPIRATION you need no holiday. This means 7.9 billion divided by 365 = 21.643.835. So I'm 30 now, and I thought that was old, but when this little quest finishes you would be 21 *million*. Imagine this. "Hey how old are you by the way?" "Oh I'm 21 million". "Aah you're 21 million? Well, they say 21 million is the new 20 million 999 thousand and 990!" So, this is not feasible obviously. You need to be quicker. 

Since you can't talk an entire evening with everyone, you decide to have a short conversation, lets say 15 minutes, so you are still able to drill deep. And you decide that doing it at night is not enough, you'll do it the entire day. Since talking to people for the whole day means that you cannot work, you convince some institution to back you financially, say a university, cause it could be interesting to research the effect of knowing 8 billion people on the human psyche. They would pay you to talk to these people every day, and would pay for the cost of the flight ticket of every person, and compensate them for the time they would have to take off work to fly to your country and back. Since downtime means less time spent talking, you decide to streamline the process. This means that some sort of queue will have to be formed in front of your house, consisting of the people that you will talk to next. This means that those people definitely will have to stay in the city SLEEP EXTRA ELAB RESTAURANTS. It would be a monumental logistical challenge. Let us further assume that you are committed to this goal, so you do it from 9 - 8, so that you can still relax at night and process it all. You wouldn't need a lunch break, since you can talk while eating. (You can have some sick culinary journey if all these nationalities would bring some local food!) 11 hours / 15 minutes = 44 people per day. 7.9 billion divided by 44 ~= 492 000 years. Still too much. That's it, you think. Impossible. 

You're about to throw the towel in the ring - as the Dutch are wont to say - and are about to schedule the meeting with your university contact to tell them that it's not possible. But then an idea strikes you. So you contact your contact as planned, but you say they must construct an assembly line that will move past your couch. You may not be able to speak to everyone, but at least you'll have *seen* everyone. Hell, and if everyone mentions their name while they move past you on the assembly line and you greet them back, technically this will have constituted a conversation! It would be interesting to see how many names you remember afterward. Let's assume you have 50 years left. Since you're motivated about this goal you will take no more vacation, since meeting everyone will give you all the relaxation you need. 50 years * 365 days = 18250 days left. This means you have to see 432.876 people every day. Assuming you are awake from 9 to 8, and your lunch is done in front of the assembly line, you have to see 432876/11 hours/3600 seconds per hour = 11 people per second. Assuming every person takes 1m of space on the assembly line, the speed of this assembly line would be 11m/s = 11 hours * 3600 seconds / 1000  = 40km/h. So there you have it! It would be possible to talk to 7.9 billion people in your lifetime. All it requires is an elaborately constructed assembly line, a logistical miracle, and a shit-ton of money. 

But wait a minute... there is a problem. 

This means you have *just* talked to 7.9 billion people. But in 50 years some of those people that you talked to are dead, and new ones will have been born. So, to be able to say that you have talked to every person *alive*, you have to take that into account. 

So you decide, that you aim for a point at which you have talked to the entire population alive. 

To do that, we fit a simple model, where we take the current population, and each year add the people that are born, and remove the people that die. As per below.  

```ruby
class World:
    
    def __init__(self, population, growth_rate, mortality_rate):
        self.population = population
        self.growth_rate = growth_rate
        self.mortality_rate = mortality_rate
        
    def evolve(self, years):
        for year in range(1, years):
            self.population += (
                self._transform_rate(self.growth_rate) - 
                self._transform_rate(self.mortality_rate)
            )
            print(self.population)
        return self.population
        
    def _transform_rate(self, rate):
        return self.population/1000 * rate
```
Of course this model is quite rudimentary. This assumes that the fertility and mortality rates stay constant, which they of course do not, since the advent of progress means that mortality rate goes down when life quality goes up, and fertility rates go down since progress on average means less children. But then this exercise would turn into 'create an accurate model for world population' which is not the goal here. So I'll just assume those rates stay constant. We take the current population, average global fertility rate and average global mortality rate, as given by the World Bank https://data.worldbank.org/indicator/SP.DYN.CDRT.IN. This gives us a yearly prediction of the population. 
```python
w = World(7.9E9, 18.1, 7.7)
w.evolve(50)
```
What we can then do, is to take the current population, and then add the number of people that are born and subtract the people that die. So now there's 7.9 billion people. Next year there is 8.2 billion, which already includes the born and the dead people. So we add the difference each year. In the beginning this makes sense, since in the beginning you will not have spoken to anyone, so all the people that die will be in your set of people that you still have to speak to, whereas at the end you will have spoken to almost everyone, so all the people that die will most probably come out of your set of people that you have already spoken to. So as time progresses it would make more sense to add the newly-borns instead of the newly-borns minus newly-deads so that newly-borns minus deaths converges to newly-borns CLEAR. But again, I just want to get a decent estimate instead of the most accurate one, so we will make this simplifying assumption. So adding the population change for the next 50 years to our initial number, we get... 13.116 billion.So dividing this number by how many days we had left (18250) we get 718,684 people we have to see each day, resulting in a assembly line speed of ~66km/h. Which is basically the maximum speed of a rural road in the Netherlands, so it's as if you'd be watching cars flick by.

THE PICTURE. Assembly line blabla.

So that's it. Even though it will not last long - since every second four new people are born, and two die - but at least for a moment (literally) you will have known... everyone. "Do you know this person?" will be a pointless question to ask you, because you will know them. "Do you know..." "Yes", you interrupt. "But I didn't finish my sentence!" "Doesn't matter, I know them". Every time you hear a gossip, from anyone, you'll be like.. "Ah i know this person!" Every person that has a problem, you'll be like... "I know the perfect person to solve this for you." Your network will be huge. Not just huge, literally all-encompassing. Some people have a huge network. Well, now you are a step above that. How would you call that? Your network is full? All-encompassing? You would be able to meet your theoretical perfect match on this planet. Assuming that person is not already accounted for. You could pick and choose your best friends. Your best matches. It would be interesting. 

But it would also be a strange experience, to know literally everyone. And to have everyone know you. You would be a supercelebrity, a hyperstar, more so than current celebrities who are limited to the Western world, because literally everyone on this planet would know who you are. Everytime you go shopping, or for a walk you'd see acquaintances everywhere. Every time you take a trip, you step on a plane and land in the middle of nowhere and you would go to the outskirts of some jungle to land with a (hitherto) unknown tribe and they would greet you as an acquaintance. No. everywhere there would be acquaintances. Or if you go to the biggest gathering of people possible, to a stadium, EVERYONE would know you. You'd be like, are they watching me or the match? Would also be quite tiring. If you meet someone you know you have a quick chat, but to have to do this with literally everyone you pass? Quite difficult. Everytime you walk on a bus, all those eyes staring at you, knowingly. You know that feeling when you get on a bus and you recognize someone, but you have no desire to talk to that person so you pretend to not notice them and go sit somewhere else? Well good luck, wherever you're going to sit you're going to know them. So superfame would not actually be a nice thing. Anonymity is valuable, it turns out.

So what did we learn from this? I have no idea, I just thought it would be fun to think it through. And perhaps we learned something about the blessing of anonimity - about not everyone knowing you, about being able to live the life you want to live in unknowningness without people bothering you - along the way.