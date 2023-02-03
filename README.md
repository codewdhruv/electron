## Electron CircleCI to Harness CI Migration Guide:

Before migrating from [CircleCI to Harness CI](https://github.com/harness-community/ci-migration-guides/tree/main/CIRCLECI_TO_HARNESS_CI), kindly go through [this](https://github.com/harness-community/ci-migration-guides/tree/main/CIRCLECI_TO_HARNESS_CI) guide.  
![](https://33333.cdn.cke-cs.com/kSW7V9NHUXugvhoQeFaf/images/8950a8c152d7b6f986dc62d9a07942d34f24d956e3e994a0.png)  
![](https://33333.cdn.cke-cs.com/kSW7V9NHUXugvhoQeFaf/images/7a07a6fe2b46eb45cc004032fdc1eb97cae1d8cea0574715.png)

### Challenge in Sharing Data between Stages:

- Sharing environment path configuration across different steps for the same stage. ($PATH, $CHROMIUM_BUILDTOOL_PATH)
- Installation of Chromium SRC (CircleCI did not explicitly execute any shell scripts to add the dependencies to the project)
  - At Harness CI we implemented custom shell script to add all the testing dependencies to the project. (GN & Ninja)
- Unable to implement matrix due to un-modular sh scripts written for testing different arch. (job-level)

What Did Not Work:

1.  Using step groups at the step level.
2.  Using caching with sh scripts testing OS type and arch type. (Darwin & Linux)

## Wait time before moving to next stage 

Consider a  pipeline with 3 stages A B C  with the following  requirements 

*   A and  B are parallel 
*   C requires  B .
*   A takes 10 mins to complete
*   B takes 2 min
*   C takes 5 min 

What will be the total execution time of the pipeline ?

*   CircelCi : 10 mins. because C will start execution as soon as B ends . Its not dependent on A.
*   Harness CI :  15 mins.  C will wait for both A and B to be completed , ie C has to wait  8 mins  extra
