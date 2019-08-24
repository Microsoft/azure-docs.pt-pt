---
title: Controle de acesso baseado em função e Privileged Identity Management na Austrália do Azure
description: Orientação sobre a implementação do controle de acesso baseado em função e Privileged Identity Management nas regiões australianas para atender aos requisitos específicos da política, regulamentos e legislações do governo australiano.
author: Galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: e2a94f82e4830bd1e9c96039f5ef8fe6546b0d0b
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982665"
---
# <a name="role-based-access-control-rbac-and-privileged-identity-management-pim"></a>Controle de acesso baseado em função (RBAC) e Privileged Identity Management (PIM)

O gerenciamento de privilégios administrativos é uma etapa crítica para garantir a segurança em qualquer ambiente de ti. A restrição do privilégio administrativo por meio do uso de segurança de privilégios mínimos é um requisito do [ISM acsc](https://acsc.gov.au/infosec/ism/index.htm) e faz parte da lista de recomendações de segurança do [acsc Essential 8](https://www.acsc.gov.au/infosec/mitigationstrategies.htm) .

A Microsoft fornece um conjunto de controles para implementar o acesso just-in-time e apenas o suficiente no Microsoft Azure. Compreender esses controles é essencial para uma postura de segurança eficaz na nuvem. Este guia fornecerá uma visão geral dos próprios controles e das principais considerações de design ao implementá-los.

## <a name="role-based-access-control-rbac"></a>Controlo de Acesso Baseado em Funções (RBAC)

O controle de acesso baseado em função é fundamental para o gerenciamento de acesso a todos os recursos dentro de Microsoft Azure e o gerenciamento de Azure Active Directory (AD do Azure). O RBAC pode ser implementado junto com vários recursos complementares disponíveis no Azure. Este artigo se concentra na implementação de RBAC efetivo usando o Azure Grupos de Gerenciamento, grupos de Azure Active Directory e o Azure Privileged Identity Management (PIM).

Em um alto nível, implementar o RBAC requer três componentes:

![RBAC-visão geral](media/rbac-overview.png)

* **Entidades de segurança**: Uma entidade de segurança pode ser qualquer uma das seguintes opções: um usuário, um grupo, [entidades de serviço](https://docs.microsoft.com/en-us/azure/active-directory/develop/app-objects-and-service-principals)ou uma [identidade gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). As entidades de segurança devem receber privilégios usando grupos de Azure Active Directory.

* **Definições de função**: Uma definição de função, também chamada de função, é uma coleção de permissões. Essas permissões definem as operações que podem ser executadas pelas entidades de segurança atribuídas à definição de função. Essa funcionalidade é fornecida pelas funções de recursos do Azure e Azure Active Directory funções de administrador. O Azure vem com um conjunto de funções internas (link) que podem ser aumentadas com funções personalizadas.

* **Escopo**: O escopo é o conjunto de recursos do Azure ao qual uma definição de função se aplica. As funções do Azure podem ser atribuídas a recursos do Azure usando o Azure Grupos de Gerenciamento.

Esses três componentes se combinam para conceder às entidades de segurança o acesso definido nas definições de função a todos os recursos que se enquadram no escopo do Azure Grupos de Gerenciamento, isso é chamado de atribuição de função. Várias definições de função podem ser atribuídas a uma entidade de segurança e várias entidades de segurança podem ser atribuídas a um único escopo.

### <a name="azure-active-directory-groups"></a>Grupos de Azure Active Directory

Ao atribuir privilégios a indivíduos ou equipes, sempre que possível, a atribuição deve ser vinculada a um grupo de Azure Active Directory e não atribuída diretamente ao usuário em questão. Essa é a mesma prática recomendada herdada de implementações de Active Directory locais. Quando for possível Azure Active Directory grupos devem ser criados por equipe, complemento à estrutura lógica do Grupos de Gerenciamento do Azure que você criou.

Em um cenário de nuvem híbrida, os grupos de segurança locais do Windows Server Active Directory podem ser sincronizados com sua instância do Azure Active Directory. Se você já tiver implementado o RBAC local usando esses grupos de segurança do Windows Server Active Directory, esses grupos, uma vez sincronizados, poderão ser usados para implementar o RBAC para os recursos do Azure. Caso contrário, seu ambiente de nuvem pode ser visto como um Tablet limpo para projetar e implementar um plano de gerenciamento de privilégios robusto criado em relação à sua implementação de Azure Active Directory.

### <a name="azure-resource-roles-versus-azure-active-directory-administrator-roles"></a>Funções de recurso do Azure versus Azure Active Directory funções de administrador

O Microsoft Azure oferece uma ampla variedade de funções internas para [recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) e [Administração de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles). Os dois tipos de função fornecem acesso granular específico aos recursos do Azure ou aos administradores do Azure AD. É importante observar que as funções de recurso do Azure não podem ser usadas para fornecer acesso administrativo ao Azure AD e as funções do Azure AD não fornecem acesso específico aos recursos do Azure.

Alguns exemplos dos tipos de acesso que podem ser atribuídos a um recurso do Azure usando uma função interna são:

* Permitir a um utilizador gerir máquinas virtuais numa subscrição e a outro utilizador gerir redes virtuais
* Permitir a um grupo DBA gerir bases de dados SQL numa subscrição
* Permitir a um utilizador gerir todos os recursos num grupo de recursos, como máquinas virtuais, sites e sub-redes
* Permitir a uma aplicação aceder a todos os recursos num grupo de recursos

Exemplos dos tipos de acesso que podem ser atribuídos para a administração do Azure AD são:

* Permitir que a equipe de assistência técnica Redefina senhas de usuário
* Permitir que a equipe convide usuários externos para uma instância do Azure AD para colaboração B2B
* Permitir acesso de leitura à equipe administrativa para entrada e relatórios de auditoria
* Permitir que a equipe gerencie todos os usuários e grupos, incluindo a redefinição de senhas.

É importante entender a lista completa de ações permitidas que uma função interna fornece para garantir que o acesso indevido ao não seja concedido. A lista de funções internas e o acesso que elas fornecem estão em constante evolução, a lista completa das funções e suas definições podem ser exibidas ao revisar a documentação vinculada acima ou usando o cmdlet Azure PowerShell:

```PowerShell
PS C:\> Get-AzRoleDefinition

Name             : AcrDelete
Id               : <<RoleID>>
IsCustom         : False
Description      : acr delete
Actions          : {Microsoft.ContainerRegistry/registries/artifacts/delete}
NotActions       : {}
DataActions      : {}
NotDataActions   : {}
AssignableScopes : {/}
...
```

ou o comando CLI do Azure:

```bash
PS C:\> az role definition list
[
  {
    "assignableScopes": [
      "/"
    ],
    "description": "acr delete",
    "id": "/subscriptions/49b12d1b-4030-431c-8448-39056021c4ab/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
    "permissions": [
      {
        "actions": [
          "Microsoft.ContainerRegistry/registries/artifacts/delete"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "AcrDelete",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  },
...
```

Também é possível criar funções personalizadas de recursos do Azure, conforme necessário. Essas funções personalizadas podem ser criadas no portal do Azure, por meio do PowerShell ou do CLI do Azure. Ao criar funções personalizadas, é vital garantir que a finalidade da função seja exclusiva e que sua função ainda não seja fornecida por uma função de recurso do Azure existente. Isso reduz a complexidade de gerenciamento em andamento e reduz o risco de entidades de segurança receberem privilégios desnecessários. Um exemplo seria criar uma função de recurso personalizada do Azure que se situa entre as funções internas de recursos do Azure, "colaborador de máquina virtual" e "logon de administrador de máquina virtual".

A função personalizada pode ser baseada na função de colaborador existente, que concede o seguinte acesso:

| Recurso do Azure | Nível de Acesso |
| --- | --- |
| Virtual Machines | Pode gerenciar, mas não pode acessar |
| Rede virtual conectada à VM | Não é possível acessar |
| Armazenamento anexado à VM | Não é possível acessar |
|

A função personalizada pode preservar esse acesso básico, mas permitir aos usuários designados alguns privilégios adicionais básicos para modificar a configuração de rede das máquinas virtuais.

As funções de recurso do Azure também têm a vantagem de poder ser atribuídas a recursos por meio do Azure Grupos de Gerenciamento.

### <a name="azure-management-groups"></a>Grupos de Gestão do Azure

O Azure Grupos de Gerenciamento pode ser usado por uma organização para gerenciar a atribuição de funções a todas as assinaturas e seus recursos em uma locação do Azure. Os Grupos de Gerenciamento do Azure foram projetados para permitir que você crie hierarquias de gerenciamento, incluindo a capacidade de mapear sua estrutura organizacional hierarquicamente, dentro do Azure. Criar unidades de negócios organizacionais como entidades lógicas separadas permite que as permissões sejam aplicadas em uma organização com base nos requisitos específicos de cada equipe. O Azure Grupos de Gerenciamento pode ser usado para definir uma hierarquia de gerenciamento de até seis níveis de profundidade.

![Grupos de Gestão](media/management-groups.png)

Os Grupos de Gerenciamento do Azure são mapeados para assinaturas do Azure em uma locação do Azure. Isso permite que uma organização segrege os recursos do Azure que pertencem a unidades de negócios específicas e forneça um nível de controle granular sobre o gerenciamento de custos e a atribuição de privilégios.

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

A Microsoft implementou JIT (just-in-time) e JEA (Just-suficiente-Access) por meio do Azure Privileged Identity Management. Esse serviço permite que a equipe administrativa controle, gerencie e monitore o acesso privilegiado aos recursos do Azure. O PIM permite que entidades de segurança sejam "qualificadas" para uma função por equipe administrativa, permitindo que os usuários solicitem a ativação da função por meio do portal do Azure ou por meio de cmdlets do PowerShell. Por padrão, a atribuição de função pode ser ativada por um período entre 1 e 72 horas. Se necessário, o usuário pode solicitar uma extensão para sua atribuição de função e a opção para tornar a atribuição de função permanente existe. Opcionalmente, o requisito de autenticação multifator pode ser imposto quando os usuários solicitam a ativação de suas funções qualificadas. Depois que o período alocado da ativação da função expirar, a entidade de segurança não terá mais o acesso privilegiado concedido pela função.

O uso do PIM impede os problemas comuns de atribuição de privilégio que podem ocorrer em ambientes que não usam o acesso just-in-time ou que não realizam auditorias rotineiras de atribuição de privilégios. Um problema comum é a atribuição de privilégios elevados que estão sendo esquecidos e restam muito tempo depois que a tarefa que requer privilégios elevados tiver sido concluída. Outro problema é a proliferação de privilégios elevados em um ambiente por meio da clonagem do acesso atribuído a uma entidade de segurança ao configurar outras entidades de segurança semelhantes.

## <a name="key-design-considerations"></a>Considerações de design-chave

Ao criar uma estratégia de RBAC com a intenção de impor a segurança de privilégios mínimos, os seguintes requisitos de segurança devem ser considerados:

* As solicitações de acesso privilegiado são validadas
* Os privilégios administrativos são restritos ao mínimo de acesso necessário para executar as tarefas específicas
* Os privilégios administrativos são restritos ao período mínimo de tempo necessário para executar as tarefas específicas
* Revisões regulares de privilégios administrativos concedidos são tomadas

O processo de criação de uma estratégia de RBAC exigirá uma revisão detalhada das funções de negócios para entender a diferença no acesso entre funções de negócios distintas e o tipo e a frequência de trabalho que exige privilégios elevados. A diferença na função entre um operador de backup, um administrador de segurança e um auditor exigirá níveis diferentes de acesso em momentos diferentes, com níveis variados de revisão em andamento.

## <a name="validate-requests-for-access"></a>Validar solicitações de acesso

Privilégios elevados devem ser explicitamente aprovados. Para dar suporte a isso, um processo de aprovação deve ser desenvolvido e a equipe apropriada tornou-se responsável pela validação de que todas as solicitações de privilégios adicionais são legítimas. Privileged Identity Management fornece várias opções para aprovar a atribuição de função. Uma solicitação de ativação de função pode ser configurada para permitir aprovação própria ou ser restringida e exigir que os aprovadores nomeados revisem e aprovem manualmente todas as solicitações de ativação de função. As solicitações de ativação também podem ser configuradas para exigir informações adicionais de suporte incluídas na solicitação de ativação, como números de tíquetes.

### <a name="restrict-privilege-based-on-duties"></a>Restringir o privilégio com base em tarefas

Restringir o nível de privilégio concedido a entidades de segurança é essencial, pois a atribuição de privilégios é um vetor de ataque de segurança de ti comum. Os tipos de recursos gerenciados e as equipes responsáveis devem ser avaliados para que o nível mínimo de privilégios necessário para as tarefas diárias possa ser atribuído. Privilégios adicionais que vão além daqueles necessários para as tarefas diárias devem ser concedidos apenas pelo período de tempo necessário para executar uma tarefa específica. Um exemplo disso seria fornecer acesso de "colaborador" ao administrador de um cliente, mas permitir que eles solicitem permissões de "proprietário" para um recurso do Azure para uma tarefa específica que exija um acesso temporário de alto nível.

Isso garante que cada administrador individual só tenha acesso elevado pelo período de tempo mais curto. A adesão a essas práticas reduz a superfície de ataque geral para qualquer infraestrutura de ti de organizações.

### <a name="regular-evaluation-of-administrative-privilege"></a>Avaliação regular de privilégio administrativo

É vital que as entidades de segurança em um ambiente sejam auditadas rotineiramente para garantir que o nível correto de privilégio esteja atribuído no momento. O Microsoft Azure fornece vários meios para auditar e avaliar os privilégios atribuídos às entidades de segurança do Azure. Privileged Identity Management permite que a equipe administrativa execute periodicamente "revisões de acesso" das funções concedidas a entidades de segurança. Uma revisão de acesso pode ser executada para auditar a atribuição de função de recurso do Azure e Azure Active Directory atribuição de função administrativa. Uma revisão de acesso pode ser configurada com as seguintes propriedades:

* Revisar o **nome e examinar as datas de início e término**: As revisões devem ser configuradas para serem longas o suficiente para que os usuários designados as concluam.

* **Função a ser**revisada: Cada revisão de acesso se concentra em uma única função do Azure.

* Revisores nomeados: Há três opções para executar uma revisão. Você pode atribuir a revisão a outra pessoa para concluir, você mesmo pode fazer isso ou pode fazer com que cada usuário revise seu próprio acesso.

* **Exigir que os usuários forneçam um motivo para o acesso**: Os usuários podem ser solicitados a inserir um motivo para manter seu nível de privilégio ao concluir a revisão de acesso.

O progresso das revisões de acesso pendentes pode ser monitorado a qualquer momento por meio de um Dashboard no portal do Azure. O acesso à função que está sendo revisada permanecerá inalterado até que a revisão de acesso seja concluída. Também é possível auditar [](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-use-audit-log) todas as atribuições e ativações de usuário PIM em um período de tempo indicado.

## <a name="next-steps"></a>Passos seguintes

Examine o artigo sobre [monitoramento do sistema na Austrália do Azure](system-monitor.md).
