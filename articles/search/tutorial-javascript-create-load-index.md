---
title: 'Tutorial javaScript: Adicionar pesquisa a aplicações web'
titleSuffix: Azure Cognitive Search
description: Criar dados de CSV de índice de índice de indexação e importação em índice de pesquisa com JavaScript utilizando o npm SDK @azure/search-documents .
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 0fd28262f4a4b852386fa354037e69c5097109c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726982"
---
# <a name="2---create-and-load-search-index-with-javascript"></a>2 - Criar e carregar índice de pesquisa com JavaScript

Continue a construir o seu website ativado por Pesquisa:
* Criar um recurso de pesquisa com a extensão do Código VS
* Criar um novo índice e importar dados com o JavaScript utilizando o script da amostra e o Azure SDK [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) .

## <a name="create-an-azure-search-resource"></a>Criar um recurso de pesquisa Azure 

Crie um novo recurso de pesquisa com a extensão [de Pesquisa Cognitiva Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) para Código do Estúdio Visual.

1. No Código do Estúdio Visual, abra a [barra de Atividades](https://code.visualstudio.com/docs/getstarted/userinterface)e selecione o ícone Azure. 

1. Na barra Lateral, **clique com o botão direito na subscrição do Azure** sob a `Azure: Cognitive Search` área e selecione **Criar um novo serviço de pesquisa**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="Na barra Lateral, clique à direita na sua subscrição Azure sob a área **Azure: Cognitive Search** e selecione **Criar novo serviço de pesquisa**.":::

1. Siga as instruções para fornecer as seguintes informações:

    |Prompt|ENTER|
    |--|--|
    |Insira um nome globalmente único para o novo Serviço de Pesquisa.|**Lembre-se deste nome.** Este nome de recurso torna-se parte do seu ponto final de recurso.|
    |Selecione um grupo de recursos para novos recursos|Utilize o grupo de recursos que criou para este tutorial.|
    |Selecione o SKU para o seu serviço de Pesquisa.|Selecione **Grátis** para este tutorial. Não é possível alterar um nível de preços SKU após a criação do serviço.|
    |Selecione um local para novos recursos.|Selecione uma região perto de si.|

1. Depois de completar as indicações, o seu novo recurso De pesquisa é criado. 

## <a name="get-your-search-resource-admin-key"></a>Obtenha a sua chave de administração de recursos de pesquisa

Obtenha a sua chave de administração de recursos de pesquisa com a extensão do Código do Estúdio Visual. 

1. No Código do Estúdio Visual, na barra lateral, clique à direita no seu recurso de pesquisa e selecione **Copy Admin Key**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="Na barra lateral, clique com o botão direito no seu recurso de pesquisa e selecione **Copy Admin Key**.":::

1. Guarde esta chave de administração, terá de a utilizar numa [secção posterior.](#prepare-the-bulk-import-script-for-search) 

## <a name="prepare-the-bulk-import-script-for-search"></a>Prepare o script de importação a granel para pesquisa

O script utiliza o Azure SDK para pesquisa cognitiva:

* [pacote npm @azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* [Documentação de Referência](/javascript/api/overview/azure/search-documents-readme)

1. No Código do Estúdio Visual, abra o `bulk_insert_books.js` ficheiro na subdiretória,  `search-web/bulk-insert` substitua as seguintes variáveis pelos seus próprios valores para autenticar com o Azure Search SDK:

    * NOME DO SEU RECURSO DE PESQUISA
    * A SUA CHAVE DE ADMINISTRAÇÃO DE PESQUISA

    :::code language="javascript" source="~/azure-search-javascript-samples/search-website/bulk-insert/bulk_insert_books.js" highlight="16,17" :::

1. Abra um terminal integrado no Estúdio Visual para a subdirectória do diretório do `search-web/bulk-insert` projeto, e executar o seguinte comando para instalar as dependências. 

    ```bash
    npm install 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>Executar o script de importação a granel para pesquisa

1. Continue a utilizar o terminal integrado no Visual Studio para a subdirectória do diretório do `search-web/bulk-insert` projeto, para executar o seguinte comando de bash para executar o `bulk_insert_books.js` script:

    ```javascript
    npm start
    ```

1. À medida que o código é executado, a consola apresenta o progresso. 
1. Quando o upload estiver completo, a última declaração impressa na consola é "feita".

## <a name="review-the-new-search-index"></a>Reveja o novo Índice de Pesquisa

Uma vez concluído o upload, o Índice de Pesquisa está pronto a ser utilizado. Reveja o seu novo Índice.

1. No Código do Estúdio Visual, abra a extensão de Pesquisa Cognitiva Azure e selecione o seu recurso de Pesquisa.  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="No Código do Estúdio Visual, abra a extensão de Pesquisa Cognitiva Azure e abra o seu recurso de Pesquisa.":::

1. Expandir Índices, em seguida, `good-books` Documentos, em seguida, em seguida, selecione um doc para ver todos os dados específicos do documento.
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="Expandir Índices, em seguida, 'bons livros', em seguida, selecionar um doc.":::

## <a name="copy-your-search-resource-name"></a>Copie o nome do seu recurso de pesquisa

Note o nome do seu **recurso de pesquisa.** Necessitará disto para ligar a aplicação Azure Function ao seu recurso De pesquisa. 

> [!CAUTION]
> Embora possa sentir-se tentado a usar a sua chave de administração de pesquisa na Função Azure, isso não está a seguir o princípio do privilégio mínimo. A Função Azure utilizará a chave de consulta para se conformar com o mínimo privilégio. 

## <a name="next-steps"></a>Passos seguintes

[Implemente a sua Aplicação Web Estática](tutorial-javascript-deploy-static-web-app.md)