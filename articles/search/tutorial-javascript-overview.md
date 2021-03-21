---
title: 'Tutorial JavaScript: Visão geral da integração de pesquisa'
titleSuffix: Azure Cognitive Search
description: Visão geral técnica e configuração para adicionar pesquisa a um site e implantação para Azure Static Web App.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 03192b8a84b78682b53bf3d47e7de7b65eb8bceb
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726945"
---
# <a name="1---overview-of-adding-search-to-a-website"></a>1 - Visão geral da adição de pesquisa a um website

Este tutorial constrói um website para pesquisar através de um catálogo de livros e depois implementa o site para uma App Web Estática Azure. 

A aplicação está disponível: 
* [Sample](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website)
* [Site de demonstração - aka.ms/azs-good-books](https://aka.ms/azs-good-books)

## <a name="what-does-the-sample-do"></a>O que faz a amostra? 

Este site de amostras fornece acesso a um catálogo de 10.000 livros. Um utilizador pode pesquisar o catálogo introduzindo texto na barra de pesquisa. Enquanto o utilizador introduz texto, o site utiliza a funcionalidade de sugestão do Índice de Pesquisa para completar o texto. Uma vez terminada a consulta, a lista de livros é exibida com uma parte dos detalhes. Um utilizador pode selecionar um livro para ver todos os detalhes, armazenados no Índice de Pesquisa, do livro. 

:::image type="content" source="./media/tutorial-javascript-overview/cognitive-search-enabled-book-website.png" alt-text="Este site de amostras fornece acesso a um catálogo de 10.000 livros. Um utilizador pode pesquisar o catálogo introduzindo texto na barra de pesquisa. Enquanto o utilizador introduz texto, o site utiliza a funcionalidade de sugestão do Índice de Pesquisa para completar o texto. Uma vez terminada a pesquisa, a lista de livros é exibida com uma parte dos detalhes. Um utilizador pode selecionar um livro para ver todos os detalhes, armazenados no Índice de Pesquisa, do livro.":::

A experiência de pesquisa inclui: 

* Search – fornece funcionalidade de pesquisa para a aplicação.
* Sugestão – fornece sugestões uma vez que o utilizador está a escrever na barra de pesquisa.
* Document Lookup – procura um documento por ID para recuperar todo o seu conteúdo para a página de detalhes.

## <a name="how-is-the-sample-organized"></a>Como é organizada a amostra?

A [amostra](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website) inclui o seguinte:

|Aplicação|Objetivo|GitHub<br>Repositório<br>Localização|
|--|--|--|
|Cliente|Reagem à aplicação (camada de apresentação) para exibir livros, com pesquisa. Chama a app Azure Function. |[/search-site/src](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Servidor|App Azure Function (camada de negócios) - chama a API de Pesquisa Cognitiva Azure usando JavaScript SDK |[/search-website/api](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/src)|
|Inserção em massa|Ficheiro JavaScript para criar o índice e adicionar-lhe documentos.|[/search-website/granumes-insição](https://github.com/Azure-Samples/azure-search-javascript-samples/tree/master/search-website/bulk-insert)|

## <a name="set-up-your-development-environment"></a>Configurar o ambiente de desenvolvimento

Instale o seguinte para o seu ambiente de desenvolvimento local. 

- [Node.js 12 ou 14](https://nodejs.org/en/download)
    - Se tiver uma versão diferente de Node.js instalada no seu computador local, considere utilizar [o Node Version Manager](https://github.com/nvm-sh/nvm) (nvm) ou um recipiente Docker.  
- [Git](https://git-scm.com/downloads)
- [Código do Estúdio Visual](https://code.visualstudio.com/) e as seguintes extensões
    - [Recursos do Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureresourcegroups)
    - [Pesquisa Cognitiva Azure 0.2.0+](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch)
    - [App Web Estática Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps) 
- Opcional:
    - Este tutorial não funciona a API da Função Azure localmente, mas se pretende executá-lo localmente, precisa instalar [ferramentas núcleo-core-funções azure globalmente](/azure/azure-functions/functions-run-local?tabs=linux%2Ccsharp%2Cbash) com o seguinte comando de bash: 
    
    ```bash
    npm install -g azure-functions-core-tools
    ```

## <a name="fork-and-clone-the-search-sample-with-git"></a>Garfo e clone a amostra de pesquisa com git

Forear o repositório de amostras é fundamental para ser capaz de implementar a Aplicação Web Estática. As aplicações web determinam as ações de construção e conteúdo de implementação com base na sua própria localização do garfo GitHub. A execução de código na Static Web App é remota, com as Aplicações Web Estáticas Azure a ler a partir do código na sua amostra forjada.

1. No GitHub, garfo o [repositório](https://github.com/Azure-Samples/azure-search-javascript-samples)de amostras. 

    Complete o processo de garfo no seu navegador web com a sua conta GitHub. Este tutorial usa o seu garfo como parte da implementação para uma App Web Estática Azure. 

1. Num terminal de bash, descarregue a aplicação da amostra para o seu computador local. 

    `YOUR-GITHUB-ALIAS`Substitua-o pelo seu pseudónimo GitHub. 

    ```bash
    git clone https://github.com/YOUR-GITHUB-ALIAS/azure-search-javascript-samples
    ```

1. No Código do Estúdio Visual, abra a sua pasta local do repositório clonado. As restantes tarefas são realizadas a partir do Código do Estúdio Visual, a menos que seja especificado.

## <a name="create-a-resource-group-for-your-azure-resources"></a>Crie um grupo de recursos para os seus recursos Azure

1. No Código do Estúdio Visual, abra a [barra de Atividades](https://code.visualstudio.com/docs/getstarted/userinterface)e selecione o ícone Azure. 
1. Na barra Lateral, **clique com o botão direito na subscrição do Azure** sob a `Resource Groups` área e selecione **Criar grupo de recursos**.

    :::image type="content" source="./media/tutorial-javascript-overview/visual-studio-code-create-resource-group.png" alt-text="Na barra Lateral, clique à direita na sua subscrição Azure** na área de 'Grupos de Recursos' e selecione **Criar grupo de recursos**.":::
1. Introduza um nome de grupo de recursos, como `cognitive-search-website-tutorial` . 
1. Selecione um local próximo de si.
1. Quando criar os recursos de Pesquisa Cognitiva e Aplicação Web Estática, mais tarde no tutorial, utilize este grupo de recursos. 

    A criação de um grupo de recursos dá-lhe uma unidade lógica para gerir os recursos, incluindo apagá-los quando terminar de os utilizar.

## <a name="next-steps"></a>Passos seguintes

* [Criar um Índice de Pesquisa e carregar com documentos](tutorial-javascript-create-load-index.md)
* [Implemente a sua Aplicação Web Estática](tutorial-javascript-deploy-static-web-app.md)
