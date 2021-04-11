---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/08/2021
ms.author: alkohli
ms.openlocfilehash: 4740ce4bd59598747cdd9c2147fbbd460b6e648e
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107285207"
---
O dispositivo encontra-se associado a uma conta de armazenamento que é utilizada como destino para os dados no Azure. O acesso à conta de armazenamento é controlado pela subscrição e estão associadas a essa conta de armazenamento duas chaves de acesso de armazenamento de 512 bits.

Uma das chaves é utilizada para autenticação quando o dispositivo do Azure Stack Edge acede à conta de armazenamento. A outra chave é mantida como reserva, para que possa rodar as chaves periodicamente.

Por motivos de segurança, muitos datacenters exigem a rotação de chaves. Recomendamos que siga estas boas práticas para a rotação das chaves:

- A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Proteja cuidadosamente a chave da conta. Não distribua a palavra-passe a outros utilizadores, não a codifique nem a guarde num local em texto simples que seja acessível a outras pessoas.
- [Regenerar a chave da sua conta](../articles/storage/common/storage-account-keys-manage.md#manually-rotate-access-keys) através do portal Azure se achar que pode ser comprometida.
- O seu administrador Azure deve alterar ou regenerar periodicamente a tecla primária ou secundária utilizando a secção de Armazenamento do portal Azure para aceder diretamente à conta de armazenamento.
- Também pode utilizar a sua própria chave de encriptação para proteger os dados na sua conta de armazenamento Azure. Quando especifica uma chave gerida pelo cliente, essa chave é utilizada para proteger e controlar o acesso à chave que encripta os seus dados. Para obter mais informações sobre como proteger os seus dados, consulte [Ativar as chaves geridas pelo cliente para a sua conta de Armazenamento Azure](../articles/storage/common/customer-managed-keys-overview.md#enable-customer-managed-keys-for-a-storage-account).
