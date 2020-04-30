---
title: Quickstart - Criar um trabalho de Stream Analytics utilizando o portal Azure
description: Este início rápido mostra como começar ao criar uma tarefa do Stream Analytics, configurar entradas e saídas, e definir uma consulta.
author: mamccrea
ms.author: mamccrea
ms.date: 06/21/2019
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 4abf5078a005f9d928397d9666e7f2bc55d65f19
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "75431558"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-portal"></a>Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure

Este início rápido mostra como começar ao criar uma tarefa do Stream Analytics. Neste arranque rápido, define um trabalho de Stream Analytics que lê dados de streaming em tempo real e filtra mensagens com uma temperatura superior a 27. O seu trabalho no Stream Analytics irá ler dados do IoT Hub, transformar os dados e escrever os dados de volta para um contentor no armazenamento de blob. Os dados de entrada utilizados neste quickstart são gerados por um simulador online Raspberry Pi. 

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/)

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho de Stream Analytics, deve preparar os dados de entrada. Os dados do sensor em tempo real são ingeridos para o IoT Hub, que mais tarde configurado como a entrada de trabalho. Para preparar os dados de entrada necessários pelo trabalho, preencha os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** > **Internet of Things** > **IoT Hub**.

3. No painel **IoT Hub,** introduza as seguintes informações:
   
   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Subscrição  | \<A sua subscrição\> |  Selecione a subscrição do Azure que pretende utilizar. |
   |Grupo de recursos   |   asaquickstart-resourcegroup  |   Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta. |
   |Região  |  \<Selecione a região mais próxima dos seus utilizadores\> | Selecione uma localização geográfica onde pode hospedar o seu Hub IoT. Utilize a localização mais próxima dos seus utilizadores. |
   |Nome do hub iot  | MyASAIoTHub  |   Selecione um nome para o seu Hub IoT.   |

   ![Criar um Hub IoT](./media/stream-analytics-quick-create-portal/create-iot-hub.png)

4. Selecione **Seguinte: Definir tamanho e escala**.

