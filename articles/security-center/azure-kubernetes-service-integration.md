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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: f1444ea31fe693f1c912a3ecb785bbb1e3aa6333
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977281"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integração dos Serviços Azure Kubernetes com o Security Center

O Azure Kubernetes Service (AKS) é o serviço gerido pela Microsoft para desenvolver, implantar e gerir aplicações contentorizadas. 

Ative o Azure **Defender para kubernetes** para obter uma visibilidade mais profunda para os seus nós AKS, tráfego na nuvem e controlos de segurança.

Juntos, o Security Center e o AKS formam a melhor oferta de segurança de Kubernetes nativa da nuvem.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Quais são os componentes da proteção kubernetes do Centro de Segurança?

As proteções do Centro de Segurança para Kubernetes são fornecidas por uma combinação de dois elementos:

- **A proteção contra ameaças do Azure Security Center para máquinas virtuais** - Utilizando o mesmo agente Log Analytics que o Security Center utiliza em outros VMs, o Security Center pode mostrar-lhe problemas de segurança que ocorrem nos seus nós AKS. O agente também monitoriza para análises específicas de contentores.

- **O plano opcional do Azure Security Center para o plano Kubernetes** - O plano Kubernetes recebe registos e informações do subsistema Kubernetes através do serviço AKS. Estes registos já estão disponíveis no Azure através do serviço AKS. Quando ativa o Azure Defender para Kubernetes, concede ao Centro de Segurança acesso aos registos. Assim, o Security Center traz benefícios de segurança para os seus clusters AKS usando dados já recolhidos pelo nó mestre AKS. Alguns dos dados digitalizados pelo Azure Security Center do seu ambiente Kubernetes podem conter informações sensíveis.


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as funcionalidades de segurança do Security Center, consulte:

* [Centro de Segurança Azure e segurança de contentores](container-security.md)

* [Integração com o Azure Container Registry](azure-container-registry-integration.md)

* [Gestão de dados na Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) - Descreve as políticas de dados dos serviços da Microsoft (incluindo Azure, Intune e Microsoft 365), detalhes da gestão de dados da Microsoft e as políticas de retenção que afetam os seus dados
