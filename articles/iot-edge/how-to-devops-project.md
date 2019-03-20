---
title: Pipeline de CI/CD com projetos de DevOps do Azure - Azure IoT Edge | Documentos da Microsoft
description: Projetos de DevOps do Azure torna mais fácil começar a utilizar no Azure. Ele ajuda-o a iniciar uma aplicação do Azure IoT Edge à sua escolha em alguns passos rápidos.
author: shizn
manager: ''
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 02977032c5975de4098600ddbebccfcbb9b0fafd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122897"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects-preview"></a>Criar um pipeline CI/CD para o IoT Edge com projetos de DevOps do Azure (pré-visualização)

Configure integração contínua (CI) e a entrega contínua (CD) para a sua aplicação do IoT Edge com projetos de DevOps. Projetos de DevOps simplifica a configuração inicial de um pipeline de compilação e versão nos Pipelines do Azure.

Se não tiver uma subscrição ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Projetos de DevOps cria um pipeline CI/CD no Azure DevOps. Pode criar uma nova organização de DevOps do Azure ou utilizar uma organização existente. Projetos de DevOps também cria os recursos do Azure na subscrição do Azure à sua escolha.

1. Inicie sessão no [Portal do Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **criar um recurso**e, em seguida, procure **projetos de DevOps**.  

1.  Selecione **Criar**.

## <a name="create-a-new-application-pipeline"></a>Criar um novo pipeline de aplicação 

1. Os módulos do Azure IoT Edge podem ser escritos em [ C# ](tutorial-csharp-module.md), [node. js](tutorial-node-module.md), [Python](tutorial-python-module.md), [C](tutorial-c-module.md) e [Java](tutorial-java-module.md). Selecione seu idioma preferencial para iniciar uma nova aplicação: **.NET**, **node. js**, **Python**, **C**, ou **Java**. Selecione **Seguinte** para continuar.

   ![Selecione o idioma para criar uma nova aplicação](./media/how-to-devops-project/select-language.png)

2. Selecione **simples IoT (pré-visualização)** como a estrutura de aplicativo e, em seguida, selecione **próxima**.

   ![Selecione o framework de IoT simples](media/how-to-devops-project/select-iot.png)

3. Selecione **IoT Edge** como o serviço do Azure que implementa a aplicação e, em seguida, selecione **próxima**.

   ![Selecione o serviço de IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Crie uma nova organização do Azure DevOps gratuita ou escolher uma organização existente.

   1. Forneça um nome para o seu projeto. 

   2. Selecione a sua organização de DevOps do Azure. Se não tiver uma organização existente, selecione **definições adicionais** para criar um novo. 

   3. Selecione a sua subscrição do Azure.

   4. Utilize o nome do IoT Hub gerado pelo seu nome de projeto ou fornecer seu próprio.

   5. Selecione **definições adicionais** para configurar os recursos do Azure que os projetos de DevOps cria em seu nome.

   6. Selecione **feito** para concluir a criação de seu projeto. 

   ![Dê um nome e a criar aplicação](media/how-to-devops-project/select-devops.png)

Após alguns minutos, é apresentado o dashboard de projetos de DevOps no portal do Azure. Selecione o nome do projeto para ver o progresso. Terá de atualizar a página. Um exemplo de aplicação do IoT Edge é configurar num repositório na sua organização de DevOps do Azure, uma compilação é executada e a aplicação é implementada para o dispositivo do IoT Edge. Este dashboard fornece visibilidade para o repositório de código, o pipeline de CI/CD e a sua aplicação no Azure.

   ![Aplicação de vista no portal de DevOps](./media/how-to-devops-project/devops-portal.png)


## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

Projetos de DevOps criado um repositório de Git para o seu projeto no Azure repositórios. Nesta secção, veja o repositório e fazer alterações de código no aplicativo.

1. Para navegar para o repositório criado para o seu projeto, selecione **repositórios** no menu do seu dashboard do projeto.  

   ![Repositório de vista gerado nos repositórios do Azure](./media/how-to-devops-project/view-repositories.png)

2. Os seguintes passos guiá-usando o navegador da web para fazer alterações de código. Se deseja clonar o repositório localmente em vez disso, selecione **Clone** da parte superior direita da janela. Utilize o URL fornecido para clonar o repositório de Git no Visual Studio Code ou a sua ferramenta de desenvolvimento preferido. 

3. O repositório já contém o código para um módulo denominado **SampleModule** baseada na linguagem de aplicação que escolheu no processo de criação. Abra o **modules/SampleModule/module.json** ficheiro.

   ![Arquivo Module aberta nos repositórios do Azure](./media/how-to-devops-project/open-module-json.png)

4. Selecione **edite**e, em seguida, faça uma alteração ao `"version"` sob o `"tag"`. Por exemplo, pode atualizá-la para `"version": "${BUILD_BUILDID}"` utilizar [do Azure DevOps criar variáveis](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) como parte do seu tag de imagem de módulo do Azure IoT Edge.

   ![Editar a versão para aceitar as variáveis de compilação](media/how-to-devops-project/update-module-json.png)

5. Selecione **consolidar**e, em seguida, guarde as alterações.

6. No seu browser, regresse ao seu dashboard de projetos de DevOps no portal do Azure. Deverá agora ver que está em curso uma compilação. As alterações que efetuou são automaticamente criadas e implementadas por meio de um pipeline CI/CD.

    ![Ver o estado em curso](media/how-to-devops-project/ci-cd-in-progress.png)

## <a name="examine-the-cicd-pipeline"></a>Examine o pipeline de CI/CD

Nas seções anteriores, os projetos de DevOps do Azure configurado automaticamente um pipeline CI/CD completo para a sua aplicação do IoT Edge. Em seguida, testou desse pipeline de compilação, a confirmando alterações para um dos ficheiros. Agora, explore e personalizar o pipeline, conforme necessário. Siga os passos seguintes para familiarizar-se com a compilação de DevOps do Azure e lançar pipelines.

1. Para ver os pipelines de compilação no seu projeto de DevOps, selecione **criar Pipelines** no menu do seu dashboard do projeto. Esse link abre um separador do browser e o Azure DevOps criar pipeline para o novo projeto.

   ![Vista Criar pipelines em Pipelines do Azure](./media/how-to-devops-project/view-build-pipelines.png)

2. Selecione **Editar**.

    ![Editar o pipeline de compilação](media/how-to-devops-project/click-edit-button.png)

3. No painel que se abre, pode examinar as tarefas que ocorrem quando o pipeline de compilação é executada. O pipeline de compilação executa várias tarefas, tais como a obtenção de origens do repositório Git, criação de imagens do módulo de IoT Edge, enviar os módulos do IoT Edge para um registo de contentor e publicação saídas que são utilizadas para implementações. Para saber mais sobre as tarefas do Azure IoT Edge em DevOps do Azure, veja [configurar Pipelines do Azure para integração contínua](how-to-ci-cd.md#configure-continuous-integration).

4. Selecione o **Pipeline** cabeçalho na parte superior do pipeline de compilação para abrir os detalhes de pipeline. Altere o nome do seu pipeline de compilação para algo mais descritivo.

   ![Editar os detalhes de pipeline](./media/how-to-devops-project/edit-build-pipeline.png)

5. Selecione **guardar e colocar em fila**e, em seguida, selecione **guardar**.

6. No menu do seu pipeline de compilação, selecione **Acionadores** no menu. Projetos de DevOps criado automaticamente um acionador de CI e cada consolidação no repositório inicia uma nova compilação.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

7. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas de manter ou remover um determinado número de compilações.

8. Selecione **histórico**. O painel do histórico contém uma trilha de auditoria de alterações recentes à compilação. Pipelines do Azure mantém um registro de quaisquer alterações efetuadas no pipeline de compilação e permite-lhe comparar versões.

9. Quando terminar a explorar o pipeline de compilação, navegue para o pipeline de lançamento correspondente. Selecione **versões** sob **Pipelines**, em seguida, selecione **editar** para ver os detalhes de pipeline.

    ![Pipeline de lançamento do Vista](media/how-to-devops-project/release-pipeline.png)

10. Em **Artefactos**, selecione **Remover**. A origem de que assiste este artefacto é a saída do pipeline de compilação examinado nos passos anteriores. 

11. Junto aos **Drop** ícone, selecione a **acionador de implementação contínua** que se parece com um bolt extremamente. Esta versão de pipeline tiver ativado o acionador, que é executada uma implantação sempre que houver um artefacto de compilação nova disponível. Opcionalmente, pode desativar o acionador para que as suas implementações exigem execução manual.  

12. No menu do seu pipeline de lançamento, selecione **tarefas** , em seguida, escolha a **dev** estágio na lista pendente. Projetos de DevOps criado uma fase de lançamento para si que cria um hub IoT, cria um dispositivo IoT Edge nesse hub, implementa o módulo de exemplo a partir do pipeline de compilação e aprovisiona uma máquina virtual para ser executado como o seu dispositivo IoT Edge. Para saber mais sobre as tarefas do Azure IoT Edge para CD, consulte [configurar Pipelines do Azure para a implementação contínua](how-to-ci-cd.md#configure-continuous-deployment).

    ![Visualizar tarefas de implementação contínua](media/how-to-devops-project/dev-release.png)

13. À direita, selecione **ver versões**. Esta vista mostra um histórico das versões.

14. Selecione o nome de uma versão para ver mais informações sobre ele.


## <a name="clean-up-resources"></a>Limpar recursos

É possível eliminar o serviço de aplicações do Azure e outros recursos relacionados que criou quando não precisar delas. Utilize o **eliminar** funcionalidade no dashboard de projetos de DevOps.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre as tarefas do Azure IoT Edge no Azure DevOps no [integração contínua e implementação contínua para o Azure IoT Edge](how-to-ci-cd.md)
* Compreender a implementação de IoT Edge no [implementações de compreender o IoT Edge para dispositivos individuais ou em escala](module-deployment-monitoring.md)
* Siga os passos para criar, atualizar ou eliminar uma implementação no [implementar e monitorizar os módulos do IoT Edge em escala](how-to-deploy-monitor.md).
