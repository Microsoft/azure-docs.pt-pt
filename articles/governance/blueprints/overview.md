---
title: Descrição Geral do Azure Blueprints
description: Compreenda como o serviço Azure Blueprints lhe permite criar, definir e implantar artefactos no seu ambiente Azure.
ms.date: 01/27/2021
ms.topic: overview
ms.openlocfilehash: f4ba77f5fcb376bf600d94997b0d6ba569f04f82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98919347"
---
# <a name="what-is-azure-blueprints"></a>O que é o Azure Blueprints?

Da mesma forma que um esquema permite que um engenheiro ou um arquiteto crie um esboço dos parâmetros de design de um projeto, o Azure Blueprints permite que os arquitetos da cloud e os grupos de tecnologias de informação definam um conjunto repetível de recursos do Azure que implemente e adira às normas, padrões e requisitos de uma organização. A Azure Blueprints permite que as equipas de desenvolvimento construam e suportem rapidamente novos ambientes com confiança que estão a construir no âmbito do cumprimento organizacional de um conjunto de componentes incorporados, como o networking, para acelerar o desenvolvimento e a entrega.

Os esquemas são uma forma declarativa de orquestrar a implementação de vários modelos de recursos e de outros artefactos, tais como:

- Atribuições de Funções
- Atribuições de Política
- Modelos de gestor de recursos Azure (modelos ARM)
- Grupos de Recursos

O serviço do Azure Blueprints é apoiado pelo [Azure Cosmos DB](../../cosmos-db/introduction.md) globalmente distribuído. Os objetos do Blueprint são replicados para várias regiões do Azure. Esta replicação proporciona baixa latência, alta disponibilidade e acesso consistente aos seus objetos de planta, independentemente da região para onde a Azure Blueprints implementa os seus recursos.

## <a name="how-its-different-from-arm-templates"></a>Como é diferente dos modelos ARM

O serviço foi concebido para ajudar na _configuração do ambiente_. Esta configuração consiste frequentemente num conjunto de grupos de recursos, políticas, atribuições de funções e implementações de modelos ARM. Uma planta é um pacote para juntar cada um destes tipos de _artefactos_ e permitir-lhe compor e ver esse pacote, incluindo através de um pipeline de integração contínua e entrega contínua (CI/CD). Por fim, cada um deles é atribuído a uma subscrição numa única operação que pode ser auditada e controlada.

Quase tudo o que pretende incluir para implantação em Azure Blueprints pode ser realizado com um modelo ARM. No entanto, um modelo ARM é um documento que não existe de forma nativa no Azure – cada um é armazenado localmente ou em controlo de origem. O modelo é utilizado para implementações de um ou mais recursos do Azure, mas assim que esses recursos são implementados, deixam de existir ligações ou relações ativas com o modelo.

Com a Azure Blueprints, a relação entre a definição de planta (o que _deve ser_ implementada) e a atribuição do projeto (que _foi_ implantado) é preservada. Esta ligação suporta procedimentos melhorados de controlo e auditoria para implementações. O Azure Blueprints também pode atualizar várias subscrições ao mesmo tempo que são regidas pelo mesmo plano.

Não há necessidade de escolher entre um modelo ARM e uma planta. Cada planta pode consistir em _artefactos_ de modelo de arma zero ou mais. Este suporte significa que os esforços anteriores para desenvolver e manter uma biblioteca de modelos ARM são reutilizáveis em Azure Blueprints.

## <a name="how-its-different-from-azure-policy"></a>Como difere do Azure Policy

Um esquema é um pacote ou contentor que compõe conjuntos específicos de foco de normas, padrões e requisitos relacionados com a implementação de design, segurança e serviços cloud do Azure, que podem ser reutilizados para manter a consistência e a conformidade.

Uma [política](../policy/overview.md) é um sistema de negação explícita e permissão predefinida focado nas propriedades dos recursos durante a implementação e nos recursos já existentes. Suporta a governação da cloud ao confirmar que os recursos numa subscrição cumprem requisitos e normas.

A inclusão de uma política num esquema permite a criação do padrão ou design certo durante a atribuição do esquema. A inclusão da política assegura que apenas as alterações aprovadas ou esperadas podem ser efetuadas ao ambiente para proteger a conformidade contínua com o objetivo do esquema.

