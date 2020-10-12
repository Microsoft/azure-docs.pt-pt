---
title: Configure um UI hospedado para bing custom search / Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Utilize este artigo para configurar e integrar um UI hospedado para pesquisa personalizada de Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: 4a63a54f5ad4d1bcccecb1d1a4892ef55c238b92
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91319185"
---
# <a name="configure-your-hosted-ui-experience"></a>Configurar a sua experiência de IU alojada

Bing Custom Search fornece um UI hospedado que você pode facilmente integrar nas suas páginas web e aplicações web como um snippet de código JavaScript. Utilizando o portal Bing Custom Search, pode configurar as opções de layout, cor e pesquisa da UI.



## <a name="configure-the-custom-hosted-ui"></a>Configure a UI personalizada

Para configurar um UI hospedado para as suas aplicações web, siga estes passos. Ao fazer alterações, o painel à direita dá-lhe uma prévia da sua UI. Os resultados de pesquisa apresentados não são resultados reais para o seu exemplo.

1. Inscreva-se no [portal](https://customsearch.ai)Bing Custom Search .  
  
2. Selecione a sua instância de Pesquisa Personalizada Bing.

3. Clique no separador **IU Alojada**.  
  
4. Selecione um esquema.

    - Barra de pesquisa e resultados (padrão): Exibe uma caixa de pesquisa com resultados de pesquisa abaixo.
    - Apenas resultados: Exibe apenas resultados de pesquisa, sem uma caixa de pesquisa. Ao utilizar este layout, deve fornecer a consulta de pesquisa `&q=<query string>` (). Adicione o parâmetro de consulta ao URL de pedido no snippet JavaScript ou na ligação de ponto final HTML.
    - Pop-over: Fornece uma caixa de pesquisa e exibe os resultados da pesquisa numa sobreposição deslizante.

5. Selecione um tema de cores. Pode personalizar as cores para se adaptar à sua aplicação clicando no **tema Personalizar**. Para alterar uma cor, introduza o valor RGB HEX da cor (por exemplo, `#366eb8` ), ou clique na pré-visualização da cor.

   Pode visualizar as alterações no lado direito do portal. Clicar **em Reset para o predefinido** reverterá as suas alterações às cores predefinidas para o tema selecionado.

   > [!NOTE]
   > Considere a acessibilidade na escolha de cores.

6. Em **Configurações Adicionais,** forneça valores adequados para a sua aplicação. Estas definições são opcionais. Para ver o efeito de aplicá-los ou removê-los, consulte o painel de pré-visualização à direita. As opções de configuração disponíveis são:  

7. Introduza a tecla de subscrição de pesquisa ou escolha uma na lista de dropdown. A lista de dropdown é preenchida com chaves das subscrições da sua conta Azure. Consulte a [conta API dos Serviços Cognitivos.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)  

8. Se ativar o autosuggest, introduza a chave de subscrição autosusou ou escolha uma da lista de dropdown. A lista de dropdown é preenchida com chaves das subscrições da sua conta Azure. Custom Autosuggest requer um nível de subscrição específico, ver o [preço](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

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
  > Adicione os seguintes parâmetros de consulta ao URL, se necessário. Para obter informações sobre estes parâmetros, consulte a referência [API de pesquisa personalizada.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters)
  >
  > - q
  > - mkt
  > - safesearch
  > - setlang

  > [!IMPORTANT]
  > A página não pode exibir a sua declaração de privacidade ou outros avisos e termos. A adequação para a sua utilização pode variar.  

Para obter informações adicionais, incluindo o seu ID de Configuração Personalizada, vá a **Pontos Finais** no **separador Produção.**

## <a name="configuration-options"></a>Opções de configuração

Pode configurar o comportamento da UI hospedada clicando em **Configurações Adicionais**e fornecendo valores. Estas definições são opcionais. Para ver o efeito de aplicá-los ou removê-los, consulte o painel de pré-visualização à direita. 

### <a name="web-search-configurations"></a>Configurações de pesquisa web

- Resultados da Web ativados: Determina se a pesquisa na Web está ativada (verá um separador Web no topo da página)
- Ativar o autosuggest: Determine se o autosuggest personalizado está ativado (ver [preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) para custos adicionais).
- Resultados da Web por página: Número de resultados de pesquisa web a exibir de cada vez (o máximo é de 50 resultados por página).
- Legenda da imagem: Determina se as imagens são exibidas com resultados de pesquisa.

As seguintes configurações são mostradas se clicar **em Mostrar configurações avançadas**:

- Realce as palavras: Determina se os resultados são apresentados com termos de pesquisa em negrito.
- Link target: Determine se a página web abre em um novo separador de navegador (Blank) ou no mesmo separador de navegador (self) quando o utilizador clica num resultado de pesquisa.

### <a name="image-search-configurations"></a>Configurações de pesquisa de imagem

- Resultados da imagem ativados: Determina se a procura de imagem está ativada (verá um separador Imagens no topo da página).
- Resultados da imagem por página: Número de resultados de pesquisa de imagem a exibir de cada vez (o máximo é de 150 resultados por página).

A configuração a seguir é mostrada se clicar **em Mostrar configurações avançadas**.  
  
- Ativar filtros: Adiciona filtros que o utilizador pode utilizar para filtrar as imagens que o Bing devolve. Por exemplo, o utilizador pode filtrar os resultados apenas para GIFs animados.

### <a name="video-search-configurations"></a>Configurações de pesquisa de vídeo

- Resultados de vídeo ativados: Determina se a procura de vídeo está ativada (verá um separador Vídeos no topo da página).
- Resultados do vídeo por página: Número de resultados de pesquisa de vídeo a visualizar de cada vez (o máximo é de 150 resultados por página).

A configuração a seguir é mostrada se clicar **em Mostrar configurações avançadas**.  
  
- Ativar filtros: Adiciona filtros que o utilizador pode utilizar para filtrar os vídeos que o Bing devolve. Por exemplo, o utilizador pode filtrar os resultados de vídeos com uma resolução ou vídeos específicos descobertos nas últimas 24 horas.

### <a name="miscellaneous-configurations"></a>Configurações diversas

- Título da página: Texto apresentado na área do título da página de resultados de pesquisa (não para layout pop-over).
- Tema da barra de ferramentas: Determina a cor de fundo da área do título da página de resultados da pesquisa.

As seguintes configurações são mostradas se clicar **em Mostrar configurações avançadas**.  

|Coluna1  |Coluna2  |
|---------|---------|
|Espaço reservado de caixa de pesquisa   | Texto apresentado na caixa de pesquisa antes da entrada.        |
|Url de ligação de título    |Alvo para a ligação do título.         |
|URL de logotipo     | Imagem exibida ao lado do título.         |
|Rio Favicon    | Ícone exibido na barra de título do navegador.          |

As seguintes configurações aplicam-se apenas se consumir o UI hospedeiro através do ponto final HTML (não se aplicam se utilizar o snippet JavaScript).

- Título da página
- Tema da barra de ferramentas
- URL de ligação de títulos
- URL de logotipo
- Faviicon URL  

## <a name="next-steps"></a>Passos seguintes

- [Utilizar marcadores decorativos para realçar o texto](../bing-web-search/hit-highlighting.md)
- [Paginação de páginas Web](./page-webpages.md)
