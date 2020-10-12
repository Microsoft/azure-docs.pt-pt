---
title: Configurar a recuperação de desastres de servidores físicos no local com recuperação do site Azure
description: Saiba como configurar a recuperação de desastres para o Azure para os servidores Windows e Linux no local, com o serviço de Recuperação do Site Azure.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 9b05d9952628e550beae5cedc49e051936a9d633
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87927288"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Confiússe de desastre para Azure para servidores físicos no local

O serviço [Azure Site Recovery](site-recovery-overview.md) contribui para a estratégia de recuperação após desastre, através da gestão e orquestração de replicação, ativação pós-falha e reativação pós-falha de máquinas no local e máquinas virtuais (VMs) do Azure.

Este tutorial mostra-lhe como configurar a recuperação de desastres de servidores físicos windows e Linux para Azure. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar pré-requisitos do Azure e no local
> * Criar um cofre dos Serviços de Recuperação para o Site Recovery 
> * Configurar os ambientes de replicação de origem e alvo
> * Criar uma política de replicação
> * Ativar a replicação de um servidor

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Certifique-se de que compreende a [arquitetura e os componentes](physical-azure-architecture.md) para este cenário.
- Reveja os [requisitos de suporte](vmware-physical-secondary-support-matrix.md) para todos os componentes.
- Certifique-se de que os servidores que pretende replicar cumprem os [requisitos do Azure VM](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Prepara o Azure. Precisa de uma subscrição Azure, uma rede virtual Azure e uma conta de armazenamento.
- Prepare uma conta para a instalação automática do serviço Mobility em cada servidor que pretende replicar.

Antes de começar, note que:

- Depois de falhar em Azure, os servidores físicos não podem ser falhados de volta às máquinas físicas no local. Só pode falhar em VMware VMware VMs. 
- Este tutorial configura a recuperação de desastres do servidor físico para Azure com as definições mais simples. Se quiser aprender sobre outras opções, leia os nossos guias Como Fazer:
    - Configurar a [fonte de replicação,](physical-azure-set-up-source.md)incluindo o servidor de configuração de recuperação do site.
    - Configure o [destino da replicação](physical-azure-set-up-target.md).
    - Configure uma [política de replicação](vmware-azure-set-up-replication.md) e [ative a replicação](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Criar uma conta Azure

Obtenha uma conta Microsoft [Azure.](https://azure.microsoft.com/)

- Pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Saiba mais sobre [os preços de recuperação do local](site-recovery-faq.md#pricing)e obtenha detalhes sobre [preços.](https://azure.microsoft.com/pricing/details/site-recovery/)
- Descubra quais [as regiões apoiadas](https://azure.microsoft.com/pricing/details/site-recovery/) para a Recuperação do Local.

### <a name="verify-azure-account-permissions"></a>Verifique permissões da conta Azure

Certifique-se de que a sua conta Azure tem permissões para a replicação de VMs para Azure.

- Reveja as [permissões necessárias](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) para replicar máquinas para Azure.
- Verifique e modifique permissões [de controlo de acesso baseado em funções (Azure RBAC).](../role-based-access-control/role-assignments-portal.md) 



### <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

Crie uma [rede Azure.](../virtual-network/quick-create-portal.md)

- Os VMs Azure são colocados nesta rede quando são criados após o failover.
- A rede deve estar na mesma região que o cofre dos Serviços de Recuperação.


## <a name="set-up-an-azure-storage-account"></a>Configurar uma conta de armazenamento do Azure

Crie uma [conta de armazenamento Azure.](../storage/common/storage-account-create.md)

- A Recuperação do Local replica máquinas no local para o armazenamento da Azure. Os VMs Azure são criados a partir do armazenamento após o fracasso.
- A conta de armazenamento tem de estar na mesma região que o cofre dos Serviços de Recuperação.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço Mobility tem de ser instalado em cada servidor que pretende replicar. A Recuperação do Site instala este serviço automaticamente quando ativa a replicação do servidor. Para instalar automaticamente, precisa de preparar uma conta que a Recuperação do Site utilizará para aceder ao servidor.

- Pode utilizar um domínio ou conta local
- Para os VMs do Windows, se não estiver a utilizar uma conta de domínio, desative o controlo de acesso ao utilizador remoto na máquina local. Para isso, no registo em **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adicione a entrada DWORD **LocalAccountTokenFilterPolicy,** com um valor de 1.
- Para adicionar a entrada de registo para desativar a definição de um CLI, escreva:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Para o Linux, a conta deve estar na raiz do servidor Linux de origem.


## <a name="create-a-vault"></a>Criar um cofre

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Selecionar um objetivo de proteção

Selecione o que replicar e replicá-lo.

1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu de Recursos, clique em **'Recuperar o Local Preparar**a  >  **Prepare Infrastructure**  >  **Proteção da Infraestruturas'.**
3. Na **meta de Proteção**, selecione **Para Azure**  >  **Não virtualizado/Outros**.

## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

Configurar o servidor de configuração, registá-lo no cofre e descobrir VMs.

1. Clique **na recuperação do local**Preparar Fonte  >  **de Infraestrutura**  >  **Source**.
2. Se não tiver um servidor de configuração, clique em **+Servidor de Configuração**.
3. In **Add Server**, verifique se o Servidor de **Configuração** aparece no **tipo servidor**.
4. Descarregue o ficheiro de instalação de configuração unificada de recuperação do site.
5. Transfira a chave de registo do cofre. Precisas disto quando executares a Configuração Unificada. A chave é válida durante cinco dias depois de gerá-la.

   ![Screenshot mostrando as opções para descarregar o ficheiro de instalação e a chave de registo.](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registar o servidor de configuração no cofre

Faça o seguinte antes de começar: 

#### <a name="verify-time-accuracy"></a>Verificar precisão de tempo
Na máquina do servidor de configuração, certifique-se de que o relógio do sistema está sincronizado com um [Servidor de Tempo](/windows-server/networking/windows-time-service/windows-time-service-top). Deve coincidir. Se forem 15 minutos na frente ou atrás, a configuração pode falhar.

#### <a name="verify-connectivity"></a>Verificar conectividade
Certifique-se de que a máquina pode aceder a estes URLs com base no seu ambiente: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

As regras de firewall baseadas em endereços IP devem permitir a comunicação a todos os URLs Azure listados acima sobre a porta HTTPS (443). Para simplificar e limitar as gamas IP, recomenda-se que seja feita a filtragem de URL.

- **IPs comerciais** - Permitir os intervalos IP do [Centro de Dados Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)e a porta HTTPS (443). Permita que os intervalos de endereços IP para a região Azure da sua subscrição suportem os URLs de AAD, Backup, Replicação e Armazenamento.  
- **IPs do governo** - Permitir que o [Azure Government Datacenter IP Ranges](https://www.microsoft.com/en-us/download/details.aspx?id=57063), e o porto HTTPS (443) para todas as regiões USGov (Virginia, Texas, Arizona e Iowa) apoiem URLs AAD, Backup, Replication e Storage.  

#### <a name="run-setup"></a>Executar a configuração
Executar configuração unificada como administrador local, para instalar o servidor de configuração. O servidor de processo e o servidor alvo principal também são instalados por padrão no servidor de configuração.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]


## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino.

1. Clique **em Preparar a infraestrutura**  >  **Target**e selecione a subscrição Azure que pretende utilizar.
2. Especifique o modelo de implementação do alvo.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

   ![Screenshot das opções para configurar o ambiente alvo.](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Criar uma política de replicação

1. Para criar uma nova política de replicação, clique em Políticas de Replicação **da Infraestrutura de Recuperação do Local**  >  **Replication Policies**  >  **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limiar RPO**, especifique o limite do objetivo de ponto de recuperação (RPO). Este valor especifica a frequência com que são criados pontos de recuperação de dados. Será gerado um alerta se a replicação contínua exceder este limite.
4. Em **Retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. As VMs replicadas podem ser recuperadas para qualquer ponto numa janela. É suportada uma retenção de até 24 horas para máquinas replicadas para o armazenamento premium e até 72 horas para armazenamento standard.
5. Na **frequência instantânea consistente da App,** especifique com que frequência (em minutos) serão criados pontos de recuperação que contenham instantâneos consistentes com aplicações. Clique em **OK** para criar a política.

    ![Screenshot das opções para criar uma política de replicação.](./media/physical-azure-disaster-recovery/replication-policy.png)


A política é associada automaticamente ao servidor de configuração. Por predefinição, uma política correspondente é criada automaticamente para reativação pós-falha. Por exemplo, se a política de replicação for **a política de rep-política,** então é criada uma política **de rep-failback** política de recuo. Esta política não é utilizada depois de iniciar uma reativação pós-falha a partir do Azure.

## <a name="enable-replication"></a>Ativar a replicação

Ativar a replicação de cada servidor.

- A Recuperação do Site instalará o serviço mobility quando a replicação estiver ativada.
- Quando ativa a replicação de um servidor, pode demorar 15 minutos ou mais para que as alterações entrem em vigor e apareçam no portal.

1. Clique **em Replicar a aplicação**  >  **Fonte**.
2. Em **Origem**, selecione o servidor de configuração.
3. No **tipo máquina,** selecione **máquinas físicas**.
4. Selecione o servidor de processo (o servidor de configuração). Em seguida, clique em **OK**.
5. No **Target**, selecione a subscrição e o grupo de recursos no qual pretende criar os VMs Azure após o failover. Escolha o modelo de implementação que pretende utilizar no Azure (gestão clássica ou de recursos).
6. Selecione a conta de armazenamento do Azure que pretende utilizar para replicar os dados. 
7. Selecione a rede e a sub-rede do Azure às quais as VMs do Azure se vão ligar quando forem criadas após a ativação pós-falha.
8. Selecione **Configurar agora para máquinas selecionadas**, para aplicar a definição de rede a todas as máquinas selecionadas para proteção. Selecione **Configurar mais tarde** para selecionar a rede do Azure por máquina. 
9. Em **Máquinas Físicas,** clique em **+Máquina física.** Especifique o nome e o endereço IP. Selecione o sistema operativo da máquina que pretende replicar. Leva alguns minutos para os servidores serem descobertos e listados. 
10. Nas propriedades **Properties**  >  **Configure**, selecione a conta que será utilizada pelo servidor de processos para instalar automaticamente o serviço de Mobilidade na máquina.
11. Nas **definições de replicação,**  >  **certifique-se de que**a política de replicação correta está selecionada. 
12. Clique **em Ativar a replicação.** Pode acompanhar o progresso do trabalho de **Proteção ativa** em **Definições**  >  **de**  >  **Empregos Locais de Recuperação do Local .** Depois de o trabalho **de Proteção finalização,** a máquina está pronta para falhar.


Para monitorizar os servidores que adiciona, pode verificar a última vez que os descobertos são os últimos contactos dos **servidores**de  >  **configuração.** Para adicionar máquinas sem esperar por um tempo de descoberta programado, realce o servidor de configuração (não clique nele) e clique em **Refresh**.

## <a name="next-steps"></a>Passos seguintes

[Faça um exercício de recuperação de desastres.](tutorial-dr-drill-azure.md)
