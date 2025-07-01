---
source: https://github.com/MuhanedAhmed/Courses-Notes/blob/main/Notes%20on%20Git.pdf
description: https://notes.kodekloud.com/docs/GitHub-Actions/Introduction/Course-Introduction
---
 



### multiple jobs
```yml
name: Generate ASCII Artwork

on: push

jobs:

    build_job_1:
        runs-on: ubuntu-latast
        steps:
        -  name: install Cowsay program
           run: sudo apt-get install cowsay -y
        -   name: Execute Cowsay CMD
            run: cowsay -f dragon "Run for cover, I'm a DRAGON ... RAWR"  >>  dragon.txt
        -   name: Sleep for 30 secands
            run: sleep 30

    test_job_2:
        runs-on: ubuntu-latast
        steps:
        -   name: Sleep for 10 secands
            run: sleep 10
        -   name: Test File Exists
            run: grep -i | "dragon" dragon.txt

    deploy_job_3:
        runs-on: ubuntu-latast
        steps:
        -   name: Read File
            run: cat dragon.txt
        -   name: Deploy
            run: echo Deploying .... .. .
```
هنا لو محتاج ان كل job مبتعتمدش على التانية 
اما لو محتاج ان كل job بتعتمد على القبلها بستخدم الطريقة دى

###  multiple jobs in Sequence
```yml
name: Generate ASCII Artwork

on: push

jobs:

    build_job_1:
        runs-on: ubuntu-latast
        steps:
        -  name: install Cowsay program
           run: sudo apt-get install cowsay -y
        -   name: Execute Cowsay CMD
            run: cowsay -f dragon "Run for cover, I'm a DRAGON ... RAWR"  >>  dragon.txt
        -   name: Sleep for 30 secands
            run: sleep 30

    test_job_2:
        needs: build_job_1
        runs-on: ubuntu-latast
        steps:
        -   name: Sleep for 10 secands
            run: sleep 10
        -   name: Test File Exists
            run: grep -i | "dragon" dragon.txt

    deploy_job_3:
        needs: [test_job_2,build_job_1]
        runs-on: ubuntu-latast
        steps:
        -   name: Read File
            run: cat dragon.txt
        -   name: Deploy
            run: echo Deploying .... .. .
```
هنا هنواجه مشكله ان كل job الملفات بتبا جواها ومش هقدر احصل على الملف لنها هتخلص و هتفصل 
هنا هنحتاج حاجه زياده انانا نرفع الملف بشكل مواقت لكى يتم job2,3 يقدرو asses الملف ده عن طرق upload-artifact , upload-artifact

```yml
name: Generate ASCII Artwork

on: push

jobs:
    build_job_1:
        runs-on: ubuntu-latast
        steps:
        -  name: install Cowsay program
           run: sudo apt-get install cowsay -y
        -   name: Execute Cowsay CMD
            run: cowsay -f dragon "Run for cover, I'm a DRAGON ... RAWR"  >>  dragon.txt
        - uses: actions/upload-artifact@v4
          with:
              name: dragon-text-file
              path: dragon.txt
    test_job_2:
        needs: build_job_1
        runs-on: ubuntu-latast
        steps:
        - uses: actions/download-artifact@v4
          with:
              name: dragon-text-file
        -   name: Test File Exists
            run: grep -i | "dragon" dragon.txt

    deploy_job_3:
        needs: test_job_2
        runs-on: ubuntu-latast
        steps:
        - uses: actions/download-artifact@v4
          with:
              name: dragon-text-file
        -   name: Read File
            run: cat dragon.txt
        -   name: Deploy
            run: echo Deploying .... .. .

```

علشان اقدر اتحكم فى زمن الملف عن طريق settings --> Action --> General و بعد كده اقدر اتحكم فى زمن وجود الملف على git hub

### Variables at different levels
يمكن تعريف المتغيرات عن طريق jobs  او  steps او عن طريق workflow
```yml

name: Generate ASCII Artwork

on: push

jobs:
	docker:
		runs-on: ubuntu-latest
		steps :
		- name: Docker Build
		env :
CONTAINER REGISTRY: docker.io
DOCKER USERNAME: siddharthl
IMAGE NAME: github-actions-nginx
run: echo docker build -t USERNN•1E/$1hAGE NM?. latest
name: Docker Login
env :
DOCKER PASSIORD•. s3cUrePassword
DOCKER USERNAME: siddharthl
run: echo docker login
- -username=$DOCKER USERNAME
name: Docker publish
CONTAINER REGISTRY: docker.io
DOCKER USERNME-: siddharthl
It•1AGE NAME: github-actions-nginx
--password=$DOCKER PASSWRD
run: echo docker push REGISTRY/$CX)CKER USERNAME/$IMAGE NME:1atest



or
name: Generate ASCII Artwork

on: push

jobs:
	docker:

	env :
		CONTAINER REGISTRY: docker.io
		DOCKER USERNAME: siddharthl
		IMAGE NAME: github-actions-nginx
	runs-on: ubuntu-latest
	steps :
		- name: Docker Build
	



```


```yaml
run: echo "${{ env.Greeting }} ${{ env.First_Name }}. Today is ${{ env.DAY_OF_WEEK }}!"

run: echo "${{ vars.OVERRIDE_VAR }}"
run: echo "${{secrets.DOCKER PASSWORD}}"

```



### Triggering a workflow
https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/events-that-trigger-workflows#push
https://crontab.guru/

cron
```yml
name: Generate ASCII Artwork


on:
  schedule:
    - cron: "15 4,5 * * *"

jobs:
    build_job_1:
        runs-on: ubuntu-latast
        steps:
        -  name: install Cowsay program
           run: sudo apt-get install cowsay -y
        -   name: Execute Cowsay CMD
            run: cowsay -f dragon "Run for cover, I'm a DRAGON ... RAWR"  >>  dragon.txt
        - uses: actions/upload-artifact@v4
          with:
              name: dragon-text-file
              path: dragon.txt
    test_job_2:
        needs: build_job_1
        runs-on: ubuntu-latast
        steps:
        - uses: actions/download-artifact@v4
          with:
              name: dragon-text-file
        -   name: Test File Exists
            run: grep -i | "dragon" dragon.txt

    deploy_job_3:
        needs: test_job_2
        runs-on: ubuntu-latast
        steps:
        - uses: actions/download-artifact@v4
          with:
              name: dragon-text-file
        -   name: Read File
            run: cat dragon.txt
        -   name: Deploy
            run: echo Deploying .... .. .
```

```
name: Generate ASCII Artwork



on: workflow_dispatch


jobs:
    build_job_1:
        runs-on: ubuntu-latast
        steps:
        -  name: install Cowsay program
           run: sudo apt-get install cowsay -y
        -   name: Execute Cowsay CMD
            run: cowsay -f dragon "Run for cover, I'm a DRAGON ... RAWR"  >>  dragon.txt
        - uses: actions/upload-artifact@v4
          with:
              name: dragon-text-file
              path: dragon.txt
    test_job_2:
        needs: build_job_1
        runs-on: ubuntu-latast
        steps:
        - uses: actions/download-artifact@v4
          with:
              name: dragon-text-file
        -   name: Test File Exists
            run: grep -i | "dragon" dragon.txt

    deploy_job_3:
        needs: test_job_2
        runs-on: ubuntu-latast
        steps:
        - uses: actions/download-artifact@v4
          with:
              name: dragon-text-file
        -   name: Read File
            run: cat dragon.txt
        -   name: Deploy
            run: echo Deploying .... .. .
```

### Job concurrency
لو عندى workflow شغال و عصل تعديل من شخص ما فى الفريق او يوجد بيه خطى يمكن عمل concurrency وهيا وظيفتها ان اكنسل job  او اعمل ليها انتظار لحد ما اكمل workflow الجديده 
 cancel-in-progress: true    cancel
  cancel-in-progress: false   wait

```
name: Varible and Secrets

on: workflow_dispatch

jobs:
    docker:
        runs-on: ubuntu-latast
        steps:
        -  name: Docker Build
           run: echo docker build -t ${{ env.CONTAINER_REGISTRY }}/${{ vars.DOCKER_USERNAME }}/$IMAGE_NAME:1atest

        -   name: Docker Login
            run: echo docker login --username=${{ vars.DOCKER_USERNAME }} --password=${{ secrets.DOCKER_PASSWORD }}
        -   name: Docker Publish
            run: echo docker push $env.CONTAINER_REGISTRY/${{ vars.DOCKER_USERNAME }}/$IMAGE_NAME:1ates

    deploy:
        needs: docker
        concurrency:
            group: production-deployment
            cancel-in-progress: true

        runs-on: ubuntu-latast
        steps:
        -  name: Docker Run
           run: |
             echo docker run -d -p 8080:80 $env.CONTAINER_REGISTRY/${{ vars.DOCKER_USERNAME }}/$IMAGE_NAME:1ates
             sleep 600s
```

