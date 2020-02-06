---
title: Preparar VMs VMware para avaliação/migração com as migrações para Azure
description: Saiba como se preparar para a avaliação/migração de VMs VMware com as migrações para Azure.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: f00d5ba4841427098b0ab79ad1930e357008b6e0
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77030800"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparar VMs VMware para avaliação e migração para o Azure

Este artigo ajuda-o a preparar-se para avaliação e/ou migração de VMware VMware no local para Azure usando [o Azure Migrate](migrate-services-overview.md).

[A Azure Migrate](migrate-overview.md) fornece um centro de ferramentas que o ajudam a descobrir, avaliar e migrar apps, infraestruturas e cargas de trabalho para o Microsoft Azure. O Hub inclui ferramentas de migração do Azure e ofertas de fornecedores independentes de software (ISV) de terceiros.


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
**Criar um projeto Azure Migrate** | Sua conta do Azure precisa de permissões para criar um projeto.
**Registe o aparelho Azure Migrate** | A Azure Migrate utiliza um aparelho ligeiro de migração Azure para avaliar vMware VMs com avaliação do servidor migratório Azure, e para executar a [migração sem agentes](server-migrate-overview.md) de VMware VMs com migração de servidores migratórios Azure. Esse dispositivo descobre VMs e envia metadados de VM e dados de desempenho para migrações para Azure.<br/><br/>Durante o registo do aparelho, os seguintes Fornecedores de Recursos estão registados com a subscrição escolhida no aparelho: Microsoft.OffAzure, Microsoft.Migrate e Microsoft.KeyVault. O registro de um provedor de recursos configura sua assinatura para trabalhar com o provedor de recursos. Para registar os fornecedores de recursos, necessita de uma função de Colaborador ou Proprietário na subscrição.<br/><br/> Como parte do embarque, a Azure Migrate cria duas aplicações Azure Ative Directory (Azure AD):<br/> - A primeira aplicação é utilizada para comunicação (autenticação e autorização) entre os agentes que estão a trabalhar no aparelho com os respetivos serviços em funcionamento no Azure. Esta aplicação não tem privilégios para fazer chamadas ARM ou acesso RBAC em qualquer recurso.<br/> - A segunda aplicação é utilizada exclusivamente para aceder ao KeyVault criado na subscrição do utilizador para migração sem agente. É fornecido um acesso RBAC no Cofre chave Azure (criado no inquilino do cliente) quando a descoberta é iniciada a partir do aparelho.
**Criar um cofre chave** | Para migrar VMs VMware com migração de servidor de migrações para Azure, as migrações para Azure criam um Key Vault para gerenciar chaves de acesso para a conta de armazenamento de replicação em sua assinatura. Para criar o cofre, você precisa de permissões de atribuição de função no grupo de recursos no qual o projeto de migração do Azure reside.






### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

1. No portal Azure, abra a subscrição e selecione controlo de **acesso (IAM)** .
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões.
3. Deve ter permissões de **Contribuinte** ou **Proprietário.**
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

1. Em Azure AD, o inquilino/administrador global deve navegar para **o Diretório Ativo do Azure** > **utilizadores** > **Definições**de utilizador .
2. O administrador deve definir **os registos** da App para **Sim**. Essa é uma configuração padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Permissões do Azure AD](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Atribuir função de desenvolvedor de aplicativo

O locatário/administrador global pode atribuir a função de desenvolvedor de aplicativo a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal).

### <a name="assign-permissions-to-create-a-key-vault"></a>Atribuir permissões para criar um Cofre chave

Para permitir que o Azure Migrate crie um Cofre chave, atribua permissões da seguinte forma:

1. No grupo de recursos no portal Azure, selecione controlo de **acesso (IAM)** .
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões.

    - Para executar a avaliação do servidor, as permissões **do Contribuinte** são suficientes.
    - Para executar a migração do servidor sem agente, deve ter permissões **de Proprietário** (ou **Colaborador** e Administrador de Acesso **ao Utilizador).**

3. Se você não tiver as permissões necessárias, solicite-as do proprietário do grupo de recursos.



## <a name="prepare-for-vmware-vm-assessment"></a>Preparar para avaliação de VM do VMware

Para se preparar para a avaliação de VM do VMware, você precisa:

- **Verifique as definições do VMware**. Verifique se as vCenter Server e as VMs que você deseja migrar atendem aos requisitos.
- Criar uma conta de **avaliação.** As migrações para Azure precisam acessar o vCenter Server para descobrir VMs para avaliação.
- **Verifique os requisitos do aparelho**. Verifique os requisitos de implantação para o dispositivo de migrações para Azure usado para avaliação.

