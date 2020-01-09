---
title: Gerenciar servidores VMware vCenter no Azure Site Recovery
description: Este artigo descreve como adicionar e gerenciar o VMware vCenter para recuperação de desastre de VMs VMware no Azure com o Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: ba5f31049b599cd55a4a9a4261080c1672d336b1
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495340"
---
# <a name="manage-vmware-vcenter-server"></a>Gerenciar o VMware vCenter Server

Este artigo resume as ações de gerenciamento em um VMware vCenter Server no [Azure site Recovery](site-recovery-overview.md). 

## <a name="verify-prerequisites-for-vcenter-server"></a>Verificar pré-requisitos para vCenter Server

Os pré-requisitos para servidores vCenter e VMs durante a recuperação de desastre de VMs VMware para o Azure são listados na [matriz de suporte](vmware-physical-azure-support-matrix.md#replicated-machines).


## <a name="set-up-an-account-for-automatic-discovery"></a>Configurar uma conta para a descoberta automática

Quando você configura a recuperação de desastre para VMs do VMware locais, Site Recovery precisa acessar o host do vCenter Server/vSphere para que o servidor de processo do Site Recovery possa descobrir automaticamente as VMs e fazer o failover conforme necessário. Por padrão, o servidor de processo é executado no servidor de configuração Site Recovery. Adicione uma conta para o servidor de configuração para se conectar ao host vCenter Server/vSphere da seguinte maneira:

1. Entre no computador do servidor de configuração.
2. Abra a ferramenta servidor de configuração (cspsconfigtool. exe) usando o atalho da área de trabalho.
3. Na guia **gerenciar conta** , clique em **adicionar conta**. 

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
4. Forneça os detalhes da conta e clique em **OK** para adicioná-lo.  A conta deve ter os privilégios resumidos na tabela a seguir. 

É necessário cerca de 15 minutos para sincronizar as informações da conta com Site Recovery.

### <a name="account-permissions"></a>Permissões de conta

|**Tarefa** | **Conta** | **Permissões** | **Detalhes**|
|--- | --- | --- | ---|
|**Descoberta/migração de VM (sem failback)** | Pelo menos uma conta de usuário somente leitura. | Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **sem acesso** com o objeto **propagar para filho** aos objetos filho (hosts vSphere, repositórios de armazenamento, máquinas virtuais e redes).|
|**Replicação/failover** | Pelo menos uma conta de usuário somente leitura. | Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **sem acesso** com o objeto **propagar para filho** aos objetos filho (hosts vSphere, repositórios de armazenamento, máquinas virtuais e redes).<br/><br/> Útil para fins de migração, mas não para replicação completa, failover, failback.|
|**Replicação/failover/failback** | Sugerimos que você crie uma função (AzureSiteRecoveryRole) com as permissões necessárias e, em seguida, atribua a função a um usuário ou grupo do VMware. | Objeto de data center – > propagar para o objeto filho, role = AzureSiteRecoveryRole<br/><br/> Arquivo de Dados -> Alocar espaço, navegar no arquivo de dados, operações de ficheiro de baixo nível, remover ficheiros, atualizar ficheiros de máquinas virtuais<br/><br/> Rede -> Atribuição de rede<br/><br/> Recursos -> Atribuir VM a agrupamento de recursos, migrar VMs desligadas, migrar VMs ligadas<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder a perguntas, ligação de dispositivos, configurar suportes de dados em CD, configurar suportes de dados em disquete, desligar, ligar, instalação de ferramentas de VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registar, anular o registo<br/><br/> Máquina virtual -> Aprovisionamento -> Permitir transferência de máquinas virtuais, permitir carregamento de ficheiros de máquinas virtuais<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **sem acesso** com o objeto **propagar para filho** aos objetos filho (hosts vSphere, repositórios de armazenamento, máquinas virtuais e redes).|


## <a name="add-vmware-server-to-the-vault"></a>Adicionar o servidor VMware ao cofre

Ao configurar a recuperação de desastre para VMs VMware locais, você adiciona o host vCenter Server/vSphere no qual está descobrindo VMs para o cofre de Site Recovery, da seguinte maneira:

1. No cofre > **site Recovery infraestrutura** > **servidores de configuração**, abra o servidor de configuração.
2. Na página de **detalhes** , clique em **vCenter**.
3. Em **Adicionar vCenter**, especifique um nome amigável para o host vSphere ou servidor vCenter.
4. Especifique o endereço IP ou o FQDN do servidor.
5. Deixe a porta como 443, a menos que os servidores do VMware estejam configurados para escutar os pedidos numa porta diferente.
6. Selecione a conta usada para se conectar ao servidor VMware vCenter ou vSphere ESXi. Em seguida, clique em **OK**.



## <a name="modify-credentials"></a>Modificar credenciais

Se necessário, você pode modificar as credenciais usadas para se conectar ao host vCenter Server/vSphere da seguinte maneira:

1. Entre na configuração.
2. Abra a ferramenta servidor de configuração (cspsconfigtool. exe) usando o atalho da área de trabalho.
2. Clique em **adicionar conta** na guia **gerenciar conta** .

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
   
3. Forneça os detalhes da nova conta e clique em **OK**. A conta precisa das permissões listadas [acima](#account-permissions).
4. No cofre > **site Recovery infraestrutura** > **servidores de configuração**, abra o servidor de configuração.
5. Em **detalhes**, clique em **Atualizar servidor**.
6. Após a conclusão do trabalho de atualização do servidor, selecione o vCenter Server.
7. Em **Resumo**, selecione a conta recém-adicionada na **conta do host do Center Server/vSphere**e clique em **salvar**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Excluir um vCenter Server 

1. No cofre > **site Recovery infraestrutura** > **servidores de configuração**, abra o servidor de configuração.
2. Na página **detalhes** , selecione o servidor vCenter.
3. Clique no botão **excluir** .

   ![Excluir conta](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Modificar o endereço IP e a porta

Você pode modificar o endereço IP do vCenter Server ou as portas usadas para comunicação entre o servidor e o Site Recovery. Por padrão, o Site Recovery acessa informações de host vCenter Server/vSphere por meio da porta 443.

1. No cofre > **site Recovery infraestrutura** > **servidores de configuração**, clique no servidor de configurações ao qual o vCenter Server foi adicionado.
2. Em **servidores vCenter**, clique no vCenter Server que você deseja modificar.
5. Em **Resumo**, atualize o endereço IP e a porta e salve as alterações.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Para que as alterações se tornem efetivas, aguarde 15 minutos ou [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).


## <a name="migrate-all-vms-to-a-new-server"></a>Migrar todas as VMs para um novo servidor

Se você quiser migrar todas as VMs para usar uma nova vCenter Server, basta atualizar o endereço IP atribuído à vCenter Server. Não adicione outra conta do VMware, pois isso pode levar a entradas duplicadas. Atualize o endereço da seguinte maneira:

1. No cofre > **site Recovery infraestrutura** > **servidores de configuração**, lique no servidor de configurações ao qual o vCenter Server foi adicionado.
2. Na seção **servidores vCenter** , clique no vCenter Server do qual você deseja migrar.
5. Em **Resumo**, atualize o endereço IP para o novo vCenter Server e salve as alterações.
6. Assim que o endereço IP for atualizado, Site Recovery começará a receber informações de descoberta de VM do novo vCenter Server. Isso não afeta as atividades de replicação contínuas.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Migrar algumas VMs para um novo servidor

Se você quiser migrar apenas algumas de suas VMs de replicação para um novo servidor do vCenter, faça o seguinte:

1. [Adicione](#add-vmware-server-to-the-vault) o novo vCenter Server ao servidor de configuração.
2. [Desabilite a replicação](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para VMs que serão movidas para o novo servidor.
3. No VMware, migre as VMs para o novo vCenter Server. 
4. [Habilite a replicação](vmware-azure-tutorial.md#enable-replication) para as VMs migradas novamente, selecionando a nova vCenter Server.


## <a name="migrate-most-vms-to-a-new-server"></a>Migrar a maioria das VMs para um novo servidor
 Se o número de VMs que você deseja migrar para um novo vCenter Server for maior do que o número de VMs que permanecerão no vCenter Server original, faça o seguinte

1. [Atualize o endereço IP](#modify-the-ip-address-and-port) atribuído ao vCenter Server nas configurações do servidor de configuração para o endereço do novo vCenter Server.
2. [Desabilite a replicação](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para as poucas VMs restantes no servidor antigo.
3. [Adicione o vCenter Server antigo](#add-vmware-server-to-the-vault) e seu endereço IP ao servidor de configuração.
4. [Habilite novamente a replicação](vmware-azure-tutorial.md#enable-replication) para as VMs que permanecem no servidor antigo.
 
 ## <a name="next-steps"></a>Passos seguintes
Se você encontrar algum problema, [solucione problemas](vmware-azure-troubleshoot-vcenter-discovery-failures.md) vCenter Server falhas.
