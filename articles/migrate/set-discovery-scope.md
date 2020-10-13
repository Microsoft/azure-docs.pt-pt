---
title: Definir o âmbito para a descoberta VMware VM com Azure Migrate
description: Descreve como definir o âmbito de descoberta para a avaliação e migração de VMware VMware com Azure Migrate.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: dfc9c12edd93fc720ef716fd43b04e0c193d5803
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88919731"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Definir o âmbito de descoberta para VMware VMs

Este artigo descreve como limitar o âmbito de aplicação dos VMware VMs que descobre quando:

- Descobrir VMs com o [aparelho Azure Migrate](migrate-appliance-architecture.md) quando estiver a utilizar a ferramenta Azure Migrate:Server Assessment.
- Descobrir VMs com o [aparelho Azure Migrate](migrate-appliance-architecture.md) quando estiver a utilizar a ferramenta migração Azure Migrate:Server Migration, para migração sem agentes de VMware VMs para Azure.

Quando configurar o aparelho, ele liga-se ao servidor vCenter e inicia a descoberta. Antes de ligar o aparelho ao servidor vCenter, pode limitar a descoberta a centros de dados do vCenter Server, agrupamentos, uma pasta de clusters, anfitriões, uma pasta de anfitriões ou VMs individuais. Para definir o âmbito, atribui permissões na conta que o aparelho utiliza para aceder ao servidor vCenter.

## <a name="before-you-start"></a>Antes de começar

Se ainda não criou uma conta de utilizador vCenter que a Azure Migrate utiliza para a descoberta, faça-o agora para [avaliação](tutorial-prepare-vmware.md#set-up-permissions-for-assessment) ou [migração sem agentes.](tutorial-prepare-vmware.md#assign-permissions-to-an-account)


## <a name="assign-permissions-and-roles"></a>Atribuir permissões e funções

Pode atribuir permissões em objetos de inventário VMware utilizando um de dois métodos:

- Na conta utilizada pelo aparelho, atribua uma função com as permissões necessárias nos objetos que pretende utilizar.
- Em alternativa, atribua uma função à conta ao nível do datacenter e propagou-se aos objetos da criança. Em seguida, dê à conta uma função **de Não acesso,** para cada objeto que não deseja no âmbito. Não recomendamos esta abordagem, uma vez que é complicada, e pode expor controlos de acesso, porque cada novo objeto infantil é automaticamente concedido acesso herdado do progenitor.

Não é possível analisar a descoberta do inventário ao nível da pasta VM do vCenter. Se precisar de descobrir os VM numa pasta VM, crie um utilizador e conceda acesso individualmente a cada VM necessário. As pastas de hospedeiro e cluster são suportadas.


### <a name="assign-a-role-for-assessment"></a>Atribuir um papel de avaliação

1. Na conta vCenter do aparelho que está a usar para descoberta, aplique a função **Read-only** para todos os objetos-mãe que hospedam VMs que pretende descobrir e avaliar (anfitrião, cluster, pasta de anfitriões, pasta de clusters, até ao datacenter).
2. Propagar estas permissões a objetos infantis na hierarquia.

    ![Atribuir permissões](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Atribuir um papel para a migração sem agente

1. Na conta vCenter do aparelho que está a usar para migração, aplique uma função definida pelo utilizador que tenha as [permissões necessárias](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless), a todos os objetos-mãe que acolhem VMs que pretende descobrir e migrar.
2. Pode nomear o papel com algo mais fácil de identificar. Por exemplo, <em>Azure_Migrate.</em>

## <a name="work-around-vm-folder-restriction"></a>Trabalhe em torno da restrição de pasta VM

Atualmente, a ferramenta de Avaliação do Servidor não consegue descobrir VMs se o acesso for concedido ao nível da pasta VM do vCenter. Se quiser fazer a sua descoberta e avaliação por pastas VM, utilize esta solução alternativa.

1. Atribua permissões apenas de leitura em todos os VMs localizados nas pastas VM que pretende testar para a descoberta e avaliação.
2. Conceder acesso apenas de leitura a todos os objetos-mãe que acolhem o anfitrião dos VMs, cluster, pasta de anfitriões, pasta de clusters, até ao datacenter). Não é preciso propagar as permissões a todos os objetos infantis.
3. Para utilizar as credenciais para descoberta, selecione o datacenter como **Âmbito de Recolha**.


A configuração do controlo de acesso baseado em funções garante que a conta de utilizador correspondente do vCenter tenha acesso apenas a VMs específicos do arrendatário.


## <a name="next-steps"></a>Passos seguintes

[Configurar o aparelho](how-to-set-up-appliance-vmware.md)
