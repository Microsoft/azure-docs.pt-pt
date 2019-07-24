---
title: Configurar uma interface do usuário hospedada para Pesquisa Personalizada do Bing | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Use este artigo para configurar e integrar uma interface do usuário hospedada para Pesquisa Personalizada do Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: aahi
ms.openlocfilehash: ae073e10331f07d9863da1d4ed97533f95b87c86
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405056"
---
# <a name="configure-your-hosted-ui-experience"></a>Configurar sua experiência de interface do usuário hospedada

O Pesquisa Personalizada do Bing fornece uma interface do usuário hospedada que você pode integrar facilmente às suas páginas da Web e aos aplicativos como um trecho de código JavaScript. Usando o portal de Pesquisa Personalizada do Bing, você pode configurar as opções de layout, cor e pesquisa da interface do usuário.



## <a name="configure-the-custom-hosted-ui"></a>Configurar a interface do usuário hospedada personalizada

Para configurar uma interface do usuário hospedada para seus aplicativos Web, siga estas etapas. Conforme você faz alterações, o painel à direita fornece uma visualização de sua interface do usuário. Os resultados da pesquisa exibidos não são resultados reais para sua instância.

1. Entre no [portal](https://customsearch.ai)do pesquisa personalizada do Bing.  
  
2. Selecione sua instância de Pesquisa Personalizada do Bing.

3. Clique no separador **IU Alojada**.  
  
4. Selecione um esquema.

    |  |  |
    |---------|---------|
    |Barra de pesquisa e resultados (padrão)    | Exibe uma caixa de pesquisa com os resultados da pesquisa abaixo dele.         |
    |Somente resultados     | Exibe somente os resultados da pesquisa, sem uma caixa de pesquisa. Ao usar esse layout, você deve fornecer a consulta de pesquisa`&q=<query string>`(). Adicione o parâmetro de consulta à URL de solicitação no trecho de código JavaScript ou ao link de ponto de extremidade HTML.        |
    |Pop-over     | Fornece uma caixa de pesquisa e exibe os resultados da pesquisa em uma sobreposição deslizante.        |
    
5. Selecione um tema de cores. Você pode personalizar as cores para se ajustar ao seu aplicativo clicando em **Personalizar tema**. Para alterar uma cor, insira o valor hexadecimal RGB da cor (por exemplo, `#366eb8`) ou clique na visualização de cor.

   Você pode visualizar as alterações no lado direito do Portal. Clicar em **Redefinir para padrão** reverterá suas alterações para as cores padrão do tema selecionado.

   > [!NOTE]
   > Considere a acessibilidade ao escolher cores.

6. Em **configurações adicionais**, forneça valores conforme apropriado para seu aplicativo. Essas configurações são opcionais. Para ver o efeito de aplicá-los ou removê-los, consulte o painel de visualização à direita. As opções de configuração disponíveis são:  

7. Insira a chave de assinatura de pesquisa ou escolha uma na lista suspensa. A lista suspensa é preenchida com chaves das assinaturas da sua conta do Azure. Consulte [conta de API de serviços cognitivas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).  

8. Se você habilitou a sugestão automática, insira a chave de assinatura de sugestão automática ou escolha uma na lista suspensa. A lista suspensa é preenchida com chaves das assinaturas da sua conta do Azure. A sugestão automática personalizada requer uma camada de assinatura específica, consulte o [preço](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/).

[!INCLUDE [publish or revert](./includes/publish-revert.md)]

## <a name="consume-custom-ui"></a>Consumir interface do usuário personalizada

Para consumir a interface do usuário hospedada, seja: 

- Incluir o script em sua página da Web  
  
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

- Ou use a URL a seguir em um navegador da Web.   
  
  `https://ui.customsearch.ai/hosted?customConfig=YOUR-CUSTOM-CONFIG-ID`  
  
  > [!NOTE]
  > Adicione os seguintes parâmetros de consulta à URL, conforme necessário. Para obter informações sobre esses parâmetros, consulte referência de [API de pesquisa personalizada](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) .
  >
  > - p
  > - mkt
  > - pesquisa segura
  > - setlang

  > [!IMPORTANT]
  > A página não pode exibir sua política de privacidade ou outros avisos e termos. A adequação para seu uso pode variar.  

Para obter informações adicionais, incluindo sua ID de configuração personalizada, vá para **pontos de extremidade** na guia **produção** .

## <a name="configuration-options"></a>Opções de configuração

Você pode configurar o comportamento da interface do usuário hospedada clicando em **configurações adicionais**e fornecendo valores. Essas configurações são opcionais. Para ver o efeito de aplicá-los ou removê-los, consulte o painel de visualização à direita. 

### <a name="web-search-configurations"></a>Configurações de pesquisa na Web

|  |  |
|---------|---------|
|Resultados da Web habilitados    | Determina se a pesquisa da Web está habilitada (você verá uma guia da Web na parte superior da página)        |
|Habilitar sugestão automática     | Determina se a sugestão automática personalizada está habilitada (consulte os [preços](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) para obter custos adicionais).        |
|Resultados da Web por página    | Número de resultados da pesquisa na Web a serem exibidos de cada vez (o máximo é 50 resultados por página).        |
|Legenda da imagem   | Determina se as imagens são exibidas com os resultados da pesquisa.|


As configurações a seguir serão exibidas se você clicar em **Mostrar configurações avançadas**:


|  | |
|---------|---------|
|Realçar palavras     | Determina se os resultados são exibidos com os termos de pesquisa em negrito.         |
|Destino do link    |  Determina se a página da Web é aberta em uma nova guia do navegador (em branco) ou na mesma guia do navegador (própria) quando o usuário clica em um resultado da pesquisa.        |

### <a name="image-search-configurations"></a>Configurações de pesquisa de imagem

| | |
|---------|---------|
|Resultados da imagem habilitados     | Determina se a pesquisa de imagem está habilitada (você verá uma guia imagens na parte superior da página).            |
|Resultados da imagem por página     | Número de resultados da pesquisa de imagem a serem exibidos de cada vez (o máximo é 150 resultados por página).          |

A configuração a seguir será mostrada se você clicar em **Mostrar configurações avançadas**.  
  
| | |
|---------|---------|
| Habilitar filtros     | Adiciona filtros que o usuário pode usar para filtrar as imagens que o Bing retorna. Por exemplo, o usuário pode filtrar os resultados para apenas GIFs animados.|

### <a name="video-search-configurations"></a>Configurações de pesquisa de vídeo

|  | |
|---------|---------|
|Resultados de vídeo habilitados     | Determina se a pesquisa de vídeo está habilitada (você verá uma guia vídeos na parte superior da página).           |
|Resultados de vídeo por página   | Número de resultados da pesquisa de vídeo a serem exibidos de cada vez (o máximo é 150 resultados por página).        |

A configuração a seguir será mostrada se você clicar em **Mostrar configurações avançadas**.  
  
|  | |
|---------|---------|
|Habilitar filtros    | Adiciona filtros que o usuário pode usar para filtrar os vídeos que o Bing retorna. Por exemplo, o usuário pode filtrar os resultados para vídeos com uma resolução ou vídeos específicos descobertos nas últimas 24 horas.          |

### <a name="miscellaneous-configurations"></a>Configurações diversas


| |  |
|---------|---------|
|Título da página   | Texto exibido na área de título da página de resultados da pesquisa (não para layout pop-over).        |
|Tema da barra de ferramentas    | Determina a cor do plano de fundo da área de título da página de resultados da pesquisa. |

As configurações a seguir serão mostradas se você clicar em **Mostrar configurações avançadas**.  

|Column1  |Column2  |
|---------|---------|
|Espaço reservado para texto da caixa de pesquisa   | Texto exibido na caixa de pesquisa antes da entrada.        |
|URL do link do título    |Destino do link de título.         |
|URL do logotipo     | Imagem exibida ao lado do título.         |
|Favicon    | Ícone exibido na barra de título do navegador.          |

As configurações a seguir se aplicam somente se você consumir a interface do usuário hospedada por meio do ponto de extremidade HTML (elas não se aplicam se você usar o trecho de JavaScript).

- Título da página
- Tema da barra de ferramentas
- URL do link do título
- URL do logotipo
- URL do Faviicon  

## <a name="next-steps"></a>Passos Seguintes

- [Utilizar marcadores decorativos para realçar o texto](./hit-highlighting.md)
- [Paginação de páginas Web](./page-webpages.md)
