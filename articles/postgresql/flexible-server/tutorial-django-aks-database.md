---
title: 'Tutorial: Implementar Django no cluster AKS com o PostgreSQL Flexible Server utilizando o Azure CLI'
description: Aprenda a construir e implementar rapidamente o Django na AKS com a Azure Database para PostgreSQL - Servidor Flexível.
ms.service: postgresql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 12/10/2020
ms.custom: mvc
ms.openlocfilehash: 6e8effee91eed73193319238c2ad2f6eaf6d0473
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102211282"
---
# <a name="tutorial-deploy-django-app-on-aks-with-azure-database-for-postgresql---flexible-server"></a>Tutorial: Implementar aplicativo Django em AKS com Base de Dados Azure para PostgreSQL - Servidor Flexível

Neste quickstart, implementa uma aplicação Django no cluster Azure Kubernetes Service (AKS) com Azure Database for PostgreSQL - Flexible Server (Preview) utilizando o Azure CLI.

**[AKS](../../aks/intro-kubernetes.md)** é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. **[A Azure Database for PostgreSQL - Flexible Server (Preview)](overview.md)** é um serviço de base de dados totalmente gerido projetado para fornecer mais controlo granular e flexibilidade sobre funções de gestão de bases de dados e configurações de configuração.

> [!NOTE]
> - Azure Database for PostgreSQL Flexible Server está atualmente em pré-visualização pública
> - Este quickstart assume uma compreensão básica dos conceitos kubernetes, Django e PostgreSQL.

