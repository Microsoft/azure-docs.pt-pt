---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: c7be10e14daf3ee769e86d51f648cc6b656a416a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89303056"
---
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Verifique o CI/CD IoT Edge com os gasodutos de construção e libertação

Para desencadear um trabalho de construção, pode empurrar um compromisso para o repositório de código fonte ou desencadeá-lo manualmente. Nesta secção, aciona manualmente o gasoduto CI/CD para testar o seu funcionamento. Então verifique se a implantação tem sucesso.

1. A partir do menu do painel esquerdo, selecione **Pipelines** e abra o gasoduto de construção que criou no início deste artigo.

2. Pode ativar um trabalho de construção no seu oleoduto de construção selecionando o botão **de gasoduto Run** no canto superior direito.

    ![Desencadeie manualmente o seu gasoduto de construção utilizando o botão de gasoduto Run](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/manual-trigger.png)

3. Reveja as definições do **gasoduto Run.** Em seguida, selecione **Run**.

    ![Especifique as opções de pipeline de execução e selecione Executar](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/run-pipeline-settings.png)

4. Selecione **o agente trabalho 1** para ver o progresso da corrida. Pode rever os registos da saída do trabalho selecionando o trabalho. 

    ![Reveja a saída de registo do trabalho](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/view-job-run.png)

5. Se o gasoduto de construção estiver concluído com sucesso, aciona um desbloqueio para o **estágio dev.** A versão **dev** bem sucedida cria a implementação IoT Edge para direcionar os dispositivos IoT Edge.

    ![Liberar para dev](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/pending-approval.png)

6. Clique **em fase de dev** para ver os registos de lançamento.

    ![Registos de versão](./media/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment/release-logs.png)