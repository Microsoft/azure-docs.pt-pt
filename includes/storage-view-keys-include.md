---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/06/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: e3b3d944508a4261b78def0b3bee13f7395a8bf0
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748991"
---
Para exibir e copiar as chaves de acesso ou a cadeia de conexão da conta de armazenamento do portal do Azure:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Localize a sua conta de armazenamento.
3. Na secção **Definições** da descrição geral da conta de armazenamento, selecione **Chaves de acesso**. As chaves de acesso da conta são apresentadas, bem como a cadeia de ligação completa para cada chave.
4. Encontre o valor da **Chave** em **key1**e clique no botão **Copiar** para copiar a chave da conta.
5. Como alternativa, você pode copiar a cadeia de conexão inteira. Encontre o valor da **Cadeia de ligação** em **key1**e clique no botão **Copiar** para copiar a cadeia de ligação.

    ![Captura de tela mostrando como exibir chaves de acesso no portal do Azure](media/storage-view-keys-include/portal-connection-string.png)

Você pode usar qualquer chave para acessar o armazenamento do Azure, mas, em geral, é uma boa prática usar a primeira chave e reservar o uso da segunda chave para quando você estiver girando as chaves.
