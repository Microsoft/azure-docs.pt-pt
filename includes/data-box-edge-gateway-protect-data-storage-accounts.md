---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 8840e6649058837207c75dbdf7baa5c0b11ef387
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467992"
---
Seu dispositivo está associado a uma conta de armazenamento que é usada como um destino para seus dados no Azure. O acesso à conta de armazenamento é controlado pela assinatura e chaves de acesso de armazenamento de 2 512 bits associadas a essa conta de armazenamento.

Uma das chaves é usada para autenticação quando o dispositivo Data Box Edge acessa a conta de armazenamento. A outra chave é mantida em reserva, para que você possa girar as chaves periodicamente.

Por motivos de segurança, muitos data centers exigem a rotação de chaves. Recomendamos que você siga estas práticas recomendadas para a rotação de chaves:

- A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Proteja cuidadosamente sua chave de conta. Não distribua a senha para outros usuários, codifique-a ou salve-a em qualquer lugar em texto sem formatação acessível a outras pessoas.
- Gere novamente sua chave de conta por meio do portal do Azure se você considerar que ela pode estar comprometida. Para obter mais informações, consulte [gerenciar chaves de acesso da conta de armazenamento](../articles/storage/common/storage-account-keys-manage.md).
- O administrador do Azure deve alterar periodicamente ou regenerar a chave primária ou secundária usando a seção de armazenamento do portal do Azure para acessar a conta de armazenamento diretamente.