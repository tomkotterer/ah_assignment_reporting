"# ah_assignment_reporting" 

Steps to create the automated test in CircleCI with reporting

Postman
1. Documented the API-calls to the collection
2. Added tests to the calls
3. Exported the collection

Run locally to test
1. Create a project in VS
2. Run the following in the terminal:
mkdir postman-api-reports
cd postman-api-reports
npm init -y
npm install newman
npm install -S newman-reporter-htmlextra
3. Run npx newman run run AH_assignment_reporting.postman_collection.json -r htmlextra
4. Reporting is saved in the new folder: newman

CircleCI
1. Added a gitignore to make sure everything stays clean. Add node_modules AND newman to the file
2. Commit the project
3. In CircleCI, choose 'Set Up Project'
4. Click Use existing Config

Create the .circleci.config.yml
1. Copy Paste the following:
version: 2.1
jobs:
  build:
    working_directory: ~/repo
    docker:
      - image: circleci/node:10.16.3
    steps:
      - checkout
      - run:
          name: Update NPM
          command: "sudo npm install -g npm@5"
      - restore_cache:
          key: dependency-cache-{{ checksum "package-lock.json" }}
      - run:
          name: Install Dependencies
          command: npm install
      - save_cache:
          key: dependency-cache-{{ checksum "package-lock.json" }}
          paths:
            - ./node_modules
      - run:
          name: Generate Report
          command: npx newman run AH_assignment_reporting.postman_collection.json -r htmlextra
      - store_artifacts:
          path: ~/repo/newman
In the configuration above, the node.js Docker image is pulled in as the environment for running the tests. Then, npm is updated, and dependencies are installed and cached. 
With all dependencies in place, the custom report script is run using npm. This command runs the tests and generates the report. 
Finally, the reports folder (newman) is stored as an artifact. It can be found on the build’s Artifacts tab on the CircleCI console.
2. Run the build
3. Build should be successful
4. Reporting can be found in the Artifacts tab