### Timeout for Jobs and Steps

يمكن تعين وقت معين لكل Steps او Jobs كلها لتفادى الى خطى يعصل ممكن يادى لزياده الوقت او وجود خطى فى الكود او الحفاظ على Quota


```yml

name: Varible and Secrets

on: workflow_dispatch

jobs:
    docker:
        runs-on: ubuntu-latast
        steps:
        -  name: Docker Build
           run: echo docker build -t ${{ env.CONTAINER_REGISTRY }}/${{ vars.DOCKER_USERNAME }}/$IMAGE_NAME:1atest

        -   name: Docker Login
            run: echo docker login --username=${{ vars.DOCKER_USERNAME }} --password=${{ secrets.DOCKER_PASSWORD }}
        -   name: Docker Publish
            run: echo docker push $env.CONTAINER_REGISTRY/${{ vars.DOCKER_USERNAME }}/$IMAGE_NAME:1ates

    deploy:
        # timeout-minutes: 1  #{}  or
        needs: docker
        concurrency:
            group: production-deployment
            cancel-in-progress: true

        runs-on: ubuntu-latast
        steps:
        -  name: Docker Run
           timeout-minutes: 1  #{}
           run: |
             echo docker run -d -p 8080:80 $env.CONTAINER_REGISTRY/${{ vars.DOCKER_USERNAME }}/$IMAGE_NAME:1ates
             sleep 600s




```

### matrix for your jobs
هنا لو عايز اختبر على اكثر من OS مختلف فاهتر اكرر المكتوب على كل OS موعلشان حلها استخدمنا matrix
```yml
name: Configuration matrix
on:
 push:
 workflow_dispatch:
jobs:
   deploy:
    strategy:
      matrix:
        OS: [ubuntu-latest, ubuntu-20.04, windows-latest]
        images: [hello-word, alpine]
    runs-on: ${{matrix.OS}}
    steps:
     - name: Echo Docker Details
       run: docker info
     - name: Run Images on ${{matrix.OS}}
       run: docker run ${{matrix.images}}
```


```
name: Configuration matrix
on:
 push:
 workflow_dispatch:
jobs:
   deploy:
    strategy:
      fail-fast: false
      max-parallel: 2
      matrix:
        OS: [ubuntu-latest, ubuntu-20.04, windows-latest]
        images: [hello-word, alpine]
        exclude:
          - images: alpine
            OS: windows-latest
        include:
          - images: amd64/alpine
            OS: windows-latest
    runs-on: ${{matrix.OS}}
    steps:
     - name: Echo Docker Details
       run: docker info
     - name: Run Images on ${{matrix.OS}}
       run: docker run ${{matrix.images}}
```

### conext
https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/accessing-contextual-information-about-workflow-runs#runner%3Context
```
name: Context testing
on: push
jobs:
  dump_contexts_to_log:
    runs-on: ubuntu-latest
    steps:
      - name: Dump GitHub context
        env:
          GITHUB_CONTEXT: ${{ toJson(github) }}
        run: echo "$GITHUB_CONTEXT"
      - name: Dump job context
        env:
          JOB_CONTEXT: ${{ toJson(job) }}
        run: echo "$JOB_CONTEXT"
      - name: Dump steps context
        env:
          STEPS_CONTEXT: ${{ toJson(steps) }}
        run: echo "$STEPS_CONTEXT"
      - name: Dump runner context
        env:
          RUNNER_CONTEXT: ${{ toJson(runner) }}
        run: echo "$RUNNER_CONTEXT"
      - name: Dump strategy context
        env:
          STRATEGY_CONTEXT: ${{ toJson(strategy) }}
        run: echo "$STRATEGY_CONTEXT"
      - name: Dump matrix context
        env:
          SECRET_CONTEXT: ${{ toJson(secrets) }}
        run: echo "$SECRET_CONTEXT"
```

### if expression in Jobs
ممكن احتاجها زى المثال ده لو انا عايز اطبق حد معين على bransh معين 
مثال لو فى مرحلة test اكيد مش محتاج اعمل deploy  على production 
فى المثال بقواله اعمل deploy  فى حالة انا على bransh main لو هو مش على المكتوب هيعمل skip  بس بعد عمل pull  اى او mirage هيكمل الجزء التانى التعمله skip
```yml
name: Varible and Secrets
on: workflow_dispatch
jobs:
    docker:
        timeout-minutes: 1
        runs-on: ubuntu-latast
        steps:
        -  name: Docker Build
           run: echo docker build -t ${{ env.CONTAINER_REGISTRY }}/${{ vars.DOCKER_USERNAME }}/$IMAGE_NAME:1atest
        -   name: Docker Login
            run: echo docker login --username=${{ vars.DOCKER_USERNAME }} --password=${{ secrets.DOCKER_PASSWORD }}
        -   name: Docker Publish
            run: echo docker push $env.CONTAINER_REGISTRY/${{ vars.DOCKER_USERNAME }}/$IMAGE_NAME:1ates

    deploy:
        if: github.ref == 'refs/heads/main'
        needs: docker
        concurrency:
            group: production-deployment
            cancel-in-progress: true
        runs-on: ubuntu-latast
        steps:
        -  name: Docker Run
           timeout-minutes: 1
           run: |
             echo docker run -d -p 8080:80 $env.CONTAINER_REGISTRY/${{ vars.DOCKER_USERNAME }}/$IMAGE_NAME:1ates
             sleep 600s
```

### Workflow Event Filters and


```yml
name: WorkFlow filters and activities
on:
    workflow_dispatch:
    push:
        # branches-ignore:
        #     - feature/*      # feature/add-music , feature/updateImage
        #     - test/**        # test/ui/index ,test/checkout/pyment
        branches:
            - main
            - '!feature/*'   # ignore pushing to any bransh name starting with feature using !
    pull_request:
        types:
            - opened
            - closed
        paths-ignore:
            - README.md       # workflow will only run when a pull request that includes a change on any file other than README.md
        branches:
            - main            # configures your workflow to only run on pull requests that target specific branches.

jobs:
    hello:
        runs-on: ubuntu-latest
        steps:
            - run: echo this workflow/job/step is executed for type -${{github.event_name}}
```


### Skipping workflow runs
https://docs.github.com/en/actions/managing-workflow-runs-and-deployments/managing-workflow-runs/skipping-workflow-runs
- `[skip ci]`
- `[ci skip]`
- `[no ci]`
- `[skip actions]`
- `[actions skip]`


دى فى حاله انى مش عايز اطبق اى workflow  بكتب فى comment  حاجه من دى 



## Continuous integration

### NodeJS Application Overview

node -v 
npm -v
npm install   # install node_modules
npm test           test.js
npm start      # start app


### Using continue on error


```yml

name: Solar System Workflow
on:
    workflow_dispatch:
    push:    
        branches:
            - main
            - 'feature/*'

env:
  MONGO_URI: 'mongodb://${{ secrets.USERNAME}}:${{ vars.PASSWORD }}@cluster0.7x6u1mv.mongodb.net/superData'
  MONGO_USERNAME: ${{ vars.PASSWORD }}
  MONGO_PASSWORD: ${{ secrets.USERNAME}}
jobs:
    unit-testing:
        name: Unit Testing
        strategy:
          matrix:
            nodjs_version: [18, 19, 20]
            operating_system: [ubuntu-latest, macos-latest]
        runs-on: ${{ matrix.operating_system }}
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4
            - name: Setup NodeJs Version - ${{ matrix.nodjs_version }}
              uses: actions/setup-node@v3
              with:
                node-version: ${{ matrix.nodjs_version }}

            - name: Install Dependencies
              run: npm install
  
            - name: Unit Testing
              run: npm test

            - name: Archive Test Result
              uses: actions/upload-artifact@v4
              with:
                name: Mocha-Test-Resalt
                path: test-Resalt.xml

    code-coverage:
        name: Code Coverage
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4

            - name: Setup NodeJs Version - 18
              uses: actions/setup-node@v3
              with:
                node-version: 18

            - name: Install Dependencies
              run: npm install

            - name: Check code Coverage
              continue-on-error: true     # لعمل اسكب لو حصل ايرور فى الخطوه دى
              run: npm Coverage
            
            - name: Archive Test Result
              uses: actions/upload-artifact@v4
              with:
                name: Code Coverage Result
                path: Covrerage
                retention-days: 5   # لمسح الملفات المخزنة بعد فتره معينه
```


###  Using if expressions with Step contexts
https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/evaluate-expressions-in-workflows-and-actions

هنا ممكن اسنخدم اكثر من طريقة مثل 
failure()
always()
من الافضل عدم استخدام failure() لان لو الكود شغال هيعمل طخطى للجزء ده اى فى حاله failure سوف يتم تشغيل هذا اما فى حالة ناجحة سوف يتخطاها

