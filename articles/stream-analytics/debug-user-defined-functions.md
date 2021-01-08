---
title: Debug funções definidas pelo utilizador no Azure Stream Analytics
description: Este artigo descreve como depurar funções definidas pelo utilizador no Azure Stream Analytics.
author: jenssuessmeyer
ms.author: jenss
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/24/2020
ms.custom: devx-track-js
ms.openlocfilehash: 7f5a24e4d351d7c133251900a80c70094b84cc4e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019436"
---
# <a name="debug-user-defined-functions-in-azure-stream-analytics"></a>Debug funções definidas pelo utilizador no Azure Stream Analytics 

Quando as funções definidas pelo utilizador (UDF) não funcionam como espera, é necessário depurá-las para encontrar o problema. Pode depurar UDFs para os seus trabalhos stream Analytics quando executar os seus trabalhos localmente usando [Visual Studio Code](visual-studio-code-local-run-live-input.md) ou Visual [Studio](stream-analytics-vs-tools-local-run.md).

Quando executa o seu trabalho contra uma transmissão de entrada ao vivo localmente, ele imita apenas a execução do motor Azure Stream Analytics em um nó. Os testes locais de dados ao vivo não podem substituir o desempenho e teste de escala que faz na nuvem, mas poupa tempo durante os testes funcionais, não tendo de submeter o seu trabalho à nuvem sempre que quiser testar. Além disso, as políticas de tempo são desativadas para a execução local com dados locais ou de amostra, mas as políticas de tempo são suportadas para testes de dados ao vivo.

## <a name="pick-your-language"></a>Escolha a sua língua

