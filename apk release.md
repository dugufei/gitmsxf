##Android app版本发布流程：
###### 一、正常发布流程（以1.1.0升级至1.2.0为例)
- 1.总述
项目中使用了versionMajor，versionMinor，versionPatch，versionBuild(即：主版本号，次版本号，bug修复号，编译号)组成，每次提交测试时，versionBuild增1，若开始下一个版本的开发时，调整相应的versionMajor，versionMinor，versionPatch，versionBuild。项目分支使用master，develop分支的方式（若存在大版本更迭，且同时需要保留新老版本，则采用1.x，2.x的形式）进行开发，代码提交均从develop切出新分支，并在gitlab上请求pr合并至develop。
- 2.具体流程  

(1)待测试完成，uat通过后，将本地所有未提交代码提交至远程，保证分支代码清洁。  

(2)切至develop分支，并保证develop分支代码为最新。从develop分支切出一个分支release-1.2.0，将versionBuild号重置至1，并执行：  

git commit -a -m "Bumped version number to 1.2.0"  

(3)切至master分支，保证master分支代码为最新，并执行：  

git merge --no-ff release-1.2.0 -m "Merge branch 'release-1.2.0' into master"  

git push origin master  

(4)增加版本tag，执行：  

git tag -a 1.2.0 -m "v1.2.0"  

git push origin 1.2.0  

(5)切换至develop，并执行：  

git merge --no-ff release-1.2.0 -m "Merge branch 'release-1.2.0' into develop"  

git push origin develop  

(6)删除无用分支(切无将release-1.2.0分支推送至远程)：  

git branch -d release-1.2.0  

(7)切换至master分支译发布app v1.2.0:  

gradle assembleProductionRelease  

(8)为下一个开发版本做准备  


###### 二、hotfix版本发布流程（以1.2.0 hotfix至1.2.1为例）

- 1.总述
app上线之后，若遇到紧急bug需要修复上线，则采用hotfix分支方式来进行版本发布。
- 2.具体流程
(1)从线上问题tag(1.2.0)处切出分支hotfix-1.2.1，然后将bug修复代码合并至hotfix-1.2.1分支上。
(2)待bug修复完成，测试完成，uat验证完成，将本地所有未提交代码提交至远程，保证分支代码清洁。
(3)将versionBuild重置为1（1.2.1），并执行：
git commit -a -m "Bumped version number to 1.2.1"
(4)切换至master分支，保证代码为最新，并执行：
git merge --no-ff hotfix-1.2.1 -m "Merge branch 'hotfix-1.2.1' into master"
git push origin master
(5)增加版本tag，执行：
git tag -a 1.2.1 -m "v1.2.1"
git push origin 1.2.1
(6)切换至develop分支，并执行：
git merge --no-ff hotfix-1.2.1 -m "Merge branch 'hotfix-1.2.1' into develop"
git push origin develop
(7)删除无用分支：
git branch -d hotfix-1.2.1
(8)切换至master分支译发布app v1.2.1:
gradle assembleProductionRelease
###### 三、回滚版本发布流程（以1.2.0回滚至1.1.0为例）
- 1.总述
app上线之后遇到重大bug时，需要回滚至前一个版本。
- 2.具体流程
(1)从1.2.0回滚到1.1.0，则从1.1.0中切出rollback-1.2.0分支，更改versionCode为12002，
versionName为1.2.0-rb1 （此处rb代表1.2.0第一次rollback，第n次rollback依次类推）。
(2)1.2.0后续开发则按升级版本方式发布，新版本号为(1.2.1，第n次以此类推)。