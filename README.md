# Домашнее задание к занятию «Введение в Terraform»

### Цели задания

1. Установить и настроить Terrafrom.
2. Научиться использовать готовый код.

------

### Чек-лист готовности к домашнему заданию

1. Скачайте и установите **Terraform** версии >=1.8.4 . Приложите скриншот вывода команды ```terraform --version```.
2. Скачайте на свой ПК этот git-репозиторий. Исходный код для выполнения задания расположен в директории **01/src**.
3. Убедитесь, что в вашей ОС установлен docker.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. Репозиторий с ссылкой на зеркало для установки и настройки Terraform: [ссылка](https://github.com/netology-code/devops-materials).
2. Установка docker: [ссылка](https://docs.docker.com/engine/install/ubuntu/). 
------
### Внимание!! Обязательно предоставляем на проверку получившийся код в виде ссылки на ваш github-репозиторий!
------

### Задание 1

1. Перейдите в каталог [**src**](https://github.com/netology-code/ter-homeworks/tree/main/01/src). Скачайте все необходимые зависимости, использованные в проекте. 
2. Изучите файл **.gitignore**. В каком terraform-файле, согласно этому .gitignore, допустимо сохранить личную, секретную информацию?(логины,пароли,ключи,токены итд)
3. Выполните код проекта. Найдите  в state-файле секретное содержимое созданного ресурса **random_password**, пришлите в качестве ответа конкретный ключ и его значение.
4. Раскомментируйте блок кода, примерно расположенный на строчках 29–42 файла **main.tf**.
Выполните команду ```terraform validate```. Объясните, в чём заключаются намеренно допущенные ошибки. Исправьте их.
5. Выполните код. В качестве ответа приложите: исправленный фрагмент кода и вывод команды ```docker ps```.
6. Замените имя docker-контейнера в блоке кода на ```hello_world```. Не перепутайте имя контейнера и имя образа. Мы всё ещё продолжаем использовать name = "nginx:latest". Выполните команду ```terraform apply -auto-approve```.
Объясните своими словами, в чём может быть опасность применения ключа  ```-auto-approve```. Догадайтесь или нагуглите зачем может пригодиться данный ключ? В качестве ответа дополнительно приложите вывод команды ```docker ps```.
8. Уничтожьте созданные ресурсы с помощью **terraform**. Убедитесь, что все ресурсы удалены. Приложите содержимое файла **terraform.tfstate**. 
9. Объясните, почему при этом не был удалён docker-образ **nginx:latest**. Ответ **ОБЯЗАТЕЛЬНО НАЙДИТЕ В ПРЕДОСТАВЛЕННОМ КОДЕ**, а затем **ОБЯЗАТЕЛЬНО ПОДКРЕПИТЕ** строчкой из документации [**terraform провайдера docker**](https://docs.comcloud.xyz/providers/kreuzwerker/docker/latest/docs).  (ищите в классификаторе resource docker_image )

## Ответ на задание 1

1. Сохранять личную и секретную информацию согласно файлу .gitignore можно в файле personal.auto.tfvars.
2.  ![alt text](https://github.com/VN351/ter_hw_01/raw/main/images/task-1-1.png)
3.  ![alt text](https://github.com/VN351/ter_hw_01/raw/main/images/task-1-2.png)
    Первая ошибка указывает на то, что в блоке docker_images отсутствует второе имя, которое необходимо для идентификации ресурса. В Terraform каждый ресурс должен иметь два идентификатора: тип ресурса и его уникальное имя внутри конфигурации.
    Вторая ошибка - имя ресурса 1nginx начинается с цифры, что не допустимо в Terraform. В названиях ресурсов должны использоваться только буквы, цифры, подчеркивания и дефисы, при этом имя должно начинаться с буквы или подчеркивания.
    ![alt text](https://github.com/VN351/ter_hw_01/raw/main/images/task-1-3.png)
    Третья ошибка - неверное обращение к ресурсу random password    
4.  Фрагмент кода
      ```
      resource "docker_image" "nginx" {
      name         = "nginx:latest"
      keep_locally = true
      }

      resource "docker_container" "nginx" {
        image = docker_image.nginx.image_id
        name  = "example_${random_password.random_string.result}"

        ports {
          internal = 80
          external = 9090
        }
      }
      ```
    ![alt text](https://github.com/VN351/ter_hw_01/raw/main/images/task-1-4.png)  
5.  Ключ -auto-approve автоматически подтверждает все действия, которые Terraform собирается выполнить. Исходя их этого можно сделать вывод, что опасность заклбчается в том, что любые изменения в коде будут применяться без подтверждения, которые могут сказаться негативно на инфраструктуре.
    Ключ -auto-approve может помочь при интеграции Terraform в pipeline, где требуется беспрепятственное применение изменений, а также когда вы уверены в конфигурации и хотите избежать интерактивных подтверждений для ускорения процессов.
    ![alt text](https://github.com/VN351/ter_hw_01/raw/main/images/task-1-5.png)
7.  ```
    {
      "version": 4,
      "terraform_version": "1.10.3",
      "serial": 40,
      "lineage": "0824d40c-4248-e385-229f-859782e9c36a",
      "outputs": {},
      "resources": [],
      "check_results": null
    }
    ```
8.  Согласно документации keep_locally: Если true, то образ Docker не будет удален при операции уничтожения. Если false, то образ будет удален из локального хранилища docker при операции уничтожения.
------

## Дополнительное задание (со звёздочкой*)

**Настоятельно рекомендуем выполнять все задания со звёздочкой.** Они помогут глубже разобраться в материале.   
Задания со звёздочкой дополнительные, не обязательные к выполнению и никак не повлияют на получение вами зачёта по этому домашнему заданию. 

### Задание 2*

1. Создайте в облаке ВМ. Сделайте это через web-консоль, чтобы не слить по незнанию токен от облака в github(это тема следующей лекции). Если хотите - попробуйте сделать это через terraform, прочитав документацию yandex cloud. Используйте файл ```personal.auto.tfvars``` и гитигнор или иной, безопасный способ передачи токена!
2. Подключитесь к ВМ по ssh и установите стек docker.
3. Найдите в документации docker provider способ настроить подключение terraform на вашей рабочей станции к remote docker context вашей ВМ через ssh.
4. Используя terraform и  remote docker context, скачайте и запустите на вашей ВМ контейнер ```mysql:8``` на порту ```127.0.0.1:3306```, передайте ENV-переменные. Сгенерируйте разные пароли через random_password и передайте их в контейнер, используя интерполяцию из примера с nginx.(```name  = "example_${random_password.random_string.result}"```  , двойные кавычки и фигурные скобки обязательны!) 
```
    environment:
      - "MYSQL_ROOT_PASSWORD=${...}"
      - MYSQL_DATABASE=wordpress
      - MYSQL_USER=wordpress
      - "MYSQL_PASSWORD=${...}"
      - MYSQL_ROOT_HOST="%"
```

6. Зайдите на вашу ВМ , подключитесь к контейнеру и проверьте наличие секретных env-переменных с помощью команды ```env```. Запишите ваш финальный код в репозиторий.

## Ответ на задание 2
1.  [Ссылка на Terraform создания VM](https://github.com/VN351/vm-sql-tf/tree/master/create-vm) <br>
    [Ссылка на Terraform разворачивания SQL](https://github.com/VN351/vm-sql-tf/tree/master/deploy-mysql)
2.  ![alt text](https://github.com/VN351/ter_hw_01/raw/main/images/task-2-1.png)

### Задание 3*
1. Установите [opentofu](https://opentofu.org/)(fork terraform с лицензией Mozilla Public License, version 2.0) любой версии
2. Попробуйте выполнить тот же код с помощью ```tofu apply```, а не terraform apply.
------

### Правила приёма работы

Домашняя работа оформляется в отдельном GitHub-репозитории в файле README.md.   
Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

### Критерии оценки

Зачёт ставится, если:

* выполнены все задания,
* ответы даны в развёрнутой форме,
* приложены соответствующие скриншоты и файлы проекта,
* в выполненных заданиях нет противоречий и нарушения логики.

На доработку работу отправят, если:

* задание выполнено частично или не выполнено вообще,
* в логике выполнения заданий есть противоречия и существенные недостатки. 