### <a name="verify-vmware-settings"></a>Verificar as configurações do VMware

1. [Verifique](migrate-support-matrix-vmware.md#vmware-requirements) Requisitos do servidor VMware para avaliação.
2. [Certifique-se](migrate-support-matrix-vmware.md#port-access) de que as portas de que necessita estão abertas no VCenter Server.
3. Em vCenter Server, verifique se sua conta tem permissões para criar uma VM usando um arquivo OVA. Você implanta o dispositivo migrações para Azure como uma VM VMware, usando um arquivo OVA.


### <a name="set-up-an-account-for-assessment"></a>Configurar uma conta para avaliação

As migrações para Azure precisam acessar o vCenter Server para descobrir VMs para avaliação e migração sem agente.

- Se planeia descobrir aplicações ou visualizar a dependência de forma sem agente, crie uma conta vCenter Server com acesso apenas de leitura, juntamente com privilégios habilitados para **máquinas virtuais** > **Operações de Hóspedes**.

  ![privilégios de conta de vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Se você não estiver planejando fazer a descoberta de aplicativos e a visualização de dependência sem agente, configure uma conta somente leitura para o vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Verificar as configurações do dispositivo para avaliação

Antes de configurar o dispositivo migrações para Azure e iniciar a avaliação no próximo tutorial, prepare-se para a implantação do dispositivo.

1. [Verifique os](migrate-appliance.md#appliance---vmware) requisitos do aparelho para vMware VMs.
2. [Reveja](migrate-appliance.md#url-access) os URLs Azure a que o aparelho terá de aceder. Se você estiver usando um firewall ou proxy baseado em URL, verifique se ele permite o acesso às URLs necessárias.
3. [Reveja](migrate-appliance.md#collected-data---vmware) se o aparelho irá recolher durante a descoberta e avaliação.
4. [Note](migrate-support-matrix-vmware.md#port-access) os requisitos de acesso à porta do aparelho.




## <a name="prepare-for-agentless-vmware-migration"></a>Preparar para a migração do VMware sem agente

Examine os requisitos para a migração sem agente de VMs VMware.

1. [Revisão](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) Requisitos do servidor VMware, e as [permissões](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) que o Azure Migrate precisa para aceder ao vCenter Server para migração sem agente usando a Migração do Servidor Migratório De Migração de Migração de Migração de Migração de Migração de Migração De Migração De Migração De Migração.
2. [Reveja](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) os requisitos para VMware VMs que pretende migrar para Azure usando migração sem agente.
4. [Reveja](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) os requisitos para a utilização do aparelho Azure Migrate para migração sem agente.
5. Note o [acesso url](migrate-appliance.md#url-access) e o [acesso portuário](migrate-support-matrix-vmware-migration.md#agentless-ports) necessário para a migração sem agente.


## <a name="prepare-for-agent-based-vmware-migration"></a>Preparar para a migração VMware baseada em agente

Reveja os requisitos para a [migração baseada em agentes](server-migrate-overview.md) de VMware VMs.

1. [Revisão](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Requisitos do servidor VMware, e as permissões que o Azure Migrate precisa para aceder ao vCenter Server para migração baseada em agentes usando a Migração do Servidor Migratório De Migração de Migração de Migração de Migração de Migração de Migração de Migração de Migração De Migração De Migração De Migração.
2. [Reveja](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) os requisitos para VMware VMs que pretende migrar para Azure utilizando a migração baseada em agentes, incluindo a instalação do serviço mobility em cada VM que pretende migrar.
3. As migrações baseadas em agente usam um dispositivo de replicação:
    - [Reveja](migrate-replication-appliance.md#appliance-requirements) os requisitos de implantação do aparelho de replicação e as [opções](migrate-replication-appliance.md#mysql-installation) para instalar o MySQL no aparelho.
    - Reveja os requisitos [de URL](migrate-replication-appliance.md#url-access) e de acesso à [porta](migrate-replication-appliance.md#port-access) para o aparelho de replicação.
    
## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Configure as permissões do Azure.
> * VMware preparado para avaliação e migração.


Continue no segundo tutorial para configurar um projeto de migrações para Azure e avaliar as VMs do VMware para migração para o Azure.

> [!div class="nextstepaction"]
> [Avaliar VMware VMs](./tutorial-assess-vmware.md)
