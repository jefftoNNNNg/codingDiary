# Build typeorm & express project
1. build project
```
express -e YOURPROJECTNAME
```

2. Init typeorm. NOTICE: This would rewrite the file. Do not run this in your current project. Use in your new project.
```
typeorm init --database mssql
```

3. install
```
npm i
```