---
title: Central de segurança do Azure e serviço kubernetes do Azure | Microsoft Docs
description: Saiba mais sobre a integração da central de segurança do Azure com os serviços Kubernetess do Azure
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
ms.openlocfilehash: 4fc0654aab1c68888e86dc95de658bc69a01e02d
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123978"
---
# <a name="azure-kubernetes-services-integration-with-security-center-preview"></a>Integração dos serviços Kubernetess do Azure com a central de segurança (versão prévia)
O AKS (serviço kubernetes do Azure) é o serviço gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres. 

Use o AKS junto com a camada Standard da central de segurança do Azure (consulte [preços](security-center-pricing.md)) para obter uma visibilidade mais profunda dos nós AKs, do tráfego de nuvem e dos controles de segurança.

A central de segurança traz benefícios de segurança para seus clusters AKS usando dados já coletados pelo nó mestre AKS. 

![Visão geral de alto nível da central de segurança do Azure e do AKS (serviço de kubernetes do Azure)](./media/azure-kubernetes-service-integration/aks-asc-integration-overview.png)

Juntas, essas duas ferramentas formam a melhor oferta de segurança de kubernetes de nuvem nativa. 

## <a name="benefits-of-integration"></a>Benefícios da integração

O uso de dois serviços juntos fornece:

* **Recomendações de segurança** – a central de segurança identifica os recursos do AKs e os categoriza: de clusters para máquinas virtuais individuais. Em seguida, você pode exibir as recomendações de segurança por recurso. Para obter mais informações, consulte [como implementar recomendações de segurança](security-center-recommendations.md). 

    > [!NOTE]
    > Se o nome de uma recomendação da central de segurança terminar com uma marca "(visualização)", ele se referirá à natureza de visualização da recomendação; Não o recurso.

* **Proteção de ambiente** -a central de segurança monitora constantemente a configuração dos seus clusters kubernetes e gera recomendações de segurança que refletem os padrões do setor.

* **Proteção de tempo de execução** -por meio de análise contínua das seguintes fontes de AKs, a central de segurança alerta você sobre ameaças e atividades mal-intencionadas detectadas no nível de cluster do host *e* do AKS (para obter mais informações, consulte [serviço de contêiner do Azure ](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)):
    * Eventos de segurança brutos, como dados de rede e criação de processo
    * O log de auditoria do kubernetes

![Central de segurança do Azure e serviço de kubernetes do Azure (AKS) com mais detalhes](./media/azure-kubernetes-service-integration/aks-asc-integration-detailed.png)

> [!NOTE]
> Alguns dos dados verificados pela central de segurança do Azure do ambiente do kubernetes podem conter informações confidenciais.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre os recursos de segurança do contêiner da central de segurança, confira:

* [Central de segurança do Azure e segurança do contêiner](container-security.md)

* [Integração com o registro de contêiner do Azure](azure-container-registry-integration.md)

* [Proteção da máquina virtual](security-center-virtual-machine-protection.md) – descreve as recomendações da central de segurança

* [Gerenciamento de dados na Microsoft](https://www.microsoft.com/trust-center/privacy/data-management) – descreve as políticas de dados dos serviços da Microsoft (incluindo o Azure, o Intune e o Office 365), os detalhes do gerenciamento de dados da Microsoft e as políticas de retenção que afetam seus dados