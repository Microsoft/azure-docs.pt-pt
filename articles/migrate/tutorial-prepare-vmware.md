---
title: Prepare VMware VMs para avaliação/migração com a Migração Azure
description: Saiba como se preparar para avaliação/migração de VMware VMs com Azure Migrate.
ms.topic: tutorial
ms.date: 11/19/2019
ms.custom: mvc
ms.openlocfilehash: 2e8aa72300c840832168138015e0a01ab054f954
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619434"
---
# <a name="prepare-vmware-vms-for-assessment-and-migration-to-azure"></a>Preparar VMs VMware para avaliação e migração para o Azure

Este artigo ajuda-o a preparar-se para avaliação e/ou migração de VMware VMware no local para Azure usando [o Azure Migrate](migrate-services-overview.md).



Este tutorial é o primeiro de uma série que mostra como avaliar e migrar VMware VMs. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Prepare Azure para trabalhar com a Azure Migrate.
> * Prepare o VMware para avaliação vm com a ferramenta de avaliação de migração de emigração Azure:Servidor.
> * Prepare vMware para migração vm com a ferramenta migração de migração Azure Migrate:Server. 

> [!NOTE]
> Os tutoriais mostram-lhe o caminho de implantação mais simples para um cenário. São úteis quando se aprende a configurar uma implantação, e como uma rápida prova de conceito. Os tutoriais usam opções padrão sempre que possível, e não mostram todas as configurações e caminhos possíveis. 

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de começar.


## <a name="prepare-azure"></a>Preparar o Azure

Precisa destas permissões para estas tarefas em Azure, antes de poder avaliar ou migrar VMware VMs.

