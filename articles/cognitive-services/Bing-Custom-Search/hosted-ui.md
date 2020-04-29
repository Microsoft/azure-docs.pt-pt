---
title: Configure um UI hospedado para bing custom search [ Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para configurar e integrar um UI hospedado para bing custom search.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: 2cc89bf57167db75404c044f58d18ab48edfaf38
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68854074"
---
# <a name="configure-your-hosted-ui-experience"></a>Configurar a sua experiência de IU alojada

Bing Custom Search fornece um UI hospedado que você pode facilmente integrar nas suas páginas web e aplicações web como um snippet de código JavaScript. Utilizando o portal bing Custom Search, pode configurar as opções de layout, cor e pesquisa da UI.



## <a name="configure-the-custom-hosted-ui"></a>Configure a UI personalizada hospedada

Para configurar um UI hospedado para as suas aplicações web, siga estes passos. À medida que faz espetar, o painel à direita lhe dará uma pré-visualização do seu UI. Os resultados de pesquisa apresentados não são resultados reais para a sua instância.

1. Inscreva-se no [portal](https://customsearch.ai)de pesquisa personalizada Bing .  
  
2. Selecione a sua instância de pesquisa personalizada Bing.

3. Clique no separador **IU Alojada**.  
  
4. Selecione um esquema.

    |  |  |
    |---------|---------|
    |Barra de pesquisa e resultados (padrão)    | Exibe uma caixa de pesquisa com resultados de pesquisa abaixo.         |
    |Apenas resultados     | Exibe apenas resultados de pesquisa, sem uma caixa de pesquisa. Ao utilizar este layout, deve fornecer`&q=<query string>`a consulta de pesquisa ( ). Adicione o parâmetro de consulta ao URL de pedido no corte JavaScript ou na ligação final HTML.        |
    |Pop-over     | Fornece uma caixa de pesquisa e exibe os resultados da pesquisa numa sobreposição deslizante.        |
    
5. Selecione um tema de cores. Pode personalizar as cores para se adaptar à sua aplicação clicando no **tema Personalizar**. Para alterar uma cor, introduza o valor RGB HEX da cor (por exemplo, `#366eb8`ou clique na pré-visualização da cor.

   Pode visualizar as suas alterações no lado direito do portal. Clicar **em Redefinir para padrão** reverterá as suas alterações para as cores padrão para o tema selecionado.

   > [!NOTE]
   > Considere a acessibilidade na escolha das cores.

6. Em **Configurações Adicionais,** forneça valores conforme apropriado para a sua aplicação. Estas configurações são opcionais. Para ver o efeito de aplicá-los ou removê-los, consulte o painel de pré-visualização à direita. As opções de configuração disponíveis são:  

7. Introduza a chave de subscrição de pesquisa ou escolha uma da lista de dropdown. A lista de dropdown é preenchida com chaves das assinaturas da sua conta Azure. Consulte [a conta API dos Serviços Cognitivos](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Se ativar automaticamente, introduza a tecla de subscrição de sugestão automática ou escolha uma da lista de dropdown. A lista de dropdown é preenchida com chaves das assinaturas da sua conta Azure. A Custom Autosuggest requer um nível de subscrição específico, consulte os [preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Consumir UI personalizado

Para consumir a UI hospedada, também: 

- Inclua o script na sua página web  
  
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

- Ou, use o seguinte URL num navegador Web.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Adicione os seguintes parâmetros de consulta ao URL, conforme necessário. Para obter informações sobre estes parâmetros, consulte a referência da API de [Pesquisa Personalizada.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)
  >
  > - q
  > - mkt
  > - busca segura
  > - setlang

  > [!IMPORTANT]
  > A página não pode apresentar a sua declaração de privacidade ou outros avisos e termos. A adequação à sua utilização pode variar.  

Para obter informações adicionais, incluindo o seu ID de Configuração Personalizada, vá a **Pontos Finais** sob o separador **Produção.**

## <a name="configuration-options"></a>Opções de configuração

Pode configurar o comportamento do seu UI hospedado clicando em **Configurações Adicionais**e fornecendo valores. Estas configurações são opcionais. Para ver o efeito de aplicá-los ou removê-los, consulte o painel de pré-visualização à direita. 

### <a name="web-search-configurations"></a>Configurações de pesquisa web

|  |  |
|---------|---------|
|Resultados web ativados    | Determina se a pesquisa na Web está ativada (verá um separador Web no topo da página)        |
|Ativar autosugerir     | Determina se o auto-sugerido personalizado está ativado (ver [preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) para custos adicionais).        |
|Resultados da web por página    | Número de resultados de pesquisa web para visualizar de cada vez (o máximo é de 50 resultados por página).        |
|Legenda da imagem   | Determina se as imagens são exibidas com resultados de pesquisa.|


As seguintes configurações são mostradas se clicar em **Mostrar configurações avançadas:**


|  | |
|---------|---------|
|Destacar palavras     | Determina se os resultados são apresentados com termos de pesquisa em negrito.         |
|Alvo de ligação    |  Determina se a página web abre num novo separador de navegador (Blank) ou no mesmo separador de navegador (auto) quando o utilizador clica num resultado de pesquisa.        |

### <a name="image-search-configurations"></a>Configurações de pesquisa de imagem

| | |
|---------|---------|
|Resultados de imagem ativados     | Determina se a pesquisa de imagem está ativada (verá um separador Imagens na parte superior da página).            |
|Resultados da imagem por página     | Número de resultados de pesquisa de imagem para visualizar de cada vez (o máximo é de 150 resultados por página).          |

A seguinte configuração é mostrada se clicar em **Mostrar configurações avançadas**.  
  
| | |
|---------|---------|
| Ativar filtros     | Adiciona filtros que o utilizador pode usar para filtrar as imagens que bing retorna. Por exemplo, o utilizador pode filtrar os resultados apenas para GIFs animados.|

### <a name="video-search-configurations"></a>Configurações de pesquisa de vídeo

|  | |
|---------|---------|
|Resultados de vídeo ativados     | Determina se a pesquisa de vídeo está ativada (verá um separador De Vídeos na parte superior da página).           |
|Resultados de vídeo por página   | Número de resultados de pesquisa de vídeo para exibir de cada vez (o máximo é de 150 resultados por página).        |

A seguinte configuração é mostrada se clicar em **Mostrar configurações avançadas**.  
  
|  | |
|---------|---------|
|Ativar filtros    | Adiciona filtros que o utilizador pode usar para filtrar os vídeos que bing retorna. Por exemplo, o utilizador pode filtrar os resultados de vídeos com uma resolução específica ou vídeos descobertos nas últimas 24 horas.          |

### <a name="miscellaneous-configurations"></a>Configurações diversas


| |  |
|---------|---------|
|Título da página   | Texto apresentado na área do título da página de resultados de pesquisa (não para layout pop-over).        |
|Tema da barra de ferramentas    | Determina a cor de fundo da área do título da página de resultados da pesquisa. |

As seguintes configurações são mostradas se clicar em **Mostrar configurações avançadas**.  

|Coluna1  |Coluna2  |
|---------|---------|
|Espaço reservado para texto da caixa de pesquisa   | Texto apresentado na caixa de pesquisa antes da entrada.        |
|Url de ligação de título    |Alvo para a ligação do título.         |
|URL do logotipo     | Imagem exibida ao lado do título.         |
|Favicon    | Ícone exibido na barra de título do navegador.          |

As seguintes configurações só se aplicam se consumir o UI hospedeiro através do ponto final HTML (não se aplicam se utilizar o snippet JavaScript).

- Título da página
- Tema da barra de ferramentas
- URL de ligação de título
- URL do logotipo
- Faviicon URL  

## <a name="next-steps"></a>Passos seguintes

- [Utilizar marcadores decorativos para realçar o texto](../bing-web-search/hit-highlighting.md)
- [Paginação de páginas Web](./page-webpages.md)
