---
title: Ativação pós-falha e reativação pós-falha de servidores físicos para recuperação após desastre para o Azure com o Site Recovery | Documentos da Microsoft
description: Saiba como efetuar a ativação pós-falha de servidores físicos no Azure e reativação pós-falha para o site no local para a recuperação após desastre com o Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: raynew
ms.openlocfilehash: edb169d131aafd045fdf0f670e1dda87677d57ee
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57834682"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Ativação pós-falha e reativação pós-falha de servidores físicos replicados no Azure

Este tutorial descreve como fazer a ativação pós-falha de um servidor físico para o Azure. Depois que tive a ativação pós-falha, falhar o servidor de volta para o seu site no local quando estiver disponível.

## <a name="preparing-for-failover-and-failback"></a>Preparar a ativação pós-falha e a reativação pós-falha

Servidores físicos replicados no Azure com o Site Recovery só podem falhar novamente como VMs de VMware. Terá de uma infraestrutura do VMware para a reativação pós-falha.

A ativação pós-falha e a reativação pós-falha têm quatro fases:

1. **Efetuar a ativação pós-falha para o Azure**: Pós-falha de máquinas do site no local para o Azure.
2. **Voltar a proteger VMs do Azure**: Voltar a proteger as VMs do Azure, para que comecem a ser replicadas novamente para VMs de VMware no local.
3. **Efetuar a ativação pós-falha no local**: Execute uma ativação pós-falha, reativação pós-falha do Azure.
4. **Voltar a proteger VMs no local**: Depois de dados falhou, novamente, voltar a proteger as VMs do VMware no local que falharam ao, para que comecem a ser replicadas para o Azure.

## <a name="verify-server-properties"></a>Verifique se as propriedades do servidor

Verifique se as propriedades do servidor e certifique-se de que ele está em conformidade com [requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines) para VMs do Azure.

1. Na **itens protegidos**, clique em **itens replicados**e selecione a máquina.

2. Na **item replicado** painel, há um resumo das informações da máquina, o estado de funcionamento, e de pontos de recuperação mais recentes disponíveis. Clique em **Propriedades** para ver mais detalhes.
3. Na **computação e rede**, pode modificar o nome do Azure, o grupo de recursos, o tamanho de destino, [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)e as definições de disco gerido
4. Pode ver e modificar as definições de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após a ativação pós-falha e o endereço IP que será atribuído à mesma.
5. Na **discos**, pode ver informações sobre o sistema operativo da máquina e os discos de dados.

## <a name="run-a-failover-to-azure"></a>Executar uma ativação pós-falha para o Azure

1. Em **Definições** > **Itens replicados**, clique no computador > **Ativação Pós-falha**.
2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual fazer a ativação pós-falha. Pode utilizar uma das opções seguintes:
   - **Mais recente**: Esta opção processa primeiro todos os dados enviados para o Site Recovery. Disponibiliza o último RPO (Objetivo de Ponto de Recuperação), porque a VM do Azure criada após a ativação pós-falha tem todos os dados que foram replicados para o Site Recovery quando a ativação pós-falha foi acionada.
   - **Processado mais recentemente**: Esta opção faz a ativação pós-falha da máquina do ponto de recuperação mais recente processado pelo Site Recovery. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
   - **Mais recente consistente com a aplicação**: Esta opção faz a ativação pós-falha da máquina para o ponto mais recente recuperação consistente com a aplicação processado pelo Site Recovery.
   - **Custom**: Especifique um ponto de recuperação.

