---
title: Use cadernos Azure para analisar dados no Azure Data Explorer
description: Este tópico mostra-lhe como criar uma consulta usando um Caderno Azure
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 0f99e11be99f22feec73b72397b27522b90dbf49
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522410"
---
# <a name="use-azure-notebooks-to-analyze-data-in-azure-data-explorer"></a>Use cadernos Azure para analisar dados no Azure Data Explorer

[O Azure Notebooks](https://notebooks.azure.com/) é um serviço de nuvem Azure que simplifica a criação e partilha de [Cadernos Jupyter.](https://jupyter.org/) Os Cadernos Azure facilitam a combinação de documentação, código e os resultados da execução do código.

> [!Note]
> * O procedimento seguinte utiliza o cliente Python no ambiente dos Cadernos Azure para consultar o Azure Data Explorer. No entanto, também pode usar [o KQLmagic](https://docs.microsoft.com/azure/data-explorer/kqlmagic) para consultar o Azure Data Explorer.
> * Alguns utilizadores relataram problemas de autenticação usando o Edge; até que tais problemas sejam resolvidos, use um navegador diferente.

## <a name="create-a-project"></a>Criar um projeto

1. Abra [os Cadernos Azure](https://notebooks.azure.com/) no seu navegador e iniciar sessão.

1. Selecione o separador **My Projects** no cabeçalho. 

    [![](media/azurenotebooks/an-myprojects.png "My projects")](media/azurenotebooks/an-myprojects.png#lightbox)

1. Selecione **+ Novos Projetos.**
    
1. Na caixa de diálogo **Create New Project:**
    1. Introduza o Nome do **Projeto**.
    1. Limpe a caixa de verificação **pública.**
        >[!Important]
        > Se não limpar a caixa de verificação pública, o seu projeto será exposto na Internet aberta.
    1. Selecione **Criar**.
    
    ![Criar um novo projeto](media/azurenotebooks/an-create-new-project-blank.png)

    O ID do projeto é criado automaticamente.

## <a name="create-a-notebook"></a>Criar um bloco de notas

1. No seu novo projeto, crie um caderno. O caderno deve utilizar uma [linguagem apoiada](https://github.com/Azure/azure-kusto-python#minimum-requirements).
Para criar um caderno, selecione **+ Novo** e selecione **Notebook**.

    ![Criar novo caderno](media/azurenotebooks/an-create-new-notebook-menu.png) 

1. Na caixa de diálogo **Create New Notebook,** introduza o nome do caderno.

1. Selecione **Python 3.6** e selecione **New**.
    
    ![Criar novo caderno](media/azurenotebooks/an-create-new-notebook.png) 
    
1. No seu projeto, selecione o seu novo caderno.

    ![Selecione novo caderno](media/azurenotebooks/an-select-notebook.png)

    Espere até que o seu kernel Python ininicialize. Quando o ícone do círculo cheio muda para um ícone de círculo oco, pode proceder.

    ![Kernel inicializa](media/azurenotebooks/an-python-init-icon.png)

1. Para importar o módulo do sistema, introduza os seguintes comandos e selecione **Executar:**
    ```python
    import sys
    sys.version
    ```

    > [!Note]
    > Para executar uma célula, também pode premir Shift+Enter.

1.  Para importar classes SDK, insira os seguintes comandos e selecione **Executar:**
    ```python
    from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
    ```

1.  Para construir a cadeia de ligação e iniciar o cliente Kusto, introduza os seguintes comandos e selecione **Executar:**  
    ```python
    kcsb = KustoConnectionStringBuilder.with_aad_device_authentication("https://help.kusto.windows.net")
    kc = KustoClient(kcsb)
    kc.execute("Samples", ".show version")
    ```
1. No momento, abra um novo [https://aka.ms/devicelogin](https://aka.ms/devicelogin)separador de navegador para . 
   
1. Insira o código de autorização e@microsoft.cominscreva a sua conta AAD ( ). O cliente `kc` Kusto já pode autenticar o Azure Data Explorer usando a sua identidade.

1. Volte ao seu caderno para ver o resultado da autenticação. 

[![](media/azurenotebooks/an-python-commands.png "Python commands")](media/azurenotebooks/an-python-commands.png#lightbox)

## <a name="execute-a-kusto-query"></a>Execute uma consulta de Kusto

1. Insira a sua consulta Kusto e selecione **Executar**. Por exemplo:

    ```python
    query= "StormEvents | project State, EventType | take 10"
    response = kc.execute("Samples", query)
    for row in response.primary_results[0]:
        print(", ".join(row))
    ```    

[![](media/azurenotebooks/an-commands.png "Python commands")](media/azurenotebooks/an-commands.png#lightbox)

## <a name="next-steps"></a>Passos seguintes

* [Kusto-python GitHub repo](https://github.com/Azure/azure-kusto-python)
