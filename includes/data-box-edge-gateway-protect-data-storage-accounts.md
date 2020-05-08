---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 9ac9865afe37916f1777d92eab8637884eba0c08
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82562134"
---
O seu dispositivo está associado a uma conta de armazenamento que é usada como destino para os seus dados em Azure. O acesso à conta de armazenamento é controlado pela subscrição e duas chaves de acesso de armazenamento de 512 bits associadas a essa conta de armazenamento.

Uma das chaves é utilizada para autenticação quando o dispositivo Azure Stack Edge acede à conta de armazenamento. A outra tecla é mantida na reserva, para que possa rodar as teclas periodicamente.

Por razões de segurança, muitos centros de dados requerem rotação chave. Recomendamos que siga estas boas práticas para a rotação da chave:

- A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Proteja cuidadosamente a chave da sua conta. Não distribua a palavra-passe a outros utilizadores, código-a duramente, ou guarde-a em qualquer lugar em texto simples que seja acessível a outros.
- Regenerar a chave da sua conta através do portal Azure se achar que pode estar comprometida. Para mais informações, consulte Gerir as chaves de [acesso à conta](../articles/storage/common/storage-account-keys-manage.md)de armazenamento .
- O seu administrador Azure deve alterar ou regenerar periodicamente a chave primária ou secundária utilizando a secção de armazenamento do portal Azure para aceder diretamente à conta de armazenamento.