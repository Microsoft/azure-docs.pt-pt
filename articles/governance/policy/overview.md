---
title: Descrição Geral do Azure Policy
description: O Azure Policy é um serviço no Azure utilizado para criar, atribuir e gerir definições de política no seu ambiente do Azure.
ms.date: 01/14/2021
ms.topic: overview
ms.openlocfilehash: df430586af2e701ec2881f6ea760095fd2ca79d0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98220733"
---
# <a name="what-is-azure-policy"></a>O que é o Azure Policy?

O Azure Policy ajuda a impor normas organizacionais e a avaliar o cumprimento em escala. Através do seu painel de conformidade, proporciona uma visão agregada para avaliar o estado geral do ambiente, com a capacidade de aprofundar a granularidade por recurso, por política. Também ajuda a levar os seus recursos ao cumprimento através da reparação a granel dos recursos existentes e da reparação automática de novos recursos.

Os casos de utilização comum para a Política Azure incluem a implementação da governação para a consistência dos recursos, a conformidade regulamentar, a segurança, os custos e a gestão. As definições de política para estes casos de uso comum já estão disponíveis no seu ambiente Azure como incorporados para ajudá-lo a começar.

Todos os dados e objetos da Azure Policy são encriptados em repouso. Para obter mais informações, consulte [a encriptação de dados do Azure em repouso](../../security/fundamentals/encryption-atrest.md).

## <a name="overview"></a>Descrição geral

