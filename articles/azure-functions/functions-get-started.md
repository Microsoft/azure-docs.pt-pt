---
title: Introdução às Funções do Azure
description: Dê os primeiros passos para trabalhar com as Funções Azure.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 6aba58dad9853714bf26442592f74fc77e39765c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94975028"
---
# <a name="getting-started-with-azure-functions"></a>Introdução às Funções do Azure

## <a name="introduction"></a>Introdução

[As Funções Azure](./functions-overview.md) permitem implementar a lógica do seu sistema em blocos de código facilmente disponíveis. Estes blocos de código são chamados de "funções".

Use os seguintes recursos para começar.

::: zone pivot="programming-language-csharp"

| Ação | Recursos |
| --- | --- |
| **Criar a sua primeira função** | Utilizando uma das seguintes ferramentas:<br><br><li>[Visual Studio Code](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio](./create-first-function-vs-code-csharp.md)<li>[Linha de comandos](./create-first-function-cli-csharp.md) |
| **Ver uma função a funcionar** | <li>[Navegador Azure Samples](https://docs.microsoft.com/samples/browse/?languages=csharp&expanded=azure&products=azure-functions)<li>[Biblioteca Comunitária de Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **Explore um tutorial interativo**| <li>[Escolher a melhor tecnologia sem servidor do Azure para o seu cenário empresarial](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Quadro Bem Architected - Eficiência de desempenho](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Executar uma Função do Azure com acionadores](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>Consulte o Microsoft Learn para obter uma [lista completa de tutoriais interativos.](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)|
| **Saiba mais aprofundadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à procura<li>Explore os [diferentes métodos](./functions-deployment-technologies.md) de implementação disponíveis<li>Utilize ferramentas de [monitorização incorporadas](./functions-monitoring.md) para ajudar a analisar as suas funções<li>Leia a [referência linguística C#](./functions-dotnet-class-library.md)|

::: zone-end

::: zone pivot="programming-language-java"
| Ação | Recursos |
| --- | --- |
| **Criar a sua primeira função** | Utilizando uma das seguintes ferramentas:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[Função Java/Maven com terminal/comando](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[Ideia Intellij](./functions-create-maven-intellij.md) |
| **Ver uma função a funcionar** | <li>[Navegador Azure Samples](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=java)<li>[Biblioteca Comunitária de Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **Explore um tutorial interativo**| <li>[Escolher a melhor tecnologia sem servidor do Azure para o seu cenário empresarial](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Quadro Bem Architected - Eficiência de desempenho](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Desenvolver uma aplicação com o Maven Plugin para as Funções do Azure](https://docs.microsoft.com/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>Consulte o Microsoft Learn para obter uma [lista completa de tutoriais interativos.](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)|
| **Saiba mais aprofundadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à procura<li>Explore os [diferentes métodos](./functions-deployment-technologies.md) de implementação disponíveis<li>Utilize ferramentas de [monitorização incorporadas](./functions-monitoring.md) para ajudar a analisar as suas funções<li>Leia a [referência da língua java](./functions-reference-java.md)|
::: zone-end

::: zone pivot="programming-language-javascript"
| Ação | Recursos |
| --- | --- |
| **Criar a sua primeira função** | Utilizando uma das seguintes ferramentas:<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Node.js terminal/comando](./create-first-function-cli-java.md) |
| **Ver uma função a funcionar** | <li>[Navegador Azure Samples](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=javascript%2Ctypescript)<li>[Biblioteca Comunitária de Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **Explore um tutorial interativo** | <li>[Escolher a melhor tecnologia sem servidor do Azure para o seu cenário empresarial](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Quadro Bem Architected - Eficiência de desempenho](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Criar APIs Sem Servidor com Funções do Azure](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Criar a lógica sem servidor com as Funções do Azure](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Refatorizar APIs Node.js e Express para APIs sem servidor com as Funções do Azure](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>Consulte o Microsoft Learn para obter uma [lista completa de tutoriais interativos.](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)|
| **Saiba mais aprofundadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à procura<li>Explore os [diferentes métodos](./functions-deployment-technologies.md) de implementação disponíveis<li>Utilize ferramentas de [monitorização incorporadas](./functions-monitoring.md) para ajudar a analisar as suas funções<li>Leia a referência de linguagem [JavaScript](./functions-reference-node.md) ou [TypeScript](./functions-reference-node.md#typescript)|
::: zone-end

::: zone pivot="programming-language-powershell"
| Ação | Recursos |
| --- | --- |
| **Criar a sua primeira função** | <li>Usando [código de estúdio visual](./create-first-function-vs-code-powershell.md) |
| **Ver uma função a funcionar** | <li>[Navegador Azure Samples](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=powershell)<li>[Biblioteca Comunitária de Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **Explore um tutorial interativo** | <li>[Escolher a melhor tecnologia sem servidor do Azure para o seu cenário empresarial](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Quadro Bem Architected - Eficiência de desempenho](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Criar APIs Sem Servidor com Funções do Azure](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Criar a lógica sem servidor com as Funções do Azure](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Executar uma Função do Azure com acionadores](https://docs.microsoft.com/learn/modules/execute-azure-function-with-triggers/) <br><br>Consulte o Microsoft Learn para obter uma [lista completa de tutoriais interativos.](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)|
| **Saiba mais aprofundadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à procura<li>Explore os [diferentes métodos](./functions-deployment-technologies.md) de implementação disponíveis<li>Utilize ferramentas de [monitorização incorporadas](./functions-monitoring.md) para ajudar a analisar as suas funções<li>Leia a [referência linguística PowerShell)](./functions-reference-powershell.md)|
::: zone-end

::: zone pivot="programming-language-python"
| Ação | Recursos |
| --- | --- |
| **Criar a sua primeira função** | Utilizando uma das seguintes ferramentas:<br><br><li>[Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-python)<li>[Solicitação de terminal/comando](./functions-create-first-azure-function-azure-cli.md?pivots=programming-language-python) |
| **Ver uma função a funcionar** | <li>[Navegador Azure Samples](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-functions&languages=python)<li>[Biblioteca Comunitária de Azure](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **Explore um tutorial interativo** | <li>[Escolher a melhor tecnologia sem servidor do Azure para o seu cenário empresarial](https://docs.microsoft.com/learn/modules/serverless-fundamentals/)<li>[Quadro Bem Architected - Eficiência de desempenho](https://docs.microsoft.com/learn/modules/azure-well-architected-performance-efficiency/)<li>[Criar APIs Sem Servidor com Funções do Azure](https://docs.microsoft.com/learn/modules/build-api-azure-functions/)<li>[Criar a lógica sem servidor com as Funções do Azure](https://docs.microsoft.com/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>Consulte o Microsoft Learn para obter uma [lista completa de tutoriais interativos.](https://docs.microsoft.com/learn/browse/?expanded=azure&products=azure-functions)|
| **Saiba mais aprofundadamente** | <li>Saiba como as funções [aumentam ou diminuem automaticamente](./functions-scale.md) as instâncias para corresponder à procura<li>Explore os [diferentes métodos](./functions-deployment-technologies.md) de implementação disponíveis<li>Utilize ferramentas de [monitorização incorporadas](./functions-monitoring.md) para ajudar a analisar as suas funções<li>Leia a [referência da língua Python](./functions-reference-python.md)|
::: zone-end

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça a anatomia de uma aplicação Azure Functions](./functions-reference.md)
