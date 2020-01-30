---
title: Criar uma app de pesquisa no portal Azure
titleSuffix: Azure Cognitive Search
description: Executar o assistente da aplicação Create (pré-visualização) para gerar páginas HTML e script para uma aplicação web operacional. A página inclui uma barra de pesquisa, área de resultados, barra lateral e suporte à máquina de escrever.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 12/05/2019
ms.openlocfilehash: 9ed62ea6fdfc0732128ed340cc0bc48481eb73ec
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76841085"
---
# <a name="quickstart-create-a-search-app-in-the-portal-azure-cognitive-search"></a>Quickstart: Criar uma app de pesquisa no portal (Pesquisa Cognitiva Azure)

Utilize o assistente de **aplicações** de pesquisa Create do portal para gerar uma aplicação web de estilo "localhost" descarregada e "local" que funciona num navegador. Dependendo da sua configuração, a aplicação gerada está operacional na primeira utilização, com uma ligação ao vivo a um índice remoto. Uma aplicação padrão pode incluir uma barra de pesquisa, área de resultados, filtros de barra lateral e suporte à máquina de escrever.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="prerequisites"></a>Pré-requisitos

Você vai querer fazer upgrade para a [versão mais recente do Microsoft Edge](https://www.microsoft.com/edge) ou usar o navegador Chrome da Google para este quickstart neste momento.

[Crie um serviço de pesquisa cognitiva do Azure](search-create-service-portal.md) ou [Localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) em sua assinatura atual. Você pode usar um serviço gratuito para este guia de início rápido. 

[Crie um índice](search-create-index-portal.md) para usar como base da sua aplicação. 

Este quickstart usa os dados e índice da amostra imobiliária incorporada porque tem imagens de miniaturas. Para criar o índice utilizado neste exercício, execute o assistente de **dados da Importação,** escolhendo a fonte de dados da *amostra realestate-us.*

![página de fonte de dados para dados de amostra](media/search-create-app-portal/import-data-realestate.png)

Quando o índice estiver pronto a ser utilizado, passe para o próximo passo.

## <a name="start-the-wizard"></a>Iniciar o assistente

1. Assine no [portal Azure](https://portal.azure.com) e encontre o [seu serviço de pesquisa.](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)

1. Na página 'Visão Geral', a partir dos links no meio da página, selecione **Indexes**. 

1. Escolha o *índice de amostras imobiliária-us* da lista de índices existentes.

1. Na página de índice, no topo, selecione Criar app de **pesquisa (pré-visualização)** para iniciar o assistente.

1. Na primeira página do assistente, selecione **Enable Cross Origin Resource Sharing (CORS)** para adicionar suporte CORS à sua definição de índice. Este passo é opcional, mas a sua aplicação web local não se liga ao índice remoto sem ele.

## <a name="configure-search-results"></a>Configurar resultados de pesquisa

O assistente fornece um layout básico para resultados de pesquisa renderizados que inclui espaço para uma imagem de miniatura, um título e descrição. Apoiar cada um destes elementos é um campo no seu índice que fornece os dados. 

1. Em Miniatura, escolha o campo de *miniaturas* no índice de *amostras imobiliária-nos.* Esta amostra inclui miniaturas de imagem sob a forma de imagens endereçadas a URL armazenadas num campo chamado *miniatura*. Se o seu índice não tiver imagens, deixe este campo em branco.

1. Em Título, escolha um campo que transmita a singularidade de cada documento. Nesta amostra, o ID de listagem é uma seleção razoável.

1. Em Descrição, escolha um campo que forneça detalhes que possam ajudar alguém a decidir se deve clicar nesse documento em particular.

![página de fonte de dados para dados de amostra](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Adicione uma barra lateral

O serviço de pesquisa suporta a navegação faceada, que é muitas vezes renderizada como uma barra lateral. As facetas baseiam-se em campos filtrantes e facetas, tal como expresso no esquema de índice.

Na Pesquisa Cognitiva Azure, a navegação facetada é uma experiência de filtragem cumulativa. Dentro de uma categoria, a seleção de vários filtros expande os resultados (por exemplo, selecionando Seattle e Bellevue dentro da Cidade). Entre categorias, selecionar vários filtros estreita os resultados.

> [!TIP]
> Pode ver o esquema de índice completo no portal. Procure o link de **definição de Índice (JSON)** na página geral de cada índice. Os campos que se qualificam para a navegação facetada têm atributos "filtrados: verdadeiros" e "facetable: verdadeiros".

Aceite a seleção atual de facetas e continue para a página seguinte.


## <a name="add-typeahead"></a>Adicionar tipo de letra

A funcionalidade Typeahead está disponível sob a forma de sugestões de auto-completas e consultas. O assistente suporta sugestões de consulta. Com base nas inputs de teclas fornecidas pelo utilizador, o serviço de pesquisa devolve uma lista de cordas de consulta "completas" que podem ser selecionadas como entrada.

As sugestões são ativadas em definições de campo específicas. O assistente dá-lhe opções para configurar a quantidade de informação incluída numa sugestão. 

A imagem seguinte mostra opções no assistente, justtaposamente com uma página renderizada na aplicação. Pode ver como são utilizadas as seleções de campo e como o "Show Field Name" é usado para incluir ou excluir a rotulagem dentro da sugestão.

![Configuração de sugestão de consulta](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Criar, transferir e executar

1. Selecione **Criar aplicativo** de pesquisa para gerar o ficheiro HTML.

1. Quando solicitado, selecione **Descarregue** a sua aplicação para descarregar o ficheiro.

1. Abra o ficheiro. Deve ver uma página semelhante à seguinte imagem. Introduza um termo e utilize filtros para reduzir os resultados. 

O índice subjacente é composto por dados fictícios e gerados que foram duplicados entre documentos, e as descrições às vezes não correspondem à imagem. Pode esperar uma experiência mais coesa quando criar uma app com base nos seus próprios índices.

![Executar a aplicação](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Limpar recursos

Quando está a trabalhar na sua própria subscrição, é uma boa ideia no final de um projeto identificar se ainda precisa dos recursos que criou. Os recursos deixados a funcionar podem custar-lhe dinheiro. Pode eliminar os recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link **todos os recursos** ou **grupos de recursos** no painel de navegação esquerdo.

Se você estiver usando um serviço gratuito, lembre-se de que você está limitado a três índices, indexadores e fontes de dados. Você pode excluir itens individuais no portal para permanecer abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Embora a aplicação padrão seja útil para a exploração inicial e pequenas tarefas, rever as APIs desde o início irá ajudá-lo a entender os conceitos e fluxo de trabalho a um nível mais profundo:

> [!div class="nextstepaction"]
> [Criar um índice usando .NET SDK](https://docs.microsoft.com/azure/search/search-create-index-dotnet)