3. Selecione **encerrar a máquina antes de iniciar a ativação pós-falha** se pretender que o Site Recovery para tentar encerrar a máquina de origem antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.
4. Se preparou para ligar à VM do Azure, ligue para validá-la após a ativação pós-falha.
5. Depois de verificar, **consolide** a ativação pós-falha. São eliminados todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancele uma ativação pós-falha em curso. Antes do início da ativação pós-falha, deixa de replicação da máquina. Se cancelar a ativação pós-falha, parar, mas a máquina não replicar novamente.
> Para servidores físicos, processamento de ativação pós-falha adicional pode demorar cerca de oito a dez minutos para concluir.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Se quiser ligar a VMs do Azure através de RDP/SSH após a ativação pós-falha, siga os requisitos resumidos na tabela [aqui](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Siga os passos descritos [aqui](site-recovery-failover-to-azure-troubleshoot.md) para resolver quaisquer problemas de conectividade após a ativação pós-falha.

## <a name="create-a-process-server-in-azure"></a>Criar um servidor de processos no Azure

O servidor de processos recebe dados da VM do Azure e envia-os para o site no local. Uma rede de baixa latência é necessária entre o servidor de processos e a máquina protegida.

- Para fins de teste, se tiver uma ligação do Azure ExpressRoute, pode utilizar o servidor de processos no local que é instalado automaticamente no servidor de configuração.
- Se tiver uma ligação VPN ou se estiver a executar a reativação pós-falha num ambiente de produção, tem de configurar uma VM do Azure como um servidor de processos baseado no Azure para reativação pós-falha.
- Siga as instruções em [este artigo](vmware-azure-set-up-process-server-azure.md) para configurar um servidor de processos no Azure.

## <a name="configure-the-master-target-server"></a>Configurar o servidor de destino mestre

Por predefinição, o servidor de destino mestre recebe dados de reativação pós-falha. Ele é executado no servidor de configuração no local.

- Se estiver a VM de VMware para o qual efetuar a reativação pós-falha num anfitrião ESXi gerido pelo servidor vCenter do VMware, o servidor de destino principal tem de ter acesso ao arquivo de dados da VM (VMDK), para escrever os dados replicados para os discos da VM. Certifique-se de que o arquivo de dados da VM está montado no anfitrião do servidor de destino mestre, com acesso de leitura/escrita.
- Se o anfitrião do ESXi que não seja gerido por um vCenter server, o serviço Site Recovery cria uma nova VM durante a nova proteção. A VM é criada no anfitrião ESX no qual criou a VM de destino mestre. O disco rígido da VM tem de estar num arquivo de dados que seja acessível pelo anfitrião no qual o servidor de destino principal está em execução.
- Para máquinas físicas que efetuar a reativação pós-falha, deve concluir a deteção do anfitrião no qual o servidor de destino mestre está em execução, antes de poder voltar a proteger a máquina.
- Outra opção, se a VM no local já existe para a reativação pós-falha, é eliminá-lo antes de fazer uma reativação pós-falha. A reativação pós-falha cria, em seguida, uma VM nova no mesmo anfitrião do anfitrião ESX do servidor de destino mestre. Quando fizer a reativação pós-falha para uma localização alternativa, os dados são recuperados para o mesmo arquivo de dados e para o mesmo anfitrião ESX que foi utilizado pelo servidor de destino mestre no local.
- Não pode utilizar o Storage vMotion no servidor de destino mestre. Se o fizer, a reativação pós-falha não funcionará, porque os discos não estão disponíveis para a mesma. Exclua os servidores de destino mestre da sua lista do vMotion.

## <a name="reprotect-azure-vms"></a>Voltar a proteger VMs do Azure

Este procedimento parte do princípio de que a VM no local não está disponível e que está a proteger novamente para uma localização alternativa.

1. Em **Definições** > **Itens replicados**, clique com o botão direito do rato na VM da qual foi feita a ativação pós-falha > **Voltar a proteger**.
2. Em **Voltar a proteger**, confirme que está selecionado **Do Azure para o local**.
3. Especifique o servidor de destino mestre no local e o servidor de processos.

4. Em **Arquivo de dados**, selecione o arquivo de dados de destino mestre para o qual pretende recuperar os discos no local. Utilize esta opção se a VM no local tiver sido eliminada e for preciso criar discos novos. Esta definição é ignorada se os discos já existirem, mas tem de especificar um valor.
5. Selecione a unidade de retenção de destino mestre. A política de reativação pós-falha é selecionada automaticamente.
6. Clique em **OK** para começar a nova proteção. Um trabalho começa a replicar a máquina virtual do Azure para o site no local. Pode acompanhar o progresso no separador **Trabalhos**.

> [!NOTE]
> Se quiser recuperar a VM do Azure para uma VM no local já existente, monte o arquivo de dados da máquina virtual no local com acesso de leitura/escrita no anfitrião ESXi do servidor de destino mestre.


## <a name="run-a-failover-from-azure-to-on-premises"></a>Executar uma ativação pós-falha do Azure para o local

Para replicar novamente para o local, é utilizada uma política de reativação pós-falha. Esta política é criada automaticamente quando criou uma política de replicação para replicação para o Azure:

- A política é associada automaticamente ao servidor de configuração.
- A política não pode ser modificada.
- Os valores da política são:
    - Limiar de RPO = 15 minutos
    - Retenção de ponto de recuperação = 24 horas
    - Frequência de instantâneos consistentes com a aplicação = 60 minutos

Execute a ativação pós-falha da seguinte forma:

1. Na página **Itens Replicados**, clique com o botão direito do rato na máquina > **Ativação pós-falha não planeada**.
2. Em **Confirmar Ativação Pós-falha**, confirme que a direção da ativação pós-falha provém do Azure.

3. Selecione o ponto de recuperação que pretende utilizar para a ativação pós-falha. Um ponto de recuperação consistente com a aplicação ocorre antes do ponto no tempo mais recente, o que provoca alguma perda de dados. Quando a ativação pós-falha é executada, o Site Recovery encerra as VMs do Azure e arranca a VM no local. Haverá algum tempo de inatividade, pelo que deve escolher uma hora adequada.
4. Clique com o botão direito do rato na máquina e clique em **Consolidar**. É acionado um trabalho que remove as VMs do Azure.
5. Confirme que as VMs do Azure foram encerradas, conforme esperado.


## <a name="reprotect-on-premises-machines-to-azure"></a>Voltar a proteger máquinas no local para o Azure

Os dados devem estar agora outra vez no site no local, mas não estão a ser replicados para o Azure. Pode iniciar a replicação para o Azure novamente da seguinte forma:

1. Em cofre > **Definições** >**Itens Replicados**, selecione as VMs das quais foi feita reativação pós-falha e clique em **Voltar a Proteger**.
2. Selecione o servidor de processos que é utilizado para enviar os dados replicados para o Azure e clique em **OK**.

Após a conclusão da nova proteção, a VM é replicada outra vez para o Azure e pode executar uma ativação pós-falha, conforme necessário.
