# hw23
Домашнее задание к занятию «2.3. Ветвления в Git»
Давайте потренеруемся делать merge и rebase, чтобы понять разницу и научиться решать конфликты.

1. Задание №1 – Ветвление, merge и rebase.
Предположим, что есть задача написать скрипт, выводящий на экран параметры его запуска. Давайте посмотрим, как будет отличаться работа над этим скриптом с использованием ветвления, мержа и ребейза. Создайте в своем репозитории каталог branching и в нем два файла merge.sh и rebase.sh с содержимым:
```
#!/bin/bash
# display command line options

count=1
for param in "$*"; do
    echo "\$* Parameter #$count = $param"
    count=$(( $count + 1 ))
done
```
Этот скрипт отображает на экране все параметры одной строкой, а не разделяет их.

```
root@ovpn1:~/devops-netology/branching# vi merge.sh
root@ovpn1:~/devops-netology/branching# cp merge.sh rebase.sh
root@ovpn1:~/devops-netology/branching# ls -la
total 16
drwxr-xr-x 2 root root 4096 Aug  4 11:52 .
drwxr-xr-x 5 root root 4096 Aug  3 20:21 ..
-rw-r--r-- 1 root root  149 Aug  4 11:51 merge.sh
-rw-r--r-- 1 root root  149 Aug  4 11:52 rebase.sh
root@ovpn1:~/devops-netology/branching#
```

2. Создадим коммит с описанием prepare for merge and rebase и отправим его в ветку main.


Подготовка файла merge.sh.
1. Создайте ветку git-merge.
```
root@ovpn1:~/devops-netology#  git checkout -b git-merge
Switched to a new branch 'git-merge'
```


2. Замените в ней содержимое файла merge.sh на
```
#!/bin/bash
# display command line options

count=1
for param in "$@"; do
    echo "\$@ Parameter #$count = $param"
    count=$(( $count + 1 ))
done
```

```
root@ovpn1:~/devops-netology# vi ./branching/merge.sh
root@ovpn1:~/devops-netology# git add branching/merge.sh
root@ovpn1:~/devops-netology# git status
On branch git-merge
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   branching/merge.sh
```

3. Создайте коммит merge: @ instead * отправьте изменения в репозиторий.


```
root@ovpn1:~/devops-netology# git commit -m "merge: @ instead *"
[git-merge 90007a3] merge: @ instead *
 1 file changed, 2 insertions(+), 2 deletions(-)
root@ovpn1:~/devops-netology# git push -u origin git-merge
Username for 'https://github.com': vladyezh@gmail.com
Password for 'https://vladyezh@gmail.com@github.com':
Counting objects: 4, done.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 383 bytes | 383.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/vldzh/devops-netology.git
   6c5f9e0..90007a3  git-merge -> git-merge
Branch 'git-merge' set up to track remote branch 'git-merge' from 'origin'.
```


4. И разработчик подумал и решил внести еще одно изменение в merge.sh
```
#!/bin/bash
# display command line options

count=1
while [[ -n "$1" ]]; do
    echo "Parameter #$count = $1"
    count=$(( $count + 1 ))
    shift
done
```
Теперь скрипт будет отображать каждый переданный ему параметр отдельно.
```
root@ovpn1:~/devops-netology# cat << 'EOF' > branching/merge.sh
> #!/bin/bash
> # display command line options
>
> count=1
> while [[ -n "$1" ]]; do
>     echo "Parameter #$count = $1"
>     count=$(( $count + 1 ))
>     shift
> done
> EOF
root@ovpn1:~/devops-netology# git status
On branch git-merge
Your branch is up to date with 'origin/git-merge'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   branching/merge.sh

no changes added to commit (use "git add" and/or "git commit -a")
```


