---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "82562134"
---
O dispositivo encontra-se associado a uma conta de armazenamento que é utilizada como destino para os dados no Azure. O acesso à conta de armazenamento é controlado pela subscrição e estão associadas a essa conta de armazenamento duas chaves de acesso de armazenamento de 512 bits.

Uma das chaves é utilizada para autenticação quando o dispositivo do Azure Stack Edge acede à conta de armazenamento. A outra chave é mantida como reserva, para que possa rodar as chaves periodicamente.

Por motivos de segurança, muitos datacenters exigem a rotação de chaves. Recomendamos que siga estas boas práticas para a rotação das chaves:

- A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Proteja cuidadosamente a chave da conta. Não distribua a palavra-passe a outros utilizadores, não a codifique nem a guarde num local em texto simples que seja acessível a outras pessoas.
- Regenerar a chave da sua conta através do portal Azure se achar que pode ser comprometida. Para obter mais informações, consulte [gerir as teclas de acesso à conta de armazenamento.](../articles/storage/common/storage-account-keys-manage.md)
- O seu administrador Azure deve alterar ou regenerar periodicamente a tecla primária ou secundária utilizando a secção de Armazenamento do portal Azure para aceder diretamente à conta de armazenamento.