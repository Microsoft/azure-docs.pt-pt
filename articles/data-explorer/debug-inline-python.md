---
title: Debug Kusto consulta linguagem inline Python usando código VS - Azure Data Explorer
description: Aprenda a depurar a linguagem de consulta Kusto (KQL) inline Python usando o Código VS.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: 96bd66f96b04bd7032d976ba9ebbbeb60c8415e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75444459"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Debug Kusto consulta linguagem inline Python usando código VS

O Azure Data Explorer suporta a execução do código Python incorporado na linguagem de consulta de Kusto utilizando o [plugin python.](/azure/kusto/query/pythonplugin) O tempo de execução do plugin está hospedado numa caixa de areia, um ambiente python isolado e seguro. A capacidade de plugin python() estende as funcionalidades nativas da linguagem da linguagem de Kusto com o enorme arquivo de pacotes OSS Python. Esta extensão permite-lhe executar algoritmos avançados, tais como machine learning, inteligência artificial, estatística e séries temporais como parte da consulta.

As ferramentas linguísticas de consulta Kusto não são convenientes para desenvolver e depurar algoritmos Python. Portanto, desenvolva o algoritmo no seu ambiente de desenvolvimento integrado python favorito, como Jupyter, PyCharm, VS ou VS Code. Quando o algoritmo estiver completo, copie e cole no KQL. Para melhorar e agilizar este fluxo de trabalho, o Azure Data Explorer suporta a integração entre os clientes kusto Explorer ou Web UI e o Código VS para a autoria e depuração do código Python inline KQL. 

> [!NOTE]
> Este fluxo de trabalho só pode ser utilizado para depurar tabelas de entrada relativamente pequenas (até poucos MB). Por isso, poderá ter de limitar a entrada para a depuração.  Se precisar de processar uma mesa grande, limite-a para depurar usando `| take`, `| sample`ou `where rand() < 0.x`.

## <a name="prerequisites"></a>Pré-requisitos

1. Instale a [Distribuição da Anaconda](https://www.anaconda.com/distribution/#download-section)Python. Em **Opções Avançadas,** selecione **Adicionar Anaconda à minha variável ambiente PATH**.
2. Instalar código de [estúdio visual](https://code.visualstudio.com/Download)
3. Instale [a extensão Python para o Código do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Execute a sua consulta na sua aplicação de cliente

1. Na sua aplicação de cliente, prefixe uma consulta contendo python inline com`set query_python_debug;`
1. Execute a consulta.
    * Kusto Explorer: O Código VS é lançado automaticamente com o script *debug_python.py.*
    * Kusto Web UI: 
        1. Baixe e guarde *debug_python.py,* *df.txt*e *kargs.txt*. Na janela, selecione **Permitir**. **Guarde** ficheiros em diretório selecionado. 

            ![Web UI descarrega os ficheiros de python inline](media/debug-inline-python/webui-inline-python.png)

        1. Clique à direita *debug_python.py* e abra com o código VS. 
        O script *debug_python.py* contém o código Python inline, da consulta KQL, prefixado pelo código do modelo para inicializar o quadro de dados de entrada de *df.txt* e o dicionário de parâmetros de *kargs.txt*.    
            
1. No código VS, lance o depurador de código VS: **Debug** > **Start Debugging (F5)**, selecione a configuração **Python.** O debugger lançará e quebrará automaticamente o ponto de rutura para depurar o código de intenção.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Como funciona a depuração inline Python no Código VS?

1. A consulta é analisada e executada no servidor até `| evaluate python()` que a cláusula necessária seja alcançada.
1. A caixa de areia Python é invocada mas em vez de executar o código, serializa a tabela de entrada, o dicionário dos parâmetros, e o código, e envia-os de volta para o cliente.
1. Estes três objetos são guardados em três ficheiros: *df.txt,* *kargs.txt,* e *debug_python.py* no diretório selecionado (Web UI) ou no diretório %TEMP% cliente (Kusto Explorer).
1. O código VS é lançado, pré-carregado com o ficheiro *debug_python.py* que contém um código prefixo para inicializar df e kargs dos respetivos ficheiros, seguido do script Python incorporado na consulta KQL.

## <a name="query-example"></a>Exemplo de consulta

1. Execute a seguinte consulta KQL na sua aplicação de cliente:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Consulte a tabela resultante:

    | x  | x4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Execute a mesma consulta KQL `set query_python_debug;`na sua aplicação de cliente usando:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. É lançado o Código VS:

    ![lançar código VS](media/debug-inline-python/launch-vs-code.png)

1. Vs Code debugs e imprime o 'resultado' na consola de depuração:

    ![Depuração de código VS](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Pode haver diferenças entre a imagem da caixa de areia Python e a sua instalação local. Verifique a imagem da caixa de [areia para obter pacotes específicos consultando o plugin](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