5. Создайте коммит merge: use shift и отправьте изменения в репозиторий.
```
root@ovpn1:~/devops-netology# git add branching/merge.sh
root@ovpn1:~/devops-netology# git commit -m "merge: use shift"
[git-merge 0aedaeb] merge: use shift
 1 file changed, 3 insertions(+), 2 deletions(-)
root@ovpn1:~/devops-netology# git push origin git-merge
Username for 'https://github.com': vladyezh@gmail.com
Password for 'https://vladyezh@gmail.com@github.com':
Counting objects: 4, done.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 453 bytes | 453.00 KiB/s, done.
Total 4 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/vldzh/devops-netology.git
   90007a3..0aedaeb  git-merge -> git-merge
root@ovpn1:~/devops-netology#
```

Изменим main.
1. Вернитесь в ветку main.
```
root@ovpn1:~/devops-netology# git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
root@ovpn1:~/devops-netology#
```
2. Предположим, что кто-то, пока мы работали над веткой git-merge, изменил main. Для этого изменим содержимое файла rebase.sh на следующее
```
#!/bin/bash
# display command line options

count=1
for param in "$@"; do
    echo "\$@ Parameter #$count = $param"
    count=$(( $count + 1 ))
done

echo "====="
```

В этом случае скрипт тоже будет отображать каждый параметр в новой строке.

```
root@ovpn1:~/devops-netology# cat << 'EOF' > branching/rebase.sh
> #!/bin/bash
> # display command line options
>
> count=1
> for param in "$@"; do
>     echo "\$@ Parameter #$count = $param"
>     count=$(( $count + 1 ))
> done
>
> echo "====="
> EOF
```
3. Отправляем измененную ветку main в репозиторий.
```
root@ovpn1:~/devops-netology# git commit -m "rebase: @ instead *"
[main ea74438] rebase: @ instead *
 1 file changed, 4 insertions(+), 2 deletions(-)
root@ovpn1:~/devops-netology# git push
Username for 'https://github.com': vladyezh@gmail.com
Password for 'https://vladyezh@gmail.com@github.com':
Counting objects: 4, done.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 392 bytes | 392.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/vldzh/devops-netology.git
   6c5f9e0..ea74438  main -> main
root@ovpn1:~/devops-netology#
```

Подготовка файла rebase.sh.

1. Предположим, что теперь другой участник нашей команды не сделал git pull, либо просто хотел ответвиться не от последнего коммита в main, а от коммита когда мы только создали два файла merge.sh и rebase.sh на первом шаге.
Для этого при помощи команды git log найдем хэш коммита prepare for merge and rebase и выполним git checkout на него примерно так: git checkout 8baf217e80ef17ff577883fda90f6487f67bbcea (хэш будет другой).
```
root@ovpn1:~/devops-netology# git log --oneline | grep 'prepare for merge and rebase'
6c5f9e0 prepare for merge and rebase
root@ovpn1:~/devops-netology# git  checkout 6c5f9e0
Note: checking out '6c5f9e0'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 6c5f9e0 prepare for merge and rebase
```

2. Создадим ветку git-rebase основываясь на текущем коммите.
```
root@ovpn1:~/devops-netology# git checkout -b git-rebase
Switched to a new branch 'git-rebase'
```

3. И изменим содержимое файла rebase.sh на следующее, тоже починив скрипт, но немного в другом стиле
```
#!/bin/bash
# display command line options

count=1
for param in "$@"; do
    echo "Parameter: $param"
    count=$(( $count + 1 ))
done

echo "====="
```

