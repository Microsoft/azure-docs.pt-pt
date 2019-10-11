---
title: Pipeline de CI/CD com Azure DevOps Projects-Azure IoT Edge | Microsoft Docs
description: Azure DevOps Projects facilita a introdução ao Azure. Ele ajuda a iniciar um aplicativo Azure IoT Edge de sua escolha em algumas etapas rápidas.
author: shizn
manager: ''
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: daa4bc7b1584dc2159d4128fa4b44056df347ecb
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72253068"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Criar um pipeline de CI/CD para IoT Edge com Azure DevOps Projects

Configure a CI (integração contínua) e o CD (entrega contínua) para seu aplicativo IoT Edge com DevOps Projects. DevOps Projects simplifica a configuração inicial de um pipeline de Build e versão no Azure Pipelines.

Se não tiver uma subscrição ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

DevOps Projects cria um pipeline de CI/CD no Azure DevOps. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. DevOps Projects também cria recursos do Azure na assinatura do Azure de sua escolha.

1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**e procure **DevOps Projects**.  

1.  Selecione **Criar**.

## <a name="create-a-new-application-pipeline"></a>Criar um novo pipeline de aplicativo 

1. Seus módulos de Azure IOT Edge [C#](tutorial-csharp-module.md)podem ser gravados, [node. js](tutorial-node-module.md), [python](tutorial-python-module.md), [C](tutorial-c-module.md) e [Java](tutorial-java-module.md). Selecione seu idioma preferido para iniciar um novo aplicativo: **.net**, **node. js**, **python**, **C**ou **Java**. Selecione **Seguinte** para continuar.

   ![Selecione o idioma para criar um novo aplicativo](./media/how-to-devops-project/select-language.png)

2. Selecione **IOT simples** como sua estrutura de aplicativo e, em seguida, selecione **Avançar**.

   ![Selecione a estrutura IoT simples](media/how-to-devops-project/select-iot.png)

3. Selecione **IOT Edge** como o serviço do Azure que implanta seu aplicativo e, em seguida, selecione **Avançar**.

   ![Selecionar serviço IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Crie uma nova organização gratuita do Azure DevOps ou escolha uma organização existente.

   1. Forneça um nome para seu projeto. 

   2. Selecione sua organização do DevOps do Azure. Se você não tiver uma organização existente, selecione **configurações adicionais** para criar uma nova. 

   3. Selecione a sua subscrição do Azure.

   4. Use o nome do Hub IoT gerado pelo nome do projeto ou forneça o seu próprio.

   5. Aceite o local padrão ou escolha um próximo a você. 

   5. Selecione **configurações adicionais** para configurar os recursos do Azure que DevOps Projects cria em seu nome.

   6. Selecione **concluído** para concluir a criação do projeto. 

   ![Nome e criar aplicativo](media/how-to-devops-project/select-devops.png)

Depois de alguns minutos, o painel de DevOps Projects é exibido na portal do Azure. Selecione o nome do projeto para ver o progresso. Talvez seja necessário atualizar a página. Um aplicativo de IoT Edge de exemplo é configurado em um repositório em sua organização DevOps do Azure, uma compilação é executada e seu aplicativo é implantado no dispositivo IoT Edge. Esse painel fornece visibilidade do seu repositório de códigos, do pipeline de CI/CD e de seu aplicativo no Azure.

   ![Exibir aplicativo no portal do Azure](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

DevOps Projects criou um repositório Git para seu projeto no Azure Repos. Nesta seção, você exibirá o repositório e fará alterações de código em seu aplicativo.

1. Para navegar até o repositório criado para seu projeto, selecione **repositórios** no menu do painel do projeto.  

   ![Exibir repositório gerado no Azure Repos](./media/how-to-devops-project/view-repositories.png)

2. As etapas a seguir percorrem o uso do navegador da Web para fazer alterações de código. Se você quiser clonar seu repositório localmente, selecione **clonar** no canto superior direito da janela. Use a URL fornecida para clonar seu repositório git no Visual Studio Code ou sua ferramenta de desenvolvimento preferida. 

3. O repositório já contém código para um módulo chamado **FilterModule** com base no idioma do aplicativo que você escolheu no processo de criação. Abra o arquivo **modules/FilterModule/Module. JSON** .

   ![Abra o arquivo module. JSON no Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Observe que esse arquivo usa [variáveis de compilação DevOps do Azure](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) no parâmetro **version** . Essa configuração garante que uma nova versão do módulo será criada toda vez que uma nova compilação for executada. 


## <a name="examine-the-cicd-pipeline"></a>Examinar o pipeline de CI/CD

Nas seções anteriores, Azure DevOps Projects configurou automaticamente um pipeline de CI/CD completo para seu aplicativo IoT Edge. Agora, explore e personalize o pipeline conforme necessário. Use as etapas a seguir para se familiarizar com os pipelines de versão e de compilação DevOps do Azure.

1. Para exibir os pipelines de compilação em seu projeto do DevOps, selecione **criar pipelines** no menu do painel do projeto. Esse link abre uma guia do navegador e o pipeline do Azure DevOps Build para seu novo projeto.

   ![Exibir pipelines de Build no Azure Pipelines](./media/how-to-devops-project/view-build-pipelines.png)

2. Selecione **Editar**.

    ![Editar pipeline de compilação](media/how-to-devops-project/click-edit-button.png)

3. No painel que é aberto, você pode examinar as tarefas que ocorrem quando o pipeline de compilação é executado. O pipeline de compilação executa várias tarefas, como buscar fontes do repositório git, criar IoT Edge imagens de módulo, enviar por push IoT Edge módulos para um registro de contêiner e publicar saídas que são usadas para implantações. Para saber mais sobre Azure IoT Edge tarefas no Azure DevOps, consulte [configurar Azure pipelines para integração contínua](how-to-ci-cd.md#configure-continuous-integration).

4. Selecione o cabeçalho do **pipeline** na parte superior do pipeline de compilação para abrir os detalhes do pipeline. Altere o nome do pipeline de compilação para algo mais descritivo.

   ![Editar os detalhes do pipeline](./media/how-to-devops-project/edit-build-pipeline.png)

5. Selecione **salvar & fila**e, em seguida, selecione **salvar**.

6. Selecione **gatilhos** no menu pipeline de compilação. DevOps Projects criou automaticamente um gatilho de CI e todas as confirmações para o repositório iniciam uma nova compilação.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

7. Selecione **Retenção**. Dependendo do seu cenário, você pode especificar políticas para manter ou remover um determinado número de compilações.

8. Selecione **histórico**. O painel histórico contém uma trilha de auditoria de alterações recentes na compilação. Azure Pipelines controla as alterações feitas no pipeline de compilação e permite que você compare as versões.

9. Quando você terminar de explorar o pipeline de compilação, navegue até o pipeline de liberação correspondente. Selecione **versões** em **pipelines**e, em seguida, selecione **Editar** para exibir os detalhes do pipeline.

    ![Exibir pipeline de liberação](media/how-to-devops-project/release-pipeline.png)

10. Em **Artefactos**, selecione **Remover**. A origem que esse artefato inspeciona é a saída do pipeline de compilação que você examinou nas etapas anteriores. 

11. Ao lado do ícone de **soltar** , selecione o **gatilho de implantação contínua** que se parece com um raio. Este pipeline de lançamento habilitou o gatilho, que executa uma implantação sempre que há um novo artefato de compilação disponível. Opcionalmente, você pode desabilitar o gatilho para que suas implantações exijam execução manual.  

12. No menu do seu pipeline de lançamento, selecione **tarefas** e, em seguida, escolha o estágio de **desenvolvimento** na lista suspensa. DevOps Projects criou um estágio de lançamento para você que cria um hub IoT, cria um dispositivo IoT Edge nesse Hub, implanta o módulo de exemplo do pipeline de compilação e provisiona uma máquina virtual para ser executada como seu dispositivo IoT Edge. Para saber mais sobre Azure IoT Edge tarefas para o CD, consulte [configurar Azure pipelines para implantação contínua](how-to-ci-cd.md#configure-continuous-deployment).

    ![Exibir tarefas de implantação contínua](media/how-to-devops-project/dev-release.png)

13. À direita, selecione **Exibir versões**. Esta vista mostra um histórico das versões.

14. Selecione o nome de uma versão para exibir mais informações sobre ela.


## <a name="clean-up-resources"></a>Limpar recursos

Você pode excluir Azure App serviço e outros recursos relacionados que você criou quando não precisar mais deles. Use a funcionalidade **excluir** no painel DevOps Projects.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as tarefas para Azure IoT Edge no Azure DevOps em [integração contínua e implantação contínua para Azure IOT Edge](how-to-ci-cd.md)
* Entenda a implantação do IoT Edge em [entender implantações IOT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md)
* Percorra as etapas para criar, atualizar ou excluir uma implantação em [implantar e monitorar módulos IOT Edge em escala](how-to-deploy-monitor.md).