A Azure Policy avalia os recursos em Azure comparando as propriedades desses recursos com as regras empresariais. Estas regras de negócio, descritas no [formato JSON,](./concepts/definition-structure.md)são conhecidas como [definições políticas.](#policy-definition) Para simplificar a gestão, várias regras empresariais podem ser agrupadas para formar uma [iniciativa política](#initiative-definition) (por vezes chamada de _PolicySet)._ Uma vez formadas as suas regras de negócio, a definição ou iniciativa de política é [atribuída](#assignments) a qualquer âmbito de recursos que a Azure suporte, tais como grupos de [gestão,](../management-groups/overview.md)subscrições, [grupos de recursos](../../azure-resource-manager/management/overview.md#resource-groups)ou recursos individuais. A atribuição aplica-se a todos os recursos no âmbito do Gestor de [Recursos](../../azure-resource-manager/management/overview.md#understand-scope) dessa atribuição. Os subscópios podem ser excluídos, se necessário. Para mais informações, consulte [Scope in Azure Policy](./concepts/scope.md).

A Azure Policy utiliza um [formato JSON](./concepts/definition-structure.md) para formar a lógica que a avaliação utiliza para determinar se um recurso está ou não em conformidade. As definições incluem metadados e a regra da política. A regra definida pode usar funções, parâmetros, operadores [lógicos,](./concepts/definition-structure.md#aliases) condições e pseudónimos de propriedade para corresponder exatamente ao cenário que deseja. A regra da política determina quais os recursos no âmbito da atribuição que são avaliados.

### <a name="understand-evaluation-outcomes"></a>Compreender os resultados da avaliação

Os recursos são avaliados em momentos específicos durante o ciclo de vida dos recursos, o ciclo de vida da atribuição de políticas e para uma avaliação regular da conformidade contínua. Seguem-se os tempos ou eventos que fazem com que um recurso seja avaliado:

- Um recurso é criado, atualizado ou eliminado num âmbito com uma atribuição de política.
- Uma política ou iniciativa é recentemente atribuída a um âmbito de aplicação.
- Uma política ou iniciativa já atribuída a um âmbito é atualizada.
- Durante o ciclo de avaliação padrão de conformidade, que ocorre uma vez a cada 24 horas.

Para obter informações detalhadas sobre quando e como a avaliação da política acontece, consulte [os gatilhos de avaliação](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>Controlar a resposta a uma avaliação

As regras comerciais para o manuseamento de recursos não conformes variam muito entre as organizações. Exemplos de como uma organização quer que a plataforma responda a um recurso não conforme incluem:

- Negar a mudança de recursos
- Registar a alteração para o recurso
- Altere o recurso antes da alteração
- Alterar o recurso após a alteração
- Implementar recursos compatíveis relacionados

A Política Azure torna possível cada uma destas respostas empresariais através da aplicação de [efeitos.](./concepts/effects.md) Os efeitos são definidos na parte da **regra política** da [definição de política](./concepts/definition-structure.md).

### <a name="remediate-non-compliant-resources"></a>Identificar recursos em não conformidade

Embora estes efeitos afetem principalmente um recurso quando o recurso é criado ou atualizado, a Azure Policy também apoia lidar com recursos não conformes existentes sem necessidade de alterar esse recurso. Para obter mais informações sobre a conformidade com os recursos existentes, consulte [os recursos de reparação.](./how-to/remediate-resources.md)

### <a name="video-overview"></a>Descrição geral em vídeo

A seguinte descrição geral do Azure Policy é do Build 2018. Para slides ou download de vídeo, visite [O seu ambiente Azure através da Política Azure](https://channel9.msdn.com/events/Build/2018/THR2030) no Canal 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Introdução

### <a name="azure-policy-and-azure-rbac"></a>Política Azure e Azure RBAC

Existem algumas diferenças fundamentais entre a Política Azure e o controlo de acesso baseado em funções Azure (Azure RBAC). A Azure Policy avalia o estado examinando propriedades sobre recursos que estão representados em Gestor de Recursos e propriedades de alguns Fornecedores de Recursos. A Política Azure não restringe as ações (também chamadas _operações)._ A Azure Policy garante que o estado de recursos está em conformidade com as suas regras de negócio sem se preocupar com quem fez a mudança ou quem tem permissão para fazer uma mudança.

O Azure RBAC foca-se na gestão [das ações dos](../../role-based-access-control/resource-provider-operations.md) utilizadores em diferentes âmbitos. Se for necessário controlar uma ação, então o Azure RBAC é a ferramenta correta a utilizar. Mesmo que um indivíduo tenha acesso a uma ação, se o resultado for um recurso não conforme, a Azure Policy ainda bloqueia a criação ou a atualização.

A combinação da Azure RBAC e da Política Azure proporciona controlo de âmbito total em Azure.

### <a name="azure-rbac-permissions-in-azure-policy"></a>Permissões do Azure RBAC na Política Azure

O Azure Policy tem várias permissões, conhecidas como operações, em dois Fornecedores de Recursos:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Muitas Funções incorporadas concedem permissão aos recursos do Azure Policy. O papel **de Contribuinte de Política de Recursos** inclui a maioria das operações da Política Azure. **O dono** tem todos os direitos. Tanto **o Contribuinte** como o **Reader** têm acesso a todas as operações _da_ Azure Policy. **O contribuinte** pode desencadear a remediação de recursos, mas não pode _criar_ definições ou atribuições. **O Administrador de Acesso** ao Utilizador é necessário para conceder a identidade gerida no **deployIfNotExists** ou **modificar** as permissões necessárias.

Se nenhuma das Funções incorporadas tiver as permissões exigidas, crie uma [função personalizada](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> A identidade gerida de um **deployIfNotExists** ou **modificar** a atribuição de políticas precisa de permissões suficientes para criar ou atualizar recursos direcionados. Para obter mais informações, consulte [definições de política de configuração para remediação.](./how-to/remediate-resources.md#configure-policy-definition)

### <a name="resources-covered-by-azure-policy"></a>Recursos abrangidos pela Política Azure

A Azure Policy avalia todos os recursos Azure a nível de subscrição ou abaixo, incluindo recursos ativados pela Arc. Para certos fornecedores de recursos, como [a Configuração de Hóspedes](./concepts/guest-configuration.md), [Serviço Azure Kubernetes](../../aks/intro-kubernetes.md)e [Azure Key Vault,](../../key-vault/general/overview.md)existe uma integração mais profunda para gerir configurações e objetos. Para saber mais, consulte os [modos Provedor de Recursos.](./concepts/definition-structure.md)

### <a name="recommendations-for-managing-policies"></a>Recomendações para a gestão de políticas

Aqui estão algumas dicas e dicas a ter em mente:

- Comece com um efeito de auditoria em vez de um efeito de negação para acompanhar o impacto da sua definição de política nos recursos no seu ambiente. Se já tiver scripts para autodimensionar as suas aplicações, definir um efeito de negação pode dificultar tais tarefas de automação já em vigor.

- Considere as hierarquias organizacionais ao criar definições e atribuições. Recomendamos a criação de definições a níveis mais elevados, como o grupo de gestão ou o nível de subscrição. Em seguida, criar a tarefa no próximo nível de criança. Se criar uma definição num grupo de gestão, a atribuição pode ser avaliada até um grupo de subscrição ou recursos dentro desse grupo de gestão.

- Recomendamos a criação e atribuição de definições de iniciativa, mesmo para uma definição política única.
  Por exemplo, tem política de definição de _políticaDefA_ e criá-la no âmbito da iniciativa de definição _de iniciativaDefC_. Se criar outra definição de política mais tarde para _o PolicyEfB_ com objetivos semelhantes aos _da policyDefA,_ pode adicioná-la sob _iniciativaDefC_ e rastreá-las em conjunto.

- Uma vez criada uma atribuição de iniciativa, as definições políticas adicionadas à iniciativa também se tornam parte das atribuições dessa iniciativa.

- Quando uma atribuição de iniciativa é avaliada, todas as políticas dentro da iniciativa também são avaliadas.
  Se precisa de avaliar uma política individualmente, é melhor não incluí-la numa iniciativa.

## <a name="azure-policy-objects"></a>Objetos da Política Azure

### <a name="policy-definition"></a>Definição de política

O percurso de criar e implementar uma política no Azure Policy começa pela criação de uma definição de política. Cada definição de política tem condições ao abrigo das quais é aplicada. E tem um efeito definido que ocorre se as condições forem cumpridas.

Na Política Azure, oferecemos várias políticas incorporadas que estão disponíveis por padrão. Por exemplo:

- **SKUs de conta de armazenamento permitido** (Negar): Determina se uma conta de armazenamento que está a ser implantada está dentro de um conjunto de tamanhos SKU. O seu efeito é negar todas as contas de armazenamento que não aderem ao conjunto de tamanhos SKU definidos.
- **Tipo de recurso permitido** (Negar): Define os tipos de recursos que pode implementar. O seu efeito é negar todos os recursos que não fazem parte desta lista definida.
- **Locais Permitidos** (Negar): Restringe os locais disponíveis para novos recursos. O efeito é utilizado para impor os requisitos de geoconformidade.
- **SKUs de máquina virtual permitida** (Negar): Especifica um conjunto de SKUs de máquina virtual que pode implementar.
- **Adicionar uma etiqueta aos recursos** (Modificar): Aplica uma etiqueta necessária e o seu valor predefinido se não for especificado pelo pedido de implantação.
- **Não são permitidos tipos** de recursos (Negar): Impede a implantação de uma lista de tipos de recursos.

Para implementar estas definições de política (ambas as definições incorporadas e personalizadas), terá de as atribuir. Pode atribuir qualquer uma destas políticas através do portal do Azure, do PowerShell ou da CLI do Azure.

A avaliação de políticas acontece com várias ações diferentes, tais como atribuição de políticas ou atualizações de políticas. Para obter uma lista completa, consulte [os gatilhos de avaliação de política](./how-to/get-compliance-data.md#evaluation-triggers).

Para saber mais sobre as estruturas de definições de política, veja [Estrutura de Definição de Política](./concepts/definition-structure.md).

Os parâmetros de política ajudam a simplificar a gestão de políticas ao reduzir o número de definições de política que tem de criar. Pode definir os parâmetros durante a criação de uma definição de política para torná-la mais genérica. Em seguida, pode reutilizar essa definição de política para diferentes cenários. Pode fazê-lo ao transmitir diferentes valores quando atribui a definição de política. Por exemplo, pode especificar um conjunto de localizações para uma subscrição.

Os parâmetros são definidos na criação de uma definição de política. Quando um parâmetro é definido, é fornecido um nome e, opcionalmente, um valor para o mesmo. Por exemplo, pode definir um parâmetro para uma política intitulada _localização_. Em seguida, pode atribuir-lhe valores diferentes, tais como _EastUS_ ou _WestUS_ quando atribui uma política.

Para obter mais informações sobre parâmetros de política, consulte [a estrutura de Definição - Parâmetros](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Definição de iniciativa

Uma definição de iniciativa é uma coleção de definições de política adaptadas para alcançar um objetivo único abrangente. As definições de iniciativa simplificam a gestão e a atribuição de definições de política. Simplificam através do agrupamento de um conjunto de políticas num único item. Por exemplo, pode criar uma iniciativa intitulada **Ativar Monitorização no Centro de Segurança do Azure**, com o objetivo de monitorizar todas as recomendações de segurança disponíveis no Centro de Segurança do Azure.

> [!NOTE]
> O SDK, como Azure CLI e Azure PowerShell, utiliza propriedades e parâmetros chamados **PolicySet** para se referir a iniciativas.

Ao abrigo desta iniciativa, terá definições de política como:

- **Monitorizar a Base de Dados SQL não encriptada no Centro de Segurança** – Para monitorização de servidores e bases de dados SQL não encriptados.
- **Monitorize as vulnerabilidades do SO no Centro de Segurança** – Para monitorizar servidores que não satisfaçam a linha de base configurada.
- **Monitorizar a Proteção de Ponto Final em falta no Centro de Segurança** – Para monitorização de servidores sem um agente de proteção de ponto final instalado.

Tal como os parâmetros de política, os parâmetros de iniciativa ajudam a simplificar a gestão de iniciativas ao reduzir a redundância. Os parâmetros da iniciativa são parâmetros que estão a ser utilizados pelas definições políticas no âmbito da iniciativa.

Por exemplo, considere um cenário em que tem uma definição de iniciativa - **initiativeC**, com as definições de política **policyA** e **policyB**, em que cada uma espera um tipo diferente de parâmetro:

| Política | Nome do parâmetro |Tipo de parâmetro  |Nota |
|---|---|---|---|
| policyA | allowedLocations | matriz  |Este parâmetro espera uma lista de cadeias para um valor, uma vez que o tipo de parâmetro foi definido como uma matriz |
| policyB | allowedSingleLocation |string |Este parâmetro espera uma palavra para um valor, uma vez que o tipo de parâmetro foi definido como uma cadeia |

Neste cenário, quando define os parâmetros da iniciativa para **initiativeC**, tem três opções:

- Utilizar os parâmetros das definições de política nesta iniciativa: neste exemplo, _allowedLocations_ e _allowedSingleLocation_ tornam-se parâmetros de iniciativa para **initiativeC**.
- Indicar os valores para os parâmetros das definições de política nesta definição de iniciativa. Neste exemplo, pode fornecer uma lista de localizações para o parâmetro da **policyA**– **permitiu que as localizações** e o parâmetro do **PolicyB**– **permitissem aLocalização**. Também pode fornecer valores quando atribui esta iniciativa.
- Fornecer uma lista das opções de _valor_ que podem ser utilizadas quando atribui esta iniciativa. Quando atribui esta iniciativa, os parâmetros herdados das definições de política na iniciativa apenas podem ter valores desta lista fornecida.

Ao criar opções de valor numa definição de iniciativa, não é capaz de inserir um valor diferente durante a atribuição da iniciativa porque não faz parte da lista.

Para saber mais sobre as estruturas das definições de iniciativa, reveja [a Estrutura de Definição de Iniciativa.](./concepts/initiative-definition-structure.md)

### <a name="assignments"></a>Atribuições

Uma atribuição é uma definição ou iniciativa política que foi atribuída para ter lugar dentro de um âmbito específico. Este âmbito pode ir de um grupo de [gestão](../management-groups/overview.md) a um recurso individual. O termo _scope_ refere-se a todos os recursos, grupos de recursos, subscrições ou grupos de gestão a que a definição é atribuída. As atribuições são herdadas por todos os recursos infantis. Este desenho significa que uma definição aplicada a um grupo de recursos também é aplicada aos recursos desse grupo de recursos. No entanto, pode excluir um subscópio da atribuição.

Por exemplo, no âmbito de subscrição, pode atribuir uma definição que impede a criação de recursos de networking. Pode excluir um grupo de recursos nessa subscrição destinada a infraestruturas de networking. Em seguida, concede acesso a este grupo de recursos de rede aos utilizadores que confia na criação de recursos de rede.

Em outro exemplo, é melhor atribuir uma definição de lista de tipo de recurso ao nível do grupo de gestão. Em seguida, atribui uma política mais permissiva (permitindo mais tipos de recursos) num grupo de gestão de crianças ou mesmo diretamente em subscrições. No entanto, este exemplo não funcionaria porque a Política Azure é um sistema de negação explícito. Em vez disso, você precisa excluir o grupo de gestão de crianças ou a subscrição da atribuição de nível de grupo de gestão. Em seguida, atribua a definição mais permissiva no grupo de gestão de crianças ou no nível de subscrição. Se qualquer atribuição resultar na recusa de um recurso, então a única maneira de permitir o recurso é modificar a atribuição de negação.

Para obter mais informações sobre a definição de atribuições através do portal, consulte [Criar uma atribuição de política para identificar recursos não conformes no seu ambiente Azure](./assign-policy-portal.md). Também estão disponíveis passos para o [PowerShell](./assign-policy-powershell.md) e a [CLI do Azure](./assign-policy-azurecli.md). Para obter informações sobre a estrutura de atribuição, consulte [a Estrutura de Atribuições.](./concepts/assignment-structure.md)

## <a name="maximum-count-of-azure-policy-objects"></a>Contagem máxima de objetos da Política Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma ideia geral do Azure Policy e de alguns dos principais conceitos, seguem-se os passos sugeridos seguintes:

- [Reveja a estrutura de definição de políticas.](./concepts/definition-structure.md)
- [Atribua uma definição de política utilizando o portal](./assign-policy-portal.md).
