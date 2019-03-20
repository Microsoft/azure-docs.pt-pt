---
title: Integração contínua e implementação contínua – Azure IoT Edge | Documentos da Microsoft
description: Configurar a integração contínua e implementação contínua – Azure IoT Edge com o Azure DevOps, Pipelines do Azure
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f449449c542ce6ac04daa58ff37a3577f0d75aee
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57896233"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integração contínua e implementação contínua para o Azure IoT Edge

Pode adotar facilmente o DevOps com as suas aplicações do Azure IoT Edge com as tarefas incorporadas do Azure IoT Edge em Pipelines do Azure. Este artigo demonstra como pode utilizar a integração contínua e os recursos de implementação contínua do Pipelines do Azure para criar, testar e implementar aplicações rápida e eficiente para o Azure IoT Edge. 

Neste artigo, saiba como utilizar as tarefas incorporadas do Azure IoT Edge para Pipelines do Azure para criar dois pipelines para a sua solução de IoT Edge. O primeiro usa seu código e compila a solução, enviar suas imagens de módulo para o seu registo de contentor e a criação de um manifesto de implantação. A segunda implanta seus módulos de hardware para dispositivos do IoT Edge visados.  

![Diagrama - CI e CD ramos para desenvolvimento e produção](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>Pré-requisitos

* Um repositório de repositórios do Azure. Se não tiver uma, pode [criar um novo repositório de Git no seu projeto](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Uma solução de IoT Edge compromisso e enviados por push para o seu repositório. Se quiser criar uma nova solução de exemplo para fins de teste neste artigo, siga os passos em [módulos de desenvolver e depurar no Visual Studio Code](how-to-vs-code-develop-module.md) ou [desenvolver e depurar C# módulos no Visual Studio](how-to-visual-studio-develop-csharp-module.md).
   * Neste artigo, tudo o que precisa é a pasta de solução criada pelos modelos do IoT Edge no Visual Studio Code ou o Visual Studio. Não precisa de criar, emitir, implementar ou depurar esse código antes de continuar. Configure esses processos nos Pipelines do Azure. 
   * Se estiver a criar uma nova solução, clone o repositório localmente em primeiro lugar. Em seguida, ao criar a solução pode optar por criá-lo diretamente na pasta do repositório. Pode facilmente consolidar e enviar os ficheiros de novo a partir daí. 
* Um registo de contentores em que pode enviar imagens de módulo. Pode usar [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) ou um registo de terceiros. 
* Um ativo [IoT hub](../iot-hub/iot-hub-create-through-portal.md) com, pelo menos, dispositivos de IoT Edge para testar as fases de implementação de produção e teste separadas. Pode seguir os artigos de início rápido para criar um dispositivo IoT Edge num [Linux](quickstart-linux.md) ou [Windows](quickstart.md)


Para obter mais informações sobre como utilizar o Azure repositórios, consulte [compartilhar seu código com o Visual Studio e transparente de repositórios do Azure](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Configure a integração contínua
Nesta secção, vai criar um novo pipeline de compilação. Configure o pipeline para ser executada automaticamente quando verificar todas as alterações para o exemplo de solução de IoT Edge e publicar os registos de compilação.

>[!NOTE]
>Este artigo utiliza o designer visual de DevOps do Azure. Antes de seguir os passos nesta secção, desative a funcionalidade de pré-visualização para a nova experiência de criação do pipeline YAML. 
>1. Na programação e operações do Azure, selecione o ícone do perfil, em seguida, selecione **funcionalidades de pré-visualização**.
>2. Ative **experiência de criação do novo YAML pipeline** desativado. 
>
>Para obter mais informações, consulte [criar um pipeline de compilação](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Inicie sessão na sua organização de DevOps do Azure (**https:\//dev.azure.com/{your organização} /**) e abra o projeto que contém o seu repositório de solução de IoT Edge.

   Neste artigo, criamos um repositório chamado **IoTEdgeRepo**. Esse repositório contém **IoTEdgeSolution** que tem o código para um módulo com o nome **filtermodule**. 

   ![Abra o projeto de DevOps](./media/how-to-ci-cd/init-project.png)

2. Navegue para os Pipelines do Azure no seu projeto. Abra o **baseia-se** separador e selecione **novo pipeline**. Ou, se já tiver pipelines de compilação, selecione o **New** botão. Em seguida, escolha **criar novo pipeline**.

    ![Criar um novo pipeline de compilação](./media/how-to-ci-cd/add-new-build.png)

3. Siga as instruções para criar o seu pipeline. 

   1. Forneça as informações de origem para o seu novo pipeline de compilação. Selecione **Azure repositórios Git** como a origem, em seguida, selecione o projeto, o repositório e o ramo onde está localizado o seu código de solução de IoT Edge. Em seguida, selecione **continuar**. 

      ![Selecione a sua origem de pipeline](./media/how-to-ci-cd/pipeline-source.png)

   2. Selecione **tarefa vazia** em vez de um modelo. 

      ![Começar com um processo vazio](./media/how-to-ci-cd/start-with-empty.png)

4. Depois de criar o pipeline, será direcionado para o editor do pipeline. Em sua descrição do pipeline, selecione o conjunto de agentes correto com base na sua plataforma de destino: 
    
   * Se gostaria de criar os seus módulos na plataforma amd64 para contentores do Linux, escolha **alojado 1604 da Ubuntu**

   * Se gostaria de criar os seus módulos na plataforma amd64 para contentores do Windows 1809, terá [configure autoalojado agente no Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Se gostaria de criar os seus módulos na plataforma arm32v7 para contentores do Linux, tem de [configure autoalojado agente no Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
     ![Configurar o conjunto de agentes de compilação](./media/how-to-ci-cd/configure-env.png)

5. O pipeline vem pré-configurada com uma tarefa denominada **tarefa de agente 1**. Selecione o sinal (**+**) para adicionar três tarefas da tarefa: **O Azure IoT Edge** duas vezes, e **publicar artefactos de compilação** depois. (Passe o rato sobre o nome de cada tarefa para ver os **adicionar** botão.)

   ![Adicionar tarefa do Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Quando todos os três tarefas são adicionadas, a tarefa de agente terá um aspeto semelhante ao seguinte exemplo:
    
   ![Três tarefas no pipeline de compilação](./media/how-to-ci-cd/add-tasks.png)

6. Selecione a primeira **do Azure IoT Edge** tarefas editá-lo. Esta tarefa cria todos os módulos na solução com a plataforma de destino que especificar, também gera o **deployment.json** ficheiro que diz para seus dispositivos IoT Edge como configurar a implementação.

   * **Nome a apresentar**: Aceite a predefinição **do Azure IoT Edge - imagens de módulo de compilação**.
   * **Ação**: Aceite a predefinição **criar imagens de módulo**. 
   * **. ficheiro Template JSON**: Selecione as reticências (**...** ) e navegue para o **deployment.template.json** ficheiros no repositório que contém a sua solução de IoT Edge. 
   * **Plataforma padrão**: Selecione a plataforma adequada para seus módulos com base no seu dispositivo IoT Edge de destino. 
   * **Variáveis de saída**: As variáveis de saída incluem um nome de referência que pode utilizar para configurar o caminho do ficheiro onde o ficheiro de deployment.json será gerado. Defina o nome de referência algo fácil de memorizar como **edge**. 

7. Selecione a segunda **do Azure IoT Edge** tarefas editá-lo. Esta tarefa envia todas as imagens de módulo para o registo de contentor que selecionou. Ele também adiciona as credenciais de registo de contentor para o **deployment.json** para que o seu dispositivo IoT Edge pode acessar as imagens de módulo de ficheiros. 

   * **Nome a apresentar**: O nome a apresentar é atualizado automaticamente quando o campo de ação é alterado. 
   * **Ação**: Utilize a lista pendente para selecionar **enviar imagens de módulo**. 
   * **O tipo de registo de contentor**: Selecione o tipo de registo de contentor que utiliza para armazenar as imagens de módulo. Consoante o tipo de registo que escolher, as alterações de formulário. Se escolher **Azure Container Registry**, utilize as listas pendentes para selecionar a subscrição do Azure e o nome do seu registo de contentor. Se escolher **genérico Container Registry**, selecione **New** para criar uma ligação de serviço de registo. 
   * **. ficheiro Template JSON**: Selecione as reticências (**...** ) e navegue para o **deployment.template.json** ficheiros no repositório que contém a sua solução de IoT Edge. 
   * **Plataforma padrão**: Selecione a mesma plataforma as suas imagens de módulo criados.

   Se tiver vários registos de contentores para alojar as suas imagens de módulo, precisa duplicar esta tarefa, selecione o registo de contentor diferente e utilizar **ignorar módulos** nas definições avançadas para ignorar as imagens que não são para isso Registro específicos.

8. Selecione o **publicar artefactos de compilação** tarefas editá-lo. Forneça o caminho de ficheiro para o ficheiro de implementação gerado pela tarefa de compilação. Definir o **caminho para publicar** valor de acordo com a variável de saída que definiu na tarefa de módulo de compilação. Por exemplo, `$(edge.DEPLOYMENT_FILE_PATH)`. Deixe os outros valores nas predefinições. 

9. Abra o **Acionadores** separador e marque a caixa à **ativar a integração contínua**. Certifique-se de que o ramo que contém o código está incluído.

    ![Ativar o acionador de integração contínua](./media/how-to-ci-cd/configure-trigger.png)

10. Guardar o novo pipeline de compilação com **guardar** botão.

Este pipeline está agora configurado para ser executada automaticamente quando enviar o novo código para o seu repositório. A última tarefa, publicar os artefactos de pipeline, aciona um pipeline de lançamento. Continue para a secção seguinte para criar o pipeline de lançamento. 

## <a name="configure-continuous-deployment"></a>Configurar a implementação contínua
Nesta secção, vai criar um pipeline de versão que está configurado para ser executada automaticamente quando o pipeline de compilação cai artefactos e irá mostrar registos de implementação nos Pipelines do Azure.

Nesta secção, vai criar dois estágios diferentes, para implementações de teste e para implementações de produção. 

### <a name="create-test-stage"></a>Criar a fase de teste

Criar um novo pipeline e configure o seu primeiro estágio para implementações de assurance (controle de qualidade) de qualidade. 

1. Na **versões** separador, escolha **+ novo pipeline**. Ou, se já tiver pipelines de versão, escolha o **+ novo** e selecione **+ novo pipeline de versões**.  

    ![Adicionar o pipeline de lançamento](./media/how-to-ci-cd/add-release-pipeline.png)

2. Quando lhe for pedido para selecionar um modelo, escolha começar com uma **tarefa vazia**.

    ![Começar com uma tarefa vazia](./media/how-to-ci-cd/start-with-empty-job.png)

3. Seu novo pipeline de lançamento inicializa com uma fase, chamada **fase 1**. Mudar o nome da fase 1 para **controle de qualidade** e tratá-lo como um ambiente de teste. Normalmente, a implementação contínua pipelines têm vários estágios. Pode criar mais com base nas suas práticas de DevOps. Feche a janela de detalhes de fase, uma vez que é o nome alterado. 

    ![Criar o estágio de ambiente de teste](./media/how-to-ci-cd/QA-env.png)

4. A versão de uma ligação para os artefactos de compilação que são publicados pelo pipeline de compilação. Clique em **adicionar** na área de artefactos.

   ![Adicione os artefactos](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Na **adicionar uma página de artefacto**, selecione o tipo de origem **criar**. Em seguida, selecione o projeto e o pipeline de compilação que criou. Em seguida, selecione **Adicionar**.

   ![Adicione um artefacto de compilação](./media/how-to-ci-cd/add-an-artifact.png)

6. Abra os acionadores de artefacto e selecione o botão de alternar para ativar o acionador de implementação contínua. Agora, uma nova versão será criada sempre que uma nova compilação está disponível.

   ![Configurar o acionador de implementação contínua](./media/how-to-ci-cd/add-a-trigger.png)

7. O **controle de qualidade** estágio é pré-configurado com uma tarefa e tarefas de zero. No menu do pipeline, selecione **tarefas** , em seguida, escolha a **controle de qualidade** estágio.  Selecione a contagem de trabalhos e tarefas para configurar as tarefas neste estágio.

    ![Configurar tarefas de controle de qualidade](./media/how-to-ci-cd/view-stage-tasks.png)

8. Na fase de controle de qualidade, deverá ver uma predefinição **tarefa de agente**. Pode configurar os detalhes sobre a tarefa de agente, mas a tarefa de implementação é plataforma de minúsculas, pelo que pode utilizar qualquer um **Hosted VS2017** ou **alojados 1604 de Ubuntu** no **conjunto de agentes**(ou qualquer outro agente gerido por si mesmo). 

9. Selecione o sinal (**+**) para adicionar uma tarefa. Procure e adicione **do Azure IoT Edge**. 

    ![Adicionar tarefas para controle de qualidade](./media/how-to-ci-cd/add-task-qa.png)

10. Selecione a nova tarefa do Azure IoT Edge e configurá-lo com os seguintes valores:

    * **Nome a apresentar**: O nome a apresentar é atualizado automaticamente quando o campo de ação é alterado. 
    * **Ação**: Utilize a lista pendente para selecionar **implementar no dispositivo IoT Edge**. Também alterar o valor de ação atualiza o nome de exibição de tarefas para corresponder.
    * **Subscrição do Azure**: Selecione a subscrição que contém o seu IoT Hub.
    * **Nome do IoT Hub**: Selecione o seu hub IoT. 
    * **Selecione o dispositivo único/múltipla**: Escolha se pretende que o pipeline de versões para implementar um ou vários dispositivos. 
      * Se implementar um único dispositivo, introduza o **ID de dispositivo do IoT Edge**. 
      * Se estiver a implementar vários dispositivos, especifique o dispositivo **condição de destino**. A condição de destino é um filtro para corresponder a um conjunto de dispositivos periféricos do IoT Hub. Se pretender utilizar etiquetas do dispositivo como a condição, terá de atualizar os dispositivos correspondentes etiquetas com twin de dispositivos do IoT Hub. Atualização do **ID de implementação do IoT Edge** e **prioridade de implantação do IoT Edge** nas definições avançadas. Para obter mais informações sobre a criação de uma implementação para vários dispositivos, consulte [implementações automáticas de compreender o IoT Edge](module-deployment-monitoring.md).

11. Selecione **guardar** para guardar as alterações para o novo pipeline de lançamento. Regressar à vista de pipeline, selecionando **Pipeline** no menu. 

### <a name="create-production-stage"></a>Criar a fase de produção

Crie uma segunda fase no seu pipeline de lançamento para a implementação de produção. 

1. Fazer uma segunda fase de produção através da clonagem o estágio de controle de qualidade. Coloque o cursor sobre a fase de controle de qualidade, em seguida, selecione o botão de clone. 

    ![Fase de clone](./media/how-to-ci-cd/clone-stage.png)

2. Selecione a fase de novo, chamada **cópia do controle de qualidade**, para abrir as respetivas propriedades. Altere o nome do estágio para **PROD**, para produção. Feche a janela de propriedades da fase. 

3. Para abrir as tarefas da fase de produção, selecione **tarefas** no menu do pipeline, em seguida, escolha a **PROD** estágio. 

4. Selecione a tarefa do Azure IoT Edge para configurar se para o seu ambiente de produção. As definições de implementação são provavelmente a mesma para controle de qualidade e para produção, exceto que deseja registrar um dispositivo diferente ou um conjunto de dispositivos na produção. Atualize o campo de ID de dispositivo ou os campos de ID de destino condição e a implementação, para os seus dispositivos de produção. 

5. Salve-o com o **guardar** botão. E, em seguida, selecione **Pipeline** para regressar à vista do pipeline.
    
6. A forma como este pipeline de lançamento está atualmente configurado, o artefacto de compilação irá acionar a **controle de qualidade** estágio e, em seguida **PROD** testar sempre que uma nova compilação é concluída. No entanto, geralmente deseja integrar alguns casos de teste nos dispositivos de controle de qualidade e aprovar manualmente a implementação de produção. Utilize os seguintes passos para criar uma condição de aprovação para a fase de produção:

    1. Abra o **condições de pré-implantação** painel configurações.

        ![Condições de pré-implantação abertas](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Ativar/desativar a **aprovações de pré-implantação** condição para **ativado**. Adicionar um ou mais utilizadores ou grupos a **aprovadores** campo e personalizar quaisquer outras diretivas de aprovação que pretende. Para guardar as alterações, feche o painel de condições de pré-implementação.
    
       ![Conjunto de condições](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. Guardar o seu pipeline de lançamento com o **guardar** botão. 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verifique se o IoT Edge CI/CD com a compilação e lançar pipelines

Para acionar uma tarefa de compilação, pode emitir uma confirmação para o repositório de código fonte ou acionar manualmente. Nesta secção, vai acionar manualmente o pipeline de CI/CD para testar que ele funciona. Em seguida, certifique-se de que a implementação com êxito.

1. Navegue para o pipeline de compilação que criou no início deste artigo. 

2. Pode acionar uma tarefa de compilação no seu pipeline de compilação, selecionando o **fila** botão como na seguinte captura de ecrã.

    ![Acionador manual](./media/how-to-ci-cd/manual-trigger.png)

3. Selecione a tarefa de compilação para ver o progresso. Se o pipeline de compilação for concluído com êxito, aciona uma versão **controle de qualidade** estágio. 

    ![Os registos de compilação](./media/how-to-ci-cd/build-logs.png)

4. A implementação bem-sucedida **controle de qualidade** estágio aciona uma notificação para o aprovador. Certifique-se de que os módulos implementado com êxito no dispositivo ou dispositivos visados com o estágio de controle de qualidade. Em seguida, navegue para o pipeline de versões e dê a aprovação para a versão Ir para a fase de produção, selecionando o **PROD** botão e, em seguida, selecionando **aprovar**. 

    ![Aprovação pendente](./media/how-to-ci-cd/pending-approval.png)

5. Depois do aprovador aprovar esta alteração, pode ser implementado para **PROD**.

## <a name="next-steps"></a>Passos Seguintes

* Compreender a implementação de IoT Edge no [implementações de compreender o IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md)
* Siga os passos para criar, atualizar ou eliminar uma implementação no [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md).
