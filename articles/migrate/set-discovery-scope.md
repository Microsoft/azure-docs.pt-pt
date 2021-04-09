---
title: Definir a margem para a descoberta de servidores em VMware com Azure Migrate
description: Descreve como definir o âmbito de descoberta para servidores hospedados na avaliação e migração de VMware com Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 29ac42da6560a717f12cd256fdd71282e0bd313f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104775362"
---
# <a name="set-discovery-scope-for-servers-in-vmware-environment"></a>Definir o âmbito de descoberta para servidores em ambiente VMware

Este artigo descreve como limitar o âmbito de descoberta para servidores em ambiente VMware quando estiver:

- Descobrir servidores com o [aparelho Azure Migrate](migrate-appliance-architecture.md) quando estiver a utilizar a ferramenta Azure Migrate: Discovery and assessment.
- Descobrir servidores com o [aparelho Azure Migrate](migrate-appliance-architecture.md) quando estiver a utilizar a ferramenta Azure Migrate:Server Migration, para migração sem agentes de servidores do ambiente VMware para Azure.

Quando configurar o aparelho, ele liga-se ao servidor vCenter e inicia a descoberta. Antes de ligar o aparelho ao servidor vCenter, pode limitar a descoberta a centros de dados do vCenter Server, agrupamentos, uma pasta de clusters, anfitriões, uma pasta de anfitriões ou servidores individuais. Para definir o âmbito, atribui permissões na conta que o aparelho utiliza para aceder ao servidor vCenter.

## <a name="before-you-start"></a>Antes de começar

Se ainda não criou uma conta de utilizador vCenter que a Azure Migrate utiliza para a descoberta, faça-o agora para [avaliação](./tutorial-discover-vmware.md#prepare-vmware) ou [migração sem agentes.](./migrate-support-matrix-vmware-migration.md#agentless-migration)


## <a name="assign-permissions-and-roles"></a>Atribuir permissões e funções

Pode atribuir permissões em objetos de inventário VMware utilizando um de dois métodos:

- Na conta utilizada pelo aparelho, atribua uma função com as permissões necessárias nos objetos que pretende utilizar.
- Em alternativa, atribua uma função à conta ao nível do datacenter e propagou-se aos objetos da criança. Em seguida, dê à conta uma função **de Não acesso,** para cada objeto que não deseja no âmbito. Não recomendamos esta abordagem, uma vez que é complicada, e pode expor controlos de acesso, porque cada novo objeto infantil é automaticamente concedido acesso herdado do progenitor.

Não é possível analisar a descoberta do inventário ao nível da pasta do servidor vCenter. Se precisar de mira para descobrir os servidores numa pasta, crie um utilizador e conceda acesso individualmente a cada servidor necessário. As pastas de hospedeiro e cluster são suportadas.


### <a name="assign-a-role-for-assessment"></a>Atribuir um papel de avaliação

1. Na conta vCenter do aparelho que está a usar para descoberta, aplique a função **Read-only** para todos os objetos-mãe que hospedam servidores que pretende descobrir e avaliar (anfitrião, cluster, pasta de anfitriões, pasta de clusters, até ao datacenter).
2. Propagar estas permissões a objetos infantis na hierarquia.

    ![Atribuir permissões](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Atribuir um papel para a migração sem agente

1. Na conta vCenter do aparelho que está a usar para migração, aplique uma função definida pelo utilizador que tenha as [permissões necessárias](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless), a todos os objetos-mãe que hospedam servidores que pretende descobrir e migrar.
2. Pode nomear o papel com algo mais fácil de identificar. Por exemplo, <em>Azure_Migrate.</em>

## <a name="work-around-for-server-folder-restriction"></a>Trabalhe em torno da restrição de pasta de servidor

Atualmente, a ferramenta Azure Migrate: Discovery and assessment não consegue descobrir servidores se o acesso for concedido ao nível da pasta do servidor vCenter. Se pretender estender a sua descoberta e avaliação através de pastas de servidor, utilize esta solução alternativa.

1. Atribua permissões apenas de leitura em todos os servidores localizados nas pastas que pretende procurar para a descoberta e avaliação.
2. Conceder acesso apenas de leitura a todos os objetos-mãe que hospedam os servidores hospedam, cluster, acolhe pasta, pasta de clusters, até ao datacenter). Não é preciso propagar as permissões a todos os objetos infantis.
3. Para utilizar as credenciais para descoberta, selecione o datacenter como **Âmbito de Recolha**.


A configuração do controlo de acesso baseada em funções garante que a conta de utilizador correspondente do vCenter tenha acesso apenas a servidores específicos do inquilino.


## <a name="next-steps"></a>Passos seguintes

[Configurar o aparelho](how-to-set-up-appliance-vmware.md)