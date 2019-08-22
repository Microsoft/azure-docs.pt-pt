---
title: Integração contínua e implementação contínua – Azure IoT Edge | Documentos da Microsoft
description: Configurar a integração contínua e implementação contínua – Azure IoT Edge com o Azure DevOps, Pipelines do Azure
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e14025a5a7a3e81404498638d6f6f9c5ff18ed58
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650820"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integração contínua e implementação contínua para o Azure IoT Edge

Você pode adotar facilmente o DevOps com seus aplicativos Azure IoT Edge com as tarefas de Azure IoT Edge internas no Azure Pipelines. Este artigo demonstra como você pode usar os recursos de integração contínua e implantação contínua do Azure Pipelines para compilar, testar e implantar aplicativos de forma rápida e eficiente em seu Azure IoT Edge. 

![Diagrama - CI e CD ramos para desenvolvimento e produção](./media/how-to-ci-cd/cd.png)

Neste artigo, você aprenderá a usar as tarefas internas de Azure IoT Edge para Azure Pipelines criar dois pipelines para sua solução de IoT Edge. Há quatro ações que podem ser usadas nas tarefas de Azure IoT Edge.
   - As **imagens de módulo Azure IOT Edge-Build** pegam o código da solução IOT Edge e compilam as imagens de contêiner.
   - **Azure IOT Edge-as imagens do módulo Push** enviam imagens de módulo para o registro de contêiner especificado.
   - **Azure IOT Edge-gerar o manifesto de implantação** Obtém um arquivo Deployment. Template. JSON e as variáveis e, em seguida, gera o arquivo de manifesto de implantação final IOT Edge.
   - **Azure IOT Edge-implantar em dispositivos IOT Edge** ajuda a criar implantações IOT Edge para dispositivos de IOT Edge único/vários.

## <a name="prerequisites"></a>Pré-requisitos

