---
title: Melhorar a excelência operacional com o Conselheiro
description: Utilize o Azure Advisor para otimizar e amadurecer a sua excelência operacional para as suas subscrições Azure.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: 25c470a968f2d31ae1190f765046d593190d697b
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92077393"
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

O Azure Advisor deteta regras de alerta que têm consultas inválidas especificadas na sua secção de condições. Pode criar regras de alerta de registo no Azure Monitor e usá-las para executar consultas de análise em intervalos especificados. Os resultados da consulta determinam se um alerta tem de ser acionado. As consultas analíticas podem tornar-se inválidas ao longo do tempo devido a alterações nos recursos, tabelas ou comandos referenciados. O Advisor recomenda que corrija a consulta na regra de alerta para evitar que seja automaticamente desativada e garantir a monitorização da cobertura dos seus recursos em Azure. [Saiba mais sobre regras de alerta de resolução de problemas.](../azure-monitor/platform/alerts-troubleshoot-log.md)

## <a name="use-azure-policy-recommendations"></a>Use recomendações da Política Azure

A Azure Policy é um serviço em Azure que pode usar para criar, atribuir e gerir políticas. Estas políticas impõem regras e efeitos nos seus recursos. As seguintes recomendações da Política Azure podem ajudá-lo a alcançar a excelência operacional: 

**Gerir etiquetas.** Esta política adiciona ou substitui a etiqueta e o valor especificados quando cria ou atualiza um recurso. Pode remediar os recursos existentes desencadeando uma tarefa de reparação. Esta política não modifica etiquetas em grupos de recursos.

**Impor os requisitos de geo-conformidade.** Esta política permite-lhe restringir as localizações que a sua organização pode especificar ao implementar recursos. 

**Especifique as máquinas virtuais permitidas SKUs para implementações.** Esta política permite-lhe especificar um conjunto de SKUs de máquinas virtuais que a organização pode implementar.

**Impor *VMs de auditoria que não utilizem discos geridos*.**

**Ativar *herdar uma etiqueta de grupos de recursos*.** Esta política adiciona ou substitui a etiqueta e o valor especificados do grupo de recursos principal quando cria ou atualiza um recurso. Pode remediar os recursos existentes desencadeando uma tarefa de reparação.

## <a name="no-validation-environment-enabled"></a>Nenhum ambiente de validação ativado
O Azure Advisor determina que não tem um ambiente de validação ativado na subscrição atual. Ao criar as suas piscinas hospedeiras, selecionou \" o ambiente Nº \" para \" validação no \" separador propriedades. Ter pelo menos um pool de anfitriões com um ambiente de validação habilitado garante a continuidade do negócio através de implementações de serviços de desktop virtual do Windows com deteção precoce de potenciais problemas. [Saiba mais](../virtual-desktop/create-validation-host-pool.md)

## <a name="ensure-production-non-validation-environment-to-benefit-from-stable-functionality"></a>Garantir que o ambiente de produção (sem validação) beneficia de uma funcionalidade estável
O Azure Advisor deteta que muitas das suas piscinas de anfitrião têm ambiente de validação ativado. Para que os ambientes de validação sirvam melhor o seu propósito, você deve ter pelo menos um, mas nunca mais de metade das suas piscinas de anfitrião em ambiente de validação. Ao ter um equilíbrio saudável entre as piscinas de anfitrião com ambiente de validação ativado e aqueles com ele desativados, será melhor utilizar os benefícios das implementações multistage que o Windows Virtual Desktop oferece com determinadas atualizações. Para corrigir este problema, abra as propriedades da piscina do anfitrião e selecione \" Não ao lado da \" \" definição de Ambiente de \" Validação.

## <a name="enable-traffic-analytics-to-view-insights-into-traffic-patterns-across-azure-resources"></a>Ativar a Análise de Tráfego para obter informações sobre os padrões de tráfego nos recursos do Azure
A Análise de Tráfego é uma solução com base na cloud, que proporciona visibilidade sobre a atividade dos utilizadores e das aplicações no Azure. A Análise de Tráfego analisa os registos de fluxo do grupo de segurança de rede (NSG) do Observador de Rede para proporcionar informações sobre o fluxo do tráfego. A Análise de Tráfego permite ver as principais fontes de tráfego nas implementações Azure e não Azure, investigar portas abertas, protocolos e fluxos mal intencionados no seu ambiente e otimizar a implementação de rede para melhor desempenho. Pode processar os registos de fluxo em intervalos de processamento de 10 minutos e de 60 minutos, o que lhe permite analisar mais rapidamente o tráfego. É uma boa prática para permitir a Traffic Analytics para os seus recursos Azure. 


## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações do Advisor, consulte:
* [Introdução ao Conselheiro](advisor-overview.md)
* [Introdução](advisor-get-started.md)
* [Recomendações de custos do Assistente](advisor-cost-recommendations.md)
* [Recomendações de desempenho do conselheiro](advisor-performance-recommendations.md)
* [Recomendações de fiabilidade do consultor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Conselheiro REST API](/rest/api/advisor/)