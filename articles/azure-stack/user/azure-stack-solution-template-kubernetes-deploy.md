---
title: Implementar o Kubernetes para o Azure Stack | Documentos da Microsoft
description: Saiba como implementar o Kubernetes no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 6b00f63fac0110a8964270b9cbcad5330ac44645
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56986248"
---
# <a name="deploy-kubernetes-to-azure-stack"></a>Implementar o Kubernetes para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!Note]  
> Kubernetes no Azure Stack está em pré-visualização. Cenário de desligado de pilha do Azure não é atualmente suportado pela pré-visualização.

Pode seguir os passos neste artigo para implementar e configurar os recursos do Kubernetes numa operação única e coordenada. Os passos utilizam um modelo de solução do Azure Resource Manager. Irá precisar para recolher as informações necessárias sobre a instalação do Azure Stack, gerar o modelo e, em seguida, implementar a sua cloud. O modelo do Azure Stack não usa o mesmo serviço AKS gerido oferecido no global Azure.

## <a name="kubernetes-and-containers"></a>Kubernetes e contentores

Pode instalar o Kubernetes com modelos do Azure Resource Manager gerados pelo motor de ACS no Azure Stack. [Kubernetes](https://kubernetes.io) é um sistema de código-fonte aberto para automatizar a implantação, dimensionamento e da gestão de aplicações em contentores. R [contentor](https://www.docker.com/what-container) está numa imagem. A imagem de contentor é semelhante a uma VM, no entanto, ao contrário de uma VM, o contentor apenas inclui recursos de que necessita para executar um aplicativo, como o código, o tempo de execução para executar o código, bibliotecas específicas e definições.

Pode utilizar o Kubernetes para:

- Desenvolva aplicações escaláveis em massa, pode ser atualizadas, que podem ser implementadas em segundos. 
- Simplificar o design da sua aplicação e melhore a sua confiabilidade ao diferentes aplicativos de Helm. [Helm](https://github.com/kubernetes/helm) é uma ferramenta de empacotamento de código-fonte aberto que ajuda a instalar e gerenciar o ciclo de vida de aplicações do Kubernetes.
- Facilmente monitorizar e diagnosticar o estado de funcionamento das suas aplicações.

Será cobrado apenas a utilização de computação necessária para os nós do cluster de suporte. Para obter mais informações, consulte [utilização e faturação no Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback).

## <a name="deploy-kubernetes"></a>Implementar o Kubernetes

Os passos para implementar um cluster do Kubernetes no Azure Stack dependerá do seu serviço de gestão de identidade. Certifique-se a solução de gestão de identidade utilizada pela sua instalação do Azure Stack. Contacte o administrador do Azure Stack para verificar o seu serviço de gestão de identidade.

- **Azure Active Directory (Azure AD)**  
Para obter instruções sobre como instalar o cluster ao utilizar o Azure AD, consulte [implementar o Kubernetes no Azure Stack com o Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md).

- **Do Active Directory federado a serviços (AD FS)**  
Para obter instruções sobre como instalar o cluster ao utilizar o AD FS, consulte [implementar o Kubernetes no Azure Stack com o Active Directory Federated Services (AD FS)](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Ligar ao seu cluster

Agora, está pronto para ligar ao seu cluster. O modelo global pode ser encontrado no seu grupo de recursos do cluster e, com o nome `k8s-master-<sequence-of-numbers>`. Utilize um cliente SSH para ligar a mestre. No controlador, pode usar **kubectl**, o cliente de linha de comandos do Kubernetes para gerir o cluster. Para obter instruções, consulte [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Também pode encontrar os **Helm** Gestor de pacotes úteis para a instalação e implementação de aplicações no seu cluster. Para obter instruções sobre como instalar e utilizar o Helm com o seu cluster, consulte [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Passos Seguintes

[Ativar o Dashboard do Kubernetes](azure-stack-solution-template-kubernetes-dashboard.md)

[Adicionar o Kubernetes no Marketplace (para o operador do Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Implementar o Kubernetes no Azure Stack com o Azure Active Directory (Azure AD)](azure-stack-solution-template-kubernetes-azuread.md)

[Implementar o Kubernetes no Azure Stack com o Active Directory Federated Services (AD FS)](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes no Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
