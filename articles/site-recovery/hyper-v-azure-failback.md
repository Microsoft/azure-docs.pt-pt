---
title: Executar uma reativação pós-falha durante desastre de VMs de Hyper-v do Azure no local | Documentos da Microsoft
description: Saiba como a falha de volta as VMs de Hyper-V para um site no local durante a recuperação após desastre para o Azure com o serviço Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 4030b1905f8d5b50ef6be3ffa61eda74d8a27951
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60552421"
---
# <a name="run-a-failback-for-hyper-v-vms"></a>Executar uma reativação pós-falha para VMs de Hyper-V

Este artigo descreve como efetuar a ativação de máquinas de virtuais de back Hyper-V protegidas pelo Site Recovery.

## <a name="prerequisites"></a>Pré-requisitos
1. Certifique-se de que leu os detalhes sobre o [diferentes tipos de reativação pós-falha](concepts-types-of-failback.md) e advertências correspondentes.
1. Certifique-se de que o servidor VMM do site primário ou um servidor de anfitrião do Hyper-V está ligado ao Azure.
2. Deve ter realizado **consolidar** na máquina virtual.

## <a name="perform-failback"></a>Realizar a reativação pós-falha
Após a ativação pós-falha dos principais para a localização secundária, máquinas virtuais replicadas não estão protegidas pelo Site Recovery e a localização secundária agora está atuando como o Active Directory local. Para efetuar a ativação pós-falha de VMs num plano de recuperação, execute uma ativação pós-falha planeada do site secundário para o primário, da seguinte forma. 
1. Selecione **planos de recuperação** > *recoveryplan_name*. Clique em **ativação pós-falha** > **ativação pós-falha planeada**.
2. Sobre o **confirmar a ativação de pós-falha planeadas** página, selecione as localizações de origem e de destino. Tenha em atenção a direção de ativação pós-falha. Se a ativação pós-falha do primário funcionou como esperado e todas as máquinas virtuais estão na localização secundária, que isso é apenas para informação.
3. Se estiver a efetuar a ativação pós-falha do Azure, selecione as definições no **sincronização de dados**:
    - **Sincronizar os dados antes da ativação pós-falha (sincronizar apenas o alterações de delta)**— esta opção minimiza o tempo de inatividade para as máquinas virtuais como sincronizá-lo sem encerrá-los. Ele faz as seguintes etapas:
        - Fase 1: Tira o instantâneo da máquina virtual no Azure e copia-o para o anfitrião de Hyper-V no local. A máquina continua em execução no Azure.
        - Fase 2: Encerra a máquina virtual no Azure, para que não existem novas alterações ocorrem aqui. O conjunto final das alterações delta é transferido para o servidor no local e a máquina de virtual no local é iniciada.

    - **Sincronizar os dados durante a ativação pós-falha apenas (transferência completa)**— esta opção é mais rápida.
        - Esta opção é mais rápida porque podemos esperar que a maioria do disco foi alterado e não queremos passar um tempo no cálculo da soma de verificação. Ele executa uma transferência do disco. Também é útil quando a máquina de virtual no local foi eliminada.
        - Recomendamos que utilize esta opção se esteve executando Azure há algum tempo (um mês ou mais) ou a máquina de virtual no local foi eliminada. Esta opção não realiza quaisquer cálculos de soma de verificação.


4. Se a encriptação de dados estiver ativada para a nuvem, no **chave de encriptação** selecione o certificado que foi emitido quando ativou a encriptação de dados durante a instalação do fornecedor no servidor do VMM.
5. Iniciar a ativação pós-falha. Pode seguir o progresso da ativação pós-falha no separador **Trabalhos**.
6. Se tiver selecionado a opção para sincronizar os dados antes da ativação pós-falha, assim que a sincronização de dados inicial estiver concluída e está pronto para encerrar as máquinas virtuais no Azure, clique em **trabalhos** > nome da tarefa >  **Concluir ativação pós-falha**. Isso encerra a máquina do Azure, transfere as alterações mais recentes para a máquina de virtual no local e inicia a VM no local.
7. Pode agora inicie sessão na máquina virtual para validar está disponível como esperado.
8. A máquina virtual está no estado de confirmação pendente. Clique em **consolidação** para consolidar a ativação pós-falha.
9. Para concluir a reativação pós-falha, clique em **inverter replicar** para começar a proteger a máquina virtual no site primário.


Siga estes procedimentos para efetuar a ativação pós-falha para o site primário original. Este procedimento descreve como executar uma ativação pós-falha para um plano de recuperação. Em alternativa pode executar a ativação pós-falha para uma única máquina virtual no **máquinas virtuais** separador.


## <a name="failback-to-an-alternate-location-in-hyper-v-environment"></a>Reativação pós-falha para uma localização alternativa no ambiente de Hyper-V
Se implementou a proteção entre uma [site de Hyper-V e o Azure](site-recovery-hyper-v-site-to-azure.md) tem capacidade para reativação pós-falha do Azure para uma localização alternativa no local. Isto é útil se precisar de configurar o novo hardware no local. Eis como fazê-lo.

