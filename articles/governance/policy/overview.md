---
title: Descrição Geral do Azure Policy
description: O Azure Policy é um serviço no Azure utilizado para criar, atribuir e gerir definições de política no seu ambiente do Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/06/2018
ms.topic: overview
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 7b5d9d483d05ac8225ea4b7497922192be44f1bc
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731684"
---
# <a name="what-is-azure-policy"></a>O que é o Azure Policy?

Governação valida que sua organização pode atingir as metas por meio de uma utilização eficaz e eficiente de IT. Ele atende a essa necessidade criando clareza entre os objetivos empresariais e projetos de TI.

A sua empresa tem um número significativo de problemas de TI que parece nunca ficarem resolvidos?
Uma boa governação de TI envolve o planeamento das suas iniciativas e a definição de prioridades num nível estratégico para ajudar a gerir e evitar problemas. Essa necessidade estratégica é onde entra o Azure Policy.

O Azure Policy é um serviço no Azure que utilizar para criar, atribuir e gerir políticas. Estas políticas impõem diferentes regras e efeitos aos recursos, de forma a que esses recursos se mantenham em conformidade com as normas empresariais e os contratos de nível de serviço. O Azure Policy atende a essa necessidade através da avaliação de seus recursos de não conformidade com as políticas atribuídas. Por exemplo, pode ter uma política para permitir apenas um determinado tamanho de SKU de máquinas virtuais no seu ambiente. Assim que esta política é implementada, os recursos novos e existentes são avaliados quanto à conformidade. Com o tipo certo de política, os recursos existentes podem ser colocados em conformidade. Mais tarde nesta documentação, vamos examinar mais detalhes sobre como criar e implementar políticas com o Azure Policy.

> [!IMPORTANT]
> A avaliação de conformidade do Azure Policy é agora fornecida para todas as atribuições, independentemente do escalão de preço. Se a suas atribuições não mostrarem os dados de conformidade, certifique-se de que a subscrição está registada com o fornecedor de recursos Microsoft.PolicyInsights.

## <a name="how-is-it-different-from-rbac"></a>Em que medida é diferente do RBAC?

Existem algumas diferenças fundamentais entre a política e o controlo de acesso baseado em funções (RBAC). O RBAC concentra-se nas ações do utilizador em âmbitos diferentes. Pode ser adicionado à função de Contribuidor para um grupo de recursos, permitindo-lhe efetuar alterações ao grupo de recursos. A política concentra-se nas propriedades dos recursos durante a implementação e para recursos já existentes. Política controla as propriedades, como os tipos ou localizações de recursos. Ao contrário do RBAC, a política está a permitir a um padrão e explícito sistema de negação.

### <a name="rbac-permissions-in-azure-policy"></a>Permissões RBAC no Azure Policy

O Azure Policy tem várias permissões, conhecidas como operações, em dois Fornecedores de Recursos:

