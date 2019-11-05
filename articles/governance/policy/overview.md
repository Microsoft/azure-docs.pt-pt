---
title: Descrição Geral do Azure Policy
description: O Azure Policy é um serviço no Azure utilizado para criar, atribuir e gerir definições de política no seu ambiente do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: overview
ms.service: azure-policy
ms.openlocfilehash: 46d78ca9f82017e1a11642af6e5dcdc68e239c8a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498272"
---
# <a name="overview-of-the-azure-policy-service"></a>Visão geral do serviço de Azure Policy

A governança valida que sua organização pode atingir suas metas por meio de uso eficaz e eficiente de ti. Ele atende a essa necessidade, criando uma clareza entre as metas de negócios e os projetos de ti.

A sua empresa tem um número significativo de problemas de TI que parece nunca ficarem resolvidos?
Uma boa governação de TI envolve o planeamento das suas iniciativas e a definição de prioridades num nível estratégico para ajudar a gerir e evitar problemas. Essa necessidade estratégica é onde Azure Policy entra.

O Azure Policy é um serviço do Azure que utiliza para criar, atribuir e gerir políticas. Estas políticas impõem diferentes regras e efeitos aos recursos, de forma a que esses recursos se mantenham em conformidade com as normas empresariais e os contratos de nível de serviço. Azure Policy atende a essa necessidade avaliando seus recursos para não conformidade com políticas atribuídas. Por exemplo, pode ter uma política para permitir apenas um determinado tamanho de SKU de máquinas virtuais no seu ambiente. Depois que essa política é implementada, os recursos novos e existentes são avaliados quanto à conformidade. Com o tipo certo de política, os recursos existentes podem ser colocados em conformidade. Mais adiante nesta documentação, vamos abordar mais detalhes sobre como criar e implementar políticas com Azure Policy.

> [!IMPORTANT]
> A avaliação de conformidade do Azure Policy é agora fornecida para todas as atribuições, independentemente do escalão de preço. Se a suas atribuições não mostrarem os dados de conformidade, certifique-se de que a subscrição está registada com o fornecedor de recursos Microsoft.PolicyInsights.

