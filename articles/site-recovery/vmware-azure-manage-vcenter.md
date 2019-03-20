---
title: Gerir servidores VMware vCenter para recuperação após desastre de VMs de VMware para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como adicionar e gerir o VMware vCenter para recuperação após desastre de VMs de VMware para o Azure com o Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: efbcc0143570b3d379cf392c170f599fcc0176d4
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57855134"
---
# <a name="manage-vmware-vcenter-server"></a>Gerir o servidor VMware vCenter

Este artigo aborda as várias operações de recuperação de sites que podem ser realizadas num VMware vCenter. Verifique se o [pré-requisitos](vmware-physical-azure-support-matrix.md#replicated-machines) antes de começar.


## <a name="set-up-an-account-for-automatic-discovery"></a>Configurar uma conta para a deteção automática

Recuperação de sites precisa de aceder ao VMware para o servidor de processos detetar automaticamente as máquinas virtuais e para a ativação pós-falha e reativação pós-falha de máquinas virtuais. Crie uma conta de acesso da seguinte forma:

1. Inicie sessão no computador do servidor de configuração.
2. Abra o lançamento cspsconfigtool.exe usando o atalho de ambiente de trabalho.
3. Clique em **adicionar conta** sobre o **gerir conta** separador.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Forneça os detalhes da conta e clique em **OK** para adicioná-lo.  A conta deve ter os privilégios resumidos na tabela seguinte. 

Demora cerca de 15 minutos para as informações da conta a ser sincronizada a cópia de segurança com o serviço Site Recovery.

### <a name="account-permissions"></a>Permissões de conta

|**Tarefa** | **Conta** | **Permissões** | **Detalhes**|
|--- | --- | --- | ---|
|**Deteção automática/migrar (sem ativação pós-falha)** | Tem de, pelo menos, um utilizador só de leitura | Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a **sem acesso** função com o **propagar ao filho** objeto, aos objetos subordinados (anfitriões vSphere, arquivos de dados, máquinas virtuais e redes).|
|**A replicação/ativação pós-falha** | Tem de, pelo menos, um utilizador só de leitura| Objeto Data Center –> Propagar ao Objeto Subordinado, função=Só de Leitura | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a **sem acesso** função com o **propagar ao filho** objeto aos objetos subordinados (anfitriões vSphere, arquivos de dados, máquinas virtuais e redes).<br/><br/> Útil para fins de migração, mas a replicação completa não, a ativação pós-falha e reativação pós-falha.|
|**A replicação/ativação pós-falha/reativação pós-falha** | Sugerimos que cria uma função (AzureSiteRecoveryRole) com as permissões necessárias e, em seguida, atribua a função a um grupo ou utilizador de VMware | Objeto Data Center –> propagar ao objeto subordinado, função = AzureSiteRecoveryRole<br/><br/> Arquivo de Dados -> Alocar espaço, navegar no arquivo de dados, operações de ficheiro de baixo nível, remover ficheiros, atualizar ficheiros de máquinas virtuais<br/><br/> Rede -> Atribuição de rede<br/><br/> Recursos -> Atribuir VM a agrupamento de recursos, migrar VMs desligadas, migrar VMs ligadas<br/><br/> Tarefas -> Criar tarefa, atualizar tarefa<br/><br/> Máquina virtual -> Configuração<br/><br/> Máquina virtual -> Interagir -> responder a perguntas, ligação de dispositivos, configurar suportes de dados em CD, configurar suportes de dados em disquete, desligar, ligar, instalação de ferramentas de VMware<br/><br/> Máquina virtual -> Inventário -> Criar, registar, anular o registo<br/><br/> Máquina virtual -> Aprovisionamento -> Permitir transferência de máquinas virtuais, permitir carregamento de ficheiros de máquinas virtuais<br/><br/> Máquina virtual -> Instantâneos -> Remover instantâneos | Utilizador atribuído ao nível do datacenter, com acesso a todos os objetos no datacenter.<br/><br/> Para restringir o acesso, atribua a **sem acesso** função com o **propagar ao filho** objeto, aos objetos subordinados (anfitriões vSphere, arquivos de dados, máquinas virtuais e redes).|


## <a name="add-vmware-server-to-the-vault"></a>Adicionar servidor VMware no Cofre

1. No portal do Azure, abra o Cofre > **infraestrutura do Site Recovery** > **servidores de configuração**e abra o servidor de configuração.
2. Sobre o **detalhes** página, clique em **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Modificar as credenciais

Modificar as credenciais utilizadas para ligar ao servidor vCenter ou anfitrião ESXi da seguinte forma:

1. Inicie sessão no servidor de configuração e inicie o cspsconfigtool.exe da área de trabalho.
2. Clique em **adicionar conta** sobre o **gerir conta** separador.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Forneça os detalhes da conta nova e clique em **OK** para adicioná-lo. A conta deve ter os privilégios listados [acima](#account-permissions).
4. No portal do Azure, abra o Cofre > **infraestrutura do Site Recovery** > **servidores de configuração**e abra o servidor de configuração.
5. Na **detalhes** página, clique em **atualizar servidor**.
6. Depois de concluída a tarefa de atualização do servidor, selecione o vCenter Server, para abrir o vCenter **resumo** página.
7. Selecione a conta recentemente adicionada no **conta de anfitrião do vCenter server/vSphere** campo e clique em **guardar**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Eliminar um servidor vCenter

1. No portal do Azure, abra o Cofre > **infraestrutura do Site Recovery** > **servidores de configuração**e abra o servidor de configuração.
2. Sobre o **detalhes** , selecione o servidor vCenter.
3. Clique nas **eliminar** botão.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Modificar o endereço IP do vCenter e a porta

1. Inicie sessão no Portal do Azure.
2. Navegue para **cofre dos serviços de recuperação** > **infraestrutura do Site Recovery** > **Configuration Servers**.
3. Clique no servidor de configuração o vCenter está atribuído.
4. Na **servidores vCenter** secção, clique no vCenter que pretende modificar.
5. Na página de resumo do vCenter, atualize o endereço IP e porta do vCenter nos respetivos campos e, em seguida, guarde as alterações.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. Para que as alterações entram em vigor, aguarde 15 minutos ou [atualizar o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Migrar máquinas virtuais protegidas tudo para um novo vCenter

Para migrar todas as máquinas virtuais para o vCenter novo, não adicione outra conta do vCenter. Isso pode levar a entradas duplicadas. Basta Atualize o endereço IP do vCenter novo:

1. Inicie sessão no Portal do Azure.
2. Navegue para **cofre dos serviços de recuperação** > **infraestrutura do Site Recovery** > **Configuration Servers**.
3. Clique no servidor de configuração antigo vCenter está atribuído.
4. Na **servidores vCenter** secção, clique no vCenter estiver a planear migrar a partir de.
5. Na página de resumo do vCenter, atualizar o endereço IP do vCenter novo no campo **vCenter server/vSphere nome de anfitrião ou endereço IP**. Guarde as alterações.

Assim que o endereço IP é atualizado, componentes do Site Recovery começará a receber informações de deteção de máquinas virtuais a partir do vCenter novo. Isto não afetará as atividades de replicação em curso.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Migrar algumas máquinas virtuais protegidas para um novo vCenter

> [!NOTE]
> Esta secção é aplicável apenas quando estiver a migrar algumas das suas máquinas virtuais protegidas para um novo vCenter. Se pretender proteger um novo conjunto de máquinas virtuais a partir de um novo vCenter, [adicionar novos detalhes do vCenter para o servidor de configuração](#add-vmware-server-to-the-vault) e começar com  **[ativar proteção](vmware-azure-tutorial.md#enable-replication)**.

Para mover algumas máquinas virtuais para um novo vCenter:

1. [Adicionar os novos detalhes do vCenter para o servidor de configuração](#add-vmware-server-to-the-vault).
2. [Desative a replicação das máquinas virtuais](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) estiver a planear migrar.
3. Conclua a migração de máquinas virtuais selecionadas para o vCenter novo.
4. Agora, proteger máquinas virtuais migradas pela [selecionando o vCenter novo quando ativar a proteção](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Se o número de máquinas virtuais a ser migrado **superior** que o número de número de máquinas virtuais retidos no vCenter antigo, atualize o endereço IP do vCenter novo ao seguir as instruções dadas [aqui](#modify-vcenter-ip-address-port). Para algumas máquinas virtuais que são mantidas no vCenter antigo, [desative a replicação](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure); [adicionar novos detalhes do vCenter para o servidor de configuração](#add-vmware-server-to-the-vault)e comece  **[ativar proteção](vmware-azure-tutorial.md#enable-replication)**.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

1. Se as máquinas virtuais protegidas são movidas de um anfitrião do ESXi para outro, irá afetar a replicação?

    Não, não irá afetar a replicação contínua. No entanto, [Certifique-se para implementar o servidor de destino mestre com privilégios suficientes](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Quais são os números de porta utilizado para comunicação entre vCenter e o outro Site Recovery componentes?

    A porta predefinida é 443. Servidor de configuração irá aceder a informações de anfitrião do vCenter/vSphere por essa porta. Se desejar atualizá-las, clique em [aqui](#modify-the-vcenter-ip-address-and-port).
