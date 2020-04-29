---
title: Defina sugestões personalizadas de autossugestão - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Custom Autosuggest devolve uma lista de cordas de consulta de pesquisa sugeridas que são relevantes para a sua experiência de pesquisa.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: 5b2b8871d868e827532f23c7ef4f14fb00afb5bc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "74072811"
---
# <a name="configure-your-custom-autosuggest-experience"></a>Configure a sua experiência personalizada de autossugestão

Custom Autosuggest devolve uma lista de cordas de consulta de pesquisa sugeridas que são relevantes para a sua experiência de pesquisa. As cordas de consulta sugeridas baseiam-se numa corda de consulta parcial que o utilizador fornece na caixa de pesquisa. A lista conterá um máximo de 10 sugestões. 

Especifica se devolve apenas sugestões personalizadas ou se inclui sugestões de Bing. Se incluir sugestões de Bing, as sugestões personalizadas aparecem antes das sugestões do Bing. Se fornecer sugestões relevantes suficientes, é possível que a lista de sugestões devolvidas não inclua sugestões de Bing. As sugestões de bing estão sempre no contexto da sua instância de Pesquisa Personalizada. 

Para configurar sugestões de consulta de pesquisa para a sua instância, clique no separador **Autosuggest.**  

> [!NOTE]
> Para utilizar esta funcionalidade, deve subscrever a Pesquisa Personalizada ao nível apropriado (ver [preços).](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/)

Pode levar até 24 horas para que as sugestões se reflitam no ponto final de serviço (API ou UI hospedado).

## <a name="enable-bing-suggestions"></a>Ativar sugestões bing

Para permitir sugestões de Bing, alternar as **sugestões automáticas de Bing** deslizam para a posição on. O deslizador torna-se azul.

## <a name="add-your-own-suggestions"></a>Adicione as suas próprias sugestões

Para adicionar as suas próprias sugestões de cordas de consulta, adicione-as à lista sob **sugestões definidas pelo Utilizador**. Depois de adicionar uma sugestão na lista, **+** prima a tecla de entrada ou clique no ícone. Pode especificar a sugestão em qualquer idioma. Pode adicionar um máximo de 5.000 sugestões de cordas de consulta.

## <a name="upload-suggestions"></a>Upload sugestões

Como opção, pode fazer o upload de uma lista de sugestões de um ficheiro. O ficheiro deve conter uma cadeia de consulta de pesquisa por linha. Para fazer o upload do ficheiro, clique no ícone de upload e selecione o ficheiro para fazer o upload. O serviço extrai as sugestões do ficheiro e adiciona-as à lista.

## <a name="remove-suggestions"></a>Remover sugestões

Para remover uma sugestão de corda de consulta, clique no ícone de remoção ao lado da sugestão que pretende remover.

## <a name="block-suggestions"></a>Sugestões de blocos

Se incluir sugestões de Bing, pode adicionar uma lista de cadeias de consulta de pesquisa que não quer que bing regresse. Para adicionar cordas de consulta bloqueadas, clique em **Mostrar sugestões bloqueadas**. Adicione a corda de consulta à lista e **+** pressione a tecla de entrada ou clique no ícone. Pode adicionar um máximo de 50 cordas de consulta bloqueadas.



[!INCLUDE [publish or revert](./includes/publish-revert.md)]

>[!NOTE]  
>Pode levar até 24 horas para que as alterações de configuração personalizadas possam fazer efeito.


## <a name="enabling-autosuggest-in-hosted-ui"></a>Habilitar a Autosuggest na UI hospedada

Para permitir sugestões de cordas de consulta para o seu UI hospedado, clique em **UI hospedado**. Desloque-se até à secção de **Configuração Adicional.** Em **pesquisa na Web,** selecione **On** for **Enable autosuggest**. Para ativar a Sugestão Automática, deve selecionar um layout que inclua uma caixa de pesquisa.


## <a name="calling-the-autosuggest-api"></a>Chamando a API autosuggest

Para obter cordas de consulta sugeridas utilizando a API de pesquisa personalizada bing, envie um `GET` pedido para o seguinte ponto final.

```
GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions 
```

A resposta contém `SearchAction` uma lista de objetos que contêm as cordas de consulta sugeridas.

```
        {  
            "displayText" : "sailing lessons seattle",  
            "query" : "sailing lessons seattle",  
            "searchKind" : "CustomSearch"  
        },  
```

Cada sugestão `displayText` `query` inclui um campo e campo. O `displayText` campo contém a corda de consulta sugerida que usa para preencher a lista de abandono da sua caixa de pesquisa.

Se o utilizador selecionar uma cadeia de consulta sugerida da lista de `query` dropdown, utilize a corda de consulta no campo ao ligar para a [API](overview.md)de Pesquisa Personalizada bing .


## <a name="next-steps"></a>Passos seguintes

- [Obter sugestões personalizadas](./get-custom-suggestions.md)
- [Pesquise a sua instância personalizada](./search-your-custom-view.md)
- [Configure e consuma UI personalizado](./hosted-ui.md)
