---
title: Como usar um repositório Helm privado no Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Use um repositório Helm privado em um espaço de desenvolvimento do Azure.
keywords: Docker, kubernetes, Azure, AKS, serviço de contêiner do Azure, contêineres, Helm
manager: gwallace
ms.openlocfilehash: f212df74ab8102391e4170ccef6b0c3b1129b046
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279928"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Usar um repositório Helm privado no Azure Dev Spaces

[Helm][helm] é um Gerenciador de pacotes para Kuberentes. Helm usa um formato de [gráfico][helm-chart] para dependências de pacote. Os gráficos Helm são armazenados em um repositório, que pode ser público ou privado. Azure Dev Spaces recupera apenas gráficos Helm de repositórios públicos ao executar o aplicativo. Nos casos em que o repositório Helm é privado ou Azure Dev Spaces não pode acessá-lo, você pode adicionar um gráfico desse repositório diretamente ao seu aplicativo. Adicionar o gráfico diretamente permite que Azure Dev Spaces execute seu aplicativo sem precisar acessar o repositório Helm privado.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Adicionar o repositório Helm privado ao computador local

Use o repositório [Helm Add][helm-repo-add] e a [atualização do repositório Helm][helm-repo-update] para acessar o depósito Helm privado do seu computador local.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Adicionar o gráfico ao seu aplicativo

Navegue até o diretório do seu projeto e execute `azds prep`.

```cmd
azds prep --public
```

Crie um arquivo [requirements. YAML][helm-requirements] com seu gráfico no diretório do gráfico do seu aplicativo. Por exemplo, se seu aplicativo for denominado *App1*, você criará *gráficos/App1/requirements. YAML*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Navegue até o diretório do gráfico do seu aplicativo e use a [atualização de dependência do Helm][helm-dependency-update] para atualizar as dependências do Helm para seu aplicativo e baixe o gráfico do repositório privado.

```cmd
helm dependency update
```

Verifique se um subdiretório de *gráficos* com um arquivo *tgz* foi adicionado ao diretório de gráfico do seu aplicativo. Por exemplo, *Charts/App1/Charts/myChart-0.1.0. tgz*.

O gráfico do seu repositório Helm privado foi baixado e adicionado ao seu projeto. Remova o arquivo *requirements. YAML* para que os espaços de desenvolvimento não tentem atualizar essa dependência.

## <a name="run-your-application"></a>Executar a aplicação

Navegue até o diretório raiz do seu projeto e execute `azds up` para verificar se o aplicativo é executado com êxito em seu espaço de desenvolvimento.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Helm e como ele funciona][helm].

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://v2.helm.sh/docs/helm/#helm-dependency-update
[helm-repo-add]: https://v2.helm.sh/docs/helm/#helm-repo-add
[helm-repo-update]: https://v2.helm.sh/docs/helm/#helm-repo-update
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies