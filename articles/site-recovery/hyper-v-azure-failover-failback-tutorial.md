---
title: Fazer a ativação pós-falha e a reativação pós-falha de VMs de Hyper-V durante a recuperação após desastre para o Azure com o Azure Site Recovery | Microsoft Docs
description: Saiba como fazer a ativação pós-falha e a reativação pós-falha de VMs de Hyper-V durante a recuperação após desastre para o Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 53b2c2945062ef348104e24a352895a14eed1a04
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58314863"
---
# <a name="fail-over-and-fail-back-hyper-v-vms-replicated-to-azure"></a>Ativação pós-falha e reativação pós-falha de VMs de Hyper-V replicadas no Azure

Este tutorial descreve como proceder à ativação pós-falha de uma VM de Hyper-V para o Azure. Depois de feita a ativação pós-falha, vai fazer a reativação pós-falha para o seu site no local quando estiver disponível. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Verificar as propriedades da VM de Hyper-V para cumprir os requisitos do Azure
> * Executar uma ativação pós-falha para o Azure
> * Reativação pós-falha do Azure para o local
> * Replicação inversa das VM no local, para voltar a iniciar a replicação para o Azure

Este é o quinto tutorial de uma série. Este tutorial parte do princípio de que já concluiu as tarefas dos tutoriais anteriores.    

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. [Prepara Hyper-V no local](tutorial-prepare-on-premises-hyper-v.md)
3. Configurar recuperação após desastre para as [ VM de Hyper-V](tutorial-hyper-v-to-azure.md) ou para as [VM de Hyper-V geridas nas System Center VMM clouds](tutorial-hyper-v-vmm-to-azure.md)
4. [Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md)

## <a name="prepare-for-failover-and-failback"></a>Preparar a ativação pós-falha e a reativação pós-falha

Certifique-se de que não existem instantâneos na VM e de que a VM no local está desativada durante a reativação pós-falha. Desta forma, garante-se a consistência dos dados durante a replicação. Não ative a VM no local durante a reativação pós-falha. 

A ativação pós-falha e a reativação pós-falha têm quatro fases:

1. **Ativação pós-falha do Azure**: Ativação pós-falha de VMs Hyper-V do site no local para o Azure.
2. **Reativação pós-falha para o local**: VMs do Azure de ativação pós-falha para o seu site no local quando o site no local está disponível. Começa a sincronização de dados a partir do Azure para o local e, após a conclusão, apresenta as VM no local.  
3. **A replicação inversa VMs no local**: Depois de falha para o local, o inverso replicar as VMs no local para iniciar a replicação-los para o Azure.

## <a name="verify-vm-properties"></a>Verificar as propriedades da VM

Antes da ativação pós-falha verifique as propriedades da VM e certifique-se de que a VM está em conformidade com os [requisitos do Azure](hyper-v-azure-support-matrix.md#replicated-vms).

Em **Itens Protegidos**, clique em **Itens Replicados** > VM.

1. No painel **Item replicado**, existe um resumo das informações, do estado de funcionamento e dos pontos de recuperação mais recentes disponíveis da VM. Clique em **Propriedades** para ver mais detalhes.

1. Em **Computação e Rede**, pode modificar o nome do Azure, o grupo de recursos, o tamanho de destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) e as definições do disco gerido.

1. Pode ver e modificar as definições de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após a ativação pós-falha e o endereço IP que será atribuído à mesma.

1. Em **Discos**, pode ver informações sobre o sistema operativo e os discos de dados na VM.

## <a name="failover-to-azure"></a>Ativação pós-falha para o Azure

1. Em **Definições** > **Itens replicados** clique na VM > **Ativação Pós-falha**.
2. Na **Ativação pós-falha**, selecione o ponto de recuperação **Mais recente**. 
3. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tenta encerrar as VM de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.
4. Depois de verificar a ativação pós-falha, clique em **Consolidar**. Todos os pontos de recuperação disponíveis são eliminados.

> [!WARNING]
> **Não cancelar uma ativação pós-falha em curso**: Se cancela em curso, paradas de ativação pós-falha, mas a VM não replicar novamente.

## <a name="failback-azure-vm-to-on-premises-and-reverse-replicate-the-on-premises-vm"></a>Reativação pós-falha da VM do Azure para o local e replicação inversa da VM no local

A operação de reativação pós-falha é basicamente uma ativação pós-falha do Azure para o site no local e, na replicação inversa, começa novamente a replicar as VM do site no local para o Azure.

1. Em **Definições** > **Itens replicados** clique na VM > **Ativação Pós-falha Planeada**.
2. Em **Confirmar Ativação Pós-falha Planeada**, verifique a direção da ativação pós-falha (a partir do Azure) e selecione as localizações de origem e de destino.
3. Selecione **Sincronizar dados antes da ativação pós-falha (sincronizar apenas alterações delta)**. Esta opção minimiza o período de indisponibilidade da VM porque a sincronização é efetuada sem encerrar a VM.
4. Iniciar a ativação pós-falha. Pode seguir o progresso da ativação pós-falha no separador **Trabalhos**.
5. Após a conclusão da sincronização inicial dos dados e quando estiver preparado para encerrar as VM do Azure, clique em **Trabalhos** > nome-da-tarefa-de-ativação-pós-falha-planeada> **Concluir Ativação Pós-Falha**. Encerra a VM do Azure, transfere as alterações mais recentes no local e inicia a VM no local.
6. Inicie sessão na VM no local para verificar se está disponível como previsto.
7. A VM no local está agora num estado **Consolidar Pendente**. Clique em **Consolidar**. Elimina as VM do Azure e os respetivos discos e prepara a VM no local para a replicação inversa.
Para iniciar a replicação das VM no local para o Azure, ative a **Replicação Inversa**. Aciona a replicação das alterações delta introduzidas desde que a VM do Azure foi desligada.  
