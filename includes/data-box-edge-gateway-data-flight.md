---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "67120474"
---
Para dados em trânsito:

- Padrão de TLS 1.2 é usado para dados que circulam entre o dispositivo e o Azure. Não há nenhum contingência para TLS 1.1 e versões anteriores. Comunicação do agente será bloqueada se o TLS 1.2 não é suportada. TLS 1.2 também é necessário para o portal e gestão de SDK.
- Quando os clientes acessam o seu dispositivo através da web local da interface do Usuário de um navegador, padrão TLS 1.2 é usado como o protocolo de segurança padrão.

    - A melhor prática é configurar o browser para utilizar TLS 1.2.
    - Se o browser não suporta o TLS 1.2, pode usar a TLS 1.1 ou TLS 1.0.
- Recomendamos que utilize o SMB 3.0 com a encriptação para proteger os dados quando copia a partir dos seus servidores de dados.
