---
title: Pipeline CI/CD com projetos Azure DevOps - Azure IoT Edge / Microsoft Docs
description: A azure DevOps Projects facilita a entrada em Azure. Ajuda-o a lançar uma aplicação Azure IoT Edge à sua escolha em poucos passos rápidos.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c7598aafea85e91f28233645d3d71bf85bf45425
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82130983"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Criar um pipeline CI/CD para IoT Edge com projetos Azure DevOps

Configure a integração contínua (CI) e a entrega contínua (CD) para a sua aplicação IoT Edge com Projetos DevOps. DevOps Projects simplifica a configuração inicial de um gasoduto de construção e libertação em Pipelines Azure.

Se não tiver uma subscrição ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

DevOps Projects cria um oleoduto CI/CD em Azure DevOps. Pode criar uma nova organização Azure DevOps ou utilizar uma organização existente. DevOps Projects também cria recursos Azure na subscrição Azure da sua escolha.

1. Inscreva-se no [portal Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, **selecione Criar um recurso**e, em seguida, procurar por **Projetos DevOps**.  

1. Selecione **Criar**.

## <a name="create-a-new-application-pipeline"></a>Criar um novo pipeline de aplicações

1. O seu módulo Azure IoT Edge pode ser escrito em [C#](tutorial-csharp-module.md), [Node.js](tutorial-node-module.md), [Python,](tutorial-python-module.md) [C](tutorial-c-module.md) e [Java](tutorial-java-module.md). Selecione o seu idioma preferido para iniciar uma nova aplicação: **.NET**, **Node.js**, **Python,** **C**ou **Java**. Selecione **Seguinte** para continuar.

   ![Selecione o idioma para criar uma nova aplicação](./media/how-to-devops-project/select-language.png)

2. Selecione **Simple IoT** como a sua estrutura de aplicação e, em seguida, selecione **Next**.

   ![Selecione quadro simples de IoT](media/how-to-devops-project/select-iot.png)

3. Selecione **IoT Edge** como o serviço Azure que implementa a sua aplicação e, em seguida, selecione **Next**.

   ![Selecione o serviço IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Crie uma nova organização gratuita da Azure DevOps ou escolha uma organização existente.

   1. Forneça um nome para o seu projeto.

   2. Selecione a sua organização Azure DevOps. Se não tiver uma organização existente, selecione **definições adicionais** para criar uma nova.

   3. Selecione a sua subscrição do Azure.

   4. Use o nome IoT Hub gerado pelo seu nome de projeto, ou forneça o seu próprio.

   5. Aceite a localização padrão ou escolha uma próxima.

   6. Selecione **configurações adicionais** para configurar os recursos Azure que a DevOps Projects cria em seu nome.

   7. Selecione **Feito** para terminar a criação do seu projeto.

   ![Nomee criação de aplicação](media/how-to-devops-project/select-devops.png)

Após alguns minutos, o painel de instrumentos DevOps Projects é apresentado no portal Azure. Selecione o nome do seu projeto para ver o progresso. Pode ser necessário refrescar a página. Uma aplicação IoT Edge de amostra é criada num repositório na sua organização Azure DevOps, uma construção é executada e a sua aplicação é implantada no dispositivo IoT Edge. Este painel fornece visibilidade no seu repositório de código, no gasoduto CI/CD e na sua aplicação em Azure.

   ![Ver aplicação no portal Azure](./media/how-to-devops-project/devops-portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

A DevOps Projects criou um repositório git para o seu projeto em Azure Repos. Nesta secção, você vê o repositório e faz alterações de código na sua aplicação.

1. Para navegar até ao repo criado para o seu projeto, **selecione Repositórios** no menu do painel de instrumentos do seu projeto.  

   ![Ver repositório gerado em Azure Repos](./media/how-to-devops-project/view-repositories.png)

2. Os passos seguintes passam por usando o navegador web para fazer alterações de código. Se pretender clonar o seu repositório localmente, selecione **Clone** a partir da parte superior direita da janela. Utilize o URL fornecido para clonar o seu repositório Git no Código do Estúdio Visual ou na sua ferramenta de desenvolvimento preferida.

3. O repositório já contém código para um módulo chamado **FilterModule** baseado no idioma de aplicação que escolheu no processo de criação. Abra os **módulos/FilterModule/module.jsno** ficheiro.

   ![Abra module.jsem arquivo em Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Note que este ficheiro utiliza [Azure DevOps para construir variáveis](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) no parâmetro da **versão.** Esta configuração garante que uma nova versão do módulo será criada sempre que uma nova construção for executado.

## <a name="examine-the-cicd-pipeline"></a>Examinar o gasoduto CI/CD

Nas secções anteriores, a Azure DevOps Projects configurau automaticamente um pipeline CI/CD completo para a sua aplicação IoT Edge. Agora, explore e personalize o oleoduto conforme necessário. Utilize os seguintes passos para se familiarizar com os oleodutos Azure DevOps.

1. Para ver os oleodutos de construção no seu projeto DevOps, selecione **Build Pipelines** no menu do painel de instrumentos do seu projeto. Este link abre um separador de navegador e o Azure DevOps constrói o pipeline para o seu novo projeto.

   ![Ver construir gasodutos em Gasodutos Azure](./media/how-to-devops-project/view-build-pipelines.png)

2. Selecione **Editar**.

    ![Editar o oleoduto de construção](media/how-to-devops-project/click-edit-button.png)

3. No painel que se abre, pode examinar as tarefas que ocorrem quando o seu gasoduto de construção funciona. O oleoduto de construção executa várias tarefas, tais como obter fontes do repositório Git, construir imagens de módulos IoT Edge, empurrar módulos IoT Edge para um registo de contentores, e publicar saídas que são usadas para implantações. Para saber mais sobre as tarefas Azure IoT Edge em Azure DevOps, consulte os [Pipelines Configure Azure para integração contínua](how-to-ci-cd.md#configure-continuous-integration).

4. Selecione o cabeçalho **pipeline** na parte superior do gasoduto de construção para abrir os detalhes do gasoduto. Altere o nome do pipeline de compilação para algo mais descritivo.

   ![Editar os detalhes do pipeline](./media/how-to-devops-project/edit-build-pipeline.png)

5. **Selecione Guardar & fila**e, em seguida, selecione **Guardar**.

6. Selecione **Triggers** do menu de gasoduto de construção. DevOps Projects criou automaticamente um gatilho de CI, e cada compromisso com o repositório inicia uma nova construção.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

7. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

8. Selecione **Histórico**. O painel de história contém um rasto de auditoria das recentes alterações à construção. A Azure Pipelines regista quaisquer alterações que sejam feitas ao oleoduto de construção, e permite comparar versões.

9. Quando terminar de explorar o oleoduto de construção, navegue para o oleoduto de libertação correspondente. Selecione **Versões** em **Pipelines**e, em seguida, selecione **Editar** para ver os detalhes do pipeline.

    ![Ver gasoduto de libertação](media/how-to-devops-project/release-pipeline.png)

10. Em **Artefactos**, selecione **Remover**. A fonte que este artefacto observa é a saída do gasoduto de construção que examinou nos passos anteriores.

11. Ao lado do ícone **Drop,** selecione o **gatilho de implementação contínua** que se parece com um relâmpago. Este gasoduto de libertação permitiu o gatilho, que executa uma implantação sempre que há um novo artefacto de construção disponível. Opcionalmente, pode desativar o gatilho para que as suas implementações exijam uma execução manual.  

12. No menu para o seu pipeline de lançamento, selecione **Tarefas** e escolha o fase **dev** da lista de dropdown. A DevOps Projects criou uma fase de lançamento para si que cria um hub IoT, cria um dispositivo IoT Edge naquele hub, implanta o módulo de amostra a partir do pipeline de construção e fornece uma máquina virtual para funcionar como o seu dispositivo IoT Edge. Para saber mais sobre as tarefas Azure IoT Edge para CD, consulte [os Pipelines Configure Azure para implementação contínua](how-to-ci-cd.md#configure-continuous-deployment).

    ![Ver tarefas de implementação contínua](media/how-to-devops-project/dev-release.png)

13. À direita, **selecione Ver lançamentos**. Esta vista mostra um histórico das versões.

14. Selecione o nome de um lançamento para ver mais informações sobre o mesmo.

## <a name="clean-up-resources"></a>Limpar recursos

Pode eliminar o Azure App Service e outros recursos relacionados que criou quando já não precisa deles. Utilize a funcionalidade **Eliminar** no painel de projetos de DevOps.

## <a name="next-steps"></a>Passos seguintes

* Conheça as Tarefas do Azure IoT Edge em Azure DevOps em [integração contínua e implementação contínua para Azure IoT Edge](how-to-ci-cd.md)
* Compreenda a implementação IoT Edge em [implementações de Borda De IoT para dispositivos individuais ou à escala](module-deployment-monitoring.md)
* Caminhe pelos passos para criar, atualizar ou eliminar uma implementação em [módulos de Implementação e Monitorização IoT Edge à escala](how-to-deploy-at-scale.md).
