---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 653c175a559f5c0b7dc551b396e91276332df20a
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184903"
---
O dispositivo está associado uma conta de armazenamento que é utilizada como um destino para os seus dados no Azure. Acesso à conta de armazenamento é controlado por duas de 512 bits de armazenamento e a subscrição associadas essa conta de armazenamento de chaves de acesso.

Uma das chaves é utilizada para autenticação quando o dispositivo de limite de caixa de dados acede a conta de armazenamento. A outra chave é mantida em reserva, portanto, pode girar periodicamente as chaves.

Por motivos de segurança, muitos data Centers requerem a rotação de chaves. Recomendamos que siga estas práticas recomendadas para a rotação de chaves:

- A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Proteger cuidadosamente a sua chave de conta. Não distribua a palavra-passe para outros utilizadores, duro codificá-la ou guardá-lo em qualquer lugar no texto simples que é acessível a outras pessoas.
- [Regenerar a chave da conta](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) através do Azure portal se acha que ele podia ficar comprometida.
- O administrador do Azure periodicamente deve alterar ou voltar a gerar a chave primária ou secundária, utilizando a secção de armazenamento do portal do Azure para aceder à conta de armazenamento diretamente.