5. Selecione o seu **Escalão de preço e dimensionamento**. Para este arranque rápido, selecione o nível **F1 - Free** se ainda estiver disponível na sua subscrição. Para mais informações, consulte [os preços do IoT Hub.](https://azure.microsoft.com/pricing/details/iot-hub/)

   ![Tamanho e escala o seu Hub IoT](./media/stream-analytics-quick-create-portal/iot-hub-size-and-scale.png)

6. Selecione **Rever + criar**. Reveja as informações do IoT Hub e clique em **Criar**. O seu Hub IoT pode levar alguns minutos para criar. Pode monitorizar o progresso no painel **Notificações**.

7. No seu menu de navegação IoT Hub, clique em **Adicionar** em **dispositivos IoT**. Adicione um **ID do dispositivo** e clique em **Guardar**.

   ![Adicione um dispositivo ao seu Hub IoT](./media/stream-analytics-quick-create-portal/add-device-iot-hub.png)

8. Assim que o dispositivo for criado, abra o dispositivo a partir da lista de **dispositivos IoT.** Copie a **cadeia de ligação - chave primária** e guarde-a para um bloco de notas para usar mais tarde.

   ![Linha de ligação do dispositivo Copy IoT Hub](./media/stream-analytics-quick-create-portal/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Criar armazenamento de bolhas

1. A partir do canto superior esquerdo do portal Azure, selecione **Criar uma** > conta de**armazenamento** > **Storage account**de recursos .

2. No painel de conta de **armazenamento Create,** insira um nome de conta de armazenamento, localização e grupo de recursos. Escolha o mesmo local e grupo de recursos que o IoT Hub que criou. Em seguida, clique em **Rever + criar** para criar a conta.

   ![Criar conta de armazenamento](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. Assim que a sua conta de armazenamento for criada, selecione o azulejo **Blobs** no painel **overview.**

   ![Descrição geral da conta de armazenamento](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. Na página **do Serviço Blob,** selecione **Recipiente** e forneça um nome para o seu recipiente, como *o contentor1*. Deixe o **nível** de acesso público como **Privado (sem acesso anónimo)** e selecione **OK**.

   ![Criar contentor de blobs](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. Inicie sessão no Portal do Azure.

2. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.  

3. Selecione o trabalho **analytics** > **Stream Analytics** na lista de resultados.  

4. Preencha a página da tarefa do Stream Analytics com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Nome da tarefa   |  MyASAJob   |   Introduza um nome para identificar a tarefa do Stream Analytics. O nome da tarefa do Stream Analytics só pode conter carateres alfanuméricos, hífenes e carateres de sublinhado e tem de ter entre 3 a 63 carateres. |
   |Subscrição  | \<A sua subscrição\> |  Selecione a subscrição do Azure que quer utilizar para esta tarefa. |
   |Grupo de recursos   |   asaquickstart-resourcegroup  |   Selecione o mesmo grupo de recursos que o seu Hub IoT. |
   |Localização  |  \<Selecione a região mais próxima dos seus utilizadores\> | Selecione a localização geográfica onde pode alojar a tarefa do Stream Analytics. Utilize a localização mais próxima dos seus utilizadores para um melhor desempenho e reduzir os custos de transferência de dados. |
   |Unidades de transmissão em fluxo  | 1  |   As unidades de transmissão em fluxo representam os recursos informáticos que são necessários para executar uma tarefa. Por predefinição, este valor está definido como 1. Para saber mais sobre o dimensionamento de unidades de transmissão em fluxo, veja o artigo [Compreender e ajustar as unidades de transmissão em fluxo](stream-analytics-streaming-unit-consumption.md).   |
   |Ambiente de alojamento  |  Nuvem  |   As tarefas do Stream Analytics podem ser implementadas na cloud ou no Edge. A Cloud permite-lhe implantar para azure Cloud, e edge permite-lhe implementar para um dispositivo IoT Edge. |

   ![Criar tarefa](./media/stream-analytics-quick-create-portal/create-asa-job.png)

5. Selecione a caixa **Afixar ao dashboard** para colocar a tarefa no seu dashboard e, em seguida, selecione **Criar**.  

6. Deve ver uma *Implementação em andamento...* notificação exibida na parte superior direita da janela do seu navegador. 

## <a name="configure-job-input"></a>Configurar a entrada da tarefa

Nesta secção, irá configurar uma entrada do dispositivo IoT Hub para o trabalho stream analytics. Utilize o Hub IoT que criou na secção anterior do quickstart.

1. Navegue para a tarefa do Stream Analytics.  

2. Selecione **Inputs** > **Adicionar fluxo** > de entrada**IoT Hub**.  

3. Preencha a página **IoT Hub** com os seguintes valores:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada  |  IoTHubInput   |  Introduza um nome para identificar a entrada da tarefa.   |
   |Subscrição   |  \<A sua subscrição\> |  Selecione a subscrição do Azure que tem a conta de armazenamento que criou. A conta de armazenamento pode estar na mesma subscrição ou numa diferente. Este exemplo assume que criou a conta de armazenamento na mesma subscrição. |
   |IoT Hub  |  MyASAIoTHub |  Introduza o nome do Hub IoT que criou na secção anterior. |

4. Mantenha as outras opções com os valores predefinidos e selecione **Guardar** para guardar as definições.  

   ![Configurar dados de entrada](./media/stream-analytics-quick-create-portal/configure-asa-input.png)
 
## <a name="configure-job-output"></a>Configurar a saída da tarefa

1. Navegue para a tarefa do Stream Analytics que criou anteriormente.  

2. Selecione **Saídas** > **Adicione** > **armazenamento Blob**.  

3. Preencha a página **Armazenamento de blobs** com os seguintes valores:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de saída |   BlobOutput   |   Introduza um nome para identificar a saída da tarefa. |
   |Subscrição  |  \<A sua subscrição\>  |  Selecione a subscrição do Azure que tem a conta de armazenamento que criou. A conta de armazenamento pode estar na mesma subscrição ou numa diferente. Este exemplo assume que criou a conta de armazenamento na mesma subscrição. |
   |Conta de armazenamento |  asaquickstartstorage |   Escolha ou introduza o nome da conta de armazenamento. Os nomes de contas de armazenamento são detetados automaticamente se forem criados na mesma subscrição.       |
   |Contentor |   container1  |  Selecione o contentor existente que criou na conta de armazenamento.   |

4. Mantenha as outras opções com os valores predefinidos e selecione **Guardar** para guardar as definições.  

   ![Configurar a saída](./media/stream-analytics-quick-create-portal/configure-asa-output.png)
 
## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

1. Navegue para a tarefa do Stream Analytics que criou anteriormente.  

2. Selecione **Consulta** e atualize a consulta da seguinte forma:  

   ```sql
   SELECT *
   INTO BlobOutput
   FROM IoTHubInput
   HAVING Temperature > 27
   ```

3. Neste exemplo, a consulta lê os dados do IoT Hub e copia-os para um novo ficheiro na bolha. Selecione **Guardar**.  

   ![Configurar a transformação da tarefa](./media/stream-analytics-quick-create-portal/add-asa-query.png)

## <a name="run-the-iot-simulator"></a>Executar o simulador IoT

1. Abra o [Simulador Online Raspberry Pi Azure IoT](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Substitua o espaço reservado na Linha 15 pela cadeia de ligação do dispositivo Azure IoT Hub que guardou numa secção anterior.

3. Clique em **Executar**. A saída deve mostrar os dados do sensor e as mensagens que estão a ser enviadas para o seu Hub IoT.

   ![Simulador Online do Azure IoT Raspberry Pi](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

1. Regresse à página de descrição geral da tarefa e selecione **Iniciar**.

2. Em **funções de início**, selecione **Agora**, para o campo de tempo de início de saída de **trabalho.** Em seguida, selecione **Começar** a trabalhar.

3. Após alguns minutos, no portal, localize a conta de armazenamento e o contentor que configurou como saída da tarefa. Agora, pode ver o ficheiro de saída no contentor. A tarefa demora alguns minutos a iniciar pela primeira vez. Depois de ser iniciada, continuará a ser executada à medida que os dados são recebidos.  

   ![Saída transformada](./media/stream-analytics-quick-create-portal/check-asa-results.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, o trabalho do Stream Analytics e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este início rápido ao utilizar os seguintes passos:

1. No menu do lado esquerdo no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou uma tarefa simples do Stream Analytics com o portal do Azure. Também pode implementar trabalhos stream analytics usando [PowerShell,](stream-analytics-quick-create-powershell.md) [Visual Studio](stream-analytics-quick-create-vs.md)e Visual [Studio Code](quick-create-vs-code.md).

Para saber mais sobre como configurar outras origens de entrada e efetuar a deteção em tempo real, avance para o seguinte artigo:

> [!div class="nextstepaction"]
> [Deteção de fraudes em tempo real com o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
