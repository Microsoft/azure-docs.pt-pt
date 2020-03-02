---
title: Considerações para o fornecimento de ações de ficheiros Azure.
description: Provision Azure partilha si para utilização com o Azure File Sync. Um bloco de texto comum, partilhado entre documentos de migração.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209431"
---
Uma partilha de ficheiros Azure é armazenada na nuvem numa conta de armazenamento Azure.
Há outro nível de considerações de desempenho aqui.

Se tiver ações altamente ativas - ações utilizadas por muitos utilizadores e/ou aplicações, então duas ações de ficheiros Azure podem atingir o limite de desempenho de uma conta de armazenamento.

A melhor prática é implementar contas de armazenamento com uma partilha de ficheiros cada.
Pode juntar várias ações de ficheiros Azure na mesma conta de armazenamento, caso tenha ações de arquivo ou espere atividade sem valor diário.

Estas considerações aplicam-se mais ao acesso direto à nuvem (através de um VM Azure) do que aplicam-se ao Azure File Sync. Se planeia utilizar apenas o Azure File Sync nestas ações, então agrupar várias numa única conta de armazenamento Azure é bom.

Se fez uma lista das suas ações, então deve mapear cada ação para a conta de armazenamento em que residirão.

Na fase anterior, determinou o número adequado de ações. Neste passo, criou um mapeamento de contas de armazenamento para arquivar ações. Implemente o número agora adequado de contas de armazenamento Azure com o número adequado de ações de ficheiros Azure nas mesmas.

Certifique-se de que a região de cada uma das suas contas de armazenamento é a mesma e corresponde à região do recurso do Serviço de Sincronização de Armazenamento que já implementou.

> [!CAUTION]
> Se criar uma quota de ficheiro supor 100 TiB limit Azure, essa partilha só pode utilizar opções de armazenamento redundantes localmente ou de redundância de armazenamento redundante. Considere as suas necessidades de redundância de armazenamento antes de usar 100 ações de ficheiro TiB.

As ações de ficheiros Azure ainda são criadas com um limite de 5 TiB por padrão. Uma vez que está a criar novas contas de armazenamento, certifique-se de seguir as orientações para criar contas de [armazenamento que permitam ações de ficheiros Azure com 100 limites de TiB](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Outra consideração ao implementar uma conta de armazenamento, é a redundância do seu armazenamento Azure. Ver: Opções de [redundância](../articles/storage/common/storage-redundancy.md)de armazenamento azure .

Os nomes dos seus recursos também são importantes. Por exemplo, se agrupar várias ações para o departamento de RH numa conta de armazenamento Azure, deve nomear a conta de armazenamento adequadamente. Da mesma forma, ao nomear as suas ações de ficheiro Saque, deve utilizar nomes semelhantes aos utilizados para os seus homólogos no local.