---
title: Início rápido-criar um trabalho de Stream Analytics usando o portal do Azure
description: Este início rápido mostra como começar ao criar uma tarefa do Stream Analytics, configurar entradas e saídas, e definir uma consulta.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 06/21/2019
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
ms.openlocfilehash: 65114f2ddb7567b47ac3951fbaf6664654e379f0
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707377"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-portal"></a>Início Rápido: Criar uma tarefa do Stream Analytics com o portal do Azure

Este início rápido mostra como começar ao criar uma tarefa do Stream Analytics. Neste guia de início rápido, você define um trabalho de Stream Analytics que lê dados de streaming em tempo real e filtra mensagens com uma temperatura maior que 27. Seu trabalho de Stream Analytics lerá os dados do Hub IoT, transformar os dados e gravar os dados de volta em um contêiner no armazenamento de BLOBs. Os dados de entrada usados neste guia de início rápido são gerados por um simulador online do Raspberry Pi. 

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho de Stream Analytics, você deve preparar os dados de entrada. Os dados do sensor em tempo real são ingeridos no Hub IoT, que é configurado posteriormente como a entrada do trabalho. Para preparar os dados de entrada exigidos pelo trabalho, conclua as seguintes etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.

3. No painel **Hub IOT** , insira as seguintes informações:
   
   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Subscrição  | \<A sua subscrição\> |  Selecione a subscrição do Azure que pretende utilizar. |
   |Grupo de recursos   |   asaquickstart-resourcegroup  |   Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta. |
   |Região  |  \<Selecione a região mais próxima dos seus utilizadores\> | Selecione uma localização geográfica na qual você pode hospedar o Hub IoT. Use o local mais próximo de seus usuários. |
   |Nome do Hub IoT  | MyASAIoTHub  |   Selecione um nome para o Hub IoT.   |

   ![Criar um Hub IoT](./media/stream-analytics-quick-create-portal/create-iot-hub.png)

4. Selecione **Avançar: definir tamanho e escala**.

5. Selecione o seu **Escalão de preço e dimensionamento**. Para este guia de início rápido, selecione a camada **F1-Free** se ela ainda estiver disponível em sua assinatura. Para obter mais informações, consulte [preços do Hub IOT](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Dimensionar e dimensionar seu hub IoT](./media/stream-analytics-quick-create-portal/iot-hub-size-and-scale.png)

6. Selecione **Rever + criar**. Examine as informações do Hub IoT e clique em **criar**. O Hub IoT pode levar alguns minutos para ser criado. Pode monitorizar o progresso no painel **Notificações**.

7. No menu de navegação do Hub IoT, clique em **Adicionar** em **dispositivos IOT**. Adicione uma **ID de dispositivo** e clique em **salvar**.

   ![Adicionar um dispositivo ao Hub IoT](./media/stream-analytics-quick-create-portal/add-device-iot-hub.png)

