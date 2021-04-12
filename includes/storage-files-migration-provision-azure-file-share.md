---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: c003e765574d764b6653823e8554d0718ea85e0e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081109"
---
Uma partilha de ficheiros Azure é armazenada na nuvem numa conta de armazenamento Azure.
Há outro nível de considerações de desempenho aqui.

Se tiver ações altamente ativas (ações utilizadas por muitos utilizadores e/ou aplicações), então duas ações de ficheiros Azure poderão atingir o limite de desempenho de uma conta de armazenamento.

Uma boa prática é implementar contas de armazenamento com uma partilha de ficheiros cada.
Você pode juntar várias ações de arquivo Azure na mesma conta de armazenamento, caso você tenha ações de arquivo ou você espera uma atividade baixa no dia-a-dia nelas.

Estas considerações aplicam-se mais ao acesso direto à nuvem (através de um VM Azure) do que ao Azure File Sync. Se planeia utilizar o Azure File Sync apenas nestas ações, então agrupar várias numa única conta de armazenamento Azure está bem.

Se fez uma lista das suas ações, deve mapear cada ação para a conta de armazenamento em que reside.

Na fase anterior, determinou o número adequado de ações. Neste passo, criou-se um mapeamento de contas de armazenamento para arquivar ações. Agora implemente o número adequado de contas de armazenamento Azure com o número adequado de ações de ficheiros Azure nelas.

Certifique-se de que a região de cada uma das suas contas de armazenamento é a mesma e corresponde à região do recurso Storage Sync Service que já implementou.

> [!CAUTION]
> Se criar uma partilha de ficheiros Azure que tenha um limite de 100 TiB, essa partilha só pode usar armazenamento localmente redundante ou opções de despedimento de armazenamento redundante. Considere as suas necessidades de redundância de armazenamento antes de usar ações de ficheiros 100-TiB.

As ações de ficheiros Azure ainda são criadas com um limite de 5-TiB por padrão. Como está a criar novas contas de armazenamento, certifique-se de seguir as [orientações para criar contas de armazenamento que permitam ações de ficheiros Azure com limites de 100-TiB](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Outra consideração quando se está a implantar uma conta de armazenamento é a redundância do Azure Storage. Consulte [as opções de redundância do Azure Storage](../articles/storage/common/storage-redundancy.md).

Os nomes dos seus recursos também são importantes. Por exemplo, se agrupar várias ações para o departamento de RH numa conta de armazenamento Azure, deve nomear a conta de armazenamento adequadamente. Da mesma forma, ao nomear as suas ações de ficheiroS Azure, deverá utilizar nomes semelhantes aos utilizados para as suas congéneres no local.