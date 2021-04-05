---
title: Fornecer uma chave de encriptação sobre um pedido para o armazenamento Blob
titleSuffix: Azure Storage
description: Os clientes que fazem pedidos contra o armazenamento Azure Blob têm a opção de fornecer uma chave de encriptação por pedido. A inclusão da chave de encriptação no pedido fornece controlo granular sobre as definições de encriptação para operações de armazenamento Blob.
services: storage
author: tamram
ms.service: storage
ms.date: 12/14/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: fcc5c02c4a37e205622470260d3c620ad76d07d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97694706"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage"></a>Fornecer uma chave de encriptação sobre um pedido para o armazenamento Blob

Os clientes que fazem pedidos contra o armazenamento Azure Blob têm a opção de fornecer uma chave de encriptação AES-256 por pedido. A inclusão da chave de encriptação no pedido fornece controlo granular sobre as definições de encriptação para operações de armazenamento Blob. As chaves fornecidas pelo cliente podem ser armazenadas no Cofre da Chave Azure ou noutra loja de chaves.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="encrypting-read-and-write-operations"></a>Encriptação de operações de leitura e escrita

Quando uma aplicação do cliente fornece uma chave de encriptação no pedido, o Azure Storage executa a encriptação e desencriptação de forma transparente enquanto lê e escreve dados blob. O Azure Storage escreve um haxixe SHA-256 da chave de encriptação ao lado do conteúdo da bolha. O haxixe é utilizado para verificar se todas as operações subsequentes contra a bolha utilizam a mesma chave de encriptação.

O Azure Storage não armazena nem gere a chave de encriptação que o cliente envia com o pedido. A chave é eliminada de forma segura assim que o processo de encriptação ou desencriptação estiver concluído.

Quando um cliente cria ou atualiza uma bolha utilizando uma chave fornecida pelo cliente no pedido, então posteriormente ler e escrever pedidos para essa bolha também deve fornecer a chave. Se a chave não for fornecida num pedido de bolha que já tenha sido encriptada com uma chave fornecida pelo cliente, então o pedido falha com o código de erro 409 (Conflito).

Se a aplicação do cliente enviar uma chave de encriptação no pedido, e a conta de armazenamento também for encriptada usando uma chave gerida pela Microsoft ou uma chave gerida pelo cliente, então o Azure Storage utiliza a chave fornecida no pedido de encriptação e desencriptação.

Para enviar a chave de encriptação como parte do pedido, um cliente deve estabelecer uma ligação segura ao Azure Storage usando HTTPS.

Cada instantâneo blob pode ter a sua própria chave de encriptação.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Pedido de cabeçalhos para especificar chaves fornecidas pelo cliente

Para chamadas REST, os clientes podem usar os seguintes cabeçalhos para passar de forma segura informações das chaves de encriptação num pedido de armazenamento blob:

|Cabeçalho do Pedido | Description |
|---------------|-------------|
|`x-ms-encryption-key` |Requerido tanto para escrever como ler pedidos. Um valor chave de encriptação AES-256 codificado pela Base64. |
|`x-ms-encryption-key-sha256`| Requerido tanto para escrever como ler pedidos. O SHA256 codificado pela Base64 da chave de encriptação. |
|`x-ms-encryption-algorithm` | Requerido para pedidos de escrita, opcional para pedidos de leitura. Especifica o algoritmo a utilizar ao encriptar dados utilizando a chave dada.  O valor deste cabeçalho deve ser `AES256` . |

Especificar chaves de encriptação no pedido é opcional. No entanto, se especificar um dos cabeçalhos acima indicados para uma operação de escrita, deve especificar todos eles.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operações de armazenamento de bolhas que suportam chaves fornecidas pelo cliente

As seguintes operações de armazenamento Blob suportam o envio de chaves de encriptação fornecidas pelo cliente a pedido:

- [Colocar o Blob](/rest/api/storageservices/put-blob)
- [Colocar lista de blocos](/rest/api/storageservices/put-block-list)
- [Colocar Bloco](/rest/api/storageservices/put-block)
- [Coloque o Bloco de URL](/rest/api/storageservices/put-block-from-url)
- [Colocar página](/rest/api/storageservices/put-page)
- [Colocar página de URL](/rest/api/storageservices/put-page-from-url)
- [Bloco de Apêndice](/rest/api/storageservices/append-block)
- [Definir propriedades blob](/rest/api/storageservices/set-blob-properties)
- [Definir metadados blob](/rest/api/storageservices/set-blob-metadata)
- [Obter Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties) (Obter Propriedades do Blob)
- [Obtenha metadados blob](/rest/api/storageservices/get-blob-metadata)
- [Blob de Instantâneo](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Rode as chaves fornecidas pelo cliente

Para rodar uma chave de encriptação que foi usada para encriptar uma bolha, descarregue a bolha e, em seguida, re carrequique-a com a nova chave de encriptação.

> [!IMPORTANT]
> O portal Azure não pode ser utilizado para ler ou escrever para um recipiente ou bolha que esteja encriptado com uma chave fornecida no pedido.
>
> Certifique-se de proteger a chave de encriptação que fornece num pedido de armazenamento Blob numa loja de chaves segura como o Azure Key Vault. Se tentar uma operação de escrita num recipiente ou numa bolha sem a chave de encriptação, a operação falhará e perderá o acesso ao objeto.

## <a name="next-steps"></a>Passos seguintes

- [Especifique uma chave fornecida pelo cliente num pedido de armazenamento blob com .NET](storage-blob-customer-provided-key.md)
- [Azure Storage encryption for data at rest](../common/storage-service-encryption.md) (Encriptação do Armazenamento do Azure para dados inativos)
