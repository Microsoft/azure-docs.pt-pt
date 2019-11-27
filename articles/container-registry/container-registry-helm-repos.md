---
title: Armazenar gráficos do Helm
description: Saiba como usar um repositório Helm com o registro de contêiner do Azure para armazenar gráficos para seus aplicativos
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 0c5e66d5f2fc3dd3c2d8c0a975c3e9d1c813732d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456341"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Usar o registro de contêiner do Azure como um repositório Helm para seus gráficos de aplicativo

Para gerenciar e implantar aplicativos rapidamente para o kubernetes, você pode usar o [Gerenciador de pacotes do Helm de código aberto][helm]. Com o Helm, os aplicativos são definidos como *gráficos* armazenados em um repositório de gráfico do Helm. Esses gráficos definem configurações e dependências e podem ter a versão em todo o ciclo de vida do aplicativo. O registro de contêiner do Azure pode ser usado como o host para repositórios de gráfico Helm.

Com o registro de contêiner do Azure, você tem um repositório de gráfico de Helm privado seguro, que pode ser integrado com pipelines de compilação ou outros serviços do Azure. Os repositórios de gráfico Helm no registro de contêiner do Azure incluem recursos de replicação geográfica para manter seus gráficos próximos às implantações e à redundância. Você paga apenas pelo armazenamento usado pelos gráficos e está disponível em todas as camadas de preço do registro de contêiner do Azure.

Este artigo mostra como usar um repositório de gráfico de Helm armazenado no registro de contêiner do Azure.

> [!IMPORTANT]
> Esta funcionalidade encontra-se em pré-visualização. As pré-visualizações são tornadas disponíveis para si na condição de concordar com os [termos suplementares de utilização][terms-of-use]. Alguns aspetos desta funcionalidade podem alterar-se após a disponibilidade geral (GA).

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas neste artigo, os pré-requisitos a seguir devem ser atendidos:

- **Registro de contêiner do Azure** -crie um registro de contêiner em sua assinatura do Azure. Por exemplo, use o [portal do Azure](container-registry-get-started-portal.md) ou o [CLI do Azure](container-registry-get-started-azure-cli.md).
- **Helm Client versão 2.11.0 (não é uma versão RC) ou posterior** -execute `helm version` para localizar a versão atual. Você também precisa de um servidor Helm (gaveta) inicializado em um cluster kubernetes. Se necessário, você pode [criar um cluster do serviço kubernetes do Azure][aks-quickstart]. Para obter mais informações sobre como instalar e atualizar o Helm, consulte [instalando o Helm][helm-install].
- **CLI do Azure versão 2.0.46 ou posterior** -execute `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Adicionar um repositório ao cliente do Helm

Um repositório Helm é um servidor HTTP que pode armazenar gráficos Helm. O registro de contêiner do Azure pode fornecer esse armazenamento para gráficos Helm e gerenciar a definição de índice à medida que você adiciona e remove gráficos no repositório.

Para adicionar o registro de contêiner do Azure como um repositório de gráfico Helm, use o CLI do Azure. Com essa abordagem, o cliente Helm é atualizado com o URI e as credenciais para o repositório apoiado pelo registro de contêiner do Azure. Você não precisa especificar manualmente essas informações do repositório, portanto, as credenciais não são expostas no histórico de comandos, por exemplo.

Se necessário, faça logon no CLI do Azure e siga os prompts:

```azurecli
az login
```

Configure o CLI do Azure padrões com o nome do seu registro de contêiner do Azure usando o comando [AZ configure][az-configure] . No exemplo a seguir, substitua `<acrName>` pelo nome do registro:

```azurecli
az configure --defaults acr=<acrName>
```

Agora, adicione o repositório de gráficos Helm do registro de contêiner do Azure ao cliente Helm usando o comando [AZ ACR Helm Repository Add][az-acr-helm-repo-add] . Esse comando obtém um token de autenticação para o registro de contêiner do Azure que é usado pelo cliente Helm. O token de autenticação é válido por 1 hora. Semelhante à `docker login`, você pode executar esse comando em sessões de CLI futuras para autenticar o cliente Helm com o repositório de gráfico Helm do registro de contêiner do Azure:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Adicionar um gráfico ao repositório

Para este artigo, vamos obter um gráfico de Helm existente do repositório *estável* Helm público. O repositório *estável* é um repositório público e organizado que inclui os gráficos de aplicativos comuns. Os mantenedores de pacote podem enviar seus gráficos para o repositório *estável* , da mesma forma que o Hub do Docker fornece um registro público para imagens de contêiner comuns. O gráfico baixado do repositório *estável* público pode ser enviado por push para seu repositório privado do registro de contêiner do Azure. Na maioria dos cenários, você criaria e carregaria seus próprios gráficos para os aplicativos que você desenvolve. Para obter mais informações sobre como criar seus próprios gráficos do Helm, consulte [desenvolvendo gráficos do Helm][develop-helm-charts].

Primeiro, crie um diretório em *~/ACR-Helm*e baixe o gráfico *estável/WordPress* existente:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Liste o gráfico baixado e anote a versão do WordPress incluída no nome do arquivo. O comando `helm fetch stable/wordpress` não especificou uma versão específica, portanto, a versão *mais recente* foi buscada. Todos os gráficos do Helm incluem um número de versão no nome de arquivo que segue o padrão [SemVer 2][semver2] . Na saída de exemplo a seguir, o gráfico do WordPress é a versão *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Agora, envie o gráfico por push ao seu repositório de gráficos do Helm no registro de contêiner do Azure usando o comando CLI do Azure [AZ ACR Helm Push][az-acr-helm-push] . Especifique o nome do seu gráfico de Helm baixado na etapa anterior, como *WordPress-2.1.10. tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Após alguns instantes, a CLI do Azure relata que o gráfico foi salvo, conforme mostrado na seguinte saída de exemplo:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Listar gráficos no repositório

O cliente Helm mantém uma cópia armazenada em cache local do conteúdo de repositórios remotos. As alterações em um repositório remoto não atualizam automaticamente a lista de gráficos disponíveis conhecidos localmente pelo cliente do Helm. Quando você procura gráficos entre repositórios, o Helm usa o índice armazenado em cache local. Para usar o gráfico carregado na etapa anterior, o índice do repositório Helm local deve ser atualizado. Você pode reindexar os repositórios no cliente Helm ou usar o CLI do Azure para atualizar o índice do repositório. Cada vez que você adiciona um gráfico ao repositório, essa etapa deve ser concluída:

```azurecli
az acr helm repo add
```

Com um gráfico armazenado em seu repositório e o índice atualizado disponível localmente, você pode usar os comandos de cliente Helm regulares para pesquisar ou instalar. Para ver todos os gráficos em seu repositório, use `helm search <acrName>`. Forneça seu próprio nome de registro de contêiner do Azure:

```console
helm search <acrName>
```

O gráfico do WordPress enviado por push na etapa anterior é listado, conforme mostrado na seguinte saída de exemplo:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Você também pode listar os gráficos com o CLI do Azure, usando [AZ ACR Helm List][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Mostrar informações de um gráfico do Helm

Para exibir informações de um gráfico específico no repositório, você pode usar novamente o cliente normal do Helm. Para ver as informações do gráfico chamado *WordPress*, use `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

