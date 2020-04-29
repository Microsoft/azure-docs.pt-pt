---
title: Definir a margem para a descoberta vmware vm com azure migrate
description: Descreve como definir o âmbito de descoberta para a avaliação vmware vm e migração com Azure Migrate.
ms.topic: how-to
ms.date: 03/23/2020
ms.openlocfilehash: 29b3077ead168cef2790468d6ac62d1c59c24c11
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80337639"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Definir âmbito de descoberta para VMware VMs

Este artigo descreve como limitar o âmbito de descoberta para VMware VMs que são descobertos pelo [aparelho Migratório Azure](migrate-appliance-architecture.md).

O aparelho Azure Migrate descobre vMs no local quando: 

- Utilize a ferramenta de avaliação do [servidor Azure Migrate:Server](migrate-services-overview.md#azure-migrate-server-assessment-tool) para avaliar vMware VMs para migração para Azure.
- Utilize a ferramenta [Migração Azure Migrate:Server](migrate-services-overview.md#azure-migrate-server-migration-tool) para [migração sem agentes](server-migrate-overview.md) de VMware VMware VMs para Azure.

## <a name="set-discovery-scope"></a>Definir o âmbito da descoberta


Depois de configurar o aparelho Azure Migrate para avaliação ou migração de VMware VMs, o aparelho começa a descobrir VMs e a enviar metadados VM para o Azure. Antes de ligar o aparelho ao VCenter Server para ser descoberto, pode definir o âmbito de descoberta para limitar a descoberta aos centros de dados do VCenter Server, clusters, uma pasta de clusters, anfitriões, uma pasta de anfitriões ou VMs individuais.

Para definir o âmbito, atribui permissões na conta que o Azure Migrate está a usar para aceder ao vCenter Server.

## <a name="create-a-vcenter-user-account"></a>Criar uma conta de utilizador vCenter

Se ainda não criou uma conta de utilizador vCenter que o aparelho Azure Migrate usa para descobrir, avaliar e migrar VMware VMware VMs, faça-o primeiro.

1.    Inicie sessão no VSphere Web Client como administrador do VCenter Server.
2.    Selecione**utilizadores e grupos SSO**da **Administração** > e clique no separador **Utilizadores.**
3.    Selecione o ícone **Novo Utilizador.**
4.    Preencha as novas informações do utilizador > **OK**.

As permissões da conta dependem do que estás a implementar.

**Funcionalidade** | **Permissões de conta**
--- | ---
[Avaliar](tutorial-assess-vmware.md)| A conta precisa de acesso só para leitura.
[Detetar aplicações/funções/funcionalidades](how-to-discover-applications.md) | A conta necessita de acesso apenas para leitura, com privilégios habilitados para máquinas virtuais > Operações de Hóspedes.
[Analisar dependências (sem agente)](how-to-create-group-machine-dependencies-agentless.md) | A conta necessita de acesso apenas para leitura, com privilégios habilitados para máquinas virtuais > Operações de Hóspedes.
[Migrar (sem agente)](tutorial-migrate-vmware.md) | Precisa de um papel que tenha as permissões certas.<br/><br/> Para criar uma função, inicie sessão no VSphere Web Client como administrador do VCenter Server. Selecione a instância do servidor vCenter > **Criar a função**. Especifique um nome de papel, por exemplo <em>Azure_Migrate,</em>e atribua as [permissões necessárias](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) ao papel.


## <a name="assign-permissions-on-vcenter-objects"></a>Atribuir permissões em objetos vCenter

Pode atribuir permissões em objetos de inventário utilizando um de dois métodos:

- Atribuir um papel com as permissões necessárias, à conta que está a usar, para todos os objetos-mãe que hospedam VMs que deseja descobrir/migrar.
- Em alternativa, pode atribuir a função e a conta do utilizador ao nível do datacenter e propagá-las aos objetos infantis. Em seguida, dê à conta um papel **de não acesso,** para cada objeto que você não quer descobrir/migrar. Não recomendamos esta abordagem, uma vez que é complicada, e pode expor controlos de acesso, porque cada novo objeto infantil é automaticamente concedido acesso herdado do progenitor.

Para atribuir uma função à conta que está a utilizar para todos os objetos relevantes, faça o seguinte:

- **Para avaliação**: Para avaliação de VM, aplique a função **de Leitura apenas** na conta de utilizador vCenter para todos os objetos-mãe que hospedam VMs que deseja descobrir. Objetos-mãe incluídos: hospedeiro, pasta de anfitriões, cluster e pasta de clusters na hierarquia, até ao datacenter. Propagaestas permissões a objetos infantis na hierarquia.

    ![Atribuir permissões](./media/tutorial-assess-vmware/assign-perms.png)

- **Para a migração sem agente**: Para uma migração sem agente, aplique uma função definida pelo utilizador com [permissões necessárias](migrate-support-matrix-vmware-migration.md#agentless-vmware-servers) à conta do utilizador, para todos os objetos-mãe que hospedam VMs que deseja descobrir. Pode nomear o papel <em>Azure_Migrate.</em>

### <a name="scope-support"></a>Suporte de âmbito

Atualmente, a ferramenta de Avaliação do Servidor não pode descobrir VMs se a conta vCenter tiver acesso concedido ao nível da pasta VCenter VM. As pastas de anfitriões e agrupamentos são suportadas.

Se pretender analisar a sua descoberta através de pastas VM, complete o procedimento seguinte para garantir que a conta vCenter tem acesso apenas de leitura atribuído a um nível VM.

1. Atribuir permissões de leitura apenas em todos os VMs nas pastas VM que pretende procurar para ser descoberta.
2. Conceder acesso apenas a todos os objetos-mãe que acolhem VMs.
    - Todos os objetos-mãe (anfitrião, pasta de anfitriões, cluster, pasta de clusters) na hierarquia até ao datacenter estão incluídos.
    - Não precisas de propagar as permissões a todos os objetos infantis.
3. Utilize as credenciais para descoberta selecionando o datacenter como Âmbito de **Recolha**. A configuração de controlo de acesso baseada em funções garante que a conta de utilizador vCenter correspondente tem acesso apenas a VMs específicos do arrendatário.


## <a name="next-steps"></a>Passos seguintes

[Instale o aparelho](how-to-set-up-appliance-vmware.md) e [inicie a descoberta contínua](how-to-set-up-appliance-vmware.md#start-continuous-discovery-by-providing-vcenter-server-and-vm-credential).