* Um repositório Azure Repos. Se você não tiver uma, poderá [criar um novo repositório git em seu projeto](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Uma solução IoT Edge confirmada e enviada por push para seu repositório. Se você quiser criar uma nova solução de exemplo para testar este artigo, siga as etapas em [módulos desenvolver e depurar em Visual Studio Code](how-to-vs-code-develop-module.md) ou [desenvolver e depurar C# módulos no Visual Studio](how-to-visual-studio-develop-csharp-module.md).
   * Para este artigo, tudo o que você precisa é a pasta da solução criada pelos modelos de IoT Edge no Visual Studio Code ou no Visual Studio. Você não precisa criar, enviar por push, implantar ou depurar esse código antes de continuar. Você definirá esses processos em Azure Pipelines. 
   * Se você estiver criando uma nova solução, clone seu repositório localmente primeiro. Em seguida, ao criar a solução, você pode optar por criá-la diretamente na pasta do repositório. Você pode facilmente confirmar e enviar por push os novos arquivos a partir daí. 
* Um registro de contêiner no qual você pode enviar imagens de módulo. Você pode usar o [registro de contêiner do Azure](https://docs.microsoft.com/azure/container-registry/) ou um registro de terceiros. 
* Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) ativo com pelo menos IOT Edge dispositivos para testar os estágios de implantação de teste e produção separados. Você pode seguir os artigos de início rápido para criar um dispositivo IoT Edge no [Linux](quickstart-linux.md) ou no [Windows](quickstart.md)


Para obter mais informações sobre como usar Azure Repos, consulte [compartilhar seu código com o Visual Studio e Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Configurar a integração contínua
Nesta seção, você criará um novo pipeline de compilação. Configure o pipeline para ser executado automaticamente quando você fizer check-in de qualquer alteração no IoT Edge solução de exemplo e publicar logs de compilação.

>[!NOTE]
>Este artigo usa o designer visual DevOps do Azure. Antes de seguir as etapas nesta seção, desative o recurso de visualização para a nova experiência de criação de pipeline YAML. 
>1. No Azure DevOps, selecione o ícone de perfil e selecione **recursos de visualização**.
>2. Desative a **nova experiência de criação de pipeline YAML** . 
>
>Para obter mais informações, consulte [criar um pipeline de compilação](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Entre em sua organização do Azure DevOps (**https\/:/dev.Azure.com/{Your Organization}/** ) e abra o projeto que contém seu repositório de solução de IOT Edge.

   Para este artigo, criamos um repositório chamado **IoTEdgeRepo**. Esse repositório contém **IoTEdgeSolution** que tem o código para um módulo chamado **filtermodule**. 

   ![Abra seu projeto do DevOps](./media/how-to-ci-cd/init-project.png)

2. Navegue até Azure Pipelines em seu projeto. Abra a guia compilações e selecione **novo pipeline**. Ou, se você já tiver pipelines de compilação, selecione o botão **novo** . Em seguida, escolha **novo pipeline de Build**.

    ![Criar um novo pipeline de compilação](./media/how-to-ci-cd/add-new-build.png)

3. Siga os prompts para criar seu pipeline. 

   1. Forneça as informações de origem para seu novo pipeline de Build. Selecione **Azure Repos git** como a origem e, em seguida, selecione o projeto, o repositório e a ramificação em que seu código de solução IOT Edge está localizado. Em seguida, selecione **continuar**. 

      ![Selecionar a origem do pipeline](./media/how-to-ci-cd/pipeline-source.png)

   2. Selecione **trabalho vazio** em vez de um modelo. 

      ![Começar com um processo vazio](./media/how-to-ci-cd/start-with-empty.png)

4. Depois que o pipeline for criado, você será levado para o editor de pipeline. Na descrição do pipeline, escolha o pool de agentes correto com base na sua plataforma de destino: 
    
   * Se gostaria de criar os seus módulos na plataforma amd64 para contentores do Linux, escolha **alojado 1604 da Ubuntu**

   * Se você quiser criar seus módulos na plataforma AMD64 para contêineres do Windows 1809, precisará [Configurar o agente auto-hospedado no Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Se você quiser criar seus módulos na plataforma arm32v7 ou arm64 para contêineres do Linux, precisará [Configurar o agente auto-hospedado no Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Configurar o conjunto de agentes de compilação](./media/how-to-ci-cd/configure-env.png)

5. Seu pipeline vem pré-configurado com um trabalho chamado **agente trabalho 1**. Selecione o sinal de adição **+** () para adicionar três tarefas ao trabalho: **Azure IOT Edge** duas vezes, **Copie os arquivos** uma vez e publique os artefatos de **compilação** uma vez. (Passe o mouse sobre o nome de cada tarefa para ver o botão **Adicionar** .)

   ![Adicionar tarefa de Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Quando todas as quatro tarefas forem adicionadas, seu trabalho do Agent será semelhante ao exemplo a seguir:
    
   ![Três tarefas no pipeline de compilação](./media/how-to-ci-cd/add-tasks.png)

6. Selecione a primeira tarefa de **Azure IOT Edge** para editá-la. Essa tarefa compila todos os módulos na solução com a plataforma de destino que você especificar.

   * **Nome para exibição**: Aceite as imagens padrão do **módulo Azure IOT Edge Build**.
   * **Ação**: Aceite as imagens padrão do **módulo de compilação**. 
   * **arquivo. Template. JSON**: Selecione as reticências ( **...** ) e navegue até o arquivo **Deployment. Template. JSON** no repositório que contém sua solução de IOT Edge. 
   * **Plataforma padrão**: Selecione a plataforma apropriada para seus módulos com base em seu dispositivo de IoT Edge de destino. 
   * **Variáveis de saída**: As variáveis de saída incluem um nome de referência que você pode usar para configurar o caminho do arquivo no qual o arquivo Deployment. JSON será gerado. Defina o nome de referência como algo que se memorizar como o **Edge**. 

7. Selecione a segunda tarefa de **Azure IOT Edge** para editá-la. Essa tarefa envia por push todas as imagens de módulo para o registro de contêiner que você selecionar.

   * **Nome para exibição**: O nome de exibição é atualizado automaticamente quando o campo de ação é alterado. 
   * **Ação**: Use a lista suspensa para selecionar **imagens de módulo Push**. 
   * **Tipo de registro de contêiner**: Selecione o tipo de registro de contêiner que você usa para armazenar suas imagens de módulo. Dependendo de qual tipo de registro você escolher, o formulário será alterado. Se você escolher **registro de contêiner do Azure**, use as listas suspensas para selecionar a assinatura do Azure e o nome do registro de contêiner. Se você escolher **registro de contêiner genérico**, selecione **novo** para criar uma conexão de serviço do registro. 
   * **arquivo. Template. JSON**: Selecione as reticências ( **...** ) e navegue até o arquivo **Deployment. Template. JSON** no repositório que contém sua solução de IOT Edge. 
   * **Plataforma padrão**: Selecione a mesma plataforma que as imagens de módulo criadas.

   Se tiver vários registos de contentores para alojar as suas imagens de módulo, precisa duplicar esta tarefa, selecione o registo de contentor diferente e utilizar **ignorar módulos** nas definições avançadas para ignorar as imagens que não são para isso Registro específicos.

8. Selecione a tarefa **copiar arquivos** para editá-la. Use essa tarefa para copiar arquivos para o diretório de preparo do artefato.

   * **Nome para exibição**: Copiar arquivos para: Pasta de destino.
   * **Conteúdo**: Coloque duas linhas nesta seção `deployment.template.json` e. `**/module.json` Esses dois tipos de arquivos são as entradas para gerar IoT Edge manifesto de implantação. Precisa ser copiado para a pasta de preparo do artefato e publicado para o pipeline de liberação.
   * **Pasta de destino**: Coloque a variável `$(Build.ArtifactStagingDirectory)`. Consulte [criar variáveis](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) para saber mais sobre a descrição.

9. Selecione a tarefa **publicar artefatos de compilação** para editá-lo. Forneça o caminho do diretório de preparo do artefato para a tarefa para que o caminho possa ser publicado no pipeline de liberação.
   
   * **Nome para exibição**: Publicar artefato: soltar.
   * **Caminho para publicar**: Coloque a variável `$(Build.ArtifactStagingDirectory)`. Consulte [criar variáveis](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) para saber mais sobre a descrição.
   * **Nome do artefato**: Drop.
   * **Local de publicação do artefato**: Azure Pipelines.


10. Abra a guia gatilhos e marque a caixa para **habilitar a integração contínua**. Certifique-se de que o ramo que contém o código está incluído.

    ![Ativar o acionador de integração contínua](./media/how-to-ci-cd/configure-trigger.png)

11. Salve o novo pipeline de compilação com o botão **salvar** .

Este pipeline agora está configurado para ser executado automaticamente quando você envia por push um novo código para seu repositório. A última tarefa, a publicação de artefatos de pipeline, dispara um pipeline de liberação. Continue na próxima seção para compilar o pipeline de liberação. 

## <a name="configure-continuous-deployment"></a>Configurar a implementação contínua
Nesta seção, você criará um pipeline de liberação que é configurado para ser executado automaticamente quando o pipeline de compilação descartar artefatos e mostrará os logs de implantação em Azure Pipelines.

Criar um novo pipeline e adicionar um novo estágio 

1. Na **versões** separador, escolha **+ novo pipeline**. Ou, se você já tiver pipelines de versão, escolha o botão **+ novo** e selecione **+ novo pipeline de liberação**.  

    ![Adicionar o pipeline de lançamento](./media/how-to-ci-cd/add-release-pipeline.png)

2. Quando for solicitado a selecionar um modelo, escolha começar com um **trabalho vazio**.

    ![Começar com uma tarefa vazia](./media/how-to-ci-cd/start-with-empty-job.png)

3. Seu novo pipeline de versão inicializa com um estágio, chamado **estágio 1**. Renomeie o estágio 1 para **desenvolver** e tratá-lo como um ambiente de teste. Normalmente, os pipelines de implantação contínua têm vários estágios, incluindo **desenvolvimento**, **preparo** e **produção**. Você pode criar mais com base em sua prática DevOps. Feche a janela detalhes do estágio depois que ela for renomeada. 

4. Vincule a versão aos artefatos de compilação publicados pelo pipeline de compilação. Clique em **adicionar** na área de artefactos.

   ![Adicione os artefactos](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Na **página Adicionar um artefato**, selecione **compilação**do tipo de origem. Em seguida, selecione o projeto e o pipeline de compilação que você criou. Em seguida, selecione **Adicionar**.

   ![Adicione um artefacto de compilação](./media/how-to-ci-cd/add-an-artifact.png)

6. Abra os gatilhos de artefato e selecione a alternância para habilitar o gatilho de implantação contínua. Agora, uma nova versão será criada cada vez que uma nova compilação estiver disponível.

   ![Configurar o acionador de implementação contínua](./media/how-to-ci-cd/add-a-trigger.png)

7. O estágio de **desenvolvimento** é pré-configurado com um trabalho e zero tarefas. No menu pipeline, selecione **tarefas** e escolha o estágio **desenvolvimento** .  Selecione o trabalho e a contagem de tarefas para configurar as tarefas neste estágio.

    ![Configurar tarefas de desenvolvimento](./media/how-to-ci-cd/view-stage-tasks.png)

8. No estágio de **desenvolvimento** , você deve ver um **trabalho de agente**padrão. Você pode configurar detalhes sobre o trabalho do Agent, mas a tarefa de implantação é insensível à plataforma para que você possa usar o **VS2017 hospedado** ou o **Ubuntu 1604 hospedado** no **pool de agentes** (ou qualquer outro agente gerenciado por conta própria). 

9. Selecione o sinal de adição **+** () para adicionar duas tarefas. Pesquise e adicione **Azure IOT Edge** duas vezes.

    ![Adicionar tarefas para desenvolvimento](./media/how-to-ci-cd/add-task-qa.png)

10. Selecione a primeira tarefa de **Azure IOT Edge** e configure-a com os seguintes valores:

    * **Nome para exibição**: O nome de exibição é atualizado automaticamente quando o campo de ação é alterado. 
    * **Ação**: Use a lista suspensa para selecionar **gerar manifesto de implantação**. Alterar o valor da ação também atualiza o nome de exibição da tarefa para corresponder.
    * **arquivo. Template. JSON**: Coloque o caminho `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`. O caminho é publicado a partir do pipeline de compilação.
    * **Plataforma padrão**: Escolha o mesmo valor ao criar as imagens de módulo.
    * **Caminho de saída**: Coloque o caminho `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`. Esse caminho é o arquivo de manifesto de implantação IoT Edge final.

    Essas configurações ajudam a substituir as URLs de imagem de `deployment.template.json` módulo no arquivo. O **manifesto de implantação de geração** também ajuda a substituir as variáveis pelo valor exato que você `deployment.template.json` definiu no arquivo. No vs/vs Code, você está especificando o valor real em um `.env` arquivo. Em Azure Pipelines, defina o valor na guia variáveis de pipeline de liberação. Mova para a guia variáveis e configure o nome e o valor da seguinte maneira.

    * **ACR_ADDRESS**: Seu endereço de registro de contêiner do Azure. 
    * **ACR_PASSWORD**: Sua senha do registro de contêiner do Azure.
    * **ACR_USER**: Seu nome de usuário do registro de contêiner do Azure.

    Se você tiver outras variáveis em seu projeto, poderá especificar o nome e o valor nessa guia. O **manifesto de implantação de geração** só pode reconhecer as variáveis `${VARIABLE}` em tipo, verifique se você está usando isso em `*.template.json` seus arquivos.

    ![Configurar variáveis para o pipeline de liberação](./media/how-to-ci-cd/configure-variables.png)

10. Selecione a segunda tarefa de **Azure IOT Edge** e configure-a com os seguintes valores:

    * **Nome para exibição**: O nome de exibição é atualizado automaticamente quando o campo de ação é alterado. 
    * **Ação**: Use a lista suspensa para selecionar **implantar em dispositivos IOT Edge**. Alterar o valor da ação também atualiza o nome de exibição da tarefa para corresponder.
    * **Assinatura do Azure**: Selecione a assinatura que contém o Hub IoT.
    * **Nome do Hub IOT**: Selecione o seu hub IoT. 
    * **Escolha um dispositivo único/múltiplo**: Escolha se deseja que o pipeline de liberação seja implantado em um dispositivo ou em vários dispositivos. 
      * Se você implantar em um único dispositivo, insira o **IOT Edge ID do dispositivo**. 
      * Se você estiver implantando em vários dispositivos, especifique a **condição de destino**do dispositivo. A condição de destino é um filtro para corresponder a um conjunto de dispositivos IoT Edge no Hub IoT. Se pretender utilizar etiquetas do dispositivo como a condição, terá de atualizar os dispositivos correspondentes etiquetas com twin de dispositivos do IoT Hub. Atualize a **ID de implantação IOT Edge** e **IOT Edge prioridade de implantação** nas configurações avançadas. Para obter mais informações sobre como criar uma implantação para vários dispositivos, consulte [entender IOT Edge](module-deployment-monitoring.md)implantações automáticas.
    * Expanda Configurações avançadas, selecione **IOT Edge ID da implantação**, coloque `$(System.TeamProject)-$(Release.EnvironmentName)`a variável. Isso mapeia o projeto e o nome da versão com sua ID de implantação IoT Edge.

11. Selecione **salvar** para salvar as alterações no novo pipeline de lançamento. Retorne ao modo de exibição de pipeline selecionando **pipeline** no menu. 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verifique se o IoT Edge CI/CD com a compilação e lançar pipelines

Para acionar uma tarefa de compilação, pode emitir uma confirmação para o repositório de código fonte ou acionar manualmente. Nesta seção, você dispara manualmente o pipeline de CI/CD para testar se ele funciona. Em seguida, verifique se a implantação foi realizada com sucesso.

1. Navegue até o pipeline de compilação que você criou no início deste artigo. 

2. Você pode disparar um trabalho de compilação em seu pipeline de compilação selecionando o botão de **fila** como na captura de tela a seguir.

    ![Acionador manual](./media/how-to-ci-cd/manual-trigger.png)

3. Selecione o trabalho de compilação para observar seu progresso. Se o pipeline de compilação for concluído com êxito, ele disparará uma liberação para o estágio de **desenvolvimento** . 

    ![Criar registos](./media/how-to-ci-cd/build-logs.png)

4. A versão de **desenvolvimento** bem-sucedida cria IOT Edge implantação para dispositivos de IOT Edge de destino.

    ![Versão para desenvolvimento](./media/how-to-ci-cd/pending-approval.png)

5. Clique em estágio de **desenvolvimento** para ver os logs de liberação.

    ![Logs de liberação](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>Passos Seguintes
* IoT Edge exemplo de práticas recomendadas do DevOps no [projeto DevOps do Azure para IOT Edge](how-to-devops-project.md)
* Compreender a implementação de IoT Edge no [implementações de compreender o IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md)
* Siga os passos para criar, atualizar ou eliminar uma implementação no [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md).
