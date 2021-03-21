---
title: Tutorial - Escreva funções definidas pelo utilizador C# para trabalhos de Azure Stream Analytics em Estúdio Visual (Pré-visualização)
description: Este tutorial mostra como escrever funções definidas pelo utilizador c# para trabalhos stream Analytics em Estúdio Visual.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/06/2018
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 851229e441aa2fbdf7b6eec05390c0ce2b149da2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020473"
---
# <a name="tutorial-write-a-c-user-defined-function-for-azure-stream-analytics-job-preview"></a>Tutorial: Escreva uma função definida pelo utilizador C# para o trabalho Azure Stream Analytics (Pré-visualização)

As funções definidas pelo utilizador (UDFs) de C# criadas no Visual Studio permitem-lhe expandir a linguagem de consulta do Azure Stream Analytics com as suas próprias funções. Pode reutilizar o código existente (incluindo DLLs) e utilizar a lógica de matemática ou complexa com o C#. Existem três formas de implementar UDFs: ficheiros CodeBehind num projeto do Stream Analytics, UDFs de um projeto em C# local ou UDFs de um pacote existente de uma conta de armazenamento. Este tutorial utiliza o método CodeBehind para implementar uma função de C# básica. A funcionalidade UDF para trabalhos stream Analytics está atualmente em pré-visualização e não deve ser usada em cargas de trabalho de produção.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar uma função definida pelo utilizador de C# através do CodeBehind.
> * Teste o seu trabalho stream analytics localmente.
> * Publique o seu trabalho no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, confirme que concluiu os seguintes pré-requisitos:

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Instale as [ferramentas do Stream Analytics para Visual Studio](stream-analytics-tools-for-visual-studio-install.md) e as cargas de trabalho **Desenvolvimento do Azure** ou **Processamento e armazenamento de Dados**.
* Veja o guia de [desenvolvimento stream analytics edge](stream-analytics-tools-for-visual-studio-edge-jobs.md) existente se estiver a construir um trabalho IoT Edge.

## <a name="create-a-container-in-your-azure-storage-account"></a>Criar um contentor na Conta de Armazenamento do Azure

O recipiente que criar será utilizado para armazenar o pacote C# compilado. Se criar uma função Edge, esta conta de armazenamento também será usada para implantar a embalagem no seu dispositivo IoT Edge. Utilize um contentor dedicado para cada tarefa do Stream Analytics. Não é suportada a reutilização do mesmo contentor para várias tarefas do Stream Analytics Edge. Se já tiver uma conta de armazenamento com contentores existentes, poderá utilizá-los. Caso contrário, precisará de [criar um novo contentor](../storage/blobs/storage-quickstart-blobs-portal.md). 

## <a name="create-a-stream-analytics-project-in-visual-studio"></a>Criar um projeto Stream Analytics em Estúdio Visual

1. Inicie o Visual Studio.

2. Selecione **Ficheiro > Novo > Projeto**.

3. Na lista de modelos à esquerda, selecione **Stream Analytics** e, em seguida, selecione **Azure Stream Analytics Edge Application** ou **Azure Stream Analytics Application**.

4.  Introduza o **Nome** do projeto, a **Localização** e o **Nome da solução** e selecione **OK**.

    ![Criar um projeto do Azure Stream Analytics Edge no Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>Configurar o caminho de assemblagem do pacote

1. Abra o Visual Studio e navegue até ao **Explorador de Soluções**.

2. Faça duplo clique no ficheiro de configuração da tarefa, `EdgeJobConfig.json`.

3. Expanda a secção **Configuração do Código Definido Pelo Utilizador** e preencha a configuração com os seguintes valores sugeridos:

   |**Definição**|**Valor Sugerido**|
   |-------|---------------|
   |Recurso global de definições de armazenamento|Escolha a origem de dados na conta atual|
   |Subscrição global de configurações de armazenamento| < o seu > de subscrição|
   |Conta global de armazenamento de configurações de armazenamento| < a sua conta de armazenamento >|
   |Recurso de definições de armazenamento de código personalizado|Escolha a origem de dados na conta atual|
   |Conta de armazenamento de definições de código personalizada|< a sua conta de armazenamento >|
   |Recipiente de definições de armazenamento de código personalizado|< o seu recipiente de armazenamento >|


## <a name="write-a-c-udf-with-codebehind"></a>Escrever UDF de C# com o CodeBehind
Um ficheiro CodeBehind é um ficheiro C# associado a um único script de consulta ASA. As ferramentas do Visual Studio zipam automaticamente o ficheiro CodeBehind e carregam-no para a sua conta de armazenamento do Azure após a submissão. Todas as classes têm de ser definidas como *públicas* e todos os objetos têm de ser definidos como *públicos estáticos*.

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

1. Descarregue o ficheiro de dados da [amostra do simulador de temperatura.](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json)

2. No **Explorador de Soluções**, expanda **Entradas**, clique com o botão direito do rato em **Input.json** e selecione **Adicionar Entrada Local**.

   ![Adicione entrada local ao trabalho de Stream Analytics no Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. Especifique o caminho do ficheiro de entrada local para os dados de exemplo que transferiu e selecione **Guardar**.

    ![Configuração de entrada local para trabalho de Stream Analytics em Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. Clique em **Executar Localmente** no editor de scripts. Assim que a execução local tenha guardado com êxito os resultados de saída, prima qualquer tecla para ver os resultados no formato de tabela. 

    ![Executar a tarefa do Azure Stream Analytics localmente com o Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. Também pode selecionar **Abrir Pasta de Resultados** para ver os ficheiros sem formato no formato JSON e CSV.

    ![Ver os resultados da tarefa do Azure Stream Analytics local com o Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>Depurar uma UDF
Pode depurar a UDF de C# localmente da mesma forma que depura o código C# padrão. 

1. Adicione pontos de interrupção à função C#.

    ![Adicionar pontos de rutura à função definida pelo utilizador stream Analytics no Estúdio Visual](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. Prima **F5** para iniciar a depuração. O programa irá parar nos seus pontos de interrupção, conforme esperado.

    ![Ver Stream Analytics resultados de depuragem de função definida pelo utilizador](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Publicar a tarefa no Azure
Após ter testado a sua consulta localmente, selecione **Submeter para o Azure** no editor de scripts para publicar a tarefa no Azure.

![Submeter a tarefa do Stream Analytics Edge para o Azure a partir do Visual Studio](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>Implementar em dispositivos IoT Edge
Se optar por construir um trabalho stream Analytics Edge, este pode agora ser implementado como um módulo IoT Edge. Siga o [início rápido do IoT Edge](../iot-edge/quickstart.md) para criar um Hub IoT, registar um dispositivo IoT Edge e instalar e iniciar o runtime do IoT Edge no dispositivo. Em seguida, siga o tutorial [implementar a tarefa](../iot-edge/tutorial-deploy-stream-analytics.md#deploy-the-job) para implementar a sua tarefa do Stream Analytics como um módulo do IoT Edge. 

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou uma função simples definida pelo utilizador C# utilizando o CodeBehind, publicou o seu trabalho para o Azure e implementou o trabalho para o dispositivo Azure ou IoT Edge. 

Para saber mais sobre as diferentes formas de utilizar funções definidas pelo utilizador C# para trabalhos stream Analytics, continue a este artigo:

> [!div class="nextstepaction"]
> [Escreva funções C# para Azure Stream Analytics](stream-analytics-edge-csharp-udf-methods.md)