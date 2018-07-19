### Android App git工作流程

总述  
项目包括feature分支、master分支、release分支、bugfix分支、hotfix分支。（若存在大版本更迭，且同时需要保留新老版本，则采用1.x，2.x的形式）进行开发。

- feature分支：  
分支名以feature开头，命名类似于feature/quickpass_optimise、feature/pwdStrength_tip等。
以闪付优化功能为例，从master分支切出feature/quickpass_optimise分支。在该分支进行闪付优化相关功能开发。如有多人协作开发该功能，则每人都从feature/quickpass_optimise分支切出新分支进行开发，需要合并的时候PR至feature/quickpass_optimise分支，并assign给peer reviewer进行review。
- release分支：  
分支名以release开头，命名类似于release-2.5.0, release-2.6.0等。
该分支是在“某个版本里要上线哪几个功能”这个目标已经明确之后（一般是在正式提测之前的几个小时）从最新的线上稳定版本tag处切出。比如2.5.0的版本明确了要上线feature-A，feature-B，feature-C三个功能，那么在提测之前从tag v2.4.9切出release-2.5.0分支，在gitlab上提出3个PR，分别将feature/A, feature/B, feature/C 三个功能分支合并到release-2.5.0分支，由code reviewer检查代码并解决冲突，然后修改代码中的版本号，jenkins指定release-2.5.0分支进行打包提测。全部测试完成之后，PR至master分支，由code reviewer确认之后执行合并，并从master分支打出测试包供测试执行全流程回归测试（其实可以用UAT代替）。测试回归之后输出测试报告，开发用jenkins指定master分支打线上包进入线上流程。
- bugfix分支：  
分支名以bugfix开头。比如bugfix/update_mini_program_AppId。对于测试过程中出现的bug，从release-2.5.0分支切出bugfix分支，修复bug之后提出PR合并到release-2.5.0分支，并assign给peer reviewer进行review，确认之后执行合并操作。
- master分支：   
项目的主分支，所有发布线上的版本只能从该分支拉取代码。发布线上之后记得打tag。
tag名一般以v2.5.0, v2.6.0。
- hotfix 分支   
app上线之后，若遇到紧急bug需要修复上线，则采用hotfix分支方式来进行版本发布。以1.2.0 hotfix至1.2.1为例。
从线上问题tag(1.2.0)处切出分支hotfix-1.2.1，然后将bug修复代码合并至hotfix-1.2.1分支上。打出测试包，供测试。待测试完成，uat验证完成。pr合并至master，jenkins指定master分支打线上包进入线上流程，打tag，pr合并至各release分支。



总体原则就是：

1.所有代码尽量不直接push至远程，必须执行PR流程，基本保证了每行代码都是由合并而来，也保证每行代码一定有至少2个人看过。  

2.所有要上线的代码必须从master分支拉取。  
 
3.对于合并代码过程中出现的问题，如果改动较少，只有1、2处冲突或者有疑问的地方，当面沟通效率更高。如果发现多处疑问，可以在merge request中有疑问的那一行添加comment，针对每一处疑问分别讨论。   

4.对于合并代码出现的冲突，不能随意删除代码。尽量叫上冲突部分代码的相关人员讨论决定。



