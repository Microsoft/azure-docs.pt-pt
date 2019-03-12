---
title: Utilizar repositórios do Helm no Azure Container Registry
description: Saiba como utilizar um repositório Helm com o Azure Container Registry para armazenar gráficos para as suas aplicações
services: container-registry
author: iainfoulds
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: ba0e1386d67e920f1805d244f9042044bb462ec9
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57551998"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Utilizar o Azure Container Registry como um repositório Helm para seus gráficos de aplicação

Para gerir e implementar aplicações para Kubernetes rapidamente, pode utilizar o [Gestor de pacotes do código-fonte aberto Helm][helm]. Com o Helm, os aplicativos são definidos como *gráficos* que são armazenados num repositório de gráficos do Helm. Estes gráficos definem configurações e as dependências e podem ser atualizados em todo o ciclo de vida do aplicativo. O Azure Container Registry pode ser utilizado como o host para repositórios de gráficos do Helm.

Com o Azure Container Registry, tem um privado e seguro gráfico repositório Helm, que pode ser integrado com pipelines de compilação ou outros serviços do Azure. Repositórios de gráficos do Helm no Azure Container Registry incluem funcionalidades de georreplicação para manter seus gráficos próximo de implementações e para a redundância. Só paga o armazenamento utilizado por gráficos e estão disponíveis em todos os escalões de preço do Azure Container Registry.

Este artigo mostra-lhe como utilizar um repositório de gráficos do Helm armazenado no Azure Container Registry.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações ser-lhe-ão disponibilizadas na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="before-you-begin"></a>Antes de começar

Para concluir os passos neste artigo, devem ser cumpridos os seguintes pré-requisitos:

- **O Azure Container Registry** -criar um registo de contentor na sua subscrição do Azure. Por exemplo, utilizar o [portal do Azure](container-registry-get-started-portal.md) ou o [CLI do Azure](container-registry-get-started-azure-cli.md).
- **Versão de cliente do Helm 2.11.0 (não uma versão RC) ou posterior** – execução `helm version` para encontrar a versão atual. Também precisa de um servidor Helm (Tiller) inicializado dentro de um cluster de Kubernetes. Se for necessário, pode [criar um cluster do Azure Kubernetes Service][aks-quickstart]. Para obter mais informações sobre como instalar e atualizar o Helm, consulte [instalar o Helm][helm-install].
- **CLI do Azure versão 2.0.46 ou posterior** – execução `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Adicionar um repositório para o cliente do Helm

Um repositório Helm é um servidor HTTP, que pode armazenar gráficos do Helm. O Azure Container Registry pode fornecer esse armazenamento para gráficos Helm e gerir a definição do índice à medida que adiciona e remover gráficos para o repositório.

Para adicionar o seu registo de contentor do Azure como um repositório de gráficos do Helm, utilize a CLI do Azure. Com esta abordagem, o cliente do Helm é atualizado com as credenciais para o repositório apoiadas pelo Azure Container Registry e URI. Não precisa de especificar manualmente as informações neste repositório, para que as credenciais não são expostas no histórico do comando, por exemplo.

Se for necessário, inicie sessão para a CLI do Azure e siga as instruções:

```azurecli
az login
```

Configurar as predefinições da CLI do Azure com o nome do seu Azure Container Registry com o [az configure] [ az-configure] comando. No exemplo seguinte, substitua `<acrName>` com o nome do seu registo:

```azurecli
az configure --defaults acr=<acrName>
```

Agora, adicione o repositório de gráfico do Helm de registo de contentor do Azure para o cliente de Helm utilizando o [adicione o repositório helm do az acr] [ az-acr-helm-repo-add] comando. Este comando obtém uma autenticação de token para o seu registo de contentor do Azure que é utilizado pelo cliente do Helm. O token de autenticação é válido durante uma hora. Semelhante à `docker login`, pode executar este comando em futuras sessões da CLI para autenticar o cliente do Helm com o seu repositório de gráfico do Helm de registo de contentor do Azure:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Adicionar um gráfico para o repositório

Neste artigo, vamos obter um gráfico Helm existente do Helm pública *estável* repositório. O *estável* repositório é um repositório organizado, público que inclui gráficos comum de aplicativo. Maintainers de pacote podem enviar seus gráficos para o *estável* repositório, da mesma forma que o Docker Hub fornece um registo público para imagens de contentor comuns. O gráfico transferido do público *estável* repositório, em seguida, pode ser enviado para o seu repositório privado do Azure Container Registry. Na maioria dos cenários, teria de criar e carregar seus próprios gráficos para os aplicativos desenvolvidos. Para obter mais informações sobre como criar seus próprios gráficos Helm, consulte [desenvolver gráficos Helm][develop-helm-charts].

Em primeiro lugar, crie um diretório na *~/acr-helm*, em seguida, transfira o existente *stable/wordpress* gráfico:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Liste o gráfico transferido e tome nota da versão do Wordpress incluída no nome de ficheiro. O `helm fetch stable/wordpress` comando não especificou uma versão específica, por isso, o *mais recente* versão foi obtida. Todos os gráficos do Helm incluem um número de versão no nome de ficheiro que se segue a [SemVer 2] [ semver2] padrão. No exemplo seguinte, o gráfico de Wordpress é a versão *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Push agora o gráfico para o seu repositório de gráfico do Helm no Azure Container Registry com a CLI do Azure [push do az acr helm] [ az-acr-helm-push] comando. Especifique o nome do gráfico Helm transferido no passo anterior, tal como *wordpress 2.1.10.tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Após alguns instantes, a CLI do Azure comunica que foi guardado o gráfico, conforme mostrado no seguinte exemplo:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Lista de gráficos no repositório

O cliente do Helm mantém uma cópia em cache local do conteúdo dos repositórios remotos. As alterações para um repositório remoto não atualizar automaticamente a lista de gráficos disponíveis conhecido localmente pelo cliente do Helm. Quando pesquisa gráficos em repositórios, o Helm utiliza é local em cache índice. Para utilizar o gráfico que carregou no passo anterior, o índice de repositório Helm local tem de ser atualizado. Pode reindexar os repositórios no cliente do Helm ou utilizar a CLI do Azure para atualizar o índice de repositório. Sempre que adicionar um gráfico para o seu repositório, este passo deve ser concluído:

```azurecli
az acr helm repo add
```

Com um gráfico armazenado no seu repositório e o índice atualizado disponível localmente, pode utilizar os comandos de cliente do Helm regulares para pesquisar ou instalar. Para ver todos os gráficos no seu repositório, utilize `helm search <acrName>`. Fornece seu próprio nome de registo de contentor do Azure:

```console
helm search <acrName>
```

O gráfico de Wordpress enviado no passo anterior está listado, conforme mostrado no seguinte exemplo:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Também pode listar os gráficos com a CLI do Azure, utilizando [lista de helm az acr][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Mostrar informações para um gráfico do Helm

Para ver as informações de um gráfico específico no repositório, pode utilizar novamente o cliente regular do Helm. Para ver informações para o gráfico com o nome *wordpress*, utilize `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

