---
author: rothja
ms.service: key-vault
ms.topic: include
ms.date: 04/21/2020
ms.author: jroth
ms.openlocfilehash: e4abbeadb0d30911d99fff57c0e99a3e427a6d8d
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95555998"
---
### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Principais transações (transações máximas permitidas em 10 segundos, por abóbada por região<sup>1):</sup>

|Tipo de chave|Chave HSM<br>Criar chave|Chave HSM<br>Todas as outras transações|Chave de software<br>Criar chave|Chave de software<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA 2,048-bit|5|1,000|10|2.000|
|RSA 3,072-bit|5|250|10|500|
|RSA 4,096-bit|5|125|10|250|
|ECC P-256|5|1,000|10|2.000|
|ECC P-384|5|1,000|10|2.000|
|ECC P-521|5|1,000|10|2.000|
|ECC SECP256K1|5|1,000|10|2.000|

> [!NOTE]
> Na tabela anterior, vemos que para chaves de software RSA de 2.048 bits, são permitidas 2.000 transações GET por 10 segundos. Para as teclas HSM de 2.048 bits, são permitidas 1.000 transações GET por 10 segundos.
>
> Os limiares de estrangulamento são ponderados e a aplicação da lei está na sua soma. Por exemplo, como mostrado na tabela anterior, quando executa operações GET em teclas RSA HSM, é oito vezes mais caro usar teclas de 4.096 bits em comparação com teclas de 2.048 bits. Isso é porque 1.000/125 = 8.
>
> Num intervalo de 10 segundos, um cliente Azure Key Vault pode fazer *apenas uma das* seguintes operações antes de encontrar um código de `429` estado HTTP estrangulador:
> - 2.000 RSA 2.048 bits de chave de software GET transações
> - 1.000 RSA 2.048-bit-key GET transações
> - 125 RSA 4.096-bit-chave HSM-key GET transações
> - 124 RSA 4.096-bit-chave HSM-key GET transações e 8 RSA 2.048-bit-key GET

### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segredos, chaves de conta de armazenamento geridas e transações de cofre:

| Tipo de transações | Transações máximas permitidas em 10 segundos, por abóbada por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2.000 |

Para obter informações sobre como lidar com o estrangulamento quando estes limites forem ultrapassados, consulte a orientação de [estrangulamento do Azure Key Vault](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> Um limite de subscrição para todos os tipos de transações é cinco vezes por limite de cofre de tecla. Por exemplo, as transações HSM-other por subscrição estão limitadas a 5.000 transações em 10 segundos por subscrição.

### <a name="azure-private-link-integration"></a>Integração de Azure Private Link

> [!NOTE]
> O número de cofres-chave com pontos finais privados ativados por subscrição é um limite ajustável. O limite abaixo indicado é o limite de predefinição. Se pretender solicitar um aumento limite para o seu serviço, por favor envie um e-mail para akv-privatelink@microsoft.com . Aprovaremos estes pedidos caso a caso.

| Recurso | Limite |
| -------- | ----- |
| Pontos finais privados por cofre chave | 64 |
| Cofres-chave com pontos finais privados por subscrição | 400 |