---
title: Fazer failback de VMs do Hyper-v do Azure com Azure Site Recovery
description: Saiba como executar failback de VMs do Hyper-V para um site local durante a recuperação de desastres no Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 09/12/2019
ms.author: rajanaki
ms.openlocfilehash: b924c1424a309fb61f690c21e5665a70356c7a62
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084233"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Executar um failback para VMs do Hyper-V

Este artigo descreve como fazer failback de máquinas virtuais Hyper-V protegidas por Site Recovery.

## <a name="prerequisites"></a>Pré-requisitos

- Verifique se você leu os detalhes sobre os [diferentes tipos de failback](concepts-types-of-failback.md) e as advertências correspondentes.
- Verifique se o servidor VMM do site primário ou o servidor host Hyper-V está conectado ao Azure.
- Você deve ter executado a **confirmação** na máquina virtual.
- Verifique se você está usando uma conta de armazenamento para replicação e não para discos gerenciados. Não há suporte para o failback de VMs do Hyper-V replicadas usando discos de gerenciamento.

## <a name="perform-failback"></a>Executar failback
Após o failover do local primário para o secundário, as máquinas virtuais replicadas não são protegidas pelo Site Recovery, e o local secundário agora está atuando como o local ativo. Para fazer failback de VMs em um plano de recuperação, execute um failover planejado do site secundário para o primário, como a seguir. 
1. Selecione **planos de recuperação** > *recoveryplan_name*. Clique em **failover** > **failover planejado**.
2. Na página **confirmar failover planejado** , escolha os locais de origem e de destino. Observe a direção do failover. Se o failover do primário funcionou como esperado e todas as máquinas virtuais estão no local secundário, isso é apenas para fins informativos.
3. Se você estiver realizando o failback do Azure, selecione configurações na **sincronização de dados**:
    - **Sincronizar dados antes do failover (sincronizar somente alterações delta)** – essa opção minimiza o tempo de inatividade para máquinas virtuais à medida que ela é sincronizada sem desligá-las. Ele realiza as seguintes etapas:
        - Fase 1: tira o instantâneo da máquina virtual no Azure e a copia para o host Hyper-V local. O computador continua sendo executado no Azure.
        - Fase 2: desliga a máquina virtual no Azure para que nenhuma nova alteração ocorra lá. O conjunto final de alterações delta é transferido para o servidor local e a máquina virtual local é iniciada.

    - **Sincronizar dados somente durante o failover (download completo)** — essa opção é mais rápida.
        - Essa opção é mais rápida porque esperamos que a maior parte do disco tenha mudado e não queremos gastar tempo no cálculo da soma de verificação. Ele realiza um download do disco. Ele também é útil quando a máquina virtual local foi excluída.
        - Recomendamos que você use essa opção se estiver executando o Azure por um tempo (um mês ou mais) ou se a máquina virtual local tiver sido excluída. Essa opção não executa nenhum cálculo de soma de verificação.


4. Se a criptografia de dados estiver habilitada para a nuvem, em **chave de criptografia** , selecione o certificado que foi emitido quando você habilitou a criptografia de dados durante a instalação do provedor no servidor do VMM.
5. Iniciar a ativação pós-falha. Pode seguir o progresso da ativação pós-falha no separador **Trabalhos**.
6. Se você selecionou a opção para sincronizar os dados antes do failover, depois que a sincronização de dados inicial for concluída e você estiver pronto para desligar as máquinas virtuais no Azure, clique em **trabalhos** > nome do trabalho > **concluir failover**. Isso desliga o computador do Azure, transfere as alterações mais recentes para a máquina virtual local e inicia a VM no local.
7. Agora você pode fazer logon na máquina virtual para validar que ela está disponível conforme o esperado.
8. A máquina virtual está em um estado de confirmação pendente. Clique em **confirmar** para confirmar o failover.
9. Para concluir o failback, clique em **replicação inversa** para começar a proteger a máquina virtual no site primário.


Siga estes procedimentos para fazer failback para o site primário original. Este procedimento descreve como executar um failover planejado para um plano de recuperação. Como alternativa, você pode executar o failover para uma única máquina virtual na guia **máquinas virtuais** .


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Failback para um local alternativo no ambiente do Hyper-V
Se você implantou a proteção entre um [site do Hyper-V e o Azure](site-recovery-hyper-v-site-to-azure.md) , é necessário realizar o failback do Azure para um local alternativo. Isso será útil se você precisar configurar um novo hardware local. Veja como fazer isso.

