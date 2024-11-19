---
title: "Simulating a Society"
date: 2024-09-17
tags:
  - Random
---

Not a day goes by when some politician makes some comment about the economy. Taxes go up! No! Taxes go down! No, something else entirely!

But what are all these statements based on?

Hot air. Hot air and the delusions of of the many. 

So let's take a bit more quantitative approach. 

So let's simulate a society. 

We will build up the society slowly. We will define some macro-economic variables that all behaviour will interact on. These are Economic Health and Political Stability. 
Economic Health can be seen as a measure of how well the economy is doing. Political Stability can be seen as a measure of how stable the society is. Both of these are 
important for a country. If one is low, the going gets tough. We can implement this as follows.

```python
class Society:

  def __init__(self):
    self.economic_health = 60
    self.political_stability = 60
```
We will initialize the variables at 60 (out of 100). A bit above average, but not too high.

A society must be ruled of course. And who rules? A party usually. So we will define those also. 

```python
class Party:
  def __init__(self):
    self.popularity = 50
```
Since political parties are never too popular, we will initialize its popularity at 50. But what is a party (or a person for that matter) without a name? We will create
a simple script that generates a random name. 

```python
letters = [i for i in 'abcdefghijklmnopqrstuvwxyz']
vowels = ['a', 'e', 'i', 'o', 'u']
consonants = [l for l in letters if l not in vowels]

def gen_next_letter(letter, method):
    if letter in klinkers:
        if method == 'old':
            return np.random.choice(letters)
        else:
            return np.random.choice(medeklinkers)
    else:
        return np.random.choice(klinkers)
    
def generate_name(names = 1, method = 'new', entity = 'person'):
    name = ''
    for subname in range(names):
        length = np.random.randint(5, 12)
        first = np.random.choice(letters).upper()
        name += first
        iters = 1
        while iters < length:
            next_letter = gen_next_letter(name[-1], method)
            name += next_letter
            iters +=1 
        name += ' '
    if entity == 'party':
        return 'The ' + name[:-1]
    else:
        return name[:-1] 
```
This chooses a random length for a name, then chooses a random first letter, and keeps extending the string with random letters until its finished. 

The following dynamics are at play. Since an economy is partially random (or it can be discussed if it is random, it could be an emergent phenomenon due to multiple independent actors interacting.)

Every year, there will be random shocks (that can be positive or negative) to the economy. Also other factors that we do not build in that could be of influence will be part of this.

Every year, people will be annoyed by the ruling party. This is a timeless constant, we get fed up with the ruling party. So every year tehre is a decrease in popularity. 

-random increases
-ruling party achteruit
-je hebt popularity. als economy goed gaat, extra boon for 
popularity. 
-standaard achteruit popularity gaat achteruit. 
als popularity laag is: andere worden verkozen. auth als 
condities erg laag zijn.

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

But it would also be a strange experience, to know literally everyone. And to have everyone know you. You would be a supercelebrity, a hyperstar, more so than current celebrities who are recognized only by the developed world, because literally everyone on this planet would know who you are. Everytime you go shopping, or for a walk, you'd see acquaintances everywhere. No, everywhere there would *be* acquaintances, behind every corner, behind every house. No matter where you go on this Earth, to the outskirts of civilization, to the edges of the farthest reachest of the end of the world, you would be recognized. From the driest desert through the highest mountain to the deepest jungle, on the islands of the peoples of the islands that are spread around like droplets on the boundless oceans, where there would be people there would be recognition. Or you venture to the biggest gathering of people possible, a stadium housing tens of thousands, *everyone* would know you. You'd be like, are they watching me or the match? Imagine all those people looking at you. You better not have any stage fright then. 

It would also be quite tiring. If you meet someone you know on the street, you have a quick chat. But to have to do this with literally everyone you pass? Quite difficult. Or that feeling when you get on a bus and you recognize someone, but you have no desire to talk to that person so you pretend to not notice them and go sit somewhere else? Well good luck, wherever you're going to sit you're going to know them. So superfame would not actually be a nice thing. Anonymity is valuable, it turns out.

So what did we learn from this? I have no idea; I just thought it would be fun to think through, to see what would be required to accomplish such a task. 

And perhaps we learned something about the blessing of anonimity - about not everyone knowing you, about being able to live the life you want to live in unknowningness without people bothering you - along the way.
