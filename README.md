# ELECTION-MANAGER
A cryptographically secure portal to manage online elections, created as a part of codefundo++ 2019.
Getting Started

Follow these instructions to get a copy of the application on your computer :

Prerequisites

    Go
    Install Go (go1.11+) by following the instructions given on the Go installation page.
    MongoDB
    Install MongoDB by following the instructions given on the MongoDB installation page.
    sjcl
        You can either compile it yourself from the source code by following the instructions given here. [Note that if you are compiling yourself, you need to do ./configure --with-ecc before executing the make command.]
        Or you can download a pre-compiled version (with the ecc feature) from here.
        You will need the sjcl.js file later.

Installing

cd $GOPATH/src
go get github.com/sareenpriyam/ELECTION-MANAGER/cmd/ELECTION-MANAGER

go get github.com/sareenpriyam/ELECTION-MANAGER/cmd/initialize-database

cd github.com/sareenpriyam/ELECTION-MANAGER
go install ./...

This creates two executables in $GOPATH/bin :

    ELECTION-MANAGER
    initialize-database

Also, copy the sjcl.js file into assets/js/.

Configuration

You need to create a System Admin and populate the database with details of the students. You also need to create a configuration file which will export important values to the environment variables.

Initializing the database

    Start MongoDB.
    mongod
    Connect to the database in a new terminal.
    mongo
    Create a new Election Database.
    > use ElectionDb
    Create a system admin for this database.
    > db.createUser({user: "ElectionAdmin", pwd: "password", roles: ["readWrite", "dbAdmin"]})
    Create a new collection called students.
    > db.createCollection("citizens")
    Fill this collection with the list of citizens (voters, candidates and the CEO). Each citizen is a separate document with three fields (all are strings) :
        voterid - it contains unique voterid of citizen
        email - Just the username of the email address of the citizen (e.g. this field contains dsjohal if the email ID of the student is dsjohal@iitk.ac.in. (The remaining part of the email ID will be specified in the configuration file).
        name - Full name of the citizen.

Creating the election data CSV file

Follow the template given in configurationTemplates/electionData.csv to create a CSV file containing the details of the posts for which the elections are being held, the voters eligible to vote for each post, and the candidates contesting the elections for various posts.

    You can have any number of posts. All the details of one post are in a single row.
    The first value is the name of the post.
    The second value is the regular expression that the students' roll number must match with, to check if they are eligible to vote for this post.
    The remaining values in each row are the details of the candidates. You need to fill two values for each candidate :
        The first value is the voter id of the candidate.
        The second value is the link to the manifesto of the candidate.
        All these values occur alternately on the same row. Thus, the format for each row is:<Name Of The Post>,<Regular expression for voters>,<voter id of candidate 1>,<Manifesto of candidate 1>,<voter id of candidate 2>,<Manifesto of candidate 2>,<voter id of candidate 3>,<Manifesto of candidate 3>,...

Creating the configuration file

Follow the template given in configurationTemplates/configuration.sh and replace the default values with your own values.
Deployment

Follow these instructions to host an election using this application :

    Start MongoDB in auth mode.
    mongod --auth
    Open a new terminal and load values from the configuration file into the environment variables.
    source /path/to/configuration/file/configuration.sh
    Change to the directory where the application's executables were installed.
    cd $GOPATH/bin
    Initialize the database with the details of the posts and the candidates.
    ./initialize-database
    Start the application.
    ./ELECTION-MANAGER