Uma política pode ser incluída como um dos _muitos artefactos_ numa definição de planta. Os esquemas também suportam a utilização de parâmetros com políticas e iniciativas.

## <a name="blueprint-definition"></a>Definição de esquema

Uma planta é composta por _artefactos._ A Azure Blueprints suporta atualmente os seguintes recursos como artefactos:

|Recurso  | Opções de hierarquia| Description  |
|---------|---------|---------|
|Grupos de Recursos | Subscrição | Crie um novo grupo de recursos para utilização por outros artefactos no esquema.  Estes grupos de recursos de espaço reservado permitem-lhe organizar recursos exatamente da forma como os quer estruturados e fornece um limitador de âmbito para artefactos de design de política e função incluídos e modelos ARM. |
|Modelo ARM | Subscrição, Grupo de Recursos | Modelos, incluindo modelos aninhados e ligados, são usados para compor ambientes complexos. Ambientes de exemplo: um farm do SharePoint, a Configuração de Estado da Automatização do Azure ou uma área de trabalho do Log Analytics. |
|Atribuição de Política | Subscrição, Grupo de Recursos | Permite a atribuição de uma política ou iniciativa à subscrição à qual o esquema está atribuído. A política ou iniciativa deve estar no âmbito da definição do projeto. Se a política ou iniciativa tiver parâmetros, estes parâmetros são atribuídos durante a criação ou atribuição do esquema. |
|Atribuição de Função | Subscrição, Grupo de Recursos | Adicione um utilizador ou grupo existente a uma função incorporada para garantir que as pessoas certas têm sempre o acesso adequado aos seus recursos. As atribuições de funções podem ser definidas para a subscrição completa ou aninhadas num grupo de recursos específico incluído no esquema. |

### <a name="blueprint-definition-locations"></a>Locais de definição de plantas

Ao criar uma definição de esquema, irá definir onde o esquema é guardado. As plantas podem ser guardadas para um [grupo de gestão](../management-groups/overview.md) ou subscrição a que tenha acesso **ao Contribuinte.** Se a localização for um grupo de gestão, o projeto está disponível para atribuir a qualquer subscrição infantil desse grupo de gestão.

### <a name="blueprint-parameters"></a>Parâmetros de esquema

As plantas podem passar parâmetros para uma política/iniciativa ou um modelo ARM. Quando adicionar qualquer um dos _artefactos_ a um esquema, o opta por fornecer um valor definido para cada atribuição de esquema ou por permitir que cada atribuição de esquema forneça um valor no momento da atribuição. Esta flexibilidade oferece a possibilidade de definir um valor previamente determinado para todas as utilizações do esquema ou de permitir que essa decisão seja tomada no momento da atribuição.

> [!NOTE]
> Um esquema pode ter os seus próprios parâmetros, mas, atualmente, estes só podem ser criados se um esquema for gerado a partir da API REST, em vez de através do Portal.

