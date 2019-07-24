---
title: Definir sugestões de sugestão automática personalizada-Pesquisa Personalizada do Bing
titleSuffix: Azure Cognitive Services
description: Descreve como configurar a sugestão automática personalizada com sugestões personalizadas
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: b08eb49b0c4f9655326d2ab09ce39210205e28a9
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405101"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Configurar sua experiência de sugestão automática personalizada

A sugestão automática personalizada retorna uma lista de cadeias de caracteres de consulta de pesquisa sugeridas que são relevantes para sua experiência de pesquisa. As cadeias de consulta sugeridas se baseiam em uma cadeia de caracteres de consulta parcial que o usuário fornece na caixa de pesquisa. A lista conterá um máximo de 10 sugestões. 

Especifique se deseja retornar apenas sugestões personalizadas ou também incluir sugestões do Bing. Se você incluir sugestões do Bing, as sugestões personalizadas aparecerão antes das sugestões do Bing. Se você fornecer sugestões relevantes suficientes, será possível que a lista de sugestões retornada não inclua sugestões do Bing. As sugestões do Bing estão sempre no contexto da sua instância de pesquisa personalizada. 

Para configurar as sugestões de consulta de pesquisa para sua instância, clique na guia **sugestão** automática.  

> [!NOTE]
> Para usar esse recurso, você deve assinar a pesquisa personalizada no nível apropriado (consulte [preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)).

Pode levar até 24 horas para que as sugestões sejam refletidas no ponto de extremidade de serviço (API ou interface do usuário hospedada).

## <a name="enable-bing-suggestions"></a>Habilitar sugestões do Bing

Para habilitar sugestões do Bing, alterne o controle deslizante de **sugestões do Bing automático** para a posição ligado. O controle deslizante se torna azul.

## <a name="add-your-own-suggestions"></a>Adicione suas próprias sugestões

Para adicionar suas próprias sugestões de cadeia de caracteres de consulta, adicione-as à lista em **sugestões definidas pelo usuário**. Depois de adicionar uma sugestão na lista, pressione a tecla Enter ou clique no **+** ícone. Você pode especificar a sugestão em qualquer idioma. Você pode adicionar no máximo 5.000 sugestões de cadeia de caracteres de consulta.

## <a name="upload-suggestions"></a>Carregar sugestões

Como opção, você pode carregar uma lista de sugestões de um arquivo. O arquivo deve conter uma cadeia de caracteres de consulta de pesquisa por linha. Para carregar o arquivo, clique no ícone carregar e selecione o arquivo a ser carregado. O serviço extrai as sugestões do arquivo e as adiciona à lista.

## <a name="remove-suggestions"></a>Remover sugestões

Para remover uma sugestão de cadeia de caracteres de consulta, clique no ícone Remover ao lado da sugestão que você deseja remover.

## <a name="block-suggestions"></a>Bloquear sugestões

Se você incluir sugestões do Bing, poderá adicionar uma lista de cadeias de consulta de pesquisa que não deseja que o Bing retorne. Para adicionar cadeias de caracteres de consulta bloqueadas, clique em **Mostrar sugestões bloqueadas**. Adicione a cadeia de caracteres de consulta à lista e pressione a tecla Enter ou **+** clique no ícone. Você pode adicionar no máximo 50 cadeias de caracteres de consulta bloqueadas.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Pode levar até 24 horas para que as alterações de configuração de sugestão automática personalizada entrem em vigor.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Habilitando a sugestão automática na interface do usuário hospedada

Para habilitar sugestões de cadeia de caracteres de consulta para sua interface do usuário hospedada, clique em **IU hospedada**. Role para baixo até a seção **configuração adicional** . Em **pesquisa da Web**, selecione **ativado** para **habilitar a sugestão**automática. Para habilitar a sugestão automática, você deve selecionar um layout que inclua uma caixa de pesquisa.


## <a name="calling-the-autosuggest-api"></a>Chamando a API de sugestão automática

Para obter cadeias de caracteres de consulta sugeridas usando `GET` o API de pesquisa personalizada do Bing, envie uma solicitação para o ponto de extremidade a seguir.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

A resposta contém uma lista de `SearchAction` objetos que contêm as cadeias de caracteres de consulta sugeridas.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Cada sugestão inclui um `displayText` campo `query` e. O `displayText` campo contém a cadeia de caracteres de consulta sugerida que você usa para preencher a lista suspensa da caixa de pesquisa.

Se o usuário selecionar uma cadeia de caracteres de consulta sugerida na lista suspensa, use a cadeia `query` de caracteres de consulta no campo ao chamar o [API de pesquisa personalizada do Bing](overview.md).


## <a name="next-steps"></a>Passos Seguintes

- [Obter sugestões personalizadas](./get-custom-suggestions.md)
- [Pesquisar sua instância personalizada](./search-your-custom-view.md)
- [Configurar e consumir a interface do usuário hospedada personalizada](./hosted-ui.md)
