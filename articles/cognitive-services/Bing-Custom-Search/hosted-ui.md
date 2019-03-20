---
title: Configurar uma interface do Usuário alojado para pesquisa personalizada do Bing | Documentos da Microsoft
titlesuffix: Azure Cognitive Services
description: Utilize este artigo para configurar e integrar uma interface do Usuário alojado para pesquisa personalizada do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: af1e65cc7dfe1a0934056ad141f4c62a96627bbb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58084137"
---
# <a name="configure-your-hosted-ui-experience"></a>Configurar a sua experiência de interface do Usuário alojada

Pesquisa personalizada do Bing fornece uma interface do Usuário hospedado que podem integrar facilmente em seus aplicativos web e de páginas da Web como um trecho de código JavaScript. Utilizar o portal de pesquisa personalizada do Bing, pode configurar o esquema, a cor e opções de pesquisa da interface do Usuário.



## <a name="configure-the-custom-hosted-ui"></a>Configurar a interface do Usuário personalizada alojada

Para configurar uma interface do Usuário alojado para seus aplicativos web, siga estes passos. Como efetuar alterações, o painel no lado direito irá dar-lhe uma pré-visualização da sua interface do Usuário. Os resultados da pesquisa exibidos não são os resultados reais da sua instância.

1. Entrar para a pesquisa personalizada do Bing [portal](https://customsearch.ai).  
  
2. Selecione a sua instância de pesquisa personalizada do Bing.

3. Clique no separador **IU Alojada**.  
  
4. Selecione um esquema.

    |  |  |
    |---------|---------|
    |Barra de pesquisa e os resultados (predefinição)    | Apresenta uma caixa de pesquisa com os resultados de pesquisa abaixo dele.         |
    |Apenas os resultados     | Apresenta apenas os resultados da procura sem uma caixa de pesquisa. Ao usar esse layout, tem de fornecer a consulta de pesquisa (`&q=<query string>`). Adicione o parâmetro de consulta para o URL de pedido no fragmento do JavaScript ou a ligação de ponto final HTML.        |
    |Ativação pós-falha pop     | Fornece uma caixa de pesquisa e exibe os resultados da pesquisa numa sobreposição deslizante.        |
    
5. Selecione um tema de cores. Pode personalizar as cores de acordo com seu aplicativo clicando **personalizar tema**. Para alterar uma cor, introduza um valor da cor RGB HEXADECIMAL (por exemplo, `#366eb8`), ou clique na pré-visualização de cor.

   Pode visualizar as alterações no lado direito do portal. Clicar **repor para predefinição** irá reverter as alterações às cores predefinidas para o tema que selecionou.

   > [!NOTE]
   > Ao escolher cores, considere acessibilidade.

6. Sob **configurações adicionais**, forneça os valores conforme adequado para a sua aplicação. Estas definições são opcionais. Para ver o efeito de aplicar ou removê-los, consulte o painel de visualização no lado direito. Opções de configuração disponíveis são:  

7. Introduza a chave de subscrição de pesquisa ou escolha um na lista pendente. A lista pendente é preenchida com chaves de subscrições da sua conta do Azure. Ver [conta de API dos serviços cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Se ativou a sugestão automática, introduza a chave de subscrição de sugestão automática ou escolha um na lista pendente. A lista pendente é preenchida com chaves de subscrições da sua conta do Azure. Sugestão automática personalizada requer um escalão de subscrição específica, consulte a [preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Consumir a interface do Usuário personalizada

Para consumir a interface do Usuário alojado, optar por: 

- Inclua o script na página da web  
  
  ```html
  <html>
      <body>
          <script type="text/javascript" 
              id="bcs_js_snippet"
              src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
          </script>
      </body>    
  </html>
  ```

- Em alternativa, utilize o seguinte URL num browser.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Adicione os seguintes parâmetros de consulta para o URL conforme necessário. Para obter informações sobre estes parâmetros, consulte [a API de pesquisa personalizada](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) referência.
  >
  > - p
  > - mkt
  > - pesquisa segura
  > - setlang

  > [!IMPORTANT]
  > A página não é possível apresentar a sua declaração de privacidade ou outros avisos e os termos. Pode variar de adequação para a sua utilização.  

Para obter mais informações, incluindo o seu ID de configuração personalizada, aceda a **pontos de extremidade** sob a **produção** separador.

## <a name="configuration-options"></a>Opções de configuração

Pode configurar o comportamento da sua interface do Usuário alojada clicando **configurações adicionais**e o fornecimento de valores. Estas definições são opcionais. Para ver o efeito de aplicar ou removê-los, consulte o painel de visualização no lado direito. 

### <a name="web-search-configurations"></a>Configurações de pesquisa Web

|  |  |
|---------|---------|
|Resultados da Web habilitados    | Determina se a pesquisa na web está ativada (verá um separador de Web na parte superior da página)        |
|Ativar sugestão automática     | Determina se sugestão automática personalizada está ativada (consulte [preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) custo adicional).        |
|Resultados da Web por página    | Número de resultados de pesquisa web a apresentar ao mesmo tempo (o máximo é de 50 resultados por página).        |
|Legenda da imagem   | Determina se as imagens são apresentadas com os resultados da pesquisa.|


As seguintes configurações são apresentadas se clicar **Show configurações avançadas**:


|  | |
|---------|---------|
|Realçar palavras     | Determina se os resultados são apresentados com os termos de pesquisa em negrito.         |
|Destino de ligação    |  Determina se a página Web abre num novo separador do browser (em branco) ou o mesmo separador do browser (auto) quando o usuário clica num resultado de pesquisa.        |

### <a name="image-search-configurations"></a>Configurações de pesquisa de imagem

| | |
|---------|---------|
|Resultados da imagem ativada     | Determina se a pesquisa de imagens está ativada (verá um separador de imagens na parte superior da página).            |
|Resultados de imagem por página     | Número de resultados de pesquisa de imagem a apresentar ao mesmo tempo (o máximo é 150 resultados por página).          |

A seguinte configuração é apresentada se clicar **Show configurações avançadas**.  
  
| | |
|---------|---------|
| Ativar filtros     | Adiciona os filtros que o utilizador pode utilizar para filtrar as imagens que o Bing devolve. Por exemplo, o utilizador pode filtrar os resultados para apenas os GIF animados.|

### <a name="video-search-configurations"></a>Configurações de pesquisa de vídeos

|  | |
|---------|---------|
|Resultados de vídeo ativados     | Determina se a pesquisa de vídeos está ativada (verá um separador de vídeos na parte superior da página).           |
|Resultados de vídeo por página   | Número de resultados de pesquisa de vídeos para apresentar ao mesmo tempo (o máximo é 150 resultados por página).        |

A seguinte configuração é apresentada se clicar **Show configurações avançadas**.  
  
|  | |
|---------|---------|
|Ativar filtros    | Adiciona os filtros que o utilizador pode utilizar para filtrar os vídeos que o Bing devolve. Por exemplo, o utilizador pode filtrar os resultados para vídeos com uma resolução específico ou vídeos detetados nas últimas 24 horas.          |

### <a name="miscellaneous-configurations"></a>Diversas configurações


| |  |
|---------|---------|
|Título da página   | Texto apresentado na área de título da página de resultados de pesquisa (não para a ativação pós-falha pop layout).        |
|Tema de barra de ferramentas    | Determina a cor de fundo da área do título da página de resultados de pesquisa. |

As seguintes configurações são apresentadas se clicar **Show configurações avançadas**.  

|Column1  |Column2  |
|---------|---------|
|Marcador de posição de texto de caixa de pesquisa   | Texto exibido na antes de caixa de pesquisa de entrada.        |
|Url de ligação do título    |Destino da ligação do título.         |
|URL de logótipo     | Imagem exibida ao lado do título.         |
|Favicon    | Ícone apresentado na barra de título do navegador.          |

As seguintes configurações aplicam-se apenas se consumir a interface do Usuário alojadas através do ponto final HTML (eles não se aplicam se utilizar o fragmento do JavaScript).

- Título da página
- Tema de barra de ferramentas
- URL de ligação do título
- URL de logótipo
- Faviicon URL  

## <a name="next-steps"></a>Passos Seguintes

- [Utilizar marcadores decorativos para realçar o texto](./hit-highlighting.md)
- [Paginação de páginas Web](./page-webpages.md)
