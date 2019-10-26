---
title: Gravar C# funções definidas pelo usuário para trabalhos do Azure Stream Analytics Edge
description: Saiba como escrever funções definidas pelo utilizador de C# para tarefas do Stream Analytics Edge no Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 2ddb5f6c57c49b33c992b13f38c969cbc5e91225
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72925625"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-edge-job-preview"></a>Tutorial: gravar uma C# função definida pelo usuário para Azure Stream Analytics trabalho de borda (versão prévia)

As funções definidas pelo utilizador (UDFs) de C# criadas no Visual Studio permitem-lhe expandir a linguagem de consulta do Azure Stream Analytics com as suas próprias funções. Pode reutilizar o código existente (incluindo DLLs) e utilizar a lógica de matemática ou complexa com o C#. Existem três formas de implementar UDFs: ficheiros CodeBehind num projeto do Stream Analytics, UDFs de um projeto em C# local ou UDFs de um pacote existente de uma conta de armazenamento. Este tutorial utiliza o método CodeBehind para implementar uma função de C# básica. A funcionalidade UDF das tarefas do Stream Analytics Edge está atualmente em pré-visualização e não deve ser utilizada em cargas de trabalho de produção.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma função definida pelo utilizador de C# através do CodeBehind.
> * Testar a tarefa do Stream Analytics Edge localmente.
> * Publicar a tarefa do Edge no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que concluiu os seguintes pré-requisitos:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Instale as [ferramentas do Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio-install.md) e as cargas de trabalho **Desenvolvimento do Azure** ou **Processamento e armazenamento de Dados**.
* Leia o [Guia de desenvolvimento do Stream Analytics Edge](stream-analytics-tools-for-visual-studio-edge-jobs.md) existente.

## <a name="create-a-container-in-your-azure-storage-account"></a>Criar um contentor na Conta de Armazenamento do Azure

O contentor que criar servirá para armazenar o pacote de C# compilado e implementar o pacote no seu dispositivo IoT Edge. Utilize um contentor dedicado para cada tarefa do Stream Analytics. Não é suportada a reutilização do mesmo contentor para várias tarefas do Stream Analytics Edge. Se já tiver uma conta de armazenamento com contentores existentes, poderá utilizá-los. Caso contrário, precisará de [criar um novo contentor](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 

## <a name="create-a-stream-analytics-edge-project-in-visual-studio"></a>Criar um projeto do Stream Analytics Edge no Visual Studio

1. Inicie o Visual Studio.

2. Selecione **Ficheiro > Novo > Projeto**.

3. Na lista de modelos no lado esquerdo, selecione **Stream Analytics** e, em seguida, selecione **Aplicação do Azure Stream Analytics Edge**.

4.  Introduza o **Nome** do projeto, a **Localização** e o **Nome da solução** e selecione **OK**.

    ![Criar um projeto do Azure Stream Analytics Edge no Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Configurar o caminho de assemblagem do pacote

1. Abra o Visual Studio e navegue até ao **Explorador de Soluções**.

2. Faça duplo clique no ficheiro de configuração da tarefa, `EdgeJobConfig.json`.

3. Expanda a secção **Configuração do Código Definido Pelo Utilizador** e preencha a configuração com os seguintes valores sugeridos:

    |**Definição**  |**Valor sugerido**  |
    |---------|---------|
    |Origem da Assemblagem  |  Referência do Projeto Local ou CodeBehind   |
    |Recurso  |  Escolher os dados na conta atual   |
    |Subscrição  |  Escolha a sua subscrição.   |
    |Conta de Armazenamento  |  Escolha a sua conta de armazenamento.   |
    |Contentor  |  Escolha o contentor que criou na conta de armazenamento.   |

    ![Configuração da tarefa do Azure Stream Analytics Edge no Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-edge-job-config.png)


## <a name="write-a-c-udf-with-codebehind"></a>Escrever UDF de C# com o CodeBehind
Um ficheiro CodeBehind é um ficheiro C# associado a um único script de consulta do ASA Edge. As ferramentas do Visual Studio zipam automaticamente o ficheiro CodeBehind e carregam-no para a sua conta de armazenamento do Azure após a submissão. Todas as classes têm de ser definidas como *públicas* e todos os objetos têm de ser definidos como *públicos estáticos*.

1. No **Explorador de Soluções**, expanda **Script.asql** para localizar o ficheiro CodeBehind **Script.asaql.cs**.

2. Substitua o código pelo exemplo seguinte:

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>Implementar a UDF

1. No **Explorador de Soluções**, abra o ficheiro **Script.asaql**.

2. Substitua a consulta existente pela seguinte:

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>Testes locais

1. Transfira o [ficheiro de dados de exemplo do simulador de temperatura](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json) do Edge.

2. No **Explorador de Soluções**, expanda **Entradas**, clique com o botão direito do rato em **Input.json** e selecione **Adicionar Entrada Local**.

   ![Adicionar entrada local para Stream Analytics trabalho no Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Especifique o caminho do ficheiro de entrada local para os dados de exemplo que transferiu e selecione **Guardar**.

    ![Configuração de entrada local para Stream Analytics trabalho no Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. Clique em **Executar Localmente** no editor de scripts. Assim que a execução local tenha guardado com êxito os resultados de saída, prima qualquer tecla para ver os resultados no formato de tabela. 

    ![Executar a tarefa do Azure Stream Analytics localmente com o Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. Também pode selecionar **Abrir Pasta de Resultados** para ver os ficheiros sem formato no formato JSON e CSV.

    ![Ver os resultados da tarefa do Azure Stream Analytics local com o Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Depurar uma UDF
Pode depurar a UDF de C# localmente da mesma forma que depura o código C# padrão. 

1. Adicione pontos de interrupção à função C#.

    ![Adicionar pontos de interrupção para Stream Analytics função definida pelo usuário no Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Prima **F5** para iniciar a depuração. O programa irá parar nos seus pontos de interrupção, conforme esperado.

    ![Exibir Stream Analytics resultados da depuração da função definida pelo usuário](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Publicar a tarefa no Azure
Após ter testado a sua consulta localmente, selecione **Submeter para o Azure** no editor de scripts para publicar a tarefa no Azure.

![Submeter a tarefa do Stream Analytics Edge para o Azure a partir do Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Implementar em dispositivos IoT Edge
A tarefa do Stream Analytics está agora pronta para ser implementada como um módulo do IoT Edge. Siga o [início rápido do IoT Edge](https://docs.microsoft.com/azure/iot-edge/quickstart) para criar um Hub IoT, registar um dispositivo IoT Edge e instalar e iniciar o runtime do IoT Edge no dispositivo. Em seguida, siga o tutorial [implementar a tarefa](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job) para implementar a sua tarefa do Stream Analytics como um módulo do IoT Edge. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função definida pelo utilizador de C# simples com o CodeBehind, publicou a sua tarefa no Azure e implementou a tarefa em dispositivos IoT Edge através do portal do Hub IoT. 

Para obter mais informações sobre as diferentes formas de utilizar as funções definidas pelo utilizador de C# para tarefas do Stream Analytics Edge, veja este artigo:

> [!div class="nextstepaction"]
> [Funções C# de gravação para Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)
