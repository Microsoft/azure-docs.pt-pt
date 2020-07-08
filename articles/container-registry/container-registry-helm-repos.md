---
title: Gráficos de leme da loja
description: Saiba como armazenar gráficos Helm para as suas aplicações Kubernetes usando repositórios no Registo de Contentores Azure
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 04ba3aaf312188ab77c04a97ab960cf9b9af078f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82857604"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Empurre e puxe as fichas de Helm para um registo de contentores Azure

Para gerir e implementar rapidamente aplicações para Kubernetes, pode utilizar o [gestor de pacotes Helm de código aberto.][helm] Com helm, os pacotes de aplicações são definidos como [gráficos,](https://helm.sh/docs/topics/charts/)que são recolhidos e armazenados num [repositório de gráficos Helm](https://helm.sh/docs/topics/chart_repository/).

Este artigo mostra-lhe como hospedar repositórios helm charts em um registo de contentores Azure, usando uma instalação Helm 3 ou Helm 2. Em muitos cenários, construiria e carregaria os seus próprios gráficos para as aplicações que desenvolve. Para obter mais informações sobre como construir os seus próprios gráficos helm, consulte o [Guia do Desenvolvedor de Modelos de Gráficos.][develop-helm-charts] Você também pode armazenar um gráfico helm existente a partir de outro repo Helm.

> [!IMPORTANT]
> O suporte para gráficos helm no Registo de Contentores Azure está atualmente em pré-visualização. As pré-visualizações são-lhe disponibilizadas na condição de concordar com os termos suplementares [de utilização.][terms-of-use] Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="helm-3-or-helm-2"></a>Leme 3 ou Helm 2?

Para armazenar, gerir e instalar gráficos Helm, utilize um cliente Helm e o Helm CLI. As principais versões do cliente Helm incluem Helm 3 e Helm 2. O Helm 3 suporta um novo formato de gráfico e já não instala o componente do lado do servidor Tiller. Para mais detalhes sobre as diferenças de versão, consulte a [versão FAQ](https://helm.sh/docs/faq/). Se já implementou previamente os gráficos Helm 2, consulte [o Leme Migratório v2 para v3](https://helm.sh/docs/topics/v2_v3_migration/).

Pode utilizar o Helm 3 ou o Helm 2 para hospedar gráficos Helm no Registo de Contentores Azure, com fluxos de trabalho específicos para cada versão:

* [Helm 3 client](#use-the-helm-3-client) - use `helm chart` comandos no Helm CLI para gerir gráficos no seu registo como [artefactos OCI](container-registry-image-formats.md#oci-artifacts)
* [Helm 2 client](#use-the-helm-2-client) - use comandos [de leme az acr][az-acr-helm] no CLI Azure para adicionar e gerir o seu registo de contentores como um repositório de gráfico helm

### <a name="additional-information"></a>Informações adicionais

* Para a maioria dos cenários, recomendamos a utilização do fluxo de trabalho Helm 3 com `helm chart` comandos nativos para gerir gráficos como artefactos OCI.
* A partir do Helm 3, os comandos [az acr helm][az-acr-helm] são suportados para compatibilidade com o cliente Helm 2 e formato gráfico. O desenvolvimento futuro destes comandos não está planeado. Consulte o roteiro do [produto.](https://github.com/Azure/acr/blob/master/docs/acr-roadmap.md#acr-helm-ga)
* Os gráficos de leme 2 não podem ser vistos ou geridos utilizando o portal Azure.

## <a name="use-the-helm-3-client"></a>Use o cliente Helm 3

### <a name="prerequisites"></a>Pré-requisitos

- **Um registo de contentores Azure** na sua assinatura Azure. Se necessário, crie um registo utilizando o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md).
- **Versão do cliente helm 3.1.0 ou mais tarde** - Corra `helm version` para encontrar a sua versão atual. Para obter mais informações sobre como instalar e atualizar o Helm, consulte [instalar o Leme.][helm-install]
- **Um cluster Kubernetes** onde vai instalar um gráfico Helm. Se necessário, crie um [cluster de serviço Azure Kubernetes][aks-quickstart]. 
- **Versão Azure CLI 2.0.71 ou mais tarde** - Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

### <a name="high-level-workflow"></a>Fluxo de trabalho de alto nível

Com **Helm 3** você:

* Pode criar um ou mais repositórios helm num registo de contentores Azure
* Armazenar gráficos Helm 3 num registo como [artefactos OCI](container-registry-image-formats.md#oci-artifacts). Atualmente, o apoio ao Helm 3 para o OCI é *experimental.*
* Autenticar com o seu registo utilizando o `helm registry login` comando.
* Use `helm chart` comandos no Helm CLI para empurrar, puxar e gerir gráficos de Helm em um registo
* Use `helm install` para instalar gráficos num cluster Kubernetes a partir de uma cache de repositório local.

Consulte as seguintes secções por exemplo.

### <a name="enable-oci-support"></a>Ativar o suporte do OCI

Desaprote a seguinte variável ambiental para permitir o suporte do OCI no cliente Helm 3. Atualmente, este apoio é experimental. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Criar um gráfico de amostras

Criar um gráfico de teste utilizando os seguintes comandos:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Como exemplo básico, altere o diretório para a `templates` pasta e elimine primeiro os conteúdos lá:

```console
cd hello-world/templates
rm -rf *
```

Na `templates` pasta, crie um ficheiro chamado `configmap.yaml` com os seguintes conteúdos:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Para mais informações sobre a criação e execução deste exemplo, consulte ["Começar](https://helm.sh/docs/chart_template_guide/getting_started/) no Helm Docs".

### <a name="save-chart-to-local-registry-cache"></a>Guardar gráfico para cache de registo local

Mude o diretório para a `hello-world` subdiretório. Em seguida, corra `helm chart save` para guardar uma cópia do gráfico localmente e também crie um pseudónimo com o nome totalmente qualificado do registo (todos minúsculos) e o repositório e etiqueta alvo. 

No exemplo seguinte, o nome do registo é *mycontainerregistry*, o repo alvo é *olá-mundo*, e a etiqueta de gráfico-alvo é *v1*, mas valores substitutos para o seu ambiente:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Corra `helm chart list` para confirmar que guardou as fichas na cache de registo local. A saída é semelhante a:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Autenticar com o registo

Executar o `helm registry login` comando no Helm 3 CLI para [autenticar com o registo](container-registry-authentication.md) usando credenciais apropriadas para o seu cenário.

Por exemplo, crie um diretor de serviço Azure Ative Directory [com permissões de puxar e empurrar](container-registry-auth-service-principal.md#create-a-service-principal) (papel AcrPush) para o registo. Em seguida, forneça as principais credenciais de serviço para `helm registry login` . O exemplo a seguir fornece a palavra-passe utilizando uma variável ambiental:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Gráfico de push para registo de contentores Azure

Executar o `helm chart push` comando no Helm 3 CLI para empurrar a tabela para o repositório de alvos totalmente qualificado:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Após um impulso bem sucedido, a saída é semelhante a:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Gráficos de listas no repositório

Tal como acontece com as imagens armazenadas num registo de contentores Azure, pode utilizar [comandos de repositório az acr][az-acr-repository] para mostrar os repositórios que hospedam os seus gráficos, e etiquetas de gráficos e manifestos. 

Por exemplo, executar [az acr repositório mostrar][az-acr-repository-show] para ver as propriedades do repo que criou no passo anterior:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

A saída é semelhante a:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Executar o comando [az acr repositório de manifestos][az-acr-repository-show-manifests] para ver detalhes do gráfico armazenado no repositório. Por exemplo:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

A produção, abreviada neste exemplo, mostra um `configMediaType` `application/vnd.cncf.helm.config.v1+json` de:

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

### <a name="pull-chart-to-local-cache"></a>Puxe o gráfico para cache local

Para instalar um gráfico Helm para Kubernetes, o gráfico deve estar na cache local. Neste exemplo, primeira corrida `helm chart remove` para remover o gráfico local existente chamado `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Corra `helm chart pull` para descarregar o gráfico do registo de contentores Azure para a sua cache local:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Gráfico de leme de exportação

Para trabalhar mais com o gráfico, exporte-o para um diretório local utilizando `helm chart export` . Por exemplo, exporte o gráfico que puxou para o `install` diretório:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Para ver informações sobre o gráfico exportado no repo, executar o `helm show chart` comando no diretório onde exportou o gráfico.

```console
cd install
helm show chart hello-world
```

Helm devolve informações detalhadas sobre a versão mais recente do seu gráfico, como mostrado na seguinte saída da amostra:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Instalar gráfico de leme

Corra `helm install` para instalar o gráfico Helm que puxou para a cache local e exportado. Especifique um nome de libertação como *myhelmtest*, ou passe o `--generate-name` parâmetro. Por exemplo:

```console
helm install myhelmtest ./hello-world
```

A saída após a instalação de gráficos bem sucedida é semelhante a:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Para verificar a instalação, verifique o `helm get manifest` comando. O comando devolve os dados YAML no seu `configmap.yaml` ficheiro de modelo.

Corra `helm uninstall` para desinstalar a versão do gráfico no seu cluster:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Eliminar um gráfico helm do repositório

Para eliminar um gráfico do repositório, utilize o comando [de eliminação do repositório az acr.][az-acr-repository-delete] Executar o seguinte comando e confirmar a operação quando solicitado:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>Use o cliente Helm 2

### <a name="prerequisites"></a>Pré-requisitos

- **Um registo de contentores Azure** na sua assinatura Azure. Se necessário, crie um registo utilizando o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md).
- **Versão do cliente helm 2.11.0 (não uma versão RC) ou posterior** - Corra `helm version` para encontrar a sua versão atual. Também precisa de um servidor Helm (Tiller) inicializado dentro de um cluster Kubernetes. Se necessário, crie um [cluster de serviço Azure Kubernetes][aks-quickstart]. Para obter mais informações sobre como instalar e atualizar o Helm, consulte [instalar o Leme.][helm-install-v2]
- **Versão Azure CLI 2.0.46 ou mais tarde** - Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

### <a name="high-level-workflow"></a>Fluxo de trabalho de alto nível

Com **Helm 2** você:

* Configure o seu registo de contentores Azure como um *único* repositório de gráficos Helm. O Registo de Contentores Azure gere a definição de índice à medida que adiciona e remove gráficos ao repositório.
* Autenticar com o seu registo de contentores Azure através do CLI Azure, que atualiza automaticamente o seu cliente Helm com o registo URI e credenciais. Não é necessário especificar manualmente esta informação do registo, para que as credenciais não estejam expostas no histórico de comandos.
* Utilize os comandos [de leme az acr][az-acr-helm] no CLI Azure para adicionar o seu registo de contentores Azure como um repositório de gráficos Helm, e para empurrar e gerir gráficos. Estes comandos Azure CLI envolvem comandos de clientes Helm 2.
* Adicione o repositório de gráficos no seu registo de contentores Azure ao índice de repo Helm local, suportando a pesquisa de gráficos.
* Use `helm install` para instalar gráficos num cluster Kubernetes a partir de uma cache de repositório local.

Consulte as seguintes secções por exemplo.

### <a name="add-repository-to-helm-client"></a>Adicione repositório ao cliente Helm

Adicione o seu repositório de cartão helm de registo de contentores Azure ao seu cliente Helm utilizando o comando [az acr helm repo add.][az-acr-helm-repo-add] Este comando obtém um sinal de autenticação para o seu registo de contentores Azure que é usado pelo cliente Helm. O token de autenticação é válido por 3 horas. Similar a `docker login` , você pode executar este comando em futuras sessões CLI para autenticar o seu cliente Helm com o seu repositório de gráfico de registo de contentores Azure:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Adicione um gráfico de amostra ao repositório

Primeiro, crie um diretório local em *~/acr-helm,* em seguida, descarregue o gráfico *estável/wordpress* existente:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Digite `ls` para listar o gráfico descarregado e note a versão Wordpress incluída no nome de ficheiro. O `helm fetch stable/wordpress` comando não especificou uma versão em particular, por isso a versão *mais recente* foi recolhida. Na saída de exemplo a seguir, o gráfico Wordpress é a versão *8.1.0*:

```output
wordpress-8.1.0.tgz
```

Empurre a tabela para o repositório de gráfico helm no registo do contentor Azure utilizando o comando [de impulso az acr][az-acr-helm-push] no CLI Azure. Especifique o nome da sua tabela Helm descarregada no passo anterior, tal como *wordpress-8.1.0.tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Após alguns momentos, o Azure CLI informa que a sua carta é guardada, como mostra a seguinte saída de exemplo:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Gráficos de listas no repositório

Para utilizar o gráfico carregado no passo anterior, o índice de repositório helm local deve ser atualizado. Pode reindexar os repositórios no cliente Helm ou utilizar o CLI Azure para atualizar o índice de repositório. Cada vez que adicionar um gráfico ao seu repositório, este passo deve ser concluído:

```azurecli
az acr helm repo add --name mycontainerregistry
```

Com um gráfico armazenado no seu repositório e o índice atualizado disponível localmente, pode utilizar os comandos regulares do cliente Helm para pesquisar ou instalar. Para ver todas as tabelas do seu repositório, use o `helm search` comando, fornecendo o seu próprio nome de Registo de Contentores Azure:

```console
helm search mycontainerregistry
```

O gráfico Wordpress empurrado no passo anterior é listado, como mostrado na saída do exemplo seguinte:

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

Também pode listar as tabelas com o Azure CLI, utilizando [a lista de lemes az acr][az-acr-helm-list]:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Mostrar informações para um gráfico helm

Para ver informações para um gráfico específico no repo, pode usar o `helm inspect` comando.

```console
helm inspect mycontainerregistry/wordpress
```

Quando não é fornecido nenhum número de versão, a versão *mais recente* é utilizada. Helm devolve informações detalhadas sobre o seu gráfico, como mostrado na seguinte saída de exemplo condensado:

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

Também pode mostrar a informação para um gráfico com o comando de show de leme Azure CLI [az acr.][az-acr-helm-show] Mais uma vez, a *versão mais recente* de um gráfico é devolvida por padrão. Pode anexar `--version` uma versão específica de um gráfico, como *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Instale um gráfico helm a partir do repositório

O gráfico Helm no seu repositório é instalado especificando o nome do repositório e o nome do gráfico. Utilize o cliente Helm para instalar o gráfico Wordpress:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Se empurrar para o repositório de cartão helm do registo de contentores Azure e posteriormente regressar numa nova sessão CLI, o seu cliente Helm local precisa de um token de autenticação atualizado. Para obter um novo token de autenticação, utilize o comando [de adicionar az acr helm repo.][az-acr-helm-repo-add]

Os seguintes passos são concluídos durante o processo de instalação:

- O cliente Helm procura o índice local de repositório.
- O gráfico correspondente é descarregado do repositório de registo de contentores Azure.
- O gráfico é implantado usando o Tiller no seu cluster Kubernetes.

À medida que a instalação prossegue, siga as instruções na saída do comando para ver os URLs e credenciais WorPress. Também pode executar o `kubectl get pods` comando para ver os recursos de Kubernetes implantados através da tabela Helm:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Eliminar um gráfico helm do repositório

Para eliminar um gráfico do repositório, utilize o comando [de eliminação do leme az acr.][az-acr-helm-delete] Especificar o nome do gráfico, como *wordpress,* e a versão a eliminar, tal como *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Se desejar eliminar todas as versões do gráfico nomeado, deixe de fora o `--version` parâmetro.

O gráfico continua a ser devolvido quando `helm search` corres. Mais uma vez, o cliente Helm não atualiza automaticamente a lista de gráficos disponíveis num repositório. Para atualizar o índice de repo do cliente Helm, use o comando [de adicionar az acr helm repo][az-acr-helm-repo-add] novamente:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Próximos passos

* Para obter mais informações sobre como criar e implementar gráficos helm, consulte [gráficos de Helm em Desenvolvimento][develop-helm-charts].
* Saiba mais sobre a instalação de aplicações com o Helm in [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* Os gráficos de leme podem ser usados como parte do processo de construção do contentor. Para obter mais informações, consulte [utilizar as tarefas de registo do contentor Azure][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
