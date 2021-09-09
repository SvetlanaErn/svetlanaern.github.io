---
layout: post
title:  "Continuous integration"
date:   2021-09-09 20:04:59 +0200
author: SvetlanaErn
---
Continuous integration (CI) är mjukvara utveckling praktik. I denna praktik lagras koden i repository och utvecklare mergar kod ofta till main grenen. Varje merge triggar automatiskt en serie av handlingar-kontroller (pipeline), t.ex. bygga kod, köra test etc. Om ett fel uppstår får utvecklingsteam ett meddelande. Denna praktik ökar produktivitet av utvecklare och tillsammans med continuous deployment (CD) automatiserar och förenklar utvecklingsprocessen och minskar tiden från kodändringen till releaser samt förebygger situation när koden som ligger i main inte kan byggas.


# CI pipeline exempel för projekten [SpacePark](https://github.com/SvetlanaErn/spacepark-spacepark_-gruppx).

Här kommer [koden](https://github.com/SvetlanaErn/spacepark-spacepark_-gruppx/blob/main/.github/workflows/basic.yaml) för pipeline:


```
name: build_and_test         # namn på pipeline
on: [push]                   # händelse när pipeline aktiveras 
jobs:                        # denna pipeline består av ett jobb
  my_first_job:              # jobb_id
    name: Build and test job # namn på jobbet
    runs-on: windows-latest  # operativsystem


    steps:                      
    # checks out repository and downloads it to the runner
    - uses: actions/checkout@v2  
    # setup dotnet
    - uses: actions/setup-dotnet@v1 
      with:
        dotnet-version: 5.0.x  
    # build   
    - name: build
      run: dotnet build ./Source/  
    # kör tests
    - name: run tests
      run: dotnet test ./Source/SpaceTests/ 
```


Pipelinen ser ut så här:


![steps](https://user-images.githubusercontent.com/70198472/132754186-a67a5e5a-608a-4898-b1cb-4b992b316ecd.jpeg)


* Set up – container startar, laddar ner actions 
* Run action/checkout@v2 – laddar ner branch med koden
* Run action/setup-dotnet@v1 – installerar .Net SDK
* Build – bygger alla projekt som går in i solution
* Run tests
* Post Run action/checkout@v2 – clean up
* Complete job – avbryter alla processer och rapporterar resultat.




*Källor:*


* [Continuous Integration (CI) Explained]( https://semaphoreci.com/continuous-integration)
* [Continuous Integration](https://explainagile.com/agile/xp-extreme-programming/practices/continuous-integration/)
* [Understanding GitHub Actions](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions)
* [Workflow syntax for GitHub Actions](https://docs.github.com/en/actions/reference/workflow-syntax-for-github-actions)


