---
title: Descrição Geral do Azure Blueprints
description: Understand how the Azure Blueprints service enables you to create, define, and deploy artifacts in your Azure environment.
ms.date: 11/21/2019
ms.topic: overview
ms.openlocfilehash: 07d84d658d88e977cd73176861e5c5e080c02857
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74321769"
---
# <a name="what-is-azure-blueprints"></a>O que é o Azure Blueprints?

Da mesma forma que um esquema permite que um engenheiro ou um arquiteto crie um esboço dos parâmetros de design de um projeto, o Azure Blueprints permite que os arquitetos da cloud e os grupos de tecnologias de informação definam um conjunto repetível de recursos do Azure que implemente e adira às normas, padrões e requisitos de uma organização. O Azure Blueprints permite que as equipas de desenvolvimento criem e edifiquem rapidamente novos ambientes, com a confiança de que estão a ser criados no âmbito da conformidade organizacional com um conjunto de componentes incorporados, tais como rede, para acelerar o desenvolvimento e a entrega.

Os esquemas são uma forma declarativa de orquestrar a implementação de vários modelos de recursos e de outros artefactos, tais como:

- Atribuições de Funções
- Atribuições de Política
- Modelos do Azure Resource Manager
- Grupos de Recursos

O serviço do Azure Blueprints é apoiado pelo [Azure Cosmos DB](../../cosmos-db/introduction.md) globalmente distribuído.
Os objetos do Blueprint são replicados para várias regiões do Azure. Esta replicação oferece baixa latência, elevada disponibilidade e acesso consistente aos seus objetos de esquema, independentemente da região em que o Blueprints implementa os seus recursos.

## <a name="how-its-different-from-resource-manager-templates"></a>Em que medida difere dos modelos do Resource Manager

O serviço foi concebido para ajudar na _configuração do ambiente_. Esta configuração consiste frequentemente num conjunto de grupos de recursos, políticas, atribuições de funções e implementações de modelos do Resource Manager. Um esquema é um pacote que reúne cada um destes tipos de _artefacto_ e permite compor e criar uma versão desse pacote, nomeadamente através de um pipeline CI/CD. Por fim, cada um deles é atribuído a uma subscrição numa única operação que pode ser auditada e controlada.

Quase tudo o que pretende incluir para implementação no Blueprints pode ser feito com um modelo do Resource Manager. No entanto, um modelo do Resource Manager é um documento que não existe nativamente no Azure – cada modelo é armazenado localmente ou no controlo de origem. O modelo é utilizado para implementações de um ou mais recursos do Azure, mas assim que esses recursos são implementados, não existe nenhuma ligação ou relação ativa com o modelo.

Com o Blueprints, a relação entre a definição do esquema (o que _deve ser_ implementado) e a atribuição do esquema (o que _foi_ implementado) é preservada. Esta ligação suporta melhor controlo e auditoria de implementações. O Blueprints também pode atualizar várias subscrições ao mesmo tempo regidas pelo mesmo esquema.

Não é necessário escolher entre um modelo do Resource Manager e um esquema. Cada esquema pode ser constituído por zero ou mais _artefactos_ de modelo do Resource Manager. Este suporte significa que os esforços anteriores para desenvolver e manter uma biblioteca de modelos do Resource Manager são reutilizados no Blueprints.

## <a name="how-its-different-from-azure-policy"></a>Em que medida difere do Azure Policy

Um esquema é um pacote ou contentor para compor conjuntos específicos de foco de normas, padrões e requisitos relacionados com a implementação de serviços cloud do Azure, segurança e design, que podem ser reutilizados para manter a consistência e a conformidade.

Uma [política](../policy/overview.md) é um sistema de negação explícita e permissão predefinida focado nas propriedades dos recursos durante a implementação e nos recursos já existentes. Suporta governação da cloud ao validar que os cursos numa subscrição aderem a requisitos e normas.

