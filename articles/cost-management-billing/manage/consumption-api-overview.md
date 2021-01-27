---
title: Descrição geral da API de consumo do Azure
description: Saiba como as APIs de Consumo do Azure proporcionam acesso programático aos dados de custo e de utilização dos seus recursos do Azure.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: reference
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: 4b8b24bacaee87dc9868fab1d5d071201a7215b8
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897735"
---
# <a name="azure-consumption-api-overview"></a>Descrição geral da API de consumo do Azure

As APIs de Consumo do Azure oferecem acesso programático aos dados de custo e utilização para os seus recursos do Azure. Atualmente, estas APIs suportam apenas Inscrições Enterprise e Subscrições Web Direct (com algumas exceções). As APIs são continuamente atualizadas para suportar outros tipos de subscrições do Azure.

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

## <a name="usage-details-api"></a>API de Detalhes de Utilização

Utilize a API de Detalhes de Utilização para obter os dados de utilização e de custos de todos os recursos originais do Azure. As informações estão na forma de registos de detalhes de utilização, que atualmente são emitidos uma vez por dia por medidor e por recurso. As informações podem ser utilizadas para somar os custos em todos os recursos ou para investigar os custos/utilização em recursos específicos.

A API inclui:

-    **Dados de Consumo de Nível de Medição** – veja os dados, incluindo o custo de utilização, o medidor que emite o custo e a que recurso do Azure pertence o custo. Todos os registos de detalhes de utilização são mapeados para um registo diário.
-    **Azure control de acesso baseado em funções (Azure RBAC)** - Configurar as políticas de acesso no [portal Azure](https://portal.azure.com), os cmdlets [Azure CLI](../../role-based-access-control/role-assignments-cli.md) ou [Azure PowerShell](/powershell/azure/) para especificar quais os utilizadores ou aplicações que podem ter acesso aos dados de utilização da subscrição. Os chamadores devem utilizar tokens do Azure Active Directory padrão para a autenticação. Adicione o chamador à função Leitor de Faturação, Leitor, Proprietário ou Contribuidor para obter acesso aos dados de utilização de uma subscrição específica do Azure.
-    **Filtragem** – corte o conjunto de resultados da API para obter um conjunto menor de registos de detalhes de utilização com os seguintes filtros:
    - Fim de utilização/início de utilização
    - Grupo de Recursos
    - Nome do Recurso
-    **Agregação de Dados** – utilize o OData para aplicar expressões para agregar os detalhes de utilização por etiquetas ou propriedades de filtro
-    **Utilização para diferentes tipos de oferta** – as informações de detalhes de utilização estão atualmente disponíveis para clientes Enterprise e Web Direct.

Para obter mais informações, veja a especificação técnica da [Usage Details API](/rest/api/consumption/usagedetails) (API de Detalhes de Utilização).

## <a name="marketplace-charges-api"></a>API de Custos do Marketplace

Utilize a API de Custos do Marketplace para obter dados de utilização e de custos em todos os recursos do Marketplace (ofertas de terceiros do Azure). Estes dados podem ser utilizados para somar os custos em todos os recursos do Marketplace ou para investigar os custos/utilização num recurso ou em recursos específicos.

A API inclui:

-    **Dados de Consumo de Nível de Medição** – veja os dados, incluindo o custo de utilização do Marketplace, o medidor que emite o encargo e a que recurso pertence o encargo. Todos os registos de detalhes de utilização são mapeados para um registo diário.
-    **Azure control de acesso baseado em funções (Azure RBAC)** - Configurar as políticas de acesso no [portal Azure](https://portal.azure.com), os cmdlets [Azure CLI](../../role-based-access-control/role-assignments-cli.md) ou [Azure PowerShell](/powershell/azure/) para especificar quais os utilizadores ou aplicações que podem ter acesso aos dados de utilização da subscrição. Os chamadores devem utilizar tokens do Azure Active Directory padrão para a autenticação. Adicione o chamador à função Leitor de Faturação, Leitor, Proprietário ou Contribuidor para obter acesso aos dados de utilização de uma subscrição específica do Azure.
-    **Filtragem** – corte o conjunto de resultados da API para obter um conjunto menor de registos do Marketplace com os seguintes filtros:
    - Início de utilização/fim de utilização
    - Grupo de Recursos
    - Nome do Recurso
-    **Utilização para diferentes tipos de oferta** – as informações do Marketplace estão atualmente disponíveis para clientes Enterprise e Web Direct.

Para obter mais informações, veja a especificação técnica da [Marketplace Charges API](/rest/api/consumption/marketplaces) (API de Encargos do Marketplace).

## <a name="balances-api"></a>API de Saldos

Os clientes Enterprise podem utilizar a API de Saldos para obter um resumo mensal das informações sobre os saldos, as novas compras, os custos dos serviços do Azure Marketplace, os ajustes e os custos de utilização excedida. Pode obter estas informações para o período de faturação atual ou para qualquer período no passado. As empresas podem utilizar estes dados para fazer uma comparação com os custos de resumo calculados manualmente. Esta API não fornece informações específicas dos recursos nem uma vista agregada dos custos.

A API inclui:

-    **Azure control de acesso baseado em funções (Azure RBAC)** - Configurar as políticas de acesso no [portal Azure](https://portal.azure.com), os cmdlets [Azure CLI](../../role-based-access-control/role-assignments-cli.md) ou [Azure PowerShell](/powershell/azure/) para especificar quais os utilizadores ou aplicações que podem ter acesso aos dados de utilização da subscrição. Os chamadores devem utilizar tokens do Azure Active Directory padrão para a autenticação. Adicione o chamador à função Leitor de Faturação, Leitor, Proprietário ou Contribuidor para obter acesso aos dados de utilização de uma subscrição específica do Azure.
-    **Apenas Clientes Enterprise** – esta API está disponível apenas para clientes EA.
    - Os clientes devem ter permissões de Administrador do Enterprise para chamar esta API

Para obter mais informações, veja a especificação técnica da [Balances API](/rest/api/consumption/balances) (API de Saldos).

## <a name="budgets-api"></a>API de Orçamentos

Os clientes Enterprise podem utilizar esta API para criar orçamentos de custo ou de utilização para recursos, grupos de recursos ou medidores de faturação. Logo que estas informações tenham sido determinadas, podem ser configurados alertas para notificar quando forem excedidos os limiares de orçamento definidos pelo utilizador.

A API inclui:

-    **Azure control de acesso baseado em funções (Azure RBAC)** - Configurar as políticas de acesso no [portal Azure](https://portal.azure.com), os cmdlets [Azure CLI](../../role-based-access-control/role-assignments-cli.md) ou [Azure PowerShell](/powershell/azure/) para especificar quais os utilizadores ou aplicações que podem ter acesso aos dados de utilização da subscrição. Os chamadores devem utilizar tokens do Azure Active Directory padrão para a autenticação. Adicione o chamador à função Leitor de Faturação, Leitor, Proprietário ou Contribuidor para obter acesso aos dados de utilização de uma subscrição específica do Azure.
-    **Apenas Clientes Enterprise** – esta API está apenas disponível para clientes EA.
-    **Notificações Configuráveis** – especifique os utilizadores que serão notificados quando o orçamento for ultrapassado.
-    **Orçamentos com Base na Utilização ou no Custo** – crie o seu orçamento com base no consumo ou no custo conforme a necessidade do seu cenário.
-    **Filtragem** – filtre o seu orçamento para um subconjunto menor de recursos através dos seguintes filtros configuráveis
    - Grupo de Recursos
    - Nome do Recurso
    - Medidor
-    **Períodos de tempo de orçamento configuráveis** – especifique a frequência com que o orçamento deve ser redefinido e por quanto tempo o orçamento é válido.

Para obter mais informações, veja a especificação técnica da [Budgets API](/rest/api/consumption/budgets) (API de Orçamentos).

## <a name="reservation-recommendations-api"></a>API de Recomendações de Reservas

Utilize esta API para obter recomendações para comprar Instâncias de VM Reservadas. As recomendações foram concebidas para permitir que os clientes analisem a poupança de custos e os valores de compra esperados.

A API inclui:

-    **Azure control de acesso baseado em funções (Azure RBAC)** - Configurar as políticas de acesso no [portal Azure](https://portal.azure.com), os cmdlets [Azure CLI](../../role-based-access-control/role-assignments-cli.md) ou [Azure PowerShell](/powershell/azure/) para especificar quais os utilizadores ou aplicações que podem ter acesso aos dados de utilização da subscrição. Os chamadores devem utilizar tokens do Azure Active Directory padrão para a autenticação. Adicione o chamador à função Leitor de Faturação, Leitor, Proprietário ou Contribuidor para obter acesso aos dados de utilização de uma subscrição específica do Azure.
-    **Filtragem** – personalize os seus resultados de recomendação com os seguintes filtros:
    - Âmbito
    - Período retroativo
-    **Informações de reserva para diferentes tipos de oferta** – as informações de reserva estão atualmente disponíveis para clientes Enterprise e Web Direct.

Para obter mais informações, veja a especificação técnica da [Reservation Recommendations API](/rest/api/consumption/reservationrecommendations) (API de Recomendações de Reserva).

## <a name="reservation-details-api"></a>API de Detalhes das Reservas

Utilize a API de Detalhes das Reservas para ver informações sobre as reservas de VMs compradas anteriormente, como a quantidade de consumo reservado versus o que está realmente a ser utilizado. Pode ver os dados ao nível da VM.

A API inclui:

-    **Azure control de acesso baseado em funções (Azure RBAC)** - Configurar as políticas de acesso no [portal Azure](https://portal.azure.com), os cmdlets [Azure CLI](../../role-based-access-control/role-assignments-cli.md) ou [Azure PowerShell](/powershell/azure/) para especificar quais os utilizadores ou aplicações que podem ter acesso aos dados de utilização da subscrição. Os chamadores devem utilizar tokens do Azure Active Directory padrão para a autenticação. Adicione o chamador à função Leitor de Faturação, Leitor, Proprietário ou Contribuidor para obter acesso aos dados de utilização de uma subscrição específica do Azure.
-    **Filtragem** – corte o conjunto de resultados da API para obter um conjunto menor de reservas com os seguintes filtros:
    - Intervalo de datas
-    **Informações de reserva para diferentes tipos de oferta** – as informações de reserva estão atualmente disponíveis para clientes Enterprise e Web Direct.

Para obter mais informações, veja a especificação técnica da [Reservation Details API](/rest/api/consumption/reservationsdetails) (API de Detalhes de Reserva).

## <a name="reservation-summaries-api"></a>API de Resumos das Reservas

Utilize esta API para ver informações agregadas sobre as reservas de VMs compradas anteriormente, como a quantidade de consumo reservado versus o que está realmente a ser utilizado na agregação.

A API inclui:

-    **Azure control de acesso baseado em funções (Azure RBAC)** - Configurar as políticas de acesso no [portal Azure](https://portal.azure.com), os cmdlets [Azure CLI](../../role-based-access-control/role-assignments-cli.md) ou [Azure PowerShell](/powershell/azure/) para especificar quais os utilizadores ou aplicações que podem ter acesso aos dados de utilização da subscrição. Os chamadores devem utilizar tokens do Azure Active Directory padrão para a autenticação. Adicione o chamador à função Leitor de Faturação, Leitor, Proprietário ou Contribuidor para obter acesso aos dados de utilização de uma subscrição específica do Azure.
-    **Filtragem** – personalize os seus resultados com um detalhamento diário com o seguinte filtro:
    - Data de Utilização
-    **Informações de reserva para diferentes tipos de oferta** – as informações de reserva estão atualmente disponíveis para clientes Enterprise e Web Direct.
-    **Agregações diárias ou mensais** – os chamadores podem especificar se querem os seus dados de resumo de reservas com detalhamento diário ou mensal.

Para obter mais informações, veja a especificação técnica da [Reservation Summaries API](/rest/api/consumption/reservationssummaries) (API de Resumos de Reserva).

## <a name="price-sheet-api"></a>API de Folha de Preços
O cliente Enterprise pode utilizar esta API para obter os preços personalizados de todos os medidores. As empresas podem utilizar esta API em combinação com os detalhes de utilização e as informações de utilização dos Marketplaces para calcular os custos com os dados de utilização e do Marketplace.

A API inclui:

-    **Azure control de acesso baseado em funções (Azure RBAC)** - Configurar as políticas de acesso no [portal Azure](https://portal.azure.com), os cmdlets [Azure CLI](../../role-based-access-control/role-assignments-cli.md) ou [Azure PowerShell](/powershell/azure/) para especificar quais os utilizadores ou aplicações que podem ter acesso aos dados de utilização da subscrição. Os chamadores devem utilizar tokens do Azure Active Directory padrão para a autenticação. Adicione o chamador à função Leitor de Faturação, Leitor, Proprietário ou Contribuidor para obter acesso aos dados de utilização de uma subscrição específica do Azure.
-    **Apenas Clientes Enterprise** – esta API está apenas disponível para clientes EA. Os clientes Web Direct devem utilizar a API RateCard para obter os preços.

Para obter mais informações, veja a especificação técnica da [Price Sheet API](/rest/api/consumption/pricesheet) (API de Folha de Preços).

## <a name="scenarios"></a>Cenários

Veja a seguir alguns dos cenários que se tornaram possíveis com as APIs de consumo:

-    **Reconciliação da Fatura** – A Microsoft faturou-me o montante correto?  De quanto é a minha fatura e posso ser eu a calculá-la?
-    **Encargos Cruzados** – Agora que sei quanto está a ser faturado, quem na minha organização terá de pagar?
-    **Otimização de Custos** – Sei o montante que me foi faturado... Como posso aproveitar ao máximo o dinheiro que estou a gastar no Azure?
-    **Controlo de Custos** – Quero ver quanto estou a gastar e como estou utilizar o Azure ao longo do tempo. Quais são as tendências? Como posso melhorar?
-    **Azure gastar durante o mês** - Quanto é que o meu mês atual gasta até à data? É necessário fazer ajustes nos meus gastos e/ou na utilização do Azure? Em que alturas do mês é maior o consumo do Azure?
-    **Configurar alertas** – Gostaria de configurar alertas de consumo baseado em recursos ou alertas monetários com base num orçamento.

## <a name="next-steps"></a>Passos Seguintes

- Para obter informações sobre a utilização de APIs REST para todos os serviços Azure, consulte [a visão geral dos preços de venda a retalho do Azure](/rest/api/cost-management/retail-prices/azure-retail-prices).
