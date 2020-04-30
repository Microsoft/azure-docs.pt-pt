---
title: Integração contínua & implantação contínua - Azure IoT Edge
description: Configurar integração contínua e implantação contínua - Azure IoT Edge com Azure DevOps, Pipelines Azure
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9a653d13137a3067bfaf51c64c09454a08783e31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82131402"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Integração contínua e implantação contínua para Azure IoT Edge

Pode facilmente adotar DevOps com as suas aplicações Azure IoT Edge com as tarefas Azure IoT Edge incorporadas em Pipelines Azure. Este artigo demonstra como pode utilizar as funcionalidades contínuas de integração e implantação contínua dos Oleodutos Azure para construir, testar e implementar aplicações de forma rápida e eficiente para o seu Azure IoT Edge.

![Diagrama - Ramos ci e CD para desenvolvimento e produção](./media/how-to-ci-cd/cd.png)

Neste artigo, aprende-se a utilizar as tarefas Azure IoT Edge incorporadas para os Oleodutos Azure para criar dois oleodutos para a sua solução IoT Edge. Existem quatro ações que podem ser usadas nas tarefas Azure IoT Edge.

* **Borda Azure IoT - Construir imagens do Módulo** pega no seu código de solução IoT Edge e constrói as imagens do recipiente.
* **Borda Azure IoT - As imagens do Módulo push** empurram as imagens do módulo para o registo do recipiente que especificou.
* **Azure IoT Edge - Generate Deployment Manifest** leva um ficheiro deployment.template.json e as variáveis, em seguida, gera o arquivo manifesto de implantação de IoT Edge final.
* **Borda Azure IoT - Implementar para dispositivos IoT Edge** ajuda a criar implementações IoT Edge para dispositivos IoT Edge únicos/múltiplos.

## <a name="prerequisites"></a>Pré-requisitos

