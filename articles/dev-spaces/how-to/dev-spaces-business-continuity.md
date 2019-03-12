---
title: Recuperação de desastre e continuidade de negócio nos espaços de desenvolvimento do Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Desenvolvimento rápido da Kubernetes com contentores e microsserviços no Azure
keywords: 'Docker, o Kubernetes, o Azure, o AKS, o serviço Kubernetes do Azure, contentores, Helm, a malha de serviço, roteamento de malha do serviço, kubectl, k8s '
manager: jeconnoc
ms.openlocfilehash: 7b463be143ed3f89c1b10424dafc7a0e841ecbfc
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775650"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Recuperação de desastre e continuidade de negócio nos espaços de desenvolvimento do Azure

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Reveja as orientações sobre a recuperação após desastre para o Azure Kubernetes Service (AKS)

Espaços de desenvolvimento do Azure é um recurso do Azure Kubernetes Service (AKS). Deve estar ciente de diretrizes para recuperação após desastre no AKS e considerar se aplicam-se para os clusters do AKS que utiliza para espaços de desenvolvimento. Para obter mais informações, consulte [melhores práticas para o negócio continuidade e recuperação após desastre no Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Ativar os espaços de desenvolvimento em clusters do AKS em regiões diferentes

Ativar espaços de desenvolvimento em clusters do AKS em regiões diferentes permite-lhe continuar a utilizar espaços de desenvolvimento imediatamente após uma falha de região do Azure.

Para obter informações gerais sobre implementações em várias regiões do AKS, consulte [planear a implementação em várias regiões](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multi-region-deployment)

Para obter informações sobre como implementar um cluster do AKS que é compatível com os espaços de desenvolvimento do Azure, consulte [criar um cluster de Kubernetes com o Azure Cloud Shell](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Ativar os espaços de desenvolvimento através do portal do Azure

Clique nas **Dev espaços** item de navegação em Propriedades de cada cluster no portal do Azure. Em seguida, escolha a opção para ativar os espaços de desenvolvimento.

![Ativar espaços de desenvolvimento através do portal do Azure](../media/common/enable-dev-spaces.jpg)

Repita este processo para cada cluster.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Ativar os espaços de desenvolvimento através da CLI do Azure

Também pode ativar os espaços de desenvolvimento na linha de comandos:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Implementar a linha de base da sua equipe a cada cluster

Ao trabalhar com os espaços de desenvolvimento, normalmente, implementa toda a aplicação para um espaço de desenvolvimento principal no seu cluster de Kubernetes. Por predefinição, o `default` espaço é utilizado. A implementação inicial inclui todos os serviços, bem como os recursos externos que esses serviços estão dependentes, tais como bases de dados ou filas. Isso é conhecido como o *linha de base*. Depois de configurar uma linha de base no espaço de desenvolvimento principal, que reanalisa e depurar serviços individuais dentro de espaços de desenvolvimento do filho.

Deve implementar as versões mais recentes do seu conjunto de linha de base dos serviços de clusters em várias regiões. A atualizar os serviços de linha de base desta forma, garante que pode continuar a utilizar espaços de desenvolvimento se ocorrer uma falha de região do Azure. Por exemplo, se implementar a linha de base por meio de um pipeline CI/CD, modifique o pipeline, para que o implementa vários clusters em regiões diferentes.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Selecione o cluster do AKS correto a utilizar para espaços de desenvolvimento

Uma vez que configurou corretamente um cluster de cópia de segurança com a linha de base da sua equipe, pode alternar rapidamente ao longo para o cluster de cópia de segurança em qualquer altura. Em seguida, pode executar novamente os serviços individuais que lhe está a trabalhar nos espaços de desenvolvimento.

Selecione outro cluster com o seguinte comando da CLI:

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Pode listar os espaços de desenvolvimento disponíveis no novo cluster com o seguinte comando:

```cmd
azds space list
```

Pode criar um novo espaço de desenvolvimento para trabalhar, ou selecione um espaço de desenvolvimento existente, com o seguinte comando:

```cmd
azds space select -n <space name>
```

Depois de executar estes comandos, o cluster selecionado e o espaço de desenvolvimento serão utilizados para operações subsequentes da CLI e para depuração de projetos usando a extensão do Visual Studio Code para espaços de desenvolvimento do Azure.

Se estiver a utilizar o Visual Studio, pode alternar o cluster utilizado por um projeto existente, os seguintes passos:

1. Abra o projeto no Visual Studio.
1. Clique com o botão direito no nome do projeto no Explorador de soluções e clique em **propriedades**
1. No painel esquerdo, clique em **depurar**
1. Na página de propriedades de depuração, clique nas **perfil** pendente lista e escolha **Azure Dev espaços**.
1. Clique nas **alteração** botão.
1. Na caixa de diálogo que aparece, selecione o cluster do AKS que deseja usar. Se assim o desejar, escolher um espaço de desenvolvimento diferentes para funcionar em ou criar um novo espaço de desenvolvimento, selecionando a opção adequada a partir da **espaço** na lista pendente.

Assim que tiver selecionado o cluster correto e o espaço, pode premir F5 para executar o serviço nos espaços de desenvolvimento.

Repita estes passos para todos os outros projetos configurados para utilizar o cluster original.

## <a name="access-a-service-on-a-backup-cluster"></a>Aceder a um serviço num cluster de cópia de segurança

Se tiver configurado o seu serviço para utilizar um nome DNS público, em seguida, o serviço terá um URL diferente se executá-lo num cluster de cópia de segurança. Nomes DNS públicos são sempre no formato `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`. Se mudar para outro cluster, o GUID do cluster e, possivelmente, a região serão alterado.

Espaços de desenvolvimento sempre mostra o URL correto para o serviço ao executar o `azds up`, ou na janela de saída no Visual Studio sob **do Azure Dev espaços**.

Também pode encontrar o URL ao executar o `azds list-uris` comando:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Utilize este URL ao acessar o serviço.
