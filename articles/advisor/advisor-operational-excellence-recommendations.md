---
title: Melhorar a excelência operacional com o Conselheiro
description: Utilize o Azure Advisor para otimizar e amadurecer a sua excelência operacional para as suas subscrições Azure.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 1cac5a3f93df8422a3896b1251857bf552731fb4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85125400"
---
# <a name="achieve-operational-excellence-by-using-azure-advisor"></a>Alcançar a excelência operacional utilizando o Azure Advisor

Recomendações de excelência operacional no Azure Advisor podem ajudá-lo com: 
- Eficiência do processo e do fluxo de trabalho.
- Gestão de recursos.
- Implementação de boas práticas. 

Pode obter estas recomendações no separador **Excelência Operacional** do painel de instrumentos Do Advisor.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Crie alertas de Saúde do Serviço Azure para ser notificado quando os problemas do Azure o afetarem

Recomendamos que crie alertas de Saúde do Serviço Azure para que seja notificado quando os problemas de serviço do Azure o afetarem. [O Azure Service Health](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece orientação e suporte personalizados quando é afetado por um problema de serviço Azure. O Advisor identifica subscrições que não têm alertas configurados e recomenda a sua configuração.


## <a name="design-your-storage-accounts-to-prevent-reaching-the-maximum-subscription-limit"></a>Desenhe as suas contas de armazenamento para evitar atingir o limite máximo de subscrição

Uma região de Azure pode suportar um máximo de 250 contas de armazenamento por subscrição. Depois de atingir esse limite, não poderá criar contas de armazenamento nessa região/combinação de subscrição. O advisor verifica as suas subscrições e fornece recomendações para que você concene para menos contas de armazenamento para qualquer região/subscrição que esteja perto de atingir o limite.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Certifique-se de que tem acesso a especialistas em nuvem Azure quando precisar

Ao executar uma carga de trabalho crítica de negócios, é importante ter acesso a suporte técnico quando você precisa. O Advisor identifica potenciais subscrições críticas ao negócio que não têm suporte técnico incluído no seu plano de suporte. Recomenda o upgrade para uma opção que inclua suporte técnico.

## <a name="delete-and-re-create-your-pool-to-remove-a-deprecated-internal-component"></a>Elimine e re-crie a sua piscina para remover um componente interno precotado

Se a sua piscina estiver a utilizar um componente interno precotado, elimine e reco crie a piscina para uma maior estabilidade e desempenho.

## <a name="repair-invalid-log-alert-rules"></a>Reparar regras de alerta de registo inválido

O Azure Advisor deteta regras de alerta que têm consultas inválidas especificadas na sua secção de condições. Pode criar regras de alerta de registo no Azure Monitor e usá-las para executar consultas de análise em intervalos especificados. Os resultados da consulta determinam se um alerta tem de ser acionado. As consultas analíticas podem tornar-se inválidas ao longo do tempo devido a alterações nos recursos, tabelas ou comandos referenciados. O Advisor recomenda que corrija a consulta na regra de alerta para evitar que seja automaticamente desativada e garantir a monitorização da cobertura dos seus recursos em Azure. [Saiba mais sobre regras de alerta de resolução de problemas.](https://aka.ms/aa_logalerts_queryrepair)

## <a name="use-azure-policy-recommendations"></a>Use recomendações da Política Azure

A Azure Policy é um serviço em Azure que pode usar para criar, atribuir e gerir políticas. Estas políticas impõem regras e efeitos nos seus recursos. As seguintes recomendações da Política Azure podem ajudá-lo a alcançar a excelência operacional: 

**Gerir etiquetas.** Esta política adiciona ou substitui a etiqueta e o valor especificados quando cria ou atualiza um recurso. Pode remediar os recursos existentes desencadeando uma tarefa de reparação. Esta política não modifica etiquetas em grupos de recursos.

**Impor os requisitos de geo-conformidade.** Esta política permite-lhe restringir as localizações que a sua organização pode especificar ao implementar recursos. 

**Especifique as máquinas virtuais permitidas SKUs para implementações.** Esta política permite-lhe especificar um conjunto de SKUs de máquinas virtuais que a organização pode implementar.

**Impor *VMs de auditoria que não utilizem discos geridos*.**

**Ativar *herdar uma etiqueta de grupos de recursos*.** Esta política adiciona ou substitui a etiqueta e o valor especificados do grupo de recursos principal quando cria ou atualiza um recurso. Pode remediar os recursos existentes desencadeando uma tarefa de reparação.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as recomendações do Advisor, consulte:
* [Introdução ao Conselheiro](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de custos do Assistente](advisor-cost-recommendations.md)
* [Recomendações de desempenho do conselheiro](advisor-performance-recommendations.md)
* [Recomendações de fiabilidade do consultor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Conselheiro REST API](https://docs.microsoft.com/rest/api/advisor/)
