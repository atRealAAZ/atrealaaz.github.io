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

We will build up the society slowly. We will define some macro-economic variables that all behaviour will interact on. These are Economic Health, Political Stability and Civil Liberties. Economic Health can be seen as a measure of how well the economy is doing. Political Stability can be seen as a measure of how stable the society is. Both of these are 
important for a country. If one is low, the going gets tough. We can implement this as follows. Civil Liberties is a measure of how 'free' its citizens are.

```python
class Society:

  def __init__(self):
    self.economic_health = 60
    self.political_stability = 60
    self.civil_liberties = 60
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
        return 'The ' + name[:-1] + 's'
    else:
        return name[:-1] 
```
This chooses a random length for a name, then chooses a random first letter, and keeps extending the string with random letters until its finished. 

We will update our Party code, and also add a method that updates the popularity of the party, but ensures it stays within [0, 100]:

```python
class Party:

    def __init__(self):
        self.name = generate_name(1, entity = 'party')
        self.popularity = 50

    def update_popularity(self, change):
        self.popularity = min(100, max(0, self.popularity + change))

    def policies(self):
        economic_policy, political_policy, civil_policy = np.random.normal(0, 1, 3)
```

We have also added a policies method, that generates the impact of policies on the societal parameters randomly, since it is impossible to predict the effect of policy on
societal parameters (if we could, many societal problems could be solved). We also assume they are independent here. 

Let's simulate a party, to see what we get.

```python
p = Party()
p.name
```
yields us 'The Aegets'. Interesting. 

We also define a Human:

```python
class Human:
    
    def __init__(self):
        self.name = generate_name(2, 'person')
```

Initializing a human to see what kind of name we get: 

```python
h = Human()
h.name
```

yields us Sajilemoko Eosibo. Interesting!

So in our societal simulation, we'll start with two parties. And one of these will randomly be chosen as the initial government. Elections will be held every 4 years. Let's update our Society.

```python
   
class Society:
    
    def __init__(self, election_interval = 4):
        self.economic_health = 60
        self.political_stability = 60
        self.civil_liberties = 60
        self.parties = [Party(), Party()]
        self.government = np.random.choice(self.parties)
        self.election_interval = election_interval
        self.year = 0
        self.stats = {
          'economy': [], 
          'politics': [], 
          'civil': [], 
          'government': []
        }
```

We of course start in year 0. And the stats feature is a way to save our societal variables every year so we can track it over time.

So let's think about what kind of dynamics we want in our society. We want the economy to evolve of course. Let's also build in some natural disasters, e.g. earthquakes
and tornadoes. We also want to see an effect of the ruling party in our society, let's call this governing. If it is an election year, we want to see elections of course. 
And we want to see some political maneuvering, perhaps even a revolution. Let's add this:

```python

class Society:
    
    def __init__(self, election_interval = 4):
        ####
        
    def advance(self, years):
        for year in range(years):
            print(f"----------YEAR {year} STARTED----------")
            self.economy()
            
            self.natural_disasters()
            
            self.govern()
            
            if year % self.election_interval == 0 and year != 0:
                print(f"Elections in year {year}!")
                self.elections()
                
            self.political_dynamics()
            
            self.revolution()
            
            self.status()
            print(f"----------YEAR {year} ENDED----------")

     def status(self):
        self.stats['government'].append(self.government)
        self.stats['economy'].append(self.economic_health)
        self.stats['politics'].append(self.political_stability)
        self.stats['civil'].append(self.civil_liberties)
        print(f"""
          Current Government: {self.government.name}, \\
          Economy: {self.economic_health}, \\
          Politics: {self.political_stability}, \\
          Civil Liberties: {self.civil_liberties}
        """)
            
```

We also added the self.status(), to print our societal variables after every year. Great! Now let's build them.


# Economy
To start off with the economy. There will always be some natural growth. We assume the average is 1%, and it can be higher or lower, probabilistically. In other words, it is a normal distribution with average 1 and standard deviation also 1. 

```python

class Society:
    
    def __init__(self, election_interval = 4):
      <SNIP>
        
    def advance(self, years):
      <SNIP>

    def economy(self):
        natural_growth = np.random.normal(1, 1)
```

We assume that high civil liberties lead to a higher natural growth, because high civil liberties encourage innovation, and open exchange of ideas, benefitting the economy. We therefore add a constant to the natural growth rate proportional to the civil liberties. Equally, with low civil liberties we subtract a constant, with a maximum of 1:

```python
class Society:
    
    def __init__(self, election_interval = 4):
      <SNIP>
        
    def advance(self, years):
      <SNIP>

    def economy(self):
        #There will be natural growth most of the time, sometimes slight recession
        natural_growth = np.random.normal(1, 1)
        #Natural growth is boosted by high civil liberties, aka innovation, entrepeneurship
        natural_growth += (self.civil_liberties - 50)/100
        self.update_societal_parameters(natural_growth, 0, 0)
        print(f"Economy grew by {natural_growth}")

    def update_societal_parameters(self, economic_impact, political_impact, civil_impact):
        self.economic_health = min(100, max(0, self.economic_health + economic_impact))
        self.political_stability = min(100, max(0, self.political_stability + political_impact))
        self.civil_liberties = min(100, max(0, self.civil_liberties + civil_impact))
```
We have also added a `update_societal_parameters` method to ensure that they're bounded by 0 and 100.

Also, economic crises are a thing. We assume that one happens with a 5% probability (ie we can expect one every 20 years). If one hits, the economy can shrink between 1% and 20%, with an average of 5%. In other words, a -5% mean and 4% standard deviation. We assume an economic crisis also has an impact on the political stability, i.e. people lose trust in the government after a crisis (we assume with a multiplier of 2). The ruling party will get a boon that is equal to how well the economy is performing. Let's add this. 

```python
class Society:
    
    def __init__(self, election_interval = 4):
      <SNIP>
        
    def advance(self, years):
      <SNIP>

    def economy(self):
        #There will be natural growth most of the time, sometimes slight recession
        natural_growth = np.random.normal(1, 1)
        #Natural growth is boosted by high civil liberties, aka innovation, entrepeneurship
        natural_growth += (self.civil_liberties - 50)/50
        self.update_societal_parameters(natural_growth, 0, 0)
        #Ruling party will be attributed the growth
        self.government.update_popularity(2 * natural_growth)
        print(f"Economy grew by {natural_growth}")
        #The ruling party's popularity is updated depending on the state of the economy
        self.government.update_popularity((self.economic_health - 50)/10)
        if event(0.05):
            #Every few years there will be an economic crisis
            economic_crisis = max(0, np.random.normal(-5, 4))
            self.update_societal_parameters(economic_crisis, 2 * economic_crisis, 0)
            print(f"Economic crisis of {economic_crisis} hit!")
```
where an event is defined as:

```python
def event(p):
    if np.random.uniform(0, 1) <= p:
        return True
    else:
        return False
```

Great! That's our economy module. Let's continue.

# Political Dynamics

We will now continue with our political dynamics module. We assume that if the party is is in power, it will probably lose popularity, unless it is extremely lucky. In other
words, it will lose popularity every year with a mean of -2 and a standard deviation of 2. This means that most years popularity will decrease a bit, some years slightly, and in some years it will increase slightly. This should somehow mirror real-life dynamics that we mostly hold our government in worse esteem. For the party not in power we'll also simulate some behaviour, but since they are not in power they lose popularity slower. 

```python
class Society:
    
    def __init__(self, election_interval = 4):
      <SNIP>
        
    def advance(self, years):
      <SNIP>

    def economy(self):
      <SNIP>

    def political_dynamics(self):


      #Ruling party will be more criticized
        for party in self.parties:
            if party == self.government:
                party.update_popularity(np.random.normal(-2, 2))
            else:
                party.update_popularity(np.random.normal(-1, 2))
      
```

We'll also assume that politicians do bad stuff, and this may come to light. There will be scandals, but depending on civil liberties, it will be more likely to come to light. We assume one happens every year with a 50% probability. But even if one happens, we'll assume it'll only come to light probabilistically. The higher the civil liberties (and thus the freeer the media), the more likely it is to happen. We'll assume that the probability it comes to light is equal to the civil liberties. So in a completely free society it will always come to light, and in a totally unfree society it will never come to light.

```python
class Society:
    
    def __init__(self, election_interval = 4):
      <SNIP>
        
    def advance(self, years):
      <SNIP>

    def economy(self):
      <SNIP>

    def political_dynamics(self):


      #Ruling party will be more criticized
        for party in self.parties:
            if party == self.government:
                party.update_popularity(np.random.normal(-2, 2))
            else:
                party.update_popularity(np.random.normal(-1, 2))
            if event(0.5):
                #50% chance of scandal.
                if event(self.civil_liberties):
```

A scandal will have an impact on the popularity of the party. Let's assume it has a mean of -3 and a standard deviation of 2, meaning in some years it will be around 6, or near 0, but most of the time will hover around 3. Let's also make sure it cannot go above 0, because that would mean that a politician would gain popularity after a scandal (how politicians would love this). Let's also give the scandal a name, to make it speak to the mind more.

