---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/25/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: bed8475e5d6c7bf26003672b6cf9ce51a82384ad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91376463"
---
Para criar uma conta de armazenamento para fins gerais v2 no portal do Azure, siga estes passos:

1. No menu do portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Contas de Armazenamento**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Contas de Armazenamento**.
1. Na janela **Contas de Armazenamento** que é apresentada, escolha **Adicionar**.
1. No **separador Básicos,** selecione a subscrição na qual criar a conta de armazenamento.
1. No campo **do grupo Recursos,** selecione o grupo de recursos pretendido ou crie um novo grupo de recursos.  Para obter mais informações sobre os grupos de recursos Azure, consulte [a visão geral do Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md).
1. A seguir, introduza um nome para a sua conta de armazenamento. O nome que escolher tem de ser exclusivo em todo o Azure. O nome também deve ter entre 3 e 24 caracteres de comprimento, podendo incluir apenas números e letras minúsculas.
1. Selecione uma localização para a sua conta de armazenamento ou utilize a localização predefinida.
1. Selecione um nível de desempenho. O nível predefinido é *Standard*.
1. Desagravar o campo **de tipo conta** para o armazenamento *V2 (v2 de uso geral)*.
1. Especifique como a conta de armazenamento será replicada. A opção de replicação predefinida é *o armazenamento geo-redundante de acesso à leitura (RA-GRS)*. Para obter mais informações sobre as opções de replicação disponíveis, consulte [a redundância do Azure Storage](../articles/storage/common/storage-redundancy.md).
1. Especifique o nível de acesso para bolhas na conta de armazenamento. O nível predefinido é *quente*. Para obter mais informações sobre os níveis de acesso blob, consulte [os níveis de acesso quentes, frescos e arquivados para bolhas.](../articles/storage/blobs/storage-blob-storage-tiers.md)
1. Para utilizar o Azure Data Lake Storage, escolha o separador **Avançado** e, em seguida, desempate o **espaço de nome hierárquico** para **Ativado**. Para mais informações, consulte [Azure Data Lake Storage Gen2 Introdução](../articles/storage/blobs/data-lake-storage-introduction.md)
1. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.
1. Selecione **Criar**.

A imagem a seguir mostra as definições no **separador Básicos** para uma nova conta de armazenamento:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Captura de ecrã a mostrar como criar uma conta de armazenamento no portal do Azure":::