**Tarefa** | **Detalhes** 
--- | --- 
**Criar um projeto Azure Migrate** | A sua conta Azure necessita de permissões do Colaborador ou proprietário para criar um projeto. 
**Registe os fornecedores de recursos** | A Azure Migrate utiliza um aparelho azure migratório leve para descobrir e avaliar VMware VMs, e migrar para Azure com Azure Migrate:Server Assessment.<br/><br/> Durante o registo do aparelho, os fornecedores de recursos são registados com a assinatura escolhida no aparelho. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Para registar os fornecedores de recursos, necessita de uma função de Colaborador ou Proprietário na subscrição.
**Criar aplicativos Azure AD** | Ao registar o aparelho, a Azure Migrate cria aplicações azure Ative Directory (Azure AD). <br/><br/> - A primeira aplicação é utilizada para a comunicação entre os agentes que estão a trabalhar no aparelho e os respetivos serviços em funcionamento no Azure.<br/><br/> - A segunda aplicação é utilizada exclusivamente para aceder ao KeyVault criado na subscrição do utilizador para a migração VMware VM sem agente. [Saiba mais](migrate-appliance-architecture.md#appliance-registration).<br/><br/> Precisa de permissões para criar aplicações Azure AD (disponíveis no Desenvolvedor de Aplicações).
**Criar um cofre chave** | Para migrar VMware VMs usando migração sem agente, a Azure Migrate cria um Cofre chave para gerir as chaves de acesso da conta de armazenamento de replicação na sua subscrição.<br/><br/> Para criar o cofre, você precisa de permissões de atribuição de papéis no grupo de recursos em que o projeto Azure Migrate reside.




### <a name="assign-permissions-to-create-project"></a>Atribuir permissões para criar projeto

1. No portal Azure, abra a subscrição e selecione controlo de **acesso (IAM)**.
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões.
3. Deve ter permissões de **Contribuinte** ou **Proprietário.**
    - Se acabou de criar uma conta Azure gratuita, é o proprietário da sua subscrição.
    - Se não for o proprietário da subscrição, trabalhe com o proprietário para atribuir o papel.

### <a name="assign-permissions-to-register-the-appliance"></a>Atribuir permissões para registar o aparelho

Para registar o aparelho, atribua permissões para a Azure Migrate criar as aplicações Azure AD durante o registo do aparelho. As permissões podem ser atribuídas utilizando um dos seguintes métodos:

- **Permissões de concessão**: Um inquilino/administrador global pode conceder permissões aos utilizadores do inquilino, para criar e registar aplicações da Azure AD.
- Atribuir a função de promotor de **aplicações**: Um inquilino/administrador global pode atribuir a função de Desenvolvedor de Aplicações (que tem as permissões) à conta.

> [!NOTE]
> - As aplicações não têm outras permissões de acesso na subscrição que não as descritas acima.
> - Só precisa destas permissões quando regista um novo aparelho. Pode retirar as permissões depois de o aparelho ser montado.


#### <a name="grant-account-permissions"></a>Concessão de permissões de conta

Se quiser que o inquilino/administrador global conceda permissões, faça-o da seguinte forma:

1. Em Azure AD, o inquilino/administrador global deve navegar para**as Definições**de**Utilizadores** > de **Diretório** > Ativo Azure .
2. O administrador deve definir **os registos** da App para **Sim**. Esta é uma definição padrão que não é sensível. [Saiba mais](https://docs.microsoft.com/azure/active-directory/develop/active-directory-how-applications-are-added#who-has-permission-to-add-applications-to-my-azure-ad-instance).

    ![Permissões da AD Azure](./media/tutorial-prepare-vmware/aad.png)



#### <a name="assign-application-developer-role"></a>Atribuir papel de desenvolvedor de aplicações

Em alternativa, o inquilino/administrador global pode atribuir a função de Desenvolvedor de Aplicações a uma conta. [Saiba mais](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal) sobre atribuir um papel.

### <a name="assign-permissions-to-create-a-key-vault"></a>Atribuir permissões para criar um Cofre chave

Para permitir que o Azure Migrate crie um Cofre chave, atribua permissões da seguinte forma:

1. No grupo de recursos no portal Azure, selecione controlo de **acesso (IAM)**.
2. No **Check access,** encontre a conta relevante e clique nela para visualizar permissões.

    - Para executar a avaliação do servidor, as permissões **do Contribuinte** são suficientes.
    - Para executar a migração do servidor sem agente, deve ter permissões **de Proprietário** (ou **Colaborador** e Administrador de Acesso **ao Utilizador).**

3. Se não tiver as permissões necessárias, solicite-as ao proprietário do grupo de recursos.



## <a name="prepare-for-vmware-vm-assessment"></a>Preparar para avaliação VMware VM

Para se preparar para a avaliação vmware VM, você precisa:

- **Verifique as definições do VMware**. Certifique-se de que o vCenter Server e os VMs que pretende migrar cumprem os requisitos.
- **Criar uma conta para avaliação**. A Azure Migrate utiliza esta conta para aceder ao vCenter Server, para descobrir VMs para avaliação.
- **Verifique os requisitos do aparelho**. Verifique os requisitos de implantação do aparelho Azure Migrate, antes de o utilizar.

### <a name="verify-vmware-settings"></a>Verifique as definições de VMware

1. [Verifique](migrate-support-matrix-vmware.md#vmware-requirements) Requisitos do servidor VMware para avaliação.
2. [Certifique-se](migrate-support-matrix-vmware.md#port-access) de que as portas de que necessita estão abertas no VCenter Server.
3. No vCenter Server, certifique-se de que a sua conta tem permissões para criar um VM utilizando um ficheiro OVA. Implementa o aparelho Azure Migrate como VMware VM, utilizando um ficheiro OVA.


### <a name="set-up-an-account-for-assessment"></a>Criar uma conta para avaliação

A Azure Migrate precisa de aceder ao vCenter Server para descobrir VMs para avaliação e migração sem agente.

- Se planeia descobrir aplicações ou visualizar a dependência de forma sem agente, crie uma conta vCenter Server com acesso apenas de leitura, juntamente com privilégios habilitados para as**Operações de Hóspedes**das **Máquinas** > Virtuais .

  ![privilégios de conta vCenter Server](./media/tutorial-prepare-vmware/vcenter-server-permissions.png)

- Se não estiver a planear fazer a descoberta da aplicação e visualização da dependência sem agente, configurar uma conta apenas para leitura para o vCenter Server.

### <a name="verify-appliance-settings-for-assessment"></a>Verifique as definições do aparelho para avaliação

Antes de configurar o aparelho Azure Migrate e iniciar a avaliação no próximo tutorial, prepare-se para a colocação do aparelho.

1. [Verificar](migrate-appliance.md#appliance---vmware) Requisitos do aparelho Azure Migrate.
2. [Reveja](migrate-appliance.md#url-access) os URLs Azure a que o aparelho terá de aceder. Se estiver a utilizar uma firewall ou procuração baseada em URL, certifique-se de que permite o acesso aos URLs necessários.
3. [Reveja os dados](migrate-appliance.md#collected-data---vmware) que o aparelho recolhe durante a descoberta e avaliação.
4. [Note](migrate-support-matrix-vmware.md#port-access) os requisitos de acesso à porta do aparelho.




## <a name="prepare-for-agentless-vmware-migration"></a>Prepare-se para a migração vmware sem agente

Reveja os requisitos para a [migração sem agente](server-migrate-overview.md) de VMware VMs.

1. [Revisão](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) Requisitos do servidor VMware.
2. [Reveja as permissões](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) que o Azure Migrate precisa para aceder ao vCenter Server.
3. [Revisão](migrate-support-matrix-vmware-migration.md#agentless-vmware-vms) Requisitos vMs VMware.
4. [Reveja](migrate-support-matrix-vmware-migration.md#agentless-azure-migrate-appliance) os requisitos do aparelho Azure Migrate.
5. Note os requisitos de [acesso url](migrate-appliance.md#url-access) e acesso à [porta.](migrate-support-matrix-vmware-migration.md#agentless-ports)

## <a name="prepare-for-agent-based-vmware-migration"></a>Prepare-se para a migração vmware baseada em agente

Reveja os requisitos para a [migração baseada em agentes](server-migrate-overview.md) de VMware VMs.

1. [Revisão](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) Requisitos do servidor VMware.
2. [Reveja as permissões](migrate-support-matrix-vmware-migration.md#agent-based-vmware-servers) O Azure Migrate precisa de aceder ao vCenter Server.
2. [Revisão](migrate-support-matrix-vmware-migration.md#agent-based-vmware-vms) VMware VMs requisitos, incluindo a instalação do serviço mobility em cada VM que você quer migrar.
3. A migração baseada no agente utiliza um aparelho de replicação:
    - [Reveja](migrate-replication-appliance.md#appliance-requirements) os requisitos de implantação do aparelho de replicação.
    - [Reveja as opções](migrate-replication-appliance.md#mysql-installation) para instalar o MySQL no aparelho.
    - Reveja os requisitos [de URL](migrate-replication-appliance.md#url-access) e de acesso à [porta](migrate-replication-appliance.md#port-access) para o aparelho de replicação.
    
## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

> [!div class="checklist"]
> * Instale permissões Azure.
> * VMware preparado para avaliação e migração.


Continue até ao segundo tutorial para criar um projeto Azure Migrate, e avalie vMware vMs para migração para Azure.

> [!div class="nextstepaction"]
> [Avaliar VMware VMs](./tutorial-assess-vmware.md)
