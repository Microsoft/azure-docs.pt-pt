---
title: Continuidade dos negócios e recuperação de desastres no Azure Dev Spaces
services: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Desenvolvimento rápido do Kubernetes com contentores e microsserviços no Azure
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
manager: gwallace
ms.openlocfilehash: f2c2767d23a99644ee4ecb4e1040162c58a72b1a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280094"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Continuidade dos negócios e recuperação de desastres no Azure Dev Spaces

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Examinar as diretrizes de recuperação de desastre para o serviço kubernetes do Azure (AKS)

Azure Dev Spaces é um recurso do AKS (serviço kubernetes do Azure). Você deve estar ciente das diretrizes para a recuperação de desastres em AKS e considerar se elas se aplicam aos clusters AKS que você usa para espaços de desenvolvimento. Para obter mais informações, consulte [as práticas recomendadas para continuidade dos negócios e recuperação de desastres no serviço de kubernetes do Azure (AKs)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Habilitar espaços de desenvolvimento em clusters AKS em regiões diferentes

Habilitar espaços de desenvolvimento em clusters AKS em regiões diferentes permite que você retome usando espaços de desenvolvimento imediatamente após uma falha de região do Azure.

Para obter informações gerais sobre implantações de várias regiões do AKS, consulte [planejar a implantação em várias regiões](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment)

Para obter informações sobre como implantar um cluster AKS compatível com o Azure Dev Spaces, consulte [criar um cluster kubernetes usando Azure cloud Shell](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Habilitar espaços de desenvolvimento por meio do portal do Azure

Clique no item de navegação **espaços de desenvolvimento** nas propriedades de cada cluster na portal do Azure. Em seguida, escolha a opção para habilitar espaços de desenvolvimento.

![Habilitando espaços de desenvolvimento via portal do Azure](../media/common/enable-dev-spaces.jpg)

Repita esse processo para cada cluster.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Habilitar espaços de desenvolvimento por meio do CLI do Azure

Você também pode habilitar espaços de desenvolvimento na linha de comando:

```cmd
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Implantar a linha de base da sua equipe em cada cluster

Ao trabalhar com espaços de desenvolvimento, normalmente você implanta todo o aplicativo em um espaço de desenvolvimento pai no cluster kubernetes. Por padrão, o espaço de `default` é usado. A implantação inicial inclui todos os serviços, bem como os recursos externos dos quais esses serviços dependem, como bancos de dados ou filas. Isso é conhecido como a *linha de base*. Depois de configurar uma linha de base no espaço de desenvolvimento pai, você itera e depura serviços individuais dentro de espaços de desenvolvimento filho.

Você deve implantar as versões mais recentes do conjunto de serviços de linha de base em clusters em várias regiões. Atualizar os serviços de linha de base dessa maneira garante que você possa continuar a usar espaços de desenvolvimento se houver uma falha de região do Azure. Por exemplo, se você implantar sua linha de base por meio de um pipeline de CI/CD, modifique o pipeline para que ele seja implantado em vários clusters em regiões diferentes.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Selecione o cluster AKS correto a ser usado para espaços de desenvolvimento

Depois de configurar corretamente um cluster de backup executando a linha de base da sua equipe, você pode alternar rapidamente para o cluster de backup a qualquer momento. Em seguida, você pode executar novamente os serviços individuais nos quais está trabalhando em espaços de desenvolvimento.

Selecione um cluster diferente com o seguinte comando da CLI:

```cmd
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Você pode listar os espaços de desenvolvimento disponíveis no novo cluster com o seguinte comando:

```cmd
azds space list
```

Você pode criar um novo espaço de desenvolvimento para trabalhar no ou selecionar um espaço de desenvolvimento existente, com o seguinte comando:

```cmd
azds space select -n <space name>
```

Depois de executar esses comandos, o cluster selecionado e o espaço de desenvolvimento serão usados para operações de CLI subsequentes e para depurar projetos usando a extensão de Visual Studio Code para Azure Dev Spaces.

Se você estiver usando o Visual Studio, poderá alternar o cluster usado por um projeto existente por meio das seguintes etapas:

1. Abra seu projeto no Visual Studio.
1. Clique com o botão direito do mouse no nome do projeto em Gerenciador de Soluções e clique em **Propriedades**
1. No painel esquerdo, clique em **depurar**
1. Na página Propriedades de depuração, clique na lista suspensa **perfil** e escolha **Azure dev Spaces**.
1. Clique no botão **alterar** .
1. Na caixa de diálogo que aparece, selecione o cluster AKS que você deseja usar. Se desejar, escolha um espaço de desenvolvimento diferente para trabalhar ou crie um novo espaço de desenvolvimento, selecionando a opção apropriada na lista suspensa **espaço** .

Depois de selecionar o cluster e o espaço corretos, você pode pressionar F5 para executar o serviço em espaços de desenvolvimento.

Repita essas etapas para todos os outros projetos configurados para usar o cluster original.

## <a name="access-a-service-on-a-backup-cluster"></a>Acessar um serviço em um cluster de backup

Se você tiver configurado seu serviço para usar um nome DNS público, o serviço terá uma URL diferente se você executá-lo em um cluster de backup. Os nomes DNS públicos sempre estão no formato `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`. Se você alternar para um cluster diferente, o GUID do cluster e, possivelmente, a região será alterado.

Os espaços de desenvolvimento sempre mostram a URL correta para o serviço ao executar `azds up`ou na janela de saída no Visual Studio em **Azure dev Spaces**.

Você também pode encontrar a URL executando o comando `azds list-uris`:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Use essa URL ao acessar o serviço.
