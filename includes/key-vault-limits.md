---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 0b9d87fd7929607da8407ae5bbfb2f6dd6d69dab
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "67184664"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transações de chave (máximo de transações permitidas em 10 segundos, por cofre por região<sup>1</sup>):

|Tipo de chave|Chave HSM<br>CRIAR chave|Chave HSM<br>Todas as outras transações|Chave de software<br>CRIAR chave|Chave de software<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA de 2.048 bits|5|1,000|10|2\.000|
|RSA de 3.072 bits|5|250|10|500|
|RSA de 4.096 bits|5|125|10|250|
|P-256 ECC|5|1,000|10|2\.000|
|P-384 ECC|5|1,000|10|2\.000|
|P-521 ECC|5|1,000|10|2\.000|
|ECC SECP256K1|5|1,000|10|2\.000|

> [!NOTE]
> Na tabela anterior, vemos que, para chaves de software RSA de 2.048 bits, são permitidas 2.000 transações GET por 10 segundos. Para chaves RSA de 2.048 bits HSM-Keys, são permitidas 1.000 transações GET por 10 segundos.
>
> Os limites de limitação são ponderados e a imposição está em sua soma. Por exemplo, conforme mostrado na tabela anterior, quando você executa operações GET em chaves RSA HSM, é oito vezes mais caro usar chaves de 4.096 bits comparadas com chaves de 2.048 bits. Isso ocorre porque 1000/125 = 8.
>
> Em um determinado intervalo de 10 segundos, um cliente Azure Key Vault pode fazer *apenas uma* das seguintes operações antes de encontrar um código `429` de status HTTP de limitação:
> - 2\.000 RSA 2.048-bit software-chave obter transações
> - 1\.000 RSA 2.048 bits HSM-chave obter transações
> - 125 RSA 4.096 bits HSM-chave obter transações
> - 124 RSA 4.096 bits HSM-Key GET Transactions e 8 RSA 2.048-bit HSM-Key GET Transactions

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segredos, chaves de conta de armazenamento gerenciado e transações de cofre:
| Tipo de transações | Máximo de transações permitidas em 10 segundos, por cofre por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2\.000 |

Para obter informações sobre como lidar com a limitação quando esses limites são excedidos, consulte [diretrizes de limitação de Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> um limite de toda a assinatura para todos os tipos de transação é cinco vezes por limite de cofre de chaves. Por exemplo, HSM-outras transações por assinatura são limitadas a 5.000 transações em 10 segundos por assinatura.
