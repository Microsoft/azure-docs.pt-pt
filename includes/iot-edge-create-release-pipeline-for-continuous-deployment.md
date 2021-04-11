---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 8009d98ddbfa778cf5f357248ecd943b810e06e3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104803352"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>Criar um gasoduto de libertação para implantação contínua

Nesta secção, cria-se um gasoduto de desbloqueio configurado para funcionar automaticamente quando o seu gasoduto de construção deixa cair artefactos, e mostrará registos de implantação em Pipelines Azure.

Crie um novo oleoduto e adicione uma nova etapa:

1. No separador **Versões** em **Pipelines,** escolha **+ novo gasoduto**. Ou, se já tiver os oleodutos de desbloqueio, escolha o botão **+ Novo** e selecione **+ Novo pipeline de lançamento**.  

    ![Adicione um gasoduto de desbloqueio utilizando o botão + novo do gasoduto](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. Quando solicitado para selecionar um modelo, opte por começar com um **trabalho Vazio**.

    ![Comece com um trabalho vazio para o seu oleoduto de libertação](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. O seu novo oleoduto de lançamento iniciaisiza-se com um estágio, chamado **Fase 1**. Mude o nome da Fase 1 para **dev** e trate-o como um oleoduto de implantação contínua para o seu ambiente de desenvolvimento. Normalmente, os gasodutos de implantação contínua têm várias fases, incluindo **dev,** **encenação** e **prod**. Pode utilizar nomes diferentes e criar mais com base na sua prática de DevOps. Feche a janela dos detalhes do palco assim que for renomeada.

   Também pode mudar o nome do seu pipeline de lançamento selecionando o texto "Novo pipeline de lançamento" na parte superior.

4. Ligue o lançamento aos artefactos de construção que são publicados pelo pipeline de construção. Clique em **Adicionar** na área de artefactos.

   ![Clique em adicionar na área de artefactos da interface](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. Na **página Adicionar um artefacto**, selecione **Construir** como o **tipo Fonte**. Escolha o projeto e o pipeline de construção que criou. Se desejar, pode alterar o **pseudónimo Fonte** para algo mais descritivo. Em seguida, **selecione Adicionar**.

   ![Na página de adicionar um artefacto, selecione Adicionar para criar o artefacto](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. Abra os gatilhos do artefacto e selecione o alternância para ativar o gatilho de implantação contínua. Agora, um novo lançamento será criado cada vez que uma nova construção estiver disponível.

   ![Abra os gatilhos do artefacto e alterne para ativar o gatilho de implantação contínua](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. O **estágio dev** é pré-configurado com um trabalho e zero tarefas. A partir do menu do pipeline, selecione **Tarefas** e escolha o estágio **dev.** Selecione o **trabalho de Agente** e altere o seu nome de **exibição** para **QA**. Pode configurar detalhes sobre o trabalho do agente, mas a tarefa de implementação é insensível à plataforma para que possa utilizar qualquer **especificação de Agente** na piscina do **Agente** escolhido.

   ![Ver as tarefas para o seu estágio dev no separador Tarefas](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. No trabalho de QA, selecione o sinal de mais ( **+** ) para adicionar duas tarefas. Procure e adicione **Azure IoT Edge** duas vezes.

9. Selecione a primeira tarefa **Azure IoT Edge** e configuure-a com os seguintes valores:

    | Parâmetro | Descrição |
    | --- | --- |
    | Nome a apresentar | O nome de visualização é atualizado automaticamente quando o campo Ação muda. |
    | Ação | Selecione `Generate deployment manifest`. |
    | .template.jsno arquivo | Especificar o caminho: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` . O caminho é publicado a partir de um oleoduto de construção. |
    | Plataforma padrão | Selecione o sistema operativo apropriado para os seus módulos com base no seu dispositivo IoT Edge direcionado. |
    | Percurso de saída| Coloque o `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` caminho. Este caminho é o ficheiro manifesto de implantação IoT Edge final. |

    Estas configurações ajudam a substituir os URLs de imagem do módulo no `deployment.template.json` ficheiro. O **manifesto de implantação gerar** também ajuda a substituir as variáveis pelo valor exato definido no `deployment.template.json` ficheiro. No Código VS/VS, está a especificar o valor real num `.env` ficheiro. Em Pipelines Azure, define o valor no **separador 'Variáveis do Pipeline' de lançamento.** Mover para separador **variáveis** e configurar o nome e o valor como seguinte:

    * **ACR_ADDRESS**: O valor do servidor de registo do registo do seu registo de registo de **dados.** Pode recuperar o servidor 'Iniciar sessão' a partir da página de visão geral do seu registo de contentores no portal Azure.
    * **ACR_PASSWORD:** A sua senha de registo do contentor Azure.
    * **ACR_USER**: O nome de utilizador do registo do seu registo de contentores Azure.

    Se tiver outras variáveis no seu projeto, pode especificar o nome e o valor neste separador. O **manifesto de implantação geração** só pode reconhecer as variáveis que estão no `${VARIABLE}` sabor. Certifique-se de que está a usar este sabor nos seus `*.template.json` ficheiros.
    
    ```json-interactive
    "registryCredentials": {
      "<ACR name>": { // Your Azure Container Registry **Registry name** value
        "username": "${ACR_USER}",
        "password": "${ACR_PASSWORD}",
        "address": "${ACR_ADDRESS}"
      }
    }
    ```
    
    ![Configure as variáveis para o seu pipeline de libertação no separador Variáveis](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. Selecione a segunda tarefa **Azure IoT Edge** e configuure-a com os seguintes valores:

    | Parâmetro | Descrição |
    | --- | --- |
    | Nome a apresentar | O nome de visualização é atualizado automaticamente quando o campo Ação muda. |
    | Ação | Selecione `Deploy to IoT Edge devices`. |
    | Ficheiro de implantação | Coloque o `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` caminho. Este caminho é o ficheiro manifesto de implementação IoT Edge. |
    | Subscrição do Azure | Selecione a subscrição que contém o seu Hub IoT.|
    | Nome do Hub IoT | Selecione o seu hub IoT.|
    | Escolha dispositivo único/múltiplo | Escolha se deseja que o gasoduto de desbloqueio seja implantado num ou em vários dispositivos. Se utilizar para um único dispositivo, introduza o **ID do dispositivo IoT Edge**. Se estiver a ser implantado em vários dispositivos, especifique a **condição de alvo** do dispositivo . A condição do alvo é um filtro que combina com um conjunto de dispositivos IoT Edge no IoT Hub. Se pretender utilizar as etiquetas do dispositivo como condição, tem de atualizar as etiquetas dos dispositivos correspondentes com o ioT Hub de dispositivo twin. Atualize a prioridade de **implementação IoT Edge ID** e **IoT Edge** nas definições avançadas. Para obter mais informações sobre a criação de uma implementação para vários dispositivos, consulte [as implementações automáticas Understand IoT Edge](../articles/iot-edge/module-deployment-monitoring.md). |
    | ID do dispositivo ou condição alvo | Dependendo da seleção prévia, especifique uma [identificação](../articles/iot-edge/module-deployment-monitoring.md#target-condition) do dispositivo ou uma condição alvo para implantar em vários dispositivos. |
    | Avançado | Para o IoT Edge deployment ID, especifique `$(System.TeamProject)-$(Release.EnvironmentName)` . Esta variável mapeia o nome do projeto e do lançamento com o seu IoT Edge implementação ID. |
    

    Se a sua tarefa envolver a utilização de uma imagem que reside num Registo De Confiança De Docker privado que não seja visível para a nuvem pública, pode definir a variável **ambiente SKIP_MODULE_IMAGE_VALIDATION** para saltar `true` a validação da imagem. 

    ![Adicione tarefas Azure IoT Edge para o seu estágio dev](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. **Selecione Guardar** para guardar as alterações no novo oleoduto de lançamento. Volte à vista do gasoduto selecionando o separador **Pipeline** no menu.
