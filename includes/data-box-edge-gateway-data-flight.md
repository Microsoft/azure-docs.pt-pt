---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: bea14544949f90290bf3878295b49f1e08be9eea
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684438"
---
Para o data-em-voo:

- Para dados que se encontra entre o dispositivo e o Azure, o padrão TLS 1.2 é usado. Não há nenhum contingência para TLS 1.1 e versões anteriores. A comunicação do agente será bloqueada se o TLS 1.2 não é suportada. O TLS 1.2 também é necessário para o portal e operações de gestão do SDK.
- Quando os clientes acessam o seu dispositivo através da web local da interface do Usuário num navegador, o padrão TLS 1.2 é usado como o protocolo de segurança padrão.

    - A melhor prática é configurar o browser para utilizar TLS 1.2.
    - Se o navegador não oferece suporte a TLS 1.2, pode utilizar o TLS 1.1 ou TLS 1.0.
- Para proteger os dados quando copia a partir dos seus servidores de dados, recomendamos que utilize o SMB 3.0 com a encriptação.
