---
title: Visão geral da API de consumo do Azure | Microsoft Docs
description: Saiba como as APIs de consumo do Azure oferecem acesso programático aos dados de custo e de uso para os recursos do Azure.
services: billing
documentationcenter: ''
author: bandersmsft
manager: dougeby
editor: ''
tags: billing
ms.assetid: 68825e85-de64-466d-b11a-8baffde836b5
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: banders
ms.openlocfilehash: 2a2922daef5e75af41252119f9a467fae976bcda
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443259"
---
# <a name="azure-consumption-api-overview"></a>Visão geral da API de consumo do Azure 

As APIs de Consumo do Azure oferecem acesso programático aos dados de custo e utilização para os seus recursos do Azure. Atualmente, essas APIs só dão suporte a registros corporativos e assinaturas do Direct Web (com algumas exceções). As APIs são continuamente atualizadas para dar suporte a outros tipos de assinaturas do Azure.

As APIs de Consumo do Azure oferecem acesso a:
- Clientes Diretos Enterprise e Web 
    - Detalhes de Utilização 
    - Encargos do Marketplace 
    - Recomendações da Reserva 
    - Detalhes da Reserva 
    - Resumos da Reserva 
- Apenas Clientes Enterprise 
    - Folha de preços 
    - Orçamentos 
    - Saldos 

## <a name="usage-details-api"></a>API de detalhes de uso

Use a API de detalhes de uso para obter dados de uso e de custos para todos os recursos de terceiros do Azure. As informações estão na forma de registros de detalhes de uso que são emitidos uma vez por metro por recurso por dia. As informações podem ser usadas para somar os custos em todos os recursos ou investigar os custos/uso em recursos específicos. 

A API inclui:

