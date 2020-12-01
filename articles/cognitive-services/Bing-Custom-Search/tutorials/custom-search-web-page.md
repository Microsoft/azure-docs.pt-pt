---
title: 'Tutorial: Criar uma página Web de pesquisa personalizada - Pesquisa Personalizada do Bing'
titleSuffix: Azure Cognitive Services
description: Aprenda a configurar uma instância de pesquisa bing personalizada e integre-a numa página web com este tutorial.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: tutorial
ms.date: 03/05/2019
ms.author: aahi
ms.openlocfilehash: a789cb3fde05d12a8793196043f1c246bbab6559
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96342418"
---
# <a name="tutorial-build-a-custom-search-web-page"></a>Tutorial: Criar uma página Web de Pesquisa Personalizada

> [!WARNING]
> As APIs de Pesquisa de Bing estão a mover-se dos Serviços Cognitivos para os Serviços de Pesquisa Bing. A partir **de 30 de outubro de 2020,** quaisquer novos casos de Bing Search devem ser adquir-se na sequência do processo [aqui](/bing/search-apis/bing-web-search/create-bing-search-service-resource)documentado.
> Bing Search APIs aforados usando Serviços Cognitivos será suportado durante os próximos três anos ou até o final do seu Contrato de Empresa, o que acontecer primeiro.
> Para obter instruções de migração, consulte [os Serviços de Busca Bing.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)

Saiba como a Pesquisa Personalizada do Bing lhe permite criar experiências de pesquisa personalizada dos tópicos que mais lhe interessam. Por exemplo, se possuir um site de artes marciais que forneça uma experiência de pesquisa, pode especificar os domínios, sub-sites e páginas web que bing pesquisa. Os seus utilizadores veem resultados de pesquisa personalizados de acordo com os conteúdos que lhes interessam, em vez de terem de percorrer resultados de pesquisa gerais que podem apresentar conteúdos irrelevantes. 

Este tutorial demonstra como configurar uma instância de pesquisa personalizada e integrá-la numa nova página Web.

As tarefas abrangidas são:

> [!div class="checklist"]
> - Criar uma instância de pesquisa personalizada
> - Adicionar entradas ativas
> - Adicionar entradas bloqueadas
> - Adicionar entradas afixadas
> - Integrar a pesquisa personalizada numa página Web

## <a name="prerequisites"></a>Pré-requisitos

- Para acompanhar o tutorial, necessita de uma chave de subscrição para a API de Pesquisa Personalizada do Bing.  Para obter uma chave, [Crie um recurso de pesquisa personalizada Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) no portal Azure.
- Se ainda não tiver o Visual Studio 2017 ou posteriormente **free** instalado, pode descarregar e utilizar a [Edição Comunitária visual 2019 gratuita do Visual Studio 2019.](https://www.visualstudio.com/downloads/)

## <a name="create-a-custom-search-instance"></a>Criar uma instância de pesquisa personalizada

Para criar uma instância de Pesquisa Personalizada do Bing:

1. Abra um browser.  
  
2. Navegue para o [portal](https://customsearch.ai) da pesquisa personalizada.  
  
3. Inicie sessão no portal com uma conta Microsoft (MSA). Se não tiver um MSA, clique em **Criar uma conta Microsoft**. Se for a primeira vez que utiliza o portal, irá pedir permissões para aceder aos seus dados. Clique em **Sim**.  
  
4. Depois de iniciar sessão, clique em **Nova pesquisa personalizada**. Na janela Criar uma nova janela **de instância de pesquisa personalizada,** insira um nome que seja significativo e descreva o tipo de conteúdo que a pesquisa retorna. Pode alterar o nome em qualquer altura.  
  
   ![Captura de ecrã da caixa Criar uma nova instância de pesquisa personalizada](../media/newCustomSrch.png)  
  
5. Clique em OK, especifique um URL e se quer incluir subpáginas do URL.  
  
   ![Captura de ecrã da página de definições do URL](../media/newCustomSrch1-a.png)  


## <a name="add-active-entries"></a>Adicionar entradas ativas

Para incluir os resultados de sites ou URLs específicos, adicione-os ao separador **Ativo**.

1. Na página **Configuração**, clique no separador **Ativo** e introduza o URL de um ou mais sites que queira incluir na sua pesquisa.

    ![Captura de ecrã do separador Ativo do Editor de Definições](../media/customSrchEditor.png)

2. Para confirmar que a sua instância devolve resultados, introduza uma consulta no painel de pré-visualização no lado direito. O Bing só devolve resultados para sites públicos que lhe estejam indexados.

## <a name="add-blocked-entries"></a>Adicionar entradas bloqueadas

Para excluir os resultados de sites ou URLs específicos, adicione-os ao separador **Bloqueado**.

1. Na página **Configuração**, clique no separador **Bloqueado** e introduza o URL de um ou mais sites que queira excluir da sua pesquisa.

    ![Captura de ecrã do separador Bloqueado do Editor de Definições](../media/blockedCustomSrch.png)


2. Para confirmar que a sua instância não devolve resultados de sites bloqueados, introduza uma consulta no painel de pré-visualização no lado direito. 

## <a name="add-pinned-entries"></a>Adicionar entradas afixadas

Para fixar uma página web específica no topo dos resultados de pesquisa, adicione a página web e o termo de consulta ao **separador Pinned.** O separador **Pinned** contém uma lista de pares de páginas web e de termo de consulta que especificam a página web que aparece como o resultado principal para uma consulta específica. A página web só é fixada se a cadeia de consulta do utilizador corresponder à cadeia de consulta do pino com base na condição de correspondência do pino. Apenas as páginas Web indexadas serão apresentadas nas pesquisas. Para mais informações, consulte [Definir a sua vista personalizada.](../define-your-custom-view.md#pin-slices-to-the-top-of-search-results)

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
  
5. Selecione as chaves de subscrição nas listas pendentes. Em alternativa, pode introduzir a chave de subscrição manualmente.
  
   ![Screenshot da chave de subscrição de UI hospedada](./media/custom-search-hosted-ui-subscription-key.png)

[!INCLUDE [publish or revert](../includes/publish-revert.md)]

<a name="consuminghostedui"></a>
## <a name="consuming-hosted-ui"></a>Consumir a IU Alojada

Existem duas formas de consumir a IU alojada.  

- Opção 1: Integrar o fragmento de JavaScript fornecido na sua aplicação.
- Opção 2: Utilizar o Ponto Final de HTML fornecido.

O restante deste tutorial ilustra **a Opção 1: JavaScript snippet**.  

## <a name="set-up-your-visual-studio-solution"></a>Configurar a sua solução do Visual Studio

1. Abra o **Visual Studio** no seu computador.  
  
2. No menu **Ficheiro**, selecione **Novo**, e, em seguida, escolha **Projeto**.  
  
3. Na janela **Novo Projeto**, selecione **Aplicação Web Visual C#/Web/ASP.NET Core**, dê um nome ao projeto e, em seguida, clique em **OK**.  
  
   ![Captura de ecrã da janela Novo Projeto](./media/custom-search-new-project.png)  
  
4. Na janela **Nova Aplicação Web do ASP.NET Core**, selecione **Aplicação Web** e clique em **OK**.  
  
   ![Screenshot da nova janela do webapp](./media/custom-search-new-webapp.png)  

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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Chamar o ponto final de Pesquisa Personalizada do Bing (C#)](../call-endpoint-csharp.md)