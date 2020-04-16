---
title: Identidade gerida no espaço de trabalho Azure Synapse
description: Um artigo que explica a identidade gerida no espaço de trabalho do Azure Synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ee0e6249acf3fbbab369d42ae691a5a826df1ee8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81425120"
---
# <a name="azure-synapse-workspace-managed-identity-preview"></a>Azure Synapse workspace gerido identidade (pré-visualização)

Neste artigo, você vai aprender sobre identidade gerida no espaço de trabalho Azure Synapse.

## <a name="managed-identities"></a>Identidades geridas

A identidade gerida para os recursos Azure é uma característica do Azure Ative Directory. Oferece aos serviços do Azure uma identidade gerida automaticamente no Azure AD. Pode utilizar a capacidade de Identidade Gerida para autenticar qualquer serviço que suporte a autenticação Azure AD.

Identidades geridas para recursos Azure são o novo nome para o serviço anteriormente conhecido como Identidade de Serviço Gerido (MSI). Ver [Identidades Geridas](../../active-directory/managed-identities-azure-resources/overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para saber mais.

## <a name="azure-synapse-workspace-managed-identity"></a>Azure Synapse workspace gerido identidade

Uma identidade gerida atribuída ao sistema é criada para o seu espaço de trabalho Azure Synapse quando criar o espaço de trabalho.

>[!NOTE]
>Este espaço de trabalho gerido identidade será referido como identidade gerida através do resto deste documento.

Azure Synapse usa a identidade gerida para orquestrar oleodutos. O ciclo de vida de identidade gerido está diretamente ligado ao espaço de trabalho Azure Synapse. Se eliminar o espaço de trabalho Azure Synapse, então a identidade gerida também é limpa.

A identidade gerida pelo espaço de trabalho necessita de permissões para realizar operações nos oleodutos. Pode utilizar o ID do objeto ou o seu nome de espaço de trabalho Azure Synapse para encontrar a identidade gerida ao conceder permissões.

## <a name="retrieve-managed-identity-in-azure-portal"></a>Recuperar identidade gerida no portal Azure

Pode recuperar a identidade gerida no portal Azure. Abra o seu espaço de trabalho Azure Synapse no portal Azure e selecione **Visão Geral** da navegação à esquerda. O ID do objeto da identidade gerida é apresentado no ecrã principal.

![ID de objeto de identidade gerido](./media/synapse-workspace-managed-identity/workspace-managed-identity-1.png)

As informações de identidade geridas também aparecerão quando criar um serviço ligado que suporta a autenticação de identidade gerida do Azure Synapse Studio.

Lance **o Azure Synapse Studio** e selecione o separador **Manage** a partir da navegação à esquerda. Em seguida, selecione **os serviços Linked** e escolha a **opção + Nova** para criar um novo serviço ligado.

![Criação de serviço ligado 1](./media/synapse-workspace-managed-identity/workspace-managed-identity-2.png)

Na nova janela **de serviço ligada,** *escreva Azure Data Lake Storage Gen2*. Selecione o tipo de recurso **Azure Data Lake Storage Gen2** da lista abaixo e escolha **Continuar**.

![Criação de serviço seleto 2](./media/synapse-workspace-managed-identity/workspace-managed-identity-3.png)

Na janela seguinte, escolha **identidade gerida** para método **de autenticação**. Verá o **nome** e **identificação**do objeto geridos.

![Criação de serviço seleto 3](./media/synapse-workspace-managed-identity/workspace-managed-identity-4.png)

## <a name="next-steps"></a>Passos seguintes

[Concessão de permissões à identidade gerida pelo espaço de trabalho Azure Synapse](./how-to-grant-workspace-managed-identity-permissions.md)