* Um repositório Azure Repos. Se não tiver um, pode [criar um novo repo Git no seu projeto.](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav)
* Uma solução IoT Edge comprometida e empurrada para o seu repositório. Se quiser criar uma nova solução de amostra para testar este artigo, siga os passos em [Módulos De desenvolvimento e depuração em Código](how-to-vs-code-develop-module.md) de Estúdio Visual ou [Desenvolva e debugifique módulos C# no Estúdio Visual](how-to-visual-studio-develop-csharp-module.md).

   Para este artigo, tudo o que precisa é da pasta de soluções criada pelos modelos IoT Edge em Código de Estúdio Visual ou Estúdio Visual. Não precisa de construir, empurrar, implementar ou desinver este código antes de prosseguir. Vais preparar esses processos em Azure Pipelines.

   Se está a criar uma nova solução, clone o seu repositório localmente primeiro. Depois, quando criar a solução, pode optar por criá-la diretamente na pasta de repositório. Pode facilmente comprometer-se e empurrar os novos ficheiros a partir daí.

* Um registo de contentores onde pode empurrar imagens de módulos. Pode utilizar o Registo de [Contentores Azure](https://docs.microsoft.com/azure/container-registry/) ou um registo de terceiros.
* Um [hub ioT](../iot-hub/iot-hub-create-through-portal.md) ativo com dispositivos IoT Edge para testar as fases separadas de implantação de testes e de produção. Pode seguir os artigos quickstart para criar um dispositivo IoT Edge no [Linux](quickstart-linux.md) [ou](quickstart.md) Windows

Para mais informações sobre a utilização do Azure Repos, consulte [Partilhar o seu código com o Visual Studio e o Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Configurar a integração contínua

Nesta secção, cria-se um novo oleoduto de construção. Configure o gasoduto para funcionar automaticamente quando verificar quaisquer alterações na solução IoT Edge da amostra e publique registos de construção.

>[!NOTE]
>Este artigo usa o designer visual Azure DevOps. Antes de seguir os passos nesta secção, desligue a função de pré-visualização para a nova experiência de criação de gasodutoS YAML.
>
>1. Em Azure DevOps, selecione o ícone do seu perfil e selecione **as funcionalidades de pré-visualização**.
>2. Desligue a experiência de criação do **novo gasoduto YAML.**
>
>Para mais informações, consulte Criar um oleoduto de [construção.](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline)

1. Inscreva-se na sua organização Azure DevOps **(https:\//dev.azure.com/{your organization}/**) e abra o projeto que contém o seu repositório de soluções IoT Edge.

   Para este artigo, criámos um repositório chamado **IoTEdgeRepo.** Este repositório contém **IoTEdgeSolution** que tem o código para um módulo chamado **filtermodule**.

   ![Abra o seu projeto DevOps](./media/how-to-ci-cd/init-project.png)

2. Navegue para azure pipelines no seu projeto. Abra o separador **Builds** e selecione **Novo pipeline**. Ou, se já tiver oleodutos de construção, selecione o botão **Novo.** Em seguida, escolha **novo oleoduto de construção.**

    ![Criar um novo pipeline de compilação](./media/how-to-ci-cd/add-new-build.png)

3. Siga as instruções para criar o seu oleoduto.

   1. Forneça as informações de origem para o seu novo oleoduto de construção. Selecione **Azure Repos Git** como fonte e, em seguida, selecione o projeto, o repositório e o ramo onde está localizado o seu código de solução IoT Edge. Em seguida, selecione **Continuar**.

      ![Selecione a sua fonte de gasoduto](./media/how-to-ci-cd/pipeline-source.png)

   2. Selecione **trabalho vazio** em vez de um modelo.

      ![Comece com um processo vazio](./media/how-to-ci-cd/start-with-empty.png)

4. Assim que o teu oleoduto for criado, és levado para o editor do oleoduto. Na descrição do seu pipeline, escolha a piscina de agente correta com base na sua plataforma-alvo:

   * Se quiser construir os seus módulos na plataforma amd64 para contentores Linux, escolha **Ubuntu 1604**

   * Se quiser construir os seus módulos na plataforma amd64 para contentores Windows 1809, precisa de [configurar um agente auto-hospedado no Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Se quiser construir os seus módulos na plataforma arm32v7 ou arm64 para recipientes Linux, precisa de [configurar um agente auto-hospedado no Linux](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).

     ![Configure piscina de agente de construção](./media/how-to-ci-cd/configure-env.png)

5. O seu oleoduto vem reconfigurado com um trabalho chamado **Agente Job 1**. Selecione o**+** sinal de adição () para adicionar três tarefas ao trabalho: **Azure IoT Edge** duas vezes, **Copy Files** uma vez e Publicar Artefactos de **Construção** uma vez. (Pairar sobre o nome de cada tarefa para ver o botão **Adicionar.)**

   ![Adicionar tarefa Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Quando as quatro tarefas são adicionadas, o seu trabalho de Agente parece ser o seguinte exemplo:

   ![Três tarefas no oleoduto de construção](./media/how-to-ci-cd/add-tasks.png)

6. Selecione a primeira tarefa **Azure IoT Edge** para editá-lo. Esta tarefa constrói todos os módulos na solução com a plataforma-alvo que especifica.

   * **Nome**do ecrã : Aceitar o limite de **1º Aresta Azul padrão - Construir imagens de módulos**.
   * **Ação**: Aceitar as imagens padrão **do módulo Construa**.
   * **.template.json file**: Selecione a elipsis **(...**) e navegue para o ficheiro **deployment.template.json** no repositório que contém a sua solução IoT Edge.
   * **Plataforma padrão**: Selecione a plataforma adequada para os seus módulos com base no dispositivo IoT Edge alvo.
   * **Variáveis de saída**: As variáveis de saída incluem um nome de referência que pode utilizar para configurar o caminho do ficheiro onde o ficheiro deployment.json será gerado. Desloque o nome de referência para algo memorável como **edge**.

7. Selecione a segunda tarefa **Azure IoT Edge** para editá-la. Esta tarefa empurra todas as imagens do módulo para o registo do recipiente que selecionar.

   * Nome do **visor**: O nome do visor é automaticamente atualizado quando o campo de ação muda.
   * **Ação**: Utilize a lista de abandono para selecionar **imagens**do módulo Push .
   * Tipo de registo de **contentores:** Selecione o tipo de registo de contentores que utiliza para armazenar as imagens do módulo. Dependendo do tipo de registo que escolher, o formulário muda. Se escolher o Registo de **Contentores Azure,** utilize as listas de abandono para selecionar a subscrição Azure e o nome do seu registo de contentores. Se escolher o Registo genérico de **contentores,** selecione **Novo** para criar uma ligação de serviço de registo.
   * **.template.json file**: Selecione a elipsis **(...**) e navegue para o ficheiro **deployment.template.json** no repositório que contém a sua solução IoT Edge.
   * **Plataforma padrão**: Selecione a mesma plataforma que as imagens do módulo construído.

   Se tiver vários registos de contentores para alojar as imagens do seu módulo, precisa de duplicar esta tarefa, selecionar diferentes registos de contentores e utilizar os **módulos** bypass nas definições avançadas para contornar as imagens que não são para este registo específico.

8. Selecione a tarefa **Copy Files** para editá-la. Utilize esta tarefa para copiar ficheiros para o diretório de encenação de artefactos.

   * **Nome do ecrã**: Copiar Ficheiros para: Largar pasta.
   * **Conteúdo**: Coloque duas linhas `deployment.template.json` `**/module.json`nesta secção e . Estes dois tipos de ficheiros são as inputs para gerar manifesto de implantação do IoT Edge. É preciso copiar para a pasta de encenação de artefactos e publicada para o pipeline de lançamento.
   * **Pasta-alvo**: `$(Build.ArtifactStagingDirectory)`Coloque a variável . Consulte [as variáveis de construção](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) para saber sobre a descrição.

9. Selecione a tarefa **publicar construir artefactos** para editá-lo. Forneça o caminho do diretório de encenação de artefactos para a tarefa para que o caminho possa ser publicado para lançar o oleoduto.

   * **Nome do ecrã**: Publicar Artefacto: gota.
   * **Caminho para publicação**: `$(Build.ArtifactStagingDirectory)`Colocar a variável . Consulte [as variáveis de construção](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) para saber sobre a descrição.
   * Nome do **artefacto:** gota.
   * **Artefacto publicar localização**: Azure Pipelines.

10. Abra o separador **Triggers** e verifique a caixa para permitir a **integração contínua**. Certifique-se de que o ramo que contém o seu código está incluído.

    ![Ativar o gatilho de integração contínua](./media/how-to-ci-cd/configure-trigger.png)

11. Guarde o novo pipeline de construção com botão **Save.**

Este gasoduto está agora configurado para ser executado automaticamente quando empurra o novo código para o seu repo. A última tarefa, publicar os artefactos do oleoduto, desencadeia um oleoduto de libertação. Continue para a secção seguinte para construir o oleoduto de libertação.

## <a name="configure-continuous-deployment"></a>Configurar a implementação contínua

Nesta secção, cria-se um gasoduto de libertação configurado para funcionar automaticamente quando o seu oleoduto de construção deixa cair artefactos, e mostrará registos de implantação em Pipelines Azure.

Crie um novo oleoduto e adicione uma nova fase

1. No separador **Lançamentos,** escolha **+ Novo oleoduto**. Ou, se já tiver gasodutos de libertação, escolha o botão **+ Novo** e selecione + Novo gasoduto de **libertação**.  

    ![Adicionar oleoduto de libertação](./media/how-to-ci-cd/add-release-pipeline.png)

2. Quando solicitado a selecionar um modelo, opte por começar com um **trabalho Vazio**.

    ![Comece com um trabalho vazio](./media/how-to-ci-cd/start-with-empty-job.png)

3. O seu novo oleoduto de lançamento ininicia-se com uma fase, chamada **Fase 1**. Mude o nome da fase 1 para **dev** e trate-o como um ambiente de teste. Normalmente, os gasodutos de implantação contínua têm várias fases, incluindo **dev,** **encenação** e **prod**. Pode criar mais com base na sua prática de DevOps. Feche a janela de detalhes do palco assim que for rebatizada.

4. Ligue a libertação aos artefactos de construção que são publicados pelo oleoduto de construção. Clique em **adicionar** a área de artefactos.

   ![Adicionar artefactos](./media/how-to-ci-cd/add-artifacts.png)  

5. Em Adicionar uma página de **artefacto,** selecione o tipo de origem **Construir**. Em seguida, selecione o projeto e o pipeline de construção que criou. Em seguida, selecione **Adicionar**.

   ![Adicione um artefacto de compilação](./media/how-to-ci-cd/add-an-artifact.png)

6. Abra os gatilhos do artefacto e selecione o alternância para ativar o gatilho de implantação contínua. Agora, um novo lançamento será criado cada vez que uma nova construção estiver disponível.

   ![Configurar o gatilho de implantação contínua](./media/how-to-ci-cd/add-a-trigger.png)

7. A fase **de dev** é pré-configurada com um trabalho e zero tarefas. A partir do menu do pipeline, selecione **Tasks** e, em seguida, escolha a fase **de dev.**  Selecione a contagem de tarefas e tarefas para configurar as tarefas nesta fase.

    ![Configure tarefas de v](./media/how-to-ci-cd/view-stage-tasks.png)

8. Na fase **de dev,** deve ver um trabalho de **agente**padrão. Pode configurar detalhes sobre o trabalho do agente, mas a tarefa de implementação é insensível à plataforma para que possa utilizar o **Vs2017 hospedado** ou o **Ubuntu 1604 hospedado** na **piscina do Agente** (ou qualquer outro agente gerido por si mesmo).

9. Selecione o**+** sinal de mais () para adicionar duas tarefas. Procure e adicione **Azure IoT Edge** duas vezes.

    ![Adicionar tarefas para dev](./media/how-to-ci-cd/add-task-qa.png)

10. Selecione a primeira tarefa **Azure IoT Edge** e configure-a com os seguintes valores:

    * Nome do **visor**: O nome do visor é automaticamente atualizado quando o campo de ação muda.
    * **Ação**: Utilize a lista de abandono para selecionar o manifesto de **implantação Generate .** Alterar o valor de ação também atualiza o nome do ecrã de tarefas para combinar.
    * **.template.json file**: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`Coloque o caminho . O caminho é publicado a partir de pipeline de construção.
    * **Plataforma padrão**: Escolha o mesmo valor ao construir as imagens do módulo.
    * **Via de saída** `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`: Coloque o caminho . Este caminho é o ficheiro manifesto de implantação de IoT Edge final.

    Estas configurações ajudam a substituir os URLs de imagem do módulo no `deployment.template.json` ficheiro. O manifesto de **implementação da Geração** também ajuda a `deployment.template.json` substituir as variáveis pelo valor exato que definiu no ficheiro. No Código VS/VS, está a especificar `.env` o valor real num ficheiro. Nos Gasodutos Azure, define o valor no separador Delançamento Pipeline Variáveis. Mova-se para o separador Variáveis e configure o nome e o valor como seguinte.

    * **ACR_ADDRESS:** O seu endereço de registo de contentores Azure.
    * **ACR_PASSWORD**: A sua senha de registo de contentores Azure.
    * **ACR_USER**: O nome de utilizador do registo do seu contentor Azure.

    Se tiver outras variáveis no seu projeto, pode especificar o nome e o valor neste separador. O manifesto de **implantação Da Geração** só pode reconhecer as variáveis que estão no `${VARIABLE}` sabor, certifique-se de que está a usar isso nos seus `*.template.json` ficheiros.

    ![Configure variáveis para o gasoduto de libertação](./media/how-to-ci-cd/configure-variables.png)

11. Selecione a segunda tarefa **Azure IoT Edge** e configure-a com os seguintes valores:

    * Nome do **visor**: O nome do visor é automaticamente atualizado quando o campo de ação muda.
    * **Ação**: Utilize a lista de dropdown para selecionar **dispositivos De si o Edge IoT**. Alterar o valor de ação também atualiza o nome do ecrã de tarefas para combinar.
    * **Assinatura Azure**: Selecione a subscrição que contém o seu Hub IoT.
    * **Nome IoT Hub**: Selecione o seu hub IoT.
    * **Escolha um dispositivo único/múltiplo**: Escolha se pretende que o gasoduto de desbloqueio seja implantado num dispositivo ou em vários dispositivos.
      * Se se implantar num único dispositivo, introduza o ID do **dispositivo IoT Edge**.
      * Se estiver a ser implantado em vários dispositivos, especifique a condição de **alvo**do dispositivo . A condição-alvo é um filtro que combina com um conjunto de dispositivos IoT Edge no IoT Hub. Se pretender utilizar as Etiquetas do Dispositivo como condição, precisa de atualizar as etiquetas correspondentes dos dispositivos com o dispositivo IoT Hub twin. Atualize a prioridade de implementação do **IoT Edge** id e **ioT Edge** nas definições avançadas. Para obter mais informações sobre a criação de uma implementação para vários dispositivos, consulte [as implementações automáticas De Entendo IoT Edge](module-deployment-monitoring.md).
    * Expandir Definições Avançadas, selecionar ID de `$(System.TeamProject)-$(Release.EnvironmentName)` **implementação ioT Edge,** colocar a variável . Isto mapeia o nome do projeto e liberta o seu ID de implementação IoT Edge.

12. Selecione **Guardar** para guardar as alterações para o novo gasoduto de lançamento. Volte à vista do gasoduto selecionando **pipeline** do menu.

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verifique ioT Edge CI/CD com os oleodutos de construção e libertação

Para desencadear um trabalho de construção, pode empurrar um compromisso para o repositório de código de origem ou desencadeá-lo manualmente. Nesta secção, aciona manualmente o gasoduto CI/CD para testar o funcionamento. Em seguida, verifique se o destacamento é bem sucedido.

1. Navegue até ao oleoduto de construção que criou no início deste artigo.

2. Pode desencadear um trabalho de construção no seu pipeline de construção selecionando o botão **de fila** como na seguinte imagem.

    ![Gatilho manual](./media/how-to-ci-cd/manual-trigger.png)

3. Selecione o trabalho de construção para ver o seu progresso. Se o gasoduto de construção for concluído com sucesso, desencadeia uma libertação para a fase **de dev.**

    ![Construir troncos](./media/how-to-ci-cd/build-logs.png)

4. A libertação de **dev** bem sucedida cria a implementação ioT Edge para dispositivos IoT Edge alvo.

    ![Lançamento para dev](./media/how-to-ci-cd/pending-approval.png)

5. Clique na fase **de dev** para ver os registos de lançamento.

    ![Registos de versão](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>Passos seguintes

* IoT Edge DevOps melhor amostra de práticas no [Projeto Azure DevOps para IoT Edge](how-to-devops-project.md)
* Compreenda a implementação ioT Edge em [Understand IoT Edge implementações para dispositivos individuais ou em escala](module-deployment-monitoring.md)
* Caminhe pelos passos para criar, atualizar ou eliminar uma implementação em [módulos Deploy e monitor ioT Edge em escala](how-to-deploy-at-scale.md).
