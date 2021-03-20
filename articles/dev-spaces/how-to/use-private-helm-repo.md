---
title: Como usar um repositório helm privado em Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Use um repositório helm privado num espaço Azure Dev.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contentores, Helm
manager: gwallace
ms.openlocfilehash: 7c5f28595df2e552fd48033b44e4e1f0ea4ec306
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "91960342"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Use um repositório de leme privado em Azure Dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

[Helm][helm] é um gestor de pacotes para Kubernetes. Helm usa um formato [de gráfico][helm-chart] para pacotes dependências. Os gráficos de leme são armazenados num repositório, que pode ser público ou privado. A Azure Dev Spaces só recupera gráficos helm de repositórios públicos ao executar a sua aplicação. Nos casos em que o repositório Helm é privado ou a Azure Dev Spaces não pode aceder a ele, pode adicionar um gráfico desse repositório diretamente à sua aplicação. Adicionar o gráfico diretamente permite que a Azure Dev Spaces execute a sua aplicação sem ter de aceder ao repositório helm privado.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Adicione o repositório helm privado à sua máquina local

Use [o leme repo add][helm-repo-add] e a [atualização do repo de leme][helm-repo-update] para aceder ao repositório helm privado da sua máquina local.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Adicione o gráfico à sua aplicação

Navegue para o diretório do seu projeto e `azds prep` corra.

```cmd
azds prep --enable-ingress
```

> [!TIP]
> O `prep` comando tenta gerar um gráfico de [Dockerfile e Helm](../how-dev-spaces-works-prep.md#prepare-your-code) para o seu projeto. O Azure Dev Spaces utiliza estes ficheiros para construir e executar o seu código, mas pode modificar estes ficheiros se quiser alterar a forma como o projeto é construído e executado.

Crie um ficheiro [requirements.yaml][helm-requirements] com o seu gráfico no diretório de gráficos da sua aplicação. Por exemplo, se a sua aplicação for nomeada *app1,* criará *gráficos/app1/requirements.yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Navegue no diretório de gráficos da sua aplicação e use a [atualização da dependência][helm-dependency-update] do leme para atualizar as dependências helm para a sua aplicação e descarregue o gráfico a partir do repositório privado.

```cmd
helm dependency update
```

Verifique se uma subdiretório *de gráficos* com um ficheiro *tgz* foi adicionado ao diretório de gráficos da sua aplicação. Por exemplo, *gráficos/app1/charts/mychart-0.1.0.tgz*.

O gráfico do seu repositório helm privado foi descarregado e adicionado ao seu projeto. Remova o ficheiro *requirements.yaml* para que o Dev Spaces não tente atualizar esta dependência.

## <a name="run-your-application"></a>Executar a aplicação

Navegue até ao diretório de raiz do seu projeto e corra `azds up` para verificar se a sua aplicação funciona com sucesso no seu espaço dev.

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

Saiba mais sobre [Helm e como funciona.][helm]

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
