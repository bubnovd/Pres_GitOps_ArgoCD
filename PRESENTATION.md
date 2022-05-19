class: title, middle, center
<!-- css classes -->

# GitOps & Argo Stack

<!-- Notes -->
???
- gitops
- argostack
- argocd
- demo
- flux
- flagger

---
## Кто я
----

<h3>
<table>
  <tr>
    <td><span style="font-size: 1em;"><i class="fas fa-user fa-fw"></i></span></td>
    <td>Дмитрий Бубнов</td>
  </tr>
  <tr>
    <td><i class="fas fa-home fa-fw"></i></td>
    <td>Челябинск, Бишкек</td>
  </tr>
  <tr>
    <td><i class="fas fa-graduation-cap fa-fw"></i></td>
    <td>Тренер Mikrotik</td>
  </tr>
  <tr>
    <td><i class="fas fa-award fa-fw"></i></td>
    <td>Mikrotik Certified, CCNA R&S, AWS Certified Solutions Architect - Associate</td>
  </tr>
  <tr>
    <td><i class="fas fa-network-wired fa-fw"></i></td>
    <td>Сети</td>
  </tr>
  <tr>
    <td><i class="fas fa-chart-line fa-fw"></i></td>
    <td>DevOps</td>
  </tr>
</table>
</h3>

---
![Sysadminka](img/sysadminka.png)

---
## <i class="fas fa-table fa-fw"></i> О чем будем говорить
----

- ### GitOps
- ### ArgoStack
- ### ArgoCD
- ### ArgoWorkflows
- ### Demo


---
## <img src="img/GitHub-Mark-Light-64px.png" alt="Github-logo" width="70"/> GitOps
----
- Первое упоминание в 2017 в контексте Kubernetes и CD
- Равитие IaC
- Best practices from git to deploy, manage and monitoring