A inclusão de uma política num esquema permite a criação do design ou padrão de direitos durante a atribuição do esquema. A inclusão da política assegura que apenas as alterações aprovadas ou esperadas podem ser efetuadas ao ambiente para proteger a conformidade contínua com a intenção do esquema.

A policy can be included as one of many _artifacts_ in a blueprint definition. Os esquemas também suportam a utilização de parâmetros com políticas e iniciativas.

## <a name="blueprint-definition"></a>Definição de esquema

Um esquema é composto por _artefactos_. Atualmente, os esquemas suportam os seguintes recursos como artefactos:

|Recurso  | Opções de hierarquia| Descrição  |
|---------|---------|---------|
|Grupos de Recursos | Subscrição | Crie um novo grupo de recursos para utilização por outros artefactos no esquema.  Estes grupos de recursos de marcador de posição permitem-lhe organizar recursos exatamente da forma que pretende que sejam estruturados e fornece um limitador de âmbito para a política incluída e os artefactos de atribuição de funções, bem como modelos do Azure Resource Manager. |
|Modelo Azure Resource Manager | Subscrição, Grupo de Recursos | Templates, including nested and linked templates, are used to compose complex environments. Ambientes de exemplo: um farm do SharePoint, a Configuração de Estado da Automatização do Azure ou uma área de trabalho do Log Analytics. |
|Atribuição de Política | Subscrição, Grupo de Recursos | Permite a atribuição de uma política ou iniciativa à subscrição à qual o esquema está atribuído. The policy or initiative must be within the scope of the blueprint definition location. Se a política ou iniciativa tiver parâmetros, estes parâmetros são atribuídos durante a criação ou atribuição do esquema. |
|Atribuição de Função | Subscrição, Grupo de Recursos | Adicione um utilizador ou grupo existente a uma função incorporada para garantir que as pessoas certas têm sempre o acesso adequado aos seus recursos. As atribuições de funções podem ser definidas para a subscrição completa ou aninhadas num grupo de recursos específico incluído no esquema. |

### <a name="blueprint-definition-locations"></a>Blueprint definition locations

Ao criar uma definição de esquema, irá definir onde o esquema é guardado. Blueprints can be saved to a [management group](../management-groups/overview.md) or subscription that you have **Contributor** access to. If the location is a management group, the blueprint is available to assign to any child subscription of that management group.

### <a name="blueprint-parameters"></a>Parâmetros de esquema

Os esquemas podem passar parâmetros para uma política/iniciativa ou para um modelo do Azure Resource Manager.
Quando adicionar qualquer um dos _artefactos_ a um esquema, o opta por fornecer um valor definido para cada atribuição de esquema ou por permitir que cada atribuição de esquema forneça um valor no momento da atribuição.
Esta flexibilidade oferece a possibilidade de definir um valor previamente determinado para todas as utilizações do esquema ou de permitir que essa decisão seja tomada no momento da atribuição.

> [!NOTE]
> Um esquema pode ter os seus próprios parâmetros, mas, atualmente, estes só podem ser criados se um esquema for gerado a partir da API REST, em vez de através do Portal.