```yml

name: Solar System Workflow
on:
    workflow_dispatch:
    push:    
        branches:
            - main
            - 'feature/*'

env:
  MONGO_URI: 'mongodb://${{ secrets.USERNAME}}:${{ vars.PASSWORD }}@cluster0.7x6u1mv.mongodb.net/superData'
  MONGO_USERNAME: ${{ vars.PASSWORD }}
  MONGO_PASSWORD: ${{ secrets.USERNAME}}
jobs:
    unit-testing:
        name: Unit Testing
        strategy:
          matrix:
            nodjs_version: [18, 19, 20]
            operating_system: [ubuntu-latest, macos-latest]
        runs-on: ${{ matrix.operating_system }}
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4
            - name: Setup NodeJs Version - ${{ matrix.nodjs_version }}
              uses: actions/setup-node@v3
              with:
                node-version: ${{ matrix.nodjs_version }}
            - name: Install Dependencies
              run: npm install
            - name: Unit Testing
              id: nodejs-unit-testing-step
              run: npm test
            - name: Archive Test Result
              if: failure() && (steps.nodejs-unit-testing-step == 'failure' || steps.nodejs-unit-testing-step == 'success')

              uses: actions/upload-artifact@v4
              with:
                name: Mocha-Test-Resalt
                path: test-Resalt.xml


    code-coverage:
        name: Code Coverage
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4
            - name: Setup NodeJs Version - 18
              uses: actions/setup-node@v3
              with:
                node-version: 18

            - name: Install Dependencies
              run: npm install  
            - name: Check code Coverage
              continue-on-error: true
              run: npm Coverage
            - name: Archive Test Result
              uses: actions/upload-artifact@v4
              with:
                name: Code Coverage Result
                path: Covrerage
                retention-days: 5
```


### Dependencies Node Cache
https://github.com/marketplace/actions/cache
تستخدم لو بعمل run على طوال مش محتاج انزال package كل شويه فابحفظها علشان اوفر وقت عند المره القادمه لو حصل اى تغيير فى الباكدج بيعمل updata بيسمح القديم ويعمل غيرها

```yml

name: Solar System Workflow
on:
    workflow_dispatch:
    push:    
        branches:
            - main
            - 'feature/*'
env:

  MONGO_URI: 'mongodb://${{ secrets.USERNAME}}:${{ vars.PASSWORD }}@cluster0.7x6u1mv.mongodb.net/superData'
  MONGO_USERNAME: ${{ vars.PASSWORD }}
  MONGO_PASSWORD: ${{ secrets.USERNAME}}
jobs:
    unit-testing:
        name: Unit Testing
        strategy:
          matrix:
            nodjs_version: [18, 20]
            operating_system: [ubuntu-latest, macos-latest]
        runs-on: ${{ matrix.operating_system }}
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4
            - name: Setup NodeJs Version - ${{ matrix.nodjs_version }}
              uses: actions/setup-node@v3
              with:
                node-version: ${{ matrix.nodjs_version }}

            - name: Cache NPM dependencies
              uses: actions/cache@v4   #cach
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}

            - name: Install Dependencies
              run: npm install
            - name: Unit Testing
              id: nodejs-unit-testing-step
              run: npm test
            - name: Archive Test Result
              if: failure() && (steps.nodejs-unit-testing-step == 'failure' || steps.nodejs-unit-testing-step == 'success')

              uses: actions/upload-artifact@v4
              with:
                name: Mocha-Test-Resalt
                path: test-Resalt.xml
    code-coverage:

        name: Code Coverage
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4

            - name: Setup NodeJs Version - 18
              uses: actions/setup-node@v3
              with:
                node-version: 18

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}

            - name: Install Dependencies
              run: npm install
            - name: Check code Coverage
              continue-on-error: true
              run: npm Coverage
            - name: Archive Test Result
              uses: actions/upload-artifact@v4
              with:
                name: Code Coverage Result
                path: Covrerage
                retention-days: 5
```


### build and Push image to image registry

يتم توليد token الخاص ب GitHub تلقائى وبعد الانتهاء من workflow يكون expire ولاكن يجب ان تضيف permission packages: write لان القيمة read default 
        
```yml
name: Solar System Workflow
on:
    workflow_dispatch:
    push:    
        branches:
            - main
            - 'feature/*'

env:
  MONGO_URI: 'mongodb://${{ secrets.USERNAME}}:${{ vars.PASSWORD }}@cluster0.7x6u1mv.mongodb.net/superData'
  MONGO_USERNAME: ${{ vars.PASSWORD }}
  MONGO_PASSWORD: ${{ secrets.USERNAME}}

jobs:
    unit-testing:
        name: Unit Testing
        strategy:
          matrix:
            nodjs_version: [18, 20]
            operating_system: [ubuntu-latest, macos-latest]
        runs-on: ${{ matrix.operating_system }}

        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4

            - name: Setup NodeJs Version - ${{ matrix.nodjs_version }}
              uses: actions/setup-node@v3
              with:
                node-version: ${{ matrix.nodjs_version }}

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}

            - name: Install Dependencies
              run: npm install

            - name: Unit Testing
              id: nodejs-unit-testing-step
              run: npm test

            - name: Archive Test Result
              if: failure() && (steps.nodejs-unit-testing-step == 'failure' || steps.nodejs-unit-testing-step == 'success')
              uses: actions/upload-artifact@v4
              with:
                name: Mocha-Test-Resalt
                path: test-Resalt.xml
  

    code-coverage:
        name: Code Coverage
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4
            
            - name: Setup NodeJs Version - 18
              uses: actions/setup-node@v3
              with:
                node-version: 18

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}

            - name: Install Dependencies
              run: npm install

            - name: Check code Coverage
              continue-on-error: true
              run: npm Coverage

            - name: Archive Test Result
              uses: actions/upload-artifact@v4
              with:
                name: Code Coverage Result
                path: Covrerage
                retention-days: 5


    docker:
      name: Containerization
      needs: [unit-testing, code-coverage]
      permissions:
        packages: write
      runs-on: ubuntu-latest
      steps:

         - name: Checkout Repo
           uses:  actions/checkout@v4

         - name: Dockerhub Login
           uses:  docker/checkout@v4

         - name: Dockerhub Login
           uses: docker/login-action@v3
           with:
            username: ${{ vars.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}

         - name: GHCR Login
           uses: docker/login-action@v3
           with:
            registry: ghcr.io
            username: ${{ github.repository_owner }}
            password: ${{ secrets.GITHUB_TOKEN }}

         - name: Build and push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: false
            tags: ${{ vars.DOCKERHUB_USERNAME }}/Solar-System:${{ github.sha }}

         - name: Docker Image testing
           run: |
             docker images
             docker run --name solar-system-app -d \
             -p 3000:3000 \
             -e MONGO_URI: $MONGO_URI \
             -e MONGO_PASSWORD: $MONGO_PASSWORD \
             -e MONGO_USERNAME: $MONGO_USERNAME \
             ${{ vars.DOCKERHUB_USERNAME }}/Solar-System:${{ github.sha }}
             export IP=$(docker inspact -f '{{range .Networksetting.Networks}}{{.IPAdress}}{{end}}' solar-system-app)
             echo $Ip
             echo Testing Iamge URL using wget
             wget -q -O -127.0.0.1:3000/live | grep live

         - name: Container Registry push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: true
            tags: |
              ${{ vars.DOCKERHUB_USERNAME }}/Solar-System:${{ github.sha }}
              ghcr.io/${{ github.repository_owner }}/Solar-System:${{ github.sha }}
```



### Run Unit Testing Job using a Service

