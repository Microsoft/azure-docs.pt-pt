---
title: Teste um trabalho de Azure Stream Analytics localmente com dados de amostra usando código de estúdio visual
description: Este artigo descreve como testar consultas localmente com dados de amostra usando ferramentas de análise de Azure Stream para código de estúdio visual.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 11/10/2019
ms.topic: conceptual
ms.openlocfilehash: c29d0d9ecd856ee9611df21d23b1b2b763e24652
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75486472"
---
# <a name="test-stream-analytics-queries-locally-with-sample-data-using-visual-studio-code"></a>Teste Stream Analytics consulta localmente com dados de amostra usando código de estúdio visual

Pode utilizar ferramentas Azure Stream Analytics para O Código de Estúdio Visual para testar os seus trabalhos de Streaming Analytics localmente com dados de amostra. Pode encontrar os resultados da consulta em ficheiros JSON na pasta **LocalRunOutputs** do seu projeto.

## <a name="prerequisites"></a>Pré-requisitos

* Instale [o Núcleo .NET SDK](https://dotnet.microsoft.com/download) e reinicie o Código do Estúdio Visual.

* Use este [quickstart](quick-create-vs-code.md) para aprender a criar um trabalho stream analytics usando o Código de Estúdio Visual.

## <a name="prepare-sample-data"></a>Preparar dados da amostra

Tem de preparar primeiro os ficheiros de dados de entrada de amostras. Se já tiver alguns ficheiros de dados de amostras na sua máquina, pode saltar este passo e passar para o próximo.

1. Clique em **dados de Pré-visualização** no ficheiro de configuração de entrada a partir da linha superior. Alguns dados de entrada serão recolhidos do IoT Hub e mostrados na janela de pré-visualização. Note que isto pode demorar um pouco.

2. Assim que os dados forem mostrados, clique em **Guardar para** guardar os dados para um ficheiro local.

 ![Pré-visualizar entrada ao vivo](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-a-local-input"></a>Definir uma entrada local

1. Clique em **input.json** sob a pasta De Entradas no seu projeto Stream Analytics. Em seguida, **selecione Adicionar entrada local** a partir da linha superior.

    ![Adicione a entrada local do projeto](./media/quick-create-vs-code/add-input-from-project.png)

    Também pode utilizar **o Ctrl+Shift+P** para abrir a paleta de comando e introduzir **asa: Adicionar entrada**.

   ![Adicionar entrada stream analytics no código VS](./media/quick-create-vs-code/add-input.png)

2. Selecione **Entrada Local**.

    ![Adicione a entrada local ASA no código Do Estúdio Visual](./media/vscode-local-run/add-local-input.png)

3. Selecione **+ Nova Entrada Local**.

    ![Adicione uma nova entrada local DAA no código Visual Studio](./media/vscode-local-run/add-new-local-input.png)

4. Insira o mesmo pseudónimo que usou na sua consulta.

    ![Adicione um novo pseudónimo de entrada local ASA](./media/vscode-local-run/new-local-input-alias.png)

5. No ficheiro **LocalInput_Input.json** recentemente gerado, introduza o caminho do ficheiro onde está localizado o ficheiro de dados local.

    ![Insira o caminho de arquivo local no Estúdio Visual](./media/vscode-local-run/local-file-path.png)

6. Selecione Dados de **Pré-visualização** para pré-visualizar os dados de entrada. O tipo de serialização dos seus dados é automaticamente detetado se o seu JSON ou CSV. Utilize o seletor para visualizar os seus dados em **formato Tabela** ou **Cru.** O quadro seguinte é um exemplo de dados no **formato tabela:**

     ![Pré-visualizar dados locais em formato de tabela](./media/vscode-local-run/local-file-preview-table.png)

    O quadro seguinte é um exemplo de dados no **formato Raw:**

    ![Pré-visualizar dados locais em formato bruto](./media/vscode-local-run/local-file-preview-raw.png)

## <a name="run-queries-locally"></a>Executar consultas localmente

Volte ao seu editor de consulta e selecione **Run localmente**. Em seguida, selecione **Utilize a entrada local** da lista de dropdown.

![Selecione executar localmente no editor de consulta](./media/vscode-local-run/run-locally.png)

![Use a entrada local](./media/vscode-local-run/run-locally-use-local-input.png)

O resultado é mostrado na janela certa. Pode clicar em **Correr** para testar novamente. Também pode selecionar **Open in folder** para ver os ficheiros de resultados no explorador de ficheiros e abra-os ainda mais com outras ferramentas. Note que os ficheiros de resultados só estão disponíveis no formato JSON.

![Ver resultado da execução local](./media/vscode-local-run/run-locally-result.png)

## <a name="next-steps"></a>Passos seguintes

* [Test Azure Stream Analytics empregos localmente com entrada ao vivo usando Código de Estúdio Visual](visual-studio-code-local-run-live-input.md)

* [Explore os trabalhos do Azure Stream Analytics com código de estúdio visual (pré-visualização)](visual-studio-code-explore-jobs.md)