???
From [here](https://www.weave.works/technologies/gitops/)

---
## <img src="img/GitHub-Mark-Light-64px.png" alt="Github-logo" width="70"/> GitOps
----
- Flux
- ArgoCD

---
## <img src="img/argo-stacked-color.png" alt="argo-logo" width="70"/> ArgoStack
----
- ArgoCD
- ArgoWorkflow
- ArgoEvents
- ArgoRollouts

---
## <img src="img/argo-stacked-color.png" alt="argo-logo" width="70"/> Resources
----
- Application
- Workflow / WorkflowTemplate
- Sensor / EventSource
- Rollout

---
## <img src="img/argo-stacked-color.png" alt="argo-logo" width="70"/> ArgoCD
----

- Manual or automatic deployment of applications to a Kubernetes cluster
- Automatic synchronization of application state to the current version of declarative configuration
- Web user interface and command-line interface (CLI)
- Ability to visualize deployment issues, detect and remediate configuration drift
- Role-based access control (RBAC) enabling multi-cluster management
- Single sign-on (SSO) with providers such as GitLab, GitHub, Microsoft, OAuth2, OIDC, LinkedIn, LDAP, and SAML 2.0
- ??? Support for webhooks triggering actions in GitLab, GitHub, and BitBucket
- Plugins


---
## <img src="img/argo-stacked-color.png" alt="argo-logo" width="70"/> Приложение
----
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: prometheus-demo8
  namespace: argocd
spec:
  destination:
    name: 'yc-demo-cluster'
    namespace: prometheus
  source:
    repoURL: 'https://github.com/REPONAME'
    path: helm/demo8.cluster/argo-cd/prometheus
    targetRevision: HEAD 
    helm:
      valueFiles:
      - values-ex.yaml
  project: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```
---
## <img src="img/argo-stacked-color.png" alt="argo-logo" width="70"/> Приложение с Vault
----
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: prometheus-demo8
  namespace: argocd
spec:
  destination:
    name: 'yc-demo-cluster'
    namespace: prometheus
  source:
    repoURL: 'https://github.com/REPONAME'
    path: helm/demo8.cluster/argo-cd/prometheus
    targetRevision: HEAD 
    plugin:
      name: argocd-vault-plugin-helm
      env:
      - name: HELM_RELEASE_NAME
        value: prometheus
      - name: helm_args
        value: -f values-ex.yaml 
  project: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

---
## <img src="img/argo-stacked-color.png" alt="argo-logo" width="70"/> Приложение с Vault
----

```
...
secret_id: <path:kv/path/secret#id>
...
```

---
## <img src="img/argo-stacked-color.png" alt="argo-logo" width="70"/> ArgoWorkflows
----

- Define workflows where each step in the workflow is a container.
- Model multi-step workflows as a sequence of tasks or capture the dependencies between tasks using a graph (DAG).
- Easily run compute intensive jobs for machine learning or data processing in a fraction of the time using Argo Workflows on Kubernetes.
- Run CI/CD pipelines natively on Kubernetes without configuring complex software development products.

---
## <img src="img/argo-stacked-color.png" alt="argo-logo" width="70"/> ArgoWorkflows
----

- Define workflows where each step in the workflow is a container.
- Model multi-step workflows as a sequence of tasks or capture the dependencies between tasks using a graph (DAG).
- Easily run compute intensive jobs for machine learning or data processing in a fraction of the time using Argo Workflows on Kubernetes.
- Ru


---
## <i class="fas fa-wrench fa-fw"></i> Нашли баг в Mikrotik, срочно закрой доступ к роутерам!
----
```
  - name: add allowed addresses to list
    routeros_command:
      commands: "/ip firewall address-list add list=allow address={{ item }}"
    with_items:
      - "{{ routeros_list_allow }}"

  - name: drop input except allow list
    routeros_command:
      commands: "{{ item }}"
    with_items:
      - /ip firewall filter add chain=input src-address-list=allow action=accept
      - /ip firewall filter add chain=input action=drop
```

--
## Массовые изменения


---
## <i class="fas fa-wrench fa-fw"></i> Помнишь, мы настраивали IPSec три месяца назад? Надо сделать так же новому клиенту
----

--
- ### А на чем мы делали? Гугл показывает libreswan, strongswan, openswan, xl2tpd

--
- ### А как мы там роуты то прописали?

--
- ### Что-то не заводится сразу, видимо что-то забыли


---
## <i class="fas fa-wrench fa-fw"></i> Помнишь, мы настраивали IPSec три месяца назад? Надо сделать так же новому клиенту
----
```
- name: Magic playbook
  hosts: сидящие_в_зале
  roles: 
    - тут_должны
    - быть_роли
    - но_я_думаю
    - вы_и_так
    - все_поняли
```
--
## Повторяемость


---
## <i class="fas fa-wrench fa-fw"></i> У нас новый сотрудник. Покажи ему как у нас все работает
----

--
- ### 10 клиентов

--
- ### Естественно, все они разные

--
- ### Документация. Какая документация?


---
## <i class="fas fa-wrench fa-fw"></i> У нас новый сотрудник. Покажи ему как у нас все работает
----
cat ansible/group_vars/prometheus.yml
```
prometheus_storage_retention: "500d"
prometheus_storage_retention_size: "40GB"
prometheus_web_listen_address: "localhost:9090"

prometheus_alertmanager_config:
  - scheme: http
    static_configs:
      - targets:
        - "127.0.0.1:9093"

prometheus_scrape_configs:
  - job_name: "mikrotik"
    scrape_timeout: "60s"
    scrape_interval: "120s"
    metrics_path: "/metrics"
    static_configs:
      - targets:
        - "127.0.0.1:9436"
```

---
## <i class="fas fa-wrench fa-fw"></i> У нас новый сотрудник. Покажи ему как у нас все работает
----
### Документирование

---
## <i class="fas fa-wrench fa-fw"></i> Перестал работать бэкап
----

--
- ### Я что-то нажал и главный роутер у клиента сломался

--
- ### С 15 июля нет бэкапов с микротиков

--
- ### СРОЧНО ВОССТАНОВИ!!!!111один

---
## <i class="fas fa-wrench fa-fw"></i> Перестал работать бэкап
----
<img src="img/git-diff.png" alt="git-diff"/>

---
## <i class="fas fa-wrench fa-fw"></i> Перестал работать бэкап
----
### <img src="img/Git-Icon-Black.png" alt="git-logo" width="50"/> Git 
### Версионирование. История изменений

---
## <i class="fas fa-spinner fa-fw"></i> Так какие задачи решает Ansible?
----

### 1. Автоматизация рутинных действий
### 2. Массовые изменения
### 3. Повторяемость конфигураций
### 4. Документирование
### 5. Версионирование. История изменений


---
class: title, middle, center

# Test Driven Development

---
##  <i class="fas fa-vial fa-fw"></i> TDD
----

- ### Test Driven Development
- ### Неплохо было бы проверять плэйбуки перед их выпуском на боевую инфраструктуру
- ### Для этого есть специальные инструменты

---
## <i class="fas fa-vial fa-fw"></i> Разработка через тесты
----

<table>
  <tr>
    <td>Testinfra</td>
    <td colspan="2" rowspan="3"><img src="img/testinfra.png" alt="testinfra-logo" width="500"/></td>
  </tr>
  <tr>
    <td>Пишем тесты, проверяющие систему</td>
  </tr>
  <tr>
    <td>Подгоняем плэйбуки под выполнение этих тестов</td>
  </tr>
</table>

---
## <i class="fas fa-hat-wizard fa-fw"></i> Molecule
----

<table>
  <tr>
    <td>Первоначальная инициализация роли</td>
    <td colspan="2" rowspan="4"><img src="img/molecule.png" alt="molecule-logo" width="500"/></td>
  </tr>
  <tr>
    <td>Тесты на идемпотентность</td>
  </tr>
  <tr>
    <td>Верификация</td>
  </tr>
  <tr>
    <td>Driver/Provider</td>
  </tr>
</table>


---
## <i class="fas fa-hat-wizard fa-fw"></i> Molecule. Первоначальная инициализация роли
----

### В случае указания новой роли создает типовую структуру для роли ansible

---
## <i class="fas fa-hat-wizard fa-fw"></i> Molecule. Тесты на идемпотентность
----

###  При повторном запуске роли не должно быть произведено каких-либо изменений

---
## <i class="fas fa-hat-wizard fa-fw"></i> Molecule. Верификация
----

- ### Автоматический прогон тестов

<img src="img/testinfra.png" alt="testinfra-logo" width="500"/>


---
## <i class="fas fa-hat-wizard fa-fw"></i> Molecule. Driver/Provider
----

- ### Необязательно гонять тесты на заранее созданных виртуалках
- ### Vagrant
  - ### Libvirt
  - ### Parallels
  - ### VirtualBox (default)
  - ### VMware Fusion
- ### Docker

---
class: title, middle, center

## Docker

---
## <i class="fab fa-docker fa-fw"></i> Docker
----

- ### Позволяет запускать приложения в изолированном окружении
- ### Прекрасный инструмент для "пощупать как оно работает"
- ### Поматросил и бросил - контейнер не стоит ничего
- ### Легко мигрировать на другую машину

--
- ### ... или в другую серверную

--
- ### ... или в облако AWS, GCP, Azure, ...

---
class: title, middle, center

# Continuous Integration and Continuous Deployment

---
## <i class="fab fa-gitlab fa-fw"></i> CI/CD
----

- ### А зачем мне вся эта куча технологий, если и так всё работает?
- ### Но ведь уходит много времени на: 
  - ### написать плэйбук
  - ### написать тесты
  - ### запустить тесты
  - ### перенести конфиг на боевую инфраструктуру
  - ### настроить всю эту чепуху


---
## <i class="fab fa-gitlab fa-fw"></i> CI/CD
----

  - ### pipeline
  - ### Все делается автоматически:
    - ### git commit запускает тесты
    - ### при успешном прохождении тестов код разливается на prod
    - ### ваше время тратится только на единоразовую настройку системы
    - ### ... ну ОК, ещё на написание тестов и плэйбуков

---
class: title, middle, center

## Demo

Возможно, я уже выложил плэйбуки для деплоя мониторинга сети на  [GitHub](https://github.com/devi1/ansible)

---
## <i class="fas fa-info fa-fw"></i> Информация

#### Технологии

-   [Ansible Galaxy](https://galaxy.ansible.com)
-   [Ansible Community](https://www.ansible.com/community)
-   [Awesome Ansible List](https://github.com/geerlingguy/awesome-ansible)
-   [Molecule](https://habr.com/ru/post/437216/)
-   [Docker](https://www.digitalocean.com/community/tutorials/the-docker-ecosystem-an-overview-of-containerization)


#### Презентация
https://devi1.github.io/Pres_Ansible_docker/

#### Инструменты, которыми сделана эта презентация

-  [markdown](https://daringfireball.net/projects/markdown/)
-  [remark](https://github.com/gnab/remark)
-  [font-awesome](https://fontawesome.com/)
-  [Visual Studio Code](https://code.visualstudio.com/)
-  [hands](http://www.bubnovd.net)
---
## <i class="fas fa-hands-helping fa-fw"></i> Контакты
----

### <i class="fas fa-link fa-fw"></i> https://mikrotik-ninja.ru
### <i class="fab fa-telegram-plane fa-fw"></i> @mikrotikninja
### <i class="fab fa-telegram-plane fa-fw"></i> @sysadminka
### <i class="fab fa-telegram-plane fa-fw"></i> @bubnov
----

### <i class="fab fa-facebook-f fa-fw"></i><i class="fab fa-vk fa-fw"></i><i class="fab fa-twitter fa-fw"></i><i class="fab fa-linkedin-in fa-fw"></i><i class="fab fa-instagram fa-fw"></i><i class="fab fa-github fa-fw"></i>: 
### \- mikrotikninja
### \- sysadminka
###  \- bubnov

