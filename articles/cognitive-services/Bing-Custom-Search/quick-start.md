---
title: 'Início rápido: Criar uma primeira instância de Pesquisa Personalizada do Bing | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Use este artigo para criar uma instância personalizada do Bing que pode Pesquisar domínios e páginas da Web que você definir.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 2806ca6f7079ffac3d2222363cd5b3839ef8f97d
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405029"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Início rápido: Criar sua primeira instância de Pesquisa Personalizada do Bing

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

6.  Clique na guia **produção** em **pontos de extremidade**e copie sua ID de **configuração personalizada**. Você precisa dessa ID para chamar o API de pesquisa personalizada anexando-o ao `customconfig=` parâmetro de consulta em suas chamadas.


## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Quickstart: Chamar seu ponto de extremidade de Pesquisa Personalizada do Bing](./call-endpoint-csharp.md)
