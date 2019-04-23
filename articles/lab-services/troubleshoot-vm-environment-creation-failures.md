---
title: Resolver problemas de falhas de criação de VM e o ambiente do Azure DevTest Labs | Documentos da Microsoft
description: Saiba como resolver problemas de máquina virtual (VM) e de falhas de criação do ambiente no Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.openlocfilehash: a653a785e99619c3e256613d6a4d2c7592f54c8c
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149398"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>Resolver problemas de máquina virtual (VM) e de falhas de criação do ambiente no Azure DevTest Labs
DevTest Labs dá avisos se um nome de máquina é inválido ou se está prestes a violar uma política de laboratório. Às vezes, pode ver a vermelho `X` junto ao seu estado VM ou de ambiente que informa-o de que algo saiu errado de laboratório.  Este artigo fornece alguns truques que pode utilizar para localizar o problema subjacente e, Espero que evitar o problema no futuro.

## <a name="portal-notifications"></a>Notificações do portal
Se estiver a utilizar o portal do Azure, o primeiro lugar para examinar é o **painel de notificações**.  Painel de notificações, disponíveis na barra de comandos principal, clicando a **ícone de sino**, informará se a criação de VM ou de ambiente de laboratório foi concluída com êxito ou não.  Se Ocorreu uma falha, verá a mensagem de erro associada a criação falha. Os detalhes muitas vezes oferecem ainda mais informações para ajudar a resolver o problema. No exemplo seguinte, a criação da máquina virtual falhou devido a ficar sem de núcleos. A mensagem detalhada indica como corrigir o problema e pedir um aumento de quota de núcleos.

![Notificação do portal do Azure](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)


## <a name="activity-logs"></a>Registos de atividade
Ver registos de atividade se estiver a investigar uma falha de algum tempo depois de tentar a criação de seu ambiente ou VM. Esta secção mostra como encontrar registos para VMs e ambientes.

## <a name="activity-logs-for-virtual-machines"></a>Registos de atividades para máquinas virtuais

1. Na home page para seu laboratório, selecione a VM para iniciar o **Máquina Virtual** página.
2. No **Máquina Virtual** página, além do **monitorização** secção do menu à esquerda, selecione **registo de atividades** para ver todos os registos associados à VM.
3. Os itens de registo de atividade, selecione a operação que falhou. Normalmente, a operação falhada é chamada `Write Virtualmachines`.
4. No painel da direita, mude para o separador JSON. Consulte os detalhes na vista de JSON do registo.

    ![Registo de atividades para uma VM](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. Examine o registo de JSON até encontrar o `statusMessage` propriedade. Ele fornece a mensagem de erro principal e obter mais informações de detalhe, se aplicável. O JSON seguinte é um erro de exemplo para o núcleo quoted excedido visto anteriormente no artigo.

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at http://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>Registo de atividades para um ambiente

Para ver o registo de atividades para a criação de um ambiente, siga estes passos:

1. Na home page para seu laboratório, selecione **Konfigurace a zásady** no menu da esquerda.
2. na **Konfigurace a zásady** página, selecione **registos de atividades** no menu.
3. Procure a falha na lista no registo de atividade e selecioná-lo.
4. No painel da direita, mude para o separador JSON e procure o **statusMessage**.

    ![Registo de atividades do ambiente](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>Registos de implementação do modelo do Resource Manager
Se seu ambiente ou a máquina virtual foi criada através da automatização, há um local de última para ver informações de erro. Que é o registo de implementação de modelo do Azure Resource Manager. Quando um recurso de laboratório é criado através da automatização, muitas vezes, é feita por meio de uma implementação do modelo do Azure Resource Manager. Ver[ https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates ](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) para modelos do Azure Resource Manager de exemplo que criam os recursos de DevTest Labs.

Para ver os registos de implementação do modelo de laboratório, siga estes passos:

1. Inicie a página para o grupo de recursos no qual existe o laboratório.
2. Selecione **implementações** no menu da esquerda sob **definições**.
3. Procure implementações com um status de falha e selecioná-lo.
4. Sobre o **implantação** página, selecione **detalhes da operação** ligação para a operação falhada.
5. Pode ver detalhes sobre a operação que falhou no **detalhes da operação** janela.

## <a name="next-steps"></a>Passos Seguintes
Consulte [resolução de problemas de falhas de artefactos](devtest-lab-troubleshoot-artifact-failure.md)