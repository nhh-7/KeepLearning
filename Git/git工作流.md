## 一、主要分支
在工作流中，通常会维护两个长期存在的“主干"分支

1. main 分支(或 master) 
     - 用途:代表了项目最稳定、已发布或即将发布的生产版本。
     - 规则: 
       	  绝对禁止直接在该分支上进行任何开发或提交代码。
        	 所有代码都必须通过从其他分支合并请求(Merge Request/Pull Request）的方式进入。
        	 合并到main 分支前，必须经过代码审查（Code Review）和自动化测试（Cl)。
     - 状态:永远处于"可部署”状态。

2. develop 分支(或 dev ) 
     - 用途:作为所有新功能开发的集成基础分支。它是所有功能分支的“父分支”和“目标分支”。它是所有feature 分支的起点，也是 feature分支完成后的合并目的地。

## 二、临时性分支

这些分支是为特定任务创建的，任务完成后就会被删除。

1. 功能分支(feature/*)

   - 命名: feature/user-Login, feature/shopping-cart-api, feature/JIRA-123-newbutton 
   - 用途:开发一个新功能。
   - 生命周期: 
     - 从 develop分支创建。
     - 开发完成后，合并回develop分支。
     - 合并后，该feature 分支应被删除。

2. 修复分支(fix/*或 bugfix/*) 

   - 命名:fix/Login-bug,fix/payment-error-on-mobile 

   - 用途:修复在开发过程或测试环境中发现的Bug。

   - 生命周期: 
     - 从 develop 分支创建。
       - 修复完成后，合并回 develop分支。
       - 合并后，该分支应被删除。

3. 紧急修复分支(hotfix/*）

   - 命名: hotfix/critical-security-vulnerability , hotfix/prod-crash-on-startup 

   - 用途:修复线上生产环境出现的紧急Bug。这是唯一一个例外，它直接从 main分支创建。

   - 生命周期: 
     - 从 main 分支创建。
     - 修复并测试完成后，必须同时合并回main分支和develop分支。
     - 合并回 main后，应立即打上新的版本标签（Tag）并部署。
     - 合并回 develop 是为了确保开发分支也包含了这个修复，避免未来的发布版本再次出现同样的问题。
     - 完成后，删除该hotfix 分支。

## 三、场景化示例

### 场景：开发一个新功能(最常见) 

任务:开发一个“用户个人资料编辑”功能。

1. 准备工作:同步本地仓库

   - 首先，确保你的 main 和develop 分支都是最新的。

   ```
   # 切换到 deveLop 分支
   git checkout develop 
   
   # 拉取远程develop 分支的最新代码，后面需要在develop分支上创建新分支
   git pull origin develop
   ```

2. 创建功能分支

   - 基于最新的develop分支，创建一个属于你的 feature 分支。

   ```
   # 当前在develop分支上,-b参数表示创建并立即切换到新分支
   git checkout -b feature/user-profile-edit ( [base-branch], 忽略此参数则基于当前所处分支进行创建）
   git switch -c feature/user-profile-edit [base-branch] 
   ```

3. 进行开发：编码、提交

   - 在功能分支上进行代码编写、修改文件
   - 每次提交都应该只包含一个逻辑上完整的改动。

   ```
   # 修改代码...
   git add .
   git commit -m "feat: add user. avatar upload component" 
   #再次修改代码. 
   git add .
   git commit -m "refactor: optimize profile data saving Logic" 
   ```

   - 提交信息规范：遵循一定的规范(如Conventional Commits） 会让你的提交历史非常清晰。例如feat:(新功能),fix:(修复bug),refactor:(重构),docs:(文档)等。

4. 保持与主分支同步

   - 当你在feature分支。为了避免最后合并时产生巨大的冲突，你应该定期将develop分支的最新代码合并到你的feature分支。如果开发完成后再merge可能会爆很多冲突。

   ```
   #1，先更新本地的 develop 分支
   git fetch origin develop 
   #2，将最新的 deveLop 合并到你当前的feature 分支（当前处于feat分支）
   git merge origin/develop 
   #(如果出现冲突，此时解决冲突）
   ```

5. 推送分支到远程

   - 当你觉得功能开发到一定阶段，或者需要同事帮助review时，就可以把你的分支推送到远程仓库。

   ```
   # 第一次推送需要设置上游分支
   git push --set-upstream origin feature/user-profile-edit 
   # 之后再推送，直接用 git push 即可
   git push
   ```

6. 创建合并请求(Pull Request / Merge Request) 

   - 当功能开发完成并通过自测后，登录到GitLab/GitHub/Gitee等代码托管平台。
   - 找到你的 feature/user-profile-edit分支，点击“创建合并请求“按钮。
   - 目标分支选择develop。
   - 在PR描述中清晰地写明：你做了什么、为什么这么做、如何测试。
   - 指定至少一到两位同事作为审查者(Reviewer)。

7. 代码审查(Code Review)与持续集成(CI) 

   - 你的同事会审查你的代码，提出修改建议。
   - 同时，CI服务器会自动运行测试用例、代码风格检查等。
   - 根据反馈修改代码，然后再次git push。新的提交会自动更新到同一个PR中。

8. 合并与清理

   - 当代码审查通过、所有CI检查都成功后，项目负责人(或你自己)点击“合并"按钮，将你的功能代码并入develop分支。
   - 合并完成后，为了保持仓库整洁，删除远程和本地的feature 分支。

```
# 删除远程分支
git push origin --delete feature/user-profile-edit 
# 切换回 develop 分支
git checkout develop 
# 删除本地分支
git branch -d feature/user-profile-edit 
```

### 场景:基于远程已有分支创建本地分支

1. 获取远程仓库的最新信息

   ```
   git fetch origin
   ```

2. 创建并切换到本地分支

   当你尝试switch或checkout到一个在本地不存在，但在远程唯一存在(feature/user-profile) 同名分支时，Git会自动为你完成所有工作

   ```
   # 推荐使用switch(较新的命令)
   git switch feature/user-profile
   
   # 或者使用 checkout(传统命令，效果相同)
   git checkout feature/user-profile
   
   # 上述命令在本地创建 feature/user-profile分支，并自动将其与远程分支建立跟踪关系
   
   # 如果需要自行控制本地分支名,如下：
   # 使用'git switch -c'(create)配合'--track'选项。
   git switch -c my-profile-work --track origin/feature/user-profile
   
   #使用checkout, 对应的命令是使用-b(branch)和--track
   git checkout -b my-profile-work --track origin/feature/user-profile
   ```

3. 在新的本地分支上进行开发

