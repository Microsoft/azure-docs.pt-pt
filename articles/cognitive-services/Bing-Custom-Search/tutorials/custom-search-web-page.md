---
title: 'Tutorial: Criar uma página da web de pesquisa personalizada - pesquisa personalizada do Bing'
titlesuffix: Azure Cognitive Services
description: Descreve como configurar uma instância de pesquisa personalizada e integrá-la numa página Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: aahi
ms.openlocfilehash: 48486153c805f4fe7bd21d3f63346f56522220f9
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57337639"
---
# <a name="tutorial-build-a-custom-search-web-page"></a>Tutorial: Criar uma página Web de Pesquisa Personalizada

Saiba como a Pesquisa Personalizada do Bing lhe permite criar experiências de pesquisa personalizada dos tópicos que mais lhe interessam. Por exemplo, se for proprietário de um site de artes marciais que fornece uma experiência de pesquisa, pode-se especificar os domínios, subsites e páginas Web que o Bing pesquisa. Os seus utilizadores veem resultados de pesquisa personalizados de acordo com os conteúdos que lhes interessam, em vez de terem de percorrer resultados de pesquisa gerais que podem apresentar conteúdos irrelevantes. 

Este tutorial demonstra como configurar uma instância de pesquisa personalizada e integrá-la numa nova página Web.

As tarefas abrangidas são:

> [!div class="checklist"]
> - Criar uma instância de pesquisa personalizada
> - Adicionar entradas ativas
> - Adicionar entradas bloqueadas
> - Adicionar entradas afixadas
> - Integrar a pesquisa personalizada numa página Web

## <a name="prerequisites"></a>Pré-requisitos

- Para acompanhar o tutorial, necessita de uma chave de subscrição para a API de Pesquisa Personalizada do Bing.  Para obter uma chave, veja [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).
- Se ainda não tiver o Visual Studio 2017 instalado, pode transferir e utilizar a [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) **gratuita**.

## <a name="create-a-custom-search-instance"></a>Criar uma instância de pesquisa personalizada

Para criar uma instância de Pesquisa Personalizada do Bing:

1. Abra um browser.  
  
