---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b9d87fd7929607da8407ae5bbfb2f6dd6d69dab
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184664"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transações da chave (máximo de transações permitido dentro de 10 segundos, por cofre por região<sup>1</sup>):

|Tipo de chave|Chave HSM<br>Criar chave|Chave HSM<br>Todas as outras transações|Chave de software<br>Criar chave|Chave de software<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA 2.048 bits|5|1,000|10|2\.000|
|RSA 3,072 bits|5|250|10|500|
|RSA 4.096 bits|5|125|10|250|
|P-256 ECC|5|1,000|10|2\.000|
|ECC P-384|5|1,000|10|2\.000|
|P-521 ECC|5|1,000|10|2\.000|
|ECC SECP256K1|5|1,000|10|2\.000|

> [!NOTE]
> Na tabela anterior, Vemos que as chaves de software RSA 2.048 bits, são permitidas 2000 transações de GET por 10 segundos. Para chaves HSM de RSA 2.048 bits, são permitidas 1000 transações de GET por 10 segundos.
>
> Os limiares de limitação são ponderados e imposição estiver na respetiva soma. Por exemplo, conforme mostrado na tabela anterior, quando efetua operações de GET chaves RSA HSM, é oito vezes mais caro utilizar chaves de 4.096 bits em comparação comparadas chaves 2.048 bits. Isso ocorre porque 1.000/125 = 8.
>
> Num determinado intervalo de 10 segundos, um cliente do Azure Key Vault pode fazer *apenas uma* das operações seguintes antes de ele encontra um `429` limitação de código de estado HTTP:
> - 2000 transações de GET de chave de software RSA 2.048 bits
> - 1000 transações RSA 2.048 bits chave HSM GET
> - Transações de RSA 4.096 bits chave HSM GET 125
> - 124 transações de chave RSA 4.096 bits de HSM GET e 8 RSA 2.048 bits chave HSM GET transações

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segredos, chaves de conta de armazenamento gerido e as transações de cofre:
| Tipo de transações | Transações máximas permitidas dentro de 10 segundos, por cofre por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2\.000 |

Para obter informações sobre como resolver problemas de limitação quando estes limites são ultrapassados, consulte [orientações para a limitação do Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> um limite de toda a subscrição para todos os tipos de transação é cinco vezes por limite de Cofre de chaves. Por exemplo, HSM outras transações por subscrição são limitadas a 5000 transações dentro de 10 segundos por subscrição.
