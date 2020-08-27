---
title: Preparar VMware VMs para avaliação/migração com Azure Migrate
description: Saiba como se preparar para a avaliação/migração de VMware VMs com Azure Migrate.
ms.topic: tutorial
ms.date: 06/08/2020
ms.custom: mvc
ms.openlocfilehash: 8d4d6ac1149c397442a8ca7dd01f46f04ffc89b4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88927311"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparar VMs VMware para avaliação e migração para o Azure

Este artigo ajuda-o a preparar-se para a avaliação e migração de VMware VMs para Azure utilizando [a Azure Migrate.](migrate-services-overview.md)

Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMware VMs. Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Prepare o Azure para trabalhar com a Azure Migrate.
> * Prepare-se para avaliar VMware VMs com a ferramenta Azure Migrate:Server Assessment.
> * Prepare-se para migrar VMware VMs com a ferramenta migração Azure Migrate:Server Migration. 

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. São úteis como uma rápida prova de conceito. Os tutoriais usam opções padrão sempre que possível e não mostram todas as definições e caminhos possíveis. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

A tabela resume as tarefas que precisa de completar em Azure. Instruções para cada tarefa siga a tabela.

**Tarefa** | **Detalhes** | **Permissões**
--- | --- | ---
**Criar um projeto Azure Migrate** | Um projeto Azure Migrate fornece uma localização central para orquestrar e gerir avaliações e migrações com ferramentas Azure Migrate, ferramentas Microsoft e ofertas de terceiros. | A sua conta Azure necessita de permissões de Contribuinte ou Proprietário no grupo de recursos em que o projeto reside.
**Aparelho de registo** | A Azure Migrate utiliza um aparelho Azure Migrate leve para descobrir VMs, para os avaliar com a ferramenta de avaliação do servidor e para os migrar usando migração sem agente com a ferramenta de migração do servidor. [Saiba mais](migrate-appliance-architecture.md#appliance-registration) sobre o registo. | Para registar o aparelho, a sua conta Azure necessita de permissões de Contribuinte ou Proprietário na assinatura Azure.
**Criar aplicativos AD AZure** | Ao registar um aparelho, a Azure Migrate cria aplicações Azure two Ative Directory (Azure AD). <br/><br/> - A primeira aplicação é utilizada para a comunicação entre os agentes que estão a trabalhar no aparelho e a Azure Migrate. <br/><br/> - A segunda aplicação é utilizada exclusivamente para aceder ao KeyVault criado na subscrição do utilizador para migração VMware VMware sem agente.   | A sua conta Azure precisa [destas permissões](https://docs.microsoft.com/azure/migrate/tutorial-prepare-vmware#assign-permissions-to-create-azure-ad-apps) para criar aplicações AD Azure.
**Criar um Key Vault** | - O primeiro Cofre-chave é criado como parte do registo do aparelho e é utilizado para a gestão do certificado descarregado no aparelho durante a sua configuração. <br/><br/> -Para migrar VMware VMs usando migração sem agente, a Azure Migrate cria outro Cofre-Chave para gerir as chaves de acesso à conta de replicação na sua subscrição.| Para permitir que a Azure Migrate crie o Cofre de Chaves, define permissões (Proprietário, ou Administrador de Acesso ao Utilizador) no grupo de recursos em que reside o projeto Azure Migrate.


### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

1. No portal Azure, abra a subscrição e selecione **Access control (IAM)**.
2. No **Acesso ao Cheque,** encontre a conta relevante e clique nela para ver permissões.
3. Deve ter permissões **de Colaborador** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não é o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.

### <a name="assign-permissions-to-create-azure-ad-apps"></a>Atribuir permissões para criar aplicações AD Azure

Para registar o aparelho, a sua conta Azure necessita de permissões para criar aplicações AD Azure. Atribuir as permissões utilizando um destes métodos:

- **Método 1: Conceder permissões à conta**: Um administrador inquilino/global pode conceder permissões às contas dos utilizadores no arrendatário, para criar e registar aplicações Azure AD.
- **Método 2: Atribuir uma função com as permissões a uma conta de utilizador**: Um administrador inquilino/global pode atribuir a função de Desenvolvedor de Aplicações (que tem as permissões necessárias) à conta de utilizador.

> [!NOTE]
> Só precisa destas permissões quando registar um novo aparelho. Pode retirar as permissões após a instalação do aparelho.


#### <a name="method-1-grant-permissions-to-the-account"></a>Método 1: Conceder permissões à conta

Conceder permissões à conta da seguinte forma:

1. Certifique-se de ser inquilino ou administrador global. Em seguida, em Azure AD, navegue para **Azure Ative Directory**  >  **Users**  >  **User Settings**.
2. Desaprote as **inscrições** da App para **Sim.** Esta é uma definição padrão que não é sensível. [Saiba mais](../active-directory/develop/active-directory-how-applications-are-added.md#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Permissões AD AZure](./media/tutorial-prepare-vmware/aad.png)



#### <a name="method-2-assign-application-developer-role"></a>Método 2: Atribuir função de desenvolvedor de aplicações

Em alternativa, o administrador inquilino/global pode atribuir o papel de Desenvolvedor de Aplicações a uma conta. [Saiba mais](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md) sobre a atribuição de um papel.

### <a name="assign-permissions-to-create-a-key-vault"></a>Atribuir permissões para criar um Cofre de Chaves

Para permitir que a Azure Migrate crie um Cofre de Chaves, atribua permissões da seguinte forma:

1. No grupo de recursos do portal Azure, selecione **Access control (IAM)**.
2. No **Acesso ao Cheque,** encontre a conta relevante e clique nela para ver permissões.

    - Para executar a avaliação do servidor, as permissões **do Contribuinte** são suficientes.
    - Para executar a migração de servidores sem agente, deverá ter permissões **do Proprietário** (ou Administrador **de Contribuinte** e de Acesso **ao Utilizador).**

3. Se não tiver as permissões necessárias, solicite-as ao proprietário do grupo de recursos.

## <a name="prepare-for-assessment"></a>Preparar para avaliação

Para se preparar para a avaliação do VMware VM, tem de:

1. **Verifique as definições de VMware**. Certifique-se de que o servidor vCenter e vMs que pretende migrar satisfazem os requisitos.
2. **Configurar permissões para avaliação.** A Azure Migrate utiliza uma conta vCenter para aceder ao servidor vCenter, para descobrir e avaliar VMs.
3. **Verifique os requisitos do aparelho**. Verifique os requisitos de implantação do aparelho Azure Migrate, antes de o colocar no tutor seguinte.

### <a name="verify-vmware-settings"></a>Verificar definições de VMware

1. [Verifique os requisitos da VMware](migrate-support-matrix-vmware.md#vmware-requirements) para avaliação.
2. [Certifique-se de](migrate-support-matrix-vmware.md#port-access-requirements) que as portas de que necessita estão abertas no servidor vCenter.
3. No vCenter Server, verifique se a sua conta tem permissões para criar um VM utilizando um ficheiro OVA. Isto é necessário quando colocar o aparelho Azure Migrate como VMware VM, utilizando um ficheiro OVA.
4. São necessárias algumas alterações nos VM antes de as migrar para Azure.

    - Para alguns sistemas operativos, a Azure Migrate faz estas alterações automaticamente. 
    - É importante fazer estas mudanças antes de começar a migração. Se migrar o VM antes de fazer a alteração, o VM pode não arrancar em Azure.
    - Reveja as alterações do [Windows](prepare-for-migration.md#windows-machines) e [do Linux](prepare-for-migration.md#linux-machines) que precisa de fazer.


### <a name="set-up-permissions-for-assessment"></a>Configurar permissões para avaliação

O Azure Migrate precisa de aceder ao servidor vCenter para que o aparelho Azure Migrate possa descobrir VMs para avaliação e migração sem agentes. Criar uma conta da seguinte forma:

1. No vSphere Web Client, open **Administration**  >  **Access**  >  **SSO Users and Groups**.
2. Nos **Utilizadores,** clique no ícone **Novo Utilizador.**
3. Digite os novos detalhes do utilizador.
4. Selecione permissões de acordo com os valores da tabela.

    **Funcionalidade** | **Permissões de conta**
    --- | ---
    [Avaliar VMs](tutorial-assess-vmware.md) | Para avaliação, a conta precisa de acesso apenas de leitura.
    [Descubra aplicativos, papéis e funcionalidades VM](how-to-discover-applications.md) | Se pretender utilizar a descoberta de aplicações, a conta apenas de leitura utilizada para a avaliação necessita de privilégios ativados para **máquinas virtuais**  >  **de Operações de Hóspedes.**
    [Analisar as dependências dos VMs (sem agente)](how-to-create-group-machine-dependencies-agentless.md) | Se pretender analisar dependências, a conta apenas de leitura utilizada para a avaliação necessita de privilégios habilitados para **operações de hóspedes de máquinas**  >  **Guest Operations**virtuais.
    
> [!NOTE]
> Se pretende limitar a descoberta de VM para avaliação a um âmbito específico, [reveja este artigo](set-discovery-scope.md#assign-a-role-for-assessment).

### <a name="verify-appliance-settings-for-assessment"></a>Verifique as definições do aparelho para avaliação

No [tutorial seguinte,](tutorial-assess-vmware.md) configura o aparelho Azure Migrate e começa a avaliação. Antes de o fazer, reveja os requisitos do aparelho da seguinte forma: 

1. [Reveja os requisitos](migrate-appliance.md#appliance---vmware) para a colocação do aparelho Azure Migrate.
2. Reveja os URLs Azure que o aparelho precisa de aceder nas nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
3. [Note](migrate-support-matrix-vmware.md#port-access-requirements) as portas que o aparelho utiliza.
4. [Reveja os dados](migrate-appliance.md#collected-data---vmware) que o aparelho recolhe durante a descoberta e avaliação.

## <a name="prepare-for-agentless-vmware-migration"></a>Prepare-se para a migração sem agente da VMware

Pode migrar VMware VMs usando [migração sem agentes ou baseada em agentes.](server-migrate-overview.md) Esta secção resume os requisitos para a migração sem agentes.

1. [Decida](server-migrate-overview.md#compare-migration-methods) se quer usar migração sem agente.
2. [Reveja](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) os requisitos de hipervisor para as máquinas que pretende migrar.
3. [Reveja](migrate-support-matrix-vmware-migration.md#vm-requirements-agentless) os requisitos para VMware VMs que pretende migrar usando migração sem agente.
4. [Reveja](migrate-support-matrix-vmware-migration.md#appliance-requirements-agentless) os requisitos do aparelho Azure Migrate para a migração sem agentes.
5. Note o acesso url necessário para nuvens [públicas](migrate-appliance.md#public-cloud-urls) e [governamentais.](migrate-appliance.md#government-cloud-urls)
6. Reveja os requisitos de acesso à [porta.](migrate-support-matrix-vmware-migration.md#port-requirements-agentless)
7. Configurar permissões para migração sem agentes, conforme descrito no procedimento seguinte.


### <a name="assign-permissions-to-an-account"></a>Atribuir permissões a uma conta

O aparelho Azure Migrate liga-se ao servidor vCenter para descobrir e migrar VMs com migração sem agente. Pode atribuir as permissões de que o aparelho necessita para a conta do utilizador, ou criar uma função com as permissões, e atribuir essa função a uma conta de utilizador.

1. No vSphere Web Client, open **Administration**  >  **Access**  >  **SSO Users and Groups**.
2. Nos **Utilizadores,** clique no ícone **Novo Utilizador.**
3. Digite os novos detalhes do utilizador.
4. Atribuir [estas permissões](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)

#### <a name="create-a-role-and-assign-to-an-account"></a>Criar uma função e atribuir a uma conta

Em alternativa, pode criar uma conta. Em seguida, crie um papel, e atribua-o à conta, da seguinte forma:

1. Inscreva-se no Cliente Web da Esfera como administrador do servidor vCenter
2. Selecione a instância do servidor vCenter >  **Criar função**.
3. Especifique um nome de função, por exemplo <em>Azure_Migrate</em>, e atribua as [permissões necessárias](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless) para a função.

    ![privilégios de conta vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

4. Agora crie uma conta e atribua o papel à conta.

> [!NOTE]
> Se pretende limitar a descoberta de VM para migração sem agente para um âmbito específico, [reveja este artigo](set-discovery-scope.md#assign-a-role-for-agentless-migration).



## <a name="prepare-for-agent-based-vmware-migration"></a>Preparar para a migração de VMware baseada em agentes

Pode migrar VMware VMs usando [migração sem agentes ou baseada em agentes.](server-migrate-overview.md) Esta secção resume os requisitos para a migração baseada em agentes.

1. [Decida](server-migrate-overview.md#compare-migration-methods) se quer usar migração baseada em agentes.
1. [Reveja](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) os requisitos de hipervisor para as máquinas que pretende migrar.
2. [Revisão](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) Os requisitos de VMware VMs, incluindo a instalação do serviço de Mobilidade em cada VM que pretende migrar.
3. A migração baseada em agentes utiliza um aparelho de replicação:
    - [Reveja](migrate-replication-appliance.md#appliance-requirements) os requisitos de implantação do aparelho de replicação.
    - [Reveja as opções](migrate-replication-appliance.md#mysql-installation) de instalação do MySQL no aparelho.
    - Note o acesso url necessário para nuvens [públicas](migrate-replication-appliance.md#url-access) e [governamentais.](migrate-replication-appliance.md#azure-government-url-access)
    - Reveja os requisitos de acesso à [porta](migrate-replication-appliance.md#port-access) para o aparelho de replicação.
4. São necessárias algumas alterações nos VM antes de as migrar para Azure. Reveja as alterações do [Windows](prepare-for-migration.md#windows-machines) e [do Linux](prepare-for-migration.md#linux-machines) que precisa de fazer.



## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Estabeleça permissões do Azure.
> * VMware preparado para avaliação e migração.


Continue até ao segundo tutorial para criar um projeto Azure Migrate, e avaliar VMware VMs para migração para Azure.

> [!div class="nextstepaction"]
> [Avaliar VMware VMs](./tutorial-assess-vmware.md)
