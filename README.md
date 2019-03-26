# Preparation, Anaylsis and Find Bugs
To run the docker on Windows 10 i had challenges;
-	Installing all the environment and making the git repository registration
o	docker-compose up -d db
o	Pull and run container.
o	Actual behavior
-	Docker return error with messages for running the container related with file systems permission
- Filesharing error on Windows c: disk. 
Solution: Go to Docker settings > shared drives > check the c: drive shared and enter the admin user credential to get sharing enabled.
-Nginx running error
Solution: Go to Docker settings > network > DNS server . change from automaic to fixed ( default is 8.8.8.8 )

After fixing these issues i am able to pull the docker repository and run on Windows 10.

# Analysis:

The docker containers successfully build and was running but i wasn't able to reach from my local pc with http://localhost:8080

CONTAINER ID        IMAGE                     COMMAND                  CREATED             STATUS              PORTS                  NAMES
038f2b0aa212        nginx:1.13.5              "nginx -g 'daemon of…"   37 hours ago        Up 26 hours         0.0.0.0:8080->80/tcp   systems-puzzle_nginx_1
5398f43a7850        systems-puzzle_flaskapp   "python app.py"          37 hours ago        Up 26 hours         5001/tcp               systems-puzzle_flaskapp_1
cd216b451c47        postgres:9.6.5            "docker-entrypoint.s…"   3 days ago          Up 26 hours         5432/tcp               systems-puzzle_db_1


Then i check if the apps are running at phyton server. 
C:\projects\systems-puzzle>docker exec 5398f43a7850 python forms.py
The apps were running.

I checked the nginx service is up and running

C:\projects\systems-puzzle>docker exec 038f2b0aa212 service nginx status
nginx is running.

nginx service was up and running.

Then i started to checking the configuration files;

nginx configuration for proxy port redirection was not correct.it was "80:8080" so that i changed it to "8080:80" in file  conf.d/flaskapp.conf 

Flask server configuration was default port (5000), it should be running on port 50001 so i fixed the con figuration in app.py added 
app.run(host='0.0.0.0', port=5001)
Making theses configuration fix i was able to reach the application from my local pc via http://localhost:8080

I was able to add the items to database via app.py but i wasn't able to list it then i add these lines to app.py to see the list of all the entiries in database;

 arr = []

    for item in results:
        lineItem = {}
        lineItem["n"] = item.name
        lineItem["qt"] = item.quantity
        lineItem["desc"] = item.description
        lineItem["da"] = item.date_added
        arr.append(lineItem)
        				
    return print (*arr)
    
    
    
    
    
    
    
    
  





# Insight DevOps Engineering Systems Puzzle

## Table of Contents
1. [Understanding the puzzle](README.md#understanding-the-puzzle)
2. [Introduction](README.md#introduction)
3. [Puzzle details](README.md#puzzle-details)
4. [Instructions to submit your solution](README.md#instructions-to-submit-your-solution)
5. [FAQ](README.md#faq)

# Understanding the puzzle

We highly recommend that you take a few dedicated minutes to read this README in its entirety before starting to think about potential solutions. You'll probably find it useful to review the codebase and understand the system at a high-level before attempting to find specific bugs.

# Introduction

Imagine you're on an engineering team that is building an eCommerce site where users can buy and sell items (similar to Etsy or eBay). One of the developers on your team has put together a very simple prototype for a system that writes and reads to a database. The developer is using Postgres for the backend database, the Python Flask framework as an application server, and nginx as a web server. All of this is developed with the Docker Engine, and put together with Docker Compose.

Unfortunately, the developer is new to many of these tools, and is having a number of issues. The developer needs your help debugging the system and getting it to work properly.

# Puzzle details

The codebase included in this repo is nearly functional, but has a few bugs that are preventing it from working properly. The goal of this puzzle is to find these bugs and fix them. To do this, you'll have to familiarize yourself with the various technologies (Docker, nginx, Flask, and Postgres). You definitely don't have to be an expert on these, but you should know them well enough to understand what the problem is.

Assuming you have the Docker Engine and Docker Compose already installed, the developer said that the steps for running the system is to open a terminal, `cd` into this repo, and then enter these two commands:

    docker-compose up -d db
    docker-compose run --rm flaskapp /bin/bash -c "cd /opt/services/flaskapp/src && python -c  'import database; database.init_db()'"

This "bootstraps" the PostgreSQL database with the correct tables. After that you can run the whole system with:

    docker-compose up -d

At that point, the web application should be visible by going to `localhost:8080` in a web browser. 

Once you've corrected the bugs and have the basic features working, commit the functional codebase to a new repo following the instructions below. As you debug the system, you should keep track of your thought process and what steps you took to solve the puzzle.

## Instructions to submit your solution
* Don't schedule your interview until you've worked on the puzzle 
* To submit your entry please use the link you received in your systems puzzle invitation
* You will only be able to submit through the link one time
* For security, we will not open solutions submitted via files
* Use the submission box to enter the link to your GitHub repo or Bitbucket ONLY
* Link to the specific repo for this project, not your general profile
* Put any comments in the README inside your project repo

# FAQ

Here are some common questions we've received. If you have additional questions, please email us at `devops@insightdata.com` and we'll answer your questions as quickly as we can (during PST business hours), and update this FAQ. Again, only contact us after you have read through the Readme and FAQ one more time and cannot find the answer to your question.

### Which Github link should I submit?
You should submit the URL for the top-level root of your repository. For example, this repo would be submitted by copying the URL `https://github.com/InsightDataScience/systems-puzzle` into the appropriate field on the application. **Do NOT try to submit your coding puzzle using a pull request**, which would make your source code publicly available.

### Do I need a private Github repo?
No, you may use a public repo, there is no need to purchase a private repo. You may also submit a link to a Bitbucket repo if you prefer.

### What sort of system should I use to run my program (Windows, Linux, Mac)?
You should use Docker to run and test your solution, which should work on any operating system. If you're unfamiliar with Docker, we recommend attending one of our Online Tech Talks on Docker, which you should've received information about in your invitation. Alternatively, there are ample free resources available on docker.com.

### How will my solution be evaluated?
While we will review your submission briefly before your interview, the main point of this puzzle is to serve as content for discussion during the interview. In the interview, we'll evaluate your problem solving and debugging skills based off how you solved this puzzle, so be sure to document your thought process.

### This eCommerce site is ugly...should I improve the design?  
No, you should focus on the functionality. Your engineering team will bring on a designer and front-end developer later in the process, so don't worry about that aspect in this puzzle. If you have extra time, it would be far better to focus on aspects that make the code cleaner and easier to use, like tests and refactoring.

### Should I use orchestration tools like Kubernetes?
While technologies like Kubernetes are quite powerful, they're likely overkill for the simple application in this puzzle. We recommend that you stick to Docker Compose for this puzzle.