Para obter mais informações, veja [parâmetros de esquema](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publicação do esquema

Quando um esquema é criado, considera-se que está no modo de **Rascunho**. Quando estiver pronto para ser atribuído, tem de ser **Publicado**. A publicação exige a definição de uma cadeia de **Versão** (letras, números e hífenes com um comprimento máximo de 20 carateres), juntamente com **Notas de alteração** opcionais. A **Versão** distingue-o de futuras alterações ao mesmo esquema e permite a atribuição de cada versão. Isto também significa que diferentes **Versões** do mesmo esquema podem ser atribuídas à mesma subscrição. Quando são feitas alterações adicionais ao projeto, a Versão **Publicada** 
  ainda existe, assim como as **alterações não publicadas**. Quando as alterações estiverem concluídas, o esquema atualizado é **Publicado** com uma **Versão** nova e exclusiva, que agora também pode ser atribuída.

## <a name="blueprint-assignment"></a>Atribuição do esquema

Cada **versão** **publicada** de uma planta pode ser atribuída (com um comprimento de nome máximo de 90 caracteres) a um grupo de gestão ou subscrição existente. No portal, o esquema utiliza como predefinição a **Versão****Publicada** mais recentemente. Se existirem parâmetros de artefactos ou parâmetros de planta, os parâmetros são definidos durante o processo de atribuição.

> [!NOTE]
> Atribuir uma definição de projeto a um grupo de gestão significa que o objeto de atribuição existe no grupo de gestão. A implantação de artefactos ainda visa uma subscrição. Para realizar uma atribuição de grupo de gestão, a [API Create or Update REST](/rest/api/blueprints/assignments/createorupdate) deve ser utilizada e o organismo de pedido deve incluir um valor para definir a `properties.scope` subscrição-alvo.

## <a name="permissions-in-azure-blueprints"></a>Permissões no Azure Blueprints

Para utilizar plantas, deve ser-lhe concedidas permissões através do [controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md). Para ler ou ver uma planta no portal Azure, a sua conta deve ter lido o acesso ao âmbito onde está localizada a definição de planta.

Para criar esquemas, a sua conta necessita das seguintes permissões:

- `Microsoft.Blueprint/blueprints/write` - Criar uma definição de esquema
- `Microsoft.Blueprint/blueprints/artifacts/write` - Criar artefactos numa definição de esquema
- `Microsoft.Blueprint/blueprints/versions/write` - Publicar um esquema

Para eliminar esquemas, a sua conta necessita das seguintes permissões:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> As permissões de definição de projeto devem ser concedidas ou herdadas no grupo de gestão ou no âmbito de subscrição onde são guardadas.

Para atribuir ou anular a atribuição de um esquema, a sua conta necessita das seguintes permissões:

- `Microsoft.Blueprint/blueprintAssignments/write` - Atribuir um esquema
- `Microsoft.Blueprint/blueprintAssignments/delete` - Anular a atribuição de um esquema

> [!NOTE]
> À medida que as atribuições de esquema são criadas numa subscrição, as permissões de atribuição e anulação da atribuição de esquema têm de ser concedidas no âmbito de uma subscrição ou herdadas para o âmbito de uma subscrição.

Estão disponíveis as seguintes funções incorporadas:

|Função do Azure | Description |
|-|-|
|[Proprietário](../../role-based-access-control/built-in-roles.md#owner) | Além de outras permissões, inclui todas as permissões relacionadas com a Azure Blueprint. |
|[Contribuinte](../../role-based-access-control/built-in-roles.md#contributor) | Além de outras permissões, pode criar e apagar definições de planta, mas não tem permissões de design. |
|[Contribuidor de Projeto](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Pode gerir definições de planta, mas não atribuí-las. |
|[Operador de blueprint](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Pode atribuir plantas publicadas existentes, mas não pode criar novas definições de planta. A atribuição de plantas só funciona se a atribuição for feita com uma identidade gerida atribuída pelo utilizador. |

Se estas funções incorporadas não se adaptarem às suas necessidades de segurança, considere criar uma [função personalizada](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Se utilizar uma identidade gerida atribuída pelo sistema, o titular do serviço para a Azure Blueprints requer a função **do Proprietário** na subscrição atribuída, a fim de permitir a implementação. Se utilizar o portal, esta função é automaticamente concedida e revogada para a implementação. Se utilizar a API REST, esta função tem de ser concedida manualmente, mas continua a ser revogada automaticamente depois de concluída a implementação. Se utilizar uma identidade gerida atribuída pelo utilizador, apenas o utilizador que cria a atribuição de plantas necessita da `Microsoft.Blueprint/blueprintAssignments/write` permissão, que está incluída tanto nas funções incorporadas **do Proprietário** como **do Operador de Blueprint.**

## <a name="naming-limits"></a>Limites de nomeação

Existem as seguintes limitações para determinados campos:

|Objeto|Campo|Personagens permitidos|Um máximo de Comprimento|
|-|-|-|-|
|Planta|Name|letras, números, hífens e períodos|48|
|Planta|Versão|letras, números, hífens e períodos|20|
|Atribuição do esquema|Name|letras, números, hífens e períodos|90|
|Artefacto de planta|Name|letras, números, hífens e períodos|48|

## <a name="video-overview"></a>Descrição geral em vídeo

A seguinte visão geral da Azure Blueprints é da Azure Fridays. Para download de vídeo, visite [Azure Fridays - Uma visão geral do Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) no Canal 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Passos seguintes

- [Criar uma planta - Portal](./create-blueprint-portal.md).
- [Criar uma planta - PowerShell](./create-blueprint-powershell.md).
- [Criar uma planta - REST API](./create-blueprint-rest-api.md).