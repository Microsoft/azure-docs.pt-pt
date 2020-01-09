---
title: 'Início Rápido: criar uma primeira instância de Pesquisa Personalizada do Bing'
titleSuffix: Azure Cognitive Services
description: Use este guia de início rápido para criar uma instância personalizada do Bing que pode Pesquisar domínios e páginas da Web que você definir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.openlocfilehash: 45478c8e4f5003ff41eb8b486d67caa452739cd4
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75384751"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Início Rápido: criar a sua primeira instância de Pesquisa Personalizada do Bing

Para utilizar a Pesquisa Personalizada do Bing, tem de criar uma instância de pesquisa personalizada que define a vista ou o setor da Web. Essa instância contém os domínios públicos, os sites e as páginas da Web que você deseja pesquisar, juntamente com quaisquer ajustes de classificação que desejar. 

Para criar a instância, use o [portal de pesquisa personalizada do Bing](https://customsearch.ai). 

![Uma imagem do portal de Pesquisa Personalizada do Bing](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Criar uma instância de pesquisa personalizada

Para criar uma instância de Pesquisa Personalizada do Bing:

1. Clique em **introdução** na página da Web do [portal de pesquisa personalizada do Bing](https://customsearch.ai) e entre com sua conta Microsoft.

2. Clique em **nova instância**e insira um nome descritivo. Você pode alterar o nome de sua instância a qualquer momento.
 
3. No separador **Ativo** em **Experiência de Pesquisa**, introduza o URL de um ou mais sites que pretende incluir na sua pesquisa. 

    > [!NOTE]
    > Pesquisa Personalizada do Bing instâncias só retornarão resultados para domínios e páginas da Web que são públicas e foram indexadas pelo Bing.

4. Você pode usar o lado direito do portal de Pesquisa Personalizada do Bing para inserir uma consulta e examinar os resultados da pesquisa retornados por sua instância de pesquisa. Se nenhum resultado for retornado, tente inserir uma URL diferente.  

5. Clique em **publicar** para publicar suas alterações no ambiente de produção e atualizar os pontos de extremidade da instância.

6.  Clique na guia **produção** em **pontos de extremidade**e copie sua ID de **configuração personalizada**. Você precisa dessa ID para chamar o API de Pesquisa Personalizada anexando-o ao parâmetro de consulta `customconfig=` em suas chamadas.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Início rápido: chamar seu ponto de extremidade de Pesquisa Personalizada do Bing](./call-endpoint-csharp.md)
