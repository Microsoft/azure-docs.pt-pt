---
title: Gráficos de leme da loja
description: Saiba como armazenar gráficos helm para as suas aplicações Kubernetes usando repositórios no Registo de Contentores Azure
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 26588bb4dc3cf50656103b50d5d0559908a1ccb7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77524636"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Empurre e puxe gráficos helm para um registo de contentores Azure

Para gerir e implementar rapidamente aplicações para kubernetes, pode utilizar o [gestor de pacotes Helm de código aberto.][helm] Com helm, os pacotes de aplicação são definidos como [gráficos](https://helm.sh/docs/topics/charts/), que são recolhidos e armazenados num [repositório](https://helm.sh/docs/topics/chart_repository/)de gráficos Helm .

Este artigo mostra-lhe como hospedar gráficos Helm em repositórios num registo de contentores Azure, utilizando uma instalação Helm 3 ou Helm 2. Para este exemplo, armazena um gráfico helm existente do repo *estável* helm público. Em muitos cenários, você construiria e carregaria os seus próprios gráficos para as aplicações que desenvolve. Para mais informações sobre como construir os seus próprios gráficos Helm, consulte o [Guia do Desenvolvedor do Modelo de Gráfico][develop-helm-charts].

> [!IMPORTANT]
> O suporte para gráficos Helm no Registo de Contentores Azure está atualmente em pré-visualização. As pré-visualizações são disponibilizadas a si com a condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="helm-3-or-helm-2"></a>Leme 3 ou Leme 2?

Para armazenar, gerir e instalar gráficos Helm, utiliza-se um cliente Helm e o Helm CLI. As principais libertações do cliente Helm incluem Helm 3 e Helm 2. O Helm 3 suporta um novo formato de gráfico e já não instala o componente do lado do servidor Tiller. Para mais detalhes sobre as diferenças da versão, consulte a [versão FAQ](https://helm.sh/docs/faq/). Se já implementou as tabelas Helm 2, consulte o [Helm v2 para v3](https://helm.sh/docs/topics/v2_v3_migration/).

Pode utilizar o Helm 3 ou o Helm 2 para acolher gráficos Helm no Registo de Contentores Azure, com fluxos de trabalho específicos de cada versão:

* [Cliente Helm 3](#use-the-helm-3-client) - use comandos `helm chart` para gerir gráficos no seu registo como [artefactos OCI](container-registry-image-formats.md#oci-artifacts)
* [Cliente Helm 2](#use-the-helm-2-client) - use comandos [de leme az acr][az-acr-helm] no Azure CLI para adicionar e gerir o seu registo de contentores como um repositório de gráficohelm

### <a name="additional-information"></a>Informações adicionais

* Recomendamos a utilização do fluxo de trabalho Helm 3 com comandos nativos `helm chart` para gerir gráficos como artefactos OCI.
* Você pode usar comandos e gráficos do legacy [acr helm][az-acr-helm] Azure CLI com o cliente Helm 3. No entanto, certos comandos como `az acr helm list` não são compatíveis com gráficos helm 3.
* A partir do Helm 3, os comandos [de leme az acr][az-acr-helm] são suportados principalmente para compatibilidade com o cliente Helm 2 e formato gráfico. O desenvolvimento futuro destes comandos não está planeado.

## <a name="use-the-helm-3-client"></a>Use o cliente Helm 3

### <a name="prerequisites"></a>Pré-requisitos

- Um registo de **contentores Azure** na sua assinatura Azure. Se necessário, crie um registo utilizando o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm client version 3.0.0 ou mais tarde** - Run `helm version` para encontrar a sua versão atual. Para obter mais informações sobre como instalar e atualizar o Leme, consulte [a instalação do Leme][helm-install].
- **Um aglomerado kubernetes** onde vai instalar um gráfico Helm. Se necessário, crie um cluster de [serviço Azure Kubernetes][aks-quickstart]. 
- **Versão Azure CLI 2.0.71 ou posterior** - Run `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

### <a name="high-level-workflow"></a>Fluxo de trabalho de alto nível

Com **o Leme 3** tu:

* Pode criar um ou mais repositórios Helm num registo de contentores Azure
* Guarde os gráficos Helm 3 num registo como [artefactos OCI](container-registry-image-formats.md#oci-artifacts). Atualmente, o suporte helm 3 para OCI é considerado *experimental.*
* Use `helm chart` comandos diretamente do Helm CLI para empurrar, puxar e gerir gráficos helm em um registo
* Autenticar com o seu registo através do Azure CLI, que depois atualiza automaticamente o seu cliente Helm com o registo URI e credenciais. Não precisa especificar manualmente esta informação do registo, para que as credenciais não estejam expostas no histórico do comando.
* Use `helm install` para instalar gráficos num cluster Kubernetes a partir de uma cache de repositório local.

Consulte as seguintes secções, por exemplo.

### <a name="enable-oci-support"></a>Ativar o suporte do OCI

Detete a seguinte variável ambiental para permitir o suporte de OCI no cliente Helm 3. Atualmente, este apoio é experimental. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="pull-an-existing-helm-package"></a>Puxe um pacote helm existente

Se ainda não adicionou o `stable` helm chart repo, execute o comando `helm repo add`:

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com
```

Puxe um pacote de gráficos do `stable` repo localmente. Por exemplo, crie um diretório local como *~/acr-helm,* em seguida, baixe o pacote *de gráficos de estável/wordpress* existente. (Este exemplo e outros comandos neste artigo são formatados para a concha bash.)

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm pull stable/wordpress --untar
```

O comando `helm pull stable/wordpress` não especificou uma versão em particular, por isso a *versão mais recente* foi puxada e descomprimida no subdiretório `wordpress`.

### <a name="save-chart-to-local-registry-cache"></a>Guardar gráfico para cache de registo local

Mude o diretório para o subdiretório `wordpress`, que contém os ficheiros da tabela Helm. Em seguida, executar `helm chart save` para guardar uma cópia da tabela localmente e também criar um pseudónimo com o nome totalmente qualificado do registo e o repositório e etiqueta alvo. 

No exemplo seguinte, o nome do registo é *mycontainerregistry*, o repo-alvo é *wordpress*, e a etiqueta de gráfico-alvo é *mais recente*, mas valores de substituição para o seu ambiente:

```console
cd wordpress
helm chart save . wordpress:latest
helm chart save . mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Faça `helm chart list` para confirmar que guardou as fichas na cache do registo local. A saída é semelhante a:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
wordpress:latest                                         wordpress       8.1.0   5899db0 29.1 KiB        1 day 
mycontainerregistry.azurecr.io/helm/wordpress:latest     wordpress       8.1.0   5899db0 29.1 KiB        1 day 
```

### <a name="push-chart-to-azure-container-registry"></a>Gráfico de impulso para o registo de contentores de Azure

Execute o comando `helm chart push` no Helm 3 CLI para empurrar a tabela Helm para um repositório no registo de contentores Azure. Se não existir, o repositório é criado.

Utilize primeiro o comando Azure CLI [az acr login][az-acr-login] para autenticar o seu registo:

```azurecli
az acr login --name mycontainerregistry
```

Empurre o gráfico para o repositório-alvo totalmente qualificado:

```console
helm chart push mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Após um impulso bem sucedido, a saída é semelhante a:

```console
The push refers to repository [mycontainerregistry.azurecr.io/helm/wordpress]
ref:     mycontainerregistry.azurecr.io/helm/wordpress:latest
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    29.1 KiB
name:    wordpress
version: 8.1.0
```

### <a name="list-charts-in-the-repository"></a>Listas no repositório

Tal como acontece com as imagens armazenadas num registo de contentores Azure, pode utilizar comandos [de repositório az acr][az-acr-repository] para mostrar os repositórios que acolhem as suas tabelas, e etiquetas de gráficos e manifestos. 

Por exemplo, executar [az acr repositório show][az-acr-repository-show] para ver as propriedades do repo que criou no passo anterior:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/wordpress
```

A saída é semelhante a:

```console
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-01-29T16:54:30.1514833Z",
  "imageName": "helm/wordpress",
  "lastUpdateTime": "2020-01-29T16:54:30.4992247Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Executar o comando de manifestos de [show-manifestos az acr acr][az-acr-repository-show-manifests] para ver detalhes da carta armazenada no repositório. Por exemplo:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/wordpress --detail
```

A saída, abreviada neste exemplo, mostra um `configMediaType` de `application/vnd.cncf.helm.config.v1+json`:

```console
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-01-29T16:54:30.2382436Z",
    "digest": "sha256:xxxxxxxx51bc0807bfa97cb647e493ac381b96c1f18749b7388c24bbxxxxxxxxx",
    "imageSize": 29995,
    "lastUpdateTime": "2020-01-29T16:54:30.3492436Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "latest"
    ]
  }
]
```

### <a name="pull-chart-to-local-cache"></a>Puxar gráfico para a cache local

Para instalar um gráfico Helm para Kubernetes, o gráfico deve estar na cache local. Neste exemplo, a primeira execução `helm chart remove` para remover o gráfico local existente chamado `mycontainerregistry.azurecr.io/helm/wordpress:latest`:

```console
helm chart remove mycontainerregistry.azurecr.io/helm/wordpress:latest
```

Faça `helm chart pull` para baixar a tabela do registo de contentores Azure para a sua cache local:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/wordpress:latest
```

### <a name="export-helm-chart"></a>Gráfico de leme de exportação

Para trabalhar mais com a tabela, exporte-o para um diretório local utilizando `helm chart export`. Por exemplo, exporte o gráfico que puxou para o diretório `install`:

```console
helm chart export mycontainerregistry.azurecr.io/helm/wordpress:latest --destination ./install
```

Para visualizar informações para o gráfico exportado no repo, faça o comando `helm inspect chart` no diretório onde exportou o gráfico.

```console
cd install
helm inspect chart wordpress
```

Quando não é fornecido nenhum número de versão, a *versão mais recente* é utilizada. O Helm devolve informações detalhadas sobre o seu gráfico, como mostra a seguinte saída condensada:

```
apiVersion: v1
appVersion: 5.3.2
dependencies:
- condition: mariadb.enabled
  name: mariadb
  repository: https://kubernetes-charts.storage.googleapis.com/
  tags:
  - wordpress-database
  version: 7.x.x
description: Web publishing platform for building blogs and websites.
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
```

### <a name="install-helm-chart"></a>Instalar gráfico helm

Faça `helm install` para instalar o gráfico helm que puxou para a cache local e exportou. Especifique um nome de lançamento ou passe o parâmetro `--generate-name`. Por exemplo:

```console
helm install wordpress --generate-name
```

À medida que a instalação prossegue, siga as instruções na saída de comando para ver os URLs e credenciais WorPress. Você também pode executar o comando `kubectl get pods` para ver os recursos Kubernetes implantados através da tabela Helm:

```console
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Eliminar um gráfico helm do repositório

Para eliminar um gráfico do repositório, utilize o comando de exclusão do [repositório az acr.][az-acr-repository-delete] Executar o seguinte comando e confirmar a operação quando solicitado:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/wordpress:latest
```

## <a name="use-the-helm-2-client"></a>Use o cliente Helm 2

### <a name="prerequisites"></a>Pré-requisitos

- Um registo de **contentores Azure** na sua assinatura Azure. Se necessário, crie um registo utilizando o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm client version 2.11.0 (não uma versão RC) ou mais tarde** - Executar `helm version` para encontrar a sua versão atual. Também precisa de um servidor Helm (Tiller) inicializado dentro de um cluster Kubernetes. Se necessário, crie um cluster de [serviço Azure Kubernetes][aks-quickstart]. Para obter mais informações sobre como instalar e atualizar o Leme, consulte [a instalação do Leme][helm-install-v2].
- **Versão Azure CLI 2.0.46 ou posterior** - Executar `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

### <a name="high-level-workflow"></a>Fluxo de trabalho de alto nível

Com **o Leme 2** tu:

* Configure o registo de contentores Azure como um *único* repositório de gráficos Helm. O Registo de Contentores Azure gere a definição de índice à medida que adiciona e remove gráficos para o repositório.
* Utilize os comandos de [leme az acr][az-acr-helm] no Azure CLI para adicionar o seu registo de contentores Azure como um repositório de gráficos Helm, e para empurrar e gerir gráficos. Estes comandos Azure CLI embrulham comandos de cliente Helm 2.
* Adicione o repositório de gráficos no seu registo de contentores Azure ao seu índice local de repo Helm, apoiando a pesquisa de gráficos
* Autenticar com o seu registo de contentores Azure através do Azure CLI, que depois atualiza automaticamente o seu cliente Helm com o registo URI e credenciais. Não precisa especificar manualmente esta informação do registo, para que as credenciais não estejam expostas no histórico do comando.
* Use `helm install` para instalar gráficos num cluster Kubernetes a partir de uma cache de repositório local.

Consulte as seguintes secções, por exemplo.

### <a name="add-repository-to-helm-client"></a>Adicione repositório ao cliente Helm

Adicione o seu repositório de gráficos de helm do registo de contentores Azure ao seu cliente Helm utilizando o comando de apoio ao [helm repo az acr.][az-acr-helm-repo-add] Este comando recebe um símbolo de autenticação para o seu registo de contentores Azure que é usado pelo cliente Helm. O símbolo de autenticação é válido por 3 horas. Semelhante a `docker login`, pode executar este comando em futuras sessões do CLI para autenticar o seu cliente Helm com o seu repositório de gráficos de helm do Registo de Contentores Azure:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-chart-to-the-repository"></a>Adicione um gráfico ao repositório

Primeiro, crie um diretório local em *~/acr-helm,* em seguida, baixe o *gráfico estável/wordpress* existente:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Digite `ls` para listar o gráfico descarregado e note a versão Wordpress incluída no nome de ficheiro. O comando `helm fetch stable/wordpress` não especificou uma versão em particular, por isso a *versão mais recente* foi recolhida. Na saída de exemplo seguinte, o gráfico Wordpress é a versão *8.1.0:*

```
wordpress-8.1.0.tgz
```

Empurre a tabela para o seu repositório de gráficos Helm no Registo de Contentores Azure utilizando o comando de impulso de [helm helm az acr][az-acr-helm-push] no Azure CLI. Especifique o nome do seu gráfico Helm descarregado no passo anterior, como *wordpress-8.1.0.tgz:*

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Após alguns momentos, o Azure CLI informa que o seu gráfico é guardado, como mostra a seguinte saída de exemplo:

```
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Listas no repositório

Para utilizar o gráfico carregado no passo anterior, o índice de repositório Helm local deve ser atualizado. Pode reindexar os repositórios no cliente Helm, ou utilizar o Azure CLI para atualizar o índice de repositório. Cada vez que adicionar um gráfico ao seu repositório, este passo deve ser concluído:

```azurecli
az acr helm repo add --name mycontainerregistry
```

Com um gráfico armazenado no seu repositório e o índice atualizado disponível localmente, pode utilizar os comandos regulares do cliente Helm para pesquisar ou instalar. Para ver todas as tabelas do seu repositório, utilize o comando `helm search`, fornecendo o seu próprio nome de Registo de Contentores Azure:

```console
helm search mycontainerregistry
```

O gráfico Wordpress empurrado no passo anterior está listado, como mostra a seguinte saída de exemplo:

```
NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  8.1.0           5.3.2       Web publishing platform for building blogs and websites.
```

Também pode listar as tabelas com o Azure CLI, utilizando a lista de [leme az acr:][az-acr-helm-list]

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Mostrar informações para um gráfico helm

Para visualizar informações para um gráfico específico no repo, pode utilizar o comando `helm inspect`.

```console
helm inspect mycontainerregistry/wordpress
```

Quando não é fornecido nenhum número de versão, a *versão mais recente* é utilizada. O Helm devolve informações detalhadas sobre o seu gráfico, como mostra a seguinte saída de exemplo condensado:

```
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

Também pode mostrar a informação para um gráfico com o comando do helm show azure CLI [acr.][az-acr-helm-show] Mais uma vez, a *versão mais recente* de um gráfico é devolvida por padrão. Pode anexar `--version` para listar uma versão específica de um gráfico, como *8.1.0:*

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Instale um gráfico Helm a partir do repositório

O gráfico Helm no seu repositório é instalado especificando o nome do repositório e o nome da tabela. Utilize o cliente Helm para instalar o gráfico Wordpress:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Se você empurrar para o seu repositório de gráfico sonorar do registo de contentores Azure e voltar mais tarde numa nova sessão CLI, o seu cliente Helm local precisa de um token de autenticação atualizado. Para obter um novo símbolo de autenticação, use o comando de [az acr helm repo.][az-acr-helm-repo-add]

Os seguintes passos são concluídos durante o processo de instalação:

- O cliente Helm procura o índice de repositório local.
- O gráfico correspondente é descarregado do repositório do Registo de Contentores Azure.
- O gráfico é implantado usando o Tiller no seu cluster Kubernetes.

À medida que a instalação prossegue, siga as instruções na saída de comando para ver os URLs e credenciais WorPress. Você também pode executar o comando `kubectl get pods` para ver os recursos Kubernetes implantados através da tabela Helm:

```
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Eliminar um gráfico helm do repositório

Para eliminar um gráfico do repositório, utilize o comando de eliminação do [leme az acr.][az-acr-helm-delete] Especifique o nome do gráfico, como o *wordpress,* e a versão a eliminar, tal como *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Se desejar eliminar todas as versões do gráfico nomeado, deixe de fora o parâmetro `--version`.

O gráfico continua a ser devolvido quando corre `helm search`. Mais uma vez, o cliente Helm não atualiza automaticamente a lista de gráficos disponíveis num repositório. Para atualizar o índice de repo do cliente Helm, use o repo helm [acr add][az-acr-helm-repo-add] comando novamente:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Passos seguintes

Este artigo usou um gráfico helm existente do repositório *público estável.* Para obter mais informações sobre como criar e implementar gráficos Helm, consulte [os gráficos Helm em desenvolvimento.][develop-helm-charts]

Os gráficos de leme podem ser usados como parte do processo de construção do recipiente. Para mais informações, consulte utilize tarefas de registo de [contentores Azure][acr-tasks].

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