```yml

name: Solar System Workflow
on:
    workflow_dispatch:
    push:    
        branches:
            - main
            - 'feature/*'
env:

  MONGO_URI: 'mongodb://${{ secrets.USERNAME}}:${{ secrets.PASSWORD }}@cluster0.7x6u1mv.mongodb.net/superData'
  MONGO_USERNAME: ${{ secrets.USERNAME }}
  MONGO_PASSWORD: ${{ secrets.PASSWORD}}

jobs:
    unit-testing:
        name: Unit Testing
        services:
          mongo-db:
            image: siddharth67/mongo-db:non-prod
            ports:
              - 27017:27017
        env:
             MONGO_URI: 'mongodb://localhost:27017/superData'
             MONGO_USERNAME: non-prod-user
             MONGO_PASSWORD: non-prod-password
        strategy:
          matrix:
            nodjs_version: [18, 20]
            operating_system: [ubuntu-latest]
        runs-on: ${{ matrix.operating_system }}
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4

            - name: Setup NodeJs Version - ${{ matrix.nodjs_version }}
              uses: actions/setup-node@v3
              with:
                node-version: ${{ matrix.nodjs_version }}

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}

            - name: Install Dependencies
              run: npm install
           
            - name: Unit Testing
              id: nodejs-unit-testing-step
              run: npm test

            - name: Archive Test Result
              if: failure() && (steps.nodejs-unit-testing-step == 'failure' || steps.nodejs-unit-testing-step == 'success')
              uses: actions/upload-artifact@v4
              with:
                name: Mocha-Test-Resalt
                path: test-Resalt.xml
  

    code-coverage:
        name: Code Coverage
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4

            - name: Setup NodeJs Version - 18
              uses: actions/setup-node@v3
              with:
                node-version: 18

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}


            - name: Install Dependencies
              run: npm install

            - name: Check code Coverage
              continue-on-error: true
              run: npm Coverage
            - name: Archive Test Result
              uses: actions/upload-artifact@v4
              with:
                name: Code Coverage Result
                path: Covrerage
                retention-days: 5

    docker:
      name: Containerization
      needs: [unit-testing, code-coverage]
      permissions:
        packages: write
      runs-on: ubuntu-latest
      steps:
         - name: Checkout Repo
           uses:  actions/checkout@v4

         - name: Dockerhub Login
           uses: docker/login-action@v3
           with:
            username: ${{ vars.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}

         - name: GHCR Login
           uses: docker/login-action@v3
           with:
            registry: ghcr.io
            username: ${{ github.repository_owner }}
            password: ${{ secrets.GITHUB_TOKEN }}

         - name: Build and push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: false
            tags: ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}


         - name: Docker Image testing
           run: |
             docker images
             docker run --name solar-system-app -d \
             -p 3000:3000 \
             -e MONGO_URI:$MONGO_URI \
             -e MONGO_PASSWORD:$MONGO_PASSWORD \
             -e MONGO_USERNAME:$MONGO_USERNAME \
             ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
             export IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' solar-system-app)
             echo $IP
             echo Testing Iamge URL using wget
             wget -q -O - http://127.0.0.1:3000/live | grep live

         - name: Container Registry push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: true
            tags: |
              ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
              ghcr.io/m0-3del/solar-system:${{ github.sha }}


```



###  Run Code Coverage Job using a Container and Service

```yml
name: Solar System Workflow
on:
    workflow_dispatch:
    push:    
        branches:
            - main
            - 'feature/*'

env:
  MONGO_URI: 'mongodb://${{ secrets.USERNAME}}:${{ secrets.PASSWORD }}@cluster0.7x6u1mv.mongodb.net/superData'
  MONGO_USERNAME: ${{ secrets.USERNAME }}
  MONGO_PASSWORD: ${{ secrets.PASSWORD}}
jobs:
    unit-testing:
        name: Unit Testing
        services:
          mongo-db:
            image: siddharth67/mongo-db:non-prod
            ports:
              - 27017:27017
        env:
             MONGO_URI: 'mongodb://localhost:27017/superData'
             MONGO_USERNAME: non-prod-user
             MONGO_PASSWORD: non-prod-password
        strategy:
          matrix:
            nodjs_version: [18, 20]
            operating_system: [ubuntu-latest]
        runs-on: ${{ matrix.operating_system }}

        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4

            - name: Setup NodeJs Version - ${{ matrix.nodjs_version }}
              uses: actions/setup-node@v3
              with:
                node-version: ${{ matrix.nodjs_version }}

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}


            - name: Install Dependencies
              run: npm install

            - name: Unit Testing
              id: nodejs-unit-testing-step
              run: npm test

            - name: Archive Test Result
              if: failure() && (steps.nodejs-unit-testing-step == 'failure' || steps.nodejs-unit-testing-step == 'success')

              uses: actions/upload-artifact@v4
              with:
                name: Mocha-Test-Resalt
                path: test-Resalt.xml

    code-coverage:
        name: Code Coverage
        container:
          image: node:18
        services:
          mongo-db:
            image: siddharth67/mongo-db:non-prod
            options:
              --name mongo
        env:
             MONGO_URI: 'mongodb://mongo:27017/superData'
             MONGO_USERNAME: non-prod-user
             MONGO_PASSWORD: non-prod-password
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4

            # - name: Setup NodeJs Version - 18
            #   uses: actions/setup-node@v3
            #   with:
            #     node-version: 18

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}


            - name: Install Dependencies
              run: npm install

            - name: Check code Coverage
              continue-on-error: true
              run: npm Coverage

            - name: Archive Test Result
              uses: actions/upload-artifact@v4
              with:
                name: Code Coverage Result
                path: Covrerage
                retention-days: 5

    docker:
      name: Containerization
      needs: [unit-testing, code-coverage]
      permissions:
        packages: write
      runs-on: ubuntu-latest
      steps:
         - name: Checkout Repo
           uses:  actions/checkout@v4

         - name: Dockerhub Login
           uses: docker/login-action@v3
           with:
            username: ${{ vars.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}
         - name: GHCR Login
           uses: docker/login-action@v3
           with:
            registry: ghcr.io
            username: ${{ github.repository_owner }}
            password: ${{ secrets.GITHUB_TOKEN }}

         - name: Build and push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: false
            tags: ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}

         - name: Docker Image testing
           run: |
             docker images
             docker run --name solar-system-app -d \
             -p 3000:3000 \
             -e MONGO_URI:$MONGO_URI \
             -e MONGO_PASSWORD:$MONGO_PASSWORD \
             -e MONGO_USERNAME:$MONGO_USERNAME \
             ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
             export IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' solar-system-app)
             echo $IP
             echo Testing Iamge URL using wget
             wget -q -O - http://127.0.0.1:3000/live | grep live


         - name: Container Registry push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: true
            tags: |
              ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
              ghcr.io/m0-3del/solar-system:${{ github.sha }}
```


###  Replace Placeholders Tokens

لتخزين قيمه مابين 2 steps نستخدم GITHUB_ENV$  <<  

``
```yml

name: Solar System Workflow
on:
    workflow_dispatch:
    push:    
        branches:
            - main
            - 'feature/*'
env:

  MONGO_URI: 'mongodb://${{ secrets.USERNAME}}:${{ secrets.PASSWORD }}@cluster0.7x6u1mv.mongodb.net/superData'
  MONGO_USERNAME: ${{ secrets.USERNAME }}
  MONGO_PASSWORD: ${{ secrets.PASSWORD}}

jobs:
    unit-testing:
        name: Unit Testing
        services:
          mongo-db:
            image: siddharth67/mongo-db:non-prod
            ports:
              - 27017:27017
        env:
             MONGO_URI: 'mongodb://localhost:27017/superData'
             MONGO_USERNAME: non-prod-user
             MONGO_PASSWORD: non-prod-password
        strategy:

          matrix:

            nodjs_version: [18, 20]

            operating_system: [ubuntu-latest]

        runs-on: ${{ matrix.operating_system }}

        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4

            - name: Setup NodeJs Version - ${{ matrix.nodjs_version }}
              uses: actions/setup-node@v3
              with:
                node-version: ${{ matrix.nodjs_version }}


            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}


            - name: Install Dependencies
              run: npm install
  

            - name: Unit Testing
              id: nodejs-unit-testing-step
              run: npm test
            
            - name: Archive Test Result
              if: failure() && (steps.nodejs-unit-testing-step == 'failure' || steps.nodejs-unit-testing-step == 'success')
              uses: actions/upload-artifact@v4
              with:
                name: Mocha-Test-Resalt
                path: test-Resalt.xml
               
    code-coverage:
        name: Code Coverage
        container:
          image: node:18
        services:
          mongo-db:
            image: siddharth67/mongo-db:non-prod
            options:
              --name mongo
        env:
             MONGO_URI: 'mongodb://mongo:27017/superData'
             MONGO_USERNAME: non-prod-user
             MONGO_PASSWORD: non-prod-password
        runs-on: ubuntu-latest
        steps:

            - name: Checkout Repo
              uses:  actions/checkout@v4

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}

  
            - name: Install Dependencies
              run: npm install

            - name: Check code Coverage
              continue-on-error: true
              run: npm Coverage

            - name: Archive Test Result
              uses: actions/upload-artifact@v4
              with:
                name: Code Coverage Result
                path: Covrerage
                retention-days: 5

    docker:
      name: Containerization
      needs: [unit-testing, code-coverage]
      permissions:
        packages: write
      runs-on: ubuntu-latest
      steps:

         - name: Checkout Repo
           uses:  actions/checkout@v4

         - name: Dockerhub Login
           uses: docker/login-action@v3
           with:
            username: ${{ vars.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}

         - name: GHCR Login
           uses: docker/login-action@v3
           with:
            registry: ghcr.io
            username: ${{ github.repository_owner }}
            password: ${{ secrets.GITHUB_TOKEN }}

         - name: Build and push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: false
            tags: ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}

  
         - name: Docker Image testing
           run: |
             docker images
             docker run --name solar-system-app -d \
             -p 3000:3000 \
             -e MONGO_URI:$MONGO_URI \
             -e MONGO_PASSWORD:$MONGO_PASSWORD \
             -e MONGO_USERNAME:$MONGO_USERNAME \
             ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
             export IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' solar-system-app)
             echo $IP
             echo Testing Iamge URL using wget
             wget -q -O - http://127.0.0.1:3000/live | grep live

         - name: Container Registry push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: true
            tags: |
              ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
              ghcr.io/m0-3del/solar-system:${{ github.sha }}

    dev-deploy:
      needs: docker
      runs-on: ubuntu-latest
      steps:
         - name: Checkout Repo
           uses:  actions/checkout@v4
         - name: install kubctl CLI
           uses: azure/setup-kubectl@v4
           with:
             version: 'v1.26.0'

         - name: Set Kubconfing
           uses: azure/k8s-set-context@v4
           with:
              method: kubeconfig
              kubeconfig: ${{ secrets.GITHUB_TOKEN }}

         - name: fetch kubernetces Cluster Details
           run: |
            kubctl version --short
            echo --------------------------------
            kubctl get nodes
            
         - name: Save Nginx Ingress Controller IP as a GITHUB Environment Variable
           run: |
            echo "IGRESS_IP=$(kubectl -n ingress-nginx get services ingress-nginx-controller -o jsonpath="{.status.loadBalancer.ingress[0].ip}")" >> $GITHUB_ENV

  
         - name: Replace Token in Minfest files
           uses: cschleiden/replace-tokens@v1
           with:
             tokenPrefix: '_{_'
             tokenSuffix: '_}_'
             files: '["kubernetes/development/*.yaml"]'
           env:
             NAMESPACE: ${{ vars.NAMESPACE }}
             REPLICAS: ${{ vars.REPLICAS }}
             IMAGE: ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
             INGRESS_IP: ${{ env.IGRESS_IP }}
  
         - name: Check files
           run: |
            cat kubernetes/development/*.yaml
```


