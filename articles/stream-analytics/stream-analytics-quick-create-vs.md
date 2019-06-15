---
title: Criar uma tarefa de Stream Analytics, utilizando as ferramentas do Azure Stream Analytics para Visual Studio
description: Este início rápido mostra como começar ao criar uma tarefa do Stream Analytics, configurar entradas e saídas, e definir uma consulta com o Visual Studio.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 06/11/2019
ms.topic: quickstart
ms.service: stream-analytics
ms.openlocfilehash: bd27495266918063c65bf4648b3453e25767e293
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67076208"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-the-azure-stream-analytics-tools-for-visual-studio"></a>Início rápido: Criar uma tarefa de Stream Analytics, utilizando as ferramentas do Azure Stream Analytics para Visual Studio

Este início rápido mostra como criar e executar uma tarefa do Stream Analytics com ferramentas do Azure Stream Analytics para Visual Studio. A tarefa de exemplo lê os dados de transmissão em fluxo de um dispositivo do IoT Hub. É possível definir uma tarefa que calcula a temperatura média quando mais de 27° e grava a saída resultante eventos para um novo ficheiro no armazenamento de Blobs.

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

* Instale o Visual Studio 2019, Visual Studio 2015 ou Visual Studio 2013 atualização 4. As edições Enterprise (Ultimate/Premium), Professional e Community são suportadas. A edição Express não é suportada.

* Siga as [instruções de instalação](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio-install) para instalar as ferramentas do Stream Analytics para Visual Studio.

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir a tarefa do Stream Analytics, deve preparar os dados, o que mais tarde são configurados como a tarefa de entrada. Para preparar os dados de entrada necessários para a tarefa, conclua os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.

3. Na **IoT Hub** painel, introduza as seguintes informações:
   
   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Subscrição  | \<A sua subscrição\> |  Selecione a subscrição do Azure que pretende utilizar. |
   |Grupo de recursos   |   asaquickstart-resourcegroup  |   Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta. |
   |Região  |  \<Selecione a região mais próxima dos seus utilizadores\> | Selecione uma localização geográfica onde pode alojar o seu IoT Hub. Utilize a localização mais próxima dos seus utilizadores. |
   |Nome do IoT Hub  | MyASAIoTHub  |   Selecione um nome para o seu IoT Hub.   |

   ![Criar um Hub IoT](./media/stream-analytics-quick-create-vs/create-iot-hub.png)

4. Selecione **seguinte: Definir o tamanho e a escala**.

5. Selecione o seu **Escalão de preço e dimensionamento**. Neste início rápido, selecione o **F1 - gratuito** camada se ela ainda está disponível na sua subscrição. Se o escalão gratuito não está disponível, escolha o escalão mais baixo disponível. Para obter mais informações, consulte [preços do IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Redimensionar e dimensione o seu IoT Hub](./media/stream-analytics-quick-create-vs/iot-hub-size-and-scale.png)

6. Selecione **Rever + criar**. Reveja as suas informações do IoT Hub e clique em **criar**. O IoT Hub pode demorar alguns minutos a criar. Pode monitorizar o progresso no painel **Notificações**.

7. No menu de navegação do IoT Hub, clique em **Add** sob **dispositivos IoT**. Adicionar uma **ID do dispositivo** e clique em **guardar**.

   ![Adicionar um dispositivo ao IoT Hub](./media/stream-analytics-quick-create-vs/add-device-iot-hub.png)

8. Depois do dispositivo é criado, abra o dispositivo a partir da **dispositivos IoT** lista. Copiar o **cadeia de ligação – chave primária** e guarde-o para um bloco de notas para utilizar mais tarde.

   ![Copie a cadeia de ligação de dispositivos do IoT Hub](./media/stream-analytics-quick-create-vs/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Criar armazenamento de BLOBs

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**.

2. Na **criar conta de armazenamento** painel, introduza um grupo de recursos, localização e nome da conta de armazenamento. Escolha a mesma localização e grupo de recursos como o IoT Hub que criou. Em seguida, clique em **rever + criar** para criar a conta.

   ![Criar conta de armazenamento](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. Depois de criar a conta de armazenamento, selecione o **Blobs** mosaico a **descrição geral** painel.

   ![Descrição geral da conta de armazenamento](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. Do **serviço Blob** página, selecione **contentor** e indique um nome para o contentor, como *container1*. Deixe o **nível de acesso público** como **privado (sem acesso anónimo)** e selecione **OK**.

   ![Criar contentor de blobs](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics

1. Inicie o Visual Studio.

2. Selecione **Ficheiro > Novo Projeto**.  

3. Na lista de modelos, no lado esquerdo, selecione **Stream Analytics** e, em seguida, selecione **Aplicação do Azure Stream Analytics**.  

4. Introduza o **Nome** do projeto, a **Localização** e o **Nome da solução** e selecione **OK**.

   ![Criar um projeto do Stream Analytics](./media/stream-analytics-quick-create-vs/create-stream-analytics-project.png)

Repare nos elementos que são incluídos num projeto do Azure Stream Analytics.

   <img src="./media/stream-analytics-quick-create-vs/stream-analytics-project.png" alt="Azure Stream Analytics project elements" width="300px"/>


## <a name="choose-the-required-subscription"></a>Escolha a subscrição obrigatória

1. No Visual Studio, no menu **Ver**, selecione **Explorador de Servidores**.

2. Clique com o botão direito do rato em **Azure**, selecione **Ligar à Subscrição do Microsoft Azure** e, em seguida, inicie sessão com a conta do Azure.

## <a name="define-input"></a>Definir entrada

1. No **Explorador de Soluções**, expanda o nó **Entradas** e faça duplo clique em **Input.json**.

2. Preencha a **Configuração de Entrada do Stream Analytics** com os seguintes valores:

   |**Definição**  |**Valor sugerido**  |**Descrição**   |
   |---------|---------|---------|
   |Alias de Entrada  |  Input   |  Introduza um nome para identificar a entrada da tarefa.   |
   |Tipo de Fonte   |  Fluxo de Dados |  Escolha a origem de entrada apropriada: Stream de dados ou dados de referência.   |
   |source  |  IoT Hub |  Escolha a origem de entrada adequada.   |
   |Resource  | Escolha a origem de dados na conta atual | Opte por introduzir dados manualmente ou selecionar uma conta existente.   |
   |Subscrição  |  \<A sua subscrição\>   | Selecione a subscrição do Azure com o IoT Hub que criou.   |
   |IoT Hub  |  MyASAIoTHub   |  Selecione ou introduza o nome do IoT Hub. Os nomes do IoT Hub são detetados automaticamente se forem criados na mesma subscrição.   |
   
3. Mantenha as outras opções com os valores predefinidos e selecione **Guardar** para guardar as definições.  

   ![Configurar dados de entrada](./media/stream-analytics-quick-create-vs/stream-analytics-vs-input.png)

## <a name="define-output"></a>Definir a saída

1. No **Explorador de Soluções**, expanda o nó **Saídas** e faça duplo clique em **Output.json**.

2. Preencha a **Configuração de Saída do Stream Analytics** com os seguintes valores:

   |**Definição**  |**Valor sugerido**  |**Descrição**   |
   |---------|---------|---------|
   |Alias de Saída  |  Saída   |  Introduza um nome para identificar a saída da tarefa.   |
   |Sink   |  Blob Storage |  Escolha o sink adequado.    |
   |Resource  |  Apresente as definições da origem de dados manualmente |  Opte por introduzir dados manualmente ou selecionar uma conta existente.   |
   |Subscrição  |  \<A sua subscrição\>   | Selecione a subscrição do Azure que tem a conta de armazenamento que criou. A conta de armazenamento pode estar na mesma subscrição ou numa diferente. Este exemplo assume que criou a conta de armazenamento na mesma subscrição.   |
   |Conta de Armazenamento  |  asaquickstartstorage   |  Escolha ou introduza o nome da conta de armazenamento. Os nomes de contas de armazenamento são detetados automaticamente se forem criados na mesma subscrição.   |
   |Contentor  |  container1   |  Selecione o contentor existente que criou na conta de armazenamento.   |
   |Padrão do Caminho  |  saída   |  Introduza o nome de um caminho de ficheiro a ser criado no contentor.   |
   
3. Mantenha as outras opções com os valores predefinidos e selecione **Guardar** para guardar as definições.  

   ![Configurar dados de saída](./media/stream-analytics-quick-create-vs/stream-analytics-vs-output.png)

## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

1. Abra o ficheiro **Script.asaql** no **Explorador de Soluções** do Visual Studio.

2. Adicione a seguinte consulta:

   ```sql
   SELECT *
   INTO BlobOutput
   FROM IoTHubInput
   HAVING Temperature > 27
   ```

## <a name="submit-a-stream-analytics-query-to-azure"></a>Submeter uma consulta do Stream Analytics para o Azure

1. No **Editor de Consultas**, selecione **Submeter para o Azure** no editor de scripts.

2. Selecione **Criar uma Nova Tarefa do Azure Stream Analytics** e introduza um **Nome da Tarefa**. Escolha a **Subscrição**, o **Grupo de Recursos** e a **Localização** utilizada no início do Início Rápido.

   ![Submeter tarefa para o Azure](./media/stream-analytics-quick-create-vs/stream-analytics-job-to-azure.png)

## <a name="run-the-iot-simulator"></a>Executar o simulador de IoT

1. Abra o [Raspberry Pi Azure IoT Online simulador](https://azure-samples.github.io/raspberry-pi-web-simulator/) num novo separador do browser ou janela.

2. Substitua o marcador de posição na linha 15 a cadeia de ligação de dispositivos do IoT Hub do Azure que guardou na secção anterior.

3. Clique em **Executar**. A saída deve mostrar os dados de sensor e as mensagens que estão a ser enviadas ao seu IoT Hub.

   ![Simulador Online do Azure IoT raspberry Pi](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

1. Quando a tarefa é criada, a vista de tarefa abre-se automaticamente. Selecione o botão da seta verde para iniciar a tarefa,

   ![Iniciar tarefa do Stream Analytics](./media/stream-analytics-quick-create-vs/start-stream-analytics-job-vs.png)

2. Alteração da **modo de início da saída da tarefa** para **JobStartTime** e selecione **iniciar**.

   ![Iniciar configuração da tarefa](./media/stream-analytics-quick-create-vs/stream-analytics-start-configuration.png)

3. Tenha em atenção que o estado da tarefa foi alterado para **Em execução** e se existem eventos de entrada/saída. Esta operação poderá demorar alguns minutos.

   ![Executar tarefa do Stream Analytics](./media/stream-analytics-quick-create-vs/stream-analytics-job-running.png)

4. Para ver os resultados, no menu **Vista**, selecione **Explorador da Cloud** e navegue para a conta de armazenamento no seu grupo de recursos. Em **Contentores de Blobs**, clique duas vezes em **contentor1** e, em seguida, no caminho do ficheiro **saída**.

   ![Ver resultados](./media/stream-analytics-quick-create-vs/stream-analytics-vs-results.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este início rápido ao utilizar os seguintes passos:

1. No menu do lado esquerdo no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou uma tarefa simples do Stream Analytics com o Visual Studio. Também pode implementar tarefas do Stream Analytics com o [portal do Azure](stream-analytics-quick-create-portal.md) e o [PowerShell](stream-analytics-quick-create-powershell.md). 

Para saber mais sobre as ferramentas do Azure Stream Analytics para Visual Studio, avance para o seguinte artigo:

> [!div class="nextstepaction"]
> [Use o Visual Studio para ver tarefas do Azure Stream Analytics](stream-analytics-vs-tools.md)
