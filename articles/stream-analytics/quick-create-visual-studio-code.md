---
title: Quickstart - Crie um trabalho Azure Stream Analytics em Código de Estúdio Visual
description: Este quickstart mostra-lhe como começar criando um trabalho stream Analytics, configurando entradas e saídas, e definindo uma consulta com o Código do Estúdio Visual.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 01/18/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 104dfe7b2ce253d7ae42c7484532cfd5b2993aec
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98014616"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-in-visual-studio-code-preview"></a>Quickstart: Criar um trabalho Azure Stream Analytics em Visual Studio Code (pré-visualização)

Este quickstart mostra-lhe como criar e executar um trabalho Azure Stream Analytics usando a extensão Azure Stream Analytics Tools para Visual Studio Code. O trabalho de exemplo lê dados de streaming de um dispositivo Azure IoT Hub. Define-se um trabalho que calcula a temperatura média quando ultrapassa os 27° e escreve os eventos de saída resultantes para um novo ficheiro no armazenamento de bolhas.

> [!NOTE]
> As ferramentas Visual Studio e Visual Studio Code não suportam empregos nas regiões do Leste da China, China Norte, Alemanha Central e Alemanha NorthEast.

## <a name="before-you-begin"></a>Antes de começar

* Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/).

* Inicie sessão no [portal do Azure](https://portal.azure.com/).

* Instale [o Código do Estúdio Visual](https://code.visualstudio.com/).

## <a name="install-the-azure-stream-analytics-tools-extension"></a>Instale a extensão Azure Stream Analytics Tools

1. Abra o Visual Studio Code.

2. A partir **de extensões** no painel esquerdo, procure **stream Analytics** e selecione **Instal** na extensão **Azure Stream Analytics Tools.**

3. Após a instalação da extensão, verifique se as **ferramentas de análise do fluxo Azure** estão visíveis nas **extensões ativadas**.

   ![Ferramentas Azure Stream Analytics sob extensões ativadas no Código do Estúdio Visual](./media/quick-create-visual-studio-code/enabled-extensions.png)

## <a name="activate-the-azure-stream-analytics-tools-extension"></a>Ativar a extensão das Ferramentas de Análise do Fluxo Azure

1. Selecione o ícone **Azure** na barra de atividades do Código do Estúdio Visual. Em **Stream Analytics** na barra lateral, selecione Iniciar **súm em Azure**.

   ![Inscreva-se no Azure em Código de Estúdio Visual](./media/quick-create-visual-studio-code/azure-sign-in.png)

2. Quando estiver assinado, o nome da sua conta Azure aparece na barra de estado no canto inferior esquerdo da janela Visual Studio Code.

> [!NOTE]
> A extensão Azure Stream Analytics Tools irá inscrevê-lo automaticamente na próxima vez se não assinar. Se a sua conta tiver autenticação de dois fatores, recomendamos que utilize a autenticação do telefone em vez de utilizar um PIN.
> Se tiver problemas com a listagem de recursos, a assinatura e a assinatura de novo geralmente ajuda. Para assinar, insira o `Azure: Sign Out` comando.

## <a name="prepare-the-input-data"></a>Preparar os dados de entrada

Antes de definir o trabalho stream Analytics, deve preparar os dados que são configurados posteriormente como entrada de trabalho. Para preparar os dados de entrada que o trabalho necessita, complete os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).

2. Selecione **Criar um recurso** Internet of  >  **Things**  >  **IoT Hub**.

3. No painel **IoT Hub,** insira as seguintes informações:

   |**Definição**  |**Valor sugerido**  |**Descrição**  |
   |---------|---------|---------|
   |Subscrição  | \<Your subscription\> |  Selecione a subscrição do Azure que pretende utilizar. |
   |Grupo de Recursos   |   asaquickstart-resourcegroup  |   Selecione **Criar Novo** e introduza um novo nome de grupo de recursos para a sua conta. |
   |Região  |  \<Select the region that is closest to your users\> | Selecione uma localização geográfica onde pode hospedar o seu hub IoT. Utilize o local mais próximo dos seus utilizadores. |
   |Nome do hub IoT  | MyASAIotHub  |   Selecione um nome para o seu hub IoT.   |

   ![Criar um hub IoT](./media/quick-create-visual-studio-code/create-iot-hub.png)

4. Selecione **Seguinte: Definir tamanho e escala**.

5. Faça uma seleção para **preços e escala.** Para este arranque rápido, selecione o **F1 - Free** tier se ainda estiver disponível na sua subscrição. Se o nível gratuito não estiver disponível, escolha o nível mais baixo disponível. Para mais informações, consulte os preços do [Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

   ![Tamanho e escala do seu hub IoT](./media/quick-create-visual-studio-code/iot-hub-size-and-scale.png)

6. Selecione **Rever + criar**. Reveja as informações do seu hub IoT e **selecione Criar**. A criação do hub IoT pode demorar alguns minutos. Pode monitorizar o progresso no painel **de notificações.**

7. No menu de navegação do seu hub IoT, **selecione Adicionar** em **dispositivos IoT**. Adicione um ID para **identificação do dispositivo** e selecione **Save**.

   ![Adicione um dispositivo ao seu hub IoT](./media/quick-create-visual-studio-code/add-device-iot-hub.png)

8. Depois de criado o dispositivo, abra o dispositivo a partir da lista de **dispositivos IoT.** Copie o fio na **cadeia De ligação (tecla primária)** e guarde-o num bloco de notas para utilizar mais tarde.

   ![Cadeia de ligação do dispositivo do dispositivo do hub IoT de cópia](./media/quick-create-visual-studio-code/save-iot-device-connection-string.png)

## <a name="run-the-iot-simulator"></a>Executar o simulador IoT

1. Abra o [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) num novo separador ou janela do navegador.

2. Substitua o espaço reservado na linha 15 pela cadeia de ligação do dispositivo do hub IoT que guardou anteriormente.

3. Selecione **Executar**. A saída deve mostrar os dados do sensor e as mensagens que estão a ser enviadas para o seu hub IoT.

   ![Raspberry Pi Azure IoT Simulador Online com saída](./media/quick-create-visual-studio-code/ras-pi-connection-string.png)

## <a name="create-blob-storage"></a>Criar armazenamento de bolhas

1. A partir do canto superior esquerdo do portal Azure, selecione **Criar uma** conta de  >  **armazenamento**  >  **de** recursos .

2. No painel de **conta de armazenamento Criar,** insira um nome de conta de armazenamento, localização e grupo de recursos. Escolha o mesmo grupo de localização e recursos que o hub IoT que criou. Em seguida, selecione **Review + criar** para criar a conta.

   ![Criar conta de armazenamento](./media/quick-create-visual-studio-code/create-storage-account.png)

3. Depois da sua conta de armazenamento ser criada, selecione o azulejo **Blobs** no **painel de visão geral.**

   ![Descrição geral da conta de armazenamento](./media/quick-create-visual-studio-code/blob-storage.png)

4. Na página **Serviço Blob,** selecione **Recipiente** e forneça um nome para o seu recipiente, tal como **o contentor1**. Deixe **o nível de acesso público** como Privado **(sem acesso anónimo)** e selecione **OK**.

   ![Criar um contentor de blobs](./media/quick-create-visual-studio-code/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Criar um projeto do Stream Analytics

1. No Código do Estúdio Visual, selecione **Ctrl+Shift+P** para abrir a paleta de comando. Em seguida, insira **as ASA** e selecione **ASA: Criar novo projeto**.

   ![Criar um novo projeto](./media/quick-create-visual-studio-code/create-new-project.png)

2. Insira o nome do seu projeto, como **myASAproj,** e selecione uma pasta para o seu projeto.

    ![Criar um nome de projeto](./media/quick-create-visual-studio-code/create-project-name.png)

3. O novo projeto é adicionado ao seu espaço de trabalho. Um projeto Stream Analytics é composto por três pastas: **Entradas,** **Saídas** e **Funções**. Também tem o script de consulta **(*.asaql)**, um **JobConfig.jsem** arquivo e um **asaproj.jsno** ficheiro de configuração.

    A **asaproj.jsno** ficheiro de configuração contém as informações de ficheiros de configuração de entradas, saídas e de configuração de trabalho necessárias para submeter o trabalho de Stream Analytics ao Azure.

    ![Stream Analytics ficheiros de projeto em Visual Studio Code](./media/quick-create-visual-studio-code/asa-project-files.png)

> [!Note]
> Quando adiciona entradas e saídas da paleta de comando, os caminhos correspondentes são adicionados a **asaproj.js** automaticamente. Se adicionar ou remover diretamente as entradas ou saídas no disco, tem de as adicionar manualmente ou removê-las de **asaproj.jsligados**. Pode optar por colocar as entradas e saídas num só local e depois faz referência a diferentes trabalhos, especificando os caminhos de cada **asaproj.jsarquivados.**

## <a name="define-the-transformation-query"></a>Definir a consulta de transformação

1. Abra **myASAproj.asaql** da sua pasta de projeto.

2. Adicione a seguinte consulta:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="define-a-live-input"></a>Definir uma entrada ao vivo

1. Clique com o botão direito na pasta **Inputs** no seu projeto Stream Analytics. Em seguida, selecione **ASA: Adicione a entrada** no menu de contexto.

    ![Adicionar entrada da pasta Entradas](./media/quick-create-visual-studio-code/add-input-from-inputs-folder.png)

    Ou selecione **Ctrl+Shift+P** para abrir a paleta de comando e **introduza ASA: Adicionar Entrada**.

   ![Adicionar entrada Stream Analytics no Código do Estúdio Visual](./media/quick-create-visual-studio-code/add-input.png)

2. Escolha **o Hub IoT** para o tipo de entrada.

   ![Selecione o hub IoT como a opção de entrada](./media/quick-create-visual-studio-code/iot-hub.png)

3. Se adicionar a entrada da paleta de comando, escolha o script de consulta stream Analytics que utilizará a entrada. Deve ser automaticamente povoado com o caminho do ficheiro para **myASAproj.asaql**.

   ![Selecione um script Stream Analytics no Código do Estúdio Visual](./media/quick-create-visual-studio-code/asa-script.png)

4. Escolha **Selecione as suas Subscrições Azure** do menu suspenso.

    ![Selecione a partir de subscrições](./media/quick-create-visual-studio-code/add-input-select-subscription.png)

5. Editar os **IoTHub1.js** recentemente gerados em ficheiro com os seguintes valores. Mantenha os valores predefinidos para campos não mencionados aqui.

   |Definição|Valor sugerido|Descrição|
   |-------|---------------|-----------|
   |Nome|Input|Insira um nome para identificar a entrada do trabalho.|
   |IotHubNamespace|MyASAIotHub|Escolha ou insira o nome do seu hub IoT. Os nomes dos hubs IoT são automaticamente detetados se forem criados na mesma subscrição.|
   |SharedAccessPolicyName|iothubowner| |

   Pode utilizar a função CodeLens para o ajudar a introduzir uma cadeia, selecionar a partir de uma lista de drop-down ou alterar o texto diretamente no ficheiro. A imagem que se segue mostra **Selecionar das suas Subscrições** como exemplo. As credenciais são auto-listadas e guardadas em gestor de credenciais locais.

   ![Configure a entrada no Código do Estúdio Visual](./media/quick-create-visual-studio-code/configure-input.png)

   ![Configure o valor de entrada no Código do Estúdio Visual](./media/quick-create-visual-studio-code/configure-input-value.png)

## <a name="preview-input"></a>Entrada de pré-visualização

Selecione **os dados de pré-visualização** em **IoTHub1.jsa** partir da linha superior. Alguns dados de entrada serão recolhidos do hub IoT e mostrados na janela de pré-visualização. Este processo pode demorar um pouco.

 ![Pré-visualização da entrada ao vivo](./media/quick-create-visual-studio-code/preview-live-input.png)

## <a name="define-an-output"></a>Definir uma saída

1. Selecione **Ctrl+Shift+P** para abrir a paleta de comando. Em seguida, insira **ASA: Adicionar saída**.

   ![Adicionar saída stream analytics no Código do Estúdio Visual](./media/quick-create-visual-studio-code/add-output.png)

2. Escolha **o Blob Storage** para o tipo de pia.

3. Escolha o roteiro de consulta stream Analytics que utilizará esta entrada.

4. Introduza o nome do ficheiro de saída como **BlobStorage**.

5. Edite **BlobStorage** utilizando os seguintes valores. Mantenha os valores predefinidos para campos não mencionados aqui. Utilize a função CodeLens para o ajudar a selecionar a partir de uma lista de recuos ou introduzir uma cadeia.

   |Definição|Valor sugerido|Descrição|
   |-------|---------------|-----------|
   |Nome|Saída| Insira um nome para identificar a saída do trabalho.|
   |Conta de Armazenamento|asaquickstartstorage|Escolha ou insira o nome da sua conta de armazenamento. Os nomes das contas de armazenamento são automaticamente detetados se forem criados na mesma subscrição.|
   |Contentor|container1|Selecione o contentor existente que criou na conta de armazenamento.|
   |Padrão do Caminho|saída|Introduza o nome de um caminho de ficheiro a ser criado no contentor.|

   ![Configure a saída no Código do Estúdio Visual](./media/quick-create-visual-studio-code/configure-output.png)

## <a name="compile-the-script"></a>Compilar o script

A compilação de scripts verifica a sintaxe e gera os modelos do Gestor de Recursos Azure para a implementação automática.

Há duas maneiras de desencadear a compilação do guião:

- Selecione o script a partir do espaço de trabalho e, em seguida, compile a partir da paleta de comando.

   ![Use a paleta de comando Visual Studio Code para compilar o script](./media/quick-create-visual-studio-code/compile-script1.png)

- Clique com o botão direito no script e selecione **ASA: Compile Script**.

    ![Clique com o botão direito no script Stream Analytics para compilar](./media/quick-create-visual-studio-code/compile-script2.png)

Após a compilação, pode encontrar os dois modelos gerados do Gestor de Recursos Azure na pasta **Implementar** do seu projeto. Estes dois ficheiros são usados para a implantação automática.

![Modelos de implementação de streaming analytics no Explorador de Ficheiros](./media/quick-create-visual-studio-code/deployment-templates.png)

## <a name="submit-a-stream-analytics-job-to-azure"></a>Submeta um trabalho de Stream Analytics ao Azure

1. Na janela do editor de scripts do seu script de consulta, **selecione Submeter-se ao Azure**.

   ![Selecione a partir do texto das suas subscrições no editor de scripts](./media/quick-create-visual-studio-code/submit-job.png)

2. Selecione a sua subscrição a partir da lista pop-up.

3. Escolha **Selecionar um trabalho.** Em seguida, escolha **Criar Novo Emprego**.

4. Insira o seu nome de trabalho, **myASAjob.** Em seguida, siga as instruções para escolher o grupo de recursos e a localização.

5. Selecione **Submeter-se a Azure**. Pode encontrar os registos na janela de saída. 

6. Quando o seu trabalho é criado, pode vê-lo no **Stream Analytics Explorer.**

    ![Trabalho listado no Stream Analytics Explorer](./media/quick-create-visual-studio-code/list-job.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Iniciar a tarefa do Stream Analytics e verificar a saída

1. Open **Stream Analytics Explorer** em Visual Studio Code e encontre o seu trabalho, **myASAJob**.

2. Clique com o direito no nome do trabalho. Em seguida, **selecione Iniciar** a partir do menu de contexto.

   ![Inicie o trabalho de Stream Analytics em Visual Studio Code](./media/quick-create-visual-studio-code/start-asa-job-vs-code.png)

3. Escolha **agora** na janela pop-up para iniciar o trabalho.

4. Note que o estado do trabalho mudou para **Running**. Clique com o botão direito no nome do trabalho e selecione **Open Job View no Portal** para ver as métricas do evento de entrada e saída. Esta ação pode levar alguns minutos.

5. Para visualizar os resultados, abra o armazenamento de bolhas na extensão do Código do Estúdio Visual ou no portal Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, apague o grupo de recursos, o trabalho de streaming e todos os recursos relacionados. A eliminação do trabalho evita cobrar as unidades de streaming que o trabalho consome. 

Se planeias usar o trabalho no futuro, podes detê-lo e reiniciá-lo mais tarde. Se não voltar a utilizar este trabalho, utilize os seguintes passos para eliminar todos os recursos que criou neste arranque rápido:

1. A partir do menu esquerdo no portal Azure, selecione **grupos de Recursos** e, em seguida, selecione o nome do recurso que criou.  

2. Na página do grupo de recursos, selecione **Eliminar**. Introduza o nome do recurso para eliminar na caixa de texto e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Próximos passos

Neste quickstart, implementou um simples trabalho stream Analytics usando o Código de Estúdio Visual. Também pode implementar trabalhos stream Analytics utilizando o [portal Azure,](stream-analytics-quick-create-portal.md) [PowerShell](stream-analytics-quick-create-powershell.md)e [Visual Studio](stream-analytics-quick-create-vs.md).

Para saber mais sobre as ferramentas Azure Stream Analytics para Código de Estúdio Visual, continue a seguir os seguintes artigos:

* [Teste Stream Analytics consultas localmente com dados de amostra usando Código de Estúdio Visual](visual-studio-code-local-run.md)

* [Teste Azure Stream Analytics empregos localmente contra entrada ao vivo com Código de Estúdio Visual](visual-studio-code-local-run-live-input.md)

* [Use código de estúdio visual para ver trabalhos Azure Stream Analytics](visual-studio-code-explore-jobs.md)

* [Configurar os gasodutos CI/CD utilizando o pacote npm](./cicd-overview.md)