## <a name="pre-requisites"></a>Pré-requisitos
[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

- Utilize o [Azure Cloud Shell](../../cloud-shell/quickstart.md) com o ambiente do Bash.

   [![Lançamento incorporado](https://shell.azure.com/images/launchcloudshell.png "Iniciar o Azure Cloud Shell")](https://shell.azure.com)  
- Se preferir, [instale](/cli/azure/install-azure-cli) o Azure CLI para executar comandos de referência CLI.
  - Se estiver a utilizar uma instalação local, inicie sessão com o CLI do Azure ao utilizar o comando [az login](/cli/azure/reference-index#az-login).  Para concluir o processo de autenticação, siga os passos apresentados no seu terminal.  Veja [Iniciar sessão com o CLI do Azure](/cli/azure/authenticate-azure-cli) para obter opções de início de sessão adicionais.
  - Quando lhe for pedido, instale as extensões do CLI do Azure durante a primeira utilização.  Para obter mais informações sobre as extensões, veja [Utilizar extensões com o CLI do Azure](/cli/azure/azure-cli-extensions-overview).
  - Execute o comando [az version](/cli/azure/reference-index?#az_version) para localizar a versão e as bibliotecas dependentes instaladas. Para atualizar para a versão mais recente, execute o comando [az upgrade](/cli/azure/reference-index?#az_upgrade). Este artigo requer a versão mais recente do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

> [!NOTE]
> Se executar os comandos neste arranque rápido localmente (em vez de Azure Cloud Shell), certifique-se de que executa os comandos como administrador.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Vamos criar um grupo de recursos, *o django-project* usando o comando [az-group-create] no local *leste.*

```azurecli-interactive
az group create --name django-project --location eastus
```

> [!NOTE]
> A localização do grupo de recursos é onde os metadados do grupo de recursos são armazenados. É também onde os seus recursos funcionam em Azure se não especificar outra região durante a criação de recursos.

A saída de exemplo a seguir mostra o grupo de recursos criado com sucesso:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/django-project",
  "location": "eastus",
  "managedBy": null,
  
  "name": "django-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Criar um cluster do AKS

Utilize o comando [az aks create](/cli/azure/aks#az-aks-create) para criar um cluster AKS. O exemplo seguinte cria um cluster com o nome *myAKSCluster* com um nó. Isto levará vários minutos para ser concluído.

```azurecli-interactive
az aks create --resource-group django-project --name djangoappcluster --node-count 1 --generate-ssh-keys
```

Após alguns minutos, o comando completa e devolve informações formatadas com JSON sobre o cluster.

> [!NOTE]
> Ao criar um cluster AKS, um segundo grupo de recursos é automaticamente criado para armazenar os recursos AKS. Veja [por que dois grupos de recursos são criados com AKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, você usa [kubectl,](https://kubernetes.io/docs/reference/kubectl/overview/)o cliente da linha de comando Kubernetes. Se utilizar a Azure Cloud Shell, `kubectl` já está instalada. Para instalar `kubectl` localmente, utilize o comando [az aks instalar-cli:](/cli/azure/aks#az-aks-install-cli)

```azurecli-interactive
az aks install-cli
```

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials](/cli/azure/aks#az-aks-get-credentials). Este comando descarrega credenciais e configura o CLI de Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group django-project --name djangoappcluster
```

> [!NOTE]
> O comando acima utiliza a localização padrão para o [ficheiro de configuração Kubernetes,](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/)que é `~/.kube/config` . Pode especificar uma localização diferente para o seu ficheiro de configuração Kubernetes utilizando *o ficheiro --file*.

Para verificar a ligação ao cluster, utilize o comando [kubectl get]( https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) para devolver uma lista de nós do cluster.

```azurecli-interactive
kubectl get nodes
```

A saída de exemplo seguinte mostra o nó único criado nos passos anteriores. Certifique-se de que o estado do nó está *pronto:*

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="create-an-azure-database-for-postgresql---flexible-server"></a>Criar uma base de dados Azure para PostgreSQL - Servidor Flexível
Crie um servidor flexível com o [comando de criação de servidor flexível az postgreSQL.](/cli/azure/postgres/flexible-server#az_postgres_flexible_server_create) O seguinte comando cria um servidor utilizando predefinições e valores de serviço a partir do contexto local do seu Azure CLI:

```azurecli-interactive
az postgres flexible-server create --public-access <YOUR-IP-ADDRESS>
```

O servidor criado tem os atributos abaixo:
- Uma nova base de dados vazia ```postgres``` é criada quando o servidor é provisido pela primeira vez. Neste arranque rápido vamos usar esta base de dados.
- Nome do servidor autogerado, nome de utilizador de administração, senha de administração, nome do grupo de recursos (se não especificado já no contexto local) e no mesmo local que o seu grupo de recursos
- Predefinições de serviço para as restantes configurações do servidor: nível de cálculo (Final geral), tamanho do cálculo/SKU (Standard_D2s_v3 que utiliza 2vCores), período de retenção de backup (7 dias) e versão PostgreSQL (12)
- A utilização de argumentos de acesso público permite-lhe criar um servidor com acesso público protegido pelas regras de firewall. Ao fornecer o seu endereço IP para adicionar a regra de firewall para permitir o acesso a partir da sua máquina cliente.
- Uma vez que o comando está a utilizar o contexto local, criará o servidor no grupo de recursos ```django-project``` e na ```eastus``` região.


## <a name="build-your-django-docker-image"></a>Construa a sua imagem de estivador Django

Crie uma nova [aplicação Django](https://docs.djangoproject.com/en/3.1/intro/) ou use o seu projeto Django existente. Certifique-se de que o seu código está nesta estrutura de pasta.

```
└───my-djangoapp
    └───views.py
    └───models.py
    └───forms.py
    ├───templates
        . . . . . . .
    ├───static
        . . . . . . .
└───my-django-project
    └───settings.py
    └───urls.py
    └───wsgi.py
        . . . . . . .
    └─── Dockerfile
    └─── requirements.txt
    └─── manage.py
    
```
Atualização ```ALLOWED_HOSTS``` para garantir que a ```settings.py``` aplicação Django utiliza o IP externo que é atribuído à app Kubernetes.

```python
ALLOWED_HOSTS = ['*']
```

Atualizar ```DATABASES={ }``` a secção no ```settings.py```  ficheiro. O código abaixo está a ler o anfitrião da base de dados, o nome de utilizador e a palavra-passe do ficheiro manifesto kubernetes.

```python
DATABASES={
   'default':{
      'ENGINE':'django.db.backends.postgresql_psycopg2',
      'NAME':os.getenv('DATABASE_NAME'),
      'USER':os.getenv('DATABASE_USER'),
      'PASSWORD':os.getenv('DATABASE_PASSWORD'),
      'HOST':os.getenv('DATABASE_HOST'),
      'PORT':'5432',
      'OPTIONS': {'sslmode': 'require'}
   }
}
```

### <a name="generate-a-requirementstxt-file"></a>Gerar um ficheiro requirements.txt
Crie um ```requirements.txt``` ficheiro para listar as dependências da Aplicação Django. Aqui está um ```requirements.txt``` ficheiro de exemplo. Pode utilizar [``` pip freeze > requirements.txt```](https://pip.pypa.io/en/stable/reference/pip_freeze/) para gerar um ficheiro requirements.txt para a sua aplicação existente.

``` text
Django==2.2.17
postgres==3.0.0
psycopg2-binary==2.8.6
psycopg2-pool==1.1
pytz==2020.4
```

### <a name="create-a-dockerfile"></a>Criar um Dockerfile
Crie um novo ficheiro nomeado ```Dockerfile``` e copie o corte de código abaixo. Este Dockerfile na criação do Python 3.8 e na instalação de todos os requisitos listados no ficheiro requirements.txt.

```docker
# Use the official Python image from the Docker Hub
FROM python:3.8.2

# Make a new directory to put our code in.
RUN mkdir /code

# Change the working directory.
WORKDIR /code

# Copy to code folder
COPY . /code/

# Install the requirements.
RUN pip install -r requirements.txt

# Run the application:
CMD python manage.py runserver 0.0.0.0:8000
```

### <a name="build-your-image"></a>Construa a sua imagem
Certifique-se de que está ```my-django-app``` no diretório de um terminal usando o ```cd``` comando. Executar o seguinte comando para construir a sua imagem do quadro de avisos:

``` bash

docker build --tag myblog:latest .

```

Coloque a sua imagem no [centro do Docker](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) ou no registo do contentor [Azure](../../container-registry/container-registry-get-started-azure-cli.md).

> [!IMPORTANT]
>Se estiver a utilizar a recadistria do contentor Azure (ACR), então executar o ```az aks update``` comando para anexar a conta ACR com o cluster AKS.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g django-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Criar ficheiro manifesto Kubernetes

Um ficheiro manifesto kubernetes define um estado desejado para o cluster, como as imagens do recipiente a executar. Vamos criar um ficheiro manifesto nomeado ```djangoapp.yaml``` e copiar na seguinte definição YAML.

>[!IMPORTANT]
> - ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]```Substitua-o pelo nome e etiqueta de imagem do Django, por ```docker-hub-user/myblog:latest``` exemplo.
> - Atualize ```env``` a secção abaixo com o ```SERVERNAME``` seu servidor flexível , do seu servidor flexível ```YOUR-DATABASE-USERNAME``` ```YOUR-DATABASE-PASSWORD``` postgres.


```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: django-app
spec:
  replicas: 1
  selector:
    matchLabels:
      app: django-app
  template:
    metadata:
      labels:
        app: django-app
    spec:
      containers:
      - name: django-app
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.postgres.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "postgres"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - django-app
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: python-svc
spec:
  type: LoadBalancer
  ports:
    - port: 8000
  selector:
    app: django-app
```

## <a name="deploy-django-to-aks-cluster"></a>Implementar Django para o cluster AKS
Implemente a aplicação utilizando o comando [de aplicação de kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f djangoapp.yaml
```

A saída de exemplo a seguir mostra as Implementações e Serviços criados com sucesso:

```output
deployment "django-app" created
service "python-svc" created
```

Uma implementação ```django-app``` permite-lhe descrever detalhes sobre a sua implementação, como quais as imagens a utilizar para a aplicação, o número de cápsulas e configuração do pod. É criado um serviço ```python-svc``` para expor a aplicação através de um IP externo.

## <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe a linha frontal da aplicação para a internet. Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, utilize o comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) com o argumento `--watch`.

```azurecli-interactive
kubectl get service django-app --watch
```

Inicialmente, o *EXTERNAL-IP* para o serviço *django-app* é apresentado como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
django-app   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *EXTERNAL-IP* mudar de *pendente* para um endereço IP público real, use `CTRL-C` para parar o processo do `kubectl` relógio. A saída de exemplo a seguir mostra um endereço IP público válido atribuído ao serviço:

```output
django-app  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Agora abra um navegador web para o endereço IP externo do seu serviço ver a aplicação Django.  

>[!NOTE]
> - Atualmente o site do Django não está a usar HTTPS. Recomenda-se [o ENABLE TLS com os seus próprios certificados.](../../aks/ingress-own-tls.md)
> - Pode [ativar o encaminhamento HTTP](../../aks/http-application-routing.md) para o seu cluster. Quando o encaminhamento de http está ativado, configura um controlador Ingress no seu cluster AKS. À medida que as aplicações são implementadas, a solução também cria nomes DNS acessíveis ao público para os pontos finais de aplicação.

## <a name="run-database-migrations"></a>Executar migrações de base de dados

Para qualquer aplicação django, você precisaria executar migração de base de dados ou recolher ficheiros estáticos. Você pode executar estes comandos de concha django usando ```$ kubectl exec <pod-name> -- [COMMAND]``` .  Antes de executar o comando, tem de encontrar o nome da cápsula utilizando ```kubectl get pods``` . 

```bash
$ kubectl get pods
```

Você verá uma saída como esta
```output
NAME                             READY   STATUS          RESTARTS   AGE
django-app-5d9cd6cd8-l6x4b     1/1     Running              0       2m
```

Uma vez encontrado o nome do pod, pode executar migrações na base de dados de Django com o comando ```$ kubectl exec <pod-name> -- [COMMAND]``` . Nota ```/code/``` é o diretório de trabalho para o projeto definido ```Dockerfile``` acima.

```bash
$ kubectl exec django-app-5d9cd6cd8-l6x4b -- python /code/manage.py migrate
```

A saída seria como 
```output 
Operations to perform:
  Apply all migrations: admin, auth, contenttypes, sessions
Running migrations:
  Applying contenttypes.0001_initial... OK
  Applying auth.0001_initial... OK
  Applying admin.0001_initial... OK
  Applying admin.0002_logentry_remove_auto_add... OK
  Applying admin.0003_logentry_add_action_flag_choices... OK
  . . . . . . 
```

Se tiver problemas, por favor corra ```kubectl logs <pod-name>```  para ver que exceção é lançada pela sua aplicação. Se a aplicação estiver a funcionar com sucesso, veria uma saída como esta durante a execução ```kubectl logs``` .

```output
Watching for file changes with StatReloader
Performing system checks...

System check identified no issues (0 silenced).

You have 17 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.
December 08, 2020 - 23:24:14
Django version 2.2.17, using settings 'django_postgres_app.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.
```

## <a name="clean-up-the-resources"></a>Limpar os recursos

Para evitar as acusações do Azure, devias limpar recursos não necessários.  Quando o cluster já não for necessário, utilize o comando [az group delete](/cli/azure/group&preserve-view=true#az_group_delete) para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name django-project --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS](../../aks/kubernetes-service-principal.md#additional-considerations). Se usou uma identidade gerida, a identidade é gerida pela plataforma e não necessita de remoção.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [aceder ao painel web kubernetes](../../aks/kubernetes-dashboard.md) para o seu cluster AKS
- Saiba como ativar a [implementação contínua](../../aks/deployment-center-launcher.md)
- Saiba como [escalar o seu cluster](../../aks/tutorial-kubernetes-scale.md)
- Saiba como gerir o seu [servidor flexível pós-pós-gres](./quickstart-create-server-cli.md)
- Saiba como [configurar os parâmetros do servidor](./howto-configure-server-parameters-using-cli.md) para o servidor de base de dados.