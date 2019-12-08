---
title: Criar um aplicativo de pesquisa no portal do Azure
titleSuffix: Azure Cognitive Search
description: Execute o assistente para criar aplicativo (versão prévia) para gerar páginas HTML e script para um aplicativo Web operacional. A página inclui uma barra de pesquisa, uma área de resultados, uma barra lateral e suporte typeahead.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: 16748d9948518964d47ee9543765e3cb59ae2ec4
ms.sourcegitcommit: 375b70d5f12fffbe7b6422512de445bad380fe1e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74901998"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>Início rápido: criar um aplicativo de pesquisa no portal (Pesquisa Cognitiva do Azure)

Use o assistente para **criar aplicativo de pesquisa** do portal para gerar um aplicativo Web de estilo "localhost" que pode ser baixado que é executado em um navegador. Dependendo de sua configuração, o aplicativo gerado está operacional no primeiro uso, com uma conexão dinâmica com um índice remoto. Um aplicativo padrão pode incluir uma barra de pesquisa, uma área de resultados, filtros de barra lateral e suporte typeahead.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

[Crie um serviço de pesquisa cognitiva do Azure](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este guia de início rápido. 

[Crie um índice](search-create-index-portal.md) para usar como a base do seu aplicativo. 

Este guia de início rápido usa os dados de exemplo internos do imóveis e o índice porque ele tem imagens em miniatura. Para criar o índice usado neste exercício, execute o assistente para **importar dados** , escolhendo a fonte de dados *realestate-US-Sample* .

![página fonte de dados para dados de exemplo](media/search-create-app-portal/import-data-realestate.png)

Quando o índice estiver pronto para uso, vá para a próxima etapa.

## <a name="start-the-wizard"></a>Iniciar o assistente

1. Entre no [portal do Azure](https://portal.azure.com) e [localize o serviço de pesquisa](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices).

1. Na página Visão geral, nos links no meio da página, selecione **índices**. 

1. Escolha *realestate-US-Sample-index* na lista de índices existentes.

1. Na página índice, na parte superior, selecione **criar aplicativo de pesquisa (versão prévia)** para iniciar o assistente.

1. Na primeira página do assistente, selecione **habilitar CORS (compartilhamento de recursos entre origens)** para adicionar suporte a CORS à sua definição de índice. Esta etapa é opcional, mas seu aplicativo Web local não se conectará ao índice remoto sem ele.

## <a name="configure-search-results"></a>Configurar resultados da pesquisa

O assistente fornece um layout básico para resultados de pesquisa renderizados que incluem espaço para uma imagem em miniatura, um título e uma descrição. O backup de cada um desses elementos é um campo no índice que fornece os dados. 

1. Em miniatura, escolha o campo de *miniatura* no índice *realestate-US-Sample* . Este exemplo é para incluir miniaturas de imagem na forma de imagens endereçadas por URL armazenadas em um campo chamado *miniatura*. Se o índice não tiver imagens, deixe esse campo em branco.

1. Em título, escolha um campo que transmita a exclusividade de cada documento. Neste exemplo, a ID de listagem é uma seleção razoável.

1. Em descrição, escolha um campo que forneça detalhes que podem ajudar alguém a decidir se deseja clicar nesse documento específico.

![página fonte de dados para dados de exemplo](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Adicionar uma barra lateral

O serviço de pesquisa dá suporte à navegação facetada, que geralmente é renderizada como uma barra lateral. As facetas são baseadas em campos filtráveis e de facetable, conforme expresso no esquema de índice.

No Azure Pesquisa Cognitiva, a navegação facetada é uma experiência de filtragem cumulativa. Dentro de uma categoria, selecionar vários filtros expande os resultados (por exemplo, selecionando Seattle e Bellevue na cidade). Nas categorias, a seleção de vários filtros reduz os resultados.

> [!TIP]
> Você pode exibir o esquema de índice completo no Portal. Procure o link **definição de índice (JSON)** na página de visão geral de cada índice. Os campos que se qualificam para navegação facetada têm atributos "filtráveis: true" e "facetable: true".

Aceite a seleção atual de facetas e continue na próxima página.


## <a name="add-typeahead"></a>Adicionar typeahead

A funcionalidade typeahead está disponível na forma de preenchimento automático e sugestões de consulta. O assistente dá suporte a sugestões de consulta. Com base nas entradas de pressionamento de teclas fornecidas pelo usuário, o serviço de pesquisa retorna uma lista de cadeias de caracteres de consulta "concluídas" que podem ser selecionadas como entrada.

As sugestões são habilitadas em definições de campo específicas. O assistente fornece opções para configurar a quantidade de informações incluídas em uma sugestão. 

A captura de tela a seguir mostra as opções no assistente, justaposto com uma página renderizada no aplicativo. Você pode ver como as seleções de campo são usadas e como "Mostrar nome do campo" é usado para incluir ou excluir rótulos dentro da sugestão.

![Configuração de sugestão de consulta](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Criar, baixar e executar

1. Selecione **criar aplicativo de pesquisa** para gerar o arquivo HTML.

1. Quando solicitado, selecione **baixar seu aplicativo** para baixar o arquivo.

1. Abra o ficheiro. Você deverá ver uma página semelhante à captura de tela a seguir. Insira um termo e use filtros para restringir os resultados. 

O índice subjacente é composto por dados fictícios e gerados que foram duplicados em documentos, e as descrições às vezes não correspondem à imagem. Você pode esperar uma experiência mais coesa ao criar um aplicativo com base em seus próprios índices.

![Executar a aplicação](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Limpar recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link **todos os recursos** ou **grupos de recursos** no painel de navegação esquerdo.

Se você estiver usando um serviço gratuito, lembre-se de que você está limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Embora o aplicativo padrão seja útil para a exploração inicial e tarefas pequenas, a revisão das APIs logo no início ajudará você a entender os conceitos e o fluxo de trabalho em um nível mais profundo:

> [!div class="nextstepaction"]
> [Criar um índice usando o SDK do .NET](https://docs.microsoft.com/azure/search/search-create-index-dotnet)