[!INCLUDE [service-provider-management-toolkit](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="how-is-it-different-from-rbac"></a>Em que medida é diferente do RBAC?

Há algumas diferenças importantes entre Azure Policy e o RBAC (controle de acesso baseado em função). O RBAC concentra-se nas ações do utilizador em âmbitos diferentes. Você pode ser adicionado à função de colaborador para um grupo de recursos, permitindo que você faça alterações nesse grupo de recursos. Azure Policy se concentra nas propriedades de recurso durante a implantação e nos recursos já existentes. Azure Policy controla propriedades como os tipos ou locais de recursos. Diferentemente do RBAC, Azure Policy é um sistema de permissão padrão e de negação explícita.

### <a name="rbac-permissions-in-azure-policy"></a>Permissões RBAC no Azure Policy

O Azure Policy tem várias permissões, conhecidas como operações, em dois Fornecedores de Recursos:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Muitas Funções incorporadas concedem permissão aos recursos do Azure Policy. A função **colaborador de política de recurso (versão prévia)** inclui a maioria das operações Azure Policy. O **proprietário** tem direitos totais. O **colaborador** e o **leitor** podem usar todas as operações de Azure Policy de leitura, mas o **colaborador** também pode disparar a correção.

Se nenhuma das Funções incorporadas tiver as permissões exigidas, crie uma [função personalizada](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definição de política

O percurso de criar e implementar uma política no Azure Policy começa pela criação de uma definição de política. Cada definição de política tem condições sob as quais é imposta. E ele tem um efeito definido que ocorre se as condições forem atendidas.

No Azure Policy, oferecemos várias políticas internas que estão disponíveis por padrão. Por exemplo:

- **SKUs de conta de armazenamento permitidas**: determina se uma conta de armazenamento que está sendo implantada está dentro de um conjunto de tamanhos de SKU. Seu efeito é negar todas as contas de armazenamento que não aderem ao conjunto de tamanhos de SKU definidos.
- **Tipo de recurso permitido**: define os tipos de recursos que você pode implantar. Seu efeito é negar todos os recursos que não fazem parte dessa lista definida.
- **Locais permitidos**: restringe os locais disponíveis para novos recursos. O efeito é utilizado para impor os requisitos de geoconformidade.
- **SKUs de máquina virtual permitidas**: especifica um conjunto de SKUs de máquina virtual que você pode implantar.
- **Adicionar uma marca a recursos**: aplica uma marca necessária e seu valor padrão se não for especificado pela solicitação de implantação.
- **Impor a marca e seu valor**: impõe uma marca necessária e seu valor a um recurso.
- **Tipos de recursos não permitidos**: impede que uma lista de tipos de recursos seja implantada.

Para implementar essas definições de política (definições internas e personalizadas), você precisará atribuí-las. Pode atribuir qualquer uma destas políticas através do portal do Azure, do PowerShell ou da CLI do Azure.

A avaliação de política ocorre com várias ações diferentes, como atribuição de política ou atualizações de política. Para obter uma lista completa, consulte [gatilhos de avaliação de política](./how-to/get-compliance-data.md#evaluation-triggers).

Para saber mais sobre as estruturas de definições de política, veja [Estrutura de Definição de Política](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Atribuição de política

Uma atribuição de política é uma definição de política que foi atribuída para ter lugar num âmbito específico. Este âmbito pode ir desde um [grupo de gestão](../management-groups/overview.md) a um grupo de recursos. O termo *âmbito* refere-se a todos os grupos de recursos, subscrições ou grupos de gestão a que a definição de política está atribuída. As atribuições de política são herdadas por todos os recursos subordinados. Esse design significa que uma política aplicada a um grupo de recursos também é aplicada aos recursos desse grupo de recursos. No entanto, pode excluir um subâmbito da atribuição de política.

Por exemplo, no âmbito da subscrição, pode atribuir uma política que impede a criação de recursos de rede. Você pode excluir um grupo de recursos nessa assinatura que destina-se à infraestrutura de rede. Em seguida, conceda acesso a esse grupo de recursos de rede a usuários nos quais você confia com a criação de recursos de rede.

Em outro exemplo, talvez você queira atribuir uma política de lista de permissões de tipo de recurso no nível do grupo de gerenciamento. Em seguida, atribua uma política mais permissiva (permitindo mais tipos de recursos) num grupo de gestão subordinado ou mesmo diretamente em subscrições. No entanto, este exemplo não funciona porque a política é um sistema de negação explícita. Em vez disso, tem de excluir o grupo de gestão do subordinado ou uma subscrição da atribuição de política ao nível do grupo de gestão. Em seguida, atribua a política mais permissiva no grupo de gestão subordinado ou ao nível da subscrição. Se qualquer política resultar em um recurso sendo negado, a única maneira de permitir o recurso é modificar a política de negação.

Para obter mais informações sobre a configuração de definições e atribuições de política através do portal, veja [Criar uma atribuição de política para identificar recursos não conformes no ambiente do Azure](assign-policy-portal.md). Também estão disponíveis passos para o [PowerShell](assign-policy-powershell.md) e a [CLI do Azure](assign-policy-azurecli.md).

## <a name="policy-parameters"></a>Parâmetros de política

Os parâmetros de política ajudam a simplificar a gestão de políticas ao reduzir o número de definições de política que tem de criar. Pode definir os parâmetros durante a criação de uma definição de política para torná-la mais genérica. Em seguida, pode reutilizar essa definição de política para diferentes cenários. Pode fazê-lo ao transmitir diferentes valores quando atribui a definição de política. Por exemplo, pode especificar um conjunto de localizações para uma subscrição.

Os parâmetros são definidos durante a criação de uma definição de política. Quando um parâmetro é definido, ele recebe um nome e, opcionalmente, recebe um valor. Por exemplo, pode definir um parâmetro para uma política intitulada *localização*. Em seguida, pode atribuir-lhe valores diferentes, tais como *EastUS* ou *WestUS* quando atribui uma política.

Para obter mais informações sobre parâmetros de política, consulte [estrutura de definição-parâmetros](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Definição de iniciativa

Uma definição de iniciativa é uma coleção de definições de política adaptadas para alcançar um objetivo único abrangente. As definições de iniciativa simplificam a gestão e a atribuição de definições de política. Simplificam através do agrupamento de um conjunto de políticas num único item. Por exemplo, pode criar uma iniciativa intitulada **Ativar Monitorização no Centro de Segurança do Azure**, com o objetivo de monitorizar todas as recomendações de segurança disponíveis no Centro de Segurança do Azure.

Ao abrigo desta iniciativa, terá definições de política como:

- **Monitorizar a Base de Dados SQL não encriptada no Centro de Segurança** – Para monitorização de servidores e bases de dados SQL não encriptados.
- **Monitorar vulnerabilidades do sistema operacional na central de segurança** – para servidores de monitoramento que não atendem à linha de base configurada.
- **Monitorizar a Proteção de Ponto Final em falta no Centro de Segurança** – Para monitorização de servidores sem um agente de proteção de ponto final instalado.

## <a name="initiative-assignment"></a>Atribuição de iniciativa

Tal como uma atribuição de política, uma atribuição de iniciativa é uma definição de iniciativa atribuída a um âmbito específico. As atribuições de iniciativa reduzem a necessidade de efetuar várias definições de iniciativa para cada âmbito. Este âmbito também pode ir desde um grupo de gestão a um grupo de recursos.

Cada iniciativa é atribuível a escopos diferentes. Uma iniciativa pode ser atribuída à **assinatura** e **subscriptionB**.

## <a name="initiative-parameters"></a>Parâmetros de iniciativa

Tal como os parâmetros de política, os parâmetros de iniciativa ajudam a simplificar a gestão de iniciativas ao reduzir a redundância. Parâmetros de iniciativa são parâmetros sendo usados pelas definições de política dentro da iniciativa.

Por exemplo, considere um cenário em que tem uma definição de iniciativa - **initiativeC**, com as definições de política **policyA** e **policyB**, em que cada uma espera um tipo diferente de parâmetro:

| Política | Nome do parâmetro |Tipo de parâmetro  |Nota |
|---|---|---|---|
| policyA | allowedLocations | array  |Este parâmetro espera uma lista de cadeias para um valor, uma vez que o tipo de parâmetro foi definido como uma matriz |
| policyB | allowedSingleLocation |string |Este parâmetro espera uma palavra para um valor, uma vez que o tipo de parâmetro foi definido como uma cadeia |

Neste cenário, quando define os parâmetros da iniciativa para **initiativeC**, tem três opções:

- Utilizar os parâmetros das definições de política nesta iniciativa: neste exemplo, *allowedLocations* e *allowedSingleLocation* tornam-se parâmetros de iniciativa para **initiativeC**.
- Indicar os valores para os parâmetros das definições de política nesta definição de iniciativa. Neste exemplo, você pode fornecer uma lista de locais para o **parâmetro da política – allowedLocations** e o **parâmetro de policyB – allowedSingleLocation**. Também pode fornecer valores quando atribui esta iniciativa.
- Fornecer uma lista das opções de *valor* que podem ser utilizadas quando atribui esta iniciativa. Quando atribui esta iniciativa, os parâmetros herdados das definições de política na iniciativa apenas podem ter valores desta lista fornecida.

Ao criar opções de valor em uma definição de iniciativa, você não pode inserir um valor diferente durante a atribuição de iniciativa porque ela não faz parte da lista.

## <a name="maximum-count-of-azure-policy-objects"></a>Contagem máxima de objetos de Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Recomendações para a gestão de políticas

Aqui estão alguns ponteiros e dicas para ter em mente:

- Comece com um efeito de auditoria em vez de um efeito de negação para controlar o impacto da definição de política nos recursos em seu ambiente. Se você já tiver scripts em vigor para dimensionamento automático de seus aplicativos, a definição de um efeito de negação poderá impedir que essas tarefas de automação já estejam em vigor.

- Considere as hierarquias organizacionais ao criar definições e atribuições. É recomendável criar definições em níveis mais altos, como o grupo de gerenciamento ou o nível de assinatura. Em seguida, crie a atribuição no próximo nível filho. Se você criar uma definição em um grupo de gerenciamento, a atribuição poderá ter o escopo reduzido para uma assinatura ou grupo de recursos dentro desse grupo de gerenciamento.

- É recomendável criar e atribuir definições de iniciativa mesmo para uma definição de política única.
Por exemplo, você tem a definição de política *policyDefA* e a cria em *initiativeDefC*de definição de iniciativa. Se você criar outra definição de política posteriormente para *policyDefB* com metas semelhantes a *policyDefA*, você poderá adicioná-la em *initiativeDefC* e rastreá-las juntas.

- Depois de criar uma atribuição de iniciativa, as definições de política adicionadas à iniciativa também se tornarão parte das atribuições de iniciativas.

- Quando uma atribuição de iniciativa é avaliada, todas as políticas dentro da iniciativa também são avaliadas. Se você precisar avaliar uma política individualmente, é melhor não incluí-la em uma iniciativa.

## <a name="video-overview"></a>Visão geral em vídeo

A seguinte descrição geral do Azure Policy é do Build 2018. Para download de slides ou vídeo, visite [controlar seu ambiente do Azure por meio de Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) no Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma ideia geral do Azure Policy e de alguns dos principais conceitos, seguem-se os passos sugeridos seguintes:

- [Atribua uma definição de política usando o portal](assign-policy-portal.md).
- [Atribua uma definição de política usando o CLI do Azure](assign-policy-azurecli.md).
- [Atribua uma definição de política usando o PowerShell](assign-policy-powershell.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](..//management-groups/overview.md).
- Exiba [o controle do seu ambiente do Azure por meio do Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) no Channel 9.
