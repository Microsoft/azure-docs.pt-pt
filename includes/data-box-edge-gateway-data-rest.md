---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8a6ceee0f7db4b7b368dce1a28c8edc0b2b11180
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754326"
---
Para dados Inativos:

- Encriptação do BitLocker XTS-AES de 256 bits é utilizada para proteger os dados locais.
- O acesso aos dados armazenados em partilhas é restrito.

    - Clientes SMB que acedem a partilha de dados tem das credenciais de utilizador associadas à partilha. Estas credenciais são definidas quando criar a partilha.
    - Os endereços IP dos clientes NFS que acedam um compartilhamento tem de ser adicionado ao criar a partilha.