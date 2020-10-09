---
title: Defina sugestões personalizadas de autosusuggest - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Custom Autosuggest devolve uma lista de cadeias de consulta de pesquisa sugeridas que são relevantes para a sua experiência de pesquisa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 5b2b8871d868e827532f23c7ef4f14fb00afb5bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "74072811"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Configure a sua experiência autosuggest personalizada

Custom Autosuggest devolve uma lista de cadeias de consulta de pesquisa sugeridas que são relevantes para a sua experiência de pesquisa. As cadeias de consulta sugeridas baseiam-se numa cadeia de consulta parcial que o utilizador fornece na caixa de pesquisa. A lista conterá um máximo de 10 sugestões. 

Especificar se deve devolver apenas sugestões personalizadas ou incluir também sugestões de Bing. Se incluir sugestões de Bing, as sugestões personalizadas aparecem antes das sugestões de Bing. Se fornecer sugestões relevantes suficientes, é possível que a lista de sugestões devolvidas não inclua sugestões de Bing. As sugestões de Bing estão sempre no contexto da sua instância de Pesquisa Personalizada. 

Para configurar sugestões de consulta de pesquisa para o seu exemplo, clique no **separador Autosuggest.**  

> [!NOTE]
> Para utilizar esta função, tem de subscrever a Pesquisa Personalizada ao nível apropriado (ver [preços).](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)

Pode levar até 24 horas para que as sugestões sejam refletidas no ponto final de serviço (API ou UI hospedado).

## <a name="enable-bing-suggestions"></a>Ativar sugestões de Bing

Para ativar as sugestões de Bing, altere as **sugestões Automáticas de Bing** para a posição de on. O slider torna-se azul.

## <a name="add-your-own-suggestions"></a>Adicione as suas próprias sugestões

Para adicionar as suas próprias sugestões de cadeia de consulta, adicione-as à lista de acordo com **sugestões definidas pelo Utilizador**. Depois de adicionar uma sugestão na lista, prima a tecla de entrada ou clique no **+** ícone. Pode especificar a sugestão em qualquer idioma. Pode adicionar um máximo de 5.000 sugestões de cordas de consulta.

## <a name="upload-suggestions"></a>Sugestões de upload

Como opção, pode fazer o upload de uma lista de sugestões de um ficheiro. O ficheiro deve conter uma cadeia de consulta de pesquisa por linha. Para fazer o upload do ficheiro, clique no ícone de upload e selecione o ficheiro para carregar. O serviço extrai as sugestões do ficheiro e adiciona-as à lista.

## <a name="remove-suggestions"></a>Remover sugestões

Para remover uma sugestão de cadeia de consulta, clique no ícone de remover ao lado da sugestão que pretende remover.

## <a name="block-suggestions"></a>Sugestões de blocos

Se incluir sugestões de Bing, pode adicionar uma lista de cadeias de consulta de pesquisa que não quer que bing regresse. Para adicionar cadeias de consulta bloqueadas, clique em **Mostrar sugestões bloqueadas**. Adicione o fio de consulta à lista e pressione a tecla de entrada ou clique no **+** ícone. Pode adicionar um máximo de 50 cadeias de consulta bloqueadas.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Pode levar até 24 horas para que as alterações de configuração personalizadas de autosususestes entrem em vigor.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Permitir a Autosuggest em UI hospedado

Para ativar sugestões de cadeias de consulta para a UI hospedada, clique em **UI anfitrião**. Desloque-se para baixo para a secção **de Configuração Adicional.** Em **pesquisa na Web**, selecione **On** for **Enable autosuggest**. Para ativar o Autosuggest, tem de selecionar um layout que inclua uma caixa de pesquisa.


## <a name="calling-the-autosuggest-api"></a>Chamando a API autosuggest

Para obter as cordas de consulta sugeridas usando a API de pesquisa personalizada Bing, envie um `GET` pedido para o seguinte ponto final.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

A resposta contém uma lista de `SearchAction` objetos que contêm as cordas de consulta sugeridas.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Cada sugestão inclui um `displayText` `query` campo e um campo. O `displayText` campo contém a cadeia de consulta sugerida que usa para preencher a lista de retirada da sua caixa de pesquisa.

Se o utilizador selecionar uma cadeia de consulta sugerida a partir da lista de retiradas, utilize a cadeia de consulta no campo ao `query` ligar para a API de Pesquisa Personalizada [Bing](overview.md).


## <a name="next-steps"></a>Passos seguintes

- [Obter sugestões personalizadas](./get-custom-suggestions.md)
- [Pese na sua instância personalizada](./search-your-custom-view.md)
- [Configure e consuma uI personalizado hospedado](./hosted-ui.md)
