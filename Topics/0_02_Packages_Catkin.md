# ROS пакеты

Вся экосистема ROS основывается на концепции пакетов, которые включают различные компоненты. В этом топике мы знакомимся с возможностями создания пакетов, их редактирования, а также инструментом сборки, который будет использоваться далее. Можно также подглядеть на [офф страницу =)](http://wiki.ros.org/ROS/Tutorials/CreatingPackage).

> Убедитесь, что ваша среда настроена, ваш ws инициализирован и виден в системе ROS.

Для начала перейдем в наш ws, в директорию `src`:
```bash
cd ~/catkin_ws/src
```

Для создания пакета используется команда:
```bash
catkin_create_pkg [pkg_name] [dep1 dep2 ...]
```

В данной команде первым аргументом передается имя нового пакета, после перечисляются зависимости данного пакета.
Для начала создадим пакет и добавим поддержку библиотек python и cpp (c++):
```bash
catkin_create_pkg study_pkg rospy roscpp
```

Далее наблюдаем созданную папку `study_pkg` и два главных файла внутри: `CMakeLists.txt` и `package.xml`

##### > Создайте пакет `study_pkg` и просмотрите файлы `CMakeLists.txt` и `package.xml`

В содержании `package.xml` можно выделить основные блоки:  

Заголовок, в нем содержится основная инфа о пакете
```xml
<?xml version="1.0"?>
<package format="2">
  <name>study_pkg</name>
  <version>0.0.0</version>
  <description>The study_pkg package</description>
  <maintainer email="user@todo.todo">user</maintainer>
  <license>TODO</license>
```

Зависимости (инструмент сборки, сборка, экспорт, runtime (exec) - выполнение)
```xml
  <buildtool_depend>catkin</buildtool_depend>
  <build_depend>roscpp</build_depend>
  <build_depend>rospy</build_depend>
  <build_export_depend>roscpp</build_export_depend>
  <build_export_depend>rospy</build_export_depend>
  <exec_depend>roscpp</exec_depend>
  <exec_depend>rospy</exec_depend>
```

Остальное можно также наблюдать внутри комметариев формата xml. Там также приведены некоторые описания строк и блоков.

Внутри `CMakeLists.txt` можно также видеть много закомментированных блоков, но в-основном можно вытащить базовые куски на момент инициализации:  

Определение минимальной версии сборки `cmake` и название проекта
```cmake
cmake_minimum_required(VERSION 2.8.3)
project(study_pkg)
```

Поиск и подключение зависимостей
```cmake
find_package(catkin REQUIRED COMPONENTS
  roscpp
  rospy
)
```

Также создаются базовые папки `include` (для заголовочных файлов) и `src` (для файлов исходных текстов).  

А теперь сделаем пакет видимым системе ROS - для этого надо просто вызвать сборку в рабочем пространстве. Перейдите в папку рабочего пространства:
```bash
cd ~/catkin_ws
```
и выполните команду сборки:
```bash
catkin_make
```

После успешного выполнения сборки убедимся в том, что экосистема ROS видит наш пакет:
```bash
rospack list
```

В списке должна быть строка
```
study_pkg /home/user/catkin_ws/src/study_pkg
```

С помощью команды `rospack help` можно получить информацию об утилите и ее аргументах.

> Вообще рекомендую не забывать этот аргумент `help` (или опцию `-h`), так как он применим ко всем утилитам ROS экосистемы. 

##### > Произведите сборку пакетов рабочего пространства и убедитесь в том, что новый пакет виден ROS

## Разместим новый пакет в репозиторий

Для начала необходимо создать пустой репозиторий на github.com в своем аккаунте. Если чего-то не хватает - разбираемся вместе или по инструкциям из веба, их благо достаточно.

Можно глянуть как [инициализировать git с дальнейшими действиями](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/).

Теперь перейдем в папку пакета `study_pkg`:
```bash
roscd study_pkg
```

Инициализируем папку как локальный git репозиторий:
```bash
git init
```
Результат
```
Initialized empty Git repository in /home/user/catkin_ws/src/study_pkg/.git/
```

Теперь надо привязать локальный репозиторий к удаленному:
```bash
git remote add origin [Repo URL]
```

Repo URL - путь удаленного репозитория, берется со страницы репозитория из зеленой кнопки "Clone or download".
Например, для репозитория курса была использована следующая команда:
```bash
git remote add origin https://github.com/KaiL4eK/ros_course.git
```
Как видно, в пути содержится имя владельца репозитория и его названия.

Настроим, чтобы ветка `master` локального репозитория следила за веткой `master` удаленного репозитория. На удаленном ветка `master` называется `origin/master`:
```bash
git branch --set-upstream-to=origin/master master
```

Далее, стянем с удаленного репозитория все файлы, что убедиться в синхронизации удаленки и локали:
```bash
git pull
```

После этого можно учесть (индексировать) новые файлы пакета:
```bash
git add -A
```

> Опция `-A` добавляет все неучтенные файлы. Вместо нее можно просто перечислить файлы, которые необходимо добавить к учету в коммитах.

И сделать коммит в локальном репо:
```bash
git commit -am 'First commit'
```

> Опция `-a` делает `git add` ко всем изменениям учтенных (индексированных) файлов - упрощает нам задачу.
> Опция `-m` устанавливает коммент к коммиту. Коммент пишется после опции.

Если гит не хочет делать коммит и пишет
```
*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.
```
значит надо настроить данные о пользователе в соответствии с предложенной инструкцией. На VM настройка уже проведена.

После остается только закинуть все сделанные коммиты (а их пока один штука) на удаленку:
```bash
git push
```

И все! Можно смотреть на результаты на сайте!

##### > Разместите пакет на github.com

## В результате

- С помощью инструмента из набора `catkin` был создан первый пакет ROS и поверхностно рассмотрены файл системы сборки `cmake` - `CMakeLists.txt` и файл описания пакета `package.xml` в формате `XML`.
- Файл `CMakeLists.txt` будет далее основным файлом для редактирования и использования, так как именно он указывает необходимые зависимости и правила для сборки пакета; в свою очередь `package.xml` является скорее описанием пакета и требует пристального внимания при оформлении внешнего вида, что также очень важно.
- Полученный пакет будет использован в качестве основы для дальнейшего обучения, рассмотрения и тестов ROS.
- Если говорить об использовании `git`, то рекомендуется размещать в репозитории именно содержимое самого пакета (то есть того, что находится в папке src внутри catkin_ws; грубо говоря заходите в папку study_pkg и [инициализируете git с дальнейшими действиями](https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line/)), таким образом репозиторий будет представлять из себя пакет ROS, что в результате позволит легко импортировать пакет в ws на других машинах; размещение в `git` всего ws является плохой практикой и не рекомендуется к использованию.
- Познакомились с утилитами `roslist` и `roscd`.