-   **Dados de consumo de nível de medição** – consulte os dados, incluindo o custo de uso, o medidor que emite o encargo e a qual recurso do Azure a cobrança pertence. Todos os registros de detalhes de uso são mapeados para um Bucket diário.
-   **Controle de acesso baseado em função do Azure** – configurar políticas de acesso no [portal do Azure](https://portal.azure.com), os cmdlets [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem obter acesso aos dados de uso da assinatura. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Filtragem** -corte o conjunto de resultados da API para um conjunto menor de registros de detalhes de uso usando os seguintes filtros:
    - Início de uso/fim de uso
    - Grupo de Recursos
    - Nome do Recurso
-   **Agregação de dados** – use OData para aplicar expressões para agregar detalhes de uso por marcas ou propriedades de filtro
-   **Uso para diferentes tipos de oferta** – informações de detalhes de uso estão disponíveis atualmente para clientes do Enterprise e Web Direct.

Para obter mais informações, consulte a especificação técnica para a [API de detalhes de uso](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>API de encargos do Marketplace

Use a API de encargos do Marketplace para obter dados de uso e de custos em todos os recursos do Marketplace (ofertas de terceiros do Azure). Esses dados podem ser usados para somar os custos em todos os recursos do Marketplace ou investigar os custos/uso em recursos específicos. 

A API inclui:

-   **Dados de consumo de nível de medição** -consulte dados incluindo o custo de uso do Marketplace, o medidor que emite o encargo e a qual recurso o encargo pertence. Todos os registros de detalhes de uso são mapeados para um Bucket diário.
-   **Controle de acesso baseado em função do Azure** – configurar políticas de acesso no [portal do Azure](https://portal.azure.com), os cmdlets [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem obter acesso aos dados de uso da assinatura. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Filtragem** -reduza o conjunto de resultados da API para um conjunto menor de registros do Marketplace usando os seguintes filtros:
    - Fim do uso/início do uso
    - Grupo de Recursos
    - Nome do Recurso
-   **Uso para diferentes tipos de oferta** – as informações do Marketplace estão disponíveis atualmente para clientes do Enterprise e Web Direct.

Para obter mais informações, consulte a especificação técnica para a API de encargos do [Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>API de saldos

Os clientes corporativos podem usar a API de saldos para obter um resumo mensal das informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos excedentes. Você pode obter essas informações para o período de cobrança atual ou para qualquer período no passado. As empresas podem usar esses dados para executar uma comparação com os encargos de resumo calculados manualmente. Essa API não fornece informações específicas do recurso e uma exibição agregada dos custos. 

A API inclui:

-   **Controle de acesso baseado em função do Azure** – configurar políticas de acesso no [portal do Azure](https://portal.azure.com), os cmdlets [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem obter acesso aos dados de uso da assinatura. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Somente clientes empresariais** Essa API só está disponível para clientes do EA. 
    - Os clientes devem ter permissões de administrador corporativo para chamar essa API 

Para obter mais informações, consulte a especificação técnica para a [API de saldos](https://docs.microsoft.com/rest/api/consumption/balances).

## <a name="budgets-api"></a>API de orçamentos

Os clientes corporativos podem usar essa API para criar orçamentos de uso ou de custo para recursos, grupos de recursos ou medidores de cobrança. Depois que essas informações forem determinadas, os alertas poderão ser configurados para notificar quando os limites de orçamento definidos pelo usuário forem excedidos. 

A API inclui:

-   **Controle de acesso baseado em função do Azure** – configurar políticas de acesso no [portal do Azure](https://portal.azure.com), os cmdlets [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem obter acesso aos dados de uso da assinatura. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Somente clientes corporativos** -esta API só está disponível para clientes do ea.
-   **Notificações configuráveis** – especifique os usuários a serem notificados quando o orçamento for ultrapassado.
-   **Orçamentos com base no uso ou no custo** – crie seu orçamento com base no consumo ou no custo conforme a necessidade do seu cenário.
-   **Filtrando** -filtre seu orçamento para um subconjunto menor de recursos usando os seguintes filtros configuráveis
    - Grupo de Recursos
    - Nome do Recurso
    - Medir
-   **Períodos de tempo de orçamento configuráveis** -especifique a frequência com que o orçamento deve ser redefinido e por quanto tempo o orçamento é válido.

Para obter mais informações, consulte a especificação técnica para a [API](https://docs.microsoft.com/rest/api/consumption/budgets)de orçamentos.

## <a name="reservation-recommendations-api"></a>API de recomendações de reserva

Use esta API para obter recomendações para a compra de instâncias de VM reservadas. As recomendações foram projetadas para permitir que os clientes analisem a economia de custos e os valores de compra esperados. 

A API inclui:

-   **Controle de acesso baseado em função do Azure** – configurar políticas de acesso no [portal do Azure](https://portal.azure.com), os cmdlets [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem obter acesso aos dados de uso da assinatura. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Filtragem** – Personalize seus resultados de recomendação usando os seguintes filtros:
    - Scope
    - Período de Lookback
-   **Informações de reserva para tipos de oferta diferentes** – informações de reserva estão disponíveis atualmente para clientes do Enterprise e Web Direct.

Para obter mais informações, consulte a especificação técnica para a [API de recomendações de reserva](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>API de detalhes de reserva

Use a API de detalhes de reserva para ver informações sobre as reservas de VM adquiridas anteriormente, como a quantidade de consumo reservado em relação ao que realmente está sendo usado. Você pode ver os dados de acordo com os detalhes de nível de VM. 

A API inclui:

-   **Controle de acesso baseado em função do Azure** – configurar políticas de acesso no [portal do Azure](https://portal.azure.com), os cmdlets [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem obter acesso aos dados de uso da assinatura. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Filtragem** -apare o conjunto de resultados da API para um conjunto menor de reservas usando o seguinte filtro:
    - Intervalo de datas
-   **Informações de reserva para tipos de oferta diferentes** – informações de reserva estão disponíveis atualmente para clientes do Enterprise e Web Direct.

Para obter mais informações, consulte a especificação técnica para a [API de detalhes de reserva](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>API de resumos de reserva

Use essa API para ver informações de agregação sobre as reservas de VM adquiridas anteriormente, como a quantidade de consumo reservado em comparação à quantidade que realmente está sendo usada na agregação. 

A API inclui:

-   **Controle de acesso baseado em função do Azure** – configurar políticas de acesso no [portal do Azure](https://portal.azure.com), os cmdlets [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem obter acesso aos dados de uso da assinatura. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Filtragem** – Personalize seus resultados ao usar o detalhamento diário com o seguinte filtro:
    - Data de Utilização
-   **Informações de reserva para tipos de oferta diferentes** – informações de reserva estão disponíveis atualmente para clientes do Enterprise e Web Direct.
-   **Agregações diárias ou mensais** – os chamadores podem especificar se desejam seus dados de Resumo de reserva no detalhamento diário ou mensal.

Para obter mais informações, consulte a especificação técnica para a API de resumos de [reserva](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>API de folha de preços
O cliente corporativo pode usar essa API para recuperar seus preços personalizados para todos os medidores. As empresas podem usar isso em combinação com detalhes de uso e informações de uso do Marketplace para executar cálculos de custo usando dados de uso e do Marketplace. 

A API inclui:

-   **Controle de acesso baseado em função do Azure** – configurar políticas de acesso no [portal do Azure](https://portal.azure.com), os cmdlets [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar quais usuários ou aplicativos podem obter acesso aos dados de uso da assinatura. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Somente clientes corporativos** -esta API só está disponível para clientes do ea. Os clientes do Direct Web devem usar a API RateCard para obter preços. 

Para obter mais informações, consulte a especificação técnica da [API da folha de preços](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Cenários

Aqui estão alguns dos cenários que se tornaram possíveis por meio das APIs de consumo:

-   **Reconciliação de nota fiscal** -a Microsoft encarregadou o valor certo?  O que é minha fatura e posso calculá-la sozinho?
-   Encargos cruzados – agora que sei quanto estou sendo cobrado, quem em minha organização precisa pagar?
-   **Otimização de custos** -sei quanto eu fui cobrado... Como posso aproveitar ao máximo o dinheiro que estou gastando no Azure?
-   **Controle de custos** – quero ver quanto estou gastando e usando o Azure com o passar do tempo. Quais são as tendências? Como posso fazer melhor?
-   **Gastos do Azure durante o mês** – quanto meu mês atual está gastando? É necessário fazer qualquer ajuste nos meus gastos e/ou no uso do Azure? Quando durante o mês eu estou consumindo o Azure mais?
-   **Configurar alertas** -eu gostaria de configurar o consumo baseado em recursos ou o alerta baseado em monetário com base em um orçamento.

## <a name="next-steps"></a>Próximos Passos

- Para obter informações sobre como usar APIs de Cobrança do Azure para obter insights programaticamente sobre o uso do Azure, consulte [visão geral da API de cobrança do Azure](billing-usage-rate-card-overview.md).



