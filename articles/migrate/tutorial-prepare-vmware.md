---
title: Preparar VMs VMware para avaliação/migração com as migrações para Azure
description: Saiba como se preparar para a avaliação/migração de VMs VMware com as migrações para Azure.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 25dab303ce62e33a09346d14c0a08a43b715075d
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989139"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparar VMs VMware para avaliação e migração para o Azure

Este artigo ajuda você a se preparar para a avaliação e/ou a migração de VMs do VMware locais para o Azure usando as [migrações para Azure](migrate-services-overview.md).

As [migrações para Azure](migrate-overview.md) fornecem um hub de ferramentas que ajudam a descobrir, avaliar e migrar aplicativos, infraestrutura e cargas de trabalho para Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros.


Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMs VMware. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare o Azure para trabalhar com as migrações para Azure.
> * Prepare o VMware para avaliação de VM.
> * Prepare o VMware para migração de VM.

> [!NOTE]
> Os tutoriais mostram o caminho de implantação mais simples para um cenário. Eles são úteis quando você aprende a configurar uma implantação e como uma prova de conceito rápida. Os tutoriais usam as opções padrão sempre que possível e não mostram todas as configurações e caminhos possíveis. Para obter instruções detalhadas, consulte o How-tos para avaliação e migração do VMware.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

Você precisa dessas permissões.

**Tarefa** | **Permissões**
--- | ---
**Criar um projeto de migrações para Azure** | Sua conta do Azure precisa de permissões para criar um projeto.
**Registrar o dispositivo de migrações para Azure** | As migrações para Azure usam um dispositivo leve de migrações para Azure para avaliar VMs VMware com a avaliação de servidor de migrações para Azure e para executar a [migração sem agente](server-migrate-overview.md) de VMs VMware com migração de servidor de migrações para Azure. Esse dispositivo descobre VMs e envia metadados de VM e dados de desempenho para migrações para Azure.<br/><br/>Durante o registo do aparelho, os seguintes Fornecedores de Registo saem registados com a subscrição escolhida no aparelho: Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault. O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. Para registar os fornecedores de recursos, necessita de uma função de Colaborador ou Proprietário na subscrição.<br/><br/> Como parte do embarque, a Azure Migrate cria duas aplicações Azure Ative Directory (Azure AD):<br/> - A primeira aplicação é utilizada para comunicação (autenticação e autorização) entre os agentes que estão a trabalhar no aparelho com os respetivos serviços em funcionamento no Azure. Esta aplicação não tem privilégios para fazer chamadas ARM ou acesso RBAC em qualquer recurso.<br/> - A segunda aplicação é utilizada exclusivamente para aceder ao KeyVault criado na subscrição do utilizador para migração sem agente. É fornecido um acesso RBAC no Cofre chave Azure (criado no inquilino do cliente) quando a descoberta é iniciada a partir do aparelho.
**Criar um Key Vault** | Para migrar VMs VMware com migração de servidor de migrações para Azure, as migrações para Azure criam um Key Vault para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura. Para criar o cofre, você precisa de permissões de atribuição de função no grupo de recursos no qual o projeto de migração do Azure reside.






### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

1. No portal do Azure, abra a assinatura e selecione **controle de acesso (iam)** .
2. Em **verificar acesso**, localize a conta relevante e clique nela para exibir permissões.
3. Você deve ter permissões de **colaborador** ou de **proprietário** .
    - Se você acabou de criar uma conta gratuita do Azure, você é o proprietário da sua assinatura.
    - Se você não for o proprietário da assinatura, trabalhe com o proprietário para atribuir a função.

### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registrar o dispositivo

Para registrar o dispositivo, você atribui permissões para migrações do Azure para criar os aplicativos do Azure AD durante o registro do dispositivo. As permissões podem ser atribuídas usando um dos seguintes métodos:

- Um locatário/administrador global pode conceder permissões a usuários no locatário para criar e registrar aplicativos do Azure AD.
- Um locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo (que tem as permissões) à conta.

> [!NOTE]
> - Os aplicativos não têm nenhuma outra permissão de acesso na assinatura diferente daquelas descritas acima.
> - Você só precisa dessas permissões ao registrar um novo dispositivo. Você pode remover as permissões depois que o dispositivo for configurado.


#### <a name="grant-account-permissions"></a>Permissões de conta de concessão

O locatário/administrador global pode conceder permissões da seguinte maneira

1. No Azure AD, o locatário/administrador global deve navegar até **Azure Active Directory** > **usuários** > **configurações de usuário**.
2. O administrador deve definir **registros de aplicativo** como **Sim**. Essa é uma configuração padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Permissões do Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Atribuir função de desenvolvedor de aplicativo

O locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

### <a name="assign-permissions-to-create-a-key-vault"></a>Atribuir permissões para criar um Cofre chave

Para permitir que o Azure Migrate crie um Cofre chave, atribua permissões da seguinte forma:

1. No grupo de recursos no portal do Azure, selecione **controle de acesso (iam)** .
2. Em **verificar acesso**, localize a conta relevante e clique nela para exibir permissões.

    - Para executar a avaliação do servidor, as permissões de **colaborador** são suficientes.
    - Para executar a migração de servidor sem agente, você deve ter permissões de **proprietário** (ou **colaborador** e **administrador de acesso do usuário**).

3. Se você não tiver as permissões necessárias, solicite-as do proprietário do grupo de recursos.



## <a name="prepare-for-vmware-vm-assessment"></a>Preparar para avaliação de VM do VMware

Para se preparar para a avaliação de VM do VMware, você precisa:

- **Verifique as configurações do VMware**. Verifique se as vCenter Server e as VMs que você deseja migrar atendem aos requisitos.
- **Configure uma conta de avaliação**. As migrações para Azure precisam acessar o vCenter Server para descobrir VMs para avaliação.
- **Verifique os requisitos do dispositivo**. Verifique os requisitos de implantação para o dispositivo de migrações para Azure usado para avaliação.

### <a name="verify-vmware-settings"></a>Verificar as configurações do VMware

1. [Verificar](migrate-support-matrix-vmware.md#vmware-requirements) Requisitos do servidor VMware para avaliação.
2. [Verifique](migrate-support-matrix-vmware.md#port-access) se as portas de que você precisa estão abertas no vCenter Server.
3. Em vCenter Server, verifique se sua conta tem permissões para criar uma VM usando um arquivo OVA. Você implanta o dispositivo migrações para Azure como uma VM VMware, usando um arquivo OVA.


### <a name="set-up-an-account-for-assessment"></a>Configurar uma conta para avaliação

As migrações para Azure precisam acessar o vCenter Server para descobrir VMs para avaliação e migração sem agente.

- Se você planeja descobrir aplicativos ou Visualizar dependências de maneira sem agente, crie uma conta de vCenter Server com acesso somente leitura junto com os privilégios habilitados para **máquinas virtuais** > **operações de convidado**.

  ![privilégios de conta de vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Se você não estiver planejando fazer a descoberta de aplicativos e a visualização de dependência sem agente, configure uma conta somente leitura para o vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Verificar as configurações do dispositivo para avaliação

Antes de configurar o dispositivo migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. [Verifique](migrate-appliance.md#appliance---vmware) os requisitos do dispositivo para VMs VMware.
2. [Examine](migrate-appliance.md#url-access) as URLs do Azure que o dispositivo precisará acessar. Se você estiver usando um firewall ou proxy baseado em URL, verifique se ele permite o acesso às URLs necessárias.
3. [Examine](migrate-appliance.md#collected-data---vmware) se o dispositivo será coletado durante a descoberta e a avaliação.
4. [Anote](migrate-support-matrix-vmware.md#port-access) os requisitos de acesso da porta para o dispositivo.




## <a name="prepare-for-agentless-vmware-migration"></a>Preparar para a migração do VMware sem agente

Examine os requisitos para a migração sem agente de VMs VMware.

1. [Examinar](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) Os requisitos do servidor VMware e as [permissões](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) que o Azure migra precisam para acessar o vCenter Server para a migração sem agente usando a migração de servidor de migrações para Azure.
2. [Examine](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) os requisitos para VMs do VMware que você deseja migrar para o Azure usando a migração sem agente.
4. [Examine](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) os requisitos para usar o dispositivo de migração do Azure para a migração sem agente.
5. Observe o acesso à [URL](migrate-appliance.md#url-access) e o [acesso à porta](migrate-support-matrix-vmware-migration.md#agentless-ports) necessários para a migração sem agente.


## <a name="prepare-for-agent-based-vmware-migration"></a>Preparar para a migração VMware baseada em agente

Examine os requisitos para a [migração baseada em agente](server-migrate-overview.md) de VMs VMware.

1. [Examinar](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Os requisitos do servidor VMware e as permissões migrações para Azure precisam acessar o vCenter Server para a migração baseada em agente usando a migração de servidor de migrações para Azure.
2. [Examine](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) os requisitos para VMs do VMware que você deseja migrar para o Azure usando a migração baseada em agente, incluindo a instalação do serviço de mobilidade em cada VM que você deseja migrar.
3. As migrações baseadas em agente usam um dispositivo de replicação:
    - [Examine](migrate-replication-appliance.md#appliance-requirements) os requisitos de implantação do dispositivo de replicação e as [Opções](migrate-replication-appliance.md#mysql-installation) de instalação do MySQL no dispositivo.
    - Examine os requisitos de acesso de [URL](migrate-replication-appliance.md#url-access) e [porta](migrate-replication-appliance.md#port-access) para o dispositivo de replicação.
    
## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Configure as permissões do Azure.
> * VMware preparado para avaliação e migração.


Continue no segundo tutorial para configurar um projeto de migrações para Azure e avaliar as VMs do VMware para migração para o Azure.

> [!div class="nextstepaction"]
> [Avaliar VMs VMware](./tutorial-assess-vmware.md)
