---
title: Configurar a recuperação de desastres da Azure VM com recuperação do local de Azure
description: Saiba como configurar a recuperação após desastre de VMs do Azure para uma região diferente do Azure, utilizando o serviço Azure Site Recovery.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 50bf1ec7f21ccbc3a3fa8feaea02e45bd08a158a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87421421"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Preparar a recuperação de desastres para os VMs do Azure

O serviço [de Recuperação do Local Azure](site-recovery-overview.md) contribui para a sua estratégia de recuperação de desastres gerindo e orquestrando replicação, failover e failback de máquinas no local e máquinas virtuais Azure (VMs).

Este tutorial mostra-lhe como configurar a recuperação de desastres para os VMs Azure, replicando-os de uma região de Azure para outra. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação 
> * Verificar as definições dos recursos de destino
> * Configurar a conectividade da rede de saída para os VMs
> * Ativar a replicação para uma VM

> [!NOTE]
> Este artigo fornece instruções para a implementação da recuperação de desastres com as configurações mais simples. Se quiser aprender sobre configurações personalizadas, reveja os artigos na [secção Como Fazer.](azure-to-azure-how-to-enable-replication.md)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Reveja a [arquitetura e os componentes do cenário](./azure-to-azure-architecture.md).
- Reveja os [requisitos de suporte](./azure-to-azure-support-matrix.md) antes de começar.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Crie o cofre em qualquer região, exceto na região de origem.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**. Em seguida, selecione **IT & Management Tools**Backup e Site  >  **Recovery**.
1. Em **Nome,** especifique um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione a que for adequada.
1. Crie um grupo de recursos ou selecione um existente. Selecione uma região do Azure. Para verificar as regiões suportadas, veja a disponibilidade geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Para aceder ao cofre a partir do painel de instrumentos, selecione **Pin para painel de instrumentos** e, em seguida, selecione **Criar**.

   ![Novo cofre](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

O cofre novo é adicionado ao **Dashboard** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="verify-target-resource-settings"></a>Verificar as definições dos recursos de destino

Verifique a sua subscrição do Azure para a região alvo.

- Verifique se a sua subscrição Azure permite criar VMs na região alvo. Contacte o suporte para ativar a quota necessária.
- Certifique-se de que a sua subscrição tem recursos suficientes para suportar tamanhos VM que correspondam aos seus VMs de origem. A Recuperação do Site escolhe o mesmo tamanho, ou o tamanho mais próximo possível, para o VM alvo.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Configurar a conectividade da rede de saída para os VMs

Para que a Recuperação do Site funcione como esperado, é necessário modificar a conectividade da rede de saída a partir dos VMs que pretende replicar.

> [!NOTE]
> A Recuperação do Site não suporta a utilização de um representante de autenticação para controlar a conectividade da rede.

### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se estiver a usar um proxy de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes URLs:

| **Nome**                  | **Comercial**                               | **Administração Pública**                                 | **Descrição** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Armazenamento                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net`              | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| Replicação               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Permite que a VM comunique com o serviço Site Recovery. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Se estiver a utilizar um grupo de segurança de rede (NSG), crie regras NSG baseadas em etiquetas de serviço para acesso ao Azure Storage, Azure Ative Directory, serviço de recuperação de locais e monitorização da recuperação do local. [Saiba mais](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

## <a name="verify-azure-vm-certificates"></a>Verificar os certificados de VM do Azure

Verifique se os VMs que pretende replicar têm os certificados de raiz mais recentes. Se não o fizerem, o VM não pode ser registado na Recuperação do Local devido a restrições de segurança.

- Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna fiquem na máquina. Num ambiente desligado, siga o padrão do Windows Update e os processos de atualização de certificados da sua organização.
- Para VMs do Linux, siga as orientações fornecidas pelo distribuidor do Linux, para obter os certificados de raiz fidedigna mais recentes e a lista de revogação de certificados na VM.

## <a name="set-permissions-on-the-account"></a>Definir permissões na conta

O Azure Site Recovery fornece três funções incorporadas para controlar as operações de gestão do Site Recovery.

- **Contribuinte do Site Recovery** - esta função tem todas as permissões necessárias para gerir as operações do Azure Site Recovery num cofre dos Serviços de Recuperação. No entanto, um utilizador com esta função não consegue criar nem eliminar um cofre dos Serviços de Recuperação, nem atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para os administradores de recuperação após desastre que podem ativar e gerir a recuperação após desastre para aplicações ou organizações completas.

- **Operador do Site Recovery** - esta função tem permissões para executar e gerir operações de Ativação Pós-falha e Reativação Pós-falha. Um utilizador com esta função não consegue ativar nem desativar a replicação, criar ou eliminar cofres, registar uma nova infraestrutura ou atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para um operador de recuperação após desastre que pode efetuar a ativação pós-falha de máquinas virtuais ou aplicações quando indicado pelos proprietários da aplicação e administradores de TI. Após a resolução do desastre, o operador de recuperação de desastres pode reprotetar e falhar as máquinas virtuais.

- **Leitor do Site Recovery** - esta função tem permissões para ver todas as operações de gestão do Site Recovery. Esta função é mais adequada para um executivo de monitorização de TI que pode monitorizar o estado atual da proteção e emitir pedidos de suporte.

Saiba mais sobre [os papéis incorporados do Azure.](../role-based-access-control/built-in-roles.md)

## <a name="enable-replication-for-a-vm"></a>Ativar a replicação para uma VM

As seguintes secções descrevem como permitir a replicação.

### <a name="select-the-source"></a>Selecionar a origem

Para iniciar a configuração da replicação, escolha a fonte onde os seus VMs Azure estão a funcionar.

1. Vá aos **cofres dos Serviços de Recuperação,** selecione o nome do cofre e, em seguida, selecione **+Replicate**.
1. Para a **Fonte**, selecione **Azure**.
1. Em **Localização de origem**, selecione a região do Azure de origem onde as VMs estão atualmente a ser executadas.
1. Selecione a **Subscrição de origem** onde as máquinas virtuais são executadas. Isto pode ser qualquer subscrição com o mesmo inquilino do Azure Active Directory onde existe o cofre de serviços de recuperação.
1. Selecione o **grupo de recursos Source**e selecione **OK** para guardar as definições.

   ![Configurar a origem](./media/azure-to-azure-tutorial-enable-replication/source.png)

### <a name="select-the-vms"></a>Selecionar as VMs

O Site Recovery obtém uma lista das VMs associadas à subscrição e o grupo de recursos/serviço cloud.

1. Em **Máquinas Virtuais**, selecione as VMs que pretende replicar.
1. Selecione **OK**.

### <a name="configure-replication-settings"></a>Configurar as definições de replicação

O Site Recovery cria as predefinições e a política de replicação para a região de destino. Pode alterar estas definições conforme necessário.

1. Selecione **Definições** para visualizar as definições de alvo e replicação.

1. Para anular as definições de alvo predefinidos, **selecione Personalizar** ao lado **do grupo de Recursos, Rede, Armazenamento e Disponibilidade**.

   ![Configurar definições](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Personalize as definições do alvo como resumido na tabela.

   | **Definição** | **Detalhes** |
   | --- | --- |
   | **Assinatura de destino** | Por padrão, a subscrição-alvo é a mesma que a subscrição de origem. **Selecione Personalizar** para selecionar uma subscrição de destino diferente dentro do mesmo inquilino do Azure Ative Directory. |
   | **Localização de destino** | A região-alvo usada para a recuperação de desastres.<br/><br/> Recomendamos que a localização de destino corresponda à localização do cofre do Site Recovery. |
   | **Grupo de recursos-alvo** | O grupo de recursos na região alvo que detém os VMs Azure após o failover.<br/><br/> Por predefinição, a Recuperação do Site cria um novo grupo de recursos na região alvo com um `asr` sufixo. A localização do grupo de recursos-alvo pode ser qualquer região, exceto a região em que as suas máquinas virtuais de origem estão hospedadas. |
   | **Rede virtual de destino** | A rede na região alvo que os VMs estão localizados após a falha.<br/><br/> Por predefinição, a Recuperação do Site cria uma nova rede virtual (e sub-redes) na região alvo com um `asr` sufixo. |
   | **Contas de armazenamento de cache** | A Recuperação do Local utiliza uma conta de armazenamento na região origem. As alterações às VMs de origem são enviadas para esta conta antes da replicação para a localização de destino.<br/><br/> Se estiver a utilizar uma conta de armazenamento de cache ativada por firewall, certifique-se de que ativa **a Permitir serviços microsoft fidedignos**. [Saiba mais](../storage/common/storage-network-security.md#exceptions). Além disso, certifique-se de que permite o acesso a pelo menos uma sub-rede da fonte Vnet. |
   | **Contas de armazenamento-alvo (fonte VM utiliza discos não geridos)** | Por predefinição, a Recuperação do Site cria uma nova conta de armazenamento na região alvo para espelhar a conta de armazenamento VM de origem.<br/><br/> Ativar **Permitir serviços de Microsoft fidedignos** se estiver a utilizar uma conta de armazenamento de cache ativada por firewall. |
   | **Discos geridos por réplica (se a fonte VM utilizar discos geridos)** | Por predefinição, a Recuperação do Site cria discos geridos por réplicas na região alvo para espelhar os discos geridos da origem VM com o mesmo tipo de armazenamento (padrão ou premium) que o disco gerido pela VM de origem. Só é possível personalizar o tipo de disco. |
   | **Conjuntos de disponibilidade de destino** | Por padrão, a Recuperação do Sítio Azure cria uma nova disponibilidade definida na região alvo com o nome a ter `asr` sufixo para os VMs parte de uma disponibilidade definida na região de origem. Caso a disponibilidade criada pela Azure Site Recovery já exista, é reutilizada. |
   | **Zonas de disponibilidade de destino** | Por predefinição, a Recuperação do Sítio atribui o mesmo número de zona que a região-alvo na região-alvo se a região alvo apoiar zonas de disponibilidade.<br/><br/> Se a região alvo não suporta zonas de disponibilidade, os VMs-alvo são configurados como instâncias únicas por padrão.<br/><br/> **Selecione Personalizar** para configurar VMs como parte de uma disponibilidade definida na região alvo.<br/><br/> Não é possível alterar o tipo de disponibilidade (instância única, disponibilidade definida ou zona de disponibilidade) depois de ativar a replicação. Para alterar o tipo de disponibilidade, desative e ative a replicação. |

1. Para personalizar as definições de política de replicação, **selecione Personalize** ao lado da **política de replicação**e modifique as definições conforme necessário.

   | **Definição** | **Detalhes** |
   | --- | --- |
   | **Nome da política de replicação** | Nome da política. |
   | **Retenção de pontos de recuperação** | Por predefinição, a Recuperação do Site mantém os pontos de recuperação durante 24 horas. Pode configurar um valor entre 1 e 72 horas. |
   | **Frequência instantânea consistente com aplicações** | Por predefinição, a Recuperação do Site tira uma imagem consistente a cada 4 horas. Pode configurar qualquer valor entre 1 e 12 horas.<br/><br/> Um instantâneo consistente com aplicações é uma imagem pontual dos dados da aplicação dentro do VM. O Serviço de Cópia Sombra de Volumes (VSS) garante que a aplicação está num estado consistente quando se obtém o instantâneo. |
   | **Grupo de replicação** | Se a sua aplicação necessitar de consistência multi-VM em VMs, pode criar um grupo de replicação para esses VMs. Por predefinição, as VMs selecionadas não fazem parte de qualquer grupo de replicação. |

1. Em **Personalizar**, selecione **Sim** para consistência multi-VM se quiser adicionar VMs a um grupo de replicação novo ou existente. Em seguida, selecione **OK**.

   > [!NOTE]
   > - Todas as máquinas de um grupo de replicação partilharam pontos de recuperação consistentes e consistentes com aplicações quando falharam.
   > - Permitir a consistência multi-VM pode ter impacto no desempenho da carga de trabalho (é intensivo de CPU). Só deve ser utilizado se as máquinas estiverem a funcionar da mesma carga de trabalho e necessitar de consistência em várias máquinas.
   > - Pode ter um máximo de 16 VMs num grupo de replicação.
   > - Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004. Certifique-se de que não há firewall bloqueando a comunicação interna entre os VMs sobre esta porta.
   > - Para os VMs Linux num grupo de replicação, certifique-se de que o tráfego de saída na porta 20004 é aberto manualmente de acordo com as orientações para a versão Linux.

### <a name="configure-encryption-settings"></a>Configurar as definições de encriptação

Se a fonte VM tiver encriptação do disco Azure (ADE) ativada, reveja as definições.

1. Verifique as definições:
   1. **Cofres de chaves de encriptação**do disco : Por padrão, a Recuperação do Site cria um novo cofre chave nas teclas de encriptação do disco VM de origem, com um `asr` sufixo. Se o cofre já existe, é reutilizado.
   1. **Cofres chave de encriptação**chave : Por padrão, a Recuperação do Local cria um novo cofre chave na região alvo. O nome tem um `asr` sufixo, e baseia-se nas chaves de encriptação da chave VM de origem. Se o cofre-chave criado pela Recuperação do Local já existe, é reutilizado.
1. **Selecione Personalizar** para selecionar cofres de chaves personalizados.

>[!NOTE]
> A Recuperação do Site suporta atualmente o ADE, com e sem Diretório Ativo Azure (AAD) para VMs que executam sistemas operativos Windows. Para os sistemas operativos Linux, só suportamos ADE sem AAD. Além disso, para as máquinas que executam ADE 1.1 (sem AAD), os VMs devem estar a utilizar discos geridos. VMs com discos não geridos não são suportados. Se mudar de ADE 0.1 (com AAD) para 1.1, tem de desativar a replicação e ativar a replicação para um VM depois de ativar 1.1.

### <a name="track-replication-status"></a>Controlar o estado de replicação

Depois de a replicação estar ativada, pode rastrear o estado do trabalho.

1. Em **Definições,** selecione **Refresh** para obter o estado mais recente.
1. Acompanhe o progresso e o estado da seguinte forma:
   1. Acompanhe o progresso do trabalho de **proteção de enable** em **Definições**  >  **de**  >  **Empregos Locais de Recuperação de Empregos**.
   1. Em **Definições**  >  **Itens Replicados,** pode ver o estado dos VMs e o progresso da replicação inicial. Selecione o VM para perfurar as suas definições.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou a recuperação após desastre para uma VM do Azure. Agora podes fazer um exercício de recuperação de desastres para verificar se o falhanço funciona como esperado.

> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](azure-to-azure-tutorial-dr-drill.md)
