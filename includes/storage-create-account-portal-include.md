---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 04/15/2021
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5798ad8721d8bf2924aa97876d0c8354681d3568
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718205"
---
Para criar uma conta de armazenamento para fins gerais v2 no portal do Azure, siga estes passos:

1. No menu do portal do Azure, selecione **Todos os serviços**. Na lista de recursos, escreva **Contas de Armazenamento**. À medida que começa a escrever, a lista filtra com base na sua entrada. Selecione **Contas de Armazenamento**.
1. Na janela **contas de armazenamento** que aparece, escolha + **Novo**.
1. Na lâmina **Basics,** selecione a subscrição na qual criará a conta de armazenamento.
1. No campo **do grupo Recursos,** selecione o grupo de recursos pretendido ou crie um novo grupo de recursos.  Para obter mais informações sobre os grupos de recursos Azure, consulte [a visão geral do Azure Resource Manager](../articles/azure-resource-manager/management/overview.md).
1. A seguir, introduza um nome para a sua conta de armazenamento. O nome que escolher tem de ser exclusivo em todo o Azure. O nome também deve ter entre 3 e 24 caracteres de comprimento, podendo incluir apenas números e letras minúsculas.
1. Selecione uma região para a sua conta de armazenamento ou use a região predefinitiva.
1. Selecione um nível de desempenho. O nível predefinido é *Standard*.
1. Especifique como a conta de armazenamento será replicada. A opção de despedimento por defeito é *o armazenamento geo-redundante (GRS)*. Para obter mais informações sobre as opções de replicação disponíveis, consulte [a redundância do Azure Storage](../articles/storage/common/storage-redundancy.md).
1. Estão disponíveis opções adicionais nas lâminas **Avançada,** **Networking,** **Proteção de Dados** e **Tags.** Para utilizar o Azure Data Lake Storage, escolha a lâmina **avançada** e, em seguida, coloque o **espaço de nome hierárquico** para **Ativado**. Para mais informações, consulte [Azure Data Lake Storage Gen2 Introdução](../articles/storage/blobs/data-lake-storage-introduction.md)
1. Selecione **Rever + Criar** para rever as definições de conta de armazenamento e criar a conta.
1. Selecione **Criar**.

A imagem a seguir mostra as definições na lâmina **Basics** para uma nova conta de armazenamento:

:::image type="content" source="media/storage-create-account-portal-include/account-create-portal.png" alt-text="Screenshot mostrando como criar uma conta de armazenamento no portal Azure." lightbox="media/storage-create-account-portal-include/account-create-portal.png":::
