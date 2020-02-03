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
ms.openlocfilehash: b1579adc00540a429170027b66c5d3e508bcb5d4
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718741"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Usar um repositório Helm privado no Azure Dev Spaces

[Helm][helm] é gerente de pacotes da Kuberentes. Helm usa um formato [gráfico][helm-chart] para embalar dependências. Os gráficos Helm são armazenados em um repositório, que pode ser público ou privado. Azure Dev Spaces recupera apenas gráficos Helm de repositórios públicos ao executar o aplicativo. Nos casos em que o repositório Helm é privado ou Azure Dev Spaces não pode acessá-lo, você pode adicionar um gráfico desse repositório diretamente ao seu aplicativo. Adicionar o gráfico diretamente permite que Azure Dev Spaces execute seu aplicativo sem precisar acessar o repositório Helm privado.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Adicionar o repositório Helm privado ao computador local

Utilize a atualização de [repo de helm e][helm-repo-add] helm [repo][helm-repo-update] para aceder ao repositório helm privado da sua máquina local.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Adicionar o gráfico ao seu aplicativo

Navegue para o diretório do seu projeto e gere`azds prep`.

```cmd
azds prep --public
```

> [!TIP]
> O comando `prep` tenta gerar [um dockerfile e gráfico helm](../how-dev-spaces-works.md#prepare-your-code) para o seu projeto. O Azure Dev Spaces usa esses arquivos para compilar e executar seu código, mas você pode modificar esses arquivos se quiser alterar a forma como o projeto é compilado e executado.

Crie um ficheiro [requisitos.yaml][helm-requirements] com a sua ficha no diretório de gráficos da sua aplicação. Por exemplo, se a sua aplicação for nomeada *app1,* criará *gráficos/app1/requisitos.yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Navegue para o diretório de gráficos da sua aplicação e use a [atualização][helm-dependency-update] da dependência do leme para atualizar as dependências helm para a sua aplicação e descarregue o gráfico do repositório privado.

```cmd
helm dependency update
```

Verifique se um subdiretório de *gráficos* com um ficheiro *TGZ* foi adicionado ao diretório de gráficos da sua aplicação. Por exemplo, *gráficos/app1/charts/mychart-0.1.0.tgz*.

O gráfico do seu repositório Helm privado foi baixado e adicionado ao seu projeto. Remova os *requisitos.yaml* ficheiro para que a Dev Spaces não tente atualizar esta dependência.

## <a name="run-your-application"></a>Executar a aplicação

Navegue para o diretório raiz do seu projeto e execute `azds up` para verificar se a sua aplicação corre com sucesso no seu espaço de dev.

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

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre [Helm e como funciona.][helm]

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies