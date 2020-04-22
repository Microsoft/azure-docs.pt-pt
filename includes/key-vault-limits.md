---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: 8247b1cedc2c5ebc8577af6be485aed0fcd5d6af
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768772"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Principais transações (transações máximas permitidas em 10 segundos, por cofre por região<sup>1</sup>):

|Tipo de chave|Chave HSM<br>Chave CRIAR|Chave HSM<br>Todas as outras transações|Chave de software<br>Chave CRIAR|Chave de software<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA 2.048-bit|5|1,000|10|2.000|
|RSA 3.072-bit|5|250|10|500|
|RSA 4.096-bit|5|125|10|250|
|ECC P-256|5|1,000|10|2.000|
|ECC P-384|5|1,000|10|2.000|
|ECC P-521|5|1,000|10|2.000|
|ECC SECP256K1|5|1,000|10|2.000|

> [!NOTE]
> Na tabela anterior, vemos que para as chaves de software RSA de 2.048 bits, são permitidas 2.000 transações GET por 10 segundos. Para rsa 2.048 bits HSM-keys, 1.000 transações GET por 10 segundos são permitidas.
>
> Os limiares de estrangulamento são ponderados, e a aplicação da lei está na sua soma. Por exemplo, como mostrado na tabela anterior, quando executa operações GET em chaves RSA HSM, é oito vezes mais caro usar teclas de 4.096 bits em comparação com 2.048 bits. Isso é porque 1.000/125 = 8.
>
> Num intervalo de 10 segundos, um cliente azure key vault só pode `429` fazer uma *das* seguintes operações antes de encontrar um código de estado HTTP estrangulado:
> - 2.000 RSA 2.048 bits-chave de software GET transações
> - 1.000 RSA 2.048 bits HSM-key GET transações
> - 125 RSA 4.096 bits HSM-key GET transações
> - 124 RSA 4.096-bits hSM-key GET e 8 rSA 2.048 bits HSM-key GET transações

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segredos, chaves de conta de armazenamento geridas e transações de cofres:

| Tipo de transações | Transações máximas permitidas em 10 segundos, por cofre por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2.000 |

Para obter informações sobre como lidar com a estrangulamento quando estes limites são ultrapassados, consulte a orientação de estrangulamento do [Cofre-Chave Azure](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> Um limite de subscrição para todos os tipos de transações é cinco vezes por limite de cofre chave. Por exemplo, outras transações HSM por subscrição estão limitadas a 5.000 transações em 10 segundos por subscrição.

### <a name="azure-private-link-integration"></a>Integração de Ligações Privadas Azure

| Recurso | Limite |
| -------- | ----- |
| Pontos finais privados por cofre chave | 64 |
| Cofres chave com pontos finais privados por subscrição | 64 |
