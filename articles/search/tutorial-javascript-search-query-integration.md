---
title: 'Tutorial JavaScript: Destaques de integração de pesquisa'
titleSuffix: Azure Cognitive Search
description: Compreenda as consultas de pesquisa javaScript SDK utilizadas no site ativado por pesquisa
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: cf4e1b1ecf209b587a45ca4c43607bfa95155aee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726939"
---
# <a name="4---search-integration-highlights"></a>4 - Destaques de integração de pesquisa

Nas lições anteriores, adicionou pesquisa a uma Aplicação Web Estática. Esta lição destaca os passos essenciais que estabelecem a integração. Se procura uma folha de batota sobre como integrar a pesquisa na sua aplicação JavaScript, este artigo explica o que precisa de saber.

## <a name="azure-sdk-azuresearch-documents"></a>Azure SDK @azure/search-documents 

A aplicação Function utiliza o Azure SDK para pesquisa cognitiva:

* P.S.A. [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* Documentação de Referência: [Biblioteca do Cliente](/javascript/api/overview/azure/search-documents-readme)

A aplicação Function autentica-se através do SDK para a API de Pesquisa Cognitiva baseada na nuvem utilizando o nome do seu recurso, chave de recursos e nome de índice. Os segredos são armazenados nas definições estáticas da Web App e puxados para a Função como variáveis ambientais. 

## <a name="configure-secrets-in-a-configuration-file"></a>Configure segredos em um arquivo de configuração

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/config.js" highlight="3,4" :::

## <a name="azure-function-search-the-catalog"></a>Função Azure: Pesse no catálogo

A `Search` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/index.js) toma um termo de pesquisa e pesquisa através dos documentos no Índice de Pesquisa, devolvendo uma lista de fósforos. 

O encaminhamento para a API de pesquisa está contido no [function.jsem](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/function.json) encadernações.

A Função Azure reusca a informação de configuração de Pesquisa e cumpre a consulta.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Search/index.js" highlight="4-9, 75" :::

## <a name="client-search-from-the-catalog"></a>Cliente: Pesquisar a partir do catálogo

Ligue para a Função Azure no cliente Reagir com o seguinte código. 

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Função Azure: Sugestões do catálogo

A `Suggest` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/index.js) toma um termo de pesquisa enquanto um utilizador está digitando e sugere termos de pesquisa como títulos de livro e autores através dos documentos no índice de pesquisa, devolvendo uma pequena lista de fósforos. 

O sugestão de pesquisa, `sg` é definido no ficheiro de [esquema](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/bulk-insert/good-books-index.json) utilizado durante o upload em massa.

O encaminhamento para a API sugere está contido no [function.jsde](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/function.json) encadernações.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Suggest/index.js" highlight="4-9, 21" :::

## <a name="client-suggestions-from-the-catalog"></a>Cliente: Sugestões do catálogo

Th Sugerir que a função API é chamada na app React `\src\components\SearchBar\SearchBar.js` como parte da inicialização de componentes:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Função Azure: Obtenha documento específico 

A `Lookup` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/index.js) pega num ID e devolve o objeto do documento do Índice de Pesquisa. 

O encaminhamento para a API de Procura está contido no [function.jssobre](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/function.json) encadernações.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Lookup/index.js" highlight="4-9, 17" :::

## <a name="client-get-specific-document"></a>Cliente: Obtenha documento específico 

Esta função API é chamada na app React `\src\pages\Details\Detail.js` como parte da inicialização de componentes:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>Passos seguintes

* [Dados do Índice Azure SQL](search-indexer-tutorial.md)
