---
title: Instale recuperação de desastres azure VM com recuperação do site Azure
description: Saiba como configurar a recuperação após desastre de VMs do Azure para uma região diferente do Azure, utilizando o serviço Azure Site Recovery.
ms.topic: tutorial
ms.date: 1/24/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c5d2bbe920f87421550fadf30a7e7e9d23931bfd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80292473"
---
# <a name="set-up-disaster-recovery-for-azure-vms"></a>Configurar recuperação de desastres para VMs Azure

O serviço de recuperação de [sítios Azure](site-recovery-overview.md) contribui para a sua estratégia de recuperação de desastres, gerindo e orquestrando replicação, failover e failback de máquinas no local e máquinas virtuais Azure (VMs).

Este tutorial mostra-lhe como configurar a recuperação de desastres para os VMs Azure, replicando-os de uma região de Azure para outra. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um cofre dos Serviços de Recuperação 
> * Verificar as definições dos recursos de destino
> * Criar conectividade de rede de saída para VMs
> * Ativar a replicação para uma VM

> [!NOTE]
> Este artigo fornece instruções para a implementação da recuperação de desastres com as configurações mais simples. Se quiser aprender sobre configurações personalizadas, reveja os artigos na [secção Como Fazer](azure-to-azure-how-to-enable-replication.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Reveja a [arquitetura e os componentes do cenário](concepts-azure-to-azure-architecture.md).
- Reveja os [requisitos](site-recovery-support-matrix-azure-to-azure.md) de suporte antes de começar.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Crie o cofre em qualquer região, exceto na região de origem.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**. Em seguida, selecione **IT & Management Tools** > **Backup and Site Recovery**.
1. Em **Nome,** especifique um nome amigável para identificar o cofre. Se tiver mais do que uma subscrição, selecione a que for adequada.
1. Crie um grupo de recursos ou selecione um existente. Selecione uma região do Azure. Para verificar as regiões suportadas, veja a disponibilidade geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
1. Para aceder ao cofre a partir do painel de instrumentos, selecione **Pin para painel de instrumentos** e, em seguida, selecione **Criar**.

   ![Novo cofre](./media/azure-to-azure-tutorial-enable-replication/new-vault-settings.png)

O cofre novo é adicionado ao **Dashboard** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.

## <a name="verify-target-resource-settings"></a>Verificar as definições dos recursos de destino

Verifique a sua subscrição Azure para a região alvo.

- Verifique se a sua subscrição Azure permite criar VMs na região alvo. Contacte o suporte para ativar a quota necessária.
- Certifique-se de que a sua subscrição dispõe de recursos suficientes para suportar tamanhos vm que correspondam aos seus VMs de origem. A Recuperação do Site escolhe o mesmo tamanho, ou o tamanho mais próximo possível, para o VM alvo.

## <a name="set-up-outbound-network-connectivity-for-vms"></a>Criar conectividade de rede de saída para VMs

Para que a Recuperação do Site funcione como esperado, é necessário modificar a conectividade da rede de saída a partir dos VMs que pretende replicar.

> [!NOTE]
> A Recuperação do Site não suporta o uso de um proxy de autenticação para controlar a conectividade da rede.

### <a name="outbound-connectivity-for-urls"></a>Conectividade de saída para URLs

Se estiver a utilizar um representante de firewall baseado em URL para controlar a conectividade de saída, permita o acesso a estes URLs:

| **URL** | **Detalhes** |
| ------- | ----------- |
| `*.blob.core.windows.net` | Permite que os dados sejam escritos da VM para a conta de armazenamento em cache na região de origem. |
| `login.microsoftonline.com` | Fornece autorização e autenticação para os URLs do serviço Site Recovery. |
| `*.hypervrecoverymanager.windowsazure.com` | Permite que a VM comunique com o serviço Site Recovery. |
| `*.servicebus.windows.net` | Permite que a VM escreva dados de monitorização e diagnóstico do Site Recovery. |

### <a name="outbound-connectivity-for-ip-address-ranges"></a>Conectividade de saída para intervalos de endereços IP

Se estiver a utilizar um grupo de segurança de rede (NSG), crie regras nsg baseadas em etiquetas de serviço para acesso ao Armazenamento Azure, Diretório Ativo Azure, serviço de Recuperação de Sites e monitorização de Recuperação do Site. [Saiba mais](azure-to-azure-about-networking.md#outbound-connectivity-using-service-tags).

## <a name="verify-azure-vm-certificates"></a>Verificar os certificados de VM do Azure

Verifique se os VMs que pretende replicar têm os mais recentes certificados de raiz. Se não o fizerem, o VM não pode ser registado na Recuperação do Local devido a restrições de segurança.

- Para VMs do Windows, instale todas as atualizações mais recentes do Windows na VM, para que todos os certificados de raiz fidedigna fiquem na máquina. Num ambiente desligado, siga o padrão do Windows Update e os processos de atualização de certificados da sua organização.
- Para VMs do Linux, siga as orientações fornecidas pelo distribuidor do Linux, para obter os certificados de raiz fidedigna mais recentes e a lista de revogação de certificados na VM.

## <a name="set-permissions-on-the-account"></a>Definir permissões na conta

O Azure Site Recovery fornece três funções incorporadas para controlar as operações de gestão do Site Recovery.

- **Contribuinte do Site Recovery** - esta função tem todas as permissões necessárias para gerir as operações do Azure Site Recovery num cofre dos Serviços de Recuperação. No entanto, um utilizador com esta função não consegue criar nem eliminar um cofre dos Serviços de Recuperação, nem atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para os administradores de recuperação após desastre que podem ativar e gerir a recuperação após desastre para aplicações ou organizações completas.

- **Operador do Site Recovery** - esta função tem permissões para executar e gerir operações de Ativação Pós-falha e Reativação Pós-falha. Um utilizador com esta função não consegue ativar nem desativar a replicação, criar ou eliminar cofres, registar uma nova infraestrutura ou atribuir direitos de acesso a outros utilizadores. Esta função é mais adequada para um operador de recuperação após desastre que pode efetuar a ativação pós-falha de máquinas virtuais ou aplicações quando indicado pelos proprietários da aplicação e administradores de TI. Após a resolução do desastre, o operador de recuperação de desastres pode reproteger e falhar as máquinas virtuais.

- **Leitor do Site Recovery** - esta função tem permissões para ver todas as operações de gestão do Site Recovery. Esta função é mais adequada para um executivo de monitorização de TI que pode monitorizar o estado atual da proteção e emitir pedidos de suporte.

Saiba mais sobre [as funções integradas do Azure RBAC.](../role-based-access-control/built-in-roles.md)

## <a name="enable-replication-for-a-vm"></a>Ativar a replicação para uma VM

As seguintes secções descrevem como permitir a replicação.

### <a name="select-the-source"></a>Selecionar a origem

Para iniciar a configuração da replicação, escolha a fonte onde os seus VMs Azure estão em execução.

1. Vá aos **cofres dos Serviços de Recuperação,** selecione o nome do cofre e, em seguida, selecione **+Replicate**.
1. Para a **Fonte,** selecione **Azure**.
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

1. Para anular as definições de destino predefinidas, selecione **Personalizar** ao lado do **grupo de Recursos, Rede, Armazenamento e Disponibilidade**.

   ![Configurar definições](./media/azure-to-azure-tutorial-enable-replication/settings.png)

1. Personalize as definições do alvo como resumidas na tabela.

   | **Definição** | **Detalhes** |
   | --- | --- |
   | **Subscrição de alvo** | Por predefinição, a subscrição do alvo é a mesma que a subscrição de origem. Selecione **Personalizar** para selecionar uma subscrição de alvo diferente dentro do mesmo inquilino do Azure Ative Directory. |
   | **Localização de destino** | A região alvo usada para a recuperação de desastres.<br/><br/> Recomendamos que a localização de destino corresponda à localização do cofre do Site Recovery. |
   | **Grupo de recursos-alvo** | O grupo de recursos na região alvo que detém VMs Azure após a falha.<br/><br/> Por padrão, a Recuperação do Site cria `asr` um novo grupo de recursos na região alvo com um sufixo. A localização do grupo de recursos alvo pode ser qualquer região, exceto a região em que as suas máquinas virtuais de origem estão hospedadas. |
   | **Rede virtual alvo** | A rede na região alvo que os VMs estão localizados após o fracasso.<br/><br/> Por padrão, a Recuperação do Site cria uma nova rede `asr` virtual (e subnets) na região alvo com um sufixo. |
   | **Contas de armazenamento de cache** | A Recuperação do Site utiliza uma conta de armazenamento na região de origem. As alterações às VMs de origem são enviadas para esta conta antes da replicação para a localização de destino.<br/><br/> Se estiver a utilizar uma conta de armazenamento de cache ativada por firewall, certifique-se de que ativa os **serviços fidedignos da Microsoft**. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions). Além disso, certifique-se de que permite o acesso a pelo menos uma sub-rede da fonte Vnet. |
   | **Contas de armazenamento-alvo (fonte VM usa discos não geridos)** | Por padrão, a Recuperação do Site cria uma nova conta de armazenamento na região alvo para espelhar a conta de armazenamento VM fonte.<br/><br/> Ativar **permitir serviços fidedignos da Microsoft** se estiver a utilizar uma conta de armazenamento de cache ativada por firewall. |
   | **Réplica de discos geridos (Se a fonte VM utilizar discos geridos)** | Por padrão, a Recuperação do Site cria discos geridos de réplica na região alvo para espelhar os discos geridos pela VM de origem com o mesmo tipo de armazenamento (padrão ou premium) que o disco gerido pela VM de origem. Só é possível personalizar o tipo de Disco. |
   | **Conjuntos de disponibilidade de destino** | Por padrão, a Recuperação do Site Azure cria uma `asr` nova disponibilidade definida na região alvo com nome tendo sufixo para os VMs parte de um conjunto de disponibilidade definido na região de origem. Caso já exista um conjunto de disponibilidade criado pela Azure Site Recovery, é reutilizado. |
   | **Zonas de disponibilidade de destino** | Por predefinição, a Recuperação do Local atribui o mesmo número de zona que a região-alvo na região alvo se a região alvo apoiar as zonas de disponibilidade.<br/><br/> Se a região alvo não suportar zonas de disponibilidade, os VMs-alvo são configurados como instâncias únicas por padrão.<br/><br/> Selecione **Personalizar** para configurar VMs como parte de um conjunto de disponibilidade na região alvo.<br/><br/> Não é possível alterar o tipo de disponibilidade (instância única, conjunto de disponibilidade ou zona de disponibilidade) depois de ativar a replicação. Para alterar o tipo de disponibilidade, desative e ativa a replicação. |

1. Para personalizar as definições de política de replicação, selecione **Personalizar** ao lado da política de **replicação**e modificar as definições conforme necessário.

   | **Definição** | **Detalhes** |
   | --- | --- |
   | **Nome da política de replicação** | Nome da apólice. |
   | **Retenção de pontos de recuperação** | Por padrão, a Recuperação do Site mantém os pontos de recuperação durante 24 horas. Pode configurar um valor entre 1 e 72 horas. |
   | **Frequência instantânea consistente com aplicativos** | Por padrão, a Recuperação do Site tira uma imagem consistente com aplicações a cada 4 horas. Pode configurar qualquer valor entre 1 e 12 horas.<br/><br/> Um instantâneo consistente com aplicações é uma imagem pontual dos dados da aplicação dentro do VM. O Serviço de Cópia Sombra de Volumes (VSS) garante que a aplicação está num estado consistente quando se obtém o instantâneo. |
   | **Grupo de replicação** | Se a sua aplicação necessitar de consistência multi-VM em VMs, pode criar um grupo de replicação para esses VMs. Por predefinição, as VMs selecionadas não fazem parte de qualquer grupo de replicação. |

1. Em **Customize**, selecione **Sim** para consistência multi-VM se quiser adicionar VMs a um novo ou existente grupo de replicação. Em seguida, selecione **OK**.

   > [!NOTE]
   > - Todas as máquinas de um grupo de replicação partilharam pontos de recuperação consistentes e consistentes com aplicações quando falharam.
   > - Permitir a consistência multi-VM pode ter impacto no desempenho da carga de trabalho (é intensivo de CPU). Só deve ser utilizado se as máquinas estiverem a funcionar com a mesma carga de trabalho, e precisar de consistência em várias máquinas.
   > - Pode ter um máximo de 16 VMs num grupo de replicação.
   > - Se ativar a consistência multi-VM, as máquinas no grupo de replicação comunicam entre si pela porta 20004. Certifique-se de que não há firewall bloqueando a comunicação interna entre os VMs sobre esta porta.
   > - Para os VMs Linux num grupo de replicação, certifique-se de que o tráfego de saída no porto 20004 é aberto manualmente de acordo com a orientação para a versão Linux.

### <a name="configure-encryption-settings"></a>Configurar as definições de encriptação

Se a fonte VM tiver encriptação de disco Azure (ADE) ativada, reveja as definições.

1. Verifique as definições:
   1. **Cofres**de chave de encriptação de disco : Por padrão, a Recuperação do `asr` Site cria um novo cofre chave nas teclas de encriptação do disco VM de origem, com um sufixo. Se o cofre já existe, é reutilizado.
   1. **Cofres chave de encriptação**: Por padrão, a Recuperação do Site cria um novo cofre chave na região alvo. O nome `asr` tem um sufixo, e é baseado nas chaves de encriptação da chave VM de origem. Se o cofre de chaves criado pela Recuperação do Site já existe, é reutilizado.
1. Selecione **Personalizar** para selecionar cofres de chaves personalizados.

> [!NOTE]
> Apenas os VMs Azure que executam sistemas operativos Windows e [estão habilitados para encriptação com a aplicação Azure AD](https://aka.ms/ade-aad-app) são atualmente suportados pela Azure Site Recovery.

### <a name="track-replication-status"></a>Controlar o estado de replicação

Após a replicação ser ativada, pode rastrear o estado do trabalho.

1. Em **Definições,** selecione **Refresh** para obter o estado mais recente.
1. Acompanhe o progresso e o estado da seguinte forma:
   1. Acompanhe o progresso do trabalho de **proteção enable** em **Configurações** > **Empregos** > **de Recuperação**de Locais .
   1. Em **Definições** > **Itens Replicados,** pode visualizar o estado dos VMs e o progresso inicial da replicação. Selecione o VM para perfurar as suas definições.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, configurou a recuperação após desastre para uma VM do Azure. Agora pode executar um exercício de recuperação de desastres para verificar se o fracasso funciona como esperado.

> [!div class="nextstepaction"]
> [Executar um teste de recuperação após desastre](azure-to-azure-tutorial-dr-drill.md)
