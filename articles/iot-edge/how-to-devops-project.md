---
title: Ci/CD pipeline com Projetos Azure DevOps - Azure IoT Edge [ Microsoft Docs
description: A Azure DevOps Projects facilita o início do Azure. Ajuda-o a lançar uma aplicação Azure IoT Edge à sua escolha em poucos passos rápidos.
author: shizn
ms.author: xshi
ms.date: 10/09/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 66401678f03ee0043345208eb32560f589829226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510316"
---
# <a name="create-a-cicd-pipeline-for-iot-edge-with-azure-devops-projects"></a>Crie um oleoduto CI/CD para IoT Edge com projetos Azure DevOps

Configure a integração contínua (CI) e a entrega contínua (CD) para a sua aplicação IoT Edge com projetos DevOps. A DevOps Projects simplifica a configuração inicial de um oleoduto de construção e lançamento em Pipelines Azure.

Se não tiver uma subscrição ativa do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

A DevOps Projects cria um oleoduto CI/CD em Azure DevOps. Você pode criar uma nova organização Azure DevOps ou usar uma organização existente. A DevOps Projects também cria recursos Azure na subscrição Azure à sua escolha.

1. Inscreva-se no [portal Microsoft Azure](https://portal.azure.com).

1. No painel esquerdo, selecione **Criar um recurso**e, em seguida, procurar **projetos DevOps**.  

1. Selecione **Criar**.

## <a name="create-a-new-application-pipeline"></a>Criar um novo pipeline de aplicações

1. Os módulos Azure IoT Edge podem ser escritos em [C#](tutorial-csharp-module.md), [Node.js,](tutorial-node-module.md) [Python,](tutorial-python-module.md) [C](tutorial-c-module.md) e [Java.](tutorial-java-module.md) Selecione o seu idioma preferido para iniciar uma nova aplicação: **.NET,** **Node.js,** **Python,** **C,** ou **Java**. Selecione **Seguinte** para continuar.

   ![Selecione idioma para criar uma nova aplicação](./media/how-to-devops-project/select-language.png)

2. Selecione **O IoT Simples** como a sua estrutura de aplicação e, em seguida, selecione **Next**.

   ![Selecione quadro ioT simples](media/how-to-devops-project/select-iot.png)

3. Selecione **IoT Edge** como o serviço Azure que implementa a sua aplicação e, em seguida, selecione **Next**.

   ![Selecione serviço IoT Edge](media/how-to-devops-project/select-iot-edge.png)

4. Crie uma nova organização gratuita azure DevOps ou escolha uma organização existente.

   1. Forneça um nome para o seu projeto.

   2. Selecione a sua organização Azure DevOps. Se não tiver uma organização existente, selecione **Configurações Adicionais** para criar uma nova.

   3. Selecione a sua subscrição do Azure.

   4. Use o nome IoT Hub gerado pelo seu nome de projeto, ou forneça o seu próprio.

   5. Aceite a localização padrão ou escolha um próximo de si.

   6. Selecione **configurações adicionais** para configurar os recursos Azure que os Projetos DevOps criam em seu nome.

   7. Selecione **Done** para terminar de criar o seu projeto.

   ![Nome e criação de aplicação](media/how-to-devops-project/select-devops.png)

Após alguns minutos, o painel de instrumentos DevOps Projects é apresentado no portal Azure. Selecione o nome do seu projeto para ver o progresso. Talvez precise de refrescar a página. Uma aplicação IoT Edge de amostra é configurada num repositório na sua organização Azure DevOps, uma construção é executada, e a sua aplicação é implementada para o dispositivo IoT Edge. Este dashboard proporciona visibilidade no seu repositório de código, no pipeline CI/CD e na sua aplicação em Azure.

   ![Ver aplicação no portal Azure](./media/how-to-devops-project/devops-portal.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Consolidar as alterações de código e executar o CI/CD

A DevOps Projects criou um repositório Git para o seu projeto em Azure Repos. Nesta secção, vê o repositório e faz alterações de código na sua aplicação.

1. Para navegar para o repo criado para o seu projeto, selecione **Repositórios** no menu do seu painel de instrumentos de projeto.  

   ![Ver repositório gerado em Azure Repos](./media/how-to-devops-project/view-repositories.png)

2. Os seguintes passos passam por usar o navegador web para fazer alterações de código. Se quiser clonar o seu repositório localmente, selecione **Clone** a partir da parte superior direita da janela. Utilize o URL fornecido para clonar o seu repositório Git em Código de Estúdio Visual ou a sua ferramenta de desenvolvimento preferida.

3. O repositório já contém código para um módulo chamado **FilterModule** com base na linguagem de aplicação que escolheu no processo de criação. Abra os **módulos/FilterModule/module.json.**

   ![Arquivo open module.json em Azure Repos](./media/how-to-devops-project/open-module-json.png)

4. Note que este ficheiro utiliza [O Azure DevOps construir variáveis](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=vsts#build-variables) no parâmetro da **versão.** Esta configuração garante que uma nova versão do módulo será criada sempre que uma nova construção for executado.

## <a name="examine-the-cicd-pipeline"></a>Examinar o gasoduto CI/CD

Nas secções anteriores, a Azure DevOps Projects configuraautomaticamente um pipeline CI/CD completo para a sua aplicação IoT Edge. Agora, explore e personalize o oleoduto conforme necessário. Use os seguintes passos para se familiarizar com os oleodutos azure DevOps.

1. Para ver os oleodutos de construção no seu projeto DevOps, selecione **Build Pipelines** no menu do seu painel de instrumentos de projeto. Este link abre um separador de navegador e o Azure DevOps constrói um pipeline para o seu novo projeto.

   ![Ver construir oleodutos em Oleodutos Azure](./media/how-to-devops-project/view-build-pipelines.png)

2. Selecione **Editar**.

    ![Editar oleoduto de construção](media/how-to-devops-project/click-edit-button.png)

3. No painel que se abre, pode examinar as tarefas que ocorrem quando o seu pipeline de construção funciona. O pipeline de construção executa várias tarefas, tais como fontes de busca do repositório Git, construindo imagens do módulo IoT Edge, empurrando os módulos IoT Edge para um registo de contentores, e publicando saídas que são usadas para implementações. Para saber mais sobre as tarefas Azure IoT Edge em Azure DevOps, consulte [o Configure Azure Pipelines para integração contínua.](how-to-ci-cd.md#configure-continuous-integration)

4. Selecione o cabeçalho **do pipeline** na parte superior do gasoduto de construção para abrir os detalhes do gasoduto. Altere o nome do pipeline de compilação para algo mais descritivo.

   ![Editar os detalhes do pipeline](./media/how-to-devops-project/edit-build-pipeline.png)

5. Selecione **Guardar & fila**e, em seguida, selecione **Guardar**.

6. Selecione **Triggers** a partir do menu de gasoduto de construção. A DevOps Projects criou automaticamente um gatilho ci, e cada compromisso com o repositório inicia uma nova construção.  Opcionalmente, pode optar por incluir ou excluir os ramos do processo de CI.

7. Selecione **Retenção**. Dependendo do seu cenário, pode especificar políticas para manter ou remover um determinado número de construções.

8. Selecione **Histórico**. O painel de história contém um rasto de auditoria de mudanças recentes na construção. A Azure Pipelines acompanha quaisquer alterações que sejam feitas no pipeline de construção, e permite comparar versões.

9. Quando terminar de explorar o oleoduto de construção, navegue até ao gasoduto de libertação correspondente. Selecione **Lançamentos** em **Pipelines**e, em seguida, selecione **Editar** para ver os detalhes do pipeline.

    ![Ver oleoduto de libertação](media/how-to-devops-project/release-pipeline.png)

10. Em **Artefactos**, selecione **Remover**. A fonte que este artefacto observa é a saída do oleoduto de construção que examinou nos passos anteriores.

11. Ao lado do ícone **Drop,** selecione o **gatilho de implantação Contínua** que se parece com um relâmpago. Este gasoduto de libertação ativou o gatilho, que executa uma implantação sempre que há um novo artefacto de construção disponível. Opcionalmente, pode desativar o gatilho de modo a que as suas implementações exijam execução manual.  

12. No menu do seu pipeline de lançamento, selecione **Tasks** e, em seguida, escolha o estágio **de dev** da lista de dropdown. A DevOps Projects criou uma fase de lançamento para si que cria um hub IoT, cria um dispositivo IoT Edge nesse hub, implanta o módulo de amostra a partir do pipeline de construção e disponibiliza uma máquina virtual para funcionar como o seu dispositivo IoT Edge. Para saber mais sobre as tarefas Azure IoT Edge para CD, consulte [o Configure Azure Pipelines para uma implantação contínua](how-to-ci-cd.md#configure-continuous-deployment).

    ![Ver tarefas de implantação contínua](media/how-to-devops-project/dev-release.png)

13. À direita, selecione **versões**. Esta vista mostra um histórico das versões.

14. Selecione o nome de um lançamento para ver mais informações sobre o mesmo.

## <a name="clean-up-resources"></a>Limpar recursos

Pode eliminar o Serviço de Aplicações Azure e outros recursos relacionados que criou quando já não precisa deles. Utilize a funcionalidade **Eliminar** no painel de instrumentos de Projetos DevOps.

## <a name="next-steps"></a>Passos seguintes

* Conheça as Tarefas para Azure IoT Edge em Azure DevOps em [integração contínua e implantação contínua para Azure IoT Edge](how-to-ci-cd.md)
* Compreenda a implementação ioT Edge em [Understand IoT Edge implementações para dispositivos individuais ou em escala](module-deployment-monitoring.md)
* Caminhe pelos passos para criar, atualizar ou eliminar uma implementação em [módulos Deploy e monitor ioT Edge em escala](how-to-deploy-monitor.md).