###  Setting Output for Integration testing
https://docs.github.com/en/actions/writing-workflows/choosing-what-your-workflow-does/passing-information-between-jobs?versionId=free-pro-team%40latest&productId=actions&restPage=writing-workflows

```yml
name: Solar System Workflow

on:
    workflow_dispatch:
    push:    
        branches:
            - main
            - 'feature/*'


env:
  MONGO_URI: 'mongodb://${{ secrets.USERNAME}}:${{ secrets.PASSWORD }}@cluster0.7x6u1mv.mongodb.net/superData'
  MONGO_USERNAME: ${{ secrets.USERNAME }}
  MONGO_PASSWORD: ${{ secrets.PASSWORD}}

jobs:
    unit-testing:
        name: Unit Testing
        services:
          mongo-db:
            image: siddharth67/mongo-db:non-prod
            ports:
              - 27017:27017
        env:
             MONGO_URI: 'mongodb://localhost:27017/superData'
             MONGO_USERNAME: non-prod-user
             MONGO_PASSWORD: non-prod-password
        strategy:
          matrix:
            nodjs_version: [18, 20]
            operating_system: [ubuntu-latest]
        runs-on: ${{ matrix.operating_system }}

        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4

  
            - name: Setup NodeJs Version - ${{ matrix.nodjs_version }}
              uses: actions/setup-node@v3
              with:
                node-version: ${{ matrix.nodjs_version }}

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}


            - name: Install Dependencies
              run: npm install

            - name: Unit Testing
              id: nodejs-unit-testing-step
              run: npm test

            - name: Archive Test Result
              if: failure() && (steps.nodejs-unit-testing-step == 'failure' || steps.nodejs-unit-testing-step == 'success')
              uses: actions/upload-artifact@v4
              with:
                name: Mocha-Test-Resalt
                path: test-Resalt.xml
  
    code-coverage:
        name: Code Coverage
        container:
          image: node:18
        services:
          mongo-db:
            image: siddharth67/mongo-db:non-prod
            options:
              --name mongo
        env:
             MONGO_URI: 'mongodb://mongo:27017/superData'
             MONGO_USERNAME: non-prod-user
             MONGO_PASSWORD: non-prod-password
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4
  

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}


            - name: Install Dependencies
              run: npm install

            - name: Check code Coverage
              continue-on-error: true
              run: npm Coverage

            - name: Archive Test Result
              uses: actions/upload-artifact@v4
              with:
                name: Code Coverage Result
                path: Covrerage
                retention-days: 4

    docker:
      name: Containerization
      needs: [unit-testing, code-coverage]
      permissions:
        packages: write
      runs-on: ubuntu-latest
      steps:
         - name: Checkout Repo
           uses:  actions/checkout@v4

  
         - name: Dockerhub Login
           uses: docker/login-action@v3
           with:
            username: ${{ vars.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}

         - name: GHCR Login
           uses: docker/login-action@v3
           with:
            registry: ghcr.io
            username: ${{ github.repository_owner }}
            password: ${{ secrets.GITHUB_TOKEN }}

  

         - name: Build and push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: false
            tags: ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}

  

         - name: Docker Image testing
           run: |
             docker images
             docker run --name solar-system-app -d \
             -p 3000:3000 \
             -e MONGO_URI:$MONGO_URI \
             -e MONGO_PASSWORD:$MONGO_PASSWORD \
             -e MONGO_USERNAME:$MONGO_USERNAME \
             ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
             export IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' solar-system-app)
             echo $IP
             echo Testing Iamge URL using wget
             wget -q -O - http://127.0.0.1:3000/live | grep live


         - name: Container Registry push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: true
            tags: |
              ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
              ghcr.io/m0-3del/solar-system:${{ github.sha }}

    dev-deploy:
      if: contains(github.ref, 'feature/')
      needs: docker
      environment:
        name: devlopment
        url: https://${{ steps.set-ingress-host-address.outputs.APP_INGRESS_HOST }}
      outputs:
        APP_INGRESS_URL: ${{ steps.set-ingress-host-address.outputs.APP_INGRESS_HOST }}
      runs-on: ubuntu-latest
      steps:
         - name: Checkout Repo
           uses:  actions/checkout@v4
         - name: install kubctl CLI
           uses: azure/setup-kubectl@v4
           with:
             version: 'v1.26.0'

         - name: Set Kubconfing
           uses: azure/k8s-set-context@v4
           with:
              method: kubeconfig
              kubeconfig: ${{ secrets.GITHUB_TOKEN }}

         - name: fetch kubernetces Cluster Details
           run: |
            kubctl version --short
            echo --------------------------------
            kubctl get nodes
         - name: Save Nginx Ingress Controller IP as a GITHUB Environment Variable
           run: |

            echo "IGRESS_IP=$(kubectl -n ingress-nginx get services ingress-nginx-controller -o jsonpath="{.status.loadBalancer.ingress[0].ip}")" >> $GITHUB_ENV


         - name: Replace Token in Minfest files
           uses: cschleiden/replace-tokens@v1
           with:
             tokenPrefix: '_{_'
             tokenSuffix: '_}_'
             files: '["kubernetes/development/*.yaml"]'
           env:
             NAMESPACE: ${{ vars.NAMESPACE }}
             REPLICAS: ${{ vars.REPLICAS }}
             IMAGE: ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
             INGRESS_IP: ${{ env.IGRESS_IP }}


         - name: Check files
           run: |
            cat kubernetes/development/*.yaml

         - name:  Create Mongo Sercret
           run: |
            kubectl -n ${{ vars.NAMESPACE }} create secret generic mongo-db-creds \
            --from-literal=MONGO_URI=${{ env.MONGO_URI }}
            --from-literal=MONGO_USERNAME=${{ env.MONGO_USERNAME }}
            --from-literal=MONGO_PASSWORD=${{ env.MONGO_PASSWORD }}
            --save-config \
            dry-run=Client \
            -o yaml | kubctl apply -f -

         - name: Deploy to PROD ENV
           run: |
            kubectl apply kubernetes/development/*.yaml

         - name: set App Ingress Host URL
           id: set-ingress-host-address
           run: |
            echo "APP_INGRESS_HOST=$(kubectl -n ${{ vars.NAMESPACE }} get ingress -o jsonpath="{.items[0].spac.tls[0].hosts[0]}")" >> "$GITHUB_OUTPUT"

  
    dev-integration-testing:
      if: contains(github.ref, 'feature/')
      name: Dev Integration Testing
      needs: dev-deploy
      runs-on: ubuntu-latest
      steps:
        - name: Test URL Output CURL and JQ
          env:
            URL: ${{ needs.dev-deploy.outputs.APP_INGRESS_URL }}
          run: |
           echo $URL
           echo "-------------------------"
           curl https://$URL/live -s -k | jq -r .status | grep -i live


    prod-deploy:
      if: github.ref == 'refs/heads/main'
      needs: docker
      environment:
        name: Production
        url: https://${{ steps.set-ingress-host-address.outputs.APP_INGRESS_HOST }}
      outputs:
        APP_INGRESS_URL: ${{ steps.set-ingress-host-address.outputs.APP_INGRESS_HOST }}
      runs-on: ubuntu-latest
      steps:
         - name: Checkout Repo
           uses:  actions/checkout@v4
         - name: install kubctl CLI
           uses: azure/setup-kubectl@v4
           with:
             version: 'v1.26.0'
         - name: Set Kubconfing

           uses: azure/k8s-set-context@v4
           with:
              method: kubeconfig
              kubeconfig: ${{ secrets.GITHUB_TOKEN }}

         - name: fetch kubernetces Cluster Details
           run: |
            kubctl version --short
            echo --------------------------------
            kubctl get nodes

         - name: Save Nginx Ingress Controller IP as a GITHUB Environment Variable
           run: |
            echo "IGRESS_IP=$(kubectl -n ingress-nginx get services ingress-nginx-controller -o jsonpath="{.status.loadBalancer.ingress[0].ip}")" >> $GITHUB_ENV


         - name: Replace Token in Minfest files
           uses: cschleiden/replace-tokens@v1
           with:
             tokenPrefix: '_{_'
             tokenSuffix: '_}_'
             files: '["kubernetes/production*.yaml"]'
           env:
             NAMESPACE: ${{ vars.NAMESPACE }}
             REPLICAS: ${{ vars.REPLICAS }}
             IMAGE: ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
             INGRESS_IP: ${{ env.IGRESS_IP }}

  
         - name: Check files
           run: |
            cat kubernetes/production*.yaml


         - name:  Create Mongo Sercret
           run: |
            kubectl -n ${{ vars.NAMESPACE }} create secret generic mongo-db-creds \
            --from-literal=MONGO_URI=${{ env.MONGO_URI }}
            --from-literal=MONGO_USERNAME=${{ env.MONGO_USERNAME }}
            --from-literal=MONGO_PASSWORD=${{ env.MONGO_PASSWORD }}
            --save-config \
            dry-run=Client \
            -o yaml | kubctl apply -f -

  
         - name: Deploy to PROD ENV
           run: |
            kubectl apply kubernetes/production*.yaml

  
         - name: set App Ingress Host URL
           id: set-ingress-host-address
           run: |
            echo "APP_INGRESS_HOST=$(kubectl -n ${{ vars.NAMESPACE }} get ingress -o jsonpath="{.items[0].spac.tls[0].hosts[0]}")" >> "$GITHUB_OUTPUT"


    prod-integration-testing:
      if: github.ref == 'refs/heads/main'
      name: Prod Integration Testing
      needs: prod-deploy
      runs-on: ubuntu-latest
      steps:
        - name: Test URL Output CURL and JQ
          env:
            URL: ${{ needs.prod-deploy.outputs.APP_INGRESS_URL }}
          run: |
           echo $URL
           echo "-------------------------"
           curl https://$URL/live -s -k | jq -r .status | grep -i live

```


