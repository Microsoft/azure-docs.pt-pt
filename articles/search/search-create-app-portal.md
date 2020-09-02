---
title: Criar uma app de demonstração no portal Azure
titleSuffix: Azure Cognitive Search
description: Execute o assistente de demo Create (pré-visualização) para gerar páginas HTML e script para uma aplicação web operacional. A página inclui uma barra de pesquisa, área de resultados, barra lateral e suporte de cabeça de tipo.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 07/01/2020
ms.openlocfilehash: 6adbd83ddfbdb82d1054d4e74dd9492d08583ede
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89292542"
---
# <a name="quickstart-create-a-demo-app-in-the-portal-azure-cognitive-search"></a>Quickstart: Criar uma app de demonstração no portal (Azure Cognitive Search)

Utilize o assistente de **demo** do portal Azure para gerar uma aplicação web de estilo "localhost" que funciona num navegador. Dependendo da sua configuração, a aplicação gerada está operacional na primeira utilização, com uma ligação apenas de leitura ao vivo a um índice remoto. Uma aplicação predefinitiva pode incluir uma barra de pesquisa, área de resultados, filtros de barra lateral e suporte de tipa.

A app de demonstração pode ajudá-lo a visualizar como um índice irá funcionar numa aplicação de clientes, mas não se destina a cenários de produção. As aplicações do cliente devem incluir segurança, manipulação de erros e lógica de hospedagem que a página HTML gerada não fornece. Quando estiver pronto para criar uma aplicação para clientes, consulte [criar a sua primeira aplicação de pesquisa utilizando o .NET SDK](tutorial-csharp-create-first-app.md) para os próximos passos.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, deve ter o seguinte:

+ Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/)

+ Um serviço de Pesquisa Cognitiva Azure. [Crie um serviço](search-create-service-portal.md) ou [encontre um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) sob a sua subscrição atual. Pode utilizar um serviço gratuito para este arranque rápido. 

