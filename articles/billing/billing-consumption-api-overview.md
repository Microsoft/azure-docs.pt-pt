---
title: Descrição geral da API de consumo do Azure | Documentos da Microsoft
description: Saiba como APIs de consumo do Azure proporcionam acesso programático a custo e dados de utilização para os seus recursos do Azure.
services: billing
documentationcenter: ''
author: Erikre
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
ms.author: erikre
ms.openlocfilehash: 16e0bdfa0fc70d5239cb4127e61891a013bf54a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615888"
---
# <a name="azure-consumption-api-overview"></a>Descrição geral da API de consumo do Azure 

As APIs de Consumo do Azure oferecem acesso programático aos dados de custo e utilização para os seus recursos do Azure. Essas APIs atualmente apenas suportam inscrições de empresas e assinaturas do Web Direct (com algumas exceções). As APIs são continuamente atualizadas para suportar outros tipos de subscrições do Azure.

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

## <a name="usage-details-api"></a>Detalhes de utilização de API

Utilizar a API de detalhes de utilização para obter dados de utilização e custos para o Azure todos os recursos de terceiros 1. Informações estão na forma de registos de detalhes de utilização que atualmente são emitidos uma vez por medidor por recurso por dia. Informações podem ser utilizadas para aumentar os custos em todos os recursos ou investigar os custos / utilização em recursos específicos. 

A API inclui:

-   **Nível de consumo de dados do medidor** - ver dados, incluindo a utilização de custos, o medidor de emitir a cobrança, e quais recursos do Azure o custo diz respeito a. Todos os registos de detalhes de utilização de mapear para um bucket de diário.
-   **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com), o [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar que os utilizadores ou aplicações podem obter acesso a dados de utilização da subscrição. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Filtragem** -Trim sua API conjunto de resultados para um conjunto menor de registos de detalhes de utilização com os seguintes filtros:
    - Fim de utilização / utilização start
    - Grupo de Recursos
    - Nome do Recurso
-   **Agregação de dados** -OData de utilização para aplicar as expressões para obter detalhes de utilização de agregação por etiquetas ou filtrar as propriedades
-   **Utilização para tipos de oferta diferente** -informações de detalhes de utilização estão atualmente disponíveis para clientes empresariais e do Web Direct.

Para obter mais informações, consulte as especificações técnicas para o [API de detalhes de utilização](https://docs.microsoft.com/rest/api/consumption/usagedetails).

## <a name="marketplace-charges-api"></a>Encargos do Marketplace API

Utilizar a API de encargos do Marketplace para obter dados de utilização e custos em todos os recursos do Marketplace (Azure 3º ofertas de terceiros). Estes dados podem ser utilizados para aumentar os custos em todos os recursos do Marketplace ou investigar os custos / utilização em recursos específicos. 

A API inclui:

-   **Nível de consumo de dados do medidor** - ver dados, incluindo a utilização do marketplace de custos, o medidor de emitir a cobrança, e quais recursos o custo diz respeito a. Todos os registos de detalhes de utilização de mapear para um bucket de diário.
-   **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com), o [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar que os utilizadores ou aplicações podem obter acesso a dados de utilização da subscrição. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Filtragem** -Trim sua API conjunto de resultados para um conjunto menor de registos de marketplace utilizando os seguintes filtros:
    - Início de utilização / terminar de utilização
    - Grupo de Recursos
    - Nome do Recurso
-   **Utilização para tipos de oferta diferente** -informação do Marketplace está atualmente disponível para clientes empresariais e do Web Direct.

Para obter mais informações, consulte as especificações técnicas para o [API de encargos do Marketplace](https://docs.microsoft.com/rest/api/consumption/marketplaces).

## <a name="balances-api"></a>API de saldos

Os clientes empresariais podem utilizar a API de saldos para obter um mensal resumo de informações sobre os saldos, novas compras, os encargos de serviços do Azure Marketplace, ajustes e custos de utilização excedida. Pode obter estas informações para o período de faturação atual ou qualquer período no passado. As empresas podem utilizar estes dados para executar uma comparação com custos de resumos manualmente calculadas. Esta API não fornece informações de recursos específicos e uma visualização agregada dos custos. 

A API inclui:

-   **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com), o [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar que os utilizadores ou aplicações podem obter acesso a dados de utilização da subscrição. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Apenas os clientes de Enterprise** esta API está apenas disponíveis aos clientes com EA. 
    - Os clientes tem de ter permissões de administrador da empresa para chamar esta API 

Para obter mais informações, consulte as especificações técnicas para o [saldos API](https://docs.microsoft.com/rest/api/consumption/balances).

## <a name="budgets-api"></a>Orçamentos de API

Os clientes empresariais podem utilizar esta API para criar os orçamentos de custos ou utilização de recursos, grupos de recursos ou medidores de faturação. Depois desta informação ter sido determinada, alertas podem ser configurado para ser notificado quando o orçamento definidas pelo utilizador limiares forem ultrapassados. 

A API inclui:

-   **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com), o [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar que os utilizadores ou aplicações podem obter acesso a dados de utilização da subscrição. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Apenas os clientes de Enterprise** -esta API está apenas disponíveis aos clientes com EA.
-   **Notificações configuráveis** -especifique o utilizador (es) para ser notificado quando o orçamento é ultrapassado.
-   **Utilização ou custo orçamentos de com base** -criar o seu orçamento com base no consumo ou custo conforme necessário, ao seu cenário.
-   **Filtragem** -filtrar o seu orçamento para um subconjunto menor de recursos com os seguintes filtros configuráveis
    - Grupo de Recursos
    - Nome do Recurso
    - Medidor
-   **Períodos de tempo configurável orçamento** -especifique a frequência com que o orçamento deverá repor e o período de tempo é válido para o orçamento.

Para obter mais informações, consulte as especificações técnicas para o [orçamentos API](https://docs.microsoft.com/rest/api/consumption/budgets).

## <a name="reservation-recommendations-api"></a>API de recomendações de reserva

Utilize esta API para obter recomendações para a compra de instâncias de VM reservadas. Recomendações destinam-se para permite aos clientes analisar economias de custo previsto e quantidades de compra. 

A API inclui:

-   **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com), o [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar que os utilizadores ou aplicações podem obter acesso a dados de utilização da subscrição. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Filtragem** -adaptar seus resultados de recomendação usando os seguintes filtros:
    - Âmbito
    - Período de Lookback
-   **Informações de reserva para tipos de oferta diferente** -informações de reserva estão atualmente disponíveis para clientes empresariais e do Web Direct.

Para obter mais informações, consulte as especificações técnicas para o [API de recomendações de reserva](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations).

## <a name="reservation-details-api"></a>Detalhes da reserva API

Utilize a API de detalhes de reserva para ver informações sobre anteriormente adquiridas reservas de VM, como a quantidade de consumo foi reservado versus quanto, na verdade, está a ser utilizado. Pode ver os dados num por VM nível de detalhe. 

A API inclui:

-   **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com), o [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar que os utilizadores ou aplicações podem obter acesso a dados de utilização da subscrição. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Filtragem** -Trim sua API conjunto de resultados para um conjunto menor de reservas usando o seguinte filtro:
    - Intervalo de datas
-   **Informações de reserva para tipos de oferta diferente** -informações de reserva estão atualmente disponíveis para clientes empresariais e do Web Direct.

Para obter mais informações, consulte as especificações técnicas para o [API de detalhes de reserva](https://docs.microsoft.com/rest/api/consumption/reservationsdetails).

## <a name="reservation-summaries-api"></a>Resumos de reserva API

Utilize esta API para ver informações agregadas sobre anteriormente adquiridas reservas de VM, como a quantidade de consumo foi reservado versus quanto está realmente sendo usada no agregado. 

A API inclui:

-   **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com), o [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar que os utilizadores ou aplicações podem obter acesso a dados de utilização da subscrição. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Filtragem** -personalizar os resultados ao utilizar a base diária com o seguinte filtro:
    - Data de Utilização
-   **Informações de reserva para tipos de oferta diferente** -informações de reserva estão atualmente disponíveis para clientes empresariais e do Web Direct.
-   **Agregações diárias ou mensais** – os autores de chamadas podem especificar se querem que seus dados de resumo de reserva no intervalo de agregação diário ou mensal.

Para obter mais informações, consulte as especificações técnicas para o [API de resumos de reserva](https://docs.microsoft.com/rest/api/consumption/reservationssummaries).

## <a name="price-sheet-api"></a>API da folha de preços
Cliente empresarial pode utilizar esta API para obter seus preços personalizados para todos os medidores. As empresas podem utilizar isto em combinação com informações de utilização de detalhes e mercados de utilização para realizar cálculos de custo com dados de utilização e o marketplace. 

A API inclui:

-   **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com), o [CLI do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli) ou [cmdlets do Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para especificar que os utilizadores ou aplicações podem obter acesso a dados de utilização da subscrição. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica. 
-   **Apenas os clientes de Enterprise** -esta API está apenas disponíveis aos clientes com EA. Os clientes Web Direct devem utilizar a API de RateCard para obter preços. 

Para obter mais informações, consulte as especificações técnicas para o [API da folha de preços](https://docs.microsoft.com/rest/api/consumption/pricesheet).

## <a name="scenarios"></a>Cenários

Aqui estão alguns dos cenários que são possíveis via o consumo de APIs:

-   **Reconciliação de nota fiscal** -Microsoft fez-me cobrada a quantidade certa?  O que é a minha fatura e pode eu calculo-lo eu mesmo?
-   **Cross custos** - agora que sei quanto eu estou a ser cobrado, que, na minha organização, precisa de pagar?
-   **Otimização de custos** -sei quanto eu tenha sido cobrado... como posso obter mais o dinheiro que estou a passar no Azure?
-   **Controlo de custos** -Quero ver quanto eu estou gastos e a utilizar o Azure ao longo do tempo. Quais são as tendências? Como foi que estar fazendo melhor?
-   **Gastos do Azure durante o mês** -quanto meu mês atual é de gastar até à data? É necessário fazer os ajustes na minha gastos e/ou utilização do Azure? Quando durante o mês estou que consumindo Azure mais?
-   **Configure alertas** – eu gostaria de configurar o consumo de recursos ou monetário com base em alertas com base num orçamento.

## <a name="next-steps"></a>Próximos Passos

- Para obter informações sobre como utilizar as APIs de faturação do Azure para obter informações sobre sua utilização do Azure programaticamente, consulte [descrição geral da API de faturação do Azure](billing-usage-rate-card-overview.md).



