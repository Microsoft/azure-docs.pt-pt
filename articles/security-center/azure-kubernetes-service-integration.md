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
ms.openlocfilehash: 3f58afa41a27427f8deabb945261d96763edb4bc
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126183"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integração dos Serviços Azure Kubernetes com o Security Center

O Azure Kubernetes Service (AKS) é o serviço gerido pela Microsoft para desenvolver, implantar e gerir aplicações contentorizadas. 

Utilize o AKS juntamente com o nível padrão do Azure Security Center (ver [preços)](security-center-pricing.md)para obter uma visibilidade mais profunda aos seus nós AKS, tráfego na nuvem e controlos de segurança.

O Security Center traz benefícios de segurança para os seus clusters AKS usando dados já recolhidos pelo nó mestre AKS. 

![Centro de Segurança Azure e Serviço Azure Kubernetes (AKS) visão geral de alto nível](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Juntas, estas duas ferramentas formam a melhor oferta de segurança de Kubernetes nativa da nuvem. 

## <a name="benefits-of-integration"></a>Benefícios da integração

A utilização dos dois serviços em conjunto fornece:

* **Recomendações de segurança** - O Centro de Segurança identifica os seus recursos AKS e categoriza-os: desde clusters a máquinas virtuais individuais. Em seguida, pode ver recomendações de segurança por recurso. Para mais informações, consulte as recomendações dos recipientes na [lista de recomendações de referência.](recommendations-reference.md#recs-containers) 

* **Endurecimento do ambiente** - O Security Center monitoriza constantemente a configuração dos seus clusters Kubernetes e das configurações do Docker. Em seguida, gera recomendações de segurança que refletem os padrões da indústria.

* **Proteção do tempo de execução** - Através de uma análise contínua das seguintes fontes AKS, o Security Center alerta-o para ameaças e atividades maliciosas detetadas ao nível do cluster hospedeiro *e* AKS:
    * Eventos de segurança bruta, como dados de rede e criação de processos
    * O registo de auditoria de Kubernetes

    Para mais informações, consulte [a proteção contra ameaças para contentores Azure](threat-protection.md#azure-containers)

    Para obter a lista de possíveis alertas, consulte estas secções na tabela de referência dos alertas: [alertas de nível de cluster AKS](alerts-reference.md#alerts-akscluster) e [alertas de nível de hospedeiro do contentor](alerts-reference.md#alerts-containerhost).  

![Azure Security Center e Azure Kubernetes Service (AKS) com mais detalhes](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Alguns dos dados digitalizados pelo Azure Security Center do seu ambiente Kubernetes podem conter informações sensíveis.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades de segurança do Security Center, consulte:

* [Centro de Segurança Azure e segurança de contentores](container-security.md)

* [Integração com o Azure Container Registry](azure-container-registry-integration.md)

* [Gestão de dados na Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) - Descreve as políticas de dados dos serviços da Microsoft (incluindo Azure, Intune e Microsoft 365), detalhes da gestão de dados da Microsoft e as políticas de retenção que afetam os seus dados
