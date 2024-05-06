# GitHub Flow

# 创建项目

其中包含两个文件，一个为`README.md`和`main.go`文件：

`main.go`：

```go
package main

import "fmt"

func add(a,b int) int {
	return a + b
}

func main() {
	num1 := 10
	num2 := 20

	sum := add(num1,num2)

	fmt.Printf("The sum of %d and %d is %d\n", num1, num2, sum)
}
```

在GitHub上创建仓库：`xxx/githubflow`

使用 Git 进行管理并上传云端：

```bash
git init
git add .
git commit -m "first commit"
git remote add origin git@github.com:xxx/githubflow.git
git push origin main
```

# GitHub Flow 工作流

## 1. 新建分支【Create a branch 】

![image-20240507001210482](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070012586.png)

使用 GitHub Flow 的第一步便是基于 main 分支，创建一个开发分支。GitHub Flow 中其实是不区分功能分支和修复分支的。但通常为了分支的可理解性，在创建分支时，还是加上分支类型前缀，例如：`feature/add-addlicense`、`fix/resolve-name-conflict`等。在起分支名字的时候，名字要尽可能简洁易懂。在我的开发生涯中，发现不少开发者创建的分支名字，1 个月后，连自己都不知道该分支是完成的哪个产品需求或修复的哪个 Bug。

