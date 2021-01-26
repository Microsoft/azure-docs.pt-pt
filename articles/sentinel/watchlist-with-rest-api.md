---
title: Gerir listas de relógios em Azure Sentinel usando REST API | Microsoft Docs
description: Este artigo descreve como as listas de observação do Azure Sentinel podem ser geridas usando a API REST do Log Analytics para criar, modificar e eliminar listas de observação e seus itens.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798462"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>Gerir listas de relógios em Azure Sentinel usando REST API

> [!IMPORTANT]
> A funcionalidade de listas de relógios encontra-se atualmente em **PREVIEW**. Consulte os [Termos Complementares de Utilização para o Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para termos legais adicionais aplicáveis às funcionalidades do Azure que estejam em versão beta, pré-visualização ou ainda não lançadas em disponibilidade geral.

O Azure Sentinel, que está a ser construído em parte no Azure Monitor Log Analytics, permite-lhe utilizar a API REST do Log Analytics para gerir as listas de observação. Este documento mostra-lhe como criar, modificar e eliminar listas de observação e seus itens utilizando a API REST.  As listas de observação criadas desta forma serão exibidas na UI Azure Sentinel.

## <a name="common-uri-parameters"></a>Parâmetros URI comuns

Seguem-se os parâmetros URI comuns para todos os comandos da API da lista de observação:

| Name | Em | Necessário | Tipo | Descrição |
|-|-|-|-|-|
| **{subscriçãoD}** | caminho | sim | GUID | o ID de assinatura Azure |
| **{recursoGroupName}** | caminho | sim | string | o nome do grupo de recursos dentro da subscrição |
| **{espaço de trabalhoName}** | caminho | sim | string | o nome do espaço de trabalho Log Analytics |
| **{watchlistAlias}** | caminho | Sim, * | string | o nome de uma lista de observação dada<br>\* Não é necessário para recuperar todas as listas de observação |
| **{watchlistItemId}** | caminho | Sim** | GUID | o ID do item para criar, adicionar ou apagar da lista de observação<br>\*\* Requerido apenas para comandos de item de lista de observação |
| **{versão api}** | consulta | sim | string | a versão do protocolo usado para fazer este pedido. A partir de 29 de outubro de 2020, a versão API da Watchlist é *2019-01-01-pré-visualização* |
| **{bearerToken}** | autorização | sim | token | o símbolo de autorização do portador |
|  

## <a name="retrieve-all-watchlists"></a>Recuperar todas as listas de observação

Este comando recupera todas as listas de vigilância associadas a um espaço de trabalho, sem os seus itens.

### <a name="request-uri"></a>URI do pedido
(URI é uma única linha, dividida para uma fácil legibilidade)

| Método | URI do pedido |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>Respostas

| Código de estado | Corpo da resposta | Descrição |
|-|-|-|
| 200 / OK | Lista de listas de vigilância existentes, ou vazia se nenhuma lista de observação encontrada |  |
| 400 / Mau pedido |  | Sintaxe de pedido mal formado, parâmetro de pedido inválido... |
|

## <a name="look-up-a-watchlist-by-name"></a>Procure uma lista de observação pelo nome

Este comando recupera uma lista de observação específica associada a um espaço de trabalho, sem os seus itens.

### <a name="request-uri"></a>URI do pedido
(URI é uma única linha, dividida para uma fácil legibilidade)

| Método | URI do pedido |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Respostas

| Código de estado | Corpo da resposta | Descrição |
|-|-|-|
| 200 / OK | A lista de observação solicitada |  |
| 400 / Mau pedido |  | Sintaxe de pedido mal formado, parâmetro de pedido inválido... |
| 404 / Não encontrado |  | Nenhuma lista de vigilância encontrada com o nome solicitado |
|

## <a name="create-a-watchlist"></a>Criar uma lista de observação

Este comando cria uma lista de observação e adiciona-lhe itens. São precisas duas chamadas para este ponto final para criar a lista de observação e os seus itens: o primeiro criará a lista de observação (pai), e a segunda irá adicionar os itens.

### <a name="request-uri"></a>URI do pedido
(URI é uma única linha, dividida para uma fácil legibilidade)

| Método | URI do pedido |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Corpo do pedido

Aqui está uma amostra do corpo de pedido de uma lista de observação criar pedido:

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>Respostas

| Código de estado | Corpo da resposta | Descrição |
|-|-|-|
| 200 / OK | A lista de observação criada pelo pedido, sem itens |  |
| 400 / Mau pedido |  | Sintaxe de pedido mal formado, parâmetro de pedido inválido... |
| 409 / Conflito |  | A operação falhou, há uma lista de vigilância existente com o mesmo pseudónimo. |
|

## <a name="delete-a-watchlist"></a>Excluir uma lista de observação

Este comando elimina uma lista de observação e os seus itens.

### <a name="request-uri"></a>URI do pedido
(URI é uma única linha, dividida para uma fácil legibilidade)

| Método | URI do pedido |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Respostas

| Código de estado | Corpo da resposta | Descrição |
|-|-|-|
| 200 / OK | Corpo de resposta vazio |  |
| 204 / Sem conteúdo | Corpo de resposta vazio | Nada apagado |
| 400 / Mau pedido |  | Sintaxe de pedido mal formado, parâmetro de pedido inválido... |
|

## <a name="add-or-update-a-watchlist-item"></a>Adicionar ou atualizar um item de lista de observação

Quando este comando for executado num *watchlisItemId* existente (um GUID), atualizará o item com os dados fornecidos no organismo de pedido. Caso contrário, será criado um novo item.

### <a name="request-uri"></a>URI do pedido
(URI é uma única linha, dividida para uma fácil legibilidade)

| Método | URI do pedido |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Corpo do pedido

Aqui está uma amostra do corpo de pedido de um pedido de suplemento/atualização de item da lista de observação:

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>Respostas

| Código de estado | Corpo da resposta | Descrição |
|-|-|-|
| 200 / OK | O item da lista de observação criado ou atualizado pelo pedido |  |
| 400 / Mau pedido |  | Sintaxe de pedido mal formado, parâmetro de pedido inválido... |
| 409 / Conflito |  | A operação falhou, há uma lista de vigilância existente com o mesmo pseudónimo. |
|

## <a name="delete-a-watchlist-item"></a>Excluir um item de lista de observação

Este comando elimina um item de lista de observação existente.

### <a name="request-uri"></a>URI do pedido
(URI é uma única linha, dividida para uma fácil legibilidade)

| Método | URI do pedido |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Respostas

| Código de estado | Corpo da resposta | Descrição |
|-|-|-|
| 200 / OK | Corpo de resposta vazio |  |
| 204 / Sem conteúdo | Corpo de resposta vazio | Nada apagado |
| 400 / Mau pedido |  | Sintaxe de pedido mal formado, parâmetro de pedido inválido... |
|

## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu a gerir listas de observação e seus itens em Azure Sentinel usando a API log Analytics. Para saber mais sobre Azure Sentinel, consulte os seguintes artigos:

- Saiba mais sobre [listas de observação](watchlists.md)
- Explore outros usos da [AZure Sentinel API](/rest/api/securityinsights/)