```
root@ovpn1:~/devops-netology# cat << 'EOF' > branching/rebase.sh
> #!/bin/bash
> # display command line options
>
> count=1
> for param in "$@"; do
>     echo "Parameter: $param"
>     count=$(( $count + 1 ))
> done
>
> echo "====="
> EOF

```
4. Отправим эти изменения в ветку git-rebase, с комментарием git-rebase 1.
```
root@ovpn1:~/devops-netology# git add branching/rebase.sh
root@ovpn1:~/devops-netology# git commit -m "git-rebase 1"
[git-rebase 9540afb] git-rebase 1
 1 file changed, 4 insertions(+), 2 deletions(-)
```
5. И сделаем еще один коммит git-rebase 2 с пушем заменив echo "Parameter: $param" на echo "Next parameter: $param".
```
root@ovpn1:~/devops-netology# git commit -m "git-rebase 2"
[git-rebase 8527ecd] git-rebase 2
 1 file changed, 1 insertion(+), 1 deletion(-)
root@ovpn1:~/devops-netology# git checkout  git-rebase
Switched to branch 'git-rebase'
root@ovpn1:~/devops-netology#  git push origin git-rebase
Username for 'https://github.com': vladyezh@gmail.com
Password for 'https://vladyezh@gmail.com@github.com':
Counting objects: 8, done.
Compressing objects: 100% (8/8), done.
Writing objects: 100% (8/8), 761 bytes | 761.00 KiB/s, done.
Total 8 (delta 3), reused 0 (delta 0)
remote: Resolving deltas: 100% (3/3), completed with 1 local object.
remote:
remote: Create a pull request for 'git-rebase' on GitHub by visiting:
remote:      https://github.com/vldzh/devops-netology/pull/new/git-rebase
remote:
To https://github.com/vldzh/devops-netology.git

```




Промежуточный итог.

Мы сэмулировали типичную ситуации в разработке кода, когда команда разработчиков работала над одним и тем же участком кода, причем кто-то из разработчиков предпочитаем делать merge, а кто-то rebase. Конфилкты с merge обычно решаются достаточно просто, а с rebase бывают сложности, поэтому давайте смержим все наработки в main и разрешим конфликты.

Если все было сделано правильно, то на странице network в гитхабе, находящейся по адресу https://github.com/ВАШ_ЛОГИН/ВАШ_РЕПОЗИТОРИЙ/network будет примерно такая схема: Созданы обе ветки

Merge
Сливаем ветку git-merge в main и отправляем изменения в репозиторий, должно получиться без конфликтов:
```
$ git merge git-merge
Merge made by the 'recursive' strategy.
 branching/merge.sh | 5 +++--
 1 file changed, 3 insertions(+), 2 deletions(-)
$ git push
#!/bin/bash
Enumerating objects: 1, done.
Counting objects: 100% (1/1), done.
Writing objects: 100% (1/1), 223 bytes | 223.00 KiB/s, done.
Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
```

В результате получаем такую схему: Первый мерж

Rebase

1.А перед мержем ветки git-rebase выполним ее rebase на main. Да, мы специально создали ситуацию с конфликтами, чтобы потренироваться их решать.
2.Переключаемся на ветку git-rebase и выполняем git rebase -i main. В открывшемся диалоге должно быть два выполненных нами коммита, давайте заодно объединим их в один, указав слева от нижнего fixup. В результате получаем что-то подобное:
```
$ git rebase -i main
Auto-merging branching/rebase.sh
CONFLICT (content): Merge conflict in branching/rebase.sh
error: could not apply dc4688f... git 2.3 rebase @ instead *
Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".
Could not apply dc4688f... git 2.3 rebase @ instead *
```

```
root@ovpn1:~/devops-netology# git checkout git-rebase
Switched to branch 'git-rebase'
root@ovpn1:~/devops-netology# git branch
  git-merge
* git-rebase
  main
root@ovpn1:~/devops-netology# git rebase -i main
Auto-merging branching/rebase.sh
CONFLICT (content): Merge conflict in branching/rebase.sh
error: could not apply 9540afb... git-rebase 1

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".

Could not apply 9540afb... git-rebase 1
```



Если посмотреть содержимое файла rebase.sh, то увидим метки, оставленные гитом для решения конфликта:
```
cat rebase.sh
#!/bin/bash
# display command line options
count=1
for param in "$@"; do
<<<<<<< HEAD
    echo "\$@ Parameter #$count = $param"
=======
    echo "Parameter: $param"
>>>>>>> dc4688f... git 2.3 rebase @ instead *
    count=$(( $count + 1 ))
done
```

