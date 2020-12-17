---
title: 'Tutorial: Implementar o WordPress no cluster AKS com o MySQL Flexible Server utilizando o Azure CLI'
description: Aprenda a construir e implementar rapidamente o WordPress em AKS com Azure Database para MySQL - Servidor Flexível.
ms.service: mysql
author: mksuni
ms.author: sumuth
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: mvc
ms.openlocfilehash: a02cb30b0f00f732fa0c4ac9319a652ef5cb6fc1
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657061"
---
# <a name="tutorial-deploy-wordpress-app-on-aks-with-azure-database-for-mysql---flexible-server"></a>Tutorial: Implementar a aplicação WordPress em AKS com Base de Dados Azure para MySQL - Servidor Flexível

Neste arranque rápido, implementa uma aplicação WordPress no cluster Azure Kubernetes Service (AKS) com Azure Database for MySQL - Flexible Server (Preview) utilizando o Azure CLI. 
**[AKS](../../aks/intro-kubernetes.md)** é um serviço gerido pela Kubernetes que permite implementar e gerir rapidamente clusters. **[A Azure Database for MySQL - Flexible Server (Preview)](overview.md)** é um serviço de base de dados totalmente gerido projetado para fornecer mais controlo granular e flexibilidade sobre funções de gestão de bases de dados e configurações de configuração. Atualmente o servidor Flexível está em Pré-Visualização.

> [!NOTE]
> - Azure Database for MySQL Flexible Server está atualmente em pré-visualização pública
> - Este quickstart pressupõe uma compreensão básica dos conceitos Kubernetes, WordPress e MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Este artigo requer a versão mais recente do Azure CLI. Se utilizar o Azure Cloud Shell, a versão mais recente já está instalada.

> [!NOTE]
> Se executar os comandos neste arranque rápido localmente (em vez de Azure Cloud Shell), certifique-se de que executa os comandos como administrador.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico, no qual os recursos do Azure são implementados e geridos. Vamos criar um grupo de recursos, *o wordpress-project* usando o comando [az-group-create] no local *leste.*

```azurecli-interactive
az group create --name wordpress-project --location eastus
```

> [!NOTE]
> A localização do grupo de recursos é onde os metadados do grupo de recursos são armazenados. É também onde os seus recursos funcionam em Azure se não especificar outra região durante a criação de recursos.

A saída de exemplo a seguir mostra o grupo de recursos criado com sucesso:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/wordpress-project",
  "location": "eastus",
  "managedBy": null,
  "name": "wordpress-project",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-aks-cluster"></a>Criar um cluster do AKS

Utilize o comando [az aks create](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-create) para criar um cluster AKS. O exemplo seguinte cria um cluster com o nome *myAKSCluster* com um nó. Isto levará vários minutos para ser concluído.

```azurecli-interactive
az aks create --resource-group wordpress-project --name wordpresscluster--node-count 1 --generate-ssh-keys
```

Após alguns minutos, o comando completa e devolve informações formatadas com JSON sobre o cluster.

