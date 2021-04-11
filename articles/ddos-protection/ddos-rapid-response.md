---
title: Azure DDos resposta rápida
description: Saiba como envolver especialistas em DDoS durante um ataque ativo para apoio especializado.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: yitoh
ms.openlocfilehash: f551f40a9bd70c4a3b54c1d2756cfb94caf79590
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103067"
---
# <a name="azure-ddos-rapid-response"></a>Azure DDos resposta rápida

Durante um acesso ativo, os clientes Azure DDoS Protection Standard têm acesso à equipa DDoS Rapid Response (DRR), que pode ajudar na investigação de ataque durante uma análise de ataque e pós-ataque.

## <a name="prerequisites"></a>Pré-requisitos

- Antes de poder completar os passos neste tutorial, tem primeiro de criar um [plano de proteção Azure DDoS Standard](manage-ddos-protection.md).

## <a name="when-to-engage-drr"></a>Quando envolver a DRR

Só deve ativar a DRR se: 

- Durante um ataque DDoS se descobrir que o desempenho do recurso protegido está severamente degradado, ou o recurso não está disponível. 
- Considerar que o recurso está sob um ataque DDoS, mas o serviço do DDoS Protection não está a mitigar o ataque de forma eficaz.
- Estiver a planear um evento viral que aumentará significativamente o tráfego.
- Para ataques que têm um impacto crítico no negócio.

## <a name="engage-drr-during-an-active-attack"></a>Engate DRR durante um ataque ativo

1. A partir do portal Azure enquanto cria um novo pedido de suporte, escolha **o Tipo de Emissão** como Técnico.
2. Escolha **o Serviço** como **Proteção DDOS**.
3. Escolha um recurso no menu suspenso de recursos. _Tem de selecionar um Plano DDoS que esteja ligado à rede virtual protegida pela DDoS Protection Standard para ativar a DRR._

    ![Escolha o recurso](./media/ddos-rapid-response/choose-resource.png)

4. Na página seguinte **do Problema** selecione a **gravidade** como A -Critical Impact and **Problem Type** como 'Under attack'.

    ![PSeverity e Tipo de Problema](./media/ddos-rapid-response/severity-and-problem-type.png)

5. Complete detalhes adicionais e envie o pedido de apoio.

A DRR segue o modelo de suporte Azure Rapid Response. Consulte [o âmbito de suporte e a capacidade de resposta](https://azure.microsoft.com/en-us/support/plans/response/) para obter mais informações sobre a Resposta Rápida.

Para saber mais, leia a [documentação Padrão de Proteção DDoS](./ddos-protection-overview.md).

## <a name="next-steps"></a>Passos seguintes

- Saiba como [testar através de simulações.](test-through-simulations.md)
- Saiba [como visualizar e configurar a telemetria de proteção DDoS](telemetry.md).
- Saiba como [visualizar e configurar a registo de diagnóstico do DDoS](diagnostic-logging.md).
