---
title: 'Início Rápido: criar uma primeira instância de Pesquisa Personalizada do Bing'
titleSuffix: Azure Cognitive Services
description: Use este quickstart para criar uma instância de Bing personalizada que pode pesquisar domínios e páginas web que você define.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: b8287250df4e278d4904e31121ed7d2df208e1c9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80238860"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Início Rápido: criar a sua primeira instância de Pesquisa Personalizada do Bing

Para utilizar a Pesquisa Personalizada do Bing, tem de criar uma instância de pesquisa personalizada que define a vista ou o setor da Web. Esta instância contém os domínios públicos, websites e páginas web que pretende pesquisar, juntamente com quaisquer ajustes de classificação que possa querer. 

Para criar a instância, utilize o [portal bing Custom Search](https://customsearch.ai). 

![Uma foto do portal bing custom search](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Criar uma instância de pesquisa personalizada

Para criar uma instância de Pesquisa Personalizada do Bing:

1. Clique em **Começar** na página web do [portal bing Custom Search](https://customsearch.ai) e inicie sessão com a sua conta Microsoft.

2. Clique em **New Instance**e introduza um nome descritivo. Pode alterar o nome da sua instância a qualquer momento.
 
3. No separador **Ativo** em **Experiência de Pesquisa**, introduza o URL de um ou mais sites que pretende incluir na sua pesquisa. 

    > [!NOTE]
    > As instâncias de Pesquisa Personalizada bing apenas devolverão resultados para domínios, e páginas web que são públicas e foram indexadas por Bing.

4. Pode utilizar o lado direito do portal bing Custom Search para introduzir uma consulta e examinar os resultados de pesquisa devolvidos pela sua instância de pesquisa. Se não forem devolvidos resultados, tente introduzir um URL diferente.  

5. Clique em **Publicar** para publicar as suas alterações no ambiente de produção e atualizar os pontos finais da instância.

6.  Clique no separador **Produção** em **pontos finais**e copie o seu ID de **Configuração Personalizado**. Você precisa deste ID para ligar para a API de pesquisa personalizada, aditando-a ao `customconfig=` parâmetro de consulta nas suas chamadas.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Quickstart: Ligue para o seu ponto final de pesquisa personalizada Bing](./call-endpoint-csharp.md)
