---
title: Falhe novamente Azure VMs de IaaS replicadas para uma região secundária do Azure para recuperação após desastre com o serviço Azure Site Recovery.
description: Saiba como efetuar a ativação de VMs de volta do Azure com o serviço Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: sideeksh
ms.custom: mvc
ms.openlocfilehash: 90ebea81c37c40dc5396d2e6378c7b9af52ad23d
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733113"
---
# <a name="fail-back-azure-vms-between-azure-regions"></a>Efetuar a ativação de VMs de volta do Azure entre regiões do Azure

O [do Azure Site Recovery](site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação após desastre através da gestão e orquestração de replicação, ativação pós-falha e reativação pós-falha de máquinas no local e máquinas virtuais do Azure (VMs).

Este tutorial descreve como efetuar a reativação pós-falha numa única VM do Azure. Depois de feita a ativação pós-falha, vai fazer a reativação pós-falha para a região primária quando estiver disponível. Neste tutorial, ficará a saber como:

> [!div class="checklist"]

> * Realizar a reativação pós-falha da VM secundária
> * Voltar a proteger a VM primária para a região secundária

> [!NOTE]
>
> Este tutorial destina-se para orientar o usuário através dos passos para ativação pós-falha para uma região de destino e com o mínimo personalização; caso deseje saber mais sobre os vários aspectos associados à ativação pós-falha, incluindo as considerações de rede, a automatização ou a resolução de problemas, consulte os documentos em "Como para" para VMs do Azure.

## <a name="prerequisites"></a>Pré-requisitos

> * Certifique-se de que a VM está no estado de ativação pós-falha consolidada e verifique se a região primária está disponível, e pode criar e aceder aos novos recursos na mesma.
> * Certifique-se de que essa nova proteção está ativada.

## <a name="fail-back-to-the-primary-region"></a>Realizar a reativação pós-falha para a região primária

Depois das VMs são novamente protegidas, pode efetuar a ativação de volta para a região primária como e quando deseja.

1. Vá para a recuperação Cofre de serviços. Clique em itens replicados e selecione a VM que foi protegida novamente.

2. Deverá ver o seguinte. Observe que é semelhante para o painel para ativação pós-falha de teste e ativação pós-falha da região primária.
![Reativação pós-falha para principal](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback.png)

3. Clique em testar ativação pós-falha para executar uma ativação pós-falha de teste para a região primária. Escolha o ponto de recuperação e a rede Virtual para a ativação pós-falha de teste e selecione OK. Pode ver o teste de que VM criada na região primária, que pode aceder e inspecionar.

4. Assim que a ativação pós-falha de teste seja satisfatória, pode clicar em ativação pós-falha de teste de limpeza para limpar os recursos criados na região de origem para a ativação pós-falha de teste.

5. Os itens de replicado, selecione a VM que pretende que a ativação pós-falha > ativação pós-falha.

6. Numa ativação pós-falha, selecione um ponto de recuperação a ativação pós-falha. Pode utilizar uma das opções seguintes:
    1. Versão mais recente (predefinição): Esta opção processa todos os dados no serviço Site Recovery e fornece o objetivo de ponto de recuperação (RPO) mais baixo
    2. Processado mais recentemente: Esta opção reverte a máquina virtual para o ponto de recuperação mais recente que foram processado pelo serviço Site Recovery
    3. Personalizado: Utilize esta opção para ativação pós-falha para um ponto de recuperação específico. Esta opção é útil para realizar uma ativação pós-falha de teste

7. Selecione desligar pendente máquina antes de iniciar a ativação pós-falha, se pretender que o Site Recovery para tentar fazer um encerramento das máquinas de virtuais de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Tenha em atenção que o Site Recovery não tem de limpeza do wsu origem após a ativação pós-falha.

8. Siga o progresso de ativação pós-falha na página de tarefas

9. Após a ativação pós-falha, valide a máquina virtual ao iniciar sessão. Se pretender ir a outro ponto de recuperação para a máquina virtual, em seguida, pode utilizar opção do ponto de recuperação de alteração.

10. Assim que estiver satisfeito com a máquina virtual, ativação pós-falha pode consolida a ativação pós-falha. Ao consolidar elimina todos os pontos de recuperação disponíveis com o serviço. A opção de ponto de recuperação de alteração já não está disponível.

![VM em regiões primária e secundária](./media/site-recovery-azure-to-azure-failback/azure-to-azure-failback-vm-view.png)

Se vir a captura de ecrã anterior, "ContosoWin2016" VM foi efetuada a partir do centro dos E.U.A., para E.U.A. leste e não foi possível do E.U.A. Leste dos EUA Central.

Tenha em atenção que as VMs de DR permanecerá no Estado desalocado encerramento. Este comportamento é propositado porque o Azure Site Recovery guarda as informações da máquina virtual, que pode ser útil na ativação pós-falha para as primárias para a região secundária mais tarde. Não é cobrada para as máquinas virtuais desalocadas, pelo que devem ser mantida como está.

> [!NOTE]
> Consulte a ["como" secção](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection) para obter mais detalhes sobre o fluxo de trabalho de nova proteção e o que acontece durante a reavaliação de proteção.
