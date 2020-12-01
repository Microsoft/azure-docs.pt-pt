---
title: 'Início Rápido: criar uma primeira instância de Pesquisa Personalizada do Bing'
titleSuffix: Azure Cognitive Services
description: Use este quickstart para criar uma instância Bing personalizada que pode pesquisar domínios e páginas web que você define.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 726a60d611abc392bc1a4629c5088ca4c6b703d9
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96338338"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Início Rápido: criar a sua primeira instância de Pesquisa Personalizada do Bing

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Para utilizar a Pesquisa Personalizada do Bing, tem de criar uma instância de pesquisa personalizada que define a vista ou o setor da Web. Este caso contém os domínios públicos, websites e páginas web que pretende pesquisar, juntamente com quaisquer ajustes de classificação que possa desejar. 

Para criar o caso, utilize o [portal Bing Custom Search](https://customsearch.ai). 

![Uma imagem do portal bing custom search](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Criar uma instância de pesquisa personalizada

Para criar uma instância de Pesquisa Personalizada do Bing:

1. Clique em **Iniciar** a página do [portal Bing Custom Search](https://customsearch.ai) e inicie sôs-se na sua conta Microsoft.

2. Clique em **New Instance** e insira um nome descritivo. Pode alterar o nome do seu caso a qualquer momento.
 
3. No separador **Ativo** em **Experiência de Pesquisa**, introduza o URL de um ou mais sites que pretende incluir na sua pesquisa. 

    > [!NOTE]
    > As instâncias de Pesquisa Personalizada bing apenas devolvem resultados para domínios e páginas web que são públicas e foram indexadas por Bing.

4. Pode utilizar o lado direito do portal Bing Custom Search para introduzir uma consulta e examinar os resultados de pesquisa devolvidos pelo seu caso de pesquisa. Se não forem devolvidos resultados, tente introduzir uma URL diferente.  

5. Clique em **Publicar** para publicar as suas alterações no ambiente de produção e atualizar os pontos finais da instância.

6.  Clique no **separador Produção** em **Pontos finais** e copie o **ID de Configuração Personalizada**. Precisa deste ID para ligar para a API de Pesquisa Personalizada, anexando-a ao `customconfig=` parâmetro de consulta nas suas chamadas.


## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Quickstart: Ligue para o seu ponto de terminação de pesquisa personalizada Bing](./call-endpoint-csharp.md)