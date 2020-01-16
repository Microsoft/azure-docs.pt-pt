---
title: Detecção de ameaças para computação nativa em nuvem na central de segurança do Azure | Microsoft Docs
description: Este artigo apresenta os alertas de computação nativos de nuvem disponíveis na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a85ec565077ac229cd24bb7b9cf753015aa56ebf
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76024856"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Detecção de ameaças para computação nativa em nuvem na central de segurança do Azure

Como uma solução nativa, a central de segurança do Azure tem visibilidade exclusiva dos logs internos para a identificação da metodologia de ataque em vários destinos. Este artigo apresenta os alertas disponíveis para os seguintes serviços do Azure:

* [Serviço de Aplicações do Azure](#app-services)
* [Contêineres do Azure](#azure-containers) 

> [!NOTE]
> Esses serviços não estão disponíveis no momento nas regiões do Azure governamental e do soberanas Cloud.

## Serviço de Azure App<a name="app-services"></a>

A central de segurança usa a escala da nuvem para identificar os ataques que visam os aplicativos em execução no serviço de aplicativo. Os invasores investigam aplicativos Web para encontrar e explorar pontos fracos. Antes de ser roteado para ambientes específicos, as solicitações para aplicativos em execução no Azure passam por vários gateways, onde são inspecionados e registrados. Esses dados são usados para identificar explorações e invasores e para aprender novos padrões que serão usados posteriormente.

Usando a visibilidade que o Azure tem como um provedor de nuvem, a central de segurança analisa os logs internos do serviço de aplicativo para identificar a metodologia de ataque em vários destinos. Por exemplo, a metodologia inclui verificação generalizada e ataques distribuídos. Esse tipo de ataque normalmente vem de um pequeno subconjunto de IPs e mostra padrões de rastreamento para pontos de extremidade semelhantes em vários hosts. Os ataques estão procurando uma página ou um plug-in vulnerável e não podem ser identificados no ponto de vista de um único host.

Se você estiver executando um plano do serviço de aplicativo baseado no Windows, a central de segurança também terá acesso às áreas restritas e VMs subjacentes. Junto com os dados de log mencionados acima, a infra-estrutura pode contar a história, desde um novo ataque que está circulando de forma a comprometimentos nas máquinas dos clientes. Portanto, mesmo que a central de segurança seja implantada depois que um aplicativo Web for explorado, ele poderá detectar ataques contínuos.

Para obter uma lista dos alertas de serviço Azure App, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-azureappserv).

## Contêineres do Azure<a name="azure-containers"></a>

A central de segurança fornece detecção de ameaças em tempo real para seus ambientes em contêineres e gera alertas para atividades suspeitas. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores.

Detectamos ameaças em diferentes níveis: 

* **Nível de host** – agente da central de segurança (disponível na camada Standard, consulte [preços](security-center-pricing.md) para obter detalhes) monitora as atividades suspeitas no Linux. O agente dispara alertas para atividades suspeitas provenientes do nó ou de um contêiner em execução nele. Exemplos dessas atividades incluem a detecção e a conexão de Shell da Web com endereços IP suspeitos conhecidos.

    Para obter uma visão mais profunda da segurança do seu ambiente em contêiner, o agente monitora a análise específica do contêiner. Ele disparará alertas para eventos como criação de contêiner privilegiado, acesso suspeito a servidores de API e servidores Secure Shell (SSH) em execução dentro de um contêiner do Docker.

    >[!NOTE]
    > Se você optar por não instalar os agentes em seus hosts, receberá apenas um subconjunto dos benefícios e alertas de detecção de ameaças. Você ainda receberá alertas relacionados à análise de rede e comunicações com servidores mal-intencionados.

    Para obter uma lista dos alertas de nível de host, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-containerhost).


* Para o **nível de cluster AKs**, há um monitoramento de detecção de ameaças com base na análise de logs de auditoria do kubernetes. Para habilitar esse monitoramento **sem agente** , adicione a opção kubernetes à sua assinatura na página de **configurações de & de preços** (consulte [preços](security-center-pricing.md)). Para gerar alertas nesse nível, a central de segurança monitora seus serviços gerenciados AKS usando os logs recuperados pelo AKS. Exemplos de eventos neste nível incluem painéis kubernetes expostos, criação de funções com altos privilégios e criação de montagens confidenciais.

    >[!NOTE]
    > A central de segurança gera alertas de detecção para ações e implantações do serviço kubernetes do Azure que ocorrem após a opção kubernetes ser habilitada nas configurações de assinatura. 

    Para obter uma lista dos alertas de nível de cluster AKS, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-akscluster).

Além disso, nossa equipe global de pesquisadores de segurança monitora constantemente o panorama de ameaças. Eles adicionam alertas e vulnerabilidades específicos do contêiner à medida que são descobertos.

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre os planos do serviço de aplicativo, consulte [planos do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/).