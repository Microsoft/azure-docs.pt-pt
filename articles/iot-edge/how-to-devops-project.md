---
title: Pipeline de CI/CD com projetos de DevOps do Azure - Azure IoT Edge | Documentos da Microsoft
description: Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Ele ajuda-o a iniciar uma aplicação do Azure IoT Edge à sua escolha em alguns passos rápidos.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ccf6ea567143180daa848566d1e7e1420c181c5f
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457373"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Criar um pipeline de CI/CD para IoT Edge com Azure DevOps Projects

Configure integração contínua (CI) e a entrega contínua (CD) para a sua aplicação do IoT Edge com projetos de DevOps. Projetos de DevOps simplifica a configuração inicial de um pipeline de compilação e versão nos Pipelines do Azure.

Se não tiver uma subscrição ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Projetos de DevOps cria um pipeline CI/CD no Azure DevOps. Você pode criar uma nova organização de DevOps do Azure ou usar uma organização existente. Projetos de DevOps também cria os recursos do Azure na subscrição do Azure à sua escolha.

1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**e procure **DevOps Projects**.  

1.  Selecione **Criar**.

## <a name="create-a-new-application-pipeline"></a>Criar um novo pipeline de aplicativo 

1. Seus módulos de Azure IOT Edge [C#](tutorial-csharp-module.md)podem ser gravados, [node. js](tutorial-node-module.md), [python](tutorial-python-module.md), [C](tutorial-c-module.md) e [Java](tutorial-java-module.md). Selecione seu idioma preferido para iniciar um novo aplicativo: **.net**, **node. js**, **python**, **C**ou **Java**. Selecione **Seguinte** para continuar.

   ![Selecione o idioma para criar uma nova aplicação](./media/how-to-devops-project/select-language.png)

2. Selecione **IOT simples** como sua estrutura de aplicativo e, em seguida, selecione **Avançar**.

   ![Selecione o framework de IoT simples](media/how-to-devops-project/select-iot.png)

3. Selecione **IOT Edge** como o serviço do Azure que implanta seu aplicativo e, em seguida, selecione **Avançar**.

   ![Selecione o serviço de IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Crie uma nova organização do Azure DevOps gratuita ou escolher uma organização existente.

   1. Forneça um nome para seu projeto. 

   2. Selecione sua organização do DevOps do Azure. Se você não tiver uma organização existente, selecione **configurações adicionais** para criar uma nova. 

   3. Selecione a sua subscrição do Azure.

   4. Use o nome do Hub IoT gerado pelo nome do projeto ou forneça o seu próprio.

   5. Aceite o local padrão ou escolha um próximo a você. 

   5. Selecione **configurações adicionais** para configurar os recursos do Azure que DevOps Projects cria em seu nome.

   6. Selecione **concluído** para concluir a criação do projeto. 

   ![Dê um nome e a criar aplicação](media/how-to-devops-project/select-devops.png)

Após alguns minutos, é apresentado o dashboard de projetos de DevOps no portal do Azure. Selecione o nome do projeto para ver o progresso. Talvez seja necessário atualizar a página. Um exemplo de aplicação do IoT Edge é configurar num repositório na sua organização de DevOps do Azure, uma compilação é executada e a aplicação é implementada para o dispositivo do IoT Edge. Este dashboard fornece visibilidade para o repositório de código, o pipeline de CI/CD e a sua aplicação no Azure.

   ![Exibir aplicativo no portal do Azure](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

DevOps Projects criou um repositório Git para seu projeto no Azure Repos. Nesta seção, você exibirá o repositório e fará alterações de código em seu aplicativo.

1. Para navegar até o repositório criado para seu projeto, selecione **repositórios** no menu do painel do projeto.  

   ![Exibir repositório gerado no Azure Repos](./media/how-to-devops-project/view-repositories.png)

2. As etapas a seguir percorrem o uso do navegador da Web para fazer alterações de código. Se você quiser clonar seu repositório localmente, selecione **clonar** no canto superior direito da janela. Use a URL fornecida para clonar seu repositório git no Visual Studio Code ou sua ferramenta de desenvolvimento preferida. 

3. O repositório já contém código para um módulo chamado **FilterModule** com base no idioma do aplicativo que você escolheu no processo de criação. Abra o arquivo **modules/FilterModule/Module. JSON** .

   ![Abra o arquivo module. JSON no Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Observe que esse arquivo usa [variáveis de compilação DevOps do Azure](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) no parâmetro **version** . Essa configuração garante que uma nova versão do módulo será criada toda vez que uma nova compilação for executada. 


## <a name="examine-the-cicd-pipeline"></a>Examine o pipeline de CI/CD

Nas seções anteriores, Azure DevOps Projects configurou automaticamente um pipeline de CI/CD completo para seu aplicativo IoT Edge. Agora, explore e personalize o pipeline conforme necessário. Use as etapas a seguir para se familiarizar com os pipelines de versão e de compilação DevOps do Azure.

1. Para exibir os pipelines de compilação em seu projeto do DevOps, selecione **criar pipelines** no menu do painel do projeto. Esse link abre um separador do browser e o Azure DevOps criar pipeline para o novo projeto.

   ![Exibir pipelines de Build no Azure Pipelines](./media/how-to-devops-project/view-build-pipelines.png)

2. Selecione **Editar**.

    ![Editar o pipeline de compilação](media/how-to-devops-project/click-edit-button.png)

3. No painel que é aberto, você pode examinar as tarefas que ocorrem quando o pipeline de compilação é executado. O pipeline de compilação executa várias tarefas, como buscar fontes do repositório git, criar IoT Edge imagens de módulo, enviar por push IoT Edge módulos para um registro de contêiner e publicar saídas que são usadas para implantações. Para saber mais sobre Azure IoT Edge tarefas no Azure DevOps, consulte [configurar Azure pipelines para integração contínua](how-to-ci-cd.md#configure-continuous-integration).

4. Selecione o cabeçalho do **pipeline** na parte superior do pipeline de compilação para abrir os detalhes do pipeline. Altere o nome do pipeline de compilação para algo mais descritivo.

   ![Editar os detalhes do pipeline](./media/how-to-devops-project/edit-build-pipeline.png)

5. Selecione **salvar & fila**e, em seguida, selecione **salvar**.

6. Selecione **gatilhos** no menu pipeline de compilação. Projetos de DevOps criado automaticamente um acionador de CI e cada consolidação no repositório inicia uma nova compilação.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

7. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

8. Selecione **histórico**. O painel histórico contém uma trilha de auditoria de alterações recentes na compilação. Pipelines do Azure mantém um registro de quaisquer alterações efetuadas no pipeline de compilação e permite-lhe comparar versões.

9. Quando você terminar de explorar o pipeline de compilação, navegue até o pipeline de liberação correspondente. Selecione **versões** em **pipelines**e, em seguida, selecione **Editar** para exibir os detalhes do pipeline.

    ![Pipeline de lançamento do Vista](media/how-to-devops-project/release-pipeline.png)

10. Em **Artefactos**, selecione **Remover**. A origem que esse artefato inspeciona é a saída do pipeline de compilação que você examinou nas etapas anteriores. 

11. Ao lado do ícone de **soltar** , selecione o **gatilho de implantação contínua** que se parece com um raio. Este pipeline de lançamento habilitou o gatilho, que executa uma implantação sempre que há um novo artefato de compilação disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual.  

12. No menu do seu pipeline de lançamento, selecione **tarefas** e, em seguida, escolha o estágio de **desenvolvimento** na lista suspensa. DevOps Projects criou um estágio de lançamento para você que cria um hub IoT, cria um dispositivo IoT Edge nesse Hub, implanta o módulo de exemplo do pipeline de compilação e provisiona uma máquina virtual para ser executada como seu dispositivo IoT Edge. Para saber mais sobre Azure IoT Edge tarefas para o CD, consulte [configurar Azure pipelines para implantação contínua](how-to-ci-cd.md#configure-continuous-deployment).

    ![Visualizar tarefas de implementação contínua](media/how-to-devops-project/dev-release.png)

13. À direita, selecione **Exibir versões**. Esta vista mostra um histórico das versões.

14. Selecione o nome de uma versão para exibir mais informações sobre ela.


## <a name="clean-up-resources"></a>Limpar recursos

É possível eliminar o serviço de aplicações do Azure e outros recursos relacionados que criou quando não precisar delas. Use a funcionalidade **excluir** no painel DevOps Projects.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as tarefas para Azure IoT Edge no Azure DevOps em [integração contínua e implantação contínua para Azure IOT Edge](how-to-ci-cd.md)
* Entenda a implantação do IoT Edge em [entender implantações IOT Edge para dispositivos únicos ou em escala](module-deployment-monitoring.md)
* Percorra as etapas para criar, atualizar ou excluir uma implantação em [implantar e monitorar módulos IOT Edge em escala](how-to-deploy-monitor.md).