Para obter mais informações, veja [parâmetros de esquema](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publicação do esquema

Quando um esquema é criado, considera-se que está no modo de **Rascunho**. Quando estiver pronto para ser atribuído, tem de ser **Publicado**. A publicação exige a definição de uma cadeia de **Versão** (letras, números e hífenes com um comprimento máximo de 20 carateres), juntamente com **Notas de alteração** opcionais. A **Versão** distingue-o de futuras alterações ao mesmo esquema e permite a atribuição de cada versão. Isto também significa que diferentes **Versões** do mesmo esquema podem ser atribuídas à mesma subscrição. When additional changes are made to the blueprint, the **Published**
**Version** still exists, as do the **Unpublished changes**. Quando as alterações estiverem concluídas, o esquema atualizado é **Publicado** com uma **Versão** nova e exclusiva, que agora também pode ser atribuída.

## <a name="blueprint-assignment"></a>Atribuição do esquema

Each **Published** **Version** of a blueprint can be assigned (with a max name length of 90 characters) to an existing subscription. No portal, o esquema utiliza como predefinição a **Versão** **Publicada** mais recentemente. Se existirem parâmetros de artefacto (ou parâmetros de esquema), os parâmetros são definidos durante o processo de atribuição.

## <a name="permissions-in-azure-blueprints"></a>Permissões no Azure Blueprints

Para utilizar esquemas, tem de ter permissões concedidas através do [Controlo de acesso baseado em funções](../../role-based-access-control/overview.md) (RBAC). Para criar esquemas, a sua conta necessita das seguintes permissões:

- `Microsoft.Blueprint/blueprints/write` - Criar uma definição de esquema
- `Microsoft.Blueprint/blueprints/artifacts/write` - Criar artefactos numa definição de esquema
- `Microsoft.Blueprint/blueprints/versions/write` - Publicar um esquema

Para eliminar esquemas, a sua conta necessita das seguintes permissões:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> The blueprint definition permissions must be granted or inherited on the management group or subscription scope where it is saved.

Para atribuir ou anular a atribuição de um esquema, a sua conta necessita das seguintes permissões:

- `Microsoft.Blueprint/blueprintAssignments/write` - Atribuir um esquema
- `Microsoft.Blueprint/blueprintAssignments/delete` - Anular a atribuição de um esquema

> [!NOTE]
> À medida que as atribuições de esquema são criadas numa subscrição, as permissões de atribuição e anulação da atribuição de esquema têm de ser concedidas no âmbito de uma subscrição ou herdadas para o âmbito de uma subscrição.

The following built-in roles are available:

|RBAC Role | Descrição |
|-|-|
|[Proprietário](../../role-based-access-control/built-in-roles.md#owner) | In addition to other permissions, includes all Azure Blueprint related permissions. |
|[Contribuidor](../../role-based-access-control/built-in-roles.md#contributor) | In addition to other permissions, can create and delete blueprint definitions, but doesn't have blueprint assignment permissions. |
|[Blueprint Contributor](../../role-based-access-control/built-in-roles.md#blueprint-contributor) | Can manage blueprint definitions, but not assign them. |
|[Blueprint Operator](../../role-based-access-control/built-in-roles.md#blueprint-operator) | Can assign existing published blueprints, but can't create new blueprint definitions. Blueprint assignment only works if the assignment is done with a user-assigned managed identity. |

Se estas funções incorporadas não se adaptarem às suas necessidades de segurança, considere criar uma [função personalizada](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> If using a system-assigned managed identity, the service principal for Azure Blueprints requires the **Owner** role on the assigned subscription in order to enable deployment. Se utilizar o portal, esta função é automaticamente concedida e revogada para a implementação. Se utilizar a API REST, esta função tem de ser concedida manualmente, mas continua a ser revogada automaticamente depois de concluída a implementação. If using a user-assigned managed identity, only the user creating the blueprint assignment needs the `Microsoft.Blueprint/blueprintAssignments/write` permission, which is included in both the **Owner** and **Blueprint Operator** built-in roles.

## <a name="naming-limits"></a>Naming limits

The following limitations exist for certain fields:

|Object|Campo|Allowed Characters|Um máximo de Comprimento|
|-|-|-|-|
|Blueprint|Nome|letters, numbers, hyphens, and periods|48|
|Blueprint|Versão|letters, numbers, hyphens, and periods|20|
|Atribuição do esquema|Nome|letters, numbers, hyphens, and periods|90|
|Blueprint artifact|Nome|letters, numbers, hyphens, and periods|48|

## <a name="video-overview"></a>Video overview

The following overview of Azure Blueprints is from Azure Fridays. For video download, visit [Azure Fridays - An overview of Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) on Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Passos seguintes

- [Create a blueprint - Portal](./create-blueprint-portal.md).
- [Create a blueprint - PowerShell](./create-blueprint-powershell.md).
- [Create a blueprint - REST API](./create-blueprint-rest-api.md).