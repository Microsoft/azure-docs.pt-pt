---
title: Criar um trabalho de nuvem Azure Stream Analytics no Visual Studio Code (visualização)
description: Este guia de início rápido mostra como começar criando um trabalho de Stream Analytics, Configurando entradas, saídas e definindo uma consulta com Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/16/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 3301be3a067982cb90e663fe3782319eb0b90ba0
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673137"
---
# <a name="quickstart-create-an-azure-stream-analytics-cloud-job-in-visual-studio-code-preview"></a>Início rápido: Criar um trabalho de nuvem Azure Stream Analytics no Visual Studio Code (visualização)

Este guia de início rápido mostra como criar e executar um trabalho de Stream Analytics usando a extensão Azure Stream Analytics para Visual Studio Code. O trabalho de exemplo lê os dados de streaming de um dispositivo de Hub IoT. Você define um trabalho que calcula a temperatura média quando mais de 27 ° e grava os eventos de saída resultantes em um novo arquivo no armazenamento de BLOBs.

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

* Instale o [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-extension"></a>Instalar a extensão de Azure Stream Analytics

1. Abra o Visual Studio Code.

2. Em **extensões** no painel esquerdo, procure **Stream Analytics** e selecione **instalar** na extensão de **Azure Stream Analytics** .

3. Depois que a extensão for instalada, verifique se as **ferramentas de Azure Stream Analytics** estão visíveis nas **extensões habilitadas**.

   ![Azure Stream Analytics ferramentas em extensões habilitadas no Visual Studio Code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-extension"></a>Ativar a extensão de Azure Stream Analytics

1. Selecione o ícone **do Azure** na barra de atividade do vs Code. **Stream Analytics** ficará visível na barra lateral. Em **Stream Analytics**, selecione **entrar no Azure**. 

   ![Entrar no Azure no Visual Studio Code](./media/quick-create-vs-code/azure-sign-in.png)

2. Quando você estiver conectado, o nome da sua conta do Azure aparecerá na barra de status no canto inferior esquerdo da janela de VS Code.

> [!NOTE]
> Azure Stream Analytics ferramentas entrarão automaticamente na próxima vez, se você não sair. Se sua conta tiver a autenticação de dois fatores, é recomendável usar a autenticação por telefone em vez de usar um PIN.
> Se você tiver problemas para listar recursos, sair e entrar novamente geralmente ajuda. Para sair, insira o comando `Azure: Sign Out`.

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho de Stream Analytics, você deve preparar os dados, que são configurados posteriormente como a entrada do trabalho. Para preparar os dados de entrada exigidos pelo trabalho, conclua as seguintes etapas:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.

3. No painel **Hub IOT** , insira as seguintes informações:
   
   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Subscription  | \<A sua subscrição\> |  Selecione a subscrição do Azure que pretende utilizar. |
   |Resource group   |   asaquickstart-resourcegroup  |   Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta. |
   |Região  |  \<Selecione a região mais próxima dos seus utilizadores\> | Selecione uma localização geográfica na qual você pode hospedar o Hub IoT. Use o local mais próximo de seus usuários. |
   |Nome do Hub IoT  | MyASAIoTHub  |   Selecione um nome para o Hub IoT.   |

   ![Criar um Hub IoT](./media/quick-create-vs-code/create-iot-hub.png)

4. Selecione **avançar: Defina tamanho e escala @ no__t-0.

5. Selecione o seu **Escalão de preço e dimensionamento**. Para este guia de início rápido, selecione a camada **F1-Free** se ela ainda estiver disponível em sua assinatura. Se a camada gratuita não estiver disponível, escolha a camada mais baixa disponível. Para obter mais informações, consulte [preços do Hub IOT](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Dimensionar e dimensionar seu hub IoT](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Selecione **Rever + criar**. Examine as informações do Hub IoT e clique em **criar**. O Hub IoT pode levar alguns minutos para ser criado. Pode monitorizar o progresso no painel **Notificações**.

7. No menu de navegação do Hub IoT, clique em **Adicionar** em **dispositivos IOT**. Adicione uma **ID de dispositivo** e clique em **salvar**.

   ![Adicionar um dispositivo ao Hub IoT](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Depois que o dispositivo for criado, abra o dispositivo na lista de **dispositivos IOT** . Copie a **cadeia de conexão--Primary Key** e salve-a em um bloco de notas para usar posteriormente.

   ![Copiar cadeia de conexão do dispositivo do Hub IoT](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Criar armazenamento de BLOBs

1. No canto superior esquerdo do portal do Azure, selecione **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**.

2. No painel **criar conta de armazenamento** , insira um nome de conta de armazenamento, um local e um grupo de recursos. Escolha o mesmo local e grupo de recursos que o Hub IoT que você criou. Em seguida, clique em **revisar + criar** para criar a conta.

   ![Criar conta de armazenamento](./media/quick-create-vs-code/create-storage-account.png)

3. Depois que sua conta de armazenamento for criada, selecione o bloco **BLOBs** no painel **visão geral** .

   ![Descrição geral da conta de armazenamento](./media/quick-create-vs-code/blob-storage.png)

4. Na página **serviço blob** , selecione **contêiner** e forneça um nome para o contêiner, como *Container1*. Deixe o **nível de acesso público** como **privado (sem acesso anônimo)** e selecione **OK**.

   ![Criar contentor de blobs](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics

1. Em Visual Studio Code, pressione **Ctrl + Shift + P** para abrir a paleta de comandos. Em seguida, digite **asa** e selecione **ASA: Crie um novo projeto @ no__t-0.

   ![Criar novo projeto](./media/quick-create-vs-code/create-new-project.png)

2. Insira o nome do projeto, como **myASAproj** e selecione uma pasta para o seu projeto.

    ![Criar nome do projeto](./media/quick-create-vs-code/create-project-name.png)

3. O novo projeto será adicionado ao seu espaço de trabalho. Um projeto ASA consiste no script de consulta **(*. asaql)** , um arquivo **JobConfig. JSON** e um arquivo de configuração **asaproj. JSON** .

   ![Stream Analytics arquivos de projeto no VS Code](./media/quick-create-vs-code/asa-project-files.png)

4. O arquivo de configuração **asaproj. JSON** contém as informações de entradas, saídas e arquivos de configuração de trabalho necessárias para enviar o trabalho de Stream Analytics para o Azure.

   ![Stream Analytics arquivo de configuração de trabalho no VS Code](./media/quick-create-vs-code/job-configuration.png)

> [!Note]
> Ao adicionar entradas e saídas da paleta de comandos, os caminhos correspondentes serão adicionados automaticamente ao **asaproj. JSON** . Se você adicionar ou remover entradas ou saídas diretamente no disco, será necessário adicioná-las ou removê-las manualmente de **asaproj. JSON**. Você pode optar por colocar as entradas e saídas em um único local e, em seguida, referenciá-las em trabalhos diferentes, especificando os caminhos em cada **asaproj. JSON**.

## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

1. Abra **myASAproj. asaql** na pasta do projeto.

2. Adicione a seguinte consulta:

   ```sql
   SELECT * 
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```
## <a name="test-with-sample-data"></a>Testar com dados de exemplo
Antes de executar a consulta na nuvem, você pode testar sua consulta com dados de exemplo locais para verificar a lógica de consulta.

Siga as instruções em [testar com dados de exemplo](vscode-local-run.md) para obter mais detalhes. 

 ![Teste com dados de exemplo no VS Code](./media/quick-create-vs-code/vscode-localrun.gif)

## <a name="define-an-input"></a>Definir uma entrada

1. Selecione **Ctrl + Shift + P** para abrir a paleta de comandos e digite **ASA: Adicione a entrada @ no__t-0.

   ![Adicionar Stream Analytics entrada no VS Code](./media/quick-create-vs-code/add-input.png)

2. Escolha **Hub IOT** para o tipo de entrada.

   ![Selecione o Hub IoT como opção de entrada](./media/quick-create-vs-code/iot-hub.png)

3. Escolha o script de consulta ASA que usará a entrada. Ele deve preencher automaticamente com o caminho do arquivo para **myASAproj. asaql**.

   ![Selecione um script do ASA no Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Insira o nome do arquivo de entrada como **IotHub**.

5. Edite **IoTHub. JSON** com os valores a seguir. Mantenha os valores padrão para os campos não mencionados abaixo. Você pode usar o CodeLens para ajudá-lo a inserir uma cadeia de caracteres, selecionar em uma lista suspensa ou alterar o texto diretamente no arquivo.

   |Definição|Valor sugerido|Descrição|
   |-------|---------------|-----------|
   |Name|Input|Introduza um nome para identificar a entrada da tarefa.|
   |IotHubNamespace|MyASAIoTHub|Escolha ou insira o nome do seu hub IoT. Os nomes de Hub IoT serão detectados automaticamente se forem criados na mesma assinatura.|
   |SharedAccessPolicyName|iothubowner| |

   ![Configurar entrada no Visual Studio Code](./media/quick-create-vs-code/configure-input.png)



## <a name="define-an-output"></a>Definir uma saída

1. Selecione **Ctrl + Shift + P** para abrir a paleta de comandos. Em seguida, digite **ASA: Adicione a saída @ no__t-0.

   ![Adicionar Stream Analytics saída no VS Code](./media/quick-create-vs-code/add-output.png)

2. Escolha **armazenamento de BLOBs** para o tipo de coletor.

3. Escolha o script de consulta ASA que usará essa entrada.

4. Insira o nome do arquivo de saída como **BlobStorage**.

5. Edite **BlobStorage** com os valores a seguir. Mantenha os valores padrão para os campos não mencionados abaixo. Use o CodeLens para ajudá-lo a selecionar em uma lista suspensa ou insira uma cadeia de caracteres. 

   |Definição|Valor sugerido|Descrição|
   |-------|---------------|-----------|
   |Name|Output| Insira um nome para identificar a saída do trabalho.|
   |Conta de Armazenamento|asaquickstartstorage|Escolha ou insira o nome da sua conta de armazenamento. Os nomes de contas de armazenamento são detetados automaticamente se forem criados na mesma subscrição.|
   |Contentor|container1|Selecione o contentor existente que criou na conta de armazenamento.|
   |Padrão do Caminho|saída|Introduza o nome de um caminho de ficheiro a ser criado no contentor.|

 ![Configurar a saída no Visual Studio Code](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Compilar o script

A compilação de script faz duas coisas: Verifique a sintaxe e gere os modelos de Azure Resource Manager para implantação implantada.

Há duas maneiras de disparar a compilação de script:

1. Selecione o script do espaço de trabalho e acione compilar na paleta de comandos. 

   ![Use a paleta de comandos VS Code para compilar o script](./media/quick-create-vs-code/compile-script1.png)

2. Clique com o botão direito do mouse no script e selecione **asa: Compilar script**.

    ![Clique com o botão direito do mouse no script ASA para compilar](./media/quick-create-vs-code/compile-script2.png)

3. Após a compilação, você pode encontrar os dois modelos de Azure Resource Manager gerados na pasta de **implantação** do seu projeto. Esses dois arquivos são usados para implantação implantada.

    ![Stream Analytics modelos de implantação no explorador de arquivos](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Enviar um trabalho de Stream Analytics para o Azure

1. Na janela Editor de scripts do Visual Studio Code, selecione **selecionar em suas assinaturas**.

   ![Selecione o texto de suas assinaturas no editor de scripts](./media/quick-create-vs-code/select-subscription.png)

2. Selecione sua assinatura na lista pop-up.

3. Selecione um trabalho * *. Em seguida, escolha criar um novo trabalho.

4. Insira o nome do trabalho, **myASAjob** e siga as instruções para escolher o grupo de recursos e o local.

5. Selecione **Enviar para o Azure**. Os logs podem ser encontrados na janela de saída. 

6. Quando seu trabalho for criado, você poderá vê-lo no **Stream Analytics Explorer**.

![Listar trabalho no Stream Analytics Explorer](./media/quick-create-vs-code/list-job.png)


## <a name="run-the-iot-simulator"></a>Executar o simulador de IoT

1. Abra o [simulador online do Azure IOT do Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/) em uma nova janela ou guia do navegador.

2. Substitua o espaço reservado na linha 15 pela cadeia de conexão do dispositivo do Hub IoT do Azure que você salvou em uma seção anterior.

3. Clique em **Executar**. A saída deve mostrar os dados do sensor e as mensagens que estão sendo enviadas para o Hub IoT.

   ![Simulador online do Azure IoT do Raspberry Pi](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

1. Abra o **Stream Analytics Explorer** no Visual Studio Code e encontre seu trabalho, **myASAJob**.

2. Clique com o botão direito do mouse no nome do trabalho. Em seguida, selecione **Iniciar** no menu de contexto.

   ![Iniciar Stream Analytics trabalho no VS Code](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Escolha **agora** na janela pop-up para iniciar o trabalho.

4. Observe que o status do trabalho foi alterado para **em execução**. Clique com o botão direito do mouse no nome do trabalho e escolha **abrir exibição de trabalho no portal** para ver as métricas de evento de entrada e saída. Essa ação pode levar alguns minutos.

5. Para exibir os resultados, abra o armazenamento de BLOBs na extensão Visual Studio Code ou na portal do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos, a tarefa de transmissão em fluxo e todos os recursos relacionados. A eliminação da tarefa evita a faturação das unidades de transmissão em fluxo consumidas pela tarefa. Se estiver a planear utilizar a tarefa no futuro, pode pará-la e reiniciá-la mais tarde, quando for necessário. Se não quiser continuar a utilizar esta tarefa, elimine todos os recursos criados por este início rápido ao utilizar os seguintes passos:

1. No menu do lado esquerdo no portal do Azure, selecione **Grupos de recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**, escreva o nome do recurso a eliminar na caixa de texto e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você implantou um trabalho simples de Stream Analytics usando Visual Studio Code. Você também pode implantar trabalhos de Stream Analytics usando o [portal do Azure](stream-analytics-quick-create-portal.md), o [PowerShell](stream-analytics-quick-create-powershell.md)e o Visual Studio (Stream-Analytics-Quick-Create-vs.MD). 

Para saber mais sobre as ferramentas de Azure Stream Analytics para o Visual Studio, prossiga para o seguinte artigo:

> [!div class="nextstepaction"]
> [Use o Visual Studio para ver tarefas do Azure Stream Analytics](stream-analytics-vs-tools.md)
