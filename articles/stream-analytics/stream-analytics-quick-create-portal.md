---
title: Criar uma tarefa do Stream Analytics com o portal do Azure | Microsoft Docs
description: Este início rápido mostra como começar ao criar uma tarefa do Stream Analytics, configurar entradas e saídas, e definir uma consulta.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 06/21/2019
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: e05d293760b88cd02fdffae60e762f040a4d1311
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449238"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-portal"></a>Início rápido: Criar uma tarefa de Stream Analytics com o portal do Azure

Este início rápido mostra como começar ao criar uma tarefa do Stream Analytics. Neste início rápido, vai definir uma tarefa de Stream Analytics que lê dados de transmissão em fluxo em tempo real e filtros de mensagens com uma temperatura superior a 27. A tarefa de Stream Analytics irá ler dados a partir do IoT Hub, transformá-los e escrever os dados de volta para um contentor no armazenamento de Blobs. Os dados de entrada utilizados neste início rápido são gerados por um simulador online Raspberry Pi. 

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir a tarefa do Stream Analytics, deve preparar os dados de entrada. Os dados de sensores em tempo real são ingeridos para o IoT Hub, que posteriormente configurado como a tarefa de entrada. Para preparar os dados de entrada necessários para a tarefa, conclua os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.

3. Na **IoT Hub** painel, introduza as seguintes informações:
   
   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Subscrição  | \<A sua subscrição\> |  Selecione a subscrição do Azure que pretende utilizar. |
   |Grupo de recursos   |   asaquickstart-resourcegroup  |   Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta. |
   |Região  |  \<Selecione a região mais próxima dos seus utilizadores\> | Selecione uma localização geográfica onde pode alojar o seu IoT Hub. Utilize a localização mais próxima dos seus utilizadores. |
   |Nome do IoT Hub  | MyASAIoTHub  |   Selecione um nome para o seu IoT Hub.   |

   ![Criar um Hub IoT](./media/stream-analytics-quick-create-portal/create-iot-hub.png)

4. Selecione **seguinte: Definir o tamanho e a escala**.

5. Selecione o seu **Escalão de preço e dimensionamento**. Neste início rápido, selecione o **F1 - gratuito** camada se ela ainda está disponível na sua subscrição. Para obter mais informações, consulte [preços do IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Redimensionar e dimensione o seu IoT Hub](./media/stream-analytics-quick-create-portal/iot-hub-size-and-scale.png)

6. Selecione **Rever + criar**. Reveja as suas informações do IoT Hub e clique em **criar**. O IoT Hub pode demorar alguns minutos a criar. Pode monitorizar o progresso no painel **Notificações**.

7. No menu de navegação do IoT Hub, clique em **Add** sob **dispositivos IoT**. Adicionar uma **ID do dispositivo** e clique em **guardar**.

   ![Adicionar um dispositivo ao IoT Hub](./media/stream-analytics-quick-create-portal/add-device-iot-hub.png)