Pode escrever UDFs para Azure Stream Analytics usando .NET (C#) ou JavaScript. 

### <a name="functions-in-c"></a>Funções em C # 

Ao [escrever .NET UDFs usando Visual Studio,](stream-analytics-edge-csharp-udf-methods.md)obtém o mesmo nível de suporte que faria para qualquer projeto de classe .NET. Este suporte inclui:

* Suporte de compilação, como verificação de sintaxe e suporte ao compilador.

* A capacidade de adicionar, construir e fazer referência a um projeto C# e artefactos na sua solução Stream Analytics. 

* Fácil reutilização do código encapsulado num projeto sharable. 

* Suporte de Debug no Estúdio Visual diretamente. Desaponte o projeto Stream Analytics como projeto de arranque e desponte pontos de rutura no código C#. Em seguida, prima **F5** para depurar o código C# como faria em qualquer outro projeto C#. 

### <a name="functions-in-javascript"></a>Funções em JavaScript

JavaScript é outra opção para criar funções no Stream Analytics. O código JavaScript é colocado diretamente na área de função do projeto Stream Analytics, o que dificulta a partilha entre projetos.

A compilação acontece quando o projeto Stream Analytics é compilado ou executado. A hipótese de encontrar um problema apenas em tempo de execução é maior. Não existe suporte de depuração para funções JavaScript diretamente no Stream Analytics.

## <a name="debug-options-for-javascript"></a>Opções de depurar para JavaScript

Como não existe suporte de depuração para funções JavaScript no Stream Analytics diretamente, pode depurar encapsulando a função num site HTML e obter a saída de lá.

O exemplo a seguir mostra-lhe como depurar UDFs JavaScript com algumas limitações num ambiente de tempo de execução integrado no [Código do Estúdio Visual.](quick-create-visual-studio-code.md)

### <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que o seu projeto Azure Stream Analytics tem os seguintes itens:

* Uma entrada 
* Uma saída 
* Uma consulta stream analytics (.asaql) 
* Uma configuração de trabalho stream Analytics (JobConfig.jsem)
* Um UDF JavaScript

### <a name="prepare-files"></a>Preparar ficheiros

Na imagem a seguir, o ficheiro de consulta *.asaql* inclui apenas a chamada para a UDF, *fxcharCount*. Esta alteração garante que ainda é capaz de compilar o projeto depois de serem feitas alterações.

> [!div class="mx-imgBorder"]
> ![Ficheiro de consulta de teste stream Analytics](./media/debug-user-defined-functions/asaql-file.png)

Crie uma pasta adicional em **Testes** para hospedar o ficheiro de teste, que é chamado para executar o teste com a função JavaScript. Neste exemplo, o nome da pasta é *fxcharCount* e o nome do teste é *Test_UDF.js*. 

A imagem abaixo mostra o código no ficheiro de teste, que carrega o ficheiro de função e executa a função. Este exemplo é simples, mas pode carregar ficheiros de dados de teste adicionais e fazer loop através de testes adicionais para obter a saída. A notação da chamada de função é pouco diferente das chamadas comuns porque o ficheiro é referenciado e não carregado no tempo de execução, tornando possível depurar. 

> [!div class="mx-imgBorder"]
> ![Ficheiro de teste stream Analytics](./media/debug-user-defined-functions/test-file.png)

Na função, adicione as seguintes linhas de código ao ficheiro para expor os métodos. Não afetam a capacidade de compilar o código no Código do Estúdio Visual.

```javascript
var methods = {};
methods.fxchartCount = fxchartCount;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Stream Analytics JavaScript UDF](./media/debug-user-defined-functions/udf-file.png)
  
## <a name="install-debug-support"></a>Instale suporte ao depurg

Para depurar, tem de [descarregar](https://nodejs.org/en/download/) e instalar **node.js**. Instale a versão correta de acordo com a plataforma que está a utilizar. Depois de instalar o tempo de execução node.js, reinicie o Código do Estúdio Visual para implementar as alterações. 

Selecione **Run and Debug** ou pressione **CTRL + SHIFT + D** para começar a depurar. Aparece uma caixa de combinação onde pode selecionar **node.js** como tempo de execução. Se tiver apenas node.js instalada, é utilizada por defeito. Deve ser capaz de passar pelo código e entrar no ficheiro de satélite, se necessário com F11. 

> [!div class="mx-imgBorder"]
> ![Stream Analytics run e depurar udf](./media/debug-user-defined-functions/run-debug-udf.png)

### <a name="debug-user-defined-aggregates"></a>Agregados definidos pelo utilizador Debug 

Pode utilizar o método de depurar para os UDFs JavaScript para depurar agregados definidos pelo utilizador (UDA). Neste exemplo, é adicionado um UDA ao ficheiro de consulta *.asaql* e ao ficheiro de teste.

Tal como acontece com a UDF, inclui uma chamada para a UDA para garantir que o projeto será compilado após as alterações serem feitas. 

> [!div class="mx-imgBorder"]
> ![Adicione uda a asaql](./media/debug-user-defined-functions/asaql-uda.png)

No ficheiro *Test_UDA.js,* refere o ficheiro UDA como fez com a UDF. Além disso, ligue, `main()` `init()` e . `accumulate()` . O `accumulate()` método é chamado em loop para colocar os valores na pilha de estado. O `computeresult()` método é chamado para compor a consulta final. 

> [!div class="mx-imgBorder"]
> ![Ficheiro de teste UDA](./media/debug-user-defined-functions/uda-test.png)

Tal como no exemplo da UDF, é necessário acrescentar à própria UDA alguns códigos para expor os métodos relevantes.

```javascript
var methods = {};
methods.main = main;
methods.init = main.init;
methods.accumulate = main.accumulate;
methods.computeResult = main.computeResult;
exports.data = methods;
``` 

> [!div class="mx-imgBorder"]
> ![Código adicionado à UDA](./media/debug-user-defined-functions/uda-expose-methods.png)

Selecione **Run and Debug** ou pressione **CTRL + SHIFT + D** para começar a depurar. Aparece uma caixa de combinação onde pode selecionar **node.js** como tempo de execução. Se tiver apenas node.js instalada, é utilizada por defeito. Deve ser capaz de passar pelo código e entrar no ficheiro de satélite, se necessário com F11.

> [!div class="mx-imgBorder"]
> ![Stream Analytics run e debug uda](./media/debug-user-defined-functions/run-debug-uda.png)


## <a name="next-steps"></a>Próximos passos

* [Desenvolver e depurar Azure Stream Analytics empregos localmente](develop-locally.md)
* [Debug Azure Stream Analytics consulta localmente usando diagrama de trabalho em Estúdio Visual](debug-locally-using-job-diagram.md)
* [Funções definidas pelo utilizador no Azure Stream Analytics](functions-overview.md)
 