8. Depois que o dispositivo for criado, abra o dispositivo na lista de **dispositivos IOT** . Copie a **cadeia de conexão--Primary Key** e salve-a em um bloco de notas para usar posteriormente.

   ![Copiar cadeia de conexão do dispositivo do Hub IoT](./media/stream-analytics-quick-create-portal/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Criar armazenamento de BLOBs

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**.

2. No painel **criar conta de armazenamento** , insira um nome de conta de armazenamento, um local e um grupo de recursos. Escolha o mesmo local e grupo de recursos que o Hub IoT que você criou. Em seguida, clique em **revisar + criar** para criar a conta.

   ![Criar conta de armazenamento](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. Depois que sua conta de armazenamento for criada, selecione o bloco **BLOBs** no painel **visão geral** .

   ![Descrição geral da conta de armazenamento](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. Na página **serviço blob** , selecione **contêiner** e forneça um nome para o contêiner, como *Container1*. Deixe o **nível de acesso público** como **privado (sem acesso anônimo)** e selecione **OK**.

   ![Criar contentor de blobs](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-job"></a>Criar uma tarefa do Stream Analytics

1. Inicie sessão no Portal do Azure.

2. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.  

3. Selecione **análise** > **Stream Analytics trabalho** na lista de resultados.  

4. Preencha a página da tarefa do Stream Analytics com as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Nome da tarefa   |  MyASAJob   |   Introduza um nome para identificar a tarefa do Stream Analytics. O nome da tarefa do Stream Analytics só pode conter carateres alfanuméricos, hífenes e carateres de sublinhado e tem de ter entre 3 a 63 carateres. |
   |Subscrição  | \<A sua subscrição\> |  Selecione a subscrição do Azure que quer utilizar para esta tarefa. |
   |Grupo de recursos   |   asaquickstart-resourcegroup  |   Selecione o mesmo grupo de recursos que o Hub IoT. |
   |Localização  |  \<Selecione a região mais próxima dos seus utilizadores\> | Selecione a localização geográfica onde pode alojar a tarefa do Stream Analytics. Utilize a localização mais próxima dos seus utilizadores para um melhor desempenho e reduzir os custos de transferência de dados. |
   |Unidades de transmissão em fluxo  | 1  |   As unidades de transmissão em fluxo representam os recursos informáticos que são necessários para executar uma tarefa. Por predefinição, este valor está definido como 1. Para saber mais sobre o dimensionamento de unidades de transmissão em fluxo, veja o artigo [Compreender e ajustar as unidades de transmissão em fluxo](stream-analytics-streaming-unit-consumption.md).   |
   |Ambiente de alojamento  |  Nuvem  |   As tarefas do Stream Analytics podem ser implementadas na cloud ou no Edge. A nuvem permite que você implante na nuvem do Azure e o Edge permite que você implante em um dispositivo IoT Edge. |

   ![Criar tarefa](./media/stream-analytics-quick-create-portal/create-asa-job.png)

5. Selecione a caixa **Afixar ao dashboard** para colocar a tarefa no seu dashboard e, em seguida, selecione **Criar**.  

6. Você deve ver a notificação *implantação em andamento...* exibida no canto superior direito da janela do navegador. 

## <a name="configure-job-input"></a>Configurar a entrada da tarefa

Nesta seção, você configurará uma entrada de dispositivo do Hub IoT para o trabalho Stream Analytics. Use o Hub IoT criado na seção anterior do guia de início rápido.

1. Navegue para a tarefa do Stream Analytics.  

2. Selecione **entradas** > **Adicionar entrada de fluxo** > **Hub IOT**.  

3. Preencha a página do **Hub IOT** com os seguintes valores:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Alias de entrada  |  IoTHubInput   |  Introduza um nome para identificar a entrada da tarefa.   |
   |Subscrição   |  \<A sua subscrição\> |  Selecione a subscrição do Azure que tem a conta de armazenamento que criou. A conta de armazenamento pode estar na mesma subscrição ou numa diferente. Este exemplo assume que criou a conta de armazenamento na mesma subscrição. |
   |Hub IoT  |  MyASAIoTHub |  Insira o nome do Hub IoT criado na seção anterior. |

4. Mantenha as outras opções com os valores predefinidos e selecione **Guardar** para guardar as definições.  

   ![Configurar dados de entrada](./media/stream-analytics-quick-create-portal/configure-asa-input.png)
 
## <a name="configure-job-output"></a>Configurar a saída da tarefa

1. Navegue para a tarefa do Stream Analytics que criou anteriormente.  

2. Selecione **saídas** > **Adicionar** > **armazenamento de BLOBs**.  

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

3. Neste exemplo, a consulta lê os dados do Hub IoT e os copia para um novo arquivo no BLOB. Selecione **Guardar**.  

   ![Configurar a transformação da tarefa](./media/stream-analytics-quick-create-portal/add-asa-query.png)

## <a name="run-the-iot-simulator"></a>Executar o simulador de IoT

1. Abra o [simulador online do Azure IOT do Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/).

2. Substitua o espaço reservado na linha 15 pela cadeia de conexão do dispositivo do Hub IoT do Azure que você salvou em uma seção anterior.

3. Clique em **Executar**. A saída deve mostrar os dados do sensor e as mensagens que estão sendo enviadas para o Hub IoT.

   ![Simulador online do Azure IoT do Raspberry Pi](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

1. Regresse à página de descrição geral da tarefa e selecione **Iniciar**.

2. Em **Iniciar trabalho**, selecione **agora**para o campo **hora de início da saída do trabalho** . Em seguida, selecione **Iniciar** para iniciar o trabalho.

3. Após alguns minutos, no portal, localize a conta de armazenamento e o contentor que configurou como saída da tarefa. Agora, pode ver o ficheiro de saída no contentor. A tarefa demora alguns minutos a iniciar pela primeira vez. Depois de ser iniciada, continuará a ser executada à medida que os dados são recebidos.  

   ![Saída transformada](./media/stream-analytics-quick-create-portal/check-asa-results.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, exclua o grupo de recursos, o trabalho de Stream Analytics e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este início rápido ao utilizar os seguintes passos:

1. No menu do lado esquerdo no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou uma tarefa simples do Stream Analytics com o portal do Azure. Você também pode implantar trabalhos de Stream Analytics usando o [PowerShell](stream-analytics-quick-create-powershell.md), o [Visual Studio](stream-analytics-quick-create-vs.md)e o [Visual Studio Code](quick-create-vs-code.md).

Para saber mais sobre como configurar outras origens de entrada e efetuar a deteção em tempo real, avance para o seguinte artigo:

> [!div class="nextstepaction"]
> [Deteção de fraudes em tempo real com o Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
