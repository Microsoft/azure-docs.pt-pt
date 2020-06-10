---
title: Melhorar a excelência operacional das suas subscrições Azure com o Azure Advisor
description: Use o Advisor para otimizar e amadurecer em excelência operacional para as suas assinaturas Azure
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 861b12c2267ffa89985ff11357de92da5a4ac870
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658449"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Alcançar a excelência operacional com o Azure Advisor

As recomendações de excelência operacional do Azure Advisor ajudam o cliente com eficiência de processos e fluxos de trabalho, gestão de recursos e boas práticas de implantação. Pode obter estas recomendações do Advisor no separador **Excelência Operacional** do painel de instrumentos Do Advisor.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Crie alertas de Saúde do Serviço Azure para ser notificado quando os problemas do Azure o afetarem

Recomendamos a criação de alertas de Saúde do Serviço Azure para serem notificados quando os problemas de serviço da Azure o afetarem. [O Azure Service Health](https://azure.microsoft.com/features/service-health/) é um serviço gratuito que fornece orientação e suporte personalizados quando é afetado por um problema de serviço Azure. O Advisor identifica subscrições que não têm alertas configurados e recomenda a criação de uma.


## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Desenhe as suas contas de armazenamento para evitar atingir o limite máximo de subscrição

Uma região de Azure pode suportar um máximo de 250 contas de armazenamento por subscrição. Uma vez atingido o limite, não poderá criar mais contas de armazenamento nessa região/combinação de subscrição. O Advisor verificará as suas subscrições e recomendações de superfície para que possa conceber menos contas de armazenamento para qualquer um que esteja perto de atingir o limite máximo.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Certifique-se de que tem acesso a especialistas em nuvem Azure quando precisar

Ao executar uma carga de trabalho crítica de negócios, é importante ter acesso ao suporte técnico quando necessário. O Advisor identifica potenciais subscrições críticas ao negócio que não têm suporte técnico incluído no seu plano de suporte e recomenda o upgrade para uma opção que inclua suporte técnico.

## <a name="delete-and-recreate-your-pool-to-remove-a-deprecated-internal-component"></a>Eliminar e recriar o conjunto para remover um componente interno preterido

O conjunto está a utilizar um componente interno preterido. Elimine e recrie o conjunto para obter uma fiabilidade e um desempenho melhorados.

## <a name="repair-invalid-log-alert-rules"></a>Reparar regras de alerta de registo inválido

O Azure Advisor detetará regras de alerta que tenham consultas inválidas especificadas na sua secção de condições. As regras de alerta de registo são criadas no Azure Monitor e são utilizadas para executar consultas de análise em intervalos especificados. Os resultados da consulta determinam se um alerta tem de ser acionado. As consultas de análise podem tornar-se inválidas ao longo do tempo devido a alterações em recursos, tabelas ou comandos referenciados. O Advisor recomendará que corrija a consulta na regra de alerta para evitar que fique desativada automaticamente e garanta a cobertura de monitorização dos seus recursos em Azure. [Saiba mais sobre regras de alerta de resolução de problemas](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Siga as melhores práticas usando a Política Azure

O Azure Policy é um serviço do Azure que utiliza para criar, atribuir e gerir políticas. Estas políticas impõem diferentes regras e efeitos sobre os recursos. Seguem-se as recomendações da Política Azure para o ajudar a alcançar a excelência operacional: 
1. Gerir Tags utilizando a Política Azure: Esta política adiciona ou substitui a etiqueta e o valor especificados quando qualquer recurso é criado ou atualizado. Os recursos existentes podem ser corrigidos ao acionar uma tarefa de remediação. Além disso, isto não modifica etiquetas em grupos de recursos.
2. Impor requisitos de geo-conformidade utilizando a Política Azure: A política permite-lhe restringir as localizações que a sua organização pode especificar ao utilizar recursos. 
3. Especificar skus de máquina virtual permitida para implementações: Esta política permite especificar um conjunto de SKUs de máquina virtual que a sua organização pode implementar.
4. Imponha “Auditar VMs que não utilizam discos geridos” com o Azure Policy
5. Utilizar "Herdar uma etiqueta de grupos de recursos" utilizando a Política Azure: A política adiciona ou substitui a etiqueta e o valor especificados do grupo de recursos-mãe quando qualquer recurso é criado ou atualizado. Os recursos existentes podem ser corrigidos ao acionar uma tarefa de remediação.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as recomendações do Advisor, consulte:
* [Introdução ao Conselheiro](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de Custos Do Consultor](advisor-cost-recommendations.md)
* [Recomendações de Desempenho do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de Alta Disponibilidade do Conselheiro](advisor-high-availability-recommendations.md)
* [Recomendações de Segurança do Conselheiro](advisor-security-recommendations.md)
