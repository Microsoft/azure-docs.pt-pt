---
title: Descrição Geral do Azure Policy
description: O Azure Policy é um serviço no Azure utilizado para criar, atribuir e gerir definições de política no seu ambiente do Azure.
ms.date: 04/21/2020
ms.topic: overview
ms.openlocfilehash: 68005a9a07e7f081a646c75566a0be4046f078ad
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770591"
---
# <a name="what-is-azure-policy"></a>O que é o Azure Policy?

A Política Azure ajuda a impor as normas organizacionais e a avaliar o cumprimento em escala. Através do seu painel de conformidade, proporciona uma visão agregada para avaliar o estado geral do ambiente, com a capacidade de aprofundar a granularidade por recurso e por política. Também ajuda a levar os seus recursos para o cumprimento através da reparação a granel dos recursos existentes e da reparação automática de novos recursos.

Os casos de utilização comum para a Política Azure incluem a implementação da governação para a coerência dos recursos, conformidade regulamentar, segurança, custos e gestão. As definições políticas para estes casos de uso comum já estão disponíveis no seu ambiente Azure como incorporados para ajudar a começar facilmente.

## <a name="overview"></a>Descrição geral

A Azure Policy avalia os recursos no Azure comparando as propriedades desses recursos com as regras empresariais. Estas regras de negócio, descritas no [formato JSON,](./concepts/definition-structure.md)são conhecidas como definições de [políticas.](#policy-definition) Para simplificar a gestão, várias regras empresariais podem ser agrunadas para formar uma [iniciativa política](#initiative-definition) (por vezes chamada de _policySet)._ Uma vez formadas as suas regras de negócio, a definição ou iniciativa de política é [atribuída](#assignments) a qualquer âmbito de recursos que o Azure apoie, tais como grupos de [gestão](../management-groups/overview.md), subscrições, [grupos](../../azure-resource-manager/management/overview.md#resource-groups)de recursos ou recursos individuais. A atribuição aplica-se a todos os recursos no [âmbito](../../azure-resource-manager/management/overview.md#understand-scope) dessa atribuição.
Os subscópios podem ser excluídos, se necessário.

A Política Azure utiliza um [formato JSON](./concepts/definition-structure.md) para formar a lógica que a avaliação utiliza para determinar se um recurso é compatível ou não. As definições incluem metadados e a regra da política. A regra definida pode usar funções, parâmetros, operadores [lógicos,](./concepts/definition-structure.md#aliases) condições e pseudónimos de propriedade para corresponder exatamente ao cenário que deseja. A regra política determina quais os recursos no âmbito da atribuição avaliados.

### <a name="understand-evaluation-outcomes"></a>Compreender os resultados da avaliação

Os recursos são avaliados em momentos específicos durante o ciclo de vida dos recursos, o ciclo de vida da atribuição de políticas e para uma avaliação regular de conformidade contínua. Seguem-se os tempos ou eventos que fazem com que um recurso seja avaliado:

- Um recurso é criado, atualizado ou eliminado num âmbito com uma atribuição de política.
- Uma política ou iniciativa é recentemente atribuída a um âmbito.
- É atualizada uma política ou iniciativa já atribuída a um âmbito de aplicação.
- Durante o ciclo padrão de avaliação da conformidade, que ocorre uma vez a cada 24 horas.

Para obter informações detalhadas sobre quando e como a avaliação da política acontece, consulte os [gatilhos de Avaliação](./how-to/get-compliance-data.md#evaluation-triggers).

### <a name="control-the-response-to-an-evaluation"></a>Controlar a resposta a uma avaliação

As regras empresariais para o tratamento de recursos não conformes variam muito entre as organizações. Exemplos de como uma organização quer que a plataforma responda a um recurso de não-reclamação incluem:

- Negar a mudança de recursos
- Faça loga na alteração do recurso
- Alterar o recurso antes da mudança
- Alterar o recurso após a mudança
- Implementar recursos conformes relacionados

A Política Azure torna possível cada uma destas respostas empresariais através da aplicação de [efeitos.](./concepts/effects.md) Os efeitos são definidos na parte da **regra** política da [definição de política.](./concepts/definition-structure.md)

### <a name="remediate-non-compliant-resources"></a>Identificar recursos em não conformidade

Embora estes efeitos afetem principalmente um recurso quando o recurso é criado ou atualizado, a Política Azure também apoia a abordagem dos recursos não conformes existentes sem necessidade de alterar esse recurso. Para obter mais informações sobre a conformidade com os recursos existentes, consulte [a remediação dos recursos.](./how-to/remediate-resources.md)

### <a name="video-overview"></a>Descrição geral em vídeo

A seguinte descrição geral do Azure Policy é do Build 2018. Para slides ou download de vídeo, visite [Governe o seu ambiente Azure através](https://channel9.msdn.com/events/Build/2018/THR2030) da Política Azure no Canal 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="getting-started"></a>Introdução

### <a name="azure-policy-and-rbac"></a>Política Azure e RBAC

Existem algumas diferenças fundamentais entre a Política Azure e o controlo de acesso baseado em papéis (RBAC). A Política Azure avalia o Estado examinando propriedades sobre recursos que estão representados em Gestor de Recursos e propriedades de alguns Fornecedores de Recursos. A Política Azure não restringe as ações (também chamadas _de operações)._ A Azure Policy garante que o Estado de recursos está em conformidade com as suas regras de negócio sem preocupação com quem fez a mudança ou quem tem permissão para fazer uma mudança.

O RBAC centra-se na gestão das [ações](../../role-based-access-control/resource-provider-operations.md) dos utilizadores em diferentes âmbitos. Se for necessário o controlo de uma ação, o RBAC é a ferramenta correta a utilizar. Mesmo que um indivíduo tenha acesso a uma ação, se o resultado for um recurso não conforme, a Política Azure ainda bloqueia a criação ou a tualização.

A combinação de RBAC e Azure Policy fornece o controlo total do âmbito em Azure.

### <a name="rbac-permissions-in-azure-policy"></a>Permissões RBAC no Azure Policy

O Azure Policy tem várias permissões, conhecidas como operações, em dois Fornecedores de Recursos:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Muitas Funções incorporadas concedem permissão aos recursos do Azure Policy. O papel **de Contribuinte da Política de Recursos** inclui a maioria das operações da Política Azure. **O dono** tem todos os direitos. Tanto **o Colaborador** como o **Reader** têm acesso a todas as operações _da_ Política Azure. **O contribuinte** pode desencadear a reparação de recursos, mas não pode _criar_ definições ou atribuições.

Se nenhuma das Funções incorporadas tiver as permissões exigidas, crie uma [função personalizada](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> A identidade gerida de um **implementadoIfNotExists** policy assignment necessita de permissões suficientes para criar ou atualizar recursos incluídos no modelo. Para mais informações, consulte [definições políticas de Configuração para remediação](./how-to/remediate-resources.md#configure-policy-definition).

### <a name="resources-covered-by-azure-policy"></a>Recursos abrangidos pela Política Azure

A Política Azure avalia todos os recursos em Azure. Para certos fornecedores de recursos, como [configuração](./concepts/guest-configuration.md)de hóspedes, [Serviço Azure Kubernetes](../../aks/intro-kubernetes.md)e [Cofre de Chaves Azure,](../../key-vault/key-vault-overview.md)há uma integração mais profunda na gestão de configurações e objetos. Para saber mais, consulte [os modos de Fornecedor de Recursos](./concepts/definition-structure.md).

### <a name="recommendations-for-managing-policies"></a>Recomendações para a gestão de políticas

Aqui estão algumas dicas e dicas a ter em mente:

- Comece com um efeito de auditoria em vez de um efeito de negação para acompanhar o impacto da sua definição política nos recursos do seu ambiente. Se já tiver scripts para escalar automaticamente as suas aplicações, definir um efeito de negação pode dificultar tais tarefas de automação já em vigor.

- Considere hierarquias organizacionais ao criar definições e atribuições. Recomendamos a criação de definições em níveis mais elevados, como o grupo de gestão ou o nível de subscrição. Então, crie a tarefa ao nível da próxima criança. Se criar uma definição num grupo de gestão, a atribuição pode ser consultada a um grupo de subscrição ou recursos dentro desse grupo de gestão.

- Recomendamos a criação e a atribuição de definições de iniciativa, mesmo para uma definição política única.
  Por exemplo, tem política de definição de _políticasDefA_ e criá-la no âmbito da iniciativa de definição de _iniciativaDefC_. Se criar outra definição política mais tarde para _a políticaDefB_ com objetivos semelhantes à _políticaDefA,_ pode adicioná-la sob _iniciativaDefC_ e rastreá-las em conjunto.

- Uma vez criado um trabalho de iniciativa, as definições políticas adicionadas à iniciativa também fazem parte dessas iniciativas.

- Quando uma atribuição de iniciativa é avaliada, todas as políticas dentro da iniciativa também são avaliadas.
  Se precisa avaliar uma política individualmente, é melhor não incluí-la numa iniciativa.

## <a name="azure-policy-objects"></a>Objetos da Política Azure

### <a name="policy-definition"></a>Definição de política

O percurso de criar e implementar uma política no Azure Policy começa pela criação de uma definição de política. Todas as definições políticas têm condições em que é aplicada. E, tem um efeito definido que ocorre se as condições forem cumpridas.

Na Política Azure, oferecemos várias políticas incorporadas que estão disponíveis por defeito. Por exemplo:

- Conta de **Armazenamento Permitida SKUs** (Deny): Determina se uma conta de armazenamento que está a ser implantada está dentro de um conjunto de tamanhos SKU. O seu efeito é negar todas as contas de armazenamento que não aderirem ao conjunto de tamanhos SKU definidos.
- **Tipo de recurso permitido** (Negar): Define os tipos de recursos que pode implementar. O seu efeito é negar todos os recursos que não fazem parte desta lista definida.
- **Localizações Permitidas** (Deny): Restringe as localizações disponíveis para novos recursos. O efeito é utilizado para impor os requisitos de geoconformidade.
- **SKUs** de máquina virtual permitida (Deny): Especifica um conjunto de SKUs da máquina virtual que pode implantar.
- **Adicione uma etiqueta aos recursos** (Modificar): Aplica uma etiqueta necessária e o seu valor predefinido se não for especificado pelo pedido de implantação.
- **Etiqueta de apêndice e seu valor predefinido** (Apêndice): Aplica uma etiqueta necessária e o seu valor para um recurso.
- Tipos de **recursos não permitidos** (Deny): Impede que uma lista de tipos de recursos seja implantada.

Para implementar estas definições políticas (ambas as definições incorporadas e personalizadas), terá de as atribuir. Pode atribuir qualquer uma destas políticas através do portal do Azure, do PowerShell ou da CLI do Azure.

A avaliação política acontece com várias ações diferentes, tais como atribuição de políticas ou atualizações políticas. Para obter uma lista completa, consulte os gatilhos de [avaliação política](./how-to/get-compliance-data.md#evaluation-triggers).

Para saber mais sobre as estruturas de definições de política, veja [Estrutura de Definição de Política](./concepts/definition-structure.md).

Os parâmetros de política ajudam a simplificar a gestão de políticas ao reduzir o número de definições de política que tem de criar. Pode definir os parâmetros durante a criação de uma definição de política para torná-la mais genérica. Em seguida, pode reutilizar essa definição de política para diferentes cenários. Pode fazê-lo ao transmitir diferentes valores quando atribui a definição de política. Por exemplo, pode especificar um conjunto de localizações para uma subscrição.

Os parâmetros são definidos na criação de uma definição de política. Quando um parâmetro é definido, é fornecido um nome e opcionalmente um valor para o mesmo. Por exemplo, pode definir um parâmetro para uma política intitulada _localização_. Em seguida, pode atribuir-lhe valores diferentes, tais como _EastUS_ ou _WestUS_ quando atribui uma política.

Para obter mais informações sobre parâmetros de política, consulte [A estrutura definição - Parâmetros](./concepts/definition-structure.md#parameters).

### <a name="initiative-definition"></a>Definição de iniciativa

Uma definição de iniciativa é uma coleção de definições de política adaptadas para alcançar um objetivo único abrangente. As definições de iniciativa simplificam a gestão e a atribuição de definições de política. Simplificam através do agrupamento de um conjunto de políticas num único item. Por exemplo, pode criar uma iniciativa intitulada **Ativar Monitorização no Centro de Segurança do Azure**, com o objetivo de monitorizar todas as recomendações de segurança disponíveis no Centro de Segurança do Azure.

> [!NOTE]
> O SDK, como o Azure CLI e o Azure PowerShell, utiliza propriedades e parâmetros nomeados **PolicySet** para se referir a iniciativas.

Ao abrigo desta iniciativa, terá definições de política como:

- **Monitorizar a Base de Dados SQL não encriptada no Centro de Segurança** – Para monitorização de servidores e bases de dados SQL não encriptados.
- **Monitorize as vulnerabilidades do OS no Security Center** – Para monitorizar servidores que não satisfaçam a linha de base configurada.
- **Monitorizar a Proteção de Ponto Final em falta no Centro de Segurança** – Para monitorização de servidores sem um agente de proteção de ponto final instalado.

Tal como os parâmetros de política, os parâmetros de iniciativa ajudam a simplificar a gestão de iniciativas ao reduzir a redundância. Os parâmetros de iniciativa são parâmetros que estão a ser utilizados pelas definições políticas no âmbito da iniciativa.

Por exemplo, considere um cenário em que tem uma definição de iniciativa - **initiativeC**, com as definições de política **policyA** e **policyB**, em que cada uma espera um tipo diferente de parâmetro:

| Política | Nome do parâmetro |Tipo de parâmetro  |Nota |
|---|---|---|---|
| policyA | allowedLocations | array  |Este parâmetro espera uma lista de cadeias para um valor, uma vez que o tipo de parâmetro foi definido como uma matriz |
| policyB | allowedSingleLocation |string |Este parâmetro espera uma palavra para um valor, uma vez que o tipo de parâmetro foi definido como uma cadeia |

Neste cenário, quando define os parâmetros da iniciativa para **initiativeC**, tem três opções:

- Utilizar os parâmetros das definições de política nesta iniciativa: neste exemplo, _allowedLocations_ e _allowedSingleLocation_ tornam-se parâmetros de iniciativa para **initiativeC**.
- Indicar os valores para os parâmetros das definições de política nesta definição de iniciativa. Neste exemplo, pode fornecer uma lista de localizações ao parâmetro da **políticaA**– **permitidaaALocalização** e parâmetro de **políticaB**– **permitidoSingleLocation**. Também pode fornecer valores quando atribui esta iniciativa.
- Fornecer uma lista das opções de _valor_ que podem ser utilizadas quando atribui esta iniciativa. Quando atribui esta iniciativa, os parâmetros herdados das definições de política na iniciativa apenas podem ter valores desta lista fornecida.

Ao criar opções de valor numa definição de iniciativa, não é capaz de inserir um valor diferente durante a atribuição de iniciativa porque não faz parte da lista.

### <a name="assignments"></a>Atribuições

Uma atribuição é uma definição ou iniciativa política que foi atribuída a ter lugar num âmbito específico. Este âmbito pode ir de um grupo de [gestão](../management-groups/overview.md) a um recurso individual. O _âmbito_ do termo refere-se a todos os recursos, grupos de recursos, subscrições ou grupos de gestão a que a definição é atribuída. As atribuições são herdadas por todos os recursos infantis. Este desenho significa que uma definição aplicada a um grupo de recursos também é aplicada aos recursos desse grupo de recursos. No entanto, pode excluir um subscópio da atribuição.

Por exemplo, no âmbito da subscrição, pode atribuir uma definição que impeça a criação de recursos de networking. Poderia excluir um grupo de recursos nessa subscrição que se destina a infraestruturas de networking. Em seguida, concede acesso a este grupo de recursos de networking aos utilizadores em que confia na criação de recursos de networking.

Noutro exemplo, é possível atribuir um tipo de recurso que permita a definição de lista ao nível do grupo de gestão. Em seguida, atribui uma política mais permissiva (permitindo mais tipos de recursos) num grupo de gestão infantil ou mesmo diretamente em subscrições. No entanto, este exemplo não funcionaria porque a Política Azure é um sistema de negação explícito. Em vez disso, tem de excluir o grupo de gestão infantil ou a subscrição da atribuição de nível de grupo de gestão. Em seguida, designar a definição mais permissiva no grupo de gestão de crianças ou nível de subscrição. Se qualquer atribuição resultar em um recurso sendo negado, então a única maneira de permitir o recurso é modificar a atribuição de negação.

Para obter mais informações sobre a definição de atribuições através do portal, consulte [Criar uma atribuição de política para identificar recursos não conformes no seu ambiente Azure](assign-policy-portal.md). Também estão disponíveis passos para o [PowerShell](assign-policy-powershell.md) e a [CLI do Azure](assign-policy-azurecli.md).

## <a name="maximum-count-of-azure-policy-objects"></a>Contagem máxima de objetos da Política Azure

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma ideia geral do Azure Policy e de alguns dos principais conceitos, seguem-se os passos sugeridos seguintes:

- [Reveja a estrutura de definição de políticas.](./concepts/definition-structure.md)
- [Atribuir uma definição de política utilizando o portal](./assign-policy-portal.md).
