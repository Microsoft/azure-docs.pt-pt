---
title: 'Tutorial JavaScript: Implementar o site ativado por pesquisa'
titleSuffix: Azure Cognitive Search
description: Implementar o website ativado por pesquisa para a aplicação web Azure Static.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: a49ede283899cec42898672f5a376221265dea10
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726957"
---
# <a name="3---deploy-the-search-enabled-website"></a>3 - Implementar o site ativado por pesquisa

Implemente o website ativado por pesquisa como uma aplicação web Azure Static. Esta implementação inclui tanto a aplicação Reagir como a aplicação Função.  

A aplicação Static Web retira as informações e ficheiros para a implementação do GitHub utilizando o seu garfo do repositório de amostras.  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Criar uma aplicação web estática no código do estúdio visual

1. Selecione **Azure** a partir da Barra de Atividades e, em seguida, selecione **Aplicações Web Estáticas** a partir da barra lateral. 
1. Clique com o botão direito no nome da subscrição e, em seguida, **selecione Create Static Web App (Advanced)**.    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="Clique com o botão direito no nome da subscrição e, em seguida, selecione **Criar a Aplicação Web Estática (Avançada)**.":::

1. Siga as instruções para fornecer as seguintes informações:

    |Prompt|ENTER|
    |--|--|
    |Como pretende criar uma Aplicação Web Estática?|Utilizar o repositório GitHub existente|
    |Escolha organização|Selecione o seu _próprio_ pseudónimo GitHub como organização.|
    |Escolha o repositório|Selecione **amostras de javascript-pesquisa azure** da lista. |
    |Escolha ramo do repositório|Selecione **mestre** da lista. |
    |Insira o nome da nova Aplicação Web Estática.|Crie um nome único para o seu recurso. Por exemplo, pode preparar o seu nome para o nome do repositório, como, `joansmith-azure-search-javascript-samples` . |
    |Selecione um grupo de recursos para novos recursos.|Utilize o grupo de recursos que criou para este tutorial.|
    |Escolha a construção predefinida para configurar a estrutura do projeto padrão.|Selecione **Personalizado**|
    |Selecione a localização do seu código de candidatura|`search-website`|
    |Selecione a localização do seu código de função Azure|`search-website/api`|
    |Entre no caminho da sua saída de construção...|compilar|
    |Selecione um local para novos recursos.|Selecione uma região perto de si.|

1. O recurso é criado, selecione **Ações Abertas no GitHub** a partir das Notificações. Isto abre uma janela do navegador apontada para o seu repo forcado. 

    A lista de ações indica que a sua aplicação web, tanto cliente como funções, foram empurradas com sucesso para a sua App Web Estática Azure. 

    Aguarde até que a construção e a implantação se concretizem antes de continuar. Isto pode levar um minuto ou dois para terminar.

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Obtenha chave de consulta de pesquisa cognitiva no Código do Estúdio Visual

1. No Código do Estúdio Visual, abra a [barra de Atividades](https://code.visualstudio.com/docs/getstarted/userinterface)e selecione o ícone Azure. 

1. Na barra Lateral, selecione a sua subscrição Azure sob a área **Azure: Cognitive Search,** em seguida, clique com o botão direito no seu recurso de Pesquisa e selecione **Copy Consultary Key**. 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="Na barra Lateral, selecione a sua subscrição Azure sob a área **Azure: Cognitive Search** e, em seguida, clique com o botão direito no seu recurso de pesquisa e selecione **Copy Query Key**.":::

1. Guarde esta chave de consulta, terá de a utilizar na secção seguinte. A chave de consulta é capaz de consultar o seu Índice. 

## <a name="add-configuration-settings-in-visual-studio-code"></a>Adicionar definições de configuração no Código do Estúdio Visual

A aplicação Azure Function não devolverá dados de pesquisa até que os segredos de Pesquisa estejam em definições. 

1. Selecione **Azure** a partir da Barra de Atividades e, em seguida, selecione **Aplicações Web Estáticas** a partir da barra lateral. 
1. Expanda a sua nova Aplicação Web Estática até que as **Definições de Aplicação** apresentem.
1. Clique com o botão direito nas **Definições de Aplicação** e, em seguida, selecione **Adicionar Nova Definição**.

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-static-web-app-configure-settings.png" alt-text="Clique com o botão direito em **Definições de aplicação*** e, em seguida, selecione **Adicionar nova definição**.":::

1. Adicione as seguintes definições:

    |Definição|O seu valor de recursos de pesquisa|
    |--|--|
    |SearchApiKey|A sua chave de consulta de pesquisa|
    |Nome do Serviço de Pesquisa|O nome do seu recurso de pesquisa|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

## <a name="use-search-in-your-static-web-app"></a>Use a pesquisa na sua aplicação web Estática

1. No Código do Estúdio Visual, abra a [barra de Atividades](https://code.visualstudio.com/docs/getstarted/userinterface)e selecione o ícone Azure.
1. Na barra Lateral, **clique à direita na subscrição do Azure** sob a área e encontre a `Static web apps` aplicação web Estática que criou para este tutorial.
1. Clique com o botão direito no nome estático da Aplicação Web e **selecione site de navegação.**
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="Clique com o botão direito no nome estático da Aplicação Web e selecione **Navegue no site**.":::    

1. Selecione **Abrir** no diálogo pop-up.
1. Na barra de pesquisa do site, insira uma consulta de pesquisa `code` como, _lentamente,_ para que a funcionalidade de sugestão sugira títulos de livro. Selecione uma sugestão ou continue a inserir a sua própria consulta. Prima insira quando tiver concluído a sua consulta de pesquisa. 
1. Reveja os resultados e, em seguida, selecione um dos livros para ver mais detalhes. 

## <a name="clean-up-resources"></a>Limpar os recursos

Para limpar os recursos criados neste tutorial, elimine o grupo de recursos.

1. No Código do Estúdio Visual, abra a [barra de Atividades](https://code.visualstudio.com/docs/getstarted/userinterface)e selecione o ícone Azure. 

1. Na barra Lateral, **clique à direita na sua subscrição Azure** sob a área e encontre o grupo de recursos que criou para este `Resource Groups` tutorial.
1. Clique com o botão direito no nome do grupo de recursos e, em seguida, **selecione Delete**.
    Isto elimina tanto os recursos de aplicações web Search como estáticos.
1. Se já não quer o garfo GitHub da amostra, lembre-se de apagar isso no GitHub. Vá às **Definições** do garfo e, em seguida, apague o garfo. 


## <a name="next-steps"></a>Passos seguintes

* [Compreender Integração de Pesquisa para o website ativado por pesquisa](tutorial-javascript-search-query-integration.md)
