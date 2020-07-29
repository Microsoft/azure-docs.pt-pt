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
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 94d1bccc9a7f45d24d8c5b92aecba54d9f7f630a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800187"
---
# <a name="azure-kubernetes-services-integration-with-security-center"></a>Integração dos Serviços Azure Kubernetes com o Security Center

O Azure Kubernetes Service (AKS) é o serviço gerido pela Microsoft para desenvolver, implantar e gerir aplicações contentorizadas. 

Se estiver no nível padrão do Azure Security Center, pode adicionar o pacote AKS (ver [preços)](security-center-pricing.md)para obter uma visibilidade mais profunda aos seus nós AKS, tráfego em nuvem e controlos de segurança.

Juntos, o Security Center e o AKS formam a melhor oferta de segurança de Kubernetes nativa da nuvem.

## <a name="what-are-the-components-of-security-centers-kubernetes-protection"></a>Quais são os componentes da proteção kubernetes do Centro de Segurança?

As proteções do Centro de Segurança para Kubernetes são fornecidas por uma combinação de dois elementos:

- **A proteção contra ameaças do Azure Security Center para máquinas virtuais** - Utilizando o mesmo agente Log Analytics que o Security Center utiliza em outros VMs, o Security Center pode mostrar-lhe problemas de segurança que ocorrem nos seus nós AKS. O agente também monitoriza para análises específicas de contentores.

- **O pacote opcional de Kubernetes do Azure Security Center** - O pacote Kubernetes recebe registos e informações do subsistema Kubernetes através do serviço AKS. Estes registos já estão disponíveis no Azure através do serviço AKS. Quando ativa o pacote Kubernetes do Security Center, concede ao Centro de Segurança acesso aos registos. Assim, o Security Center traz benefícios de segurança para os seus clusters AKS usando dados já recolhidos pelo nó mestre AKS. Alguns dos dados digitalizados pelo Azure Security Center do seu ambiente Kubernetes podem conter informações sensíveis.

    ![Centro de Segurança Azure e Serviço Azure Kubernetes (AKS) visão geral de alto nível](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

## <a name="what-protections-are-provided"></a>Que proteções são fornecidas?

A utilização dos dois serviços em conjunto fornece:

* **Recomendações de segurança** - O Centro de Segurança identifica os seus recursos AKS e categoriza-os: desde clusters a máquinas virtuais individuais. Em seguida, pode ver recomendações de segurança por recurso. Para mais informações, consulte as recomendações dos recipientes na [lista de recomendações de referência.](recommendations-reference.md#recs-containers) 

* **Endurecimento do ambiente** - O Security Center monitoriza constantemente a configuração dos seus clusters Kubernetes e das configurações do Docker. Em seguida, gera recomendações de segurança que refletem os padrões da indústria.

* **Proteção do tempo de execução** - Através de uma análise contínua das seguintes fontes AKS, o Security Center alerta-o para ameaças e atividades maliciosas detetadas ao nível do cluster hospedeiro *e* AKS. [Saiba mais sobre a proteção contra ameaças para contentores](threat-protection.md#azure-containers).


     

![Azure Security Center e Azure Kubernetes Service (AKS) com mais detalhes](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)



## <a name="aks-with-security-center-faq"></a>AKS com Centro de Segurança FAQ

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Ainda posso ter proteções AKS sem o agente Log Analytics?

Como mencionado acima, o pacote opcional kubernetes fornece proteções ao nível do cluster, o agente Log Analytics do Nível padrão do Azure Security Center protege os seus nós. 

Recomendamos a colocação de ambos, para a proteção mais completa possível.

Se optar por não instalar o agente nos seus anfitriões, receberá apenas um subconjunto dos benefícios de proteção contra ameaças e alertas de segurança. Ainda receberá alertas relacionados com análises de rede e comunicações com servidores maliciosos.



## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as funcionalidades de segurança do Security Center, consulte:

* [Centro de Segurança Azure e segurança de contentores](container-security.md)

* [Integração com o Azure Container Registry](azure-container-registry-integration.md)

* [Gestão de dados na Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) - Descreve as políticas de dados dos serviços da Microsoft (incluindo Azure, Intune e Microsoft 365), detalhes da gestão de dados da Microsoft e as políticas de retenção que afetam os seus dados
