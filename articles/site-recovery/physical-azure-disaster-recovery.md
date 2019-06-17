---
title: Configurar a recuperação de desastres para o Azure para servidores físicos no local com o Azure Site Recovery | Documentos da Microsoft
description: Saiba como configurar a recuperação após desastre para o Azure para servidores de Windows e Linux no local, com o serviço Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: c3b9aa6fcf5cf96e3ef1f3bdd76e9f1d19be5c5c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66400108"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Configurar a recuperação após desastre para o Azure para servidores físicos no local

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para a estratégia de recuperação após desastre, através da gestão e orquestração de replicação, ativação pós-falha e reativação pós-falha de máquinas no local e máquinas virtuais (VMs) do Azure.

Este tutorial mostra-lhe como configurar a recuperação após desastre de servidores no local físicos Windows e Linux para o Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar os pré-requisitos do Azure e no local
> * Criar um cofre dos Serviços de Recuperação para o Site Recovery 
> * Configurar a origem e ambientes de replicação de destino
> * Criar uma política de replicação
> * Ativar a replicação para um servidor

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Certifique-se de que compreende os [arquitetura e componentes](physical-azure-architecture.md) para este cenário.
- Reveja os [requisitos de suporte](vmware-physical-secondary-support-matrix.md) de todos os componentes.
- Certifique-se de que estão em conformidade com os servidores que pretende replicar [requisitos de VM do Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Prepare o Azure. Precisa de uma subscrição do Azure, uma rede virtual do Azure e uma conta de armazenamento.
- Prepare uma conta para a instalação automática do serviço de mobilidade em cada servidor que pretende replicar.

Antes de começar, tenha em atenção que:

- Após a ativação pós-falha para o Azure, servidores físicos não podem falhar novamente para computadores físicos no local. Apenas poderá reativar para VMs de VMware. 
- Este tutorial define-se a recuperação de desastres do servidor físico para o Azure com as definições mais simples. Se quiser saber mais sobre outras opções, leia nossa guias de procedimentos:
    - Configurar o [origem de replicação](physical-azure-set-up-source.md), incluindo o servidor de configuração do Site Recovery.
    - Configure o [destino da replicação](physical-azure-set-up-target.md).
    - Configure uma [política de replicação](vmware-azure-set-up-replication.md) e [ative a replicação](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Configurar uma conta do Azure

Obter um Microsoft [conta do Azure](https://azure.microsoft.com/).

- Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Saiba mais sobre [os preços da recuperação de Site](site-recovery-faq.md#pricing)e obtenha [os detalhes dos preços](https://azure.microsoft.com/pricing/details/site-recovery/).
- Descubra quais [regiões são suportadas](https://azure.microsoft.com/pricing/details/site-recovery/) para o Site Recovery.

### <a name="verify-azure-account-permissions"></a>Verifique as permissões de conta do Azure

Certifique-se de que a sua conta do Azure tem permissões para a replicação de VMs para o Azure.

- Reveja os [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) precisa de replicar máquinas para o Azure.
- Verifique e modifique [acesso baseado em funções](../role-based-access-control/role-assignments-portal.md) permissões. 



### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Configurar uma [rede do Azure](../virtual-network/quick-create-portal.md).

- VMs do Azure são colocadas na rede quando forem criadas após a ativação pós-falha.
- A rede deve estar na mesma região que o Cofre dos serviços de recuperação


## <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

Configurar uma [conta de armazenamento do Azure](../storage/common/storage-quickstart-create-account.md).

- O site Recovery replica as máquinas no local para o armazenamento do Azure. VMs do Azure são criadas a partir do armazenamento após a ocorrência da ativação pós-falha.
- A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de mobilidade tem de ser instalado em cada servidor que pretende replicar. Site Recovery instala este serviço automaticamente quando ativa a replicação para o servidor. Para instalar automaticamente, terá de preparar uma conta que o Site Recovery irá utilizar para aceder ao servidor.

- Pode utilizar um domínio ou conta local
- Para VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o controlo de acesso de utilizador remoto na máquina local. Para tal, no registo em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy**, com um valor de 1.
- Para adicionar a entrada de registo para desativar a definição de uma CLI, escreva:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para o Linux, a conta deve ser a raiz no servidor Linux de origem.


## <a name="create-a-vault"></a>Criar um cofre

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Selecionar um objetivo de proteção

Selecione o que, para replicar e replicá-lo para.

1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu de Recursos, clique em **Site Recovery** > **Preparar Infraestrutura** > **Objetivo de proteção**.
3. Na **objetivo de proteção**, selecione **para o Azure** > **não virtualizado/outro**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Configurar o servidor de configuração, registe-o no cofre e detetar VMs.

1. Clique em **recuperação de sites** > **preparar infraestrutura** > **origem**.
2. Se não tiver um servidor de configuração, clique em **+ servidor de configuração**.
3. Na **Adicionar servidor**, verifique se **servidor de configuração** aparece no **tipo de servidor**.
4. Transfira o ficheiro de instalação do Site Recovery Unified Setup.
5. Transferir a chave de registo do cofre. Precisará disto quando executar a configuração unificada. A chave é válida durante cinco dias depois de gerá-la.

   ![Configurar a origem](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registar o servidor de configuração no Cofre

Faça o seguinte antes de começar: 

#### <a name="verify-time-accuracy"></a>Certifique-se de precisão de hora
Na máquina do servidor de configuração, certifique-se de que o relógio do sistema está sincronizado com um [servidor de horas](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Deve ser igual. Se se trata de 15 minutos na frente ou por trás, a configuração poderá falhar.

#### <a name="verify-connectivity"></a>Verificar a conectividade
Certifique-se que a máquina pode aceder estes URLs com base no seu ambiente: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Regras de firewall baseadas no endereço IP devem permitir a comunicação a todos os URLs do Azure que estão listadas acima através da porta HTTPS (443). Para simplificar e limitar a intervalos de IP, recomenda-se que a filtragem de URLs ser feito.

- **IPs comerciais** -permitir que o [intervalos de IP de Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e a porta HTTPS (443). Permitir que os intervalos de endereços IP para a região do Azure da sua subscrição para suportar o AAD, a cópia de segurança, a replicação e a URLs de armazenamento.  
- **Governo IPs** -permitir que o [intervalos de IP do Azure Government Datacenter](https://www.microsoft.com/en-us/download/details.aspx?id=57063)e a porta HTTPS (443) para todas as regiões de USGov (Virginia, Texas, Arizona e Iowa) para suportar o AAD, cópia de segurança, replicação e URLs de armazenamento.  

#### <a name="run-setup"></a>Executar a configuração
Execute a configuração unificada como administrador Local, para instalar o servidor de configuração. O servidor de processos e o servidor de destino mestre também são instalados por predefinição no servidor de configuração.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Após a conclusão do registo, o servidor de configuração é apresentado no **configurações** > **servidores** página no cofre.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino.

1. Clique em **Preparar a infraestrutura** > **Destino** e selecione a subscrição do Azure que pretende utilizar.
2. Especifique o modelo de implementação de destino.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Destino](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Para criar uma nova política de replicação, clique em **Infraestrutura do Site Recovery** > **Políticas de Replicação** >  **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limiar RPO**, especifique o limite do objetivo de ponto de recuperação (RPO). Este valor Especifica a frequência com que são criados pontos de recuperação de dados. Será gerado um alerta se a replicação contínua exceder este limite.
4. Em **Retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela. É suportada uma retenção de até 24 horas para máquinas replicadas para o armazenamento premium e até 72 horas para armazenamento standard.
5. Na **frequência de instantâneos consistentes com a aplicação**, especifique com que frequência (em minutos) pontos de recuperação que contêm instantâneos consistentes com aplicações serão criados. Clique em **OK** para criar a política.

    ![Política de replicação](./media/physical-azure-disaster-recovery/replication-policy.png)


A política é associada automaticamente ao servidor de configuração. Por predefinição, uma política correspondente é criada automaticamente para reativação pós-falha. Por exemplo, se a política de replicação for **rep-policy** , em seguida, uma política de reativação pós-falha **rep-policy-failback** é criado. Esta política não é utilizada depois de iniciar uma reativação pós-falha a partir do Azure.

## <a name="enable-replication"></a>Ativar a replicação

Ative a replicação para cada servidor.

- Recuperação de site irá instalar o serviço de mobilidade quando a replicação está ativada.
- Quando ativa a replicação para um servidor, pode demorar 15 minutos ou mais tempo para que as alterações entrem em vigor e são apresentados no portal.

1. Clique em **Replicar aplicação** > **Origem**.
2. Em **Origem**, selecione o servidor de configuração.
3. Na **tipo de máquina**, selecione **máquinas físicas**.
4. Selecione o servidor de processos (o servidor de configuração). Em seguida, clique em **OK**.
5. Na **destino**, selecione a subscrição e o grupo de recursos no qual pretende criar as VMs do Azure após a ativação pós-falha. Escolha o modelo de implementação que pretende utilizar no Azure (clássico ou gestão de recursos).
6. Selecione a conta de armazenamento do Azure que pretende utilizar para replicar os dados. 
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se vão ligar quando forem criadas após a ativação pós-falha.
8. Selecione **Configurar agora para as máquinas selecionadas** para aplicar a definição de rede a todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina. 
9. Na **máquinas físicas**e clique em **+ computador**. Especifique o nome e endereço IP. Selecione o sistema operativo da máquina que pretende replicar. Demora alguns minutos para que os servidores sejam detetadas e listados. 
10. Na **propriedades** > **configurar propriedades**, selecione a conta que será utilizada pelo servidor de processos para instalar automaticamente o serviço de mobilidade na máquina.
11. Em **Definições de replicação** > **Configurar as definições de replicação**, certifique-se de que a política de replicação correta está selecionada. 
12. Clique em **Ativar Replicação**. Pode controlar o progresso da tarefa **Ativar Proteção** em **Definições** > **Tarefas** > **Tarefas do Site Recovery**. Depois da tarefa **Finalizar Proteção** ser executada, a máquina está preparada para ativação pós-falha.


Para monitorizar os servidores que adicionar, pode verificar a última hora de deteção na **servidores de configuração** > **último contacto em**. Para adicionar máquinas sem esperar por um período de deteção agendada, realce o servidor de configuração (não clique nele) e clique em **atualizar**.

## <a name="next-steps"></a>Passos Seguintes

[Executar um teste de recuperação após desastre](tutorial-dr-drill-azure.md).
