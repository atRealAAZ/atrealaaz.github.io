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
        self.leader = Human()
        self.popularity_over_time = []

    def update_popularity(self, change):
        self.popularity = round(min(100, max(0, self.popularity + change)), 1)

    def policies(self):
        economic_policy, political_policy, civil_policy = np.random.normal(0, 1, 3)
        return economic_policy, political_policy, civil_policy
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
        print(f"Society generated with parties {self.parties[0].name} and {self.parties[1].name} generated! Current Government is {self.government.name} Administration.")
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
            
            self.govern()
            
            if year % self.election_interval == 0 and year != 0:
                print(f"Elections in year {year}!")
                self.elections()
                
            self.political_dynamics()
            
            for party in self.parties:
                party.popularity_over_time.append(party.popularity)
            
            self.status()
            print(f"----------YEAR {year} ENDED------------")

     def status(self):
        self.stats['government'].append(self.government)
        self.stats['economy'].append(self.economic_health)
        self.stats['politics'].append(self.political_stability)
        self.stats['civil'].append(self.civil_liberties)
        print(f"""
          Current Government: {self.government.name}, Popularity: {self.government.popularity}, 
          Economy: {self.economic_health}, 
          Politics: {self.political_stability}, 
          Civil Liberties: {self.civil_liberties}
        """)
            
```

We also added the self.status(), to print our societal variables after every year. Great! Now let's build them.


# Economy
To start off with the economy. There will always be some natural growth. Of course this is not natural per se but due to people doing things in the economy, but to capture those effects we will make them part of the natural growth. We assume the average is 1%, and it can be higher or lower, probabilistically. In other words, it is a normal distribution with average 1 and standard deviation also 1. 

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
        print(f"The economy grew naturally by {natural_growth}")

    def update_societal_parameters(self, economic_impact, political_impact, civil_impact):
        self.economic_health = round(min(100, max(0, self.economic_health + economic_impact)), 1)
        self.political_stability = round(min(100, max(0, self.political_stability + political_impact)), 1)
        self.civil_liberties = round(min(100, max(0, self.civil_liberties + civil_impact)), 1)
```
We have also added a `update_societal_parameters` method to ensure that they're bounded by 0 and 100.

Also, economic crises are a thing. We assume that one happens with a 5% probability (ie we can expect one every 20 years). If one hits, the economy can shrink between 1% and 20%, with an average of 5%. In other words, a -5% mean and 4% standard deviation. We assume an economic crisis also has an impact on the political stability, i.e. people lose trust in the government after a crisis (we assume with a multiplier of 2), and of course the government's popularity is updated based on this as well. The ruling party will get a boon that is equal to how well the economy is doing. Also, the ruling party will get a popularity change in comparison to expectations for economy growth. So even if an economy is growing, but below people's expectations, the government's popularity diminishes. We will assume that people expect the economy to grow by 3% each year, and get a corresponding popularity decline if it's lower than this. Let's add this. 

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
        self.government.update_popularity(2 * (natural_growth - 3))
        print(f"The economy grew naturally by {round(natural_growth, 2)}.")
        #The ruling party's popularity is updated depending on the state of the economy
        self.government.update_popularity((self.economic_health - 50)/10)
        if event(0.05):
            #Every few years there will be an economic crisis
            economic_crisis = min(0, np.random.normal(-5, 4))
            self.update_societal_parameters(economic_crisis, 2 * economic_crisis, 0)
            self.government.update_popularity(2 * economic_crisis)
            print(f"Economic crisis of {round(economic_crisis, 2)} hit!")
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
                scandal = np.random.choice(['Corruption', 'Infidelity', 'Bribery'])
                #Higher civil liberties lead to higher probability of it getting to light.
                if event(self.civil_liberties):
                    scandal_impact = round(min(0, np.random.normal(-3, 2)), 2)
                    party.update_popularity(scandal_impact)
                    print(f"Member of {party.name} committed {scandal}. It came out: {scandal_impact} popularity lost!")
                else:
                    print(f"Member of {party.name} committed {scandal}. It was hushed up, no popularity lost!")
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
            print(f"{self.government.name} re-elected for another 4 years through election rigging.")
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
            print(f"{self.government.name} re-elected for another 4 years through election rigging.")
        else:
            popular_party = sorted(self.parties, key = lambda x: x.popularity, reverse = True)[0]
            if popular_party == self.government:
                print(f"{self.government.name} re-elected for another 4 years.")
            else:
                print(f"{popular_party.name} beat incumbent {self.government.name}.")
                self.government = popular_party
                self.government.update_popularity(5)
```

That's our elections module!

# Governing

A party in power will also have policies of course. They will govern through those policies, which will have an effect on the societal parameters. We take those from the party's policies() method. We assume that if an economy is doing very well, it is unlikely it will do even better. If an economy is overheated, it will soon slow down, and vice versa. The better the economy is doing, the more likely it will be worse off by policy. A random effect will also be added to the governing parameters, to account for randomness in running a society. 

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
            economic_policy -= (self.economic_health - 80)/20
        economic_policy += np.random.normal(0, 1)
        political_policy += np.random.normal(0, 1)
        civil_policy += np.random.normal(0, 1)
        print(f"""Due to policy the economy changed by {round(economic_policy, 2)}, politicical stability changed by {round(political_policy, 2)}, civil Liberties changed by {round(civil_policy, 2)}.""")
        self.update_societal_parameters(economic_policy, political_policy, civil_policy)
        self.government.update_popularity(2 * economic_policy + political_policy + civil_policy)
```

That's our governing module!

We now have a pretty solid base to simulate this society! Let's do so!

We first initialize the society as follows:

```python
s = Society()
```
which gives us: Society generated with parties The Uijutos and The Cakins generated! Current Government is The Uijutos Administration. Let's go

Let's start by simulating it for 10 years:

```python
s.advance(10)
```









