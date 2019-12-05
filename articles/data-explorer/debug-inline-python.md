---
title: Depurar Python embutido em linguagem de consulta Kusto usando o VS Code-Azure Data Explorer
description: Saiba como depurar Python embutido em KQL (linguagem de consulta Kusto) usando o VS Code.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/04/2019
ms.openlocfilehash: f0bf81ce6392e1650d9986d81a55eac9c416c24d
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74822906"
---
# <a name="debug-kusto-query-language-inline-python-using-vs-code"></a>Depurar Python embutido em linguagem de consulta Kusto usando o VS Code

O Azure Data Explorer dá suporte à execução de código Python inserido na linguagem de consulta Kusto usando o [plug-in Python ()](/azure/kusto/query/pythonplugin). O tempo de execução do plug-in é hospedado em uma área restrita, um ambiente Python seguro e isolado. O recurso de plug-in Python () estende as funcionalidades nativas da linguagem de consulta Kusto com o enorme arquivo de pacotes python do OSS. Essa extensão permite que você execute algoritmos avançados, como aprendizado de máquina, inteligência artificial, estatística e série temporal, como parte da consulta.

As ferramentas de linguagem de consulta Kusto não são convenientes para desenvolver e depurar algoritmos de Python. Portanto, desenvolva o algoritmo em seu ambiente favorito de desenvolvimento integrado do Python, como Jupyter, PyCharm, VS ou VS Code. Quando o algoritmo for concluído, copie e cole em KQL. Para melhorar e simplificar esse fluxo de trabalho, o Azure Data Explorer dá suporte à integração entre o Kusto Explorer ou os clientes de interface do usuário da Web e VS Code para a criação e a depuração de código Python embutido no KQL. 

> [!NOTE]
> Esse fluxo de trabalho só pode ser usado para depurar tabelas de entrada relativamente pequenas (até alguns MB). Portanto, talvez seja necessário limitar a entrada para depuração.  Se você precisar processar uma tabela grande, limite-a para depuração usando `| take`, `| sample`ou `where rand() < 0.x`.

## <a name="prerequisites"></a>Pré-requisitos

1. Instale a [distribuição](https://www.anaconda.com/distribution/#download-section)do Python Anaconda. Em **Opções avançadas**, selecione **Adicionar Anaconda à variável de ambiente meu caminho**.
2. Instalar o [Visual Studio Code](https://code.visualstudio.com/Download)
3. Instale [a extensão do Python para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

## <a name="run-your-query-in-your-client-application"></a>Executar a consulta em seu aplicativo cliente

1. No aplicativo cliente, Prefixe uma consulta que contém Python embutido com `set query_python_debug;`
1. Execute a consulta.
    * Kusto Explorer: VS Code é iniciado automaticamente com o script *debug_python. py* .
    * Interface do usuário da Web do amKusto: 
        1. Baixe e salve *debug_python. py*, *DF. txt*e *kargs. txt*. Em janela, selecione **permitir**. **Salvar** arquivos no diretório selecionado. 

            ![Interface do usuário da Web baixa os arquivos Python embutidos](media/debug-inline-python/webui-inline-python.png)

        1. Clique com o botão direito do mouse em *debug_python. py* e abra com o vs Code. 
        O script *debug_python. py* contém o código Python embutido, da consulta KQL, prefixado pelo código de modelo para inicializar o quadro de dados de entrada de *DF. txt* e o dicionário de parâmetros de *kargs. txt*.    
            
1. No VS Code, inicie o depurador do VS Code: **Debug** > **iniciar a depuração (F5)** , selecione configuração do **python** . O depurador será iniciado e automaticamente o ponto de interrupção para depurar o código embutido.

### <a name="how-does-inline-python-debugging-in-vs-code-work"></a>Como a depuração em Python embutida no VS Code funciona?

1. A consulta é analisada e executada no servidor até que a cláusula de `| evaluate python()` necessária seja atingida.
1. A área restrita do Python é invocada, mas em vez de executar o código, ela serializa a tabela de entrada, o dicionário de parâmetros e o código e os envia de volta ao cliente.
1. Esses três objetos são salvos em três arquivos: *DF. txt*, *kargs. txt*e *debug_python. py* no diretório selecionado (interface do usuário da Web) ou no diretório% Temp% do cliente (Kusto Explorer).
1. O VS Code é iniciado, pré-carregado com o arquivo *debug_python. py* que contém um código de prefixo para inicializar o DF e o kargs de seus respectivos arquivos, seguido pelo script Python inserido na consulta KQL.

## <a name="query-example"></a>Exemplo de consulta

1. Execute a seguinte consulta KQL em seu aplicativo cliente:

    ```kusto
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

    Consulte a tabela resultante:

    | x  | X4  |
    |---------|---------|
    | 1     |   1      |
    | 2     |   16      |
    | 3     |   81      |
    | 4     |    256     |
    
1. Execute a mesma consulta KQL em seu aplicativo cliente usando `set query_python_debug;`:

    ```kusto
    set query_python_debug;
    range x from 1 to 4 step 1
    | evaluate python(typeof(*, x4:int), 
    'exp = kargs["exp"]\n'
    'result = df\n'
    'result["x4"] = df["x"].pow(exp)\n'
    , pack('exp', 4))
    ```

1. VS Code é iniciado:

    ![iniciar o VS Code](media/debug-inline-python/launch-vs-code.png)

1. VS Code debugs e imprime o dataframe ' resultado ' no console de depuração:

    ![Depuração do VS Code](media/debug-inline-python/debug-vs-code.png)

> [!NOTE]
> Pode haver diferenças entre a imagem de área restrita do Python e sua instalação local. [Verifique a imagem de área restrita para obter pacotes específicos consultando o plug-in](https://github.com/Azure/azure-kusto-analytics-lib/blob/master/Utils/functions/get_modules_version.csl).