##  Reusable Workflow



.github/workflows/solar-system.yml
```yml
name: Solar System Workflow

on:
    workflow_dispatch:
    push:    
        branches:
            - main
            - 'feature/*'


env:
  MONGO_URI: 'mongodb://${{ secrets.USERNAME}}:${{ secrets.PASSWORD }}@cluster0.7x6u1mv.mongodb.net/superData'
  MONGO_USERNAME: ${{ secrets.USERNAME }}
  MONGO_PASSWORD: ${{ secrets.PASSWORD}}

jobs:
    unit-testing:
        name: Unit Testing
        services:
          mongo-db:
            image: siddharth67/mongo-db:non-prod
            ports:
              - 27017:27017
        env:
             MONGO_URI: 'mongodb://localhost:27017/superData'
             MONGO_USERNAME: non-prod-user
             MONGO_PASSWORD: non-prod-password
        strategy:
          matrix:
            nodjs_version: [18, 20]
            operating_system: [ubuntu-latest]
        runs-on: ${{ matrix.operating_system }}

        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4

  
            - name: Setup NodeJs Version - ${{ matrix.nodjs_version }}
              uses: actions/setup-node@v3
              with:
                node-version: ${{ matrix.nodjs_version }}

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}


            - name: Install Dependencies
              run: npm install

            - name: Unit Testing
              id: nodejs-unit-testing-step
              run: npm test

            - name: Archive Test Result
              if: failure() && (steps.nodejs-unit-testing-step == 'failure' || steps.nodejs-unit-testing-step == 'success')
              uses: actions/upload-artifact@v4
              with:
                name: Mocha-Test-Resalt
                path: test-Resalt.xml
  
    code-coverage:
        name: Code Coverage
        container:
          image: node:18
        services:
          mongo-db:
            image: siddharth67/mongo-db:non-prod
            options:
              --name mongo
        env:
             MONGO_URI: 'mongodb://mongo:27017/superData'
             MONGO_USERNAME: non-prod-user
             MONGO_PASSWORD: non-prod-password
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4
  

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}


            - name: Install Dependencies
              run: npm install

            - name: Check code Coverage
              continue-on-error: true
              run: npm Coverage

            - name: Archive Test Result
              uses: actions/upload-artifact@v4
              with:
                name: Code Coverage Result
                path: Covrerage
                retention-days: 4

    docker:

      name: Containerization

      needs: [unit-testing, code-coverage]

      permissions:

        packages: write

      runs-on: ubuntu-latest

      steps:

         - name: Checkout Repo
           uses:  actions/checkout@v4

  
         - name: Dockerhub Login
           uses: docker/login-action@v3
           with:
            username: ${{ vars.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}

         - name: GHCR Login
           uses: docker/login-action@v3
           with:
            registry: ghcr.io
            username: ${{ github.repository_owner }}
            password: ${{ secrets.GITHUB_TOKEN }}

  

         - name: Build and push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: false
            tags: ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}

  

         - name: Docker Image testing
           run: |
             docker images
             docker run --name solar-system-app -d \
             -p 3000:3000 \
             -e MONGO_URI:$MONGO_URI \
             -e MONGO_PASSWORD:$MONGO_PASSWORD \
             -e MONGO_USERNAME:$MONGO_USERNAME \
             ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
             export IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' solar-system-app)
             echo $IP
             echo Testing Iamge URL using wget
             wget -q -O - http://127.0.0.1:3000/live | grep live


         - name: Container Registry push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: true
            tags: |
              ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
              ghcr.io/m0-3del/solar-system:${{ github.sha }}


    dev-deploy:

      if: contains(github.ref, 'feature/')
      needs: docker
      uses: ./.github/workflows/reuse-deployment.yml
      secrets:
         k8s-kubecngig: ${{ secrets.KUBECONFIG }}
         mongodb-password: ${{ secrets.MONGO_PASSWORD }}
      with:
        mongodb-uri: ${{ vars.MONGO_URI }}
        enviroment: development
        k8s-minfiest-dir: kubernetes/development/


    dev-integration-testing:
      if: contains(github.ref, 'feature/')
      name: Dev Integration Testing
      needs: dev-deploy
      runs-on: ubuntu-latest
      steps:
        - name: Test URL Output CURL and JQ
          env:
            URL: ${{ needs.dev-deploy.outputs.appliction-url }}
          run: |
           echo $URL
           echo "-------------------------"
           curl https://$URL/live -s -k | jq -r .status | grep -i live


    prod-deploy:
      if: github.ref == 'refs/heads/main'
      needs: docker
      uses: ./.github/workflows/reuse-deployment.yml
      secrets:
         k8s-kubecngig: ${{ secrets.KUBECONFIG }}
         mongodb-password: ${{ secrets.MONGO_PASSWORD }}
      with:
        mongodb-uri: ${{ vars.MONGO_URI }}
        enviroment: production
        k8s-minfiest-dir: kubernetes/production/
        
    prod-integration-testing:
      if: github.ref == 'refs/heads/main'
      name: Prod Integration Testing
      needs: prod-deploy
      runs-on: ubuntu-latest
      steps:
        - name: Test URL Output CURL and JQ
          env:
            URL: ${{ needs.prod-deploy.outputs.appliction-url }}
          run: |
           echo $URL
           echo "-------------------------"
           curl https://$URL/live -s -k | jq -r .status | grep -i live
```

