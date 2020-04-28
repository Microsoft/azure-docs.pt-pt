---
title: Melhore a excelência operacional para as suas subscrições do Azure com o Azure Advisor
description: Use O Advisor para otimizar e amadurecer em excelência operacional para as suas subscrições do Azure
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 4133fbe40c0fc1bdaf9c3f734bc19f94437c9acb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187421"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Alcançar a excelência operacional com o Azure Advisor

As recomendações de excelência operacional do Azure Advisor ajudam o cliente com eficiência de processos e fluxos de trabalho, gestão de recursos e implementação de boas práticas. Pode obter estas recomendações do Advisor no separador **De Excelência Operacional** do painel Advisor.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Create Azure Service Health alerta para ser notificado quando problemas do Azure o afetam

Recomendamos que a criação de alertas de saúde de serviço Azure seja notificada quando os problemas de serviço do Azure o afetarem. [A Azure Service Health](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece orientação e suporte personalizados quando é afetado por um problema de serviço azure. O Advisor identifica subscrições que não têm alertas configurados e recomenda a criação de uma.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Desenhe as suas contas de armazenamento para evitar atingir o limite máximo de subscrição

Uma região do Azure pode suportar um máximo de 250 contas de armazenamento por subscrição. Uma vez atingido o limite, não poderá criar mais contas de armazenamento nessa combinação região/subscrição. O Advisor irá verificar as suas subscrições e recomendações de superfície para que desenhe por menos contas de armazenamento para qualquer pessoa que esteja perto de atingir o limite máximo.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Certifique-se de que tem acesso a especialistas em nuvem Azure quando precisar

Ao executar uma carga de trabalho crítica do negócio, é importante ter acesso ao suporte técnico quando necessário. O Advisor identifica potenciais subscrições críticas ao negócio que não têm apoio técnico incluído no seu plano de suporte e recomenda a atualização para uma opção que inclui suporte técnico.

## <a name="repair-invalid-log-alert-rules"></a>Reparação de regras de alerta de registo inválidas

O Azure Advisor irá detetar regras de alerta que tenham consultas inválidas especificadas na sua secção de condições. As regras de alerta de registo são criadas no Azure Monitor e são utilizadas para executar consultas de análise em intervalos especificados. Os resultados da consulta determinam se um alerta tem de ser acionado. As consultas de análise podem tornar-se inválidas ao longo do tempo devido a alterações em recursos, tabelas ou comandos referenciados. O advisor recomendará que corrija a consulta na regra de alerta para evitar que seja automaticamente desativada e garanta a cobertura de monitorização dos seus recursos em Azure. [Saiba mais sobre regras de alerta de resolução de problemas](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Siga as melhores práticas usando a Política Azure

O Azure Policy é um serviço do Azure que utiliza para criar, atribuir e gerir políticas. Estas políticas impõem diferentes regras e efeitos sobre os recursos. Abaixo estão as recomendações da Política Azure para ajudá-lo a alcançar a excelência operacional: 
1. Gerir etiquetas utilizando a Política Azure: Esta política adiciona ou substitui a etiqueta e o valor especificados quando qualquer recurso é criado ou atualizado. Os recursos existentes podem ser corrigidos ao acionar uma tarefa de remediação. Além disso, isto não modifica etiquetas em grupos de recursos.
2. Impor requisitos de geoconformidade utilizando a Política Azure: A política permite-lhe restringir as localizações que a sua organização pode especificar ao implementar recursos. 
3. Especifique a máquina virtual permitida SKUs para implementações: Esta política permite especificar um conjunto de SKUs de máquina virtual que a sua organização pode implementar.
4. Imponha “Auditar VMs que não utilizam discos geridos” com o Azure Policy
5. Utilize 'Herdar uma etiqueta de grupos de recursos' utilizando a Política Azure: A política adiciona ou substitui a etiqueta e o valor especificados do grupo de recursos-mãe quando qualquer recurso é criado ou atualizado. Os recursos existentes podem ser corrigidos ao acionar uma tarefa de remediação.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre recomendações do Advisor, consulte:
* [Introdução ao Consultor](advisor-overview.md)
* [Começar](advisor-get-started.md)
* [Recomendações de Custos Consultivos](advisor-cost-recommendations.md)
* [Recomendações de Desempenho Do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de Alta Disponibilidade Do Conselheiro](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
