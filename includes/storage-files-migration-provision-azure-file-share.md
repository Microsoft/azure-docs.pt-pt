---
title: Considerações para o fornecimento de ações de ficheiros Azure
description: Provision Azure partilha si para utilização com o Azure File Sync. Um bloco de texto comum, partilhado em todos os documentos migratórios.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: d48baba5ee60a2bf5a4cb5e4d1ce840fce8eec43
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143572"
---
Uma partilha de ficheiros Azure é armazenada na nuvem numa conta de armazenamento Azure.
Há outro nível de considerações de desempenho aqui.

Se tiver ações altamente ativas (ações utilizadas por muitos utilizadores e/ou aplicações), então duas ações de ficheiroS Azure podem atingir o limite de desempenho de uma conta de armazenamento.

A melhor prática é implementar contas de armazenamento com uma partilha de ficheiros cada.
Pode juntar várias ações de ficheiros Azure na mesma conta de armazenamento, caso tenha ações de arquivo ou espere atividade sem valor diário.

Estas considerações aplicam-se mais ao acesso direto à nuvem (através de um VM Azure) do que ao Azure File Sync. Se planeia utilizar o Azure File Sync apenas nestas ações, então agrupar várias numa única conta de armazenamento Azure é bom.

Se fez uma lista das suas ações, deve mapear cada ação para a conta de armazenamento em que residirá.

Na fase anterior, determinou o número adequado de ações. Neste passo, criou um mapeamento de contas de armazenamento para arquivar ações. Agora implemente o número adequado de contas de armazenamento Azure com o número adequado de ações de ficheiros Azure nas mesmas.

Certifique-se de que a região de cada uma das suas contas de armazenamento é a mesma e corresponde à região do recurso do Serviço de Sincronização de Armazenamento que já implementou.

> [!CAUTION]
> Se criar uma partilha de ficheiros Azure que tenha um limite de 100 TiB, essa parte só pode utilizar opções de armazenamento redundante localmente ou de redundância de armazenamento redundante. Considere as suas necessidades de redundância de armazenamento antes de usar as ações de ficheiros 100-TiB.

As ações de ficheiros Azure ainda são criadas com um limite de 5 TiB por padrão. Como está a criar novas contas de armazenamento, certifique-se de seguir as orientações para criar contas de [armazenamento que permitam ações de ficheiros Azure com limites de 100 TiB](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Outra consideração quando está a implementar uma conta de armazenamento é a redundância do Armazenamento Azure. Consulte opções de [redundância de armazenamento azure.](../articles/storage/common/storage-redundancy.md)

Os nomes dos seus recursos também são importantes. Por exemplo, se agrupar várias ações para o departamento de RH numa conta de armazenamento Azure, deve nomear a conta de armazenamento adequadamente. Da mesma forma, ao nomear as suas ações de ficheiro Saque, deve utilizar nomes semelhantes aos utilizados para os seus homólogos no local.