---
title: Integração contínua & implementação contínua - Azure IoT Edge
description: Configurar integração contínua e implementação contínua - Azure IoT Edge com Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 398cf947f0a2d250c3cd0ed73a75bc3c091e5f7a
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047535"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integração contínua e implantação contínua para Azure IoT Edge

Pode facilmente adotar DevOps com as suas aplicações Azure IoT Edge com as tarefas Azure IoT Edge incorporadas em Pipelines Azure. Este artigo demonstra como pode utilizar as funcionalidades de integração contínua e implementação contínua dos Gasodutos Azure para construir, testar e implementar aplicações de forma rápida e eficiente para o seu Azure IoT Edge.

![Diagrama - Sucursais de CI e CD para desenvolvimento e produção](./media/how-to-ci-cd/cd.png)

Neste artigo, aprende-se a utilizar as tarefas Azure IoT Edge incorporadas para a Azure Pipelines para criar dois oleodutos para a sua solução IoT Edge. Existem quatro ações que podem ser usadas nas tarefas Azure IoT Edge.

* **Azure IoT Edge - Build Module images** pega no seu código de solução IoT Edge e constrói as imagens do recipiente.
* **Azure IoT Edge - As imagens do Módulo push** empurram as imagens do módulo para o registo do contentor especificado.
* **Azure IoT Edge - Gerar Manifesto de Implantação** leva uma deployment.template.jsno ficheiro e nas variáveis, e depois gera o ficheiro manifesto de implantação de IoT Edge final.
* **Azure IoT Edge - Implementar para dispositivos IoT Edge** ajuda a criar implementações IoT Edge para dispositivos IoT Edge simples/múltiplos.

## <a name="prerequisites"></a>Pré-requisitos

