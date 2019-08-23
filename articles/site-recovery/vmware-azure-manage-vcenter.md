---
title: Gerenciar servidores VMware vCenter para recuperação de desastre de VMs VMware no Azure usando o Azure Site Recovery | Microsoft Docs '
description: Este artigo descreve como adicionar e gerenciar o VMware vCenter para recuperação de desastre de VMs VMware no Azure com o Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 59088d8351bf89c859312774e3e9e396be8dd532
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904257"
---
# <a name="manage-vmware-vcenter-server"></a>Gerenciar o VMware vCenter Server

Este artigo discute as várias operações Site Recovery que podem ser executadas em um VMware vCenter. Verifique os [pré-requisitos](vmware-physical-azure-support-matrix.md#replicated-machines) antes de começar.


## <a name="set-up-an-account-for-automatic-discovery"></a>Configurar uma conta para a descoberta automática

Site Recovery precisa acessar o VMware para o servidor de processo para descobrir automaticamente as máquinas virtuais e para failover e failback de máquinas virtuais. Crie uma conta para acesso da seguinte maneira:

1. Faça logon no computador do servidor de configuração.
2. Abra a inicialização do cspsconfigtool. exe usando o atalho da área de trabalho.
3. Clique em **adicionar conta** na guia **gerenciar conta** .

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Forneça os detalhes da conta e clique em **OK** para adicioná-lo.  A conta deve ter os privilégios resumidos na tabela a seguir. 

Demora cerca de 15 minutos para que as informações da conta sejam sincronizadas com o serviço Site Recovery.

### <a name="account-permissions"></a>Permissões de conta

|**Tarefa** | **Conta** | **Permissões** | **Detalhes**|
|--- | --- | --- | ---|
|**Descoberta automática/migração (sem failback)** | Você precisa de pelo menos um usuário somente leitura | Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **sem acesso** com o objeto propagar **para filho** aos objetos filho (hosts vSphere, repositórios de armazenamento, máquinas virtuais e redes).|
|**Replicação/failover** | Você precisa de pelo menos um usuário somente leitura| Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **sem acesso** com o objeto propagar **para filho** aos objetos filho (hosts vSphere, repositórios de armazenamento, máquinas virtuais e redes).<br/><br/> Útil para fins de migração, mas não para replicação completa, failover, failback.|
|**Replicação/failover/failback** | Sugerimos que você crie uma função (AzureSiteRecoveryRole) com as permissões necessárias e, em seguida, atribua a função a um usuário ou grupo do VMware | Objeto de data center – > propagar para o objeto filho, role = AzureSiteRecoveryRole<br/><br/> Arquivo de Dados -> Alocar espaço, navegar no arquivo de dados, operações de ficheiro de baixo nível, remover ficheiros, atualizar ficheiros de máquinas virtuais<br/><br/> Rede -> Atribuição de rede<br/><br/> Recursos -> Atribuir VM a agrupamento de recursos, migrar VMs desligadas, migrar VMs ligadas<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder a perguntas, ligação de dispositivos, configurar suportes de dados em CD, configurar suportes de dados em disquete, desligar, ligar, instalação de ferramentas de VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registar, anular o registo<br/><br/> Máquina virtual -> Aprovisionamento -> Permitir transferência de máquinas virtuais, permitir carregamento de ficheiros de máquinas virtuais<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a função **sem acesso** com o objeto propagar **para filho** aos objetos filho (hosts vSphere, repositórios de armazenamento, máquinas virtuais e redes).|


## <a name="add-vmware-server-to-the-vault"></a>Adicionar o servidor VMware ao cofre

1. No portal do Azure, abra seu cofre > Site Recovery**servidores de configuração**de **infraestrutura** > e abra o servidor de configuração.
2. Na página **detalhes** , clique em **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Modificar credenciais

Modifique as credenciais usadas para se conectar ao servidor vCenter ou ao host ESXi da seguinte maneira:

1. Faça logon no servidor de configuração e inicie o cspsconfigtool. exe na área de trabalho.
2. Clique em **adicionar conta** na guia **gerenciar conta** .

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Forneça os detalhes da nova conta e clique em **OK** para adicioná-lo. A conta deve ter os privilégios listados [acima](#account-permissions).
4. Na portal do Azure, abra o cofre > Site Recovery**servidores de configuração**de **infraestrutura** > e abra o servidor de configuração.
5. Na página de **detalhes** , clique em **Atualizar servidor**.
6. Após a conclusão do trabalho de atualização do servidor, selecione o vCenter Server, para abrir a página de **Resumo** do vCenter.
7. Selecione a conta recém-adicionada no campo **conta de host do vCenter Server/vSphere** e clique em **salvar**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Excluir um vCenter Server

1. No portal do Azure, abra seu cofre > Site Recovery**servidores de configuração**de **infraestrutura** > e abra o servidor de configuração.
2. Na página **detalhes** , selecione o servidor vCenter.
3. Clique no botão **excluir** .

   ![Excluir conta](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Modificar o endereço IP e a porta do vCenter

1. Inicie sessão no Portal do Azure.
2. Navegue > até **cofre dos serviços de recuperação** **site Recovery** > **servidores de configuração**de infraestrutura.
3. Clique no servidor de configuração ao qual o vCenter está atribuído.
4. Na seção **servidores do vCenter** , clique no vCenter que você deseja modificar.
5. Na página Resumo do vCenter, atualize o endereço IP e a porta do vCenter nos respectivos campos e salve as alterações.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Para que as alterações se tornem efetivas, aguarde 15 minutos ou [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Migrar todas as máquinas virtuais protegidas para um novo vCenter

Para migrar todas as máquinas virtuais para o novo vCenter, não adicione outra conta do vCenter. Isso pode levar a entradas duplicadas. Basta atualizar o endereço IP do novo vCenter:

1. Inicie sessão no Portal do Azure.
2. Navegue > até **cofre dos serviços de recuperação** **site Recovery** > **servidores de configuração**de infraestrutura.
3. Clique no servidor de configuração ao qual o vCenter antigo está atribuído.
4. Na seção **servidores vCenter** , clique no vCenter do qual você está planejando migrar.
5. Na página Resumo do vCenter, atualize o endereço IP do novo vCenter no campo **vCenter Server/vSphere nome do host ou endereço IP**. Guarde as alterações.

Assim que o endereço IP for atualizado, Site Recovery componentes começarão a receber informações de descoberta de máquinas virtuais do novo vCenter. Isso não afetará as atividades de replicação em andamento.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Migrar algumas máquinas virtuais protegidas para um novo vCenter

> [!NOTE]
> Esta seção é aplicável somente quando você está migrando algumas de suas máquinas virtuais protegidas para um novo vCenter. Se você quiser proteger um novo conjunto de máquinas virtuais de um novo vCenter, [Adicione novos detalhes do vCenter ao servidor de configuração](#add-vmware-server-to-the-vault) e comece com **[habilitar a proteção](vmware-azure-tutorial.md#enable-replication)** .

Para mover algumas máquinas virtuais para um novo vCenter:

1. [Adicione os novos detalhes do vCenter ao servidor de configuração](#add-vmware-server-to-the-vault).
2. [Desabilite a replicação das máquinas virtuais](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) que você pretende migrar.
3. Conclua a migração de máquinas virtuais selecionadas para o novo vCenter.
4. Agora, proteja as máquinas virtuais migradas [selecionando o novo vCenter ao habilitar a proteção](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Se o número de máquinas virtuais que estão sendo migradas for **maior** que o número de máquinas virtuais retidas no vCenter antigo, atualize o endereço IP do novo vCenter usando as instruções fornecidas aqui. Para as poucas máquinas virtuais que são mantidas no vCenter antigo, desabilite a [replicação](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure); [Adicione novos detalhes do vCenter ao servidor de configuração](#add-vmware-server-to-the-vault)e comece a **[habilitar a proteção](vmware-azure-tutorial.md#enable-replication)** .

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

1. Se as máquinas virtuais protegidas forem movidas de um host ESXi para outro, isso afetará a replicação?

    Não, isso não afetará a replicação em andamento. No entanto, [certifique-se de implantar o servidor de destino mestre com privilégios suficientes](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Quais são os números de porta usados para a comunicação entre o vCenter e outros componentes de Site Recovery?

    A porta padrão é 443. O servidor de configuração acessará as informações de host do vCenter/vSphere por meio desta porta. Se você quiser atualizar essas informações, clique [aqui](#modify-the-vcenter-ip-address-and-port).
