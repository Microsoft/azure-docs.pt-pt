---
title: Gerir servidores VMware vCenter em Recuperação de Site Azure
description: Este artigo descreve como adicionar e gerir vCenter VMware para recuperação de desastres de VMware VMs para Azure com Recuperação do Site Azure.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79257267"
---
# <a name="manage-vmware-vcenter-server"></a>Gerir vCenter Server VMware vCenter

Este artigo resume ações de gestão num VMware vCenter Server em Recuperação de [Site Azure](site-recovery-overview.md).

## <a name="verify-prerequisites-for-vcenter-server"></a>Verifique os pré-requisitos para o servidor vCenter

Os pré-requisitos para servidores vCenter e VMs durante a recuperação de desastres de VMware VMs para Azure estão listados na [matriz](vmware-physical-azure-support-matrix.md#replicated-machines)de suporte .

## <a name="set-up-an-account-for-automatic-discovery"></a>Criar uma conta para a descoberta automática

Quando configura a recuperação de desastres para VMware VMs no local, a Recuperação do Site precisa de acesso ao anfitrião vCenter Server/vSphere. O servidor do processo de recuperação do site pode então descobrir automaticamente VMs e reempá-los conforme necessário. Por predefinição, o servidor de processo funciona no servidor de configuração de recuperação do site. Adicione uma conta para o servidor de configuração ligar ao anfitrião vCenter Server/vSphere da seguinte forma:

1. Inscreva-se no servidor de configuração.
1. Abra a ferramenta do servidor de configuração _(cspsconfigtool.exe_) utilizando o atalho do Ambiente de Trabalho.
1. No separador **'Gerir conta',** clique em **Adicionar Conta**.

   ![conta add](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Forneça os detalhes da conta e clique em **OK** para adicioná-lo. A conta deve ter os privilégios resumidos na tabela de permissões da conta.

   > [!NOTE]
   > Leva cerca de 15 minutos para sincronizar informação da conta com a Recuperação do Site.

### <a name="account-permissions"></a>Permissões de conta

|**Tarefa** | **Conta** | **Permissões** | **Detalhes**|
|--- | --- | --- | ---|
|**Descoberta/migração VM (sem recuo)** | Pelo menos uma conta de utilizador só para leitura. | Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribuir a função **De acesso com** o **Propagate a objetos infantis,** aos objetos infantis (hospedeiros vSphere, lojas de dados, máquinas virtuais e redes).|
|**Replicação/failover** | Pelo menos uma conta de utilizador só para leitura. | Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribuir a função **De acesso ao** Propagate aos objetos **infantis** (vSphere acolhe, lojas de dados, máquinas virtuais e redes).<br/><br/> Útil para fins migratórios, mas não replicação completa, failover, failback.|
|**Replicação/failover/failback** | Sugerimos que crie uma função (AzureSiteRecoveryRole) com as permissões necessárias e, em seguida, atribuir a função a um utilizador ou grupo VMware. | Objeto do Data Center – > Propagate para o Objeto Infantil, role=AzureSiteRecoveryRole<br/><br/> Arquivo de Dados -> Alocar espaço, navegar no arquivo de dados, operações de ficheiro de baixo nível, remover ficheiros, atualizar ficheiros de máquinas virtuais<br/><br/> Rede -> Atribuição de rede<br/><br/> Recursos -> Atribuir VM a agrupamento de recursos, migrar VMs desligadas, migrar VMs ligadas<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder a perguntas, ligação de dispositivos, configurar suportes de dados em CD, configurar suportes de dados em disquete, desligar, ligar, instalação de ferramentas de VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registar, anular o registo<br/><br/> Máquina virtual -> Aprovisionamento -> Permitir transferência de máquinas virtuais, permitir carregamento de ficheiros de máquinas virtuais<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribuir a função **De acesso com** o **Propagate a objetos infantis,** aos objetos infantis (hospedeiros vSphere, lojas de dados, máquinas virtuais e redes).|

## <a name="add-vmware-server-to-the-vault"></a>Adicione o servidor VMware ao cofre

Ao configurar a recuperação de desastres para VMware VMs no local, adicione o anfitrião vCenter Server/vSphere no qual está a descobrir VMs para o cofre de Recuperação do Site, da seguinte forma:

1. No cofre >**Configuração**de Configuração de **Infraestruturade** > Recuperação do Local, abra o servidor de configuração.
1. Na página **Detalhes,** clique em **vCenter**.
1. Em **Adicionar vCenter,** especifique um nome amigável para o anfitrião vSphere ou servidor vCenter.
1. Especifique o endereço IP ou fQDN do servidor.
1. Deixe a porta como 443, a menos que os servidores do VMware estejam configurados para escutar os pedidos numa porta diferente.
1. Selecione a conta utilizada para ligar ao vCenter VMware ou ao servidor vSphere ESXi. Em seguida, clique em **OK**.

## <a name="modify-credentials"></a>Modificar credenciais

Se necessário, pode modificar as credenciais utilizadas para se ligar ao anfitrião vCenter Server/vSphere da seguinte forma:

1. Inscreva-se no servidor de configuração.
1. Abra a ferramenta do servidor de configuração _(cspsconfigtool.exe_) utilizando o atalho do Ambiente de Trabalho.
1. Clique em **Adicionar Conta** no separador **'Gerir conta'.**

   ![conta add](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Forneça os novos detalhes da conta e clique em **OK**. A conta precisa das permissões listadas na tabela de [permissões](#account-permissions) da conta.
1. No cofre >**Configuração**de Configuração de **Infraestruturade** > Recuperação do Local, abra o servidor de configuração.
1. Em **Detalhes,** clique em **Refresh Server**.
1. Depois de terminar o trabalho do Refresh Server, selecione o vCenter Server.
1. Em **Resumo,** selecione a conta recém-adicionada na **conta de anfitrião vCenter/vSphere**, e clique em **Guardar**.

   ![modificar conta](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Eliminar um servidor vCenter

1. No cofre >**Configuração**de Configuração de **Infraestruturade** > Recuperação do Local, abra o servidor de configuração.
1. Na página **Detalhes,** selecione o servidor vCenter.
1. Clique no botão **Eliminar.**

   ![excluir conta](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Modificar o endereço IP e a porta

Pode modificar o endereço IP do servidor vCenter ou as portas utilizadas para a comunicação entre o servidor e a Recuperação do Site. Por padrão, a Recuperação do Site acede à informação do anfitrião vCenter Server/vSphere através da porta 443.

1. No cofre > Servidores de > **Configuração**de **Infraestruturade Recuperação**do Site, clique no servidor de configurações ao qual o servidor vCenter é adicionado.
1. Nos **servidores vCenter,** clique no vCenter Server que pretende modificar.
1. Em **resumo,** atualize o endereço IP e a porta e guarde as alterações.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. Para que as alterações se tornem eficazes, aguarde por 15 minutos ou [refresque o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>Migrar todos os VMs para um novo servidor

Se pretender migrar todos os VMs para utilizar um novo Servidor vCenter, basta atualizar o endereço IP atribuído ao servidor vCenter. Não adicione outra conta VMware, uma vez que isso pode levar a entradas duplicadas. Atualize o endereço da seguinte forma:

1. No cofre > Servidores de > **Configuração**de **Infraestruturade Recuperação**do Site, clique no servidor de configurações ao qual o servidor vCenter é adicionado.
1. Na secção de **servidores vCenter,** clique no VCenter Server de onde pretende migrar.
1. Em **Resumo**, atualize o endereço IP para o do novo servidor vCenter e guarde as alterações.
1. Assim que o endereço IP é atualizado, a Recuperação do Site começa a receber informações de descoberta vM do novo servidor vCenter. Isto não afeta as atividades de replicação em curso.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Migrar alguns VMs para um novo servidor

Se quiser apenas migrar alguns dos seus VMs de replicação para um novo servidor vCenter, faça o seguinte:

1. [Adicione](#add-vmware-server-to-the-vault) o novo servidor vCenter ao servidor de configuração.
1. [Desative a replicação](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para VMs que se moverão para o novo servidor.
1. Em VMware, emigre os VMs para o novo VCenter Server.
1. Ative novamente a [replicação](vmware-azure-tutorial.md#enable-replication) para os VMs migrados, selecionando o novo Servidor vCenter.

## <a name="migrate-most-vms-to-a-new-server"></a>Migrar a maioria dos VMs para um novo servidor

Se o número de VMs que pretende migrar para um novo VCenter Server for superior ao número de VMs que permanecerão no vCenter Server original, faça o seguinte:

1. [Atualize o endereço IP](#modify-the-ip-address-and-port) atribuído ao servidor vCenter nas definições do servidor de configuração, para o endereço do novo servidor vCenter.
1. [Desative](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) a replicação para os poucos VMs que permanecem no servidor antigo.
1. [Adicione o antigo VCenter Server](#add-vmware-server-to-the-vault) e o seu endereço IP ao servidor de configuração.
1. [Reativar](vmware-azure-tutorial.md#enable-replication) a replicação para os VMs que permanecem no servidor antigo.

## <a name="next-steps"></a>Passos seguintes

Se tiver algum problema, consulte falhas de descoberta do [Troubleshoot vCenter Server](vmware-azure-troubleshoot-vcenter-discovery-failures.md).