Quando nenhum número de versão é fornecido, a versão *mais recente* é usada. Helm retorna informações detalhadas sobre o gráfico, conforme mostrado na seguinte saída de exemplo condensada:

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

Você também pode mostrar as informações de um gráfico com o comando CLI do Azure [AZ ACR Helm show][az-acr-helm-show] . Novamente, a versão *mais recente* de um gráfico é retornada por padrão. Você pode acrescentar `--version` para listar uma versão específica de um gráfico, como *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Instalar um gráfico do Helm do repositório

O gráfico Helm em seu repositório é instalado especificando o nome do repositório e, em seguida, o nome do gráfico. Use o cliente Helm para instalar o gráfico do WordPress:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Se você enviar por push para o repositório de gráficos Helm do registro de contêiner do Azure e retornar posteriormente em uma nova sessão da CLI, o cliente Helm local precisará de um token de autenticação atualizado. Para obter um novo token de autenticação, use o comando [AZ ACR Helm repositório Add][az-acr-helm-repo-add] .

As etapas a seguir são concluídas durante o processo de instalação:

- O cliente Helm pesquisa o índice do repositório local.
- O gráfico correspondente é baixado do repositório do registro de contêiner do Azure.
- O gráfico é implantado usando o gaveta no cluster kubernetes.

A seguinte saída de exemplo condensada mostra os recursos de kubernetes implantados por meio do gráfico Helm:

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

## <a name="delete-a-helm-chart-from-the-repository"></a>Excluir um gráfico do Helm do repositório

Para excluir um gráfico do repositório, use o comando [AZ ACR Helm Delete][az-acr-helm-delete] . Especifique o nome do gráfico, como *WordPress*, e a versão a ser excluída, como *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Se você quiser excluir todas as versões do gráfico nomeado, deixe o parâmetro `--version`.

O gráfico continua a ser retornado em `helm search <acrName>`. Novamente, o cliente Helm não atualiza automaticamente a lista de gráficos disponíveis em um repositório. Para atualizar o índice do repositório de cliente Helm, use o comando [AZ ACR Helm do repositório Add][az-acr-helm-repo-add] novamente:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Passos seguintes

Este artigo usou um gráfico de Helm existente do repositório *estável* público. Para obter mais informações sobre como criar e implantar gráficos do Helm, consulte [desenvolvendo gráficos do Helm][develop-helm-charts].

Os gráficos do Helm podem ser usados como parte do processo de criação do contêiner. Para obter mais informações, consulte [usar tarefas do registro de contêiner do Azure][acr-tasks].

Para obter mais informações sobre como usar e gerenciar o registro de contêiner do Azure, consulte as [práticas recomendadas][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/topics/charts/
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