Quando não for fornecido nenhum número de versão, o *mais recente* é utilizada a versão. Helm devolve informações detalhadas sobre o gráfico, conforme mostrado no seguinte exemplo condensado:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
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
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

Também pode mostrar as informações para um gráfico com a CLI do Azure [show do az acr helm] [ az-acr-helm-show] comando. Novamente, o *mais recente* for devolvida a versão de um gráfico por predefinição. Pode acrescentar `--version` para listar uma versão específica de um gráfico, como *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Instalar um gráfico do Helm do repositório

O gráfico do Helm no seu repositório é instalado, especificando o nome do repositório e, em seguida, nome do gráfico. Utilize o cliente do Helm para instalar o Wordpress gráfico:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Se enviar por push para o seu repositório de gráfico do Helm de registo de contentor do Azure e regressar mais tarde numa nova sessão da CLI, o seu cliente local do Helm tem um token de autenticação atualizado. Para obter um novo token de autenticação, utilize o [adicione o repositório helm do az acr] [ az-acr-helm-repo-add] comando.

Os seguintes passos são concluídos durante o processo de instalação:

- O cliente do Helm procura o índice do repositório local.
- O gráfico correspondente é transferido a partir do repositório do Azure Container Registry.
- O gráfico é implementado com o Tiller no cluster do Kubernetes.

O resultado de exemplo condensado seguinte mostra os recursos de Kubernetes implementados através do gráfico Helm:

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Eliminar um gráfico do Helm do repositório

Para eliminar um gráfico a partir do repositório, utilize o [delete do az acr helm] [ az-acr-helm-delete] comando. Especifique o nome do gráfico, tal como *wordpress*e a versão para eliminar, tal como *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Se pretender eliminar todas as versões do gráfico com nome, inclua o `--version` parâmetro.

O gráfico continua a ser devolvido num `helm search <acrName>`. Novamente, o cliente do Helm não atualiza automaticamente a lista de gráficos disponíveis num repositório. Para atualizar o índice de repositório Helm cliente, utilize o [adicione o repositório helm do az acr] [ az-acr-helm-repo-add] novamente o comando:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Passos Seguintes

Este artigo usou um gráfico Helm existente do público *estável* repositório. Para obter mais informações sobre como criar e implementar gráficos Helm, consulte [gráficos Helm desenvolvendo][develop-helm-charts].

Gráficos Helm podem ser utilizados como parte do processo de compilação de contentor. Para obter mais informações, consulte [utilizar tarefas de registo de contentor do Azure][acr-tasks].

Para obter mais informações sobre como utilizar e gerir o Azure Container Registry, veja a [melhores práticas][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://docs.helm.sh/developing_charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
