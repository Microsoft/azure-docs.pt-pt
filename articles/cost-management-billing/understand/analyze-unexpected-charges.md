---
title: Analisar custos do Azure inesperados
description: Saiba como analisar custos do Azure inesperados da sua subscrição do Azure.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperfq1
ms.openlocfilehash: b2340e5b220936c1333cf842251b669b3e034042
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151217"
---
# <a name="analyze-unexpected-charges"></a>Analisar custos inesperados

É provável que a infraestrutura de recursos de cloud que criou para a sua organização seja complexa. Muitos tipos de recursos do Azure têm diferentes tipos de custos. Os recursos podem ser propriedade de muitas equipas diversas da organização e podem ter tipos de modelos de faturação diferentes. Para compreender melhor os custos, utilize uma ou mais das estratégias nas secções abaixo para começar a sua análise.

## <a name="review-invoice-for-resource-responsible-for-charge"></a>Rever a fatura do recurso responsável pelo custo

A forma como compra os serviços do Azure ajuda a determinar a metodologia e as ferramentas à sua disposição para identificar o recurso associado a um custo. Para determinar a metodologia que lhe é aplicável, comece por [confirmar o seu tipo de oferta do Azure](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Depois, identifique a categoria de cliente na lista de [ofertas do Azure suportadas](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

Os artigos seguintes disponibilizam passos detalhados que explicam como rever a sua fatura consoante o seu tipo de cliente. Cada artigo inclui instruções para transferir um ficheiro CSV que contém detalhes de utilização e custo relativos a um determinado período de faturação.

- [Processo de revisão da fatura Pay As You Go](review-individual-bill.md#charges)
- [Processo de revisão da fatura Contrato Enterprise](review-enterprise-agreement-bill.md)
- [Processo de revisão do Contrato de Cliente Microsoft](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Processo de revisão do Contrato de Parceiros da Microsoft](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

A fatura do Azure agrega os custos do mês por _medidor_. Os medidores são utilizados para monitorizar a utilização de um recurso ao longo do tempo e para calcular a sua fatura. Quando cria um recurso do Azure individual, como uma máquina virtual, são criadas uma ou mais instâncias de medidor para esse recurso.

Filtre o ficheiro CSV de utilização de acordo com _MeterName_, conforme mostrado na fatura que pretende analisar para ver todos os itens de linha que se aplicam ao medidor. O _InstanceID_ do item de linha corresponde ao recurso do Azure real que gerou o custo.

Depois de identificar o recurso em causa, pode utilizar a Análise de custos no Azure Cost Management para fazer mais análises aos custos relacionados com o mesmo. Para saber mais sobre como utilizar a Análise de custos, veja [Começar a analisar os custos](../costs/quick-acm-cost-analysis.md).

## <a name="review-invoiced-charges-in-cost-analysis"></a>Consultar as cobranças faturadas na Análise de custos

Para ver os detalhes da sua fatura no portal do Azure, navegue para a Análise de custos para consultar o âmbito associado à fatura que está a analisar. Selecione a vista **Detalhes da fatura**. Os detalhes da fatura mostram-lhe as cobranças conforme apresentadas na fatura.

[![Exemplo a mostrar os detalhes da fatura](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

Quando vê os detalhes da fatura, pode identificar o serviço que apresenta custos inesperados e determinar que recursos estão diretamente associados ao recurso na Análise de custos. Por exemplo, se quiser analisar as cobranças associadas ao serviço Máquinas Virtuais, navegue para a vista **Custos acumulados**. Em seguida, defina a granularidade para **Diário** e filtre as cobranças **Nome do serviço: Máquinas virtuais** e agrupe as cobranças por **Recurso**.

[![Exemplo a mostrar os custos acumulados para as máquinas virtuais](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>Identificar picos no custo ao longo do tempo

Por vezes, poderá não saber que custos recentes resultaram em alterações aos custos que lhe foram cobrados. Para compreender o que mudou, pode utilizar a Análise de custos para [ver uma discriminação diária ou mensal dos custos ao longo do tempo](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Depois de criar a vista, agrupe os custos por **Serviço** ou por **Recurso** para identificar as alterações. Também pode mudar a vista para um gráfico de **Linhas** para visualizar melhor os dados.

![Exemplo que mostra os custos ao longo do tempo na análise de custos](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>Determinar os preços dos recursos e o modelo de faturação

Um mesmo recurso pode acumular custos em vários produtos e serviços do Azure. Veja a página de [preços do Azure por produto](https://azure.microsoft.com/pricing/#product-pricing) para saber mais sobre os preços de cada serviço do Azure. Por exemplo, uma única máquina virtual (VM) criada no Azure pode ter os seguintes medidores criados para monitorizar a utilização da mesma. Cada medidor pode ter preços diferentes.

- Horas de Computação
- Horas de Endereço IP
- Transferência de Dados de Entrada
- Transferência de Dados de Saída
- Disco Gerido Standard
- Operações de Discos Geridos Standard
- E/S Padrão – Disco
- E/S Padrão – Leitura de Blobs de Blocos
- E/S Padrão – Escrita em Blobs de Blocos
- E/S Padrão – Eliminação de Blobs de Blocos

Uma vez criada a VM, cada medidor começa a emitir registos de utilização. A utilização e o preço do medidor são monitorizados no sistema de medição do Azure. Pode ver os medidores que foram utilizados para calcular a faturação no ficheiro CSV de utilização.

## <a name="find-people-responsible-for-the-resource-and-engage"></a>Encontrar as pessoas responsáveis pelo recurso e contactá-las

Muitas vezes, a equipa responsável por um determinado recurso estará a par das alterações feitas ao mesmo. É importante contactá-la quando tenta determinar o porquê de os custos poderem aparecer. Por exemplo, a equipa responsável pode ter criado recentemente o recurso, atualizado o SKU do mesmo (e, consequentemente, alterado a respetiva taxa) ou aumentado a carga no recurso devido a alterações ao código. Continue a ler as próximas secções para obter mais técnicas para saber quem detém um recurso.

### <a name="analyze-the-audit-logs-for-the-resource"></a>Analisar os registos de auditoria do recurso

Se tiver permissões para ver um recurso, deverá poder aceder aos registos de auditoria do mesmo. Reveja os registos para saber que utilizador é responsável pelas alterações mais recentes a um recurso. Para saber mais, veja [Ver e obter eventos de registo de atividades do Azure](../../azure-monitor/platform/activity-log.md#view-the-activity-log).

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analisar as permissões do utilizador no âmbito principal do recurso

Geralmente, as pessoas com acesso de escrita a uma subscrição ou a um grupo de recursos têm informações sobre os recursos que foram criados. Deverão conseguir explicar o objetivo de um recurso ou indicar-lhe a pessoa que o saiba. Para identificar as pessoas com permissões para um âmbito de Subscrição, veja [Ver atribuições de funções](../../role-based-access-control/check-access.md#view-role-assignments). Pode utilizar um processo semelhante para grupos de recursos.

## <a name="get-help-to-identify-charges"></a>Obter ajuda para identificar alterações

Se tiver seguido as estratégias anteriores e ainda não percebe por que motivo recebeu um custo, ou se precisar de ajuda com outros problemas de faturação, [crie um pedido de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passos seguintes

- Saiba como [Otimizar o seu investimento na cloud com o Azure Cost Management](../costs/cost-mgt-best-practices.md).