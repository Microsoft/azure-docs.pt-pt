---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: a8979edf94c0dd0271293feb28c18530faeba09c
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56660346"
---
## <a name="key-transactions-max-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transações da chave (transações máx. permitidas dentro de 10 segundos, por cofre por região<sup>1</sup>):

|Tipo de chave|HSM-key<br>Criar chave|HSM-key<br>Todas as outras transações|Software-key<br>Criar chave|Software-key<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA 2048-bit|5|1000|10|2000|
|RSA 3072-bit|5|250|10|500|
|RSA 4096 bits|5|125|10|250|
|P-256 ECC|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|P-521 ECC|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|

> [!NOTE]
> Na tabela acima, podemos ver que para RSA 2048 bits as chaves de software, podemos permitir transações de GET de 2000 por 10 segundos e que as-chaves RSA de 2048 bits HSM, podemos permitir GET de 1000 transações por 10 segundos.
>
> Tenha em atenção que os limiares de limitação são ponderados e imposição estiver na respetiva soma. Por exemplo, na tabela acima, podemos ver que, quando executar operações de GET em chaves RSA HSM, é 8 vezes mais caro utilizar chaves de 4096 bits em comparação com as chaves de 2048 bits (desde 1000/125 = 8). Assim, num determinado intervalo de 10 segundos, um cliente AKV poderia fazer exatamente um dos seguintes antes de receber um `429` limitação de código de estado HTTP:
> - 2000 transações de GET de software-chave RSA de 2048 bits, **ou**
> - 1000 transações de chave RSA de 2048 bits de HSM GET, **ou**
> - Transações de chave RSA de 4096 bits de HSM GET 125, **ou**
> - 124 transações de chave RSA de 4096 bits de HSM GET e 8 transações do GET de chave HSM de RSA 2048 bits.

## <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segredos, chaves de conta de armazenamento gerida e transações de cofre:
| Tipo de transações | Transações de máx. permitidas dentro de 10 segundos, por cofre por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2000 |

Ver [orientações para a limitação do Azure Key Vault](../articles/key-vault/key-vault-ovw-throttling.md) para obter informações sobre como resolver problemas de limitação quando estes limites são ultrapassados.

<sup>1</sup> existe um limite de toda a subscrição para todos os tipos de transação, o que é de 5 vezes por limite de Cofre de chaves. Por exemplo, HSM - outras transações por subscrição estão limitadas a 5000 transações dentro de 10 segundos por subscrição.
