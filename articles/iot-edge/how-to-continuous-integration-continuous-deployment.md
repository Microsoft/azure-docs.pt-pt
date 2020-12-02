---
title: Integração contínua e implementação contínua para dispositivos Azure IoT Edge - Azure IoT Edge
description: Confiem de integração contínua e implementação contínua utilizando YAML - Azure IoT Edge com Azure DevOps, Pipelines Azure
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 444ab8ccfe5a8441a4fd7d280e33d8e929d9387d
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435897"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Integração contínua e implantação contínua para dispositivos Azure IoT Edge

Pode facilmente adotar DevOps com as suas aplicações Azure IoT Edge com as tarefas Azure IoT Edge incorporadas em Pipelines Azure. Este artigo demonstra como pode utilizar as funcionalidades de integração contínua e implementação contínua dos Gasodutos Azure para construir, testar e implementar aplicações de forma rápida e eficiente para o seu Azure IoT Edge utilizando YAML. Em alternativa, pode [utilizar o editor clássico.](how-to-continuous-integration-continuous-deployment-classic.md)

![Diagrama - Sucursais de CI e CD para desenvolvimento e produção](./media/how-to-continuous-integration-continuous-deployment/model.png)

Neste artigo, aprende-se a utilizar as [tarefas Azure IoT Edge incorporadas](/azure/devops/pipelines/tasks/build/azure-iot-edge) para a Azure Pipelines para criar oleodutos de construção e libertação para a sua solução IoT Edge. Cada tarefa Azure IoT Edge adicionada ao seu oleoduto implementa uma das quatro ações seguintes:

 | Ação | Descrição |
 | --- | --- |
 | Construir imagens de módulos | Pega no código de solução IoT Edge e constrói as imagens do recipiente.|
 | Empurre as imagens do módulo | Empurra as imagens do módulo para o registo do contentor especificado. |
 | Gerar manifesto de implantação | Leva uma deployment.template.jsno ficheiro e nas variáveis, e depois gera o ficheiro manifesto de implantação IoT Edge final. |
 | Implementar em dispositivos IoT Edge | Cria implementações IoT Edge para um ou mais dispositivos IoT Edge. |

Salvo especificação em contrário, os procedimentos deste artigo não exploram todas as funcionalidades disponíveis através de parâmetros de tarefa. Para obter mais informações, consulte o seguinte:

