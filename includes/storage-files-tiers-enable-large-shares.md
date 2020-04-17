---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536501"
---
Por predefinição, as ações de ficheiros padrão podem abranger apenas até 5 TiB, embora o limite de ações possa ser aumentado para 100 TiB. Para tal, a funcionalidade de partilha de *ficheiros de grande dimensão* deve ser ativada ao nível da conta de armazenamento. As contas de armazenamento premium (contas de armazenamento de*filestorage)* não têm a grande bandeira de partilha de ficheiros, uma vez que todas as ações de ficheiropremium já estão habilitadas para o fornecimento até à capacidade total de 100 TiB.

Só pode ativar grandes partilhas de ficheiros em contas de armazenamento padrão redundantes localmente redundantes ou redundantes em zonas. Uma vez ativado a grande bandeira de partilha de ficheiros, não pode alterar o nível de redundância para armazenamento geo-redundante ou geo-zona-redundante.

Para permitir grandes partilhas de ficheiros numa conta de armazenamento existente, navegue para a vista **de Configuração** na tabela de conteúdos da conta de armazenamento e troque o interruptor de roqueiro de partilha de ficheiros para ativar:

![Uma imagem do interruptor de rocker de partilha de ficheiros no portal Azure](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Também pode ativar 100 partilhas [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) de ficheiros TiB através do cmdlet PowerShell e do [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) comando Azure CLI. Para obter instruções detalhadas sobre a ativação de grandes partilhas de ficheiros, consulte [ativar e criar grandes partilhas](../articles/storage/files/storage-files-how-to-create-large-file-share.md)de ficheiros .

Para saber mais sobre como criar ações de ficheiros em novas contas de armazenamento, consulte a criação de uma partilha de [ficheiros Azure](../articles/storage/files/storage-how-to-create-file-share.md).