- [Microsoft.Authorization](../../role-based-access-control/resource-provider-operations.md#microsoftauthorization)
- [Microsoft.PolicyInsights](../../role-based-access-control/resource-provider-operations.md#microsoftpolicyinsights)

Muitas Funções incorporadas concedem permissão aos recursos do Azure Policy. O **contribuinte de política de recurso (pré-visualização)** função inclui a maioria das operações de política. **Proprietário** tem direitos totais. Ambos **contribuinte** e **leitor** pode utilizar todas as operações de leitura de política, mas **contribuinte** também pode acionar a remediação.

Se nenhuma das Funções incorporadas tiver as permissões exigidas, crie uma [função personalizada](../../role-based-access-control/custom-roles.md).

## <a name="policy-definition"></a>Definição de política

O percurso de criar e implementar uma política no Azure Policy começa pela criação de uma definição de política. Cada definição de política tem condições sob as quais é aplicada. Além disso, tem um efeito definido que é realizada se as condições são cumpridas.

No Azure Policy, disponibilizamos várias políticas incorporadas que estão disponíveis por predefinição. Por exemplo:

- **Necessita do SQL Server 12.0**: Valida que todos os servidores SQL utilizam a versão 12.0. O efeito é recusar todos os servidores que não cumpram estes critérios.
- **SKUs de conta de armazenamento permitidos**: Determina se uma conta de armazenamento que está sendo implantada dentro de um conjunto de tamanhos de SKU. Seu efeito é recusar todas as contas de armazenamento que não cumpram o conjunto de tamanhos SKU definidos.
- **Tipo de recurso permitido**: Define os tipos de recurso que pode implementar. Seu efeito é recusar todos os recursos que não fazem parte desta lista definida.
- **Localizações permitidas**: Restringe as localizações disponíveis para novos recursos. O efeito é utilizado para impor os requisitos de geoconformidade.
- **SKUs de Máquina Virtual permitidos**: Especifica um conjunto de máquina virtual de SKUs que pode implementar.
- **Aplicar etiqueta e respetivo valor predefinido**: Aplica-se uma etiqueta obrigatória e o respetivo valor predefinido se não for especificado, o pedido de implementar.
- **Impor etiqueta e respetivo valor**: Impõe uma etiqueta obrigatória e o respetivo valor a um recurso.
- **Tipos de recurso não permitidos**: Impede que uma lista de tipos de recurso que está sendo implantado.

Para implementar estas definições de política (definições incorporadas e personalizadas), terá de atribuí-las. Pode atribuir qualquer uma destas políticas através do portal do Azure, do PowerShell ou da CLI do Azure.

Avaliação da política acontece com várias ações diferentes, tais como a atribuição de política ou as atualizações de política. Para obter uma lista completa, consulte [acionadores de avaliação da política](./how-to/get-compliance-data.md#evaluation-triggers).

Para saber mais sobre as estruturas de definições de política, veja [Estrutura de Definição de Política](./concepts/definition-structure.md).

## <a name="policy-assignment"></a>Atribuição de política

Uma atribuição de política é uma definição de política que foi atribuída para ter lugar num âmbito específico. Este âmbito pode ir desde um [grupo de gestão](../management-groups/overview.md) a um grupo de recursos. O termo *âmbito* refere-se a todos os grupos de recursos, subscrições ou grupos de gestão a que a definição de política está atribuída. As atribuições de política são herdadas por todos os recursos subordinados. Esse design significa que também é aplicada uma política aplicada a um grupo de recursos para recursos nesse grupo de recursos. No entanto, pode excluir um subâmbito da atribuição de política.

Por exemplo, no âmbito da subscrição, pode atribuir uma política que impede a criação de recursos de rede. Poderia excluir um grupo de recursos nessa subscrição que se destina a infraestrutura de rede. Em seguida, concede acesso a este grupo de recursos de rede para os usuários que confia com a criação de recursos de rede.

Noutro exemplo, poderá atribuir uma política de lista aprovada de tipo de recurso ao nível do grupo de gestão. Em seguida, atribua uma política mais permissiva (permitindo mais tipos de recursos) num grupo de gestão subordinado ou mesmo diretamente em subscrições. No entanto, este exemplo não funciona porque a política é um sistema de negação explícita. Em vez disso, tem de excluir o grupo de gestão do subordinado ou uma subscrição da atribuição de política ao nível do grupo de gestão. Em seguida, atribua a política mais permissiva no grupo de gestão subordinado ou ao nível da subscrição. Se qualquer política resulta num recurso negado de introdução, em seguida, a única forma de permitir que o recurso é modificar a política de negação.

Para obter mais informações sobre a configuração de definições e atribuições de política através do portal, veja [Criar uma atribuição de política para identificar recursos não conformes no ambiente do Azure](assign-policy-portal.md). Também estão disponíveis passos para o [PowerShell](assign-policy-powershell.md) e a [CLI do Azure](assign-policy-azurecli.md).

## <a name="policy-parameters"></a>Parâmetros de política

Os parâmetros de política ajudam a simplificar a gestão de políticas ao reduzir o número de definições de política que tem de criar. Pode definir os parâmetros durante a criação de uma definição de política para torná-la mais genérica. Em seguida, pode reutilizar essa definição de política para diferentes cenários. Pode fazê-lo ao transmitir diferentes valores quando atribui a definição de política. Por exemplo, pode especificar um conjunto de localizações para uma subscrição.

Os parâmetros são definidos durante a criação de uma definição de política. Quando é definido um parâmetro, tem um nome e opcionalmente fornecido um valor. Por exemplo, pode definir um parâmetro para uma política intitulada *localização*. Em seguida, pode atribuir-lhe valores diferentes, tais como *EastUS* ou *WestUS* quando atribui uma política.

Para obter mais informações sobre parâmetros de política, consulte [estrutura de definição - parâmetros](./concepts/definition-structure.md#parameters).

## <a name="initiative-definition"></a>Definição de iniciativa

Uma definição de iniciativa é uma coleção de definições de política adaptadas para alcançar um objetivo único abrangente. As definições de iniciativa simplificam a gestão e a atribuição de definições de política. Simplificam através do agrupamento de um conjunto de políticas num único item. Por exemplo, pode criar uma iniciativa intitulada **Ativar Monitorização no Centro de Segurança do Azure**, com o objetivo de monitorizar todas as recomendações de segurança disponíveis no Centro de Segurança do Azure.

Ao abrigo desta iniciativa, terá definições de política como:

- **Monitorizar a Base de Dados SQL não encriptada no Centro de Segurança** – Para monitorização de servidores e bases de dados SQL não encriptados.
- **Monitorizar vulnerabilidades do SO no Centro de segurança** – para monitorização de servidores que não satisfaçam a linha de base configurada.
- **Monitorizar a Proteção de Ponto Final em falta no Centro de Segurança** – Para monitorização de servidores sem um agente de proteção de ponto final instalado.

## <a name="initiative-assignment"></a>Atribuição de iniciativa

Tal como uma atribuição de política, uma atribuição de iniciativa é uma definição de iniciativa atribuída a um âmbito específico. As atribuições de iniciativa reduzem a necessidade de efetuar várias definições de iniciativa para cada âmbito. Este âmbito também pode ir desde um grupo de gestão a um grupo de recursos.

Cada iniciativa é atribuível a âmbitos diferentes. Uma iniciativa pode ser atribuída a ambos **subscriptionA** e **subscriptionB**.

## <a name="initiative-parameters"></a>Parâmetros de iniciativa

Tal como os parâmetros de política, os parâmetros de iniciativa ajudam a simplificar a gestão de iniciativas ao reduzir a redundância. Parâmetros de iniciativa são parâmetros a ser utilizados pelas definições de política na iniciativa.

Por exemplo, considere um cenário em que tem uma definição de iniciativa - **initiativeC**, com as definições de política **policyA** e **policyB**, em que cada uma espera um tipo diferente de parâmetro:

| Política | Nome do parâmetro |Tipo de parâmetro  |Nota |
|---|---|---|---|
| policyA | allowedLocations | array  |Este parâmetro espera uma lista de cadeias para um valor, uma vez que o tipo de parâmetro foi definido como uma matriz |
| policyB | allowedSingleLocation |string |Este parâmetro espera uma palavra para um valor, uma vez que o tipo de parâmetro foi definido como uma cadeia |

Neste cenário, quando define os parâmetros da iniciativa para **initiativeC**, tem três opções:

- Utilize os parâmetros das definições de política nesta iniciativa: Neste exemplo, *allowedLocations* e *allowedSingleLocation* tornam-se de parâmetros de iniciativa para **initiativeC**.
- Indicar os valores para os parâmetros das definições de política nesta definição de iniciativa. Neste exemplo, pode fornecer uma lista de localizações para o **parâmetro de policyA – allowedLocations** e o **parâmetro de policyB – allowedSingleLocation**. Também pode fornecer valores quando atribui esta iniciativa.
- Fornecer uma lista das opções de *valor* que podem ser utilizadas quando atribui esta iniciativa. Quando atribui esta iniciativa, os parâmetros herdados das definições de política na iniciativa apenas podem ter valores desta lista fornecida.

Ao criar opções de valor numa definição de iniciativa, não for possível introduzir um valor diferente durante a atribuição de iniciativa, porque não faz parte da lista.

## <a name="maximum-count-of-policy-objects"></a>Contagem máxima de objetos do Azure Policy

[!INCLUDE [policy-limits](../../../includes/azure-policy-limits.md)]

## <a name="recommendations-for-managing-policies"></a>Recomendações para a gestão de políticas

Aqui estão alguns ponteiros e sugestões a ter em mente:

- Comece com um efeito de auditoria em vez de um efeito de recusa, para controlar o impacto de sua definição de política em recursos no seu ambiente. Se tiver scripts já para dimensionar automaticamente seus aplicativos, definindo um efeito de recusa podem impedir essas tarefas de automatização já em vigor.

- Considere as hierarquias organizacionais quando criar definições e atribuições. Recomendamos a criação de definições níveis mais altos, como o grupo de gestão ou o nível de assinatura. Em seguida, crie a atribuição no nível subordinado seguinte. Se criar uma definição num grupo de gestão, a atribuição pode ser confinada para uma subscrição ou grupo de recursos nesse grupo de gestão.

- É recomendável criar e atribuir as definições de iniciativa mesmo para uma definição de política única.
Por exemplo, tem a definição de política *policyDefA* e criar na definição de iniciativa *initiativeDefC*. Se criar outra definição de política mais tarde para *policyDefB* com objetivos semelhantes aos *policyDefA*, pode adicioná-lo sob *initiativeDefC* e controlá-las em conjunto.

- Depois de criar uma atribuição de iniciativa, definições de política adicionadas a iniciativa tornam-se também parte do que as atribuições de iniciativas.

- Quando é avaliada uma atribuição de iniciativa, todas as políticas na iniciativa também são avaliadas. Se precisar de avaliar uma política individualmente, é melhor não incluí-lo numa iniciativa.

## <a name="video-overview"></a>Descrição geral do vídeo

A seguinte descrição geral do Azure Policy é do Build 2018. Para slides ou download de vídeo, visite [regem o seu ambiente do Azure através da política do Azure](https://channel9.msdn.com/events/Build/2018/THR2030) no Channel 9.

> [!VIDEO https://www.youtube.com/embed/dxMaYF2GB7o]

## <a name="next-steps"></a>Passos Seguintes

Agora que tem uma ideia geral do Azure Policy e de alguns dos principais conceitos, seguem-se os passos sugeridos seguintes:

- [Atribuir uma definição de política com o portal](assign-policy-portal.md)
- [Atribuir uma definição de política com a CLI do Azure](assign-policy-azurecli.md)
- [Atribuir uma definição de política com o PowerShell](assign-policy-powershell.md)
- Rever o que é um grupo de gestão, com [Organizar os recursos com grupos de gestão do Azure](..//management-groups/overview.md)
- Ver [Govern your Azure environment through Azure Policy](https://channel9.msdn.com/events/Build/2018/THR2030) (Governar o ambiente do Azure através do Azure Policy) no Channel 9
