---
title: Identidade gerida no espaço de trabalho da Sinapse
description: Um artigo que explica a identidade gerida no espaço de trabalho da Azure Synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 10/16/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 7790bc2895449e8ab21cbd30d7da0e5529eb0562
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101670682"
---
# <a name="azure-synapse-workspace-managed-identity"></a>Identidade gerida da área de trabalho do Azure Synapse

Neste artigo, você vai aprender sobre identidade gerida no espaço de trabalho Azure Synapse.

## <a name="managed-identities"></a>Identidades geridas

A identidade gerida para recursos Azure é uma característica do Azure Ative Directory. Oferece aos serviços do Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar a capacidade de Identidade Gerida para autenticar qualquer serviço que suporte a autenticação AD Azure.

Identidades geridas para recursos Azure são o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerido (MSI). Consulte [identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md) para saber mais.

## <a name="azure-synapse-workspace-managed-identity"></a>Identidade gerida da área de trabalho do Azure Synapse

É criada uma identidade gerida atribuída pelo sistema para a sua área de trabalho do Azure Synapse quando cria a área de trabalho.

>[!NOTE]
>Esta identidade gerida pelo espaço de trabalho será referida como identidade gerida através do resto deste documento.

A Azure Synapse utiliza a identidade gerida para integrar os oleodutos. O ciclo de vida de identidade gerido está diretamente ligado ao espaço de trabalho Azure Synapse. Se eliminar o espaço de trabalho Azure Synapse, então a identidade gerida também é limpa.

O espaço de trabalho gerido identidade precisa de permissões para realizar operações nos oleodutos. Pode utilizar o ID do objeto ou o nome do espaço de trabalho Azure Synapse para encontrar a identidade gerida ao conceder permissões.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Recupere identidade gerida no portal Azure

Pode recuperar a identidade gerida no portal Azure. Abra o seu espaço de trabalho Azure Synapse no portal Azure e selecione **a visão geral** da navegação à esquerda. O ID do objeto gerido é apresentado no ecrã principal.

![ID de objeto de identidade gerido](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

As informações de identidade geridas também aparecerão quando criar um serviço ligado que suporte a autenticação de identidade gerida a partir do Azure Synapse Studio.

Lance **o Azure Synapse Studio** e selecione o **separador Gerir** a partir da navegação à esquerda. Em seguida, selecione **serviços Linked** e escolha a opção **+ Nova** para criar um novo serviço ligado.

![Criação de serviços ligados 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

Na nova janela **de serviço ligada,** *escreva Azure Data Lake Storage Gen2*. Selecione o tipo de recurso **Azure Data Lake Storage Gen2** da lista abaixo e escolha **Continue**.

![Criação de serviços ligados 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

Na janela seguinte, escolha **identidade gerida** para método **de autenticação**. Você verá o **nome** da identidade gerida e **identificação de objeto.**

![Criação de serviços ligado 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [a concessão de permissões ao espaço de trabalho Azure Synapse gerido identidade gerida](./how-to-grant-workspace-managed-identity-permissions.md)