.github/workflows/reuse-deployment.yml
```yml
name: Deployment - Reuseable Workflow
on:
    workflow_call:
      inputs:
        mongodb-uri:
         required: true
         type: string
        kubctl-version:
          description: Provide the requird Kubectl version
          default: v1.26.0
          required: false
          type: string
        k8s-minfiest-dir:
          description: Dirctroy containing kubernetes mainfiest files
          default: kubernetes/
          required: true
          type: string

        enviroment:
          description: Provide the Deployment Enviroment
          default: dev
          required: true
          type: string

      outputs:
        appliction-url:
          value: ${{ jobs.reuse-deploy.APP_INGRESS_URL }}
      secrets:
        k8s-kubecngig:
          required: true
        mongodb-password:
          required: true
jobs:

    reuse-deploy:
      environment:
        name: ${{ inputs.enviroment }}
        url: https://${{ steps.set-ingress-host-address.outputs.APP_INGRESS_HOST }}
      outputs:
        APP_INGRESS_URL: ${{ steps.set-ingress-host-address.outputs.APP_INGRESS_HOST }}
      runs-on: ubuntu-latest
      steps:
         - name: Checkout Repo
           uses:  actions/checkout@v4

         - name: install kubctl CLI
           uses: azure/setup-kubectl@v4
           with:
             version: '${{ inputs.kubctl-version }}'

         - name: Set Kubconfing
           uses: azure/k8s-set-context@v4
           with:
              method: kubeconfig
              kubeconfig: ${{ secrets.k8s-kubecngig }}

         - name: fetch kubernetces Cluster Details
           run: |
            kubctl version --short
            echo --------------------------------
            kubctl get nodes
            
         - name: Save Nginx Ingress Controller IP as a GITHUB Environment Variable
           run: |
            echo "IGRESS_IP=$(kubectl -n ingress-nginx get services ingress-nginx-controller -o jsonpath="{.status.loadBalancer.ingress[0].ip}")" >> $GITHUB_ENV


         - name: Replace Token in Minfest files
           uses: cschleiden/replace-tokens@v1
           with:
             tokenPrefix: '_{_'
             tokenSuffix: '_}_'
             files: '["${{ inputs.k8s-minfist-dir }}*.yaml"]'
           env:
             NAMESPACE: ${{ vars.NAMESPACE }}
             REPLICAS: ${{ vars.REPLICAS }}
             IMAGE: ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
             INGRESS_IP: ${{ env.IGRESS_IP }}

  
         - name: Check files
           run: |
            cat ${{ inputs.k8s-minfist-dir }}*.yaml

         - name:  Create Mongo Sercret
           run: |
            kubectl -n ${{ vars.NAMESPACE }} create secret generic mongo-db-creds \
            --from-literal=MONGO_URI=${{ inputs.mongodb-uri }}
            --from-literal=MONGO_USERNAME=${{ env.MONGO_USERNAME }}
            --from-literal=MONGO_PASSWORD=${{ secrets.mongodb-password}}
            --save-config \
            dry-run=Client \
            -o yaml | kubctl apply -f -
  
         - name: Deploy to PROD ENV
           run: |
            kubectl apply kubernetes/development/*.yaml

         - name: set App Ingress Host URL
           id: set-ingress-host-address
           run: |
            echo "APP_INGRESS_HOST=$(kubectl -n ${{ vars.NAMESPACE }} get ingress -o jsonpath="{.items[0].spac.tls[0].hosts[0]}")" >> "$GITHUB_OUTPUT"
```

### Reports to AWS S3 Storage
```yml

name: Solar System Workflow
on:
    workflow_dispatch:
    push:    
        branches:
            - main
            - 'feature/*'


env:
  MONGO_URI: 'mongodb://${{ secrets.USERNAME}}:${{ secrets.PASSWORD }}@cluster0.7x6u1mv.mongodb.net/superData'
  MONGO_USERNAME: ${{ secrets.USERNAME }}
  MONGO_PASSWORD: ${{ secrets.PASSWORD}}

jobs:
    unit-testing:
        name: Unit Testing
        services:
          mongo-db:
            image: siddharth67/mongo-db:non-prod
            ports:
              - 27017:27017
        env:
             MONGO_URI: 'mongodb://localhost:27017/superData'
             MONGO_USERNAME: non-prod-user
             MONGO_PASSWORD: non-prod-password
        strategy:
          matrix:
            nodjs_version: [18, 20]
            operating_system: [ubuntu-latest]
        runs-on: ${{ matrix.operating_system }}

        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4

  
            - name: Setup NodeJs Version - ${{ matrix.nodjs_version }}
              uses: actions/setup-node@v3
              with:
                node-version: ${{ matrix.nodjs_version }}

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}


            - name: Install Dependencies
              run: npm install

            - name: Unit Testing
              id: nodejs-unit-testing-step
              run: npm test

            - name: Archive Test Result
              if: failure() && (steps.nodejs-unit-testing-step == 'failure' || steps.nodejs-unit-testing-step == 'success')
              uses: actions/upload-artifact@v4
              with:
                name: Mocha-Test-Resalt
                path: test-Resalt.xml
  
    code-coverage:
        name: Code Coverage
        container:
          image: node:18
        services:
          mongo-db:
            image: siddharth67/mongo-db:non-prod
            options:
              --name mongo
        env:
             MONGO_URI: 'mongodb://mongo:27017/superData'
             MONGO_USERNAME: non-prod-user
             MONGO_PASSWORD: non-prod-password
        runs-on: ubuntu-latest
        steps:
            - name: Checkout Repo
              uses:  actions/checkout@v4
  

            - name: Cache NPM dependencies
              uses: actions/cache@v4
              with:
                path: node_modules
                key: ${{ runner.os }}-node_modules-${{ hashFiles('package-lock.json')}}


            - name: Install Dependencies
              run: npm install

            - name: Check code Coverage
              continue-on-error: true
              run: npm Coverage

            - name: Archive Test Result
              uses: actions/upload-artifact@v4
              with:
                name: Code Coverage Result
                path: Covrerage
                retention-days: 4

    report-s3:
      needs: [unit-testing, code-coverage]
      name: AWS S3 -upload Reports
      runs-on: ubuntu-latest
      continue-on-error: true
      steps:
        - name: Dowenload Mocha Test Artifact
          uses: actions/download-artifact@v4
          with:
            name: Mocha-Test-Result

        - name: Dowenload Mocha Coverage Artifact
          uses: actions/download-artifact@v4
          with:
            name: Mocha-Coverage-Result

        - name: Merge Test files
          run: |
           ls -ltr
           mkdir reports-${{ github.sha }}
           mv cobertura-coverage.xml reports-${{ github.sha }}/
           mv test-results.xml reports-${{ github.sha }}/
           ls -ltr reports-${{ github.sha }}/

        - name: Upload to AWS S3
          uses: jakejarvis/s3-sync-action@master
          with:
             args: --acl public-read --follow-symlinks --delete
          env:
             AWS_S3_BUCKET: ${{ secrets.AWS_S3_BUCKET }}  # name bucket
             AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
             AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
             AWS_REGION: 'us-west-1'   # optional: defaults to us-east-1
             SOURCE_DIR: 'reports-${{ github.sha }}'
             DEST_DIR: 'reports-${{ github.sha }}'

    docker:

      name: Containerization

      needs: [unit-testing, code-coverage]

      permissions:

        packages: write

      runs-on: ubuntu-latest

      steps:

         - name: Checkout Repo
           uses:  actions/checkout@v4

  
         - name: Dockerhub Login
           uses: docker/login-action@v3
           with:
            username: ${{ vars.DOCKERHUB_USERNAME }}
            password: ${{ secrets.DOCKERHUB_TOKEN }}

         - name: GHCR Login
           uses: docker/login-action@v3
           with:
            registry: ghcr.io
            username: ${{ github.repository_owner }}
            password: ${{ secrets.GITHUB_TOKEN }}

  

         - name: Build and push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: false
            tags: ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}

  

         - name: Docker Image testing
           run: |
             docker images
             docker run --name solar-system-app -d \
             -p 3000:3000 \
             -e MONGO_URI:$MONGO_URI \
             -e MONGO_PASSWORD:$MONGO_PASSWORD \
             -e MONGO_USERNAME:$MONGO_USERNAME \
             ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
             export IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' solar-system-app)
             echo $IP
             echo Testing Iamge URL using wget
             wget -q -O - http://127.0.0.1:3000/live | grep live


         - name: Container Registry push
           uses: docker/build-push-action@v6
           with:
            context: .
            push: true
            tags: |
              ${{ vars.DOCKERHUB_USERNAME }}/solar-system:${{ github.sha }}
              ghcr.io/m0-3del/solar-system:${{ github.sha }}


    dev-deploy:

      if: contains(github.ref, 'feature/')
      needs: docker
      uses: ./.github/workflows/reuse-deployment.yml
      secrets:
         k8s-kubecngig: ${{ secrets.KUBECONFIG }}
         mongodb-password: ${{ secrets.MONGO_PASSWORD }}
      with:
        mongodb-uri: ${{ vars.MONGO_URI }}
        enviroment: development
        k8s-minfiest-dir: kubernetes/development/


    dev-integration-testing:
      if: contains(github.ref, 'feature/')
      name: Dev Integration Testing
      needs: dev-deploy
      runs-on: ubuntu-latest
      steps:
        - name: Test URL Output CURL and JQ
          env:
            URL: ${{ needs.dev-deploy.outputs.appliction-url }}
          run: |
           echo $URL
           echo "-------------------------"
           curl https://$URL/live -s -k | jq -r .status | grep -i live


    prod-deploy:
      if: github.ref == 'refs/heads/main'
      needs: docker
      uses: ./.github/workflows/reuse-deployment.yml
      secrets:
         k8s-kubecngig: ${{ secrets.KUBECONFIG }}
         mongodb-password: ${{ secrets.MONGO_PASSWORD }}
      with:
        mongodb-uri: ${{ vars.MONGO_URI }}
        enviroment: production
        k8s-minfiest-dir: kubernetes/production/
        
    prod-integration-testing:
      if: github.ref == 'refs/heads/main'
      name: Prod Integration Testing
      needs: prod-deploy
      runs-on: ubuntu-latest
      steps:
        - name: Test URL Output CURL and JQ
          env:
            URL: ${{ needs.prod-deploy.outputs.appliction-url }}
          run: |
           echo $URL
           echo "-------------------------"
           curl https://$URL/live -s -k | jq -r .status | grep -i live


```



