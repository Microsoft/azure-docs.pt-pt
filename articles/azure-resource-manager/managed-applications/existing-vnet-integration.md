---
title: Implementar para a rede virtual existente
description: Descreve como permitir que os utilizadores da sua aplicação gerida selecionem uma rede virtual existente. A rede virtual pode estar fora da aplicação gerida.
author: tfitzmac
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: tomfitz
ms.openlocfilehash: fa5e59b96aada06c2dd486094d9be6a52c79e43e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84261290"
---
# <a name="use-existing-virtual-network-with-azure-managed-applications"></a>Utilize a rede virtual existente com aplicações geridas aZure

Este artigo mostra-lhe como definir uma Aplicação Gerida Azure que se integra com uma rede virtual existente na subscrição do consumidor. A aplicação gerida permite ao consumidor decidir se cria uma nova rede virtual ou se utiliza uma existente. A rede virtual existente pode estar fora do grupo de recursos geridos.

## <a name="main-template"></a>Modelo principal

Primeiro, vamos ver o **mainTemplate.jsarquivado.** Todo o modelo para a implementação de uma máquina virtual e os seus recursos associados é mostrado abaixo. Mais tarde, irá examinar mais de perto as partes do modelo que estão relacionadas com a utilização de uma rede virtual existente.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

Note que a rede virtual está [implantada condicionalmente.](../templates/conditional-resource-deployment.md) O consumidor passa num valor de parâmetro que indica se cria uma nova rede virtual ou utilização. Se o consumidor selecionar uma nova rede virtual, o recurso é implantado. Caso contrário, o recurso é ignorado durante a implantação.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="2":::

A variável para o ID de rede virtual tem duas propriedades. Uma propriedade devolve o ID do recurso quando uma nova rede virtual é implantada. A outra propriedade devolve o ID do recurso quando uma rede virtual existente é usada. O ID de recursos para a rede virtual existente inclui o nome do grupo de recursos que contém a rede virtual.

O ID da sub-rede é construído a partir do valor para o ID de rede virtual. Utiliza o valor que corresponde à seleção dos consumidores.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="6-10":::

A interface de rede está definida para a variável ID da sub-rede.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="16":::

## <a name="ui-definition"></a>Definição de UI

Agora, vamos ver a **createUiDefinition.jsarquivada.** Todo o ficheiro é:

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

O ficheiro inclui um elemento de rede virtual.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="53-81":::

Este elemento permite ao consumidor selecionar uma rede virtual nova ou existente.

:::image type="content" source="./media/existing-vnet-integration/new-or-existing-vnet.png" alt-text="Rede virtual nova ou existente":::

Nas saídas, inclui-se um valor que indica se o consumidor selecionou uma rede virtual nova ou existente. Há também um valor de identidade gerido.

> [!NOTE]
> O valor de saída para a identidade gerida deve ser nomeado **gerenciadoIdentity**.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="6,12":::

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a criação do ficheiro de definição de UI, consulteCreateUiDefinition.jspara a [experiência de criação da aplicação gerida pelo Azure.](create-uidefinition-overview.md)
