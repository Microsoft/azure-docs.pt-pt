---
title: Definir as sugestões de sugestão automática personalizada - pesquisa personalizada do Bing
titlesuffix: Azure Cognitive Services
description: Descreve como configurar o acesso de sugestão automática personalizada com sugestões personalizadas
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: bbad72b41a177bdbafd6cf98bfd2025190d98b16
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62128962"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Configurar a sua experiência de sugestão automática personalizada

Sugestão automática personalizada devolve uma lista de cadeias de consulta de pesquisa sugerida que são relevantes para a sua experiência de pesquisa. As cadeias de caracteres de consulta sugeridos são baseadas numa cadeia de consulta parcial que o utilizador fornece na caixa de pesquisa. A lista irá conter um máximo de 10 sugestões. 

Especifique se pretende devolver sugestões apenas personalizadas ou também incluem sugestões do Bing. Se incluir sugestões do Bing, sugestões personalizadas aparecem antes das sugestões do Bing. Se fornecer suficiente sugestões relevantes, é possível que a lista devolvida de sugestões não incluirá sugestões do Bing. Sugestões do Bing são sempre no contexto da sua instância de pesquisa personalizada. 

Para configurar as sugestões de consulta de pesquisa para a sua instância, clique a **sugestão automática** separador.  

> [!NOTE]
> Para utilizar esta funcionalidade, tem de subscrever a pesquisa personalizada ao nível apropriado (consulte [preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Pode demorar até 24 horas para sugestões sejam refletidas no ponto de extremidade que serve (API ou alojada da interface do Usuário).

## <a name="enable-bing-suggestions"></a>Ativar as sugestões do Bing

Para ativar as sugestões de Bing, alternar entre o **sugestões automática Bing** controlo de deslize para a posição no. O controlo de deslize torna-se azul.

## <a name="add-your-own-suggestions"></a>Adicionar suas próprias sugestões

Para adicionar suas próprias sugestões de cadeia de caracteres de consulta, adicioná-los à lista sob **sugestões definidas pelo utilizador**. Depois de adicionar uma sugestão na lista, prima a tecla enter ou clique a **+** ícone. Pode especificar a sugestão em qualquer idioma. Pode adicionar um máximo de 5000 sugestões de cadeia de caracteres de consulta.

## <a name="upload-suggestions"></a>Carregar sugestões

Como opção, pode carregar uma lista de sugestões de um ficheiro. O ficheiro tem de conter uma cadeia de consulta de pesquisa por linha. Para carregar o ficheiro, clique no ícone de carregamento e selecione o ficheiro a carregar. O serviço extrai as sugestões a partir do ficheiro e adiciona-os à lista.

## <a name="remove-suggestions"></a>Remova as sugestões

Para remover uma sugestão de cadeia de caracteres de consulta, clique no ícone de remover junto a sugestão de que pretende remover.

## <a name="block-suggestions"></a>Bloquear as sugestões

Se incluir sugestões do Bing, pode adicionar uma lista de cadeias de consulta de pesquisa que não pretende que o Bing a devolver. Para adicionar a cadeias de consulta bloqueado, clique em **Show bloqueado sugestões**. Adicionar a cadeia de consulta à lista e prima a tecla enter ou clique nas **+** ícone. Pode adicionar um máximo de 50 cadeias de caracteres de consulta bloqueado.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Pode demorar até 24 horas para alterações de configuração de sugestão automática personalizada entrar em vigor.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Ativar a sugestão automática na interface do Usuário alojado

Para ativar as sugestões de cadeia de caracteres de consulta para a interface do Usuário alojado, clique em **alojada a interface do Usuário**. Desloque para baixo para o **configuração adicional** secção. Sob **pesquisa na Web**, selecione **no** para **Enable de sugestão automática**. Para ativar a sugestão automática, tem de selecionar um esquema que inclui uma caixa de pesquisa.


## <a name="calling-the-autosuggest-api"></a>Chamar a API de sugestão automática

Para obter cadeias de caracteres de consulta sugerida com a API de pesquisa personalizada do Bing, enviar um `GET` pedido para o seguinte ponto de extremidade.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

A resposta contém uma lista de `SearchAction` objetos que contêm as cadeias de caracteres de consulta sugerida.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Cada sugestão inclui um `displayText` e `query` campo. O `displayText` campo contém a cadeia de consulta sugerido que utiliza para preencher a lista suspensa de sua caixa de pesquisa.

Se o usuário seleciona uma cadeia de consulta sugerido na lista pendente, utilize a cadeia de consulta no `query` campo ao chamar o [API de pesquisa personalizada do Bing](overview.md).


## <a name="next-steps"></a>Passos Seguintes

- [Obtenha sugestões personalizadas](./get-custom-suggestions.md)
- [Pesquisar a instância personalizada](./search-your-custom-view.md)
- [Configurar e consumir da interface do Usuário hospedado personalizado](./hosted-ui.md)
