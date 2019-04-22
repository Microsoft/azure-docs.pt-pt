---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: 23587e617c62cfe4aa24256330c5f7673dd1c674
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684452"
---
O dispositivo está associado uma conta de armazenamento que é utilizada como um destino para os seus dados no Azure. Acesso à conta de armazenamento é controlado por duas de 512 bits de armazenamento e a subscrição associadas essa conta de armazenamento de chaves de acesso.

Uma das chaves é utilizada para autenticação quando o dispositivo de limite de caixa de dados acede a conta de armazenamento. A outra chave é mantida em reserva, permitindo-lhe rodar as chaves periodicamente.

Por motivos de segurança, muitos data Centers requerem a rotação de chaves. Recomendamos que siga estas práticas recomendadas para a rotação de chaves:

- A chave da conta de armazenamento é semelhante à palavra-passe de raiz da conta de armazenamento. Proteger cuidadosamente a sua chave de conta. Não distribua a palavra-passe para outros utilizadores, duro codificá-la ou guardá-lo em qualquer lugar em texto não encriptado, que é acessível a outras pessoas.
- [Regenerar a chave da conta](../articles/storage/common/storage-account-manage.md#regenerate-access-keys) através do portal do Azure se considerar poderão ter sido comprometida.
- Periodicamente, o administrador do Azure deve alterar ou voltar a gerar a chave primária ou secundária, utilizando a secção de armazenamento do portal do Azure para aceder diretamente à conta de armazenamento.