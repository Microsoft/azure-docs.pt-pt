---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147599"
---
A **identidade gerida por defeito** é a identidade gerida atribuída pelo sistema ou a primeira identidade gerida atribuída pelo utilizador.

Durante uma corrida existem duas aplicações de identidade:

1. O sistema utiliza uma identidade para configurar os suportes de armazenamento do utilizador, o registo de contentores e as lojas de dados.

    * Neste caso, o sistema utilizará a identidade gerida por defeito.

1. O utilizador aplica uma identidade para aceder a recursos a partir do código para uma execução submetida

    * Neste caso, forneça o *client_id* correspondente à identidade gerida que pretende utilizar para recuperar uma credencial.
    * Em alternativa, obtenha a identificação do cliente atribuído pelo utilizador através da variável *ambiente DEFAULT_IDENTITY_CLIENT_ID.*

    Por exemplo, para recuperar um símbolo para uma loja de dados com a identidade gerida por defeito:

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```