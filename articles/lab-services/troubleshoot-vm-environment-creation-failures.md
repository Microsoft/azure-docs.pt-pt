---
title: Falhas na VM e no ambiente Azure DevTest Labs
description: Aprenda a resolver problemas com a máquina virtual (VM) e falhas de criação de ambiente em Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 155a359608cf6d846578306545f5ce0b4003949c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76166337"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Falhas na máquina virtual (VM) e na criação de ambiente nos Laboratórios Azure DevTest
A DevTest Labs dá-lhe avisos se um nome de máquina é inválido ou se está prestes a violar uma política de laboratório. Às vezes, `X` vemos vermelho ao lado do seu laboratório VM ou estado ambiental que nos informa que algo correu mal.  Este artigo fornece alguns truques que pode usar para encontrar a questão subjacente e, esperemos, evitar a questão no futuro.

## <a name="portal-notifications"></a>Notificações do portal
Se estiver a utilizar o portal Azure, o primeiro local para ver é o painel de **notificações.**  O painel de notificações, disponível na barra de comando principal clicando no ícone do **sino,** dir-lhe-á se o VM de laboratório ou a criação ambiental foram bem sucedidos ou não.  Se houve uma falha, vê-se a mensagem de erro associada à falha da criação. Os detalhes muitas vezes dão mais informações para ajudá-lo a resolver o problema. No exemplo seguinte, a criação de máquinas virtuais falhou devido ao esgotação dos núcleos. A mensagem detalhada diz-lhe como corrigir o problema e solicitar um aumento de quota central.

![Notificação do portal Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>VM em estado de corrupção
Se vir o estado do seu VM no laboratório como **Corrompido,** o VM subjacente pode ter sido eliminado da página **Máquina Virtual** que o utilizador pode navegar a partir da página **DevTest** Labs( não a partir da página DevTest Labs). Limpe o seu laboratório nos Laboratórios DevTest, apagando o VM do laboratório. Então, recrie o seu VM no laboratório. 

![VM em estado corrompido](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>Registos de atividade
Veja os registos de atividade se estiver a investigar uma falha algum tempo depois de tentar a criação do seu VM ou ambiente. Esta secção mostra-lhe como encontrar registos para VMs e ambientes.

## <a name="activity-logs-for-virtual-machines"></a>Registos de atividade para máquinas virtuais

1. Na página inicial do seu laboratório, selecione o VM para lançar a página **Máquina Virtual.**
2. Na página **Máquina Virtual,** na secção **MONITORING** do menu esquerdo, selecione **registo de atividade** para ver todos os registos associados ao VM.
3. Nos itens de registo de atividade, selecione o funcionamento que falhou. Tipicamente, a operação falhada `Write Virtualmachines`é chamada .
4. No painel direito, mude para o separador JSON. Veja os detalhes na vista JSON do registo.

    ![Registo de atividade para um VM](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Procure através do registo da JSON até encontrar a `statusMessage` propriedade. Dá-lhe a principal mensagem de erro e informações mais detalhadas, se aplicável. O JSON seguinte é um exemplo para o erro superior citado pelo núcleo observado anteriormente neste artigo.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Registo de atividade para um ambiente

Para ver o registo de atividade para uma criação ambiental, siga estes passos:

1. Na página inicial do seu laboratório, selecione **Configuração e políticas** no menu esquerdo.
2. na página **de Configuração e políticas,** selecione **registos de Atividade** no menu.
3. Procure a falha na lista de atividades no registo e selecione-a.
4. No painel direito, mude para o separador JSON e procure o **estadoMensagem**.

    ![Registo de atividade ambiental](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Registos de implementação do modelo do Gestor de Recursos
Se o seu ambiente ou máquina virtual foi criado através da automação, há um último lugar para procurar informações de erro. É o registo de implantação do modelo do Gestor de Recursos Azure. Quando um recurso de laboratório é criado através da automação, é feito frequentemente através de uma implementação do modelo do Gestor de Recursos Azure. Consulte[https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) os modelos do Gestor de Recursos Azure que criam recursos do DevTest Labs.

Para ver os registos de implantação do modelo de laboratório, siga estes passos:

1. Lance a página para o grupo de recursos em que o laboratório existe.
2. Selecione **Implementações** no menu esquerdo em **Definições**.
3. Procure implementações com um estado falhado e selecione-as.
4. Na página **de Implantação,** selecione link de **dados da Operação** para a operação que falhou.
5. Veja detalhes sobre a operação que falhou na janela de detalhes da **Operação.**

## <a name="next-steps"></a>Passos seguintes
Ver falhas de [artefactos de resolução de problemas](devtest-lab-troubleshoot-artifact-failure.md)
