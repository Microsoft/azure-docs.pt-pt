---
title: Teste um trabalho de Azure Stream Analytics localmente com dados de amostra usando Código de Estúdio Visual
description: Este artigo descreve como testar consultas localmente com dados de amostra usando Azure Stream Analytics Tools for Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: how-to
ms.openlocfilehash: bbd83fb3ef3225fc19c48bb4c5962d6559cf32f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97708454"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Teste Stream Analytics consultas localmente com dados de amostra usando Código de Estúdio Visual

Pode utilizar ferramentas Azure Stream Analytics para o Código do Estúdio Visual para testar os seus trabalhos stream Analytics localmente com dados de amostragem. Pode encontrar os resultados da consulta nos ficheiros JSON na pasta **LocalRunOuts** do seu projeto.

## <a name="prerequisites"></a>Pré-requisitos

* Instale [.NET core SDK](https://dotnet.microsoft.com/download) e reinicie o Código do Estúdio Visual.

* Use este [quickstart](quick-create-visual-studio-code.md) para aprender a criar um trabalho stream Analytics usando o Código de Estúdio Visual.

## <a name="prepare-sample-data"></a>Preparar dados de amostra

Primeiro, tem de preparar os ficheiros de dados de entrada de amostras. Se já tiver alguns ficheiros de dados de amostra na sua máquina, pode saltar este passo e passar para o próximo.

1. Clique em **Pré-visualizar dados** no ficheiro de configuração de entrada a partir da linha superior. Alguns dados de entrada serão recolhidos do IoT Hub e mostrados na janela de pré-visualização. Note que isto pode demorar um pouco.

2. Assim que os dados são mostrados, clique em **Guardar como** guardar os dados para um ficheiro local.

 ![Pré-visualização da entrada ao vivo](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Definir uma entrada local

1. Clique **input.jsna** pasta Entradas no seu projeto Stream Analytics. Em seguida, **selecione Adicione** a entrada local da linha superior.

    ![Adicionar entrada local do projeto](./media/quick-create-visual-studio-code/add-input-from-project.png)

    Também pode utilizar **ctrl+Shift+P** para abrir a paleta de comando e introduzir **ASA: Adicionar Entrada**.

   ![Adicionar entrada Stream Analytics no Código VS](./media/quick-create-visual-studio-code/add-input.png)

2. Selecione **Entrada Local**.

    ![Adicione entrada local asA no Código do Estúdio Visual](./media/vscode-local-run/add-local-input.png)

3. Selecione **+ Nova Entrada Local**.

    ![Adicione uma nova entrada local da ASA no Código do Estúdio Visual](./media/vscode-local-run/add-new-local-input.png)

4. Introduza o mesmo pseudónimo de entrada que usou na sua consulta.

    ![Adicione um novo pseudónimo de entrada local ASA](./media/vscode-local-run/new-local-input-alias.png)

5. NoLocalInput_Input.jsrecentemente gerado **no** ficheiro, insira o caminho do ficheiro onde o seu ficheiro de dados local está localizado.

    ![Insira o caminho do arquivo local no Estúdio Visual](./media/vscode-local-run/local-file-path.png)

6. Selecione **Dados de Pré-visualização** para visualizar os dados de entrada. O tipo de serialização dos seus dados é automaticamente detetado se o seu JSON ou CSV. Utilize o seletor para ver os seus dados no formato **Tabela** ou **Raw.** A tabela a seguir é um exemplo de dados no **formato quadro:**

     ![Pré-visualizar dados locais no formato de tabela](./media/vscode-local-run/local-file-preview-table.png)

    A tabela a seguir é um exemplo de dados no **formato Raw:**

    ![Pré-visualizar dados locais em formato bruto](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Executar consultas localmente

Volte ao seu editor de consulta e selecione **Executar localmente.** Em seguida, **selecione Utilize** a entrada local da lista de retiradas.

![Selecione executar localmente no editor de consulta](./media/vscode-local-run/run-locally.png)

![Use a entrada local](./media/vscode-local-run/run-locally-use-local-input.png)

O resultado é mostrado na janela certa. Pode clicar em **Executar** para testar novamente. Também pode selecionar **Abrir na pasta** para ver os ficheiros de resultados no explorador de ficheiros e abri-los ainda mais com outras ferramentas. Note que os ficheiros de resultados só estão disponíveis no formato JSON.

![Ver resultado de execução local](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Passos seguintes

* [Teste Azure Stream Analytics empregos localmente com entrada ao vivo usando Código de Estúdio Visual](visual-studio-code-local-run-live-input.md)

* [Explore os trabalhos do Azure Stream Analytics com o Código do Estúdio Visual (Preview)](visual-studio-code-explore-jobs.md)
