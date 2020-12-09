---
title: Continuidade de negócio e recuperação após desastre
services: azure-dev-spaces
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 01/28/2019
ms.topic: conceptual
description: Aprenda a utilizar a Azure Dev Spaces e a Azure Kubernetes Services para fornecer continuidade de negócios e preparar-se para a recuperação de desastres
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contentores, Helm, malha de serviço, encaminhamento de malha de serviço, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 77425d294dcea0546259b152b793cec291940c53
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929604"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Continuidade do negócio e recuperação de desastres em Espaços Azure Dev

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Rever orientação de recuperação de desastres para o Serviço Azure Kubernetes (AKS)

Azure Dev Spaces é uma característica do Serviço Azure Kubernetes (AKS). Deve estar atento às diretrizes para a recuperação de desastres na AKS e ponderar se se aplicam aos clusters AKS que utiliza para espaços Dev. Para mais informações, consulte [as melhores práticas para a continuidade do negócio e recuperação de desastres no Serviço Azure Kubernetes (AKS)](../../aks/operator-best-practices-multi-region.md)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Permitir espaços de dev em clusters AKS em diferentes regiões

Permitir espaços de dev em clusters AKS em diferentes regiões permite-lhe retomar a utilização de Dev Spaces imediatamente após uma falha na região de Azure.

Para obter informações gerais sobre as implantações multi-regiões da AKS, consulte [Plano de implantação de várias regiões](../../aks/operator-best-practices-multi-region.md#plan-for-multiregion-deployment)

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Ativar espaços Dev através do Azure CLI

Também pode ativar espaços Dev na linha de comando:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Desloque a linha de base da sua equipa para cada cluster

Ao trabalhar com a Dev Spaces, normalmente implanta toda a aplicação para um espaço dev dos pais no seu cluster Kubernetes. Por predefinição, o `default` espaço é utilizado. A implantação inicial inclui todos os serviços, bem como os recursos externos de que esses serviços dependem, como bases de dados ou filas. Isto é conhecido como a *linha de base.* Uma vez configurado uma linha de base no espaço dev dos pais, você iterou e depura serviços individuais dentro de espaços de dev infantis.

Deve implementar as versões mais recentes do seu conjunto de serviços de base para clusters em várias regiões. Atualizar os seus serviços de base desta forma garante que pode continuar a utilizar os Espaços Dev se houver uma falha na região de Azure. Por exemplo, se implementar a sua linha de base através de um gasoduto CI/CD, modifique o gasoduto de modo a que este se desloque a vários aglomerados em diferentes regiões.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Selecione o cluster AKS correto para usar para Espaços Dev

Uma vez configurado corretamente um cluster de backup que executa a linha de base da sua equipa, pode mudar rapidamente para o cluster de backup a qualquer momento. Em seguida, pode refazer os serviços individuais em que está a trabalhar em espaços de dev para crianças.

Selecione um cluster diferente com o seguinte comando CLI:

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Pode listar os espaços dev disponíveis no novo cluster com o seguinte comando:

```cmd
azds space list
```

Pode criar um novo espaço dev para trabalhar ou selecionar um espaço dev existente, com o seguinte comando:

```cmd
azds space select -n <space name>
```

Após a execução destes comandos, o cluster selecionado e espaço dev serão utilizados para operações clíboles subsequentes, e para projetos de depuração utilizando a extensão do Código do Estúdio Visual para Espaços Azure Dev.

Se estiver a utilizar o Visual Studio, pode mudar o cluster utilizado por um projeto existente através dos seguintes passos:

1. Abra o projeto no Visual Studio.
1. Clique no nome do projeto no Solution Explorer e clique em **Propriedades**
1. No painel esquerdo, clique em **Debug**
1. Na página propriedades Debug, clique **na** lista de drop-down profile e escolha **Azure Dev Spaces**.
1. Clique no botão **Alterar.**
1. No diálogo que aparece, selecione o cluster AKS que pretende utilizar. Se desejar, escolha um espaço dev diferente para trabalhar ou crie um novo espaço dev, selecionando a opção apropriada da lista de drop-down **do Espaço.**

Uma vez selecionado o cluster e o espaço corretos, pode premir F5 para executar o serviço em Dev Spaces.

Repita estes passos para quaisquer outros projetos configurados para utilizar o cluster original.

## <a name="access-a-service-on-a-backup-cluster"></a>Aceda a um serviço num cluster de backup

Se configurar o seu serviço para usar um nome DNS público, então o serviço terá um URL diferente se o executar num cluster de backup. Os nomes dns públicos estão sempre no formato `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io` . Se mudar para um cluster diferente, o cluster GUID e possivelmente a região mudarão.

Dev Spaces mostra sempre o URL correto para o serviço quando está `azds up` em execução, ou na janela de saída em Estúdio Visual sob **Azure Dev Spaces**.

Também pode encontrar o URL executando o `azds list-uris` comando:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Utilize este URL ao aceder ao serviço.
