---
author: amitbapat
ms.service: key-vault
ms.topic: include
ms.date: 03/09/2021
ms.author: ambapat
ms.openlocfilehash: d934d40cad5f4eec929cfd273b6e30ea291e48d5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010966"
---
O serviço Azure Key Vault suporta dois tipos de recursos: Cofres e HSMs geridos. As duas secções seguintes descrevem os limites de serviço para cada uma delas, respectivamente.

### <a name="resource-type-vault"></a>Tipo de recurso: cofre

Esta secção descreve limites de serviço para o tipo de `vaults` recurso.

#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Principais transações (transações máximas permitidas em 10 segundos, por abóbada por região<sup>1):</sup>

|Tipo de chave|Chave HSM<br>Criar chave|Chave HSM<br>Todas as outras transações|Chave de software<br>Criar chave|Chave de software<br>Todas as outras transações|
|:---|---:|---:|---:|---:|
|RSA 2,048-bit|5|1,000|10|2.000|
|RSA 3,072-bit|5|250|10|500|
|RSA 4,096-bit|5|125|10|250|
|ECC P-256|5|1,000|10|2.000|
|ECC P-384|5|1,000|10|2.000|
|ECC P-521|5|1,000|10|2.000|
|ECC SECP256K1|5|1,000|10|2.000|
||||||

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

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Segredos, chaves de conta de armazenamento geridas e transações de cofre:

| Tipo de transações | Transações máximas permitidas em 10 segundos, por abóbada por região<sup>1</sup> |
| --- | --- |
| Todas as transações |2.000 |

Para obter informações sobre como lidar com o estrangulamento quando estes limites forem ultrapassados, consulte a orientação de [estrangulamento do Azure Key Vault](../articles/key-vault/general/overview-throttling.md).

<sup>1</sup> Um limite de subscrição para todos os tipos de transações é cinco vezes por limite de cofre de tecla. Por exemplo, as transações HSM-other por subscrição estão limitadas a 5.000 transações em 10 segundos por subscrição.

#### <a name="azure-private-link-integration"></a>Integração de Azure Private Link

> [!NOTE]
> O número de cofres-chave com pontos finais privados ativados por subscrição é um limite ajustável. O limite abaixo indicado é o limite de predefinição. Se pretender solicitar um aumento limite para o seu serviço, por favor envie um e-mail para akv-privatelink@microsoft.com . Aprovaremos estes pedidos caso a caso.

| Recurso | Limite |
| -------- | -----:|
| Pontos finais privados por cofre chave | 64 |
| Cofres-chave com pontos finais privados por subscrição | 400 |

### <a name="resource-type-managed-hsm-preview"></a>Tipo de recurso: HSM gerido (pré-visualização)

Esta secção descreve limites de serviço para o tipo de `managed HSM` recurso.

#### <a name="object-limits"></a>Limites de objetos

|Item|Limites|
|----|------:|
Número de casos de HSM por subscrição por região|1 (durante a pré-visualização)
Número de chaves por piscina HSM|5000
Número de versões por chave|100
Número de definições de funções personalizadas por HSM|50
Número de atribuições de funções no âmbito HSM|50
Número de atribuição de funções em cada âmbito chave individual|10
|||

#### <a name="transaction-limits-for-administrative-operations-number-of-operations-per-second-per-hsm-instance"></a>Limites de transação para operações administrativas (número de operações por segundo por instância HSM)
|Operação |Número de operações por segundo|
|----|------:|
Todas as operações do RBAC<br/>(inclui todas as operações da CRUD para definições de funções e atribuições de funções)|5
Cópia de segurança/restauro completo do HSM<br/>(apenas uma operação de backup ou restauro simultânea por instância HSM suportada)|1

#### <a name="transaction-limits-for-cryptographic-operations-number-of-operations-per-second-per-hsm-instance"></a>Limites de transação para operações criptográficas (número de operações por segundo por instância HSM)

- Cada instância gerida do HSM constitui 3 partições HSM equilibradas de carga. Os limites de produção são uma função da capacidade de hardware subjacente atribuída para cada partição. As tabelas abaixo mostram a produção máxima com pelo menos uma divisória disponível. A produção real pode ser até 3x mais alta se todas as 3 divisórias estiverem disponíveis.
- Os limites de produção notados pressupõem que uma única chave está a ser utilizada para atingir o máximo rendimento. Por exemplo, se for utilizada uma única tecla RSA-2048, a potência máxima será de 1100 operações de sinalização. Se utilizar 1100 teclas diferentes com 1 transação por segundo cada, não conseguirão obter o mesmo rendimento.

##### <a name="rsa-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operações-chave RSA (número de operações por segundo por instância HSM)

|Operação|2048-bit|3072-bit|4096-bit|
|--|--:|--:|--:|
Criar Chave|1| 1| 1
Eliminar tecla (soft-delete)|10|10|10 
Chave de purga|10|10|10 
Chave de reserva|10|10|10 
Chave de restauro|10|10|10 
Obtenha informações chave|1100|1100|1100
Encriptar|10000|10000|6000
Desencriptar|1100|360|160
Embrulhar|10000|10000|6000
Desembrulhar|1100|360|160
Assinar|1100|360|160
Verificação|10000|10000|6000
|||||

##### <a name="ec-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operações-chave da CE (número de operações por segundo por instância HSM)

Esta tabela descreve o número de operações por segundo para cada tipo de curva.

|Operação|P-256|P-256K|P-384|P-521|
|---|---:|---:|---:|---:|
Criar Chave| 1| 1| 1| 1
Eliminar tecla (soft-delete)|10|10|10|10
Chave de purga|10|10|10|10
Chave de reserva|10|10|10|10
Chave de restauro|10|10|10|10
Obtenha informações chave|1100|1100|1100|1100
Assinar|260|260|165|56
Verificação|130|130|82|28
||||||


##### <a name="aes-key-operations-number-of-operations-per-second-per-hsm-instance"></a>Operações-chave AES (número de operações por segundo por instância HSM)
- As operações de encriptação e desencriptação assumem um tamanho de pacote 4KB.
- Os limites de produção para encriptar/desencriptar aplicam-se aos algoritmos AES-CBC e AES-GCM.
- Os limites de produção para Wrap/Unwrap aplicam-se ao algoritmo AES-KW.

|Operação|128 bits|192-bit|256 bits|
|--|--:|--:|--:|
Criar Chave|1| 1| 1
Eliminar tecla (soft-delete)|10|10|10
Chave de purga|10|10|10
Chave de reserva|10|10|10
Chave de restauro|10|10|10
Obtenha informações chave|1100|1100|1100
Encriptar|8000|8000 |8000 
Desencriptar|8000|8000|8000
Embrulhar|9000|9000|9000
Desembrulhar|9000|9000|9000

