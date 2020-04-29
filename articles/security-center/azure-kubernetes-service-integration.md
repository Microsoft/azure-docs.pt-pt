---
title: Centro de Segurança Azure e Serviço Azure Kubernetes
description: Conheça a integração do Azure Security Center com os Serviços Azure Kubernetes
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: d5c74ac760b5746d37468d692c2a46d5aedbee72
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80436185"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integração de serviços azure Kubernetes com Centro de Segurança

O Azure Kubernetes Service (AKS) é o serviço gerido pela Microsoft para desenvolver, implementar e gerir aplicações contentorizadas. 

Utilize AKS juntamente com o nível padrão do Azure Security Center (ver [preços)](security-center-pricing.md)para obter uma visibilidade mais profunda para os seus nós AKS, tráfego na nuvem e controlos de segurança.

O Security Center traz benefícios de segurança aos seus clusters AKS usando dados já recolhidos pelo nó principal aks. 

![Visão geral de alto nível do Azure Security Center e azure Kubernetes Service (AKS)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Juntos, estas duas ferramentas formam a melhor oferta de segurança kubernetes nativa da nuvem. 

## <a name="benefits-of-integration"></a>Benefícios da integração

A utilização dos dois serviços em conjunto fornece:

* **Recomendações de segurança** - O Centro de Segurança identifica os seus recursos AKS e categoriza-os: desde aglomerados a máquinas virtuais individuais. Em seguida, pode ver recomendações de segurança por recurso. Para mais informações, consulte as recomendações dos contentores na [lista de recomendações de referência.](recommendations-reference.md#recs-containers) 

* **Endurecimento ambiental** - O Security Center monitoriza constantemente a configuração dos seus clusters Kubernetes e configurações do Docker. Em seguida, gera recomendações de segurança que refletem os padrões do setor.

* **Proteção** a tempo de execução - Através da análise contínua das seguintes fontes AKS, o Security Center alerta-o para ameaças e atividades maliciosas detetadas ao nível do hospedeiro *e* do cluster AKS:
    * Eventos de segurança bruta, tais como dados de rede e criação de processos
    * O registo de auditoria kubernetes

    Para mais informações, consulte [a proteção contra ameaças para os contentores Azure](threat-protection.md#azure-containers)

    Para a lista de possíveis alertas, consulte estas secções na tabela de referência de alertas: alertas de [nível de cluster AKS](alerts-reference.md#alerts-akscluster) e alertas de nível de hospedeiro de [contentores](alerts-reference.md#alerts-containerhost).  

![Azure Security Center e Azure Kubernetes Service (AKS) em mais detalhes](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Alguns dos dados digitalizados pelo Azure Security Center do seu ambiente Kubernetes podem conter informações sensíveis.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades de segurança de contentores do Security Center, consulte:

* [Centro de Segurança Azure e segurança de contentores](container-security.md)

* [Integração com o Azure Container Registry](azure-container-registry-integration.md)

* [Gestão](https://www.microsoft.com/trust-center/privacy/data-management) de dados na Microsoft - Descreve as políticas de dados dos serviços da Microsoft (incluindo Azure, Intune e Office 365), detalhes da gestão de dados da Microsoft e as políticas de retenção que afetam os seus dados
