# Домашнее задание к занятию " `Введение в Terraform` " - `Сулименков Алексей`

---

## Задание 1

1. Перейдите в каталог src. Скачайте все необходимые зависимости, использованные в проекте.
2. Изучите файл .gitignore. В каком terraform-файле, согласно этому .gitignore, допустимо сохранить личную, секретную информацию?(логины,пароли,ключи,токены итд)
3. Выполните код проекта. Найдите в state-файле секретное содержимое созданного ресурса random_password, пришлите в качестве ответа конкретный ключ и его значение.
4. Раскомментируйте блок кода, примерно расположенный на строчках 29–42 файла main.tf. Выполните команду terraform validate. Объясните, в чём заключаются намеренно допущенные ошибки. Исправьте их.
5. Выполните код. В качестве ответа приложите: исправленный фрагмент кода и вывод команды docker ps.
6. Замените имя docker-контейнера в блоке кода на hello_world. Не перепутайте имя контейнера и имя образа. Мы всё ещё продолжаем использовать name = "nginx:latest". Выполните команду terraform apply -auto-approve. Объясните своими словами, в чём может быть опасность применения ключа -auto-approve. Догадайтесь или нагуглите зачем может пригодиться данный ключ? В качестве ответа дополнительно приложите вывод команды docker ps.
7. Уничтожьте созданные ресурсы с помощью terraform. Убедитесь, что все ресурсы удалены. Приложите содержимое файла terraform.tfstate.

Объясните, почему при этом не был удалён docker-образ nginx:latest. Ответ ОБЯЗАТЕЛЬНО НАЙДИТЕ В ПРЕДОСТАВЛЕННОМ КОДЕ, а затем ОБЯЗАТЕЛЬНО ПОДКРЕПИТЕ строчкой из документации terraform провайдера docker. (ищите в классификаторе resource docker_image )

### Ответ

2. Согласнов .gitignore допустимо сохранить личную, секретную информацию в `personal.auto.tfvars`
3. Cодержимое созданного ресурса random_password `"result": "yyo6y3Svk0nKBUH0"`
4. Неописано уникальное имя в текущем проекте (docker_image) `Error: Missing name for resource`. Так же `random_string_FAKE` не был объявлен в корневом модуле.

<details> <summary>Исправленные значения</summary>

```bash

resource "docker_image" "nginx"{
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

</details>

5.

<details> <summary>docker ps</summary>

![task1](https://github.com/biparasite/TER-HW01/blob/main/task_1.1.png "task1")

</details>

<details> <summary>Log terraform apply</summary>

```bash
random_password.random_string: Refreshing state... [id=none]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:

- create

Terraform will perform the following actions:

\# docker_container.nginx will be created

- resource "docker_container" "nginx" {

  - attach = false
  - bridge = (known after apply)
  - command = (known after apply)
  - container_logs = (known after apply)
  - container_read_refresh_timeout_milliseconds = 15000
  - entrypoint = (known after apply)
  - env = (known after apply)
  - exit_code = (known after apply)
  - hostname = (known after apply)
  - id = (known after apply)
  - image = (known after apply)
  - init = (known after apply)
  - ipc_mode = (known after apply)
  - log_driver = (known after apply)
  - logs = false
  - must_run = true
  - name = (sensitive value)
  - network_data = (known after apply)
  - read_only = false
  - remove_volumes = true
  - restart = "no"
  - rm = false
  - runtime = (known after apply)
  - security_opts = (known after apply)
  - shm_size = (known after apply)
  - start = true
  - stdin_open = false
  - stop_signal = (known after apply)
  - stop_timeout = (known after apply)
  - tty = false
  - wait = false
  - wait_timeout = 60

  - healthcheck (known after apply)

  - labels (known after apply)

  - ports { + external = 9090 + internal = 80 + ip = "0.0.0.0" + protocol = "tcp"
    }
    }

\# docker_image.nginx will be created

- resource "docker_image" "nginx" {
  - id = (known after apply)
  - image_id = (known after apply)
  - keep_locally = true
  - name = "nginx:latest"
  - repo_digest = (known after apply)
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
Terraform will perform the actions described above.
Only 'yes' will be accepted to approve.

Enter a value: yes

docker_image.nginx: Creating...
docker_image.nginx: Still creating... [10s elapsed]
docker_image.nginx: Creation complete after 19s [id=sha256:1f94323bafb2ac98d25b664b8c48b884a8db9db3d9c98921b3b8ade588b2e676nginx:latest]
docker_container.nginx: Creating...
docker_container.nginx: Creation complete after 1s [id=0640c8e486e83fd938a22d843d102998385e3e6382a81448993d171ff19939c9]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

```

</details>

6. Необходимо заменить в ресурсе docker_container :

```
name  = "example_${random_password.random_string.result}"
```

на

```
name  = "hello_world"
```

 <details> <summary>docker ps</summary>

![task1](https://github.com/biparasite/TER-HW01/blob/main/task_1.2.png "task1")

</details>

`-auto-approve` увеличивает риск ошибок и непредвиденных последствий, поскольку изменения применяются без подтверждения. Пригиться может только для автоматизации CI/CD пайплайнах или тестировании.

7. 
 <details> <summary>terraform.tfstate</summary>

```
{
  "version": 4,
  "terraform_version": "1.11.2",
  "serial": 14,
  "lineage": "997d2d4b-0dc7-1808-cbb2-cb8e47e5698e",
  "outputs": {},
  "resources": [],
  "check_results": null
}
```

</details>

docker_registry_image - можно использовать только для создания нового образа docker или для извлечения существующего из реестра. Описание ресурса https://registry.terraform.io/providers/kreuzwerker/docker/latest/docs/resources/image