```
root@ovpn1:~/devops-netology# cat branching/rebase.sh
#!/bin/bash
# display command line options

count=1
for param in "$@"; do
<<<<<<< HEAD
    echo "\$@ Parameter #$count = $param"
=======
    echo "Parameter: $param"
>>>>>>> 9540afb... git-rebase 1
    count=$(( $count + 1 ))
done

echo "====="
```



Удалим метки, отдав предпочтение варианту
```
echo "\$@ Parameter #$count = $param"
```

```
root@ovpn1:~/devops-netology# vi branching/rebase.sh
root@ovpn1:~/devops-netology# cat branching/rebase.sh
#!/bin/bash
# display command line options

count=1
for param in "$@"; do
    echo "\$@ Parameter #$count = $param"
    count=$(( $count + 1 ))
done

echo "====="
```

сообщим гиту, что конфликт решен git add rebase.sh и продолжим ребейз git rebase --continue.
```
root@ovpn1:~/devops-netology# git status branching/rebase.sh
interactive rebase in progress; onto 2d34f0a
Last command done (1 command done):
   pick 9540afb git-rebase 1
Next command to do (1 remaining command):
   pick 8527ecd git-rebase 2
  (use "git rebase --edit-todo" to view and edit)
You are currently rebasing branch 'git-rebase' on '2d34f0a'.
  (fix conflicts and then run "git rebase --continue")
  (use "git rebase --skip" to skip this patch)
  (use "git rebase --abort" to check out the original branch)

Unmerged paths:
  (use "git reset HEAD <file>..." to unstage)
  (use "git add <file>..." to mark resolution)

        both modified:   branching/rebase.sh

no changes added to commit (use "git add" and/or "git commit -a")
```


И опять в получим конфликт в файле rebase.sh при попытке применения нашего второго коммита. Давайте разрешим конфликт, оставив строчку echo "Next parameter: $param".
```
root@ovpn1:~/devops-netology# git add  branching/rebase.sh
root@ovpn1:~/devops-netology# git rebase --continue
Auto-merging branching/rebase.sh
CONFLICT (content): Merge conflict in branching/rebase.sh
error: could not apply 8527ecd... git-rebase 2

Resolve all conflicts manually, mark them as resolved with
"git add/rm <conflicted_files>", then run "git rebase --continue".
You can instead skip this commit: run "git rebase --skip".
To abort and get back to the state before "git rebase", run "git rebase --abort".

Could not apply 8527ecd... git-rebase 2
root@ovpn1:~/devops-netology# cat branching/rebase.sh
#!/bin/bash
# display command line options

count=1
for param in "$@"; do
<<<<<<< HEAD
    echo "\$@ Parameter #$count = $param"
=======
    echo "Next parameter: $param"
>>>>>>> 8527ecd... git-rebase 2
    count=$(( $count + 1 ))
done

echo "====="
root@ovpn1:~/devops-netology# vi branching/rebase.sh
root@ovpn1:~/devops-netology# cat branching/rebase.sh
#!/bin/bash
# display command line options

count=1
for param in "$@"; do
    echo "Next parameter: $param"
    count=$(( $count + 1 ))
done

echo "====="
```



Далее опять сообщаем гиту о том, что конфликт разрешен git add rebase.sh и продолжим ребейз git rebase --continue. В результате будет открыт текстовый редактор предлагающий написать комментарий к новому объединенному коммиту:
```
# This is a combination of 2 commits.
# This is the 1st commit message:

Merge branch 'git-merge'

# The commit message #2 will be skipped:

# git 2.3 rebase @ instead * (2)
```

Все строчки начинающиеся на # будут проигнорированны.