+ [Microsoft Edge (versão mais recente)](https://www.microsoft.com/edge) ou Google Chrome.

+ Um [índice de pesquisa](search-what-is-an-index.md) para usar como base da sua aplicação gerada. 

  Este quickstart utiliza os dados e índices de amostras de imóveis incorporados porque tem imagens de miniatura (o assistente suporta adicionar imagens à página de resultados). Para criar o índice utilizado neste exercício, executar o assistente **de dados de Importação,** escolhendo a fonte de dados *da amostra real-nos-us.*

  ![página de origem de dados para dados de amostra](media/search-create-app-portal/import-data-realestate.png)

Quando o índice estiver pronto a ser utilizado, passe para o próximo passo.

## <a name="start-the-wizard"></a>Inicie o assistente

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua conta do Azure.

1. [Encontre o seu serviço de pesquisa](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) e na página 'Vista Geral', a partir dos links no meio da página, selecione **Índices**. 

1. Escolha *o índice de amostra realestate-us da* lista de índices existentes.

1. Na página de índice, no topo, selecione **Criar app de demonstração (pré-visualização)** para iniciar o assistente.

1. Na primeira página de assistente, selecione **Ativar a partilha de recursos de origem cruzada (CORS)** para adicionar suporte CORS à definição de índice. Este passo é opcional, mas a sua aplicação web local não se ligará ao índice remoto sem ele.

## <a name="configure-search-results"></a>Configure os resultados da pesquisa

O assistente fornece um layout básico para resultados de pesquisa renderizados que inclui espaço para uma imagem de miniatura, um título e descrição. Apoiar cada um destes elementos é um campo no seu índice que fornece os dados. 

1. Em Miniatura, escolha o campo *de miniaturas* no índice *de amostra realestate-us.* Esta amostra inclui miniaturas de imagem sob a forma de imagens endereçadas por URL armazenadas num campo chamado *miniatura*. Se o seu índice não tiver imagens, deixe este campo em branco.

1. Em Título, escolha um campo que transmita a singularidade de cada documento. Nesta amostra, o ID de listagem é uma seleção razoável.

1. Em Descrição, escolha um campo que forneça detalhes que possam ajudar alguém a decidir se deve clicar nesse documento em particular.

   ![configure os resultados para os dados da amostra](media/search-create-app-portal/configure-results.png)

## <a name="add-a-sidebar"></a>Adicione uma barra lateral

O serviço de pesquisa suporta a navegação virada para o lado, que é muitas vezes renderizada como uma barra lateral. As facetas baseiam-se em campos filtrados e facetable, conforme expresso no esquema de índice.

Na Pesquisa Cognitiva Azure, a navegação facial é uma experiência de filtragem cumulativa. Dentro de uma categoria, selecionar vários filtros expande os resultados (por exemplo, selecionando Seattle e Bellevue dentro do City). Em categorias, selecionar vários filtros reduz os resultados.

> [!TIP]
> Pode ver o esquema de índice completo no portal. Procure o link **de definição de índice (JSON)** na página geral de cada índice. Os campos que se qualificam para a navegação facetada têm atributos "filtrados: verdadeiros" e "facetable: verdadeiros".

Aceite a atual seleção de facetas e continue para a página seguinte.


## <a name="add-typeahead"></a>Adicionar cabeça de tipo

A funcionalidade Typeahead está disponível sob a forma de sugestões de consulta automática e consulta. O assistente suporta sugestões de consulta. Com base nas entradas de teclas fornecidas pelo utilizador, o serviço de pesquisa devolve uma lista de cadeias de consulta "completas" que podem ser selecionadas como entrada.

As sugestões são ativadas em definições de campo específicas. O assistente dá-lhe opções para configurar a quantidade de informação incluída numa sugestão. 

A imagem que se segue mostra opções no assistente, justtaposa com uma página renderizada na aplicação. Pode ver como as seleções de campo são usadas e como "Show Field Name" é usado para incluir ou excluir a rotulagem dentro da sugestão.

![Configuração de sugestão de consulta](media/search-create-app-portal/suggestions.png)

## <a name="create-download-and-execute"></a>Criar, transferir e executar

1. Selecione **Criar uma aplicação de demonstração** para gerar o ficheiro HTML.

1. Quando solicitado, selecione **Descarregue a sua aplicação** para descarregar o ficheiro.

1. Abra o ficheiro. Deve ver uma página semelhante à seguinte. Introduza um termo e utilize filtros para reduzir os resultados. 

O índice subjacente é composto por dados fictícios gerados que foram duplicados entre documentos, e as descrições às vezes não correspondem à imagem. Pode esperar uma experiência mais coesa quando criar uma app baseada nos seus próprios índices.

![Executar a aplicação](media/search-create-app-portal/run-app.png)


## <a name="clean-up-resources"></a>Limpar os recursos

Ao trabalhar na sua própria subscrição, recomendamos que verifique, depois de concluir um projeto, se irá precisar dos recursos que criou. Os recursos que deixar em execução podem custar-lhe dinheiro. Pode eliminar recursos individualmente ou eliminar o grupo de recursos para eliminar todo o conjunto de recursos.

Pode encontrar e gerir recursos no portal, utilizando a ligação **de todos os recursos** ou **grupos** de recursos no painel de navegação à esquerda.

Se estiver a utilizar um serviço gratuito, lembre-se que está limitado a três índices, indexadores e fontes de dados. Pode eliminar itens individuais no portal para ficar abaixo do limite. 

## <a name="next-steps"></a>Passos seguintes

Embora a aplicação predefinitiva seja útil para a exploração inicial e pequenas tarefas, a revisão das APIs no início irá ajudá-lo a compreender os conceitos e o fluxo de trabalho a um nível mais profundo:

> [!div class="nextstepaction"]
> [Criar um índice usando .NET SDK](./search-get-started-dotnet.md)