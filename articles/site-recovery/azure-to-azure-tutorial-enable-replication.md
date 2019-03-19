---
title: Configurar a recuperação após desastre de VMs do Azure para uma região secundária do Azure com o Azure Site Recovery
description: Saiba como configurar a recuperação após desastre de VMs do Azure para uma região diferente do Azure, utilizando o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 03/12/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: ff18a14b314b5757629205f4bf0eb134411688ec
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57853135"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Configurar a recuperação após desastre para VMs do Azure

O [do Azure Site Recovery](site-recovery-overview.md) serviço contribui para a sua estratégia de recuperação após desastre através da gestão e orquestração de replicação, ativação pós-falha e reativação pós-falha de máquinas no local e máquinas virtuais do Azure (VMs).

Este tutorial mostra-lhe como configurar a recuperação após desastre para VMs do Azure, replicando-os partir de uma região do Azure para outra. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação 
> * Verificar as definições dos recursos de destino
> * Configurar o acesso de saída das VMs
> * Ativar replicação para uma VM

> [!NOTE]
> Este artigo fornece instruções para implementar a recuperação após desastre com as definições mais simples. Se quiser saber mais sobre as definições personalizadas, consulte os artigos sob o [como a seção](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Certifique-se de que compreende a [arquitetura e os componentes do cenário](concepts-azure-to-azure-architecture.md).
- Reveja a [suportar requisitos](site-recovery-support-matrix-azure-to-azure.md) antes de começar.

## <a name="create-a-vault"></a>Criar um cofre

Crie o cofre em qualquer região, exceto na região de origem.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **criar um recurso** > **ferramentas de gestão** > **cópia de segurança e recuperação de Site**.
3. Em **Nome**, especifique um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione a que for adequada.
4. Crie um grupo de recursos ou selecione um existente. Selecione uma região do Azure. Para verificar as regiões suportadas, veja a disponibilidade geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Para aceder rapidamente ao cofre a partir do dashboard, clique em **Afixar ao dashboard** e clique em **Criar**.

   ![Novo cofre](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   O cofre novo é adicionado ao **Dashboard** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="verify-target-resources"></a>Verificar recursos de destino

1. Certifique-se de que a sua subscrição do Azure permite-lhe criar VMs na região de destino. Contacte o suporte para ativar a quota necessária.
2. Certifique-se de que a sua subscrição tem recursos suficientes para suportar tamanhos VM que correspondem às VMs de origem. O site Recovery escolhe o mesmo tamanho ou o tamanho mais próximo possível, para a VM de destino.

## <a name="configure-outbound-network-connectivity"></a>Configurar a conectividade de rede de saída

Para o Site Recovery funcionar conforme esperado, terá de modificar a conectividade de rede de saída das VMs que pretende replicar.

> [!NOTE]
> Site Recovery não suporta a utilizar um proxy de autenticação para controlar a conectividade de rede.


### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se estiver a utilizar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes URLs.

| **URL** | **Detalhes** |
| ------- | ----------- |
| *.blob.core.windows.net | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| login.microsoftonline.com | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permite que a VM comunique com o serviço Site Recovery. |
| *.servicebus.windows.net | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Se quiser controlar a conectividade de saída com os endereços IP em vez de URLs, que estes endereços para firewalls baseados em IP, o proxy ou as regras do NSG.

  - [Intervalos IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653)
  - [Intervalos de IP do Datacenter do Windows Azure na Alemanha](https://www.microsoft.com/download/details.aspx?id=54770)
  - [Intervalos de IP do Datacenter do Windows Azure na China](https://www.microsoft.com/download/details.aspx?id=42064)
  - [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Endereços IP do ponto final do serviço Site Recovery](https://aka.ms/site-recovery-public-ips)

Pode utilizá-lo [script](https://gallery.technet.microsoft.com/Azure-Recovery-script-to-0c950702) para criar as regras do NSG necessárias.

## <a name="verify-azure-vm-certificates"></a>Verificar os certificados de VM do Azure

Certifique-se de que as VMs que pretende replicar tem os certificados de raiz mais recentes. Caso contrário a VM não é possível registar no Site Recovery, devido a restrições de segurança.

- Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna fiquem na máquina. Num ambiente desligado, siga o padrão do Windows Update e os processos de atualização de certificados da sua organização.
- Para VMs do Linux, siga as orientações fornecidas pelo distribuidor do Linux, para obter os certificados de raiz fidedigna mais recentes e a lista de revogação de certificados na VM.

## <a name="set-permissions-on-the-account"></a>Definir permissões na conta

O Azure Site Recovery fornece três funções incorporadas para controlar as operações de gestão do Site Recovery.

- **Contribuinte do Site Recovery** - esta função tem todas as permissões necessárias para gerir as operações do Azure Site Recovery num cofre dos Serviços de Recuperação. No entanto, um utilizador com esta função não consegue criar nem eliminar um cofre dos Serviços de Recuperação, nem atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para os administradores de recuperação após desastre que podem ativar e gerir a recuperação após desastre para aplicações ou organizações completas.

- **Operador do Site Recovery** - esta função tem permissões para executar e gerir operações de Ativação Pós-falha e Reativação Pós-falha. Um utilizador com esta função não consegue ativar nem desativar a replicação, criar ou eliminar cofres, registar uma nova infraestrutura ou atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para um operador de recuperação após desastre que pode efetuar a ativação pós-falha de máquinas virtuais ou aplicações quando indicado pelos proprietários da aplicação e administradores de TI. Após a resolução do desastre, o operador de DR pode voltar a proteger e efetuar a reativação pós-falha das máquinas virtuais.

- **Leitor do Site Recovery** - esta função tem permissões para ver todas as operações de gestão do Site Recovery. Esta função é mais adequada para um executivo de monitorização de TI que pode monitorizar o estado atual da proteção e emitir pedidos de suporte.

Saiba mais sobre [funções incorporadas do RBAC do Azure](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication"></a>Ativar a replicação

### <a name="select-the-source"></a>Selecionar a origem

1. Nos cofres dos Serviços de Recuperação, clique no nome do cofre > **+Replicar**.
2. Em **Origem**, selecione **Azure**.
3. Em **Localização de origem**, selecione a região do Azure de origem onde as VMs estão atualmente a ser executadas.
4. Selecione a **Subscrição de origem** onde as máquinas virtuais são executadas. Isto pode ser qualquer subscrição com o mesmo inquilino do Azure Active Directory onde existe o cofre de serviços de recuperação.
5. Selecione o **grupo de recursos de origem**e clique em **OK** para guardar as definições.

    ![Configurar a origem](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Selecionar as VMs

O Site Recovery obtém uma lista das VMs associadas à subscrição e o grupo de recursos/serviço cloud.

1. Em **Máquinas Virtuais**, selecione as VMs que pretende replicar.
2. Clique em **OK**.

### <a name="configure-replication-settings"></a>Configurar as definições de replicação

O Site Recovery cria as predefinições e a política de replicação para a região de destino. Pode alterar as definições conforme necessário.

1. Clique em **Definições** para ver as definições de destino e replicação.
2. Para substituir as predefinições de destino, clique em **personalizar** junto a **grupo de recursos, rede, armazenamento e disponibilidade**.

   ![Configurar definições](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Personalize as definições de destino, conforme resumido na tabela.

    **Definição** | **Detalhes**
    --- | ---
    **Subscrição de destino** | Por predefinição, a subscrição de destino é o mesmo que a subscrição de origem. Clique em "Personalizar" para selecionar uma subscrição de destino diferente no mesmo inquilino do Azure Active Directory.
    **Localização de destino** | Região de destino utilizada para recuperação após desastre.<br/><br/> Recomendamos que a localização de destino corresponda à localização do cofre do Site Recovery.
    **Grupo de recursos de destino** | O grupo de recursos na região de destino que contém as VMs do Azure após a ativação pós-falha.<br/><br/> Por predefinição, o Site Recovery cria um novo grupo de recursos na região de destino com um sufixo "asr". A localização do grupo de recursos de destino pode ser qualquer região, exceto a região na qual as máquinas de virtuais de origem estão alojadas.
    **Rede virtual de destino** | A rede na região de destino que estão localizadas as VMs após a ativação pós-falha.<br/><br/> Por predefinição, o Site Recovery cria uma nova rede virtual (e sub-redes) na região de destino com um sufixo "asr".
    **Contas de armazenamento em cache** | Site Recovery utiliza uma conta de armazenamento na região de origem. As alterações às VMs de origem são enviadas para esta conta antes da replicação para a localização de destino.<br/><br/> Se estiver a utilizar uma conta de armazenamento de cache de firewall ativada, certifique-se de que ativa **permitir confiável a serviços da Microsoft**. [Saiba mais.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
    **Contas de armazenamento de destino (a VM de origem utilizar discos não geridos)** | Por predefinição, o Site Recovery cria uma nova conta de armazenamento na região de destino para espelhar a conta de armazenamento VM de origem.<br/><br/> Ativar **permitir confiável a serviços da Microsoft** se estiver a utilizar uma conta de armazenamento de cache de firewall ativada.
    **Discos de geridos de réplica (se a origem de que VM utiliza discos geridos)** | Por predefinição, o Site Recovery cria discos geridos de réplica na região de destino para espelhar os discos geridos da VM de origem com o mesmo tipo de armazenamento (Standard ou premium), como a VM de origem de disco gerido.
    **Conjuntos de disponibilidade de destino** | Por predefinição, o Azure Site Recovery cria uma novo conjunto de disponibilidade na região de destino com um nome que o sufixo "asr" para a VMs que fazem parte de um conjunto de disponibilidade na região de origem. No caso de conjunto de disponibilidade criado pelo Azure Site Recovery já existe, este é reutilizado.
    **Zonas de disponibilidade de destino** | Por predefinição, o Site Recovery atribui o mesmo número de zona e a região de origem na região de destino se a região de destino suporta zonas de disponibilidade.<br/><br/> Se a região de destino não suporta zonas de disponibilidade, as VMs de destino estão configuradas como instâncias únicas por predefinição.<br/><br/> Clique em **personalizar** para configurar as VMs como parte de um conjunto de disponibilidade na região de destino.<br/><br/> Não é possível alterar o tipo de disponibilidade (instância única, zona de disponibilidade ou conjunto de disponibilidade) depois de ativar a replicação. Terá de desativar e ativar a replicação alterar o tipo de disponibilidade.

4. Para personalizar as definições de política de replicação, clique em **personalizar** junto a **política de replicação**e modifique as definições conforme necessário.

    **Definição** | **Detalhes**
    --- | ---
    **Nome da política de replicação** | Nome da política.
    **Retenção do ponto de recuperação** | Por predefinição, o Site Recovery mantém os pontos de recuperação durante 24 horas. Pode configurar um valor entre 1 e 72 horas.
    **Frequência de instantâneo consistente da aplicação** | Por predefinição, o Site Recovery tira um instantâneo consistente com a aplicação a cada 4 horas. Pode configurar qualquer valor entre 1 e 12 horas.<br/><br/> Um instantâneo consistente com a aplicação é um instantâneo de ponto no tempo dos dados de aplicação no interior da VM. O Serviço de Cópia Sombra de Volumes (VSS) garante que a aplicação está num estado consistente quando se obtém o instantâneo.
    **Grupo de replicação** | Se a sua aplicação precisar de consistência multi-VM entre VMs, pode criar um grupo de replicação para essas VMs. Por predefinição, as VMs selecionadas não fazem parte de qualquer grupo de replicação.

5. Na **personalizar**, selecione **Sim** por razões de consistência de várias VMS, se pretender adicionar VMs a um grupo de replicação de novo ou existente. Em seguida, clique em **OK**. 

    >[!NOTE]
    >- Todas as máquinas num grupo de replicação tem partilhado pontos de recuperação de consistente e consistente com a aplicação de falhas quando a ativação pós-falha.
    >- Ativar a consistência multi-VM pode afetar o desempenho de carga de trabalho (é exigente em termos de CPU). Ele deve ser usado apenas se os computadores estão executando a mesma carga de trabalho e precisar de consistência entre várias máquinas.
    >- Pode ter um máximo de 16 VMs num grupo de replicação.
    >- Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004. Certifique-se de que não existe nenhum firewall a bloquear a comunicação interna entre as VMs através desta porta.
    >- Para VMs do Linux num grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 é aberto manualmente de acordo com as orientações para a versão do Linux.



### <a name="configure-encryption-settings"></a>Configurar as definições de encriptação

Se a VM de origem tem a encriptação de disco do Azure (ADE) ativada, reveja as definições.

1. Verifique as definições:
    - **Cofres de chaves de encriptação de disco**: Por predefinição, o Site Recovery cria um novo cofre de chaves nas chaves de encriptação de disco do VM de origem, com um sufixo "asr". Se o Cofre de chaves já existir, este é reutilizado.
    - **Cofres de chaves de encriptação da chave**: Por predefinição, o Site Recovery cria um novo cofre de chaves na região de destino. O nome tem um sufixo "asr" e baseia-se as chaves de encriptação de chave de VM de origem. Se o Cofre de chaves criado pelo Site Recovery já existir, este é reutilizado.

2. Clique em **personalizar** para selecionar os cofres de chaves personalizados.

>[!NOTE]
>Apenas as VMs do Azure com sistemas de operativos do Windows e [ativada para a encriptação com a aplicação do Azure AD](https://aka.ms/ade-aad-app) são atualmente suportadas pelo Azure Site Recovery.
>

### <a name="track-replication-status"></a>Controlar o estado de replicação

1. Em **Definições**, clique em **Atualizar** para obter o estado mais recente.
2. Controle o progresso e o estado da seguinte forma:
    - Controlar o progresso dos **ativar a proteção** da tarefa na **definições** > **tarefas** > **tarefas do Site Recovery**.
    - Em **Definições** > **Itens Replicados**, pode ver o estado das VMs e o progresso da replicação inicial. Clique na VM para desagregar as respetivas definições.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou a recuperação após desastre para uma VM do Azure. Agora pode iniciar um teste de recuperação após desastre para verificar que a ativação pós-falha está a funcionar conforme esperado.

> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](azure-to-azure-tutorial-dr-drill.md)
