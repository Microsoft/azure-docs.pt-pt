---
title: Falhas no VM e no ambiente da Azure DevTest
description: Saiba como resolver falhas de máquina virtual (VM) e de criação de ambiente em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b7d3f3ad34d8a5bb48607816623c67121d21d78c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85476483"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Falhas na criação de máquinas virtuais (VM) e de criação de ambiente em Azure DevTest Labs
A DevTest Labs dá-lhe avisos se um nome de máquina é inválido ou se está prestes a violar uma política de laboratório. Às vezes, vês vermelho `X` ao lado do teu VM de laboratório ou estado ambiental que te informa que alguma coisa correu mal.  Este artigo fornece alguns truques que pode usar para encontrar a questão subjacente e, espero, evitar a questão no futuro.

## <a name="portal-notifications"></a>Notificações do portal
Se estiver a utilizar o portal Azure, o primeiro lugar a analisar é o **painel de notificações**.  O painel de notificações, disponível na barra de comando principal clicando no ícone da **campainha,** dir-lhe-á se o VM de laboratório ou a criação ambiental foi ou não bem sucedido.  Se houve uma falha, vê a mensagem de erro associada à falha de criação. Os detalhes geralmente dão mais informações para ajudá-lo a resolver o problema. No exemplo seguinte, a criação de máquinas virtuais falhou devido ao esgotar-se os núcleos. A mensagem detalhada diz-lhe como corrigir o problema e solicitar um aumento de quota principal.

![Notificação do portal Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM no estado de corrupção
Se vir o estado do seu VM em laboratório como **Corrupto,** o VM subjacente pode ter sido eliminado da página **Máquina Virtual** que o utilizador pode navegar a partir da página **Máquinas Virtuais** (não a partir da página DevTest Labs). Limpe o seu laboratório em Laboratórios DevTest, eliminando o VM do laboratório. Então, recrie o seu VM no laboratório. 

![VM em estado corrompido](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Registos de atividade
Veja os registos de atividades se estiver a investigar uma falha algum tempo depois de tentar a criação do seu VM ou ambiente. Esta secção mostra-lhe como encontrar registos para VMs e ambientes.

## <a name="activity-logs-for-virtual-machines"></a>Registos de atividades para máquinas virtuais

1. Na página inicial do seu laboratório, selecione o VM para lançar a página **'Máquina Virtual'.**
2. Na página **'Máquina Virtual',** na secção **MONITORING** do menu esquerdo, selecione **Registo de atividade** para ver todos os registos associados ao VM.
3. Nos itens de registo de atividade, selecione a operação que falhou. Normalmente, a operação falhada chama-se `Write Virtualmachines` .
4. No painel direito, mude para o separador JSON. Veja os detalhes na vista JSON do registo.

    ![Registo de atividades para um VM](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Procure no registo JSON até encontrar a `statusMessage` propriedade. Dá-lhe a mensagem de erro principal e informações mais detalhadas, se aplicável. O JSON seguinte é um exemplo para o erro superior citado anteriormente neste artigo.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Log de atividade para um ambiente

Para ver o registo de atividades para uma criação ambiental, siga estes passos:

1. Na página inicial do seu laboratório, selecione **Configuração e políticas** no menu esquerdo.
2. na página **de Configuração e políticas,** selecione **registos de atividade** no menu.
3. Procure a falha na lista de atividades no registo e selecione-a.
4. No painel direito, mude para o separador JSON e procure o **statusMessage**.

    ![Registo de atividade ambiental](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Registos de implementação do modelo do gestor de recursos
Se o seu ambiente ou máquina virtual foi criado através da automatização, há um último lugar para procurar informações de erro. É o registo de implementação do modelo do Azure Resource Manager. Quando um recurso de laboratório é criado através da automatização, é frequentemente feito através de uma implementação de modelo do Azure Resource Manager. Consulte [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) os modelos de gestor de recursos Azure que criam recursos da DevTest Labs.

Para ver os registos de implementação do modelo de laboratório, siga estes passos:

1. Lance a página para o grupo de recursos em que o laboratório existe.
2. Selecione **Implementações** no menu esquerdo em **Definições**.
3. Procure implementações com um estado falhado e selecione-o.
4. Na página **'Implantar',** selecione **''Operação' link** para a operação que falhou.
5. Veja detalhes sobre a operação que falhou na janela de detalhes da **Operação.**

## <a name="next-steps"></a>Passos seguintes
Ver [falhas de artefactos de resolução de problemas](devtest-lab-troubleshoot-artifact-failure.md)
