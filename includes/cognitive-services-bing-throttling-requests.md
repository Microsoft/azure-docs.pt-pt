---
author: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/09/2018
ms.author: nitinme
ms.openlocfilehash: 1d8340054ace25a0cdc36eef9c3d5b4238a6f99b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "67184543"
---
O serviço e o seu tipo de subscrição determinam o número de consultas por segundo (QPS) que pode fazer. Certifique-se de que a sua aplicação inclui a lógica necessária para se manter na sua quota. Se o limite de QPS for atingido ou excedido, o pedido falha e é devolvido um código de estado HTTP 429. A resposta inclui o cabeçalho `Retry-After`, que indica o tempo que tem de esperar antes de poder enviar outro pedido.

## <a name="denial-of-service-versus-throttling"></a>Denial of service versus Limitação

O serviço diferencia entre um ataque denial of service (DoS) e uma violação de QPS. Se o serviço suspeitar de um ataque DoS, o pedido é realizado com êxito (o código de estado HTTP é 200 OK). No entanto, o corpo da resposta está vazio.