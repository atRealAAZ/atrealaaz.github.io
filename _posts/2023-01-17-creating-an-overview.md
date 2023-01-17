---
title: "Part 3: Creating an overview"
date: 2023-01-17
tags:
  - Focus

---

<h1>Overview</h1>

Here we will be creating our overview. 

What would we like to see? We would probably want to see our name, our account number, and our balance, together with all transactions below this.

Something along the lines of this:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_overview/overview_look.png" alt="">

We will split this up in three parts, the account information part, the Transfer button, and the Transaction table.

We now have a folder structure as follows:

<img src="{{ site.url }}{{ site.baseurl }}/assets/images/building_a_bank_frontend_overview/hierarchy.png" alt="">

Our application will consist of multiple subcomponents, one of them being our Overview.

We will create a new ```subcomponents``` folder in the ```src``` folder which will contain the code for our Overview. This is good practice, keeping the code for several systems separate. In there we will create a new folder called overview, which will house our ```Overview.js``` code. Anytime you want to debug or change some code, you know the folder it will be in. 

The folder structure is then as follows:

```
src 
 - subcomponents
   - overview
     - Overview.js
   - other subcomponents to be added
```

We open our Overview.js file, which for now it a clean sheet.

We add the following code:

```javascript
import {Component} from 'react'

class Overview extends Component {
    render () {
        return (
            <>
                <AccountInformation/>
                <TransactionTable/>
            </>
        )
    }
}

export default Overview
```
We now have our boilerplate code that will contain the rest of our functions.