### Notify 
https://github.com/marketplace?query=Notify&page=1&type=actions
```yml

    notify:
     if: always()
     name: Notification
     needs: [dev-integration-testing, prod-integration-testing]
     continue-on-error: true
     runs-on: ubuntu-latest
  

     steps:
      - name: Slack Notification
        uses: rtCamp/action-slack-notify@v2
        env:
          SLACK_CHANNEL: general
          SLACK_COLOR: ${{ job.status }} # or a specific color like 'good' or '#ff00ff'
          SLACK_ICON: https://github.com/rtCamp.png?size=48
          SLACK_MESSAGE: 'Post Content :rocket:'
          SLACK_TITLE: Post Title
          SLACK_USERNAME: rtCamp
          SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}

      - name: Run Actions Telegram Notify
        uses: proDreams/actions-telegram-notifier@main
        with:
          token: ${{ secrets.TELEGRAM_BOT_TOKEN }}
          chat_id: ${{ secrets.TELEGRAM_CHAT_ID }}
          status: ${{ job.status }}
          notify_fields: "actor,repository,workflow,branch,commit"
          message: "Test message."
          footer: "Test footer"

      - name: Send Discord notification
        uses: stegzilla/discord-notify@v2
        with:
          webhook_url: ${{ secrets.DESCORD_BOT_TOKEN }}
          title: New PR opened
          message: "**${{ github.event.pull_request.title }}**\n**Creator:** ${{ github.event.pull_request.user.login }}\n${{ github.event.pull_request.html_url }}"
          include_image: true
          avatar_url: https://github.githubassets.com/images/modules/logos_page/GitHub-Mark.png
          username: GitHub PR Notifier

```


### self host runner

```bash
#!/bin/bash

# Exit immediately if a command exits with a non-zero status.
set -e
# Treat unset variables as an error when substituting.
set -u
# Pipes fail if any command in the pipe fails
set -o pipefail

# --- Configuration ---
# You can change these defaults if needed
DEFAULT_RUNNER_VERSION="2.325.0" # The version you used, or use "latest" to try and fetch the latest
RUNNER_NAME_PREFIX="prod-ubuntu-runner" # Will append a random suffix or you can set a full name
ADDITIONAL_LABELS="prod,main"
RUNNER_WORKDIR="_work"
RUNNER_GROUP="" # Empty means 'Default' group. Set to "your-group-name" if needed.

# --- Script Arguments ---
if [[ $# -ne 2 ]]; then
    echo "Usage: $0 <YOUR_GITHUB_REPO_URL> <YOUR_RUNNER_TOKEN>"
    echo "Example: $0 https://github.com/your_user/your_repo AAAAxxxxxxxxxxxxYYYYzzzzzzzzzz"
    echo "You can get the token from your repository's Settings > Actions > Runners > New self-hosted runner"
    exit 1
fi

REPO_URL="$1"
RUNNER_TOKEN="$2"

# --- Determine Runner Architecture and OS ---
# For this script, we'll stick to what you used: linux-x64
RUNNER_ARCH="x64"
RUNNER_OS="linux"

# --- Runner Version Logic (Optional: for fetching latest) ---
# If you set DEFAULT_RUNNER_VERSION to "latest", this snippet would try to find it.
# For simplicity and to match your log, we're using the fixed version.
# If you wanted latest:
# RUNNER_VERSION=$(curl -s https://api.github.com/repos/actions/runner/releases/latest | grep tag_name | cut -d '"' -f 4 | sed 's/v//')
# But you used a specific version, so:
RUNNER_VERSION="${DEFAULT_RUNNER_VERSION}"

# --- Runner Name ---
# You can make it unique if you run this script multiple times for different runners
# RUNNER_NAME="${RUNNER_NAME_PREFIX}-$(head /dev/urandom | tr -dc A-Za-z0-9 | head -c 5)"
# Or use the exact name you provided:
RUNNER_NAME="${RUNNER_NAME_PREFIX}" # As per your log, you entered 'prod-ubuntu-runner'

# --- Directory Setup ---
echo "Creating and navigating to actions-runner directory..."
mkdir -p actions-runner
cd actions-runner

# --- Check if runner is already downloaded/extracted ---
TARBALL_NAME="actions-runner-${RUNNER_OS}-${RUNNER_ARCH}-${RUNNER_VERSION}.tar.gz"
DOWNLOAD_URL="https://github.com/actions/runner/releases/download/v${RUNNER_VERSION}/${TARBALL_NAME}"

if [ -f "./config.sh" ]; then
    echo "Runner seems to be already extracted. Skipping download and extraction."
elif [ -f "./${TARBALL_NAME}" ]; then
    echo "Runner tarball ${TARBALL_NAME} already exists. Skipping download."
else
    echo "Downloading GitHub Actions Runner v${RUNNER_VERSION}..."
    curl -o "${TARBALL_NAME}" -L "${DOWNLOAD_URL}"
fi

if [ ! -f "./config.sh" ]; then
    echo "Extracting ${TARBALL_NAME}..."
    tar xzf "./${TARBALL_NAME}"
fi

# --- Configure Runner ---
# This is necessary if you are running this script as root or with sudo
# and intend for the config.sh to be run as root.
# GitHub's config.sh script checks if UID is 0 and exits if it is,
# unless this environment variable is set.
echo "Setting RUNNER_ALLOW_RUNASROOT=1 for configuration if needed..."
export RUNNER_ALLOW_RUNASROOT="1"

echo "Configuring the runner..."
# Use --unattended for non-interactive setup
# Use --replace if a runner with the same name might already exist from a previous attempt
./config.sh --unattended --url "${REPO_URL}" --token "${RUNNER_TOKEN}" \
    --name "${RUNNER_NAME}" \
    --labels "${ADDITIONAL_LABELS}" \
    --work "${RUNNER_WORKDIR}" \
    $( [[ -n "$RUNNER_GROUP" ]] && echo "--runnergroup \"${RUNNER_GROUP}\"" ) \
    --replace # Add --replace to overwrite existing config if runner name conflicts

echo "Configuration complete."

# --- Run the Runner ---
echo "Starting the runner..."
echo "To run as a service in the background (recommended for production):"
echo "  sudo ./svc.sh install"
echo "  sudo ./svc.sh start"
echo "For now, running in the foreground. Press Ctrl+C to stop it."

./run.sh

```

```bash

chmod +x setup_runner.sh
./setup_runner.sh https://github.com/M0-3del/solar-system YOUR_NEW_TOKEN_HERE
```

```yml
name: Test self-hosted Runner

on:
    workflow_dispatch:

jobs:
    testing:
        runs-on: [self-hosted, Linux, prod]
        steps:
            - name: Echo Content
              run: |
               echo OK
               sleep 15s
```

#### Exploring Self Hosted Runner
```
tree _diag/

Runner_2Ø231024-144833-utc.log
Worker_2e231024-152357-utc.log



cat _diag/Runner_2Ø231024-144833-utc.log
cat _diag/Worker_2e231024-152357-utc.log



tree _work/


cat _work/_Pipe1ineMapping/sidd-harth-7/actions-1/Pipe1ineF01der.json
cat _work/_temp/e6abcca7-de9b-4Ø6e-ba2e-f87e625ac8db.sh


cat _work/_temp/_github_workflow/event.json

cat _work/_temp/_runner_fi1e_commands/add_path_a29fdc6d-e254-4fc5-a6eb-982313c5Ø8fd

```

#### remove self hosted runner
https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/removing-self-hosted-runners