1. Se estiver a configurar novos hardwares instale o Windows Server 2012 R2 e a função Hyper-V no servidor.
2. Crie um comutador de rede virtual com o mesmo nome que tinha no servidor original.
3. Selecione **itens protegidos** -> **grupo de proteção** -> \<ProtectionGroupName > -> \<VirtualMachineName > que pretende efetuar a reativação pós-falha, e selecione **ativação pós-falha planeada**.
4. Na **confirmar a ativação de pós-falha planeadas** selecionar **criar na máquina virtual local se não existir**.
5. No nome de anfitrião, * * selecione o novo servidor de anfitrião de Hyper-V no qual pretende colocar a máquina virtual.
6. Sincronização de dados, recomendamos que selecionar a opção para sincronizar os dados antes da ativação pós-falha. Isso minimiza o tempo de inatividade para as máquinas virtuais como sincronizá-lo sem encerrá-los. Ele faz o seguinte:

    - Fase 1: Tira o instantâneo da máquina virtual no Azure e copia-o para o anfitrião de Hyper-V no local. A máquina continua em execução no Azure.
    - Fase 2: Encerra a máquina virtual no Azure, para que não existem novas alterações ocorrem aqui. O conjunto final de alterações é transferido para o servidor no local e a máquina de virtual no local é iniciada.
    
7. Clique na marca de verificação para iniciar a ativação pós-falha (reativação pós-falha).
8. Depois de concluída a sincronização inicial e está pronto para encerrar a máquina virtual no Azure, clique em **trabalhos** > \<tarefa de ativação pós-falha planeada >> **concluir a ativação pós-falha** . Isso encerra a máquina do Azure, transfere as alterações mais recentes para a máquina de virtual no local e inicia-o.
9. Pode iniciar sessão na máquina virtual no local para verificar se que está tudo a funcionar conforme esperado. Em seguida, clique em **consolidar** para concluir a ativação pós-falha. Consolidar elimina a máquina virtual do Azure e os respetivos discos e prepara a VM a ser protegida novamente.
10. Clique em **inverter replicar** para começar a proteger a máquina de virtual no local.

    > [!NOTE]
    > Se cancelar a tarefa de reativação pós-falha embora seja no passo de sincronização de dados, a VM no local vai estar num estado danificado. Isto acontece porque a sincronização de dados copia os dados mais recentes de discos de VM do Azure para os discos de dados no local e, até que a sincronização seja concluída, os dados do disco poderão não estar num estado consistente. Se a VM no local é iniciada após a sincronização de dados é cancelada, não pode arrancar. Reacionar a ativação pós-falha para concluir a sincronização de dados.


## <a name="why-is-there-no-button-called-failback"></a>Por que motivo não existe nenhum botão chamado reativação pós-falha?
No portal, não há nenhum gesto explícito chamado reativação pós-falha. Reativação pós-falha é um passo em que volte para o site primário. Por definição, a reativação pós-falha é quando fizer a ativação pós-falha de máquinas virtuais de recuperação volta para principal.

Quando iniciar uma ativação pós-falha, o painel informa-o sobre a direção em que as máquinas virtuais está a ser movido, se estiver a direção do Azure para o local, é uma reativação pós-falha.

## <a name="why-is-there-only-a-planned-failover-gesture-to-failback"></a>Por que há apenas um gesto de ativação pós-falha planeada para reativação pós-falha?
O Azure é um ambiente de elevada disponibilidade e suas máquinas virtuais estão sempre disponíveis. Reativação pós-falha é uma atividade em planeadas em que optar por adotar um pequeno período de indisponibilidade para que as cargas de trabalho podem começar a executar novamente no local. Isso espera sem perda de dados. Por conseguinte, está disponível apenas um gesto de ativação pós-falha planeada, que irá desativar as VMs no Azure, transferir as alterações mais recentes e certifique-se de que existe sem perda de dados.

## <a name="do-i-need-a-process-server-in-azure-to-failback-to-hyper-v"></a>É necessário um servidor de processos no Azure para reativação pós-falha para o Hyper-v?
Não, um servidor de processos é necessário apenas quando estiver a proteger máquinas virtuais VMware. Não existem componentes adicionais são necessários sejam implementados ao proteger/reativação pós-falha de máquinas virtuais Hyper-v.


## <a name="time-taken-to-failback"></a>Tempo decorrido para a reativação pós-falha
O tempo necessário para concluir a sincronização de dados e a máquina virtual de arranque depende de vários fatores. Para dar uma visão geral de tempo decorrido, vamos explicar o que acontece durante a sincronização de dados.

Sincronização de dados cria um instantâneo de discos da máquina virtual e inicia a verificação bloco a bloco e calcula a soma de verificação. Isso calculado a soma de verificação é enviada para o local para comparar com a soma de verificação no local do bloco do mesmo. No caso das somas de verificação corresponderem, o bloco de dados não é transferido. Se não houver, o bloco de dados é transferido para o local. Neste momento de transferência depende da largura de banda disponível. A velocidade da soma de verificação é a alguns GB por min. 

Para acelerar a transferência de dados, pode configurar o agente de MARS para utilizar mais threads para paralelizar o download. Consulte a [documentar aqui](https://support.microsoft.com/en-us/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage) sobre como alterar os threads de transferência no agente.


## <a name="next-steps"></a>Próximos Passos

Após **consolidar**, pode iniciar o *replicar inverso*. Esta ação inicia a proteger a máquina virtual no local para o Azure. Isto só irá replicar as alterações, uma vez que a VM foi desativada no Azure e envia, por conseguinte, apenas as alterações diferenciais.
