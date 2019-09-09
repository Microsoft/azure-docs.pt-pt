---
title: Configurar a recuperação de desastres para o Azure para servidores físicos locais com Azure Site Recovery
description: Saiba como configurar a recuperação de desastres para o Azure para servidores Windows e Linux locais, com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 55b375c1e98518a6c3bc2926030cfe072963216c
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70814549"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Configurar a recuperação de desastres para o Azure para servidores físicos locais

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para a estratégia de recuperação após desastre, através da gestão e orquestração de replicação, ativação pós-falha e reativação pós-falha de máquinas no local e máquinas virtuais (VMs) do Azure.

Este tutorial mostra como configurar a recuperação de desastre de servidores Windows e Linux físicos locais para o Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar os pré-requisitos do Azure e locais
> * Criar um cofre dos Serviços de Recuperação para o Site Recovery 
> * Configurar os ambientes de replicação de origem e de destino
> * Criar uma política de replicação
> * Habilitar a replicação para um servidor

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Verifique se você entendeu a [arquitetura e os componentes](physical-azure-architecture.md) para esse cenário.
- Reveja os [requisitos de suporte](vmware-physical-secondary-support-matrix.md) de todos os componentes.
- Verifique se os servidores que você deseja replicar estão em conformidade com [os requisitos de VM do Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Prepare o Azure. Você precisa de uma assinatura do Azure, de uma rede virtual do Azure e de uma conta de armazenamento.
- Prepare uma conta para a instalação automática do serviço de mobilidade em cada servidor que você deseja replicar.

Antes de começar, observe que:

- Após o failover para o Azure, os servidores físicos não podem fazer failback para computadores físicos locais. Você só pode fazer failback para VMs VMware. 
- Este tutorial configura a recuperação de desastres do servidor físico para o Azure com as configurações mais simples. Se você quiser saber mais sobre outras opções, Leia nossos guias de instruções:
    - Configure a [origem de replicação](physical-azure-set-up-source.md), incluindo o servidor de configuração site Recovery.
    - Configure o [destino da replicação](physical-azure-set-up-target.md).
    - Configure uma [política de replicação](vmware-azure-set-up-replication.md) e [ative a replicação](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Configurar uma conta do Azure

Obtenha uma conta do Microsoft [Azure](https://azure.microsoft.com/).

- Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Saiba mais sobre [preços de site Recovery](site-recovery-faq.md#pricing)e obtenha detalhes de [preços](https://azure.microsoft.com/pricing/details/site-recovery/).
- Descubra quais [regiões têm suporte](https://azure.microsoft.com/pricing/details/site-recovery/) para site Recovery.

### <a name="verify-azure-account-permissions"></a>Verificar permissões de conta do Azure

Verifique se sua conta do Azure tem permissões para replicação de VMs para o Azure.

- Examine as [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) necessárias para replicar computadores no Azure.
- Verifique e modifique as permissões [de acesso baseado em função](../role-based-access-control/role-assignments-portal.md) . 



### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Configure uma [rede do Azure](../virtual-network/quick-create-portal.md).

- As VMs do Azure são colocadas nessa rede quando são criadas após o failover.
- A rede deve estar na mesma região que o cofre dos serviços de recuperação


## <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

Configure uma [conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md).

- Site Recovery Replica computadores locais para o armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento após a ocorrência do failover.
- A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de mobilidade deve ser instalado em cada servidor que você deseja replicar. Site Recovery instala esse serviço automaticamente quando você habilita a replicação para o servidor. Para instalar automaticamente, você precisa preparar uma conta que Site Recovery será usada para acessar o servidor.

- Você pode usar uma conta local ou de domínio
- Para VMs do Windows, se você não estiver usando uma conta de domínio, desabilite o controle de acesso de usuário remoto no computador local. Para fazer isso, em registrar em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy**, com um valor de 1.
- Para adicionar a entrada do registro para desabilitar a configuração de uma CLI, digite:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para o Linux, a conta deve ser raiz no servidor Linux de origem.


## <a name="create-a-vault"></a>Criar um cofre

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Selecionar um objetivo de proteção

Selecione o que replicar e para replicar para o.

1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu de Recursos, clique em **Site Recovery** > **Preparar Infraestrutura** > **Objetivo de proteção**.
3. Em **meta de proteção**, selecione **para o Azure** > **não virtualizado/outro**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Configure o servidor de configuração, registre-o no cofre e descubra as VMs.

1. Clique em **site Recovery** > **preparar a infraestrutura** > **origem**.
2. Se você não tiver um servidor de configuração, clique em **+ servidor de configuração**.
3. Em **Adicionar servidor**, verifique se o **servidor de configuração** aparece em tipo de **servidor**.
4. Baixe o arquivo de instalação do Site Recovery Unified setup.
5. Transferir a chave de registo do cofre. Isso é necessário quando você executa a instalação unificada. A chave é válida durante cinco dias depois de gerá-la.

   ![Configurar a origem](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrar o servidor de configuração no cofre

Antes de começar, faça o seguinte: 

#### <a name="verify-time-accuracy"></a>Verificar precisão do tempo
No computador do servidor de configuração, verifique se o relógio do sistema está sincronizado com um [servidor de horário](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Ele deve corresponder. Se for 15 minutos na frente ou atrás, a instalação poderá falhar.

#### <a name="verify-connectivity"></a>Verificar conectividade
Verifique se o computador pode acessar essas URLs com base em seu ambiente: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

As regras de firewall baseadas em endereço IP devem permitir a comunicação com todas as URLs do Azure listadas acima na porta HTTPS (443). Para simplificar e limitar os intervalos de IP, é recomendável que a filtragem de URL seja feita.

- **IPS comerciais** – permitir os [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e a porta HTTPS (443). Permitir intervalos de endereços IP para a região do Azure da sua assinatura para dar suporte às URLs do AAD, backup, replicação e armazenamento.  
- **IPS do governo** -permita os [intervalos de IP do datacenter do Azure governamental](https://www.microsoft.com/en-us/download/details.aspx?id=57063)e a porta HTTPS (443) para todas as regiões USGov (Virgínia, Texas, Arizona e Iowa) para dar suporte ao AAD, backup, replicação e URLs de armazenamento.  

#### <a name="run-setup"></a>Executar a configuração
Execute a instalação unificada como um administrador local para instalar o servidor de configuração. O servidor de processo e o servidor de destino mestre também são instalados por padrão no servidor de configuração.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Após a conclusão do registro, o servidor de configuração é exibido na página de **configurações** > de**servidores** no cofre.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino.

1. Clique em **Preparar a infraestrutura** > **Destino** e selecione a subscrição do Azure que pretende utilizar.
2. Especifique o modelo de implantação de destino.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Para criar uma nova política de replicação, clique em **Infraestrutura do Site Recovery** > **Políticas de Replicação** >  **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limiar RPO**, especifique o limite do objetivo de ponto de recuperação (RPO). Esse valor especifica a frequência com que os pontos de recuperação de dados são criados. Será gerado um alerta se a replicação contínua exceder este limite.
4. Em **Retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela. É suportada uma retenção de até 24 horas para máquinas replicadas para o armazenamento premium e até 72 horas para armazenamento standard.
5. Em **frequência de instantâneo consistente com o aplicativo**, especifique com que frequência (em minutos) os pontos de recuperação que contêm instantâneos consistentes com o aplicativo serão criados. Clique em **OK** para criar a política.

    ![Política de replicação](./media/physical-azure-disaster-recovery/replication-policy.png)


A política é associada automaticamente ao servidor de configuração. Por predefinição, uma política correspondente é criada automaticamente para reativação pós-falha. Por exemplo, se a política de replicação for **Rep-Policy** , uma política de failback **Rep-Policy-failback** será criada. Esta política não é utilizada depois de iniciar uma reativação pós-falha a partir do Azure.

## <a name="enable-replication"></a>Ativar replicação

Habilite a replicação para cada servidor.

- Site Recovery instalará o serviço de mobilidade quando a replicação estiver habilitada.
- Quando você habilita a replicação para um servidor, pode levar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no Portal.

1. Clique em **Replicar aplicação** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Em **tipo de computador**, selecione **computadores físicos**.
4. Selecione o servidor de processo (o servidor de configuração). Em seguida, clique em **OK**.
5. Em **destino**, selecione a assinatura e o grupo de recursos no qual você deseja criar as VMs do Azure após o failover. Escolha o modelo de implantação que você deseja usar no Azure (clássico ou gerenciamento de recursos).
6. Selecione a conta de armazenamento do Azure que pretende utilizar para replicar os dados. 
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se vão ligar quando forem criadas após a ativação pós-falha.
8. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede a todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina. 
9. Em **computadores físicos**e clique em **+ computador físico**. Especifique o nome e o endereço IP. Selecione o sistema operacional do computador que você deseja replicar. Leva alguns minutos para que os servidores sejam descobertos e listados. 
10. Em **Propriedades** > **Configurar Propriedades**, selecione a conta que será usada pelo servidor de processo para instalar automaticamente o serviço de mobilidade no computador.
11. Em **Definições de replicação** > **Configurar as definições de replicação**, certifique-se de que a política de replicação correta está selecionada. 
12. Clique em **Ativar Replicação**. Pode controlar o progresso da tarefa **Ativar Proteção** em **Definições** > **Tarefas** > **Tarefas do Site Recovery**. Depois da tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.


Para monitorar os servidores adicionados, você pode verificar a hora da última descoberta para eles em **servidores** > de configuração**último contato em**. Para adicionar computadores sem aguardar um horário de descoberta agendada, realce o servidor de configuração (não clique nele) e clique em **Atualizar**.

## <a name="next-steps"></a>Passos seguintes

[Execute uma análise de recuperação de desastre](tutorial-dr-drill-azure.md).
