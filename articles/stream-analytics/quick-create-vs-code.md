---
title: Quickstart - Crie um trabalho azure Stream Analytics no Visual Studio Code
description: Este quickstart mostra-lhe como começar criando um trabalho stream Analytics, configurando inputs e saídas, e definindo uma consulta com visual studio code.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 01/18/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: e3e878b4f548da64ab04eb079d51b0846cf35c57
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76313880"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Quickstart: Criar um trabalho azure Stream Analytics em Visual Studio Code (pré-visualização)

Este quickstart mostra-lhe como criar e executar um trabalho de Azure Stream Analytics utilizando a extensão azure Stream Analytics Tools para Visual Studio Code. O trabalho de exemplo lê dados de streaming de um dispositivo Azure IoT Hub. Define um trabalho que calcula a temperatura média quando tem mais de 27° e escreve os eventos de saída resultantes para um novo ficheiro no armazenamento de blob.

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/)

* Inicie sessão no [Portal do Azure](https://portal.azure.com/).

* Instale o Código do [Estúdio Visual](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-tools-extension"></a>Instale a extensão de ferramentas de análise de fluxo azure

1. Abra o Visual Studio Code.

2. A partir de **extensões** no painel esquerdo, procure o **Stream Analytics** e selecione **Instalar** na extensão de ferramentas de análise de **fluxo azul.**

3. Depois de instalada a extensão, verifique se as **ferramentas de análise** do Fluxo Azure estão visíveis nas **extensões ativadas**.

   ![Ferramentas de análise azure stream sob extensões ativadas no Código do Estúdio Visual](./media/quick-create-vs-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>Ativar a extensão de ferramentas de análise de fluxo azure

1. Selecione o ícone **Azure** na barra de atividade do Código do Estúdio Visual. Em **Stream Analytics** na barra lateral, selecione Iniciar **sessão em Azure**.

   ![Inscreva-se no Azure no Código do Estúdio Visual](./media/quick-create-vs-code/azure-sign-in.png)

2. Quando está sintetizador, o nome da sua conta Azure aparece na barra de estado no canto inferior esquerdo da janela Visual Studio Code.

> [!NOTE]
> A extensão Azure Stream Analytics Tools irá instá-lo automaticamente na próxima vez se não assinar. Se a sua conta tiver autenticação de dois fatores, recomendamos que utilize a autenticação do telefone em vez de utilizar um PIN.
> Se tiver problemas com a listagem de recursos, assinar e assinar novamente ajuda. Para assinar, insira o comando. `Azure: Sign Out`

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho de Stream Analytics, deve preparar os dados que são posteriormente configurados como entrada de trabalho. Para preparar os dados de entrada que o trabalho necessita, complete os seguintes passos:

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** > **Internet of Things** > **IoT Hub**.

3. No painel **IoT Hub,** introduza as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Subscrição  | \<A sua subscrição\> |  Selecione a subscrição do Azure que pretende utilizar. |
   |Grupo de Recursos   |   asaquickstart-resourcegroup  |   Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta. |
   |Região  |  \<Selecione a região mais próxima dos seus utilizadores\> | Selecione uma localização geográfica onde possa hospedar o seu hub IoT. Utilize a localização mais próxima dos seus utilizadores. |
   |Nome do hub iot  | MyASAIoTHub  |   Selecione um nome para o seu hub IoT.   |

   ![Criar um hub IoT](./media/quick-create-vs-code/create-iot-hub.png)

4. Selecione **Seguinte: Definir tamanho e escala**.

5. Faça uma seleção para **preços e escalões**de escala. Para este arranque rápido, selecione o nível **F1 - Free** se ainda estiver disponível na sua subscrição. Se o nível livre não estiver disponível, escolha o nível mais baixo disponível. Para mais informações, consulte [os preços do Hub Azure IoT.](https://azure.microsoft.com/pricing/details/iot-hub/)

   ![Tamanho e escala o seu hub ioT](./media/quick-create-vs-code/iot-hub-size-and-scale.png)

6. Selecione **Rever + criar**. Reveja as informações do seu hub IoT e selecione **Criar**. A criação do hub IoT pode demorar alguns minutos. Pode monitorizar o progresso no painel de **Notificações.**

7. No menu de navegação do seu hub IoT, **selecione Adicionar** em **dispositivos IoT**. Adicione um ID para **ID do dispositivo,** e selecione **Guardar**.

   ![Adicione um dispositivo ao seu hub IoT](./media/quick-create-vs-code/add-device-iot-hub.png)

8. Depois de criado o dispositivo, abra o dispositivo a partir da lista de **dispositivos IoT.** Copie a cadeia na **cadeia de ligação (chave primária)** e guarde-a para um bloco de notas para utilizar mais tarde.

   ![Cadeia de ligação do dispositivo do hub Copy IoT](./media/quick-create-vs-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>Executar o simulador IoT

1. Abra o [Simulador Online Raspberry Pi Azure IoT](https://azure-samples.github.io/raspberry-pi-web-simulator/) num novo separador ou janela do navegador.

2. Substitua o espaço reservado na linha 15 pela cadeia de ligação do dispositivo do hub IoT que guardou anteriormente.

3. Selecione **Executar**. A saída deve mostrar os dados do sensor e as mensagens que estão a ser enviadas para o seu centro IoT.

   ![Simulador online Raspberry Pi Azure IoT com saída](./media/quick-create-vs-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>Criar armazenamento de bolhas

1. A partir do canto superior esquerdo do portal Azure, selecione **Criar uma** > conta de**armazenamento** > **Storage account**de recursos .

2. No painel de conta de **armazenamento Create,** insira um nome de conta de armazenamento, localização e grupo de recursos. Escolha o mesmo local e grupo de recursos que o centro IoT que criou. Em seguida, selecione **Review + criar** para criar a conta.

   ![Criar conta de armazenamento](./media/quick-create-vs-code/create-storage-account.png)

3. Depois de criada a sua conta de armazenamento, selecione o azulejo **Blobs** no painel **overview.**

   ![Descrição geral da conta de armazenamento](./media/quick-create-vs-code/blob-storage.png)

4. Na página **do Serviço Blob,** selecione **Recipiente** e forneça um nome para o seu recipiente, como **o contentor1**. Deixe o **nível** de acesso público como **Privado (sem acesso anónimo)** e selecione **OK**.

   ![Criar um contentor de blobs](./media/quick-create-vs-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics

1. No Código do Estúdio Visual, selecione **Ctrl+Shift+P** para abrir a paleta de comando. Em **seguida,** introduza asA e selecione **ASA: Criar novo projeto**.

   ![Criar um novo projeto](./media/quick-create-vs-code/create-new-project.png)

2. Insira o nome do seu projeto, como **myASAproj,** e selecione uma pasta para o seu projeto.

    ![Criar um nome de projeto](./media/quick-create-vs-code/create-project-name.png)

3. O novo projeto é adicionado ao seu espaço de trabalho. Um projeto Stream Analytics é composto por três pastas: **Entradas,** **Saídas,** e **Funções.** Também tem o script de consulta **(*.asaql)**, um ficheiro **JobConfig.json** e um ficheiro de configuração **asaproj.json.**

    O ficheiro de configuração **asaproj.json** contém as informações de entrada, saídas e ficheiros de configuração de trabalho necessárias para submeter o trabalho de Stream Analytics ao Azure.

    ![Ficheiros do projeto Stream Analytics no Código do Estúdio Visual](./media/quick-create-vs-code/asa-project-files.png)

> [!Note]
> Quando se adicionam entradas e saídas da paleta de comando, os caminhos correspondentes são adicionados automaticamente ao **asaproj.json.** Se adicionar ou remover diretamente as inputs ou saídas no disco, terá de adicioná-las manualmente ou removê-las do **asaproj.json**. Pode optar por colocar as inputs e saídas num só local e depois remeti-las em diferentes trabalhos, especificando os caminhos em cada ficheiro **asaproj.json.**

## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

1. Abra **myASAproj.asaql** da sua pasta de projeto.

2. Adicione a seguinte consulta:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="test-the-query-locally-with-sample-data"></a>Testar a consulta localmente com dados da amostra

Antes de executar a consulta na nuvem, pode testá-la localmente com um arquivo de dados de amostra local ou com dados capturados a partir de entrada ao vivo para verificar a lógica da consulta.

Siga as instruções em [consultas de teste localmente com dados](visual-studio-code-local-run.md) da amostra para obter mais detalhes.

 ![Teste com dados de amostra no Código do Estúdio Visual](./media/vscode-local-run/localrun-localinput.gif)

## <a name="define-a-live-input"></a>Defina uma entrada ao vivo

1. Clique na pasta **Inputs** no seu projeto Stream Analytics. Em seguida, selecione **ASA: Adicione a entrada** no menu de contexto.

    ![Adicione a entrada da pasta Inputs](./media/quick-create-vs-code/add-input-from-inputs-folder.png)

    Ou selecione **Ctrl+Shift+P** para abrir a paleta de comando e introduzir **ASA: Adicionar Entrada**.

   ![Adicione entrada stream analytics no Código do Estúdio Visual](./media/quick-create-vs-code/add-input.png)

2. Escolha **o IoT Hub** para o tipo de entrada.

   ![Selecione hub IoT como a opção de entrada](./media/quick-create-vs-code/iot-hub.png)

3. Se adicionar a entrada da paleta de comando, escolha o script de consulta Stream Analytics que utilizará a entrada. Deve ser automaticamente povoado com o caminho de ficheiro para **myASAproj.asaql**.

   ![Selecione um script Stream Analytics no Código do Estúdio Visual](./media/quick-create-vs-code/asa-script.png)

4. Escolha **selecione entre as suas Assinaturas Azure** a partir do menu suspenso.

    ![Selecione a partir de subscrições](./media/quick-create-vs-code/add-input-select-subscription.png)

5. Editar o ficheiro **IoTHub1.json** recentemente gerado com os seguintes valores. Mantenha os valores padrão para campos não mencionados aqui.

   |Definição|Valor sugerido|Descrição|
   |-------|---------------|-----------|
   |Nome|Input|Insira um nome para identificar a entrada do trabalho.|
   |Espaço IotHubName|MyASAIoTHub|Escolha ou insira o nome do seu hub IoT. Os nomes do hub ioT são automaticamente detetados se forem criados na mesma subscrição.|
   |Nome de política de acesso partilhado|iothubowner| |

   Pode utilizar a função CodeLens para o ajudar a introduzir uma cadeia, selecionar a partir de uma lista de drop-down ou alterar o texto diretamente no ficheiro. A imagem a seguir mostra **Select a partir das suas Subscrições** como exemplo. As credenciais são auto-listadas e guardadas em gestor de credenciais locais.

   ![Configure a entrada no Código do Estúdio Visual](./media/quick-create-vs-code/configure-input.png)

   ![Configure o valor de entrada no Código do Estúdio Visual](./media/quick-create-vs-code/configure-input-value.png)

## <a name="preview-input"></a>Visualização de entrada

Selecione dados de **pré-visualização** em **IoTHub1.json** a partir da linha superior. Alguns dados de entrada serão recolhidos do hub IoT e mostrados na janela de pré-visualização. Este processo pode demorar um pouco.

 ![Pré-visualizar entrada ao vivo](./media/quick-create-vs-code/preview-live-input.png)

## <a name="define-an-output"></a>Definir uma saída

1. Selecione **Ctrl+Shift+P** para abrir a paleta de comando. Em seguida, introduza **ASA: Adicionar Saída**.

   ![Adicionar saída de Stream Analytics no Código do Estúdio Visual](./media/quick-create-vs-code/add-output.png)

2. Escolha **o Depósito Blob** para o tipo de pia.

3. Escolha o script de consulta Stream Analytics que utilizará esta entrada.

4. Introduza o nome do ficheiro de saída como **BlobStorage**.

5. Editar **BlobStorage** utilizando os seguintes valores. Mantenha os valores padrão para campos não mencionados aqui. Utilize a função CodeLens para o ajudar a selecionar a partir de uma lista de lançamentos ou introduzir uma cadeia.

   |Definição|Valor sugerido|Descrição|
   |-------|---------------|-----------|
   |Nome|Saída| Insira um nome para identificar a saída do trabalho.|
   |Conta de Armazenamento|asaquickstartstorage|Escolha ou insira o nome da sua conta de armazenamento. Os nomes da conta de armazenamento são automaticamente detetados se forem criados na mesma subscrição.|
   |Contentor|container1|Selecione o contentor existente que criou na conta de armazenamento.|
   |Padrão do Caminho|saída|Introduza o nome de um caminho de ficheiro a ser criado no contentor.|

   ![Configure a saída no Código do Estúdio Visual](./media/quick-create-vs-code/configure-output.png)

## <a name="compile-the-script"></a>Compilar o guião

A compilação do script verifica a sintaxe e gera os modelos do Gestor de Recursos Azure para implementação automática.

Há duas maneiras de desencadear a compilação do script:

- Selecione o script a partir do espaço de trabalho e, em seguida, compilar a partir da paleta de comando.

   ![Use a paleta de comando visual Studio Code para compilar o script](./media/quick-create-vs-code/compile-script1.png)

- Clique no script e selecione **ASA: Compile script**.

    ![Clique direito no script Stream Analytics para compilar](./media/quick-create-vs-code/compile-script2.png)

Após a compilação, pode encontrar os dois modelos gerados do Gestor de Recursos Azure na pasta **De implantação** do seu projeto. Estes dois ficheiros são utilizados para a implantação automática.

![Modelos de implementação de Stream Analytics no Explorador de Ficheiros](./media/quick-create-vs-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Envie um trabalho de Stream Analytics ao Azure

1. Na janela do editor de script do seu script de consulta, selecione **Submeter para Azure**.

   ![Selecione a partir do texto das suas subscrições no editor de scripts](./media/quick-create-vs-code/submit-job.png)

2. Selecione a sua subscrição na lista pop-up.

3. Escolha **Selecione um trabalho**. Em seguida, escolha **Criar Novo Emprego**.

4. Insira o seu nome de trabalho, **myASAjob.** Em seguida, siga as instruções para escolher o grupo de recursos e a localização.

5. Selecione **Submeter ao Azure**. Pode encontrar os registos na janela de saída. 

6. Quando o seu trabalho é criado, pode vê-lo no **Stream Analytics Explorer.**

    ![Trabalho listado no Stream Analytics Explorer](./media/quick-create-vs-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

1. Open **Stream Analytics Explorer** em Visual Studio Code e encontre o seu emprego, **myASAJob**.

2. Clique no nome do trabalho. Em seguida, selecione **Iniciar** a partir do menu de contexto.

   ![Inicie o trabalho de Stream Analytics no Código do Estúdio Visual](./media/quick-create-vs-code/start-asa-job-vs-code.png)

3. Escolha **agora** na janela pop-up para começar o trabalho.

4. Note que o estado de trabalho mudou para **Running**. Clique no nome do trabalho e selecione **Open Job View no Portal** para ver as métricas do evento de entrada e saída. Esta ação pode demorar alguns minutos.

5. Para visualizar os resultados, abra o armazenamento de blob na extensão Do Código do Estúdio Visual ou no portal Azure.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não forem necessários, apague o grupo de recursos, o trabalho de streaming e todos os recursos relacionados. A apagar o trabalho evita faturar as unidades de streaming que o trabalho consome. 

Se planeias usar o trabalho no futuro, podes detê-lo e reiniciá-lo mais tarde. Se não vai voltar a usar este trabalho, use os seguintes passos para eliminar todos os recursos que criou neste arranque rápido:

1. A partir do menu esquerdo no portal Azure, selecione **grupos de Recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**. Introduza o nome do recurso para apagar na caixa de texto e, em seguida, **selecione Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, implementou um simples trabalho de Stream Analytics utilizando o Visual Studio Code. Também pode implementar trabalhos stream analytics utilizando o [portal Azure,](stream-analytics-quick-create-portal.md) [PowerShell](stream-analytics-quick-create-powershell.md), e [Visual Studio.](stream-analytics-quick-create-vs.md)

Para saber mais sobre as Ferramentas de Análise de Azure Stream para código de estúdio visual, continue para os seguintes artigos:

* [Test Azure Stream Analytics empregos localmente contra entrada ao vivo com Código de Estúdio Visual](visual-studio-code-local-run-live-input.md)

* [Use o Código do Estúdio Visual para ver empregos do Azure Stream Analytics](visual-studio-code-explore-jobs.md)

* [Configurar os gasodutos CI/CD utilizando o pacote npm](setup-cicd-vs-code.md)
