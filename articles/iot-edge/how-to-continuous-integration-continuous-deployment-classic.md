---
title: Integração contínua e implementação contínua para dispositivos Azure IoT Edge (editor clássico) - Azure IoT Edge
description: Confiem de integração contínua e implementação contínua utilizando o editor clássico - Azure IoT Edge com Azure DevOps, Azure Pipelines
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f7c28ecbaa58731c528a9ecb5f869eba2bc0c99f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484426"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Integração contínua e implementação contínua para dispositivos Azure IoT Edge (editor clássico)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Pode facilmente adotar DevOps com as suas aplicações Azure IoT Edge com as tarefas Azure IoT Edge incorporadas em Pipelines Azure. Este artigo demonstra como pode utilizar as funcionalidades de integração contínua e implementação contínua dos Oleodutos Azure para construir, testar e implementar aplicações de forma rápida e eficiente para o seu Azure IoT Edge utilizando o editor clássico. Em alternativa, pode [utilizar YAML](how-to-continuous-integration-continuous-deployment.md).

![Diagrama - Sucursais de CI e CD para desenvolvimento e produção](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

Neste artigo, aprende-se a utilizar as [tarefas Azure IoT Edge incorporadas](/azure/devops/pipelines/tasks/build/azure-iot-edge) para a Azure Pipelines para criar oleodutos de construção e libertação para a sua solução IoT Edge. Cada tarefa Azure IoT Edge adicionada ao seu oleoduto implementa uma das quatro ações seguintes:

 | Ação | Descrição |
 | --- | --- |
 | Construir imagens de módulos | Pega no código de solução IoT Edge e constrói as imagens do recipiente.|
 | Empurre as imagens do módulo | Empurra as imagens do módulo para o registo do contentor especificado. |
 | Gerar manifesto de implantação | Leva uma deployment.template.jsno ficheiro e nas variáveis, e depois gera o ficheiro manifesto de implantação IoT Edge final. |
 | Implementar em dispositivos IoT Edge | Cria implementações IoT Edge para um ou mais dispositivos IoT Edge. |

Salvo especificação em contrário, os procedimentos deste artigo não exploram todas as funcionalidades disponíveis através de parâmetros de tarefa. Para obter mais informações, consulte o seguinte:

* [Versão de tarefa](/azure/devops/pipelines/process/tasks?tabs=classic#task-versions)
* **Avançado** - Se aplicável, especifique os módulos que não pretende construir.
* [Opções de Controlo](/azure/devops/pipelines/process/tasks?tabs=classic#task-control-options)
* [Variáveis ambientais](/azure/devops/pipelines/process/variables?tabs=classic#environment-variables)
* [Variáveis de saída](/azure/devops/pipelines/process/variables?tabs=classic#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Pré-requisitos

* Um repositório de Azure Repos. Se não tiver um, pode [criar um novo git repo no seu projeto.](/azure/devops/repos/git/create-new-repo) Para este artigo, criámos um repositório chamado **IoTEdgeRepo.**
* Uma solução IoT Edge comprometida e empurrada para o seu repositório. Se pretender criar uma nova solução de amostra para testar este artigo, siga os passos em [Módulos De Desenvolvimento e Depurg em Módulos Visual Studio Code](how-to-vs-code-develop-module.md) ou Develop e [depurar C# em Visual Studio](./how-to-visual-studio-develop-module.md). Para este artigo, criámos uma solução no nosso repositório chamado **IoTEdgeSolution,** que tem o código para um módulo chamado **filtermodule.**

   Para este artigo, tudo o que precisa é da pasta de solução criada pelos modelos IoT Edge em Código de Estúdio Visual ou Estúdio Visual. Não precisa de construir, empurrar, implementar ou depurar este código antes de prosseguir. Vais configurar esses processos em Azure Pipelines.

   Se está a criar uma nova solução, clone primeiro o seu repositório localmente. Em seguida, quando criar a solução pode optar por criá-la diretamente na pasta do repositório. Pode facilmente comprometer-se e empurrar os novos ficheiros a partir daí.

* Um registo de contentores onde pode empurrar imagens do módulo. Pode utilizar [o Registo do Contentor Azure](../container-registry/index.yml) ou um registo de terceiros.
* Um hub Azure [IoT](../iot-hub/iot-hub-create-through-portal.md) ativo com pelo menos dois dispositivos IoT Edge para testar as fases de teste e de implantação separadas. Pode seguir os artigos de arranque rápido para criar um dispositivo IoT Edge no [Linux](quickstart-linux.md) ou [Windows](quickstart.md)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Criar um oleoduto de construção para integração contínua

Nesta secção, cria-se um novo oleoduto de construção. Configura o pipeline para funcionar automaticamente quando verificar quaisquer alterações na solução IoT Edge da amostra e para publicar registos de construção.

1. Inscreva-se na sua organização Azure DevOps `https://dev.azure.com/{your organization}` e abra o projeto que contém o seu repositório de soluções IoT Edge.

    ![Abra o seu projeto DevOps](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. A partir do menu do painel esquerdo no seu projeto, selecione **Pipelines**. Selecione **Criar Pipeline** no centro da página. Ou, se já tiver construído oleodutos, selecione o botão **de pipeline Novo** no topo direito.

    ![Criar um novo pipeline de compilação](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. Na parte inferior da página Onde está o **seu código,** selecione **Use o editor clássico**. Se desejar utilizar o YAML para criar os oleodutos de construção do seu projeto, consulte o [guia YAML](how-to-continuous-integration-continuous-deployment.md).

    ![Selecione Use o editor clássico](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Siga as instruções para criar o seu oleoduto.

   1. Forneça a informação de origem para o seu novo oleoduto de construção. Selecione **Azure Repos Git** como fonte, em seguida, selecione o projeto, repositório e ramo onde o seu código de solução IoT Edge está localizado. Em seguida, **selecione Continue**.

      ![Selecione a sua fonte de pipeline](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Selecione **Trabalho vazio** em vez de um modelo.

      ![Comece com um trabalho vazio para o seu oleoduto de construção](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. Uma vez criado o seu oleoduto, é levado para o editor do oleoduto. Aqui, pode alterar o nome do pipeline, piscina de agente e especificação do agente.

   Você pode selecionar uma piscina hospedada pela Microsoft, ou uma piscina auto-hospedada que você gere.

   Na descrição do seu pipeline, escolha a especificação do agente correta com base na sua plataforma-alvo:

   * Se quiser construir os seus módulos na plataforma amd64 para recipientes Linux, escolha **ubuntu-16.04**

   * Se quiser construir os seus módulos na plataforma amd64 para recipientes Windows 1809, tem de [configurar um agente auto-hospedado no Windows](/azure/devops/pipelines/agents/v2-windows).

   * Se quiser construir os seus módulos na plataforma arm32v7 ou arm64 para recipientes Linux, tem de [configurar um agente auto-hospedado no Linux](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent).

    ![Configure a especificação do agente de construção](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. O seu oleoduto vem pré-configurado com um trabalho chamado **Agente trabalho 1**. Selecione o sinal de mais ( **+** ) para adicionar quatro tarefas ao trabalho: **Azure IoT Edge** duas vezes, Copy **Files** uma vez e Publicar **Build Artifacts** uma vez. Procure por cada tarefa e paire sobre o nome da tarefa para ver o botão **Adicionar.**

   ![Adicione a tarefa Azure IoT Edge](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Quando todas as quatro tarefas são adicionadas, o seu trabalho de Agente parece ser o seguinte exemplo:

   ![Quatro tarefas no oleoduto de construção](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Selecione a primeira tarefa **Azure IoT Edge** para editá-la. Esta tarefa constrói todos os módulos na solução com a plataforma-alvo que especifica. Editar a tarefa com os seguintes valores:

    | Parâmetro | Descrição |
    | --- | --- |
    | Nome a apresentar | O nome de visualização é atualizado automaticamente quando o campo Ação muda. |
    | Ação | Selecione **Construir imagens do módulo**. |
    | .template.jsno arquivo | Selecione a elipse **(...**) e navegue para a **deployment.template.jsficheiro no** repositório que contém a sua solução IoT Edge. |
    | Plataforma padrão | Selecione o sistema operativo apropriado para os seus módulos com base no seu dispositivo IoT Edge direcionado. |
    | Variáveis de saída | Forneça um nome de referência para associar-se ao caminho do ficheiro onde o seu deployment.jsno ficheiro gera, tal como **a borda**. |

   Estas configurações utilizam o repositório de imagens e a etiqueta que são definidas no `module.json` ficheiro para nomear e marcar a imagem do módulo. **Construir imagens de módulos** também ajuda a substituir as variáveis pelo valor exato que define no `module.json` ficheiro. No Visual Studio ou Visual Studio Code, está a especificar o valor real num `.env` ficheiro. Em Pipelines Azure, define o valor no **separador Pipeline Variables.** Selecione o separador **Variáveis** no menu do editor do pipeline e configuure o nome e o valor como seguinte:

    * **ACR_ADDRESS**: O valor do servidor de registo do registo do seu registo de registo de **dados.** Pode recuperar o servidor 'Iniciar sessão' a partir da página de visão geral do seu registo de contentores no portal Azure.

    Se tiver outras variáveis no seu projeto, pode especificar  o nome e o valor neste separador. `${VARIABLE}` Certifique-se de que utiliza este formato nos seus `**/module.json` ficheiros.

8. Selecione a segunda tarefa **Azure IoT Edge** para editá-la. Esta tarefa empurra todas as imagens do módulo para o registo do contentor que seleciona.

    | Parâmetro | Descrição |
    | --- | --- |
    | Nome a apresentar | O nome de visualização é atualizado automaticamente quando o campo Ação muda. |
    | Ação | Selecione **imagens do módulo Push**. |
    | Tipo de registo de contentores | Utilize o tipo predefinido: `Azure Container Registry` . |
    | Subscrição do Azure | Escolha a sua subscrição. |
    | Registo de Contentores do Azure | Selecione o tipo de registo de contentores que utiliza para armazenar as imagens do módulo. Dependendo do tipo de registo que escolher, o formulário muda. Se escolher **o Registo do Contentor Azure,** utilize as listas de dropdown para selecionar a subscrição Azure e o nome do seu registo de contentores. Se escolher **o Registo Genérico de Contentores,** selecione **Novo** para criar uma ligação de serviço de registo. |
    | .template.jsno arquivo | Selecione a elipse **(...**) e navegue para a **deployment.template.jsficheiro no** repositório que contém a sua solução IoT Edge. |
    | Plataforma padrão | Selecione o sistema operativo apropriado para os seus módulos com base no seu dispositivo IoT Edge direcionado. |
    | Adicione credencial de registo ao manifesto de implantação | Especifique a credencial de registo para empurrar imagens de estivador para o manifesto de implantação. |

   Se tiver vários registos de contentores para hospedar as imagens do módulo, tem de duplicar esta tarefa, selecionar diferentes registos de contentores e utilizar **módulos bypass** nas definições **Avançadas** para contornar as imagens que não são para este registo específico.

9. Selecione a tarefa **Copy Files** para editá-la. Utilize esta tarefa para copiar ficheiros para o diretório de encenação de artefactos.

    | Parâmetro | Descrição |
    | --- | --- |
    | Nome a apresentar | Use o nome padrão ou personalize |
    | Pasta de origem | A pasta com os ficheiros a copiar. |
    | Conteúdos | Adicione duas linhas: `deployment.template.json` e `**/module.json` . Estes dois ficheiros servem como entradas para gerar o manifesto de implantação IoT Edge. |
    | Pasta-alvo | Especificar a variável `$(Build.ArtifactStagingDirectory)` . Consulte [variáveis build](/azure/devops/pipelines/build/variables#build-variables) para saber sobre a descrição. |

10. Selecione a tarefa **de Publicar Artefactos de Construção** para editá-lo. Forneça o caminho do diretório de encenação de artefactos para a tarefa para que o caminho possa ser publicado para libertar o pipeline.

    | Parâmetro | Descrição |
    | --- | --- |
    | Nome a apresentar | Utilize o nome predefinido ou personalize.. |
    | Caminho para publicar | Especificar a variável `$(Build.ArtifactStagingDirectory)` . Ver [Construir variáveis](/azure/devops/pipelines/build/variables#build-variables) para saber mais. |
    | Nome do artefacto | Use o nome predefinido: **drop** |
    | Local de publicação de artefactos | Utilize a localização padrão: **Gasodutos Azure** |

11. Abra o **separador Gatilhos** e verifique a caixa para Ativar a **integração contínua**. Certifique-se de que o ramo que contém o seu código está incluído.

    ![Ligue o gatilho de integração contínua](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. **Selecione Save** from the Save & de espera da **fila.**

Este gasoduto está agora configurado para funcionar automaticamente quando introduz um novo código para o seu repo. A última tarefa, publicar os artefactos do gasoduto, desencadeia um oleoduto de libertação. Continue até à secção seguinte para construir o gasoduto de libertação.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Se desejar utilizar implementações em **camadas** no seu pipeline, as implementações em camadas ainda não são suportadas nas tarefas Azure IoT Edge em Azure DevOps.
>
>No entanto, pode utilizar uma [tarefa Azure CLI em Azure DevOps](/azure/devops/pipelines/tasks/deploy/azure-cli) para criar a sua implementação como uma implementação em camadas. Para o valor **do Script Inline,** pode utilizar o [comando de criação de borda az iot](/cli/azure/iot/edge/deployment):
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Passos seguintes

* IoT Edge DevOps amostra de boas práticas em [Azure DevOps Starter para IoT Edge](how-to-devops-starter.md)
* Compreenda a implementação IoT Edge em [implementações de Borda De IoT para dispositivos individuais ou à escala](module-deployment-monitoring.md)
* Caminhe pelos passos para criar, atualizar ou eliminar uma implementação em [módulos de Implementação e Monitorização IoT Edge à escala](how-to-deploy-at-scale.md).