* [Versão de tarefa](/azure/devops/pipelines/process/tasks?tabs=yaml#task-versions)
* **Avançado** - Se aplicável, especifique os módulos que não pretende construir.
* [Opções de Controlo](/azure/devops/pipelines/process/tasks?tabs=yaml#task-control-options)
* [Variáveis ambientais](/azure/devops/pipelines/process/variables?tabs=yaml#environment-variables)
* [Variáveis de saída](/azure/devops/pipelines/process/variables?tabs=yaml#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Pré-requisitos

* Um repositório de Azure Repos. Se não tiver um, pode [criar um novo git repo no seu projeto.](/azure/devops/repos/git/create-new-repo) Para este artigo, criámos um repositório chamado **IoTEdgeRepo.**
* Uma solução IoT Edge comprometida e empurrada para o seu repositório. Se pretender criar uma nova solução de amostra para testar este artigo, siga os passos em [Módulos De Desenvolvimento e Depurg em Módulos Visual Studio Code](how-to-vs-code-develop-module.md) ou Develop e [depurar C# em Visual Studio](./how-to-visual-studio-develop-module.md). Para este artigo, criámos uma solução no nosso repositório chamado **IoTEdgeSolution,** que tem o código para um módulo chamado **filtermodule.**

   Para este artigo, tudo o que precisa é da pasta de solução criada pelos modelos IoT Edge em Código de Estúdio Visual ou Estúdio Visual. Não precisa de construir, empurrar, implementar ou depurar este código antes de prosseguir. Vais configurar esses processos em Azure Pipelines.

   Se está a criar uma nova solução, clone primeiro o seu repositório localmente. Em seguida, quando criar a solução pode optar por criá-la diretamente na pasta do repositório. Pode facilmente comprometer-se e empurrar os novos ficheiros a partir daí.

* Um registo de contentores onde pode empurrar imagens do módulo. Pode utilizar [o Registo do Contentor Azure](../container-registry/index.yml) ou um registo de terceiros.
* Um hub Azure [IoT](../iot-hub/iot-hub-create-through-portal.md) ativo com pelo menos dois dispositivos IoT Edge para testar as fases de teste e de implantação separadas. Pode seguir os artigos de arranque rápido para criar um dispositivo IoT Edge no [Linux](quickstart-linux.md) ou [Windows](quickstart.md)

Para obter mais informações sobre a utilização do Azure Repos, consulte [Partilhar o seu código com o Visual Studio e a Azure Repos](/azure/devops/repos/git/share-your-code-in-git-vs)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Criar um oleoduto de construção para integração contínua

Nesta secção, cria-se um novo oleoduto de construção. Configura o pipeline para funcionar automaticamente quando verificar quaisquer alterações na solução IoT Edge da amostra e para publicar registos de construção.

1. Inscreva-se na sua organização Azure DevOps `https://dev.azure.com/{your organization}` e abra o projeto que contém o seu repositório de soluções IoT Edge.

   ![Abra o seu projeto DevOps](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. A partir do menu do painel esquerdo no seu projeto, selecione **Pipelines**. Selecione **Criar Pipeline** no centro da página. Ou, se já tiver construído oleodutos, selecione o botão **de pipeline Novo** no topo direito.

    ![Criar um novo oleoduto de construção utilizando o novo botão de gasoduto](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. Na página Onde está o **seu código,** selecione **Azure Repos Git `YAML`**. Se deseja utilizar o editor clássico para criar os oleodutos de construção do seu projeto, consulte o guia clássico do [editor.](how-to-continuous-integration-continuous-deployment-classic.md)

4. Selecione o repositório para o quais está a criar um oleoduto.

    ![Selecione o repositório para o seu pipeline de construção](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. Na página **Configure o seu pipeline,** selecione **Starter pipeline**. Se tiver um ficheiro YAML de Pipelines Azure pré-existente que deseje utilizar para criar este oleoduto, pode selecionar **o ficheiro YAML dos Gasodutos Azure existentes** e fornecer o ramo e o caminho do repositório ao ficheiro.

    ![Selecione o pipeline Starter ou o ficheiro YAML dos Gasodutos Azure existentes para iniciar o seu pipeline de construção](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. Na página **DE IOML do pipeline,** pode clicar no nome predefinido `azure-pipelines.yml` para renomear o ficheiro de configuração do seu pipeline.

   Selecione **o assistente de exibição** para abrir a paleta **tarefas.**

    ![Selecione assistente de show para abrir a paleta de tarefas](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. Para adicionar uma tarefa, coloque o cursor no final do YAML ou onde quiser que as instruções para a sua tarefa sejam adicionadas. Procure e selecione **Azure IoT Edge**. Preencha os parâmetros da tarefa da seguinte forma. Em seguida, **selecione Adicionar**.

   | Parâmetro | Descrição |
   | --- | --- |
   | Ação | Selecione **Construir imagens do módulo**. |
   | .template.jsno arquivo | Forneça o caminho para a **deployment.template.jsficheiro** no repositório que contém a sua solução IoT Edge. |
   | Plataforma padrão | Selecione o sistema operativo apropriado para os seus módulos com base no seu dispositivo IoT Edge direcionado. |

    ![Utilize a paleta tasks para adicionar tarefas ao seu pipeline](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > Após a adição de cada tarefa, o editor irá realçar automaticamente as linhas adicionadas. Para evitar a sobreposição acidental, desmarca as linhas e fornece um novo espaço para a sua próxima tarefa antes de adicionar tarefas adicionais.

8. Repita este processo para adicionar mais três tarefas com os seguintes parâmetros:

   * Tarefa: **Azure IoT Edge**

       | Parâmetro | Descrição |
       | --- | --- |
       | Ação | Selecione **imagens do módulo Push**. |
       | Tipo de registo de contentores | Utilize o tipo predefinido: **Registo do contentor Azure**. |
       | Subscrição do Azure | Selecione a sua subscrição. |
       | Registo de Contentores do Azure | Escolha o registo que pretende utilizar para o oleoduto. |
       | .template.jsno arquivo | Forneça o caminho para a **deployment.template.jsficheiro** no repositório que contém a sua solução IoT Edge. |
       | Plataforma padrão | Selecione o sistema operativo apropriado para os seus módulos com base no seu dispositivo IoT Edge direcionado. |

   * Tarefa: **Copiar Ficheiros**

       | Parâmetro | Descrição |
       | --- | --- |
       | Pasta de Origem | A pasta de origem para copiar. Vazia é a raiz do repo. Utilize variáveis se os ficheiros não estiverem no repo. Exemplo: `$(agent.builddirectory)`.
       | Conteúdos | Adicione duas linhas: `deployment.template.json` e `**/module.json` . |
       | Pasta-alvo | Especificar a variável `$(Build.ArtifactStagingDirectory)` . Consulte [variáveis build](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) para saber sobre a descrição. |

   * Tarefa: **Publicar Artefactos de Construção**

       | Parâmetro | Descrição |
       | --- | --- |
       | Caminho para publicar | Especificar a variável `$(Build.ArtifactStagingDirectory)` . Consulte [variáveis build](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) para saber sobre a descrição. |
       | Nome do artefacto | Especificar o nome predefinido: `drop` |
       | Local de publicação de artefactos | Utilize a localização padrão: `Azure Pipelines` |

9. **Selecione Save** from the **Save e executar** dropdown no topo direito.

10. O gatilho para integração contínua é ativado por padrão para o seu oleoduto YAML. Se desejar editar estas definições, selecione o seu pipeline e clique em **Editar** no superior direito. Selecione **Mais ações** ao lado do botão **Executar** no topo direito e vá para **Triggers**. **A integração contínua** mostra como ativado sob o nome do seu pipeline. Se desejar ver os detalhes do gatilho, verifique o **gatilho de integração contínua YAML a partir daqui.**

    ![Para rever as definições do gatilho do seu pipeline, consulte Triggers em Mais ações](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

Continue até à secção seguinte para construir o gasoduto de libertação.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Passos seguintes

* IoT Edge DevOps amostra de boas práticas em [Azure DevOps Starter para IoT Edge](how-to-devops-starter.md)
* Compreenda a implementação IoT Edge em [implementações de Borda De IoT para dispositivos individuais ou à escala](module-deployment-monitoring.md)
* Caminhe pelos passos para criar, atualizar ou eliminar uma implementação em [módulos de Implementação e Monitorização IoT Edge à escala](how-to-deploy-at-scale.md).