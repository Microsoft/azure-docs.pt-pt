---
title: Configurar a recuperação após desastre de VMs do Azure para uma região secundária do Azure com o Azure Site Recovery
description: Saiba como configurar a recuperação após desastre de VMs do Azure para uma região diferente do Azure, utilizando o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 08/05/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: e46bc9e4fbb2b573338b8be43c38e658ebde05a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468020"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Configurar a recuperação de desastre para VMs do Azure

O serviço de [Azure site Recovery](site-recovery-overview.md) contribui para sua estratégia de recuperação de desastres Gerenciando e orquestrando a replicação, o failover e o failback de máquinas locais e VMs (máquinas virtuais) do Azure.

Este tutorial mostra como configurar a recuperação de desastre para VMs do Azure, replicando-as de uma região do Azure para outra. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação
> * Verificar as definições dos recursos de destino
> * Configurar a conectividade de rede de saída para VMs
> * Ativar replicação para uma VM

> [!NOTE]
> Este artigo fornece instruções para implantar a recuperação de desastre com as configurações mais simples. Se você quiser saber mais sobre as configurações personalizadas, examine os artigos na [seção como](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Reveja a [arquitetura e os componentes do cenário](concepts-azure-to-azure-architecture.md).
- Examine os [requisitos de suporte](site-recovery-support-matrix-azure-to-azure.md) antes de começar.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Crie o cofre em qualquer região, exceto na região de origem.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. No menu portal do Azure ou na **Home** Page do, selecione **criar um recurso**. Em seguida, selecione **ferramentas de gerenciamento** > **backup e site Recovery**.
3. Em **Nome**, especifique um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione a que for adequada.
4. Crie um grupo de recursos ou selecione um existente. Selecione uma região do Azure. Para verificar as regiões suportadas, veja a disponibilidade geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
5. Para aceder rapidamente ao cofre a partir do dashboard, clique em **Afixar ao dashboard** e clique em **Criar**.

   ![Novo cofre](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

   O cofre novo é adicionado ao **Dashboard** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="verify-target-resource-settings"></a>Verificar as definições dos recursos de destino

1. Verifique se sua assinatura do Azure permite que você crie VMs na região de destino. Contacte o suporte para ativar a quota necessária.
2. Verifique se sua assinatura tem recursos suficientes para dar suporte a tamanhos de VM que correspondam às suas VMs de origem. Site Recovery escolhe o mesmo tamanho, ou o tamanho mais próximo possível, para a VM de destino.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Configurar a conectividade de rede de saída para VMs

Para que Site Recovery funcionem conforme o esperado, você precisa modificar a conectividade de rede de saída das VMs que deseja replicar.

> [!NOTE]
> O Site Recovery não dá suporte ao uso de um proxy de autenticação para controlar a conectividade de rede.


### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se você estiver usando um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a essas URLs.

| **URL** | **Detalhes** |
| ------- | ----------- |
| *.blob.core.windows.net | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| login.microsoftonline.com | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| *.hypervrecoverymanager.windowsazure.com | Permite que a VM comunique com o serviço Site Recovery. |
| *.servicebus.windows.net | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Se você quiser controlar a conectividade de saída usando endereços IP em vez de URLs, permita esses endereços para firewalls, proxy ou regras NSG baseadas em IP.

  - [Intervalos IP do Datacenter do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653)
  - [Intervalos de IP do Datacenter do Windows Azure na Alemanha](https://www.microsoft.com/download/details.aspx?id=54770)
  - [Intervalos de IP do Datacenter do Windows Azure na China](https://www.microsoft.com/download/details.aspx?id=42064)
  - [Intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2#bkmk_identity)
  - [Endereços IP do ponto final do serviço Site Recovery](https://aka.ms/site-recovery-public-ips)

Se você estiver usando o NSG, poderá criar uma marca de serviço de armazenamento regras de NSG para a região de origem. [Saiba mais](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges).

## <a name="verify-azure-vm-certificates"></a>Verificar os certificados de VM do Azure

Verifique se as VMs que você deseja replicar têm os certificados raiz mais recentes. Se não for, a VM não poderá ser registrada em Site Recovery, devido a restrições de segurança.

- Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna fiquem na máquina. Num ambiente desligado, siga o padrão do Windows Update e os processos de atualização de certificados da sua organização.
- Para VMs do Linux, siga as orientações fornecidas pelo distribuidor do Linux, para obter os certificados de raiz fidedigna mais recentes e a lista de revogação de certificados na VM.

## <a name="set-permissions-on-the-account"></a>Definir permissões na conta

O Azure Site Recovery fornece três funções incorporadas para controlar as operações de gestão do Site Recovery.

- **Contribuinte do Site Recovery** - esta função tem todas as permissões necessárias para gerir as operações do Azure Site Recovery num cofre dos Serviços de Recuperação. No entanto, um utilizador com esta função não consegue criar nem eliminar um cofre dos Serviços de Recuperação, nem atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para os administradores de recuperação após desastre que podem ativar e gerir a recuperação após desastre para aplicações ou organizações completas.

- **Operador do Site Recovery** - esta função tem permissões para executar e gerir operações de Ativação Pós-falha e Reativação Pós-falha. Um utilizador com esta função não consegue ativar nem desativar a replicação, criar ou eliminar cofres, registar uma nova infraestrutura ou atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para um operador de recuperação após desastre que pode efetuar a ativação pós-falha de máquinas virtuais ou aplicações quando indicado pelos proprietários da aplicação e administradores de TI. Após a resolução do desastre, o operador de DR pode voltar a proteger e efetuar a reativação pós-falha das máquinas virtuais.

- **Leitor do Site Recovery** - esta função tem permissões para ver todas as operações de gestão do Site Recovery. Esta função é mais adequada para um executivo de monitorização de TI que pode monitorizar o estado atual da proteção e emitir pedidos de suporte.

Saiba mais sobre as [funções internas do RBAC do Azure](../role-based-access-control/built-in-roles.md).

## <a name="enable-replication-for-a-vm"></a>Ativar replicação para uma VM

### <a name="select-the-source"></a>Selecionar a origem

1. Nos cofres dos Serviços de Recuperação, clique no nome do cofre > **+Replicar**.
2. Em **Origem**, selecione **Azure**.
3. Em **Localização de origem**, selecione a região do Azure de origem onde as VMs estão atualmente a ser executadas.
4. Selecione a **Subscrição de origem** onde as máquinas virtuais são executadas. Isto pode ser qualquer subscrição com o mesmo inquilino do Azure Active Directory onde existe o cofre de serviços de recuperação.
5. Selecione o **grupo de recursos de origem**e clique em **OK** para salvar as configurações.

    ![Configurar a origem](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Selecionar as VMs

O Site Recovery obtém uma lista das VMs associadas à subscrição e o grupo de recursos/serviço cloud.

1. Em **Máquinas Virtuais**, selecione as VMs que pretende replicar.
2. Clique em **OK**.

### <a name="configure-replication-settings"></a>Configurar as definições de replicação

O Site Recovery cria as predefinições e a política de replicação para a região de destino. Você pode alterar as configurações conforme necessário.

1. Clique em **Definições** para ver as definições de destino e replicação.
2. Para substituir as configurações de destino padrão, clique em **Personalizar** ao lado de **grupo de recursos, rede, armazenamento e disponibilidade**.

   ![Configurar definições](./media/azure-to-azure-tutorial-enable-replication/settings.png)


3. Personalize as configurações de destino conforme resumido na tabela.

    **Definição** | **Detalhes**
    --- | ---
    **Assinatura de destino** | Por padrão, a assinatura de destino é igual à assinatura de origem. Clique em "Personalizar" para selecionar uma subscrição de destino diferente no mesmo inquilino do Azure Active Directory.
    **Localização de destino** | A região de destino usada para recuperação de desastre.<br/><br/> Recomendamos que a localização de destino corresponda à localização do cofre do Site Recovery.
    **Grupo de recursos de destino** | O grupo de recursos na região de destino que contém as VMs do Azure após o failover.<br/><br/> Por predefinição, o Site Recovery cria um novo grupo de recursos na região de destino com um sufixo "asr". O local do grupo de recursos de destino pode ser qualquer região, exceto a região em que as máquinas virtuais de origem estão hospedadas.
    **Rede virtual de destino** | A rede na região de destino em que as VMs estão localizadas após o failover.<br/><br/> Por predefinição, o Site Recovery cria uma nova rede virtual (e sub-redes) na região de destino com um sufixo "asr".
    **Contas de armazenamento em cache** | Site Recovery usa uma conta de armazenamento na região de origem. As alterações às VMs de origem são enviadas para esta conta antes da replicação para a localização de destino.<br/><br/> Se você estiver usando uma conta de armazenamento de cache habilitada para firewall, certifique-se de habilitar **permitir serviços confiáveis da Microsoft**. [Saiba mais.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)
    **Contas de armazenamento de destino (a VM de origem usa discos não gerenciados)** | Por padrão, Site Recovery cria uma nova conta de armazenamento na região de destino para espelhar a conta de armazenamento da VM de origem.<br/><br/> Habilite **permitir serviços confiáveis da Microsoft** se você estiver usando uma conta de armazenamento de cache habilitada para firewall.
    **Discos gerenciados de réplica (se a VM de origem usar discos gerenciados)** | Por padrão, Site Recovery cria discos gerenciados de réplica na região de destino para espelhar os discos gerenciados da VM de origem com o mesmo tipo de armazenamento (Standard ou Premium) que o disco gerenciado da VM de origem. Você só pode personalizar o tipo de disco 
    **Conjuntos de disponibilidade de destino** | Por padrão, Azure Site Recovery cria um novo conjunto de disponibilidade na região de destino com o sufixo "ASR" para a parte das VMs de um conjunto de disponibilidade na região de origem. Caso o conjunto de disponibilidade criado pelo Azure Site Recovery já exista, ele é reutilizado.
    **Zonas de disponibilidade de destino** | Por padrão, Site Recovery atribui o mesmo número de zona que a região de origem na região de destino se a região de destino oferecer suporte a zonas de disponibilidade.<br/><br/> Se a região de destino não oferecer suporte a zonas de disponibilidade, as VMs de destino serão configuradas como instâncias únicas por padrão.<br/><br/> Clique em **Personalizar** para configurar as VMs como parte de um conjunto de disponibilidade na região de destino.<br/><br/> Você não pode alterar o tipo de disponibilidade (instância única, conjunto de disponibilidade ou zona de disponibilidade) depois de habilitar a replicação. Você precisa desabilitar e habilitar a replicação para alterar o tipo de disponibilidade.

4. Para personalizar as configurações de política de replicação, clique em **Personalizar** ao lado de **política de replicação**e modifique as configurações conforme necessário.

    **Definição** | **Detalhes**
    --- | ---
    **Nome da política de replicação** | Nome da política.
    **Retenção do ponto de recuperação** | Por padrão, Site Recovery mantém os pontos de recuperação por 24 horas. Pode configurar um valor entre 1 e 72 horas.
    **Frequência de instantâneo consistente com o aplicativo** | Por padrão, Site Recovery usa um instantâneo consistente com o aplicativo a cada 4 horas. Pode configurar qualquer valor entre 1 e 12 horas.<br/><br/> Um instantâneo consistente com o aplicativo é um instantâneo pontual dos dados do aplicativo dentro da VM. O Serviço de Cópia Sombra de Volumes (VSS) garante que a aplicação está num estado consistente quando se obtém o instantâneo.
    **Grupo de replicação** | Se seu aplicativo precisar de consistência de várias VMs entre VMs, você poderá criar um grupo de replicação para essas VMs. Por predefinição, as VMs selecionadas não fazem parte de qualquer grupo de replicação.

5. Em **Personalizar**, selecione **Sim** para consistência de várias VMs se você quiser adicionar VMs a um grupo de replicação novo ou existente. Em seguida, clique em **OK**. 

    >[!NOTE]
    >- Todos os computadores em um grupo de replicação têm os pontos de recuperação consistentes com a falha compartilhada e com o aplicativo durante o failover.
    >- A habilitação da consistência de várias VMs pode afetar o desempenho da carga de trabalho (é intensiva na CPU). Ele deve ser usado somente se os computadores estiverem executando a mesma carga de trabalho e você precisar de consistência em vários computadores.
    >- Você pode ter um máximo de 16 VMs em um grupo de replicação.
    >- Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004. Certifique-se de que não há firewall bloqueando a comunicação interna entre as VMs nessa porta.
    >- Para VMs do Linux em um grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 seja aberto manualmente de acordo com as diretrizes para a versão do Linux.



### <a name="configure-encryption-settings"></a>Configurar as definições de encriptação

Se a VM de origem tiver o ADE (Azure Disk Encryption) habilitado, examine as configurações.

1. Verifique as configurações:
    - **Cofres de chaves de criptografia de disco**: por padrão, site Recovery cria um novo cofre de chaves nas chaves de criptografia de disco de VM de origem, com um sufixo "ASR". Se o cofre de chaves já existir, ele será reutilizado.
    - **Cofres de chave de criptografia de chave**: por padrão, site Recovery cria um novo cofre de chaves na região de destino. O nome tem um sufixo "ASR" e é baseado nas chaves de criptografia de chave de VM de origem. Se o cofre de chaves criado pelo Site Recovery já existir, ele será reutilizado.

2. Clique em **Personalizar** para selecionar cofres de chaves personalizados.

>[!NOTE]
>Somente as VMs do Azure que executam sistemas operacionais Windows e [habilitados para criptografia com o aplicativo do Azure ad](https://aka.ms/ade-aad-app) têm suporte no momento por Azure site Recovery.
>

### <a name="track-replication-status"></a>Controlar o estado de replicação

1. Em **Definições**, clique em **Atualizar** para obter o estado mais recente.
2. Acompanhe o progresso e o status da seguinte maneira:
    - Acompanhe o progresso do trabalho **habilitar proteção** em **configurações** > **trabalhos** > **trabalhos de site Recovery**.
    - Em **Definições** > **Itens Replicados**, pode ver o estado das VMs e o progresso da replicação inicial. Clique na VM para desagregar as respetivas definições.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou a recuperação após desastre para uma VM do Azure. Agora você pode iniciar uma análise de recuperação de desastre para verificar se o failover está funcionando conforme o esperado.

> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](azure-to-azure-tutorial-dr-drill.md)
