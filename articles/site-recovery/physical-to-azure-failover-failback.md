---
title: Fazer failover e failback de servidores físicos para recuperação de desastres no Azure com Site Recovery | Microsoft Docs
description: Saiba como fazer failover de servidores físicos para o Azure e failback para o site local para recuperação de desastre com Azure Site Recovery
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 49b61423b33282be7f0ace52c2a164d52ba20314
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814425"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Fazer failover e failback de servidores físicos replicados para o Azure

Este tutorial descreve como fazer failover de um servidor físico para o Azure. Depois de fazer o failover, você falhará no servidor de volta para o site local quando ele estiver disponível.

## <a name="preparing-for-failover-and-failback"></a>Preparar a ativação pós-falha e a reativação pós-falha

Os servidores físicos replicados para o Azure usando Site Recovery só podem realizar failback como VMs VMware. Você precisa de uma infraestrutura do VMware para realizar o failback.

A ativação pós-falha e a reativação pós-falha têm quatro fases:

1. **Failover para o Azure**: Reprovam computadores do site local para o Azure.
2. **Proteger novamente as VMs do Azure**: Proteja novamente as VMs do Azure para que elas comecem a replicar de volta para VMs VMware locais.
3. **Failover para local**: Execute um failover para fazer failback do Azure.
4. **Proteger novamente as VMs locais**: Após o failback dos dados, proteja novamente as VMs do VMware locais para as quais você fez o failback, para que elas comecem a replicar no Azure.

## <a name="verify-server-properties"></a>Verificar Propriedades do servidor

Verifique as propriedades do servidor e certifique-se de que ele esteja em conformidade com [os requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines) para VMs do Azure.

1. Em **itens protegidos**, clique em **itens replicados**e selecione o computador.

2. No painel **Item replicado** , há um resumo das informações do computador, do status de integridade e dos pontos de recuperação mais recentes disponíveis. Clique em **Propriedades** para ver mais detalhes.
3. Em **computação e rede**, você pode modificar o nome do Azure, o grupo de recursos, o tamanho do destino, o [conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)e as configurações de disco gerenciado
4. Pode ver e modificar as definições de rede, incluindo a rede/sub-rede na qual a VM do Azure será localizada após a ativação pós-falha e o endereço IP que será atribuído à mesma.
5. Em **discos**, você pode ver informações sobre o sistema operacional do computador e os discos de dados.

## <a name="run-a-failover-to-azure"></a>Executar uma ativação pós-falha para o Azure

1. Em **Definições** > **Itens replicados**, clique no computador > **Ativação Pós-falha**.
2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual fazer a ativação pós-falha. Pode utilizar uma das opções seguintes:
   - **Mais recente**: Essa opção primeiro processa todos os dados enviados para Site Recovery. Disponibiliza o último RPO (Objetivo de Ponto de Recuperação), porque a VM do Azure criada após a ativação pós-falha tem todos os dados que foram replicados para o Site Recovery quando a ativação pós-falha foi acionada.
   - **Mais recente processado**: Essa opção faz failover do computador para o último ponto de recuperação processado pelo Site Recovery. Esta opção proporciona um RTO (Objetivo de Tempo de Recuperação) baixo, porque não é despendido tempo ao processar os dados não processados.
   - **Consistente com o aplicativo mais recente**: Essa opção faz failover do computador para o ponto de recuperação consistente com o aplicativo mais recente processado pelo Site Recovery.
   - **Personalizado**: Especifique um ponto de recuperação.

3. Selecione **desligar o computador antes do início do failover** se desejar que site Recovery tente desligar o computador de origem antes de disparar o failover. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Trabalhos**.
4. Se preparou para ligar à VM do Azure, ligue para validá-la após a ativação pós-falha.
5. Depois de verificar, **consolide** a ativação pós-falha. São eliminados todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancele um failover em andamento. Antes do início do failover, a replicação da máquina é interrompida. Se você cancelar o failover, ele será interrompido, mas o computador não será replicado novamente.
> Para servidores físicos, o processamento de failover adicional pode levar cerca de oito a dez minutos para ser concluído.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar a ligação para VMs do Azure após a ativação pós-falha

Se quiser ligar a VMs do Azure através de RDP/SSH após a ativação pós-falha, siga os requisitos resumidos na tabela [aqui](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Siga os passos descritos [aqui](site-recovery-failover-to-azure-troubleshoot.md) para resolver quaisquer problemas de conectividade após a ativação pós-falha.

## <a name="create-a-process-server-in-azure"></a>Criar um servidor de processos no Azure

O servidor de processos recebe dados da VM do Azure e envia-os para o site no local. Uma rede de baixa latência é necessária entre o servidor de processo e o computador protegido.

- Para fins de teste, se tiver uma ligação do Azure ExpressRoute, pode utilizar o servidor de processos no local que é instalado automaticamente no servidor de configuração.
- Se tiver uma ligação VPN ou se estiver a executar a reativação pós-falha num ambiente de produção, tem de configurar uma VM do Azure como um servidor de processos baseado no Azure para reativação pós-falha.
- Siga as instruções neste [artigo](vmware-azure-set-up-process-server-azure.md) para configurar um servidor de processo no Azure.

## <a name="configure-the-master-target-server"></a>Configurar o servidor de destino mestre

Por padrão, o servidor de destino Mestre recebe dados de failback. Ele é executado no servidor de configuração local.

- Se a VM do VMware para a qual você realiza o failback estiver em um host ESXi gerenciado pelo VMware vCenter Server, o servidor de destino mestre deverá ter acesso ao armazenamento de dados da VM (VMDK) para gravar nos discos de VM. Certifique-se de que o arquivo de dados da VM está montado no anfitrião do servidor de destino mestre, com acesso de leitura/escrita.
- Se o host ESXi que não é gerenciado por um servidor vCenter, Site Recovery serviço cria uma nova VM durante a reproteção. A VM é criada no host ESX no qual você cria a VM de destino mestre. O disco rígido da VM tem de estar num arquivo de dados que seja acessível pelo anfitrião no qual o servidor de destino principal está em execução.
- Para computadores físicos em que você realiza o failback, você deve concluir a descoberta do host no qual o servidor de destino mestre está em execução, antes de proteger novamente o computador.
- Outra opção, se a VM local já existir para failback, é excluí-la antes de fazer um failback. A reativação pós-falha cria, em seguida, uma VM nova no mesmo anfitrião do anfitrião ESX do servidor de destino mestre. Quando fizer a reativação pós-falha para uma localização alternativa, os dados são recuperados para o mesmo arquivo de dados e para o mesmo anfitrião ESX que foi utilizado pelo servidor de destino mestre no local.
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