这里，假设我们要为 [https://github.com/LiangNing7/githubflow](https://github.com/LiangNing7/githubflow.git) 项目贡献代码。首先，我们需要克隆仓库【用创建项目代替了】，并基于 main 分支创建一个开发分支。这里假设，我们的需求是给项目添加一个减法函数。为此需要基于 main 分支创建一个 `feature/add-sub-function` 分支， 操作命令如下：

```bash
# 自己创建的即跳过该阶段
git clone https://github.com/LiangNing7/githubflow.git

# 创建一个新的分支
git checkout -b feature/add-sub-function
```

在新的开发分支上，你可以完全自由的按照自己的想法，去修改源码，开发新功能，而不用担心你的操作会影响到 main 分支。

## 2. 提交修改【Add commits】

![image-20240507001241345](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070012401.png)

当我们创建了开发分支，接下来就可以根据需要修改、并提交代码。修改一个功能，可能要提交多个 commit，这里建议提交的每个 commit 功能是独立的，并且不能很多个功能一次性提交。一个小的功能完整的 commit 可以让我们很方便的进行代码审查，并且当发现修改错误，需要回滚时，也可以很方便的执行 Revert 操作。

比如，如果你想重命名一个变量并添加一些测试，可以将变量重命名放在一个 commit 中，将测试放在另一个 commit 中。这样，以后如果你想保留测试但撤销变量重命名，你只需撤销包含变量重命名的特定 commit 即可。如果将变量重命名和测试放在同一个 commit 中，或者将变量重命名分散在多个 commit 中，那么撤销你的更改就会变得更加复杂和耗费更多精力。

在提交时，应该填写简洁易懂的 commit message，以使未来的自己和其他开发者，知道某个 commit 做了哪些变更。

这里，我们修改后的 main.go文件内容如下：

```Diff
$ git diff main.go
diff --git a/main.go b/main.go
index 55f2b09..86951e9 100644
--- a/main.go
+++ b/main.go
@@ -6,11 +6,17 @@ func add(a,b int) int {
        return a + b
 }

+func sub(a,b int) int {
+       return a - b
+}
+
 func main() {
        num1 := 10
        num2 := 20

        sum := add(num1,num2)
+       diff := sub(num1,num2)

        fmt.Printf("The sum of %d and %d is %d\n", num1, num2, sum)
+       fmt.Printf("The difference between %d and %d is %d\n",num1, num2,diff)
}
```

修改完代码后，还需要将修改提交到本地仓库和远端仓库，在提交到远端仓库前，为了避免代码冲突，最好重新 pull 下 main 分支代码，并 rebase main 分支命令如下：

```bash
git add .
git commit -m "feate: add sub function"
[feature/add-sub-function 136fe85] feate: add sub function
 1 file changed, 6 insertions(+)
git log
commit 136fe853c806f3780eedac3fe0ea5cbaf04150de (HEAD -> feature/add-sub-function)
Author: liangning <1075090027@qq.com>
Date:   Tue May 7 00:18:39 2024 +0800

    feate: add sub function

commit cd733c59e1fa4c4e535dffe9478dbd747438b0a4 (origin/main, main)
Author: liangning <1075090027@qq.com>
Date:   Mon May 6 23:51:10 2024 +0800

    first commit
git checkout main
git pull origin main
git checkout feature/add-sub-function
git push origin feature/add-sub-function
```

之后，需要基于 `feature/add-sub-function` 分支代码构建出测试产物，例如：二进制文件或者 Docker 镜像，并部署在测试环境中进行测试。在真正的项目开发中，在我们提交分支代码后，代码托管平台通常会通过 webhook，触发 CI/CD 流水线。CI/CD 流水线会对开发分支进行单元测试、静态代码检查、单元测试覆盖率检查，并构建出 Docker 镜像等。最终，我们可以基于 Docker 镜像更新测试环境的 Image Tag，从而来测试我们的开发分支。

当我们对开发分支进行了充分的测试后，例如：单测、集成测试等，就可以向 main 分支提交 PR，供其他开发者进行代码审阅，并在审阅通过后，合入 main 分支。

## 3. 创建PR【Open a Pull Request】

![image-20240507002210696](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070022762.png)

 当完成了代码的提交之后，通常需要提交一个 Pull Request，以使其他开发者可以审阅（你的代码，并根据需要进行代码讨论。如果你的团队有一个很好的代码审阅氛围和习惯，对代码质量和个人代码能力的提升是非常有帮助的。

因为我们提交的 PR 是要供其他开发者 CR 的，为了减轻开发者的 CR 压力，并且确保 CR 能够得到有效的执行，建议在保证每次 PR 功能完整性的前提下，确保 PR 的代码量在合理的范围内。

代码 push 到远端分支后，代码托管平台（不一定是 GitHub），通常会有一个 Compare & pull request 按钮，点击这个按钮，就可以创建一个 PR：

![image-20240507002304091](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070023161.png)

创建的时候，需要指定 Reviewers，如下图所示：

![image-20240507002510973](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070025062.png)

上面的截图在创建 PR 时指定了 Reviewers 和 Assignees：

- Reviewers（被分配者）：Reviewers 是 PR 的审阅者，通常只负责代码的审阅；
- Assignees（审阅者）：Assignees 就是 PR 的负责人，负责 PR 的处理（例如根据审阅意见进行修改等）和合并。

上面，我手动指定了 Reviewers 和 Assignees。在真正的想项目开发中，通常会配置代码托管平台，使其在创建 PR 时强制要求指定 Reviewers 和 Assignees，或者指定默认的 Reviewers 和 Assignees。

## 4. 代码Review

![image-20240507002614591](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070026654.png)

当你提交 PR 之后，流程就到了 Reviewers 这里，Reviewers 会收到审阅邮件，优秀的团队他同事，会及时审阅你的代码，根据 PR 的变更，给出合适的修改建议，如果审阅通过会点击 Approve 按钮，Approve 这次 PR。当你的 PR 有一个或者多个 Approve 时，就说明 PR 通过了代码审阅。

实际工作中，Reviewers 通常不会及时关注到审阅邮件，如果 PR 比较紧急，可以聊天软件私密或者@ Reviewers，以使 PR 尽快被审阅。如果代码审阅通过，通常Reviewers 会添加一个 /lgtm类似的 Comment，说明 PR 审阅通过，例如：

![image-20240507002705338](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070027453.png)

注意，上图是 Reviewers 视角的页面。

如果审阅不通过，开发者仍然可以本地修改代码，并将变更提交为一个新的 Commit 提交到相同的代码分支，或者简单粗放使用 `git commit --amend`、`git push origin feature/add-sub-function -f`命令将变更更新到远端的 `feature/add-sub-function` 仓库。提交到远端仓库后，我们不需要重新提交 PR。

当 PR 审阅通过，并且完成了其他的合并流程，例如：单元测试、静态代码检查等，我们就可以将开发分支的代码，编译成镜像部署到生产环境中。

## 5. 部署【Deploy】

![image-20240507002927114](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070029193.png)

当我们的 PR 被 Review 通过后，我们就可以将分支代码部署到预发环境，进行灰度。如果分支发生了问题，我们仍然可以回滚到之前的状态。注意，这个时候，部署的是预发环境，PR 还没有被 Merge。

> 提示：很多团队，因为人力、基础设施成熟度等原因，很多开发者会通过灰度的方式，将现网环境当做预发环境，来进行全量发布前的生产环境验证。

## 6. 合并【Merge】

![image-20240507003004367](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070030423.png)

当我们的代码，在生产环境或者语法环境验证好之后，就可以将代码合并到 main 分支了：

![image-20240507003110545](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070031649.png)

点击上图中的 Merge pull request，可以将分支代码合入 main 分支。在合入时，可以选择 3 种合入方式：

- Create a merge commit：GitHub 的底层操作是 git merge --no-ff。feature 分支上所有的 commit 都会加到 main 分支上，并且会生成一个 merge commit。这种方式可以让我们清晰地知道是谁做了提交，做了哪些提交，回溯历史的时候也会更加方便；
- Squash and merge：GitHub 的底层操作是 git merge --squash。Squash and merge会使该 pull request 上的所有 commit 都合并成一个commit ，然后加到main分支上，但原来的 commit 历史会丢失。如果开发人员在 feature 分支上提交的 commit 非常随意，没有规范，那么我们可以选择这种方法来丢弃无意义的 commit。但是在大型项目中，每个开发人员都应该是遵循 commit 规范的，因此我不建议你在团队开发中使用 Squash and merge；
- Rebase and merge：GitHub 的底层操作是 git rebase。这种方式会将 pull request 上的所有提交历史按照原有顺序依次添加到 main 分支的头部（HEAD）。因为git rebase 有风险，在你不完全熟悉 Git 工作流时，我不建议merge时选择这个。

通过分析每个方法的优缺点，在实际的项目开发中，我比较推荐你使用 Create a merge commit 方式。

合并之后，PR 流程就完全结束了，PR 最终状态为：Merged，如下图所示：

![image-20240507003148443](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070031554.png)

> 提示：可以通过将某些关键字加入到 PR 描述中，这可以将问题与代码关联起来。当 PR 被合并时，相关问题也将被关闭。例如，输入短语 Closes #32，将关闭仓库中序号为 32 的问题。

通常情况下，我们的合并是没有冲突的，这种情况下因为合并带来的风险是可以接受的。但是，如果合并时有冲突。这种情况下，建议在开发分支，先 rebase main 分支，解决完冲突，经过测试后，再合并到 main 分支。

## 7. 部署到生产环境

合并到 main 分支的代码，是经过测试、验证的代码，所以可以直接部署到生产环境。部署到生产环境，你可以手动构建镜像、手动部署，或者借助 CI/CD 流程自动部署。

## 8. 删除分支【Delete your branch】

最后，我们可以更具需要选择是否删除这个开发分支。通常，我们在 Merge 完 PR 后，会删除开发分支，防止过多的没有意义的开发分支，干扰 Git 仓库，并防止他人意外使用旧分支。



# GitHub Flow 优缺点

从上面的学习和实战，我们可以知道 GitHub Flow 很流程简洁，没有 Git Flow 的 develop、release分支，并将 feature和 fix合并为一个分支，也即开发分支。其实，GitHub Flow 是不区分功能分支和修复分支的。所有的功能都在一个叫开发分支的分支中完成，使用起来非常简单、易理解。

![image-20240507003344441](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070033491.png)

另外，GitHub Flow 支持部署，所以 GitHub Flow 对 CI/CD 友好，可以借助 CI/CD 支持频繁的代码变更和发布。

因为 GitHub Flow 不支持 release分支，所以，其对多个版本需求同时迭代的支持非常弱。另外，因为没有一个专门用来测试的分支，所以当代码通过 PR 合并到 main 时，需要确保开发分支的代码经过充分的测试和验证。如果开发分支，没有经过充分的测试，仅靠代码审阅，通过后直接合入 main，可能会给 main 引入不稳定的代码。


# 一些技巧

## 保护main分支

main 分支因为保存了生产环境的最新代码，所以 main 应该被安全的保护起来，防止一些误操作或者恶意操作。

在真实的项目开发中， 通常都会通过代码托管平台的 main 分支保护功能，将 main 分支保护起来。例如：可以在 GitHub 平台的 Settings -> Branches -> Branch protection rules 中将 main 分支保护起来：

![image-20240507003852886](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070038982.png)

## 用 Issue 来追踪 Bug

Issue 用于 Bug 追踪和需求管理。建议先新建 Issue，再新建对应的功能分支。功能分支总是为了解决一个或多个 Issue。
功能分支的名称，可以与 issue 的名字保持一致，并且以 issue 的编号起首，比如 15-require-a-password-to-change-it

![image-20240507003949786](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070039838.png)

开发完成后，在提交说明里面，可以写 fixes #14 或者 closes #67。GitHub规定，只要 commit message 里面有下面这些动词 + 编号，就会关闭对应的issue。

- close
- closes
- closed
- fix
- fixes
- fixed
- resolve
- resolves
- resolved

这种方式还可以一次关闭多个 issue，或者关闭其他代码库的 issue，格式是 `username/repository#issue_number`。
Pull Request 被接受以后，issue 会被自动关闭，原始分支就应该删除。如果以后该issue重新打开，新分支可以复用原来的名字。

## Merge 节点

Git 有两种合并：一种是 直进式合并（fast forward），不生成单独的合并节点；另一种是非直进式合并（none fast-forword），会生成单独节点。前者不利于保持 commit 信息的清晰，也不利于以后的回滚，建议总是采用后者（即使用 --no-ff 参数）。只要发生合并，就要有一个单独的合并节点。

## Squash 多个commit

为了便于他人阅读你的提交，也便于 cherry-pick 或撤销代码变化，在发起 Pull Request 之前，应该把多个 commit 合并成一个。（前提是，该分支只有你一个人开发，且没有跟 master 合并过。）

![img](https://gitee.com/liangningi/typora_picture/raw/master/Go/202405070041049.png)

具体可以使用 `git rebase -i`命令

## 合并完代码后，建议删除分支

当一个分支被合并后，建议删除这个分支。通常代码托管平台都有这样的设置：分支合并后，自动删除分支。及时删除分支，会带来以下好处：

1. 确保仓库中的分支都是开发中的功能，或者正在处理的 Issue，有利于保持代码仓库分支的清晰；
2. 未来如果有需要，也可以复用这个分支名字。

# 总结

GitHub Flow提供了一种轻量级、以部署为中心的工作流程，适合需要快速迭代和频繁部署的项目。它强调了代码的可部署性和团队协作，简化了分支管理，使得团队可以更加专注于改进产品。在 GitHub Flow 中，只有一个长期的分支 main，main 是 Protected，只有有权限的人才能推送代码，并合并到 main 分支。