```python
class Society:
    
    def __init__(self, election_interval = 4):
      <SNIP>
        
    def advance(self, years):
      <SNIP>

    def economy(self):
      <SNIP>

    def political_dynamics(self):
      #Ruling party will be more criticized
        for party in self.parties:
            if party == self.government:
                party.update_popularity(np.random.normal(-2, 2))
            else:
                party.update_popularity(np.random.normal(-1, 2))
            if event(0.5):
                #50% chance of scandal.
                if event(self.civil_liberties):
                    scandal = np.random.choice(['Corruption', 'Infidelity', 'Bribery'])
                    scandal_impact = max(0, np.random.normal(-3, 2))
                    party.update_popularity(scandal_impact)
                    print(f"Member of {party.name} committed {scandal}. {scandal_impact} popularity lost")
```

Great, that's our political dynamics module!

# Elections

We will also simulate elections. We assume that the lower the civil liberties, the more likelly it is that elections will not go fair. Let's assume this can only start happening if civil liberties are already quite low. Let's say 40%. So if civil liberties are higher than 40%, elections will always go fair, but below 40% it starts to increase. With civil liberties at 0, elections will be unfair all the time. If an election is unfair, the ruling party will win. 

```python
class Society:
    
    def __init__(self, election_interval = 4):
        <SNIP>
        
    def advance(self, years):
        <SNIP>

    def economy(self):
        <SNIP>

    def political_dynamics(self):
        <SNIP>

    def elections(self):
        election_rigging_probability = min(0, self.civil_liberties - 40) * - 2.5
        if event(election_rigging_probability):
            print(f"{self.government.name} re-elected for another 4 years through election rigging")
```

If there's no election rigging, we assume that the most popular party will win. If the non-incumbent wins, we assume they will gain a popularity boost, because 
people gain new hope. Let's put that at 5.

```python
class Society:
    
    def __init__(self, election_interval = 4):
        <SNIP>
        
    def advance(self, years):
        <SNIP>

    def economy(self):
        <SNIP>

    def political_dynamics(self):
        <SNIP>

    def elections(self):
        election_rigging_probability = min(0, self.civil_liberties - 40) * - 2.5
        if event(election_rigging_probability):
            print(f"{self.government.name} re-elected for another 4 years through election rigging")
        else:
            popular_party = sorted(self.parties, key = lambda x: x.popularity, reverse = True)[0]
            if popular_party == self.government:
                print(f"{self.government.name} re-elected for another 4 years")
            else:
                print(f"{popular_party.name} beat incumbent {self.government.name}")
                self.government = popular_party
                self.government.update_popularity(5)
```

That's our elections module!

# Governing

A party in power will also have policies of course. They will govern through those policies, which will have an effect on the societal parameters. We take those from the party's policies() method. We assume that if an economy is doing very well, it is unlikely it will do even better. If an economy is overheated, it will soon slow down, and vice versa. The better the economy is doing, the more likely it will be worse off by policy. There will also be random effects of governing.

```python
class Society:
    
    def __init__(self, election_interval = 4):
        <SNIP>
        
    def advance(self, years):
        <SNIP>

    def economy(self):
        <SNIP>

    def political_dynamics(self):
        <SNIP>

    def elections(self):
        <SNIP>

    def govern(self):
        economic_policy, political_policy, civil_policy = self.government.policies()
        if self.economic_health > 80:
            economic_policy += (self.economic_health - 80)/10
        economic_policy += np.random.normal(0, 1)
        political_policy += np.random.normal(0, 1)
        civil_policy += np.random.normal(0, 1)
        print(f"Economy changed by {economic_policy}. Politics updated by {political_policy}. Civil Liberties updated by {civil_policy}")
        self.update_societal_parameters(economic_policy, political_policy, civil_policy)
        self.government.update_popularity(2 * economic_policy + political_policy + civil_policy)
```

That's our governing module!

----------------------------------

The following dynamics are at play. Since an economy is partially random (or it can be discussed if it is random, it could be an emergent phenomenon due to multiple independent actors interacting.)

Every year, there will be random shocks (that can be positive or negative) to the economy. Also other factors that we do not build in that could be of influence will be part of this.

Every year, people will be annoyed by the ruling party. This is a timeless constant, we get fed up with the powers that be because we are never satisfied. So we blame it on the ruling party. So every year there is a decrease in popularity. 

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
