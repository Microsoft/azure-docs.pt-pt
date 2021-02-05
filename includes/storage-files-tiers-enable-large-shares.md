---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99569548"
---
Por padrão, as ações de ficheiros padrão podem abranger apenas até 5 TiB, mas pode aumentar o limite de ações para 100 TiB. Para aumentar o seu limite de ações, ative **a grande quota de ficheiro** na sua conta de armazenamento. As contas de armazenamento premium (Contas de armazenamento *FileStorage)* não têm o grande pavilhão de funcionalidades de partilha de ficheiros, uma vez que todas as ações de ficheiros premium já estão habilitados para o provisionamento até à capacidade total de 100-TiB.

Só é possível permitir grandes ações de ficheiros em contas de armazenamento padrão redundantes ou redundantes locais ou na zona. Uma vez ativado a bandeira de recurso de grande porção, não pode alterar o nível de redundância para armazenamento geo-redundante ou de zona geo-redundante.

Para ativar grandes ações de ficheiros numa conta de armazenamento existente, navegue para **arquivar ações** na tabela de conteúdos da conta de armazenamento.
Nesta lâmina, **selecione a capacidade de partilha,** altere a capacidade de partilha para **100 TiB** e selecione **Save**.

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="Uma imagem da definição de opt-in de partilha de ficheiros avultado no portal Azure." lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

Também pode ativar ações de ficheiros de 100 TiB através do [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) cmdlet PowerShell e do [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) comando Azure CLI. Para obter instruções detalhadas sobre a ativação de partilhas de ficheiros grandes, consulte [ativar e criar grandes partilhas de ficheiros](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Para saber mais sobre como criar ações de ficheiros em novas contas de armazenamento, consulte [a criação de uma partilha de ficheiros Azure](../articles/storage/files/storage-how-to-create-file-share.md).