> [!NOTE]
> Ao criar um cluster AKS, um segundo grupo de recursos é automaticamente criado para armazenar os recursos AKS. Veja [por que dois grupos de recursos são criados com AKS?](../../aks/faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Para gerir um cluster Kubernetes, você usa [kubectl,](https://kubernetes.io/docs/reference/kubectl/overview/)o cliente da linha de comando Kubernetes. Se utilizar a Azure Cloud Shell, `kubectl` já está instalada. Para instalar `kubectl` localmente, utilize o comando [az aks instalar-cli:](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-install-cli)

```azurecli-interactive
az aks install-cli
```

Para configurar `kubectl` para se ligar ao cluster do Kubernetes, utilize o comando [az aks get-credentials](/cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials). Este comando descarrega credenciais e configura o CLI de Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group wordpress-project --name wordpresscluster
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

## <a name="create-an-azure-database-for-mysql---flexible-server"></a>Criar uma base de dados Azure para MySQL - Servidor Flexível
Crie um servidor flexível com o [comando de criação de servidor flexível az mysql.](/cli/azure/mysql/flexible-server?view=azure-cli-latest&preserve-view=true) O seguinte comando cria um servidor utilizando predefinições e valores de serviço a partir do contexto local do seu Azure CLI:

```azurecli-interactive
az mysql flexible-server create --public-access <YOUR-IP-ADDRESS>
```

O servidor criado tem os atributos abaixo:
- Uma nova base de dados vazia ```flexibleserverdb``` é criada quando o servidor é provisido pela primeira vez. Neste arranque rápido vamos usar esta base de dados.
- Nome do servidor autogerado, nome de utilizador de administração, senha de administração, nome do grupo de recursos (se não especificado já no contexto local) e no mesmo local que o seu grupo de recursos
- Predefinições de serviço para as restantes configurações do servidor: nível de cálculo (Burstable), tamanho de computação/SKU (B1MS), período de retenção de backup (7 dias) e versão MySQL (5.7)
- A utilização de argumentos de acesso público permite-lhe criar um servidor com acesso público protegido pelas regras de firewall. Ao fornecer o seu endereço IP para adicionar a regra de firewall para permitir o acesso a partir da sua máquina cliente.
- Uma vez que o comando está a utilizar o contexto local, criará o servidor no grupo de recursos ```wordpress-project``` e na ```eastus``` região.


### <a name="build-your-wordpress-docker-image"></a>Construa a sua imagem de estivador WordPress

Descarregue a versão mais recente do [WordPress.](https://wordpress.org/download/) Crie um novo diretório ```my-wordpress-app``` para o seu projeto e use esta estrutura de pasta simples

```
└───my-wordpress-app
    └───public
        ├───wp-admin
        │   ├───css
        . . . . . . .
        ├───wp-content
        │   ├───plugins
        . . . . . . .
        └───wp-includes
        . . . . . . .
        ├───wp-config-sample.php
        ├───index.php
        . . . . . . .
    └─── Dockerfile

```


Mude o nome ```wp-config-sample.php``` ```wp-config.php``` e substitua as linhas 21 a 32 por este corte de código. O código abaixo está a ler o anfitrião da base de dados, o nome de utilizador e a palavra-passe do ficheiro manifesto kubernetes.

```php
//Using environment variables for DB connection information

// ** MySQL settings - You can get this info from your web host ** //
/** The name of the database for WordPress */

$connectstr_dbhost = getenv('DATABASE_HOST');
$connectstr_dbusername = getenv('DATABASE_USERNAME');
$connectstr_dbpassword = getenv('DATABASE_PASSWORD');

/** MySQL database name */
define('DB_NAME', 'flexibleserverdb');

/** MySQL database username */
define('DB_USER', $connectstr_dbusername);

/** MySQL database password */
define('DB_PASSWORD',$connectstr_dbpassword);

/** MySQL hostname */
define('DB_HOST', $connectstr_dbhost);

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/** SSL*/
define('MYSQL_CLIENT_FLAGS', MYSQLI_CLIENT_SSL);
```

### <a name="create-a-dockerfile"></a>Criar um Dockerfile
Crie um novo Dockerfile e copie este corte de código. Este Dockerfile na configuração do servidor web Apache com PHP e permitindo a extensão mysqli.

```docker
FROM php:7.2-apache
COPY public/ /var/www/html/
RUN docker-php-ext-install mysqli
RUN docker-php-ext-enable mysqli
```

### <a name="build-your-docker-image"></a>Construa a sua imagem de estivador
Certifique-se de que está ```my-wordpress-app``` no diretório de um terminal usando o ```cd``` comando. Executar o seguinte comando para construir a imagem:

``` bash

docker build --tag myblog:latest .

```

Coloque a sua imagem no [centro do Docker](https://docs.docker.com/get-started/part3/#create-a-docker-hub-repository-and-push-your-image) ou no registo do contentor [Azure](../../container-registry/container-registry-get-started-azure-cli.md).

> [!IMPORTANT]
>Se estiver a utilizar a recadistria do contentor Azure (ACR), então executar o ```az aks update``` comando para anexar a conta ACR com o cluster AKS.
>
>```azurecli-interactive
>az aks update -n myAKSCluster -g wordpress-project --attach-acr <your-acr-name>
> ```
>

## <a name="create-kubernetes-manifest-file"></a>Criar ficheiro manifesto Kubernetes

Um ficheiro manifesto kubernetes define um estado desejado para o cluster, como as imagens do recipiente a executar. Vamos criar um ficheiro manifesto nomeado `mywordpress.yaml` e copiar na seguinte definição YAML.

>[!IMPORTANT]
> - Substitua ```[DOCKER-HUB-USER/ACR ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]``` o nome e a etiqueta de imagem do wordPress do WordPress, por ```docker-hub-user/myblog:latest``` exemplo.
> - Atualize ```env``` a secção abaixo com ```SERVERNAME``` o seu servidor flexível ```YOUR-DATABASE-USERNAME``` ```YOUR-DATABASE-PASSWORD``` MySQL.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wordpress-blog
spec:
  replicas: 1
  selector:
    matchLabels:
      app: wordpress-blog
  template:
    metadata:
      labels:
        app: wordpress-blog
    spec:
      containers:
      - name: wordpress-blog
        image: [DOCKER-HUB-USER-OR-ACR-ACCOUNT]/[YOUR-IMAGE-NAME]:[TAG]
        ports:
        - containerPort: 80
        env:
        - name: DATABASE_HOST
          value: "SERVERNAME.mysql.database.azure.com"
        - name: DATABASE_USERNAME
          value: "YOUR-DATABASE-USERNAME"
        - name: DATABASE_PASSWORD
          value: "YOUR-DATABASE-PASSWORD"
        - name: DATABASE_NAME
          value: "flexibleserverdb"
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            - labelSelector:
                matchExpressions:
                  - key: "app"
                    operator: In
                    values:
                    - wordpress-blog
              topologyKey: "kubernetes.io/hostname"
---
apiVersion: v1
kind: Service
metadata:
  name: php-svc
spec:
  type: LoadBalancer
  ports:
    - port: 80
  selector:
    app: wordpress-blog
```

## <a name="deploy-wordpress-to-aks-cluster"></a>Implementar o WordPress para o cluster AKS
Implemente a aplicação utilizando o comando [de aplicação de kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f mywordpress.yaml
```

A saída de exemplo a seguir mostra as Implementações e Serviços criados com sucesso:

```output
deployment "wordpress-blog" created
service "php-svc" created
```

## <a name="test-the-application"></a>Testar a aplicação

Quando a aplicação é executado, um serviço Kubernetes expõe a linha frontal da aplicação para a internet. Este processo pode demorar alguns minutos a concluir.

Para monitorizar o progresso, utilize o comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) com o argumento `--watch`.

```azurecli-interactive
kubectl get service wordpress-blog --watch
```

Inicialmente, o *EXTERNAL-IP* para o serviço *wordpress-blog* é apresentado como *pendente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
wordpress-blog   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Quando o endereço *EXTERNAL-IP* mudar de *pendente* para um endereço IP público real, use `CTRL-C` para parar o processo do `kubectl` relógio. A saída de exemplo a seguir mostra um endereço IP público válido atribuído ao serviço:

```output
wordpress-blog  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

### <a name="browse-wordpress"></a>Navegue no WordPress

Abra um navegador web para o endereço IP externo do seu serviço para ver a sua página de instalação WordPress.

   :::image type="content" source="./media/tutorial-deploy-wordpress-on-aks/wordpress-aks-installed-success.png" alt-text="Sucesso da instalação wordpress no servidor flexível AKS e MySQL":::

>[!NOTE]
> - Atualmente o site WordPress não está a utilizar HTTPS. Recomenda-se [o ENABLE TLS com os seus próprios certificados.](../../aks/ingress-own-tls.md)
> - Pode [ativar o encaminhamento HTTP](../../aks/http-application-routing.md) para o seu cluster.

## <a name="clean-up-the-resources"></a>Limpar os recursos

Para evitar as acusações do Azure, devias limpar recursos não necessários.  Quando o cluster já não for necessário, utilize o comando [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete) para remover o grupo de recursos, o serviço de contentores e todos os recursos relacionados.

```azurecli-interactive
az group delete --name wordpress-project --yes --no-wait
```

> [!NOTE]
> Quando elimina o cluster, o principal de serviço do Azure Active Directory utilizado pelo cluster do AKS não é removido. Para obter passos sobre como remover o principal de serviço, consulte [Considerações sobre e eliminação do principal de serviço AKS](../../aks/kubernetes-service-principal.md#additional-considerations). Se usou uma identidade gerida, a identidade é gerida pela plataforma e não necessita de remoção.

## <a name="next-steps"></a>Passos seguintes

- Saiba como [aceder ao painel web kubernetes](../../aks/kubernetes-dashboard.md) para o seu cluster AKS
- Saiba como [escalar o seu cluster](../../aks/tutorial-kubernetes-scale.md)
- Saiba como gerir o seu [servidor flexível MySQL](./quickstart-create-server-cli.md)
- Saiba como [configurar os parâmetros do servidor](./how-to-configure-server-parameters-cli.md) para o servidor de base de dados.

