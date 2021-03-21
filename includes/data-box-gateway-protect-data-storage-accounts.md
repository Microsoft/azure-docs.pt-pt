---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 160f85b34b3730b14ed96854d5f60ad81f4eb63b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96583065"
---
O dispositivo encontra-se associado a uma conta de armazenamento que é utilizada como destino para os dados no Azure. O acesso à conta de armazenamento é controlado pela subscrição e estão associadas a essa conta de armazenamento duas chaves de acesso de armazenamento de 512 bits.

Uma das chaves é utilizada para autenticação quando o dispositivo do Azure Stack Edge acede à conta de armazenamento. A outra chave é mantida como reserva, para que possa rodar as chaves periodicamente.

Por motivos de segurança, muitos datacenters exigem a rotação de chaves. Recomendamos que siga estas boas práticas para a rotação das chaves:

- A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Proteja cuidadosamente a chave da conta. Não distribua a palavra-passe a outros utilizadores, codifica-a ou guarde-a em qualquer lugar em texto simples que esteja acessível a outros.
- Regenerar a chave da sua conta através do portal Azure se achar que pode ser comprometida. Para obter mais informações, consulte [gerir as teclas de acesso à conta de armazenamento.](../articles/storage/common/storage-account-keys-manage.md)
- O seu administrador Azure deve alterar ou regenerar periodicamente a tecla primária ou secundária utilizando a secção de Armazenamento do portal Azure para aceder diretamente à conta de armazenamento.