* Um repositório de Azure Repos. Se não tiver um, pode [criar um novo git repo no seu projeto.](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav)
* Uma solução IoT Edge comprometida e empurrada para o seu repositório. Se pretender criar uma nova solução de amostra para testar este artigo, siga os passos em [Módulos De Desenvolvimento e Depurg em Módulos Visual Studio Code](how-to-vs-code-develop-module.md) ou Develop e [depurar C# em Visual Studio](how-to-visual-studio-develop-csharp-module.md).

   Para este artigo, tudo o que precisa é da pasta de solução criada pelos modelos IoT Edge em Código de Estúdio Visual ou Estúdio Visual. Não precisa de construir, empurrar, implementar ou depurar este código antes de prosseguir. Vais definir esses processos em Azure Pipelines.

   Se está a criar uma nova solução, clone primeiro o seu repositório localmente. Em seguida, quando criar a solução pode optar por criá-la diretamente na pasta do repositório. Pode facilmente comprometer-se e empurrar os novos ficheiros a partir daí.

* Um registo de contentores onde pode empurrar imagens do módulo. Pode utilizar [o Registo do Contentor Azure](https://docs.microsoft.com/azure/container-registry/) ou um registo de terceiros.
* Um [hub IoT](../iot-hub/iot-hub-create-through-portal.md) ativo com pelo menos dispositivos IoT Edge para testar as fases separadas de teste e de implantação de produção. Pode seguir os artigos de arranque rápido para criar um dispositivo IoT Edge no [Linux](quickstart-linux.md) ou [Windows](quickstart.md)

Para obter mais informações sobre a utilização do Azure Repos, consulte [Partilhar o seu código com o Visual Studio e a Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Configurar a integração contínua

Nesta secção, cria-se um novo oleoduto de construção. Configure o gasoduto para funcionar automaticamente quando verificar quaisquer alterações na solução IoT Edge da amostra e publicar registos de construção.

>[!NOTE]
>Este artigo usa o designer visual Azure DevOps. Antes de seguir os passos nesta secção, desligue a funcionalidade de pré-visualização da nova experiência de criação do gasoduto YAML.
>
>1. Em Azure DevOps, selecione o ícone de perfil e, em seguida, selecione **funcionalidades de pré-visualização**.
>2. Desligue a **nova experiência de criação de gasodutos YAML.**
>
>Para obter mais informações, consulte [Criar um gasoduto de construção.](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline)

1. Inscreva-se na sua organização Azure DevOps **(https: \/ /dev.azure.com/{your organization}/**) e abra o projeto que contém o repositório de solução IoT Edge.

   Para este artigo, criámos um repositório chamado **IoTEdgeRepo.** Este repositório contém **ioTEdgeSolution** que tem o código para um módulo chamado **filtermodule**.

   ![Abra o seu projeto DevOps](./media/how-to-ci-cd/init-project.png)

2. Navegue para Azure Pipelines no seu projeto. Abra o **separador Builds** e selecione **Novo pipeline**. Ou, se já tiver construído oleodutos, selecione o botão **Novo.** Em seguida, escolha **novo oleoduto de construção.**

    ![Criar um novo pipeline de compilação](./media/how-to-ci-cd/add-new-build.png)

3. Siga as instruções para criar o seu oleoduto.

   1. Forneça a informação de origem para o seu novo oleoduto de construção. Selecione **Azure Repos Git** como fonte, em seguida, selecione o projeto, repositório e ramo onde o seu código de solução IoT Edge está localizado. Em seguida, **selecione Continue**.

      ![Selecione a sua fonte de pipeline](./media/how-to-ci-cd/pipeline-source.png)

   2. Selecione **Trabalho vazio** em vez de um modelo.

      ![Comece com um processo vazio](./media/how-to-ci-cd/start-with-empty.png)

4. Uma vez criado o seu oleoduto, é levado para o editor do oleoduto. Na descrição do seu pipeline, escolha o pool de agente correto com base na sua plataforma-alvo:

   * Se quiser construir os seus módulos na plataforma amd64 para recipientes Linux, escolha **Hosted Ubuntu 1604**

   * Se quiser construir os seus módulos na plataforma amd64 para recipientes Windows 1809, tem de [configurar um agente auto-hospedado no Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Se quiser construir os seus módulos na plataforma arm32v7 ou arm64 para recipientes Linux, tem de [configurar um agente auto-hospedado no Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).

     ![Configurar piscina de agente de construção](./media/how-to-ci-cd/configure-env.png)

5. O seu oleoduto vem pré-configurado com um trabalho chamado **Agente trabalho 1**. Selecione o sinal de mais ( **+** ) para adicionar três tarefas ao trabalho: **Azure IoT Edge** duas vezes, Copy **Files** uma vez e **publicar Artefactos de Construção** uma vez. (Pairar sobre o nome de cada tarefa para ver o botão **Adicionar.)**

   ![Adicione a tarefa Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Quando todas as quatro tarefas são adicionadas, o seu trabalho de Agente parece ser o seguinte exemplo:

   ![Três tarefas no oleoduto de construção](./media/how-to-ci-cd/add-tasks.png)

6. Selecione a primeira tarefa **Azure IoT Edge** para editá-la. Esta tarefa constrói todos os módulos na solução com a plataforma-alvo que especifica.

   * **Nome do visor**: Aceite o **Azure IoT Edge padrão - Construa imagens de módulos**.
   * **Ação**: Aceite as imagens predefinidos **do módulo de construção**.
   * **.template.jsno ficheiro**: Selecione a elipse **(...**) e navegue para a **deployment.template.jsficheiro no** repositório que contém a sua solução IoT Edge.
   * **Plataforma predefinitiva**: Selecione a plataforma adequada para os seus módulos com base no seu dispositivo IoT Edge alvo.
   * **Variáveis de saída**: As variáveis de saída incluem um nome de referência que pode utilizar para configurar o caminho do ficheiro onde o seu deployment.jsno ficheiro será gerado. Desa estaladie o nome de referência para algo memorável como **borda**.


   Estas configurações utilizam o repositório de imagens e a etiqueta que são definidas no `module.json` ficheiro para nomear e marcar a imagem do módulo. **Construir imagens de módulos** também ajuda a substituir as variáveis pelo valor exato que define no `module.json` ficheiro. No Visual Studio ou Visual Studio Code, está a especificar o valor real num `.env` ficheiro. Em Pipelines Azure, define o valor no separador **Variáveis do Pipeline.** Selecione o separador **Variáveis** e configuure o nome e o valor como seguinte:

    * **ACR_ADDRESS:** O seu endereço de registo do contentor Azure. 

    Se tiver outras variáveis no seu projeto, pode especificar **Build module images** o nome e o valor neste separador. `${VARIABLE}` Certifique-se de que utiliza este formato nos seus `**/module.json` ficheiros.
    
7. Selecione a segunda tarefa **Azure IoT Edge** para editá-la. Esta tarefa empurra todas as imagens do módulo para o registo do contentor que seleciona.

   * **Nome do visor**: O nome do visor é atualizado automaticamente quando o campo de ação muda.
   * **Ação**: Utilize a lista de retirada para selecionar **imagens do módulo Push**.
   * **Tipo de registo de contentores**: Selecione o tipo de registo de contentores que utiliza para armazenar as imagens do módulo. Dependendo do tipo de registo que escolher, o formulário muda. Se escolher **o Registo do Contentor Azure,** utilize as listas de dropdown para selecionar a subscrição Azure e o nome do seu registo de contentores. Se escolher **o Registo Genérico de Contentores,** selecione **Novo** para criar uma ligação de serviço de registo.
   * **.template.jsno ficheiro**: Selecione a elipse **(...**) e navegue para a **deployment.template.jsficheiro no** repositório que contém a sua solução IoT Edge.
   * **Plataforma padrão**: Selecione a mesma plataforma que as imagens do módulo construído.

   Se tiver vários registos de contentores para hospedar as imagens do módulo, tem de duplicar esta tarefa, selecionar diferentes registos de contentores e utilizar **módulos bypass** nas definições avançadas para contornar as imagens que não são para este registo específico.

8. Selecione a tarefa **Copy Files** para editá-la. Utilize esta tarefa para copiar ficheiros para o diretório de encenação de artefactos.

   * **Nome do visor**: Copiar ficheiros para: Deixar cair a pasta.
   * Conteúdo : Coloque **duas**linhas nesta secção `deployment.template.json` e `**/module.json` . Estes dois tipos de ficheiros são as entradas para gerar manifesto de implantação IoT Edge. Deve ser copiado para a pasta de encenação do artefacto e publicado para o gasoduto de lançamento.
   * **Pasta-alvo**: Coloque a variável `$(Build.ArtifactStagingDirectory)` . Consulte [variáveis build](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) para saber sobre a descrição.

9. Selecione a tarefa **de Publicar Artefactos de Construção** para editá-lo. Forneça o caminho do diretório de encenação de artefactos para a tarefa para que o caminho possa ser publicado para libertar o pipeline.

   * **Nome do visor**: Publicar Artefacto: cair.
   * **Caminho para publicar**: Coloque a variável `$(Build.ArtifactStagingDirectory)` . Consulte [variáveis build](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) para saber sobre a descrição.
   * **Nome do artefacto:** cair.
   * **Local de publicação de artefactos**: Azure Pipelines.

10. Abra o **separador Gatilhos** e verifique a caixa para Ativar a **integração contínua**. Certifique-se de que o ramo que contém o seu código está incluído.

    ![Ligue o gatilho de integração contínua](./media/how-to-ci-cd/configure-trigger.png)

11. Guarde o novo gasoduto de construção com o botão **Save.**

Este gasoduto está agora configurado para funcionar automaticamente quando introduz um novo código para o seu repo. A última tarefa, publicar os artefactos do gasoduto, desencadeia um oleoduto de libertação. Continue até à secção seguinte para construir o gasoduto de libertação.

## <a name="configure-continuous-deployment"></a>Configurar a implementação contínua

Nesta secção, cria-se um gasoduto de desbloqueio configurado para funcionar automaticamente quando o seu gasoduto de construção deixa cair artefactos, e mostrará registos de implantação em Pipelines Azure.

Criar um novo oleoduto e adicionar uma nova etapa

1. No separador **Versões,** escolha **+ novo pipeline.** Ou, se já tiver os oleodutos de desbloqueio, escolha o botão **+ Novo** e selecione **+ Novo pipeline de lançamento**.  

    ![Adicione o gasoduto de libertação](./media/how-to-ci-cd/add-release-pipeline.png)

2. Quando solicitado para selecionar um modelo, opte por começar com um **trabalho Vazio**.

    ![Comece com um trabalho vazio.](./media/how-to-ci-cd/start-with-empty-job.png)

3. O seu novo oleoduto de lançamento iniciaisiza-se com um estágio, chamado **Fase 1**. Mude o nome da Fase 1 para **dev** e trate-o como um ambiente de teste. Normalmente, os gasodutos de implantação contínua têm várias fases, incluindo **dev,** **encenação** e **prod**. Pode criar mais com base na sua prática de DevOps. Feche a janela dos detalhes do palco assim que for renomeada.

4. Ligue o lançamento aos artefactos de construção que são publicados pelo pipeline de construção. Clique em **Adicionar** na área de artefactos.

   ![Adicionar artefactos](./media/how-to-ci-cd/add-artifacts.png)  

5. In **Adicionar uma página de artefactos**, selecione o tipo de **origem Build**. Em seguida, selecione o projeto e o pipeline de construção que criou. Em seguida, selecione **Adicionar**.

   ![Adicione um artefacto de compilação](./media/how-to-ci-cd/add-an-artifact.png)

6. Abra os gatilhos do artefacto e selecione o alternância para ativar o gatilho de implantação contínua. Agora, um novo lançamento será criado cada vez que uma nova construção estiver disponível.

   ![Configure o gatilho de implantação contínua](./media/how-to-ci-cd/add-a-trigger.png)

7. O **estágio dev** é pré-configurado com um trabalho e zero tarefas. A partir do menu do pipeline, selecione **Tarefas** e escolha o estágio **dev.**  Selecione o trabalho e a contagem de tarefas para configurar as tarefas nesta fase.

    ![Configurar tarefas dev](./media/how-to-ci-cd/view-stage-tasks.png)

8. Na fase **de dev,** deverá ver um trabalho de Agente predefinido . **Agent job** Pode configurar detalhes sobre o trabalho do agente, mas a tarefa de implementação é insensível à plataforma para que possa utilizar **o Hosted VS2017** ou o **Hosted Ubuntu 1604** na piscina do **Agente** (ou qualquer outro agente gerido por si mesmo).

9. Selecione o sinal de mais ( **+** ) para adicionar duas tarefas. Procure e adicione **Azure IoT Edge** duas vezes.

    ![Adicionar tarefas para dev](./media/how-to-ci-cd/add-task-qa.png)

10. Selecione a primeira tarefa **Azure IoT Edge** e configuure-a com os seguintes valores:

    * **Nome do visor**: O nome do visor é atualizado automaticamente quando o campo de ação muda.
    * **Ação**: Utilize a lista de dropdown para selecionar **Gerar manifesto de implementação**. Alterar o valor de ação também atualiza o nome do visor da tarefa para corresponder.
    * **.template.jsno ficheiro**: Coloque o `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` caminho. O caminho é publicado a partir de um oleoduto de construção.
    * **Plataforma padrão**: Escolha o mesmo valor ao construir as imagens do módulo.
    * **Caminho de saída**: Coloque o caminho `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Este caminho é o ficheiro manifesto de implantação IoT Edge final.

    Estas configurações ajudam a substituir os URLs de imagem do módulo no `deployment.template.json` ficheiro. O **manifesto de implantação gerar** também ajuda a substituir as variáveis pelo valor exato definido no `deployment.template.json` ficheiro. No Código VS/VS, está a especificar o valor real num `.env` ficheiro. Em Pipelines Azure, define o valor no separador 'Variáveis de Lançamento' de Pipeline.

    * **ACR_ADDRESS:** O seu endereço de registo do contentor Azure.
    * **ACR_PASSWORD:** A sua senha de registo do contentor Azure.
    * **ACR_USER**: O nome de utilizador do registo do seu registo de contentores Azure.

    Se tiver outras variáveis no seu projeto, pode especificar o nome e o valor neste separador. O **manifesto de implantação de Geração** só pode reconhecer que as variáveis estão no `${VARIABLE}` sabor, certifique-se de que está a usar isso nos seus `*.template.json` ficheiros.

    ![Configure variáveis para o gasoduto de libertação](./media/how-to-ci-cd/configure-variables.png)

11. Selecione a segunda tarefa **Azure IoT Edge** e configuure-a com os seguintes valores:

    * **Nome do visor**: O nome do visor é atualizado automaticamente quando o campo de ação muda.
    * **Ação**: Utilize a lista de dropdown para selecionar **dispositivos Implementar para IoT Edge**. Alterar o valor de ação também atualiza o nome do visor da tarefa para corresponder.
    * **Subscrição Azure**: Selecione a subscrição que contém o seu Hub IoT.
    * **Nome do IoT Hub**: Selecione o seu hub IoT.
    * **Escolha um único/múltiplos dispositivos:** Escolha se deseja que o gasoduto de desbloqueio seja implantado num dispositivo ou em vários dispositivos.
      * Se utilizar para um único dispositivo, introduza o **ID do dispositivo IoT Edge**.
      * Se estiver a ser implantado em vários dispositivos, especifique a **condição de alvo**do dispositivo . A condição do alvo é um filtro que combina com um conjunto de dispositivos IoT Edge no IoT Hub. Se pretender utilizar tags de dispositivo como condição, tem de atualizar os seus dispositivos correspondentes Tags com o dispositivo IoT Hub twin. Atualize a prioridade de **implementação IoT Edge ID** e **IoT Edge** nas definições avançadas. Para obter mais informações sobre a criação de uma implementação para vários dispositivos, consulte [as implementações automáticas Understand IoT Edge](module-deployment-monitoring.md).
    * Expandir Definições Avançadas, selecionar **ID de implementação IoT Edge,** colocar a variável `$(System.TeamProject)-$(Release.EnvironmentName)` . Isto mapeia o nome do projeto e do lançamento com o seu IoT Edge deployment ID.

>[!NOTE]
>Se desejar utilizar implementações em **camadas** no seu pipeline, as implementações em camadas ainda não são suportadas nas tarefas Azure IoT Edge em Azure DevOps.
>
>No entanto, pode utilizar uma [tarefa Azure CLI em Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli) para criar a sua implementação como uma implementação em camadas. Para o valor **do Script Inline,** pode utilizar o [comando de criação de borda az iot](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment):
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

12. **Selecione Guardar** para guardar as alterações no novo oleoduto de lançamento. Volte à vista do gasoduto selecionando **Pipeline** no menu.

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verifique o CI/CD IoT Edge com os gasodutos de construção e libertação

Para desencadear um trabalho de construção, pode empurrar um compromisso para o repositório de código fonte ou desencadeá-lo manualmente. Nesta secção, aciona manualmente o gasoduto CI/CD para testar o seu funcionamento. Então verifique se a implantação tem sucesso.

1. Navegue até ao oleoduto de construção que criou no início deste artigo.

2. Pode ativar um trabalho de construção no seu pipeline de construção selecionando o botão **De fila** como na imagem seguinte.

    ![Gatilho manual](./media/how-to-ci-cd/manual-trigger.png)

3. Selecione o trabalho de construção para assistir ao seu progresso. Se o gasoduto de construção estiver concluído com sucesso, aciona um desbloqueio para o **estágio dev.**

    ![Construir troncos](./media/how-to-ci-cd/build-logs.png)

4. A versão **dev** bem sucedida cria a implementação IoT Edge para direcionar os dispositivos IoT Edge.

    ![Liberar para dev](./media/how-to-ci-cd/pending-approval.png)

5. Clique **em fase de dev** para ver os registos de lançamento.

    ![Registos de versão](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>Passos seguintes

* Amostra de boas práticas do IoT Edge DevOps no [Azure DevOps Project for IoT Edge](how-to-devops-project.md)
* Compreenda a implementação IoT Edge em [implementações de Borda De IoT para dispositivos individuais ou à escala](module-deployment-monitoring.md)
* Caminhe pelos passos para criar, atualizar ou eliminar uma implementação em [módulos de Implementação e Monitorização IoT Edge à escala](how-to-deploy-at-scale.md).
