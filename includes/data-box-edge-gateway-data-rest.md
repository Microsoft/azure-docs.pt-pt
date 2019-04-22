---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: d8570c0be6b64d4e289575ce3f3f1721c4a65074
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684467"
---
Para os dados Inativos:

- Para os dados em repouso, a encriptação BitLocker XTS AES-256 é utilizada para proteger os dados locais.
- Para os dados que residem nas partilhas, o acesso às partilhas está limitado.

    - Para clientes SMB que acedem os dados de partilha, necessitem de credenciais de utilizador associadas à partilha. Estas credenciais são definidas no momento da criação da partilha.
    - Para clientes de NFS aceder às partilhas, os endereços IP dos clientes tem de ser adicionados no momento da criação da partilha.