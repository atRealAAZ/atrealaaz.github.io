---
title: "The World Simulator"
date: 2022-06-19
tags:
  - Random
---

What if you could simulate the world?

We have a human
```python
class Human:
    
    def __init__(self):
        pass
        
    def _name(self):
        pass
```

We have a society:
```python
class Society:
    
    def __init__(self):
        self.name = generate_name()
        self.population = self._generate_population()
        
        self.reserves = 0
        self.resources = 0
    
    def _generate_population(self):
        pop = int(10 ** np.random.uniform(3, 5))
        return pop
    
    def _harvest(self):
        farmers = 0.25 * self.population
        farmer_productivity = 5
        food_production = farmers * farmer_productivity * harvest_success()
        self.population = min(food_production + self.reserves, self.population)
        self.reserves = max(0, food_production)
        
    def _population_change(self):
        fertility_rate = 1.04
        mortality_rate = 0.98
        self.population = int(self.population * fertility_rate * mortality_rate)
    
    def _natural_disasters(self):
        if event(0.1):
            severity = 1 - np.random.uniform(0, 0.1)
            self.population = int(severity * self.population)
        else:
            pass
            
            #nr_casualties = int(severity * len(self.population))
            #deaths = chosen(self.population, nr_casualties)
            #self.population = [h for h in self.population if h not in deaths]
        
    def _innovation(self):
        #Resources?
        #Kans dat 'level up' gebeurt. 
        
        pass
        
    def advance(self, years):
        pops = []
        ress = []
        for year in range(years):
            self._harvest()
            self._population_change()
            self._natural_disasters()
            self._innovation()
            pops.append(self.population)
            ress.append(self.reserves)
        return pops
```

We have a world:
```python
class World:
    
    def __init__(self, nr_societies):
        self.nr_societies = nr_societies
        self.societies = [Society() for s in range(nr_societies)]
        
    def war(self, p):
        if event(p):
            warring_societies = chosen(self.societies, 2)
            war_outcome = war(warring_societies)
        else:
            pass
        
    def evolve(self, years):
        for year in range(years):
            for idx, society in enumerate(self.societies):
                self.societies[idx] = Society.advance()
                self.war(0.2)
```