1. Se você estiver configurando um novo hardware, instale o Windows Server 2012 R2 e a função Hyper-V no servidor.
2. Crie um comutador de rede virtual com o mesmo nome que você tinha no servidor original.
3. Selecione **itens protegidos** -> **grupo de proteção** -> \<ProtectionGroupName >-> \<VirtualMachineName > você deseja fazer failback e selecione **failover planejado**.
4. Em **confirmar failover planejado** , selecione **criar máquina virtual local se ela não existir**.
5. Em nome do host, * * selecione o novo servidor de host Hyper-V no qual você deseja posicionar a máquina virtual.
6. Em sincronização de dados, recomendamos que você selecione a opção para sincronizar os dados antes do failover. Isso minimiza o tempo de inatividade para máquinas virtuais à medida que ela é sincronizada sem desligá-las. Ele faz o seguinte:

    - Fase 1: tira o instantâneo da máquina virtual no Azure e a copia para o host Hyper-V local. O computador continua sendo executado no Azure.
    - Fase 2: desliga a máquina virtual no Azure para que nenhuma nova alteração ocorra lá. O conjunto final de alterações é transferido para o servidor local e a máquina virtual local é iniciada.
    
7. Clique na marca de seleção para iniciar o failover (failback).
8. Depois que a sincronização inicial for concluída e você estiver pronto para desligar a máquina virtual no Azure, clique em **trabalhos** > \<trabalho de failover planejado > > **concluir o failover**. Isso desliga o computador do Azure, transfere as alterações mais recentes para a máquina virtual local e a inicia.
9. Você pode fazer logon na máquina virtual local para verificar se tudo está funcionando conforme o esperado. Em seguida, clique em **confirmar** para concluir o failover. Commit exclui a máquina virtual do Azure e seus discos e prepara a VM para ser protegida novamente.
10. Clique em **replicação inversa** para começar a proteger a máquina virtual local.

    > [!NOTE]
    > Se você cancelar o trabalho de failback enquanto ele estiver na etapa de sincronização de dados, a VM local estará em um estado corrompido. Isso ocorre porque a sincronização de dados copia os dados mais recentes dos discos de VM do Azure para os discos de dados locais e, até que a sincronização seja concluída, os dados do disco podem não estar em um estado consistente. Se a VM local for inicializada depois que a sincronização de dados for cancelada, ela poderá não ser inicializada. Dispare novamente o failover para concluir a sincronização de dados.


## <a name="why-is-there-no-button-called-failback"></a>Por que não há um botão chamado failback?
No portal, não há nenhum gesto explícito chamado failback. O failback é uma etapa em que você volta para o site primário. Por definição, o failback é quando você faz failover das máquinas virtuais da recuperação de volta para a primária.

Quando você inicia um failover, a folha informa sobre a direção na qual as máquinas virtuais serão movidas, se a direção for do Azure para o local, é um failback.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Por que há apenas um gesto de failover planejado para o failback?
O Azure é um ambiente altamente disponível e suas máquinas virtuais estão sempre disponíveis. O failback é uma atividade planejada onde você decide levar um tempo de inatividade pequeno para que as cargas de trabalho possam começar a ser executadas no local novamente. Isso não espera perda de dados. Portanto, apenas um gesto de failover planejado está disponível, o que desativará as VMs no Azure, baixará as alterações mais recentes e garantirá que não haja perda de dados.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>Preciso de um servidor de processo no Azure para fazer failback para o Hyper-v?
Não, um servidor de processo é necessário somente quando você está protegendo máquinas virtuais VMware. Nenhum componente adicional precisa ser implantado durante a proteção/failback de máquinas virtuais do Hyper-v.


## <a name="time-taken-to-failback"></a>Tempo necessário para o failback
O tempo necessário para concluir a sincronização de dados e inicializar a máquina virtual depende de vários fatores. Para dar uma percepção do tempo gasto, explicamos o que acontece durante a sincronização de dados.

A sincronização de dados tira um instantâneo dos discos da máquina virtual e inicia a verificação do bloco por bloco e calcula sua soma de verificação. Essa soma de verificação calculada é enviada para o local para comparar com a soma de verificação local do mesmo bloco. Caso as somas de verificação coincidam, o bloco de dados não é transferido. Se não corresponder, o bloco de dados será transferido para o local. Esse tempo de transferência depende da largura de banda disponível. A velocidade da soma de verificação é de alguns GBs por minuto. 

Para acelerar o download de dados, você pode configurar seu agente MARS para usar mais threads para paralelizar o download. Consulte o [documento aqui](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) sobre como alterar os threads de download no agente.


## <a name="next-steps"></a>Passos Seguintes

Após a **confirmação**, você pode iniciar a *replicação inversa*. Isso começa a proteger a máquina virtual do local de volta para o Azure. Isso replicará apenas as alterações, uma vez que a VM foi desativada no Azure e, portanto, enviará apenas alterações diferenciais.
