---
title: Continuidade de negócio e recuperação após desastre
services: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Aprenda a utilizar a Azure Dev Spaces e a Azure Kubernetes Services para proporcionar continuidade ao negócio e preparar-se para a recuperação de desastres
keywords: 'Docker, kubernetes, Azure, AKS, serviço kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, K8S '
manager: gwallace
ms.openlocfilehash: 8561486bcf8524d309ba46fcbb4b4d3e180d7c72
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252393"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Continuidade de negócios e recuperação de desastres em Espaços Azure Dev

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Rever orientação de recuperação de desastres para o Serviço Azure Kubernetes (AKS)

Azure Dev Spaces é uma característica do Serviço Azure Kubernetes (AKS). Deve estar ciente das diretrizes para a recuperação de desastres na AKS e ponderar se se aplicam aos clusters AKS que utiliza para espaços de dev. Para mais informações, consulte [as melhores práticas para a continuidade do negócio e recuperação de desastres no Serviço Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region)

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Ativar espaços de dev em clusters AKS em diferentes regiões

Permitir espaços de dev em aglomerados AKS em diferentes regiões permite-lhe retomar a utilização de Espaços Dev imediatamente após uma falha na região de Azure.

Para obter informações gerais sobre implantações multi-regiões do AKS, consulte [plano de implantação multi-regiões](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region#plan-for-multiregion-deployment)

Para obter informações sobre a implementação de um cluster AKS compatível com os Espaços Azure Dev, consulte [Create a Kubernetes cluster usando azure Cloud Shell](https://docs.microsoft.com/azure/dev-spaces/how-to/create-cluster-cloud-shell)

### <a name="enable-dev-spaces-via-the-azure-portal"></a>Ativar espaços de dev através do portal Azure

Clique no item de navegação **de Dev Spaces** sob as propriedades de cada cluster no portal Azure. Em seguida, escolha a opção de ativar os Espaços Dev.

![Habilitar os Espaços Dev através do portal Azure](../media/common/enable-dev-spaces.jpg)

Repita este processo para cada cluster.

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Ativar espaços de dev através do Azure CLI

Também pode ativar os Espaços Dev na linha de comando:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>Desloque a linha de base da sua equipa para cada cluster

Ao trabalhar com a Dev Spaces, você normalmente implementa toda a aplicação para um espaço de dev dos pais no seu cluster Kubernetes. Por padrão, o espaço `default` é utilizado. A implantação inicial inclui todos os serviços, bem como os recursos externos de que esses serviços dependem, como bases de dados ou filas. Isto é conhecido como a *linha de base.* Assim que configurar uma linha de base no espaço de dev dos pais, você itera e depura serviços individuais dentro de espaços de dev infantil.

Deve implementar as versões mais recentes do seu conjunto de serviços de base para clusters em várias regiões. Atualizar os seus serviços de base desta forma garante que pode continuar a utilizar espaços Dev se houver uma falha na região de Azure. Por exemplo, se implementar a sua linha de base através de um oleoduto CI/CD, modifique o gasoduto de modo a que se desloque para vários clusters em diferentes regiões.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Selecione o cluster AKS correto para usar para Espaços Dev

Depois de configurar corretamente um cluster de backup que executa a linha de base da sua equipa, pode rapidamente mudar para o cluster de backup a qualquer momento. Depois pode refazer os serviços individuais em que está a trabalhar em Espaços Dev.

Selecione um cluster diferente com o seguinte comando CLI:

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

Pode listar os espaços de v disponíveis no novo cluster com o seguinte comando:

```cmd
azds space list
```

Você pode criar um novo espaço de dev para trabalhar, ou selecionar um espaço de v existente, com o seguinte comando:

```cmd
azds space select -n <space name>
```

Após a execução destes comandos, o cluster selecionado e espaço de v será utilizado para operações subsequentes do CLI, e para projetos de depuração utilizando a extensão do Código do Estúdio Visual para espaços Azure Dev.

Se estiver a utilizar o Visual Studio, pode mudar o cluster utilizado por um projeto existente através dos seguintes passos:

1. Abra o seu projeto no Estúdio Visual.
1. Clique no nome do projeto no Solution Explorer e clique em **Propriedades**
1. No painel esquerdo, clique **em Debug**
1. Na página de propriedades do Debug, clique na lista de drop-down do **Perfil** e escolha **espaços Azure Dev**.
1. Clique no botão **Alterar.**
1. No diálogo que aparece, selecione o cluster AKS que deseja utilizar. Se desejar, escolha um espaço de dev diferente para trabalhar, ou crie um novo espaço de dev, selecionando a opção apropriada a partir da lista de drop-down **do Espaço.**

Depois de ter selecionado o cluster e o espaço corretos, pode premir F5 para executar o serviço em Espaços Dev.

Repita estes passos para quaisquer outros projetos configurados para utilizar o cluster original.

## <a name="access-a-service-on-a-backup-cluster"></a>Aceda a um serviço num cluster de backup

Se configurar o seu serviço para utilizar um nome Público de DNS, então o serviço terá um URL diferente se o executar num cluster de backup. Os nomes públicos do DNS estão sempre em formato `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io`. Se mudar para um cluster diferente, o cluster GUID e possivelmente a região mudarão.

A Dev Spaces mostra sempre o URL correto para o serviço ao executar `azds up`, ou na janela de saída em Estúdio Visual sob **espaços Azure Dev.**

Também pode encontrar o URL executando o comando `azds list-uris`:
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Utilize este URL ao aceder ao serviço.