После сохранения изменения, гит сообщит

Successfully rebased and updated refs/heads/git-rebase

```
root@ovpn1:~/devops-netology# git add  branching/rebase.sh
root@ovpn1:~/devops-netology# git rebase --continue
[detached HEAD 683cf76] git-rebase 2
 1 file changed, 1 insertion(+), 1 deletion(-)
Successfully rebased and updated refs/heads/git-rebase.
```


И попробуем выполнить git push, либо git push -u origin git-rebase чтобы точно указать что и куда мы хотим запушить. Эта команда завершится с ошибкой:
```
git push
To github.com:andrey-borue/devops-netology.git
 ! [rejected]        git-rebase -> git-rebase (non-fast-forward)
error: failed to push some refs to 'git@github.com:andrey-borue/devops-netology.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

```
root@ovpn1:~/devops-netology#  git push
Username for 'https://github.com': vladyezh@gmail.com
Password for 'https://vladyezh@gmail.com@github.com':
Counting objects: 4, done.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 379 bytes | 379.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/vldzh/devops-netology.git
 ! [rejected]        git-rebase -> git-rebase (non-fast-forward)
error: failed to push some refs to 'git@github.com:andrey-borue/devops-netology.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.

```


Это произошло, потому что мы пытаемся перезаписать историю. Чтобы гит позволил нам это сделать, давайте добавим флаг force:
```
git push -u origin git-rebase -f
Enumerating objects: 10, done.
Counting objects: 100% (9/9), done.
Delta compression using up to 12 threads
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 443 bytes | 443.00 KiB/s, done.
Total 4 (delta 1), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To github.com:andrey-borue/devops-netology.git
 + 1829df1...e3b942b git-rebase -> git-rebase (forced update)
Branch 'git-rebase' set up to track remote branch 'git-rebase' from 'origin'.
```

Теперь можно смержить ветку git-rebase в main без конфликтов и без дополнительного мерж-комита простой перемоткой.
```
root@ovpn1:~/devops-netology# git push -u origin git-rebase -f
Username for 'https://github.com': vladyezh@gmail.com
Password for 'https://vladyezh@gmail.com@github.com':
Counting objects: 4, done.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (4/4), 379 bytes | 379.00 KiB/s, done.
Total 4 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To https://github.com/vldzh/devops-netology.git
 + 8527ecd...683cf76 git-rebase -> git-rebase (forced update)
Branch 'git-rebase' set up to track remote branch 'git-rebase' from 'origin'.
```

```
$ git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
```

```
root@ovpn1:~/devops-netology# git checkout main
Switched to branch 'main'
Your branch is up to date with 'origin/main'.
```

```
$ git merge git-rebase
Updating 6158b76..45893d1
Fast-forward
 branching/rebase.sh | 3 +--
 1 file changed, 1 insertion(+), 2 deletions(-)
```

```
root@ovpn1:~/devops-netology# git merge git-rebase
Updating 2d34f0a..683cf76
Fast-forward
 branching/rebase.sh | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
root@ovpn1:~/devops-netology# git log --oneline --graph
* 683cf76 (HEAD -> main, origin/git-rebase, git-rebase) git-rebase 2
*   2d34f0a (origin/main, origin/HEAD) Merge branch 'git-merge' into main
|\
| * 0aedaeb (origin/git-merge, git-merge) merge: use shift
| * 90007a3 merge: @ instead *
* | ea74438 rebase: @ instead *
|/
* 6c5f9e0 prepare for merge and rebase
* 414f8a7 Update README.md
* 1952309 Update README.md
* 9d2459a Update README.md
* 989e40b Update README.md
* 527c97c Update README.md
* c515289 Moved and deleted
* fdf31c9 Prepare to delete and move
* fec6a77 Added gitignore
* fdae7e1 First commit
* a4ee496 Update README.md
* 54ba6c6 Update README.md
* f49bc1d Initial commit
```
