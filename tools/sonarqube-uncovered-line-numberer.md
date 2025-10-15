### background
SonarQube Code Analysis is used in GitHub Enterprise pull request pipeline.

However, sonarqube does not show the exact line numbers of uncovered code. see:
 - https://community.sonarsource.com/t/not-able-to-find-out-lines-not-covered-in-new-code/8693
   > However we are not able to make out exact lines which are not covered.
 - https://community.sonarsource.com/t/easily-identify-new-uncovered-lines/115612
   > but I could not find an easy way to identify which new lines are not considered as covered by the tooling.

   > The only way I found so far to try to identify my lines is to mouse over the code showed as uncovered to see which one is part of the new code.

### how to workaround
1. go to the sonarqube report and go to the detail page of one changed file.
  - sonarqube page, e.g. `https://sonar.example.com/component_measures?id=org%3Arepo&pullRequest=123&metric=new_coverage&view=list`
  - detail page, e.g. `https://sonar.example.com/component_measures?id=...&metric=new_uncovered_lines&pullRequest=123&view=list&selected=org%3Arepo%3Apath%2Fto%2Ffile.js`
2. open devtools, copy following and execute
```javascript
async function fetchAndPrintResult() {
  const host = window.location.host;
  const params = new URLSearchParams(window.location.search);
  const key = params.get('selected');
  if (key === null) {
    throw new Error(`'selected' query string value is null, cannot fetch sonar result. Check the url, and if you are in detail page`);
  }
  const pullRequestId = params.get('pullRequest');
  if (pullRequestId === null) {
    throw new Error(`'pullRequest' query string value is null, cannot fetch sonar result. Check the url`);
  }
  const url = `https://${host}/api/sources/lines` +
    `?key=${encodeURIComponent(key)}` +
    `&from=1&to=999999` + 
    `&pullRequest=${pullRequestId}`;
  const result = await fetch(url);
  const resultJson = await result.json();
  const uncoveredLines = [];
  const unconveredLinesConditional = [];
  for (let lineObject of resultJson.sources) {
    if (lineObject.isNew !== true) {
      continue;
    }
    if (lineObject.lineHits === 0) {
      uncoveredLines.push(lineObject);
    }
    if (typeof lineObject.conditions === 'number' && typeof lineObject.coveredConditions === 'number' &&
      lineObject.coveredConditions < lineObject.conditions) {
      unconveredLinesConditional.push(lineObject);
    }
  }
  
  console.log('uncoveredLines, count:', uncoveredLines.length);
  for (const x of uncoveredLines) {
    console.log(`line: ${x.line}, lintHits: ${x.lineHits}`);
  }
  
  console.log('conditional uncoveredLines, count:', unconveredLinesConditional.length);
  for (const x of unconveredLinesConditional) {
    console.log(`line: ${x.line}, covered/Conditions: ${x.coveredConditions}/${x.conditions}`);
  }
}
await fetchAndPrintResult();
```
The api format is:
`https://sonar.example.com/api/sources/lines?key=github-org-name%3Agithub-repo-name%3Apath%2Fto%2Ffile.js&from=1&to=999999&pullRequest=123`

params:
- key: encodeURIComponent(`org-name:repo-name:path/to/file.js`)
- from: 1
- to: 999999
- pullRequest: ID of the PR analyzed by sonarqube
