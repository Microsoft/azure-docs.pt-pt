---
title: Criar uma tarefa de cloud do Azure Stream Analytics no Visual Studio Code (pré-visualização)
description: Este guia de introdução mostra-lhe como começar ao criar uma tarefa do Stream Analytics, configurar entradas e saídas e definir uma consulta com o Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 894f43a7da0abd129123d5c4ddf2bb95347c42c5
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825389"
---
# <a name="quickstart-create-an-azure-stream-analytics-cloud-job-in-visual-studio-code-preview"></a>Início rápido: Criar uma tarefa de cloud do Azure Stream Analytics no Visual Studio Code (pré-visualização)

Este guia de introdução mostra-lhe como criar e executar uma tarefa de Stream Analytics com a extensão do Azure Stream Analytics para Visual Studio Code. A tarefa de exemplo lê os dados de transmissão em fluxo de um dispositivo do IoT Hub. É possível definir uma tarefa que calcula a temperatura média quando mais de 27° e grava a saída resultante eventos para um novo ficheiro no armazenamento de Blobs.

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

* Instale [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-extension"></a>Instalar a extensão do Azure Stream Analytics

1. Abra o Visual Studio Code.

2. Partir **extensões** no painel da esquerda, procure **Stream Analytics** e selecione **instalar** no **Azure Stream Analytics** extensão.

3. Depois de instalar a extensão, certifique-se de que **ferramentas do Azure Stream Analytics** está visível no seu **extensões ativadas**.

   ![Ferramentas do Azure Stream Analytics em extensões ativadas no Visual Studio Code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-extension"></a>Ativar a extensão do Azure Stream Analytics

1. Selecione o **Azure** ícone na barra de atividade do VS Code. **Stream Analytics** estará visível na barra do lado. Sob **Stream Analytics**, selecione **iniciar sessão no Azure**. 

   ![Inicie sessão no Azure no Visual Studio Code](./media/quick-create-vs-code/azure-sign-in.png)

2. Quando tem sessão iniciada, o nome da sua conta do Azure é apresentada na barra de estado no canto inferior esquerdo da janela do VS Code.

> [!NOTE]
> Ferramentas do Azure Stream Analytics irão iniciar sessão automaticamente da próxima vez que se não terminar. Se a sua conta tem a autenticação de dois fatores, recomenda-se que utilize autenticação do telefone, em vez de utilizar um PIN.
> Se tiver problemas a listagem de recursos, a terminar sessão e iniciar sessão novamente, normalmente, a ajuda. Para terminar sessão, introduza o comando `Azure: Sign Out`.

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
   |Nome do Hub IoT  | MyASAIoTHub  |   Selecione um nome para o seu IoT Hub.   |

   ![Criar um Hub IoT](./media/quick-create-vs-code/create-iot-hub.png)

4. Selecione **seguinte: Definir o tamanho e a escala**.

5. Selecione o seu **Escalão de preço e dimensionamento**. Neste início rápido, selecione o **F1 - gratuito** camada se ela ainda está disponível na sua subscrição. Se o escalão gratuito não está disponível, escolha o escalão mais baixo disponível. Para obter mais informações, consulte [preços do IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Redimensionar e dimensione o seu IoT Hub](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Selecione **Rever + criar**. Reveja as suas informações do IoT Hub e clique em **criar**. O IoT Hub pode demorar alguns minutos a criar. Pode monitorizar o progresso no painel **Notificações**.

7. No menu de navegação do IoT Hub, clique em **Add** sob **dispositivos IoT**. Adicionar uma **ID do dispositivo** e clique em **guardar**.

   ![Adicionar um dispositivo ao IoT Hub](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Depois do dispositivo é criado, abra o dispositivo a partir da **dispositivos IoT** lista. Copiar o **cadeia de ligação – chave primária** e guarde-o para um bloco de notas para utilizar mais tarde.

   ![Copie a cadeia de ligação de dispositivos do IoT Hub](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Criar armazenamento de BLOBs

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**.

2. Na **criar conta de armazenamento** painel, introduza um grupo de recursos, localização e nome da conta de armazenamento. Escolha a mesma localização e grupo de recursos como o IoT Hub que criou. Em seguida, clique em **rever + criar** para criar a conta.

   ![Criar conta de armazenamento](./media/quick-create-vs-code/create-storage-account.png)

3. Depois de criar a conta de armazenamento, selecione o **Blobs** mosaico a **descrição geral** painel.

   ![Descrição geral da conta de armazenamento](./media/quick-create-vs-code/blob-storage.png)

4. Do **serviço Blob** página, selecione **contentor** e indique um nome para o contentor, como *container1*. Deixe o **nível de acesso público** como **privado (sem acesso anónimo)** e selecione **OK**.

   ![Criar contentor de blobs](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics

1. No Visual Studio Code, prima **Ctrl + Shift + P** para abrir a paleta de comandos. Em seguida, escreva **ASA** e selecione **ASA: Criar novo projeto**.

   ![Criar novo projeto](./media/quick-create-vs-code/create-new-project.png)

2. Introduza o nome do projeto, como **myASAproj** e selecione uma pasta para o seu projeto.

    ![Criar nome do projeto](./media/quick-create-vs-code/create-project-name.png)

3. O novo projeto será adicionado à sua área de trabalho. O script de consulta consiste num projeto do ASA **(*.asaql)**, uma **JobConfig.json** ficheiro e um **asaproj.json** ficheiro de configuração.

   ![Arquivos de projeto do Stream Analytics no VS Code](./media/quick-create-vs-code/asa-project-files.png)

4. O **asaproj.json** ficheiro de configuração contém as entradas, saídas e informações de ficheiro de configuração de tarefa necessária para submeter a tarefa de Stream Analytics para o Azure.

   ![Ficheiro de configuração do Stream Analytics tarefa no VS Code](./media/quick-create-vs-code/job-configuration.png)

> [!Note]
> Ao adicionar entradas e saídas da paleta de comandos, os caminhos correspondentes serão adicionados ao **asaproj.json** automaticamente. Se adicionar ou remover entradas ou saídas no disco diretamente, terá de adicionar manualmente ou removê-los a partir **asaproj.json**. Pode optar por colocar as entradas e saídas em um local, em seguida, referenciá-los em tarefas diferentes, especificando os caminhos em cada **asaproj.json**.

## <a name="define-an-input"></a>Definir uma entrada

1. Selecione **Ctrl + Shift + P** para abrir a paleta de comandos e introduza **ASA: Adicionar entrada de**.

   ![Adicionar entrada de Stream Analytics no VS Code](./media/quick-create-vs-code/add-input.png)

2. Escolher **IoT Hub** para o tipo de entrada.

   ![Selecione o IoT Hub como opção de entrada](./media/quick-create-vs-code/iot-hub.png)

3. Escolha o script de consulta do ASA que irá utilizar a entrada. Ele deve preencher automaticamente com o caminho de ficheiro para **myASAproj.asaql**.

   ![Selecione um script do ASA no Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Introduza o nome do ficheiro de entrada **IotHub.json**.

5. Editar **IoTHub.json** com os seguintes valores. Mantenha os valores predefinidos para os campos não mencionados abaixo. Pode utilizar o CodeLens para o ajudar a introduzir uma cadeia de caracteres, selecionar a partir de uma lista suspensa ou alterar o texto diretamente no ficheiro.

   |Definição|Valor sugerido|Descrição|
   |-------|---------------|-----------|
   |Name|Entrada|Introduza um nome para identificar a entrada da tarefa.|
   |IotHubNamespace|MyASAIoTHub|Selecione ou introduza o nome do IoT Hub. Os nomes do IoT Hub são detetados automaticamente se forem criados na mesma subscrição.|
   |EndPoint|Mensagens| |
   |SharedAccessPolicyName|iothubowner| |

## <a name="define-an-output"></a>Definir uma saída

1. Selecione **Ctrl + Shift + P** para abrir a paleta de comandos. Em seguida, introduza **ASA: Adicionar a saída**.

   ![Adicionar a saída do Stream Analytics no VS Code](./media/quick-create-vs-code/add-output.png)

2. Escolher **armazenamento de BLOBs** para o tipo de Sink.

3. Escolha o script de consulta do ASA que irá utilizar esta entrada.

4. Introduza o nome de ficheiro de saída como **BlobStorage.json**.

5. Editar **BlobStorage.json** com os seguintes valores. Mantenha os valores predefinidos para os campos não mencionados abaixo. Utilize o CodeLens para introduzir uma cadeia de caracteres ou selecionar a partir de uma lista suspensa.

   |Definição|Valor sugerido|Descrição|
   |-------|---------------|-----------|
   |Name|Resultado| Introduza um nome para identificar a saída da tarefa.|
   |Conta de Armazenamento|asaquickstartstorage|Selecione ou introduza o nome da conta de armazenamento. Os nomes de contas de armazenamento são detetados automaticamente se forem criados na mesma subscrição.|
   |Contentor|container1|Selecione o contentor existente que criou na conta de armazenamento.|
   |Padrão do Caminho|saída|Introduza o nome de um caminho de ficheiro a ser criado no contentor.|

## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

1. Open **myASAproj.asaql** na pasta do projeto.

2. Adicione a seguinte consulta:

   ```sql
   SELECT * 
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="compile-the-script"></a>Compilar o script

Compilação de script faz duas coisas: Verifique a sintaxe e gerar os modelos Azure Resource Manager para a implementação automática.

Existem duas formas para acionar a compilação de script:

1. Selecione o script na área de trabalho e, em seguida, acione a compilação da paleta de comandos. 

   ![Usar a paleta de comandos do VS Code para compilar o script](./media/quick-create-vs-code/compile-script1.png)

2. Clique com o botão direito do rato no script e selecione **ASA: compilar o script**.

    ![O script do ASA para compilar com o botão direito](./media/quick-create-vs-code/compile-script2.png)

3. Após a compilação, pode encontrar os dois modelos do Azure Resource Manager gerado em **Deploy** pasta do seu projeto. Esses dois arquivos são usados para implementação automática.

    ![Modelos de implementação do Stream Analytics no Explorador de ficheiros](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Submeter uma tarefa do Stream Analytics para o Azure

1. Na janela de editor de script do Visual Studio Code, selecione **selecione a partir das subscrições**.

   ![Selecionar a partir de seu texto de subscrições no editor de script](./media/quick-create-vs-code/select-subscription.png)

2. Selecione a sua subscrição na lista de pop-up.

3. Selecione uma tarefa * *. Em seguida, escolha criar uma nova tarefa.

4. Introduza o seu nome de tarefa **myASAjob** e, em seguida, siga as instruções para escolher o grupo de recursos e localização.

5. Selecione **submeter para o Azure**. Os registos podem ser encontrados na janela de saída. 

6. Quando a tarefa é criada, pode ver no Explorador de análise do Stream.

## <a name="run-the-iot-simulator"></a>Executar o simulador de IoT

1. Abra o [Raspberry Pi Azure IoT Online simulador](https://azure-samples.github.io/raspberry-pi-web-simulator/) num novo separador do browser ou janela.

2. Substitua o marcador de posição na linha 15 a cadeia de ligação de dispositivos do IoT Hub do Azure que guardou na secção anterior.

3. Clique em **Executar**. A saída deve mostrar os dados de sensor e as mensagens que estão a ser enviadas ao seu IoT Hub.

   ![Simulador Online do Azure IoT raspberry Pi](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

1. Open **Explorer do Stream Analytics** no Visual Studio Code e localize a sua tarefa **myASAJob**.

2. Clique com o botão direito no nome da tarefa. Em seguida, selecione **iniciar** no menu de contexto.

   ![Iniciar a tarefa de Stream Analytics no VS Code](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Escolher **agora** na janela de pop-up para iniciar a tarefa.

4. Tenha em atenção o estado da tarefa foi alterado para **em execução**. Com o botão direito no nome da tarefa e escolha **vista de tarefas aberto no Portal** para ver a entrada e métricas de eventos de saída. Esta ação pode demorar alguns minutos.

5. Para ver os resultados, abra o armazenamento de BLOBs na extensão do Visual Studio Code ou no portal do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este início rápido ao utilizar os seguintes passos:

1. No menu do lado esquerdo no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou uma tarefa do Stream Analytics simples com o Visual Studio Code. Também pode implementar tarefas do Stream Analytics com o [portal do Azure](stream-analytics-quick-create-portal.md), [PowerShell](stream-analytics-quick-create-powershell.md)e o Visual Studio (stream-analytics-guia de introdução-criar-vs.md). 

Para saber mais sobre as ferramentas do Azure Stream Analytics para Visual Studio, avance para o seguinte artigo:

> [!div class="nextstepaction"]
> [Use o Visual Studio para ver tarefas do Azure Stream Analytics](stream-analytics-vs-tools.md)
