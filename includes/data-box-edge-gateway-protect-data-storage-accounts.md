---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82562134"
---
O seu dispositivo está associado a uma conta de armazenamento que é usada como destino para os seus dados em Azure. O acesso à conta de armazenamento é controlado pela subscrição e duas chaves de acesso ao armazenamento de 512 bits associadas a essa conta de armazenamento.

Uma das teclas é utilizada para a autenticação quando o dispositivo Azure Stack Edge acede à conta de armazenamento. A outra chave é guardada na reserva, para que possa rodar as teclas periodicamente.

Por razões de segurança, muitos centros de dados requerem rotação de chaves. Recomendamos que siga estas boas práticas para a rotação das chaves:

- A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Proteja cuidadosamente a chave da sua conta. Não distribua a palavra-passe a outros utilizadores, código-a rígida, ou guarde-a em qualquer lugar em texto simples que esteja acessível a outros.
- Regenerar a chave da sua conta através do portal Azure se achar que pode ser comprometida. Para obter mais informações, consulte [gerir as teclas de acesso à conta de armazenamento.](../articles/storage/common/storage-account-keys-manage.md)
- O seu administrador Azure deve alterar ou regenerar periodicamente a tecla primária ou secundária utilizando a secção de Armazenamento do portal Azure para aceder diretamente à conta de armazenamento.