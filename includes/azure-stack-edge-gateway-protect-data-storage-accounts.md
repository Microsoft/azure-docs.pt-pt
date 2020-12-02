---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96467806"
---
O dispositivo encontra-se associado a uma conta de armazenamento que é utilizada como destino para os dados no Azure. O acesso à conta de armazenamento é controlado pela subscrição e estão associadas a essa conta de armazenamento duas chaves de acesso de armazenamento de 512 bits.

Uma das teclas é utilizada para a autenticação quando o dispositivo Data Box Edge acede à conta de armazenamento. A outra chave é mantida como reserva, para que possa rodar as chaves periodicamente.

Por motivos de segurança, muitos datacenters exigem a rotação de chaves. Recomendamos que siga estas boas práticas para a rotação das chaves:

- A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Proteja cuidadosamente a chave da conta. Não distribua a palavra-passe a outros utilizadores, não a codifique nem a guarde num local em texto simples que seja acessível a outras pessoas.
- [Regenerar a chave da sua conta](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) através do portal Azure se achar que pode ser comprometida.
- O seu administrador Azure deve alterar ou regenerar periodicamente a tecla primária ou secundária utilizando a secção de Armazenamento do portal Azure para aceder diretamente à conta de armazenamento.