---
title: Início rápido-criar um trabalho de Azure Stream Analytics no Visual Studio Code
description: Este guia de início rápido mostra como começar criando um trabalho de Stream Analytics, Configurando entradas e saídas e definindo uma consulta com Visual Studio Code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 09/16/2019
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: d95a3334fe5a7767446c92f8f8668e4da09fca42
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76169856"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Início rápido: criar um trabalho de Azure Stream Analytics no Visual Studio Code (visualização)

Este guia de início rápido mostra como criar e executar um trabalho de Azure Stream Analytics usando a extensão de ferramentas de Azure Stream Analytics para Visual Studio Code. O trabalho de exemplo lê os dados de streaming de um dispositivo do Hub IoT do Azure. Você define um trabalho que calcula a temperatura média quando mais de 27 ° e grava os eventos de saída resultantes em um novo arquivo no armazenamento de BLOBs.

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [Portal do Azure](https://portal.azure.com/).

* Instalar o [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-tools-extension"></a>Instalar a extensão de ferramentas de Azure Stream Analytics

1. Abra o Visual Studio Code.

2. Em **extensões** no painel esquerdo, procure **Stream Analytics** e selecione **instalar** na extensão ferramentas de **Azure Stream Analytics** .

3. Após a instalação da extensão, verifique se as **Ferramentas do Azure Stream Analytics** estão visíveis nas **extensões habilitadas**.

   ![Azure Stream Analytics ferramentas em extensões habilitadas no Visual Studio Code](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>Ativar a extensão de ferramentas de Azure Stream Analytics

1. Selecione o ícone **do Azure** na barra de atividade do Visual Studio Code. Em **Stream Analytics** na barra lateral, selecione **entrar no Azure**.

   ![Entrar no Azure no Visual Studio Code](./media/quick-create-vs-code/azure-sign-in.png)

2. Quando você estiver conectado, o nome da sua conta do Azure aparecerá na barra de status no canto inferior esquerdo da janela de Visual Studio Code.

> [!NOTE]
> A extensão de ferramentas de Azure Stream Analytics irá conectá-lo automaticamente na próxima vez que você não sair. Se sua conta tiver a autenticação de dois fatores, recomendamos que você use a autenticação por telefone em vez de usar um PIN.
> Se você tiver problemas para listar recursos, sair e entrar novamente geralmente ajuda. Para sair, insira o comando `Azure: Sign Out`.

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho de Stream Analytics, você deve preparar os dados que são configurados posteriormente como a entrada do trabalho. Para preparar os dados de entrada que o trabalho requer, conclua as seguintes etapas:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** > **Internet das Coisas** > **Hub IoT**.

3. No painel **Hub IOT** , insira as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Subscrição  | \<A sua subscrição\> |  Selecione a subscrição do Azure que pretende utilizar. |
   |Grupo de Recursos   |   asaquickstart-resourcegroup  |   Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta. |
   |Região  |  \<Selecione a região mais próxima dos seus utilizadores\> | Selecione uma localização geográfica na qual você pode hospedar o Hub IoT. Use o local mais próximo de seus usuários. |
   |Nome do Hub IoT  | MyASAIoTHub  |   Selecione um nome para o Hub IoT.   |

   ![Criar um hub IoT](./media/quick-create-vs-code/create-iot-hub.png)

4. Selecione **Avançar: definir tamanho e escala**.

5. Faça uma seleção para a **camada de preços e escala**. Para este guia de início rápido, selecione a camada **F1-Free** se ela ainda estiver disponível em sua assinatura. Se a camada gratuita não estiver disponível, escolha a camada mais baixa disponível. Para obter mais informações, consulte [preços do Hub IOT do Azure](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Dimensionar e dimensionar seu hub IoT](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Selecione **Rever + criar**. Examine as informações do Hub IoT e selecione **criar**. A criação do hub IoT pode demorar alguns minutos. Você pode monitorar o progresso no painel **notificações** .

7. No menu de navegação do seu hub IoT, selecione **Adicionar** em **dispositivos IOT**. Adicione uma ID para a **ID do dispositivo**e selecione **salvar**.

   ![Adicionar um dispositivo ao Hub IoT](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Depois que o dispositivo for criado, abra o dispositivo na lista de **dispositivos IOT** . Copie a cadeia de caracteres na **cadeia de conexão (chave primária)** e salve-a em um bloco de notas para usar mais tarde.

   ![Copiar cadeia de conexão do dispositivo do Hub IoT](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>Executar o simulador de IoT

1. Abra o [simulador online do Azure IOT do Raspberry Pi](https://azure-samples.github.io/raspberry-pi-web-simulator/) em uma nova janela ou guia do navegador.

2. Substitua o espaço reservado na linha 15 pela cadeia de conexão do dispositivo do Hub IoT que você salvou anteriormente.

3. Selecione **Executar**. A saída deve mostrar os dados do sensor e as mensagens que estão sendo enviadas para o Hub IoT.

   ![Simulador online do Azure IoT do Raspberry Pi com saída](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>Criar armazenamento de BLOBs

1. No canto superior esquerdo da portal do Azure, selecione **criar um recurso** > **armazenamento** > **conta de armazenamento**.

2. No painel **criar conta de armazenamento** , insira um nome de conta de armazenamento, um local e um grupo de recursos. Escolha o mesmo local e grupo de recursos que o Hub IoT que você criou. Em seguida, selecione **revisar + criar** para criar a conta.

   ![Criar conta de armazenamento](./media/quick-create-vs-code/create-storage-account.png)

3. Depois que sua conta de armazenamento for criada, selecione o bloco **BLOBs** no painel **visão geral** .

   ![Descrição geral da conta de armazenamento](./media/quick-create-vs-code/blob-storage.png)

4. Na página **serviço blob** , selecione **contêiner** e forneça um nome para o contêiner, como **Container1**. Deixe o **nível de acesso público** como **privado (sem acesso anônimo)** e selecione **OK**.

   ![Criar um contentor de blobs](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics

1. Em Visual Studio Code, selecione **Ctrl + Shift + P** para abrir a paleta de comandos. Em seguida, insira **asa** e selecione **asa: criar novo projeto**.

   ![Criar um novo projeto](./media/quick-create-vs-code/create-new-project.png)

2. Insira o nome do projeto, como **myASAproj**, e selecione uma pasta para seu projeto.

    ![Criar um nome de projeto](./media/quick-create-vs-code/create-project-name.png)

3. O novo projeto é adicionado ao seu espaço de trabalho. Um projeto Stream Analytics consiste em três pastas: **entradas**, **saídas**e **funções**. Ele também tem o script de consulta **(*. asaql)** , um arquivo **JobConfig. JSON** e um arquivo de configuração **asaproj. JSON** .

    O arquivo de configuração **asaproj. JSON** contém as informações de entradas, saídas e arquivos de configuração de trabalho necessárias para enviar o trabalho de Stream Analytics para o Azure.

    ![Stream Analytics arquivos de projeto no Visual Studio Code](./media/quick-create-vs-code/asa-project-files.png)

> [!Note]
> Quando você estiver adicionando entradas e saídas da paleta de comandos, os caminhos correspondentes serão adicionados automaticamente a **asaproj. JSON** . Se você adicionar ou remover entradas ou saídas diretamente no disco, será necessário adicioná-las ou removê-las manualmente de **asaproj. JSON**. Você pode optar por colocar as entradas e saídas em um único local e, em seguida, referenciá-las em trabalhos diferentes, especificando os caminhos em cada arquivo **asaproj. JSON** .

## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

1. Abra **myASAproj. asaql** na pasta do projeto.

2. Adicione a seguinte consulta:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="test-the-query-locally-with-sample-data"></a>Testar a consulta localmente com dados de exemplo

Antes de executar a consulta na nuvem, você pode testá-la localmente com um arquivo de dados de exemplo local ou com dados capturados da entrada ao vivo para verificar a lógica de consulta.

Siga as instruções em [consultas de teste localmente com dados de exemplo](visual-studio-code-local-run.md) para obter mais detalhes.

 ![Teste com dados de exemplo no Visual Studio Code](./media/vscode-local-run/localrun-localinput.gif)

## <a name="define-a-live-input"></a>Definir uma entrada ao vivo

1. Clique com o botão direito do mouse na pasta **entradas** em seu projeto Stream Analytics. Em seguida, selecione **asa: Adicionar entrada** no menu de contexto.

    ![Adicionar entrada da pasta entradas](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

    Ou selecione **Ctrl + Shift + P** para abrir a paleta de comandos e insira **asa: Adicionar entrada**.

   ![Adicionar Stream Analytics entrada no Visual Studio Code](./media/quick-create-vs-code/add-input.png)

2. Escolha **Hub IOT** para o tipo de entrada.

   ![Selecione o Hub IoT como a opção de entrada](./media/quick-create-vs-code/iot-hub.png)

3. Se você adicionou a entrada da paleta de comandos, escolha o Stream Analytics script de consulta que usará a entrada. Ele deve ser preenchido automaticamente com o caminho do arquivo para **myASAproj. asaql**.

   ![Selecione um script de Stream Analytics no Visual Studio Code](./media/quick-create-vs-code/asa-script.png)

4. Escolha **selecionar em suas assinaturas do Azure** no menu suspenso.

    ![Selecionar entre assinaturas](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Edite o arquivo **IoTHub1. JSON** recém-gerado com os valores a seguir. Mantenha valores padrão para campos não mencionados aqui.

   |Definição|Valor sugerido|Descrição|
   |-------|---------------|-----------|
   |Nome|Input|Insira um nome para identificar a entrada do trabalho.|
   |IotHubNamespace|MyASAIoTHub|Escolha ou insira o nome do seu hub IoT. Os nomes de Hub IoT são detectados automaticamente se eles são criados na mesma assinatura.|
   |SharedAccessPolicyName|iothubowner| |

   Você pode usar o recurso CodeLens para ajudá-lo a inserir uma cadeia de caracteres, selecionar em uma lista suspensa ou alterar o texto diretamente no arquivo. A captura de tela a seguir mostra a **seleção de suas assinaturas** como um exemplo. As credenciais são listadas automaticamente e salvas no Gerenciador de credenciais local.

   ![Configurar entrada no Visual Studio Code](./media/quick-create-vs-code/configure-input.png)

   ![Configurar o valor de entrada no Visual Studio Code](./media/quick-create-vs-code/configure-input-value.png)

## <a name="preview-input"></a>Visualizar entrada

Selecione **Visualizar dados** em **IoTHub1. JSON** na linha superior. Alguns dados de entrada serão buscados do Hub IoT e mostrados na janela de visualização. Esse processo pode levar algum tempo.

 ![Visualizar entrada ao vivo](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-an-output"></a>Definir uma saída

1. Selecione **Ctrl + Shift + P** para abrir a paleta de comandos. Em seguida, insira **asa: adicionar saída**.

   ![Adicionar Stream Analytics saída no Visual Studio Code](./media/quick-create-vs-code/add-output.png)

2. Escolha **armazenamento de BLOBs** para o tipo de coletor.

3. Escolha o script de consulta Stream Analytics que usará essa entrada.

4. Insira o nome do arquivo de saída como **BlobStorage**.

5. Edite **BlobStorage** usando os valores a seguir. Mantenha valores padrão para campos não mencionados aqui. Use o recurso CodeLens para ajudá-lo a selecionar em uma lista suspensa ou insira uma cadeia de caracteres.

   |Definição|Valor sugerido|Descrição|
   |-------|---------------|-----------|
   |Nome|Saída| Insira um nome para identificar a saída do trabalho.|
   |Conta de Armazenamento|asaquickstartstorage|Escolha ou insira o nome da sua conta de armazenamento. Os nomes de conta de armazenamento são detectados automaticamente se eles são criados na mesma assinatura.|
   |Contentor|container1|Selecione o contentor existente que criou na conta de armazenamento.|
   |Padrão do Caminho|saída|Introduza o nome de um caminho de ficheiro a ser criado no contentor.|

   ![Configurar a saída no Visual Studio Code](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Compilar o script

A compilação de script verifica a sintaxe e gera os modelos de Azure Resource Manager para implantação automática.

Há duas maneiras de disparar a compilação de script:

- Selecione o script no espaço de trabalho e, em seguida, compile na paleta de comandos.

   ![Use a paleta de comandos Visual Studio Code para compilar o script](./media/quick-create-vs-code/compile-script1.png)

- Clique com o botão direito do mouse no script e selecione **asa: Compilar script**.

    ![Clique com o botão direito do mouse no script Stream Analytics para compilar](./media/quick-create-vs-code/compile-script2.png)

Após a compilação, você pode encontrar os dois modelos de Azure Resource Manager gerados na pasta **implantar** do seu projeto. Esses dois arquivos são usados para implantação automática.

![Stream Analytics modelos de implantação no explorador de arquivos](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Enviar um trabalho de Stream Analytics para o Azure

1. Na janela Editor de scripts do script de consulta, selecione **Enviar para o Azure**.

   ![Selecione o texto de suas assinaturas no editor de scripts](./media/quick-create-vs-code/submit-job.png)

2. Selecione sua assinatura na lista pop-up.

3. Escolha **selecionar um trabalho**. Em seguida, escolha **criar novo trabalho**.

4. Insira o nome do trabalho, **myASAjob**. Em seguida, siga as instruções para escolher o grupo de recursos e o local.

5. Selecione **Enviar para o Azure**. Você pode encontrar os logs na janela saída. 

6. Quando seu trabalho for criado, você poderá vê-lo no **Stream Analytics Explorer**.

    ![Trabalho listado no Stream Analytics Explorer](./media/quick-create-vs-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

1. Abra o **Stream Analytics Explorer** no Visual Studio Code e encontre seu trabalho, **myASAJob**.

2. Clique com o botão direito do mouse no nome do trabalho. Em seguida, selecione **Iniciar** no menu de contexto.

   ![Iniciar o trabalho de Stream Analytics no Visual Studio Code](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Escolha **agora** na janela pop-up para iniciar o trabalho.

4. Observe que o status do trabalho foi alterado para **em execução**. Clique com o botão direito do mouse no nome do trabalho e selecione **abrir exibição de trabalho no portal** para ver as métricas de evento de entrada e saída. Essa ação pode levar alguns minutos.

5. Para exibir os resultados, abra o armazenamento de BLOBs na extensão Visual Studio Code ou na portal do Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando eles não forem mais necessários, exclua o grupo de recursos, o trabalho de streaming e todos os recursos relacionados. A exclusão do trabalho evita a cobrança das unidades de streaming que o trabalho consome. 

Se estiver planejando usar o trabalho no futuro, você poderá interrompê-lo e reiniciá-lo mais tarde. Se você não for usar esse trabalho novamente, use as seguintes etapas para excluir todos os recursos que você criou neste guia de início rápido:

1. No menu à esquerda na portal do Azure, selecione **grupos de recursos** e, em seguida, selecione o nome do recurso que você criou.  

2. Na página do grupo de recursos, selecione **Eliminar**. Insira o nome do recurso a ser excluído na caixa de texto e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você implantou um trabalho simples de Stream Analytics usando Visual Studio Code. Você também pode implantar trabalhos de Stream Analytics usando o [portal do Azure](stream-analytics-quick-create-portal.md), o [PowerShell](stream-analytics-quick-create-powershell.md)e o [Visual Studio](stream-analytics-quick-create-vs.md).

Para saber mais sobre as ferramentas de Azure Stream Analytics para Visual Studio Code, vá para os seguintes artigos:

* [Testar Azure Stream Analytics trabalhos localmente contra a entrada ao vivo com Visual Studio Code](visual-studio-code-local-run-live-input.md)

* [Usar Visual Studio Code para exibir trabalhos de Azure Stream Analytics](visual-studio-code-explore-jobs.md)

* [Configurar pipelines de CI/CD usando o pacote NPM](setup-cicd-vs-code.md)
