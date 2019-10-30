---
title: Melhore o excellency operacional para suas assinaturas do Azure com o Azure Advisor | Microsoft Docs
description: Use o Advisor para otimizar e ficar maduro em excelência operacional para suas assinaturas do Azure.
services: advisor
documentationcenter: NA
author: sagupt
ms.service: advisor
ms.topic: article
ms.date: 10/24/2019
ms.author: sagupt
ms.openlocfilehash: 7cf3d3b34d0921cd111f8111bc2008ef0eced962
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033293"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Obtenha excelência operacional com o Azure Advisor

As recomendações de excelência operacional do Azure Advisor ajudam o cliente com eficiência de processo e fluxo de trabalho, gerenciamento de recursos e práticas recomendadas de implantação. Você pode obter essas recomendações do assistente na guia **excelência operacional** do painel do Advisor.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Crie alertas de integridade do serviço do Azure para ser notificado quando os problemas do Azure afetarem você

É recomendável configurar alertas de integridade do serviço do Azure para ser notificado quando os problemas de serviço do Azure afetarem você. A [integridade do serviço do Azure](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece orientação e suporte personalizados quando você é afetado por um problema de serviço do Azure. O Advisor identifica as assinaturas que não têm alertas configurados e recomenda a criação de um.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Projete suas contas de armazenamento para evitar atingir o limite máximo de assinaturas

Uma região do Azure pode dar suporte a um máximo de 250 contas de armazenamento por assinatura. Depois que o limite for atingido, você não poderá criar mais contas de armazenamento nessa combinação de região/assinatura. O Advisor verificará suas assinaturas e recomendações de superfície para você projetar para menos contas de armazenamento para qualquer um que esteja próximo de atingir o limite máximo.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Verifique se você tem acesso aos especialistas de nuvem do Azure quando precisar

Ao executar uma carga de trabalho comercialmente crítica, é importante ter acesso ao suporte técnico quando necessário. O Advisor identifica as possíveis assinaturas críticas para os negócios que não têm suporte técnico incluído em seu plano de suporte e recomenda a atualização para uma opção que inclui suporte técnico.

## <a name="repair-invalid-log-alert-rules"></a>Reparar regras de alerta de log inválidas

O Azure Advisor detectará regras de alerta com consultas inválidas especificadas na seção de condição. As regras de alerta de registo são criadas no Azure Monitor e são utilizadas para executar consultas de análise em intervalos especificados. Os resultados da consulta determinam se um alerta tem de ser acionado. As consultas de análise podem tornar-se inválidas ao longo do tempo devido a alterações em recursos, tabelas ou comandos referenciados. O Advisor recomendará que você corrija a consulta na regra de alerta para impedir que ela seja desabilitada automaticamente e garanta a cobertura de monitoramento de seus recursos no Azure. [Saiba mais sobre como solucionar problemas de regras de alerta](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Siga as práticas recomendadas usando Azure Policy

O Azure Policy é um serviço do Azure que utiliza para criar, atribuir e gerir políticas. Essas políticas impõem diferentes regras e efeitos sobre seus recursos. Abaixo estão as recomendações de política do Azure para ajudá-lo a obter o excellency operacional: 
1. Gerenciar marcas usando Azure Policy: essa política adiciona ou substitui a marca e o valor especificados quando qualquer recurso é criado ou atualizado. Os recursos existentes podem ser corrigidos disparando uma tarefa de correção. Além disso, isso não modifica marcas em grupos de recursos.
2. Impor os requisitos de conformidade geográfica usando o Azure Policy: a política permite restringir os locais que sua organização pode especificar ao implantar recursos. 
3. Especificar SKUs de máquina virtual permitidas para implantações: essa política permite que você especifique um conjunto de SKUs de máquina virtual que sua organização pode implantar.
4. Impor "auditar VMs que não usam discos gerenciados" usando a política do Azure
5. Use ' herdar uma marca dos grupos de recursos ' usando a política do Azure: a política adiciona ou substitui a marca e o valor especificados do grupo de recursos pai quando qualquer recurso é criado ou atualizado. Os recursos existentes podem ser corrigidos disparando uma tarefa de correção.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações do Advisor, consulte:
* [Introdução ao Advisor](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