8. Depois do dispositivo é criado, abra o dispositivo a partir da **dispositivos IoT** lista. Copiar o **cadeia de ligação – chave primária** e guarde-o para um bloco de notas para utilizar mais tarde.

   ![Copie a cadeia de ligação de dispositivos do IoT Hub](./media/stream-analytics-quick-create-portal/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Criar armazenamento de BLOBs

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**.

2. Na **criar conta de armazenamento** painel, introduza um grupo de recursos, localização e nome da conta de armazenamento. Escolha a mesma localização e grupo de recursos como o IoT Hub que criou. Em seguida, clique em **rever + criar** para criar a conta.

   ![Criar conta de armazenamento](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. Depois de criar a conta de armazenamento, selecione o **Blobs** mosaico a **descrição geral** painel.

   ![Descrição geral da conta de armazenamento](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. Do **serviço Blob** página, selecione **contentor** e indique um nome para o contentor, como *container1*. Deixe o **nível de acesso público** como **privado (sem acesso anónimo)** e selecione **OK**.

   ![Criar contentor de blobs](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. Inicie sessão no Portal do Azure.

2. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.  

3. Selecione **Analytics** > **tarefa do Stream Analytics** na lista de resultados.  

4. Preencha a página da tarefa do Stream Analytics com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Nome da tarefa   |  MyASAJob   |   Introduza um nome para identificar a tarefa do Stream Analytics. O nome da tarefa do Stream Analytics só pode conter carateres alfanuméricos, hífenes e carateres de sublinhado e tem de ter entre 3 a 63 carateres. |
   |Subscrição  | \<A sua subscrição\> |  Selecione a subscrição do Azure que quer utilizar para esta tarefa. |
   |Grupo de recursos   |   asaquickstart-resourcegroup  |   Selecione o mesmo grupo de recursos como o seu IoT Hub. |
   |Location  |  \<Selecione a região mais próxima dos seus utilizadores\> | Selecione a localização geográfica onde pode alojar a tarefa do Stream Analytics. Utilize a localização mais próxima dos seus utilizadores para um melhor desempenho e reduzir os custos de transferência de dados. |
   |Unidades de transmissão em fluxo  | 1  |   As unidades de transmissão em fluxo representam os recursos informáticos que são necessários para executar uma tarefa. Por predefinição, este valor está definido como 1. Para saber mais sobre o dimensionamento de unidades de transmissão em fluxo, veja o artigo [Compreender e ajustar as unidades de transmissão em fluxo](stream-analytics-streaming-unit-consumption.md).   |
   |Ambiente de alojamento  |  Nuvem  |   As tarefas do Stream Analytics podem ser implementadas na cloud ou no Edge. Cloud permite-lhe implementar no Azure Cloud e o Edge permite-lhe implementar um dispositivo IoT Edge. |

   ![Criar tarefa](./media/stream-analytics-quick-create-portal/create-asa-job.png)

5. Selecione a caixa **Afixar ao dashboard** para colocar a tarefa no seu dashboard e, em seguida, selecione **Criar**.  

6. Deverá ver um *implementação em curso...*  notificação apresentada no canto superior direito da janela do browser. 

## <a name="configure-job-input"></a>Configurar a entrada da tarefa

Nesta secção, irá configurar uma entrada de dispositivo do IoT Hub para a tarefa do Stream Analytics. Utilize o Hub de IoT que criou na secção anterior do início rápido.

1. Navegue para a tarefa do Stream Analytics.  

2. Selecione **entradas** > **Adicionar entrada de Stream** > **IoT Hub**.  

3. Preencha os **IoT Hub** página com os seguintes valores:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada  |  IoTHubInput   |  Introduza um nome para identificar a entrada da tarefa.   |
   |Subscrição   |  \<A sua subscrição\> |  Selecione a subscrição do Azure que tem a conta de armazenamento que criou. A conta de armazenamento pode estar na mesma subscrição ou numa diferente. Este exemplo assume que criou a conta de armazenamento na mesma subscrição. |
   |IoT Hub  |  MyASAIoTHub |  Introduza o nome do IoT Hub que criou na secção anterior. |

4. Mantenha as outras opções com os valores predefinidos e selecione **Guardar** para guardar as definições.  

   ![Configurar dados de entrada](./media/stream-analytics-quick-create-portal/configure-asa-input.png)
 
## <a name="configure-job-output"></a>Configurar a saída da tarefa

1. Navegue para a tarefa do Stream Analytics que criou anteriormente.  

2. Selecione **saídas** > **Add** > **armazenamento de BLOBs**.  

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

3. Neste exemplo, a consulta lê os dados a partir do IoT Hub e copia-o para um novo ficheiro no blob. Selecione **Guardar**.  

   ![Configurar a transformação da tarefa](./media/stream-analytics-quick-create-portal/add-asa-query.png)

## <a name="run-the-iot-simulator"></a>Executar o simulador de IoT

1. Abra o [Raspberry Pi simulador Online do Azure IoT](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Substitua o marcador de posição na linha 15 a cadeia de ligação de dispositivos do IoT Hub do Azure que guardou na secção anterior.

3. Clique em **Executar**. A saída deve mostrar os dados de sensor e as mensagens que estão a ser enviadas ao seu IoT Hub.

   ![Simulador Online do Azure IoT raspberry Pi](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

1. Regresse à página de descrição geral da tarefa e selecione **Iniciar**.

2. Sob **tarefa de início**, selecione **agora**, para o **hora de início da saída da tarefa** campo. Em seguida, selecione **iniciar** para iniciar seu trabalho.

3. Após alguns minutos, no portal, localize a conta de armazenamento e o contentor que configurou como saída da tarefa. Agora, pode ver o ficheiro de saída no contentor. A tarefa demora alguns minutos a iniciar pela primeira vez. Depois de ser iniciada, continuará a ser executada à medida que os dados são recebidos.  

   ![Saída transformada](./media/stream-analytics-quick-create-portal/check-asa-results.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa do Stream Analytics e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este início rápido ao utilizar os seguintes passos:

1. No menu do lado esquerdo no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou uma tarefa simples do Stream Analytics com o portal do Azure. Também pode implementar tarefas do Stream Analytics usando [PowerShell](stream-analytics-quick-create-powershell.md), [Visual Studio](stream-analytics-quick-create-vs.md), e [Visual Studio Code](quick-create-vs-code.md).

Para saber mais sobre como configurar outras origens de entrada e efetuar a deteção em tempo real, avance para o seguinte artigo:

> [!div class="nextstepaction"]
> [Deteção de fraudes em tempo real com o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)