2. Navegue para o [portal](https://customsearch.ai) da pesquisa personalizada.  
  
3. Inicie sessão no portal com uma conta Microsoft (MSA). Se não tiver uma MSA, clique em **Criar uma conta Microsoft**. Se estiver a utilizar o portal pela primeira vez, é-lhe pedida permissão para aceder aos dados. Clique em **Sim**.  
  
4. Depois de iniciar sessão, clique em **Nova pesquisa personalizada**. Na janela **Criar uma nova instância de pesquisa personalizada**, introduza um nome significativo e que descreva o tipo de conteúdo devolvido pela pesquisa. Pode alterar o nome em qualquer altura.  
  
  ![Captura de ecrã da caixa Criar uma nova instância de pesquisa personalizada](../media/newCustomSrch.png)  
  
5. Clique em OK, especifique um URL e se quer incluir subpáginas do URL.  
  
  ![Captura de ecrã da página de definições do URL](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Adicionar entradas ativas

Para incluir os resultados de sites ou URLs específicos, adicione-os ao separador **Ativo**.

1.  Na página **Configuração**, clique no separador **Ativo** e introduza o URL de um ou mais sites que queira incluir na sua pesquisa.

    ![Captura de ecrã do separador Ativo do Editor de Definições](../media/customSrchEditor.png)

2.  Para confirmar que a sua instância devolve resultados, introduza uma consulta no painel de pré-visualização no lado direito. O Bing só devolve resultados para sites públicos que lhe estejam indexados.

## <a name="add-blocked-entries"></a>Adicionar entradas bloqueadas

Para excluir os resultados de sites ou URLs específicos, adicione-os ao separador **Bloqueado**.

1. Na página **Configuração**, clique no separador **Bloqueado** e introduza o URL de um ou mais sites que queira excluir da sua pesquisa.

    ![Captura de ecrã do separador Bloqueado do Editor de Definições](../media/blockedCustomSrch.png)


2. Para confirmar que a sua instância não devolve resultados de sites bloqueados, introduza uma consulta no painel de pré-visualização no lado direito. 

## <a name="add-pinned-entries"></a>Adicionar entradas afixadas

Para afixar uma página Web específica à parte superior dos resultados da pesquisa, adicione o termo de página Web e consulta ao separador **Afixado**. O separador **Afixado** contém uma lista de pares de termos de página Web e consulta que especificam a página Web apresentada como o melhor resultado de uma consulta específica. A página Web só é afixada se a cadeia de consulta do utilizador corresponder à cadeia de consulta afixada com base na condição de correspondência. Apenas as páginas Web indexadas serão apresentadas nas pesquisas. Veja [Definir a sua vista personalizada](../define-your-custom-view.md#pin-slices-to-the-top-of-search-results) para obter mais informações.

1. Na página **Configuração**, clique no separador **Afixado** e introduza o termo de página Web e consulta da página Web a devolver como o melhor resultado.  
  
2. Por predefinição, a cadeia de consulta do utilizador tem de corresponder exatamente à cadeia de consulta afixada para o Bing devolver a página Web como o melhor resultado. Para alterar a condição de correspondência, clique no ícone de lápis, clique em Exato na coluna **Condição de correspondência da consulta** e selecione a condição de correspondência mais adequada para a sua aplicação.  
  
    ![Captura de ecrã do separador Afixado do Editor de Definições](../media/pinnedCustomSrch.png)
  
3. Para confirmar que a sua instância devolve a página Web especificada como o melhor resultado, introduza o termo de consulta afixado no painel de pré-visualização no lado direito.

## <a name="configure-hosted-ui"></a>Configurar a IU Alojada

A Pesquisa Personalizada fornece uma IU alojada para compor a resposta JSON da sua instância de pesquisa personalizada. Para definir a experiência de IU:

1. Clique no separador **IU Alojada**.  
  
2. Selecione um esquema.  
  
  ![Captura de ecrã do passo de seleção de esquema da IU Alojada](./media/custom-search-hosted-ui-select-layout.png)  
  
3. Selecione um tema de cores.  
  
  ![Captura de ecrã da seleção do tema de cores da IU Alojada](./media/custom-search-hosted-ui-select-color-theme.png)  

  Se precisar de ajustar o tema de cores para uma melhor integração na sua aplicação Web, clique em **Personalizar tema**. Nem todas as configurações de cores se aplicam a todos os temas de esquema. Para alterar uma cor, introduza o valor HEX de RGB (por exemplo, #366eb8) na caixa de texto correspondente. Em alternativa, clique no botão de cor e, em seguida, clique no tom mais adequado para si. Pense sempre na acessibilidade quando selecionar cores.
  
  ![Captura de ecrã da personalização do tema de cores da IU Alojada](./media/custom-search-hosted-ui-customize-color-theme.png)  

  
4. Especifique opções de configuração adicionais.  
  
  ![Captura de ecrã do passo de configurações adicionais da IU Alojada](./media/custom-search-hosted-ui-additional-configurations.png)  
  
  Para obter configurações avançadas, clique em **Mostrar configurações avançadas**. Isto permite adicionar configurações como *Destino de ligação* às opções de pesquisa Web, *Ativar filtros* às opções de Imagem e Vídeo, e *Marcador de posição de texto da caixa de pesquisa* a opções Diversas.

  ![Captura de ecrã do passo de configurações avançadas da IU Alojada](./media/custom-search-hosted-ui-advanced-configurations.png)  
  
5. Selecione as chaves de subscrição nas listas pendentes. Em alternativa, pode introduzir a chave de subscrição manualmente. Para obter informações sobre como obter chaves, veja [Experimentar os Serviços Cognitivos](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search-api).  
  
  ![Captura de ecrã do passo de configurações adicionais da IU Alojada](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Consumir a IU Alojada

Existem duas formas de consumir a IU alojada.  

- Opção 1: Integre o fragmento do JavaScript fornecido na sua aplicação.
- Opção 2: Utilize o ponto de final do HTML fornecida.

O resto deste tutorial ilustra **opção 1: Fragmento do JavaScript**.  

## <a name="set-up-your-visual-studio-solution"></a>Configurar a sua solução do Visual Studio

1. Abra o **Visual Studio** no seu computador.  
  
2. No menu **Ficheiro**, selecione **Novo**, e, em seguida, escolha **Projeto**.  
  
3. Na janela **Novo Projeto**, selecione **Aplicação Web Visual C#/Web/ASP.NET Core**, dê um nome ao projeto e, em seguida, clique em **OK**.  
  
  ![Captura de ecrã da janela Novo Projeto](./media/custom-search-new-project.png)  
  
4. Na janela **Nova Aplicação Web do ASP.NET Core**, selecione **Aplicação Web** e clique em **OK**.  
  
  ![Captura de ecrã da janela Novo Projeto](./media/custom-search-new-webapp.png)  

## <a name="edit-indexcshtml"></a>Editar o ficheiro index.cshtml

1. No **Explorador de Soluções**, expanda **Páginas** e faça duplo clique em **index.cshtml** para abrir o ficheiro.  
  
  ![Captura de ecrã do Explorador de Soluções com páginas expandidas e index.cshtml selecionado](./media/custom-search-visual-studio-webapp-solution-explorer-index.png)  
  
2. Em index.cshtml, elimine tudo a partir da linha 7 e abaixo.  
  
  ```razor
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }    
  ```  
  
3. Adicione um elemento de quebra de linha e um div para atuar como contentor.  
  
  ```html
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }
  <br />
  <div id="customSearch"></div>
  ```  
  
4. Na página **IU Alojada**, desloque-se para baixo até à secção **Consumir a IU**. Clique nos *Pontos finais* para aceder ao fragmento de JavaScript. Também pode obter o fragmento ao clicar em **Produção** e, em seguida, no separador **IU Alojada**.
  
  <!-- Get new screenshot after prod gets new bits
  ![Screenshot of the Hosted UI save button](./media/custom-search-hosted-ui-consuming-ui.png)  
  -->
  
5. Cole o elemento script no contentor que adicionou.  
  
  ``` html
  @page
  @model IndexModel
  @{
      ViewData["Title"] = "Home page";
  }
  <br />
  <div id="customSearch">
      <script type="text/javascript" 
          id="bcs_js_snippet"
          src="https://ui.customsearch.ai /api/ux/rendering-js?customConfig=<YOUR-CUSTOM-CONFIG-ID>&market=en-US&safeSearch=Moderate&version=latest&q=">
      </script>
  </div>
  ```  
  
6. No **Explorador de Soluções**, clique com o botão direito do rato em **wwwroot** e clique em **Ver no Browser**.  
  
  ![Captura de ecrã do Explorador de Soluções a selecionar Ver no Browser no menu de contexto wwwroot](./media/custom-search-webapp-view-in-browser.png)  

A nova página Web de pesquisa personalizada deve ser semelhante à seguinte:

![Captura de ecrã de página Web de pesquisa personalizada](./media/custom-search-webapp-browse-index.png)

Efetuar uma pesquisa compõe resultados como os seguintes:

![Captura de ecrã de resultados de pesquisa personalizada](./media/custom-search-webapp-results.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Chamar o ponto final de Pesquisa Personalizada do Bing (C#)](../call-endpoint-csharp.md)
