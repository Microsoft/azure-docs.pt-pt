---
title: Forneça uma chave de encriptação a pedido de armazenamento blob
titleSuffix: Azure Storage
description: Os clientes que fazem pedidos contra o armazenamento do Blob Azure têm a opção de fornecer uma chave de encriptação por pedido (pré-visualização). A inclusão da chave de encriptação no pedido fornece controlo granular sobre as definições de encriptação para operações de armazenamento blob.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c8a5555c5c33255fdc5902a115e7e9103a4e936f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79410067"
---
# <a name="provide-an-encryption-key-on-a-request-to-blob-storage-preview"></a>Forneça uma chave de encriptação num pedido de armazenamento blob (pré-visualização)

Os clientes que fazem pedidos contra o armazenamento do Blob Azure têm a opção de fornecer uma chave de encriptação por pedido (pré-visualização). A inclusão da chave de encriptação no pedido fornece controlo granular sobre as definições de encriptação para operações de armazenamento blob. As chaves fornecidas pelo cliente podem ser armazenadas no Cofre de Chaves Azure ou noutra loja-chave.

## <a name="encrypting-read-and-write-operations"></a>Encriptar as operações de leitura e escrita

Quando uma aplicação de cliente fornece uma chave de encriptação no pedido, o Azure Storage executa encriptação e desencriptação de forma transparente ao ler e escrever dados blob. O Azure Storage escreve um hash SHA-256 da chave de encriptação ao lado do conteúdo da bolha. O haxixe é usado para verificar se todas as operações subsequentes contra a bolha usam a mesma chave de encriptação.

O Azure Storage não armazena nem gere a chave de encriptação que o cliente envia com o pedido. A chave é descartada de forma segura assim que o processo de encriptação ou desencriptação estiver completo.

Quando um cliente cria ou atualiza uma bolha utilizando uma chave fornecida pelo cliente no pedido, então posteriormente ler e escrever pedidos para essa bolha também deve fornecer a chave. Se a chave não for fornecida num pedido de uma bolha que já tenha sido encriptada com uma chave fornecida pelo cliente, então o pedido falha com o código de erro 409 (Conflito).

Se a aplicação do cliente enviar uma chave de encriptação no pedido, e a conta de armazenamento também for encriptada usando uma chave gerida pela Microsoft ou uma chave gerida pelo cliente, então o Azure Storage utiliza a chave fornecida no pedido de encriptação e desencriptação.

Para enviar a chave de encriptação como parte do pedido, um cliente deve estabelecer uma ligação segura ao Armazenamento Azure usando HTTPS.

Cada imagem blob pode ter a sua própria chave de encriptação.

## <a name="request-headers-for-specifying-customer-provided-keys"></a>Pedir cabeçalhos para especificar as chaves fornecidas pelo cliente

Para chamadas REST, os clientes podem usar os seguintes cabeçalhos para passar de forma segura informações chave de encriptação num pedido ao armazenamento blob:

|Cabeçalho do Pedido | Descrição |
|---------------|-------------|
|`x-ms-encryption-key` |Necessário tanto para escrever e ler pedidos. Um valor de chave de encriptação AES-256 codificado pela Base64. |
|`x-ms-encryption-key-sha256`| Necessário tanto para escrever e ler pedidos. O SHA256 codificado pela Base64 da chave de encriptação. |
|`x-ms-encryption-algorithm` | Obrigatório para pedidos de escrita, opcional para pedidos de leitura. Especifica o algoritmo a utilizar ao encriptar dados utilizando a chave dada. Deve ser AES256. |

Especificar as chaves de encriptação no pedido é opcional. No entanto, se especificar um dos cabeçalhos acima indicados para uma operação de escrita, então deve especificar todos.

## <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operações de armazenamento blob suportando chaves fornecidas pelo cliente

O seguinte suporte de operações de armazenamento Blob envia ndo chaves de encriptação fornecidas pelo cliente a pedido:

- [Coloque Blob](/rest/api/storageservices/put-blob)
- [Lista de blocos](/rest/api/storageservices/put-block-list)
- [Bloquear](/rest/api/storageservices/put-block)
- [Colocar bloco a partir de URL](/rest/api/storageservices/put-block-from-url)
- [Página de colocação](/rest/api/storageservices/put-page)
- [Colocar página a partir de URL](/rest/api/storageservices/put-page-from-url)
- [Bloco de apêndice](/rest/api/storageservices/append-block)
- [Definir propriedades blob](/rest/api/storageservices/set-blob-properties)
- [Definir Metadados Blob](/rest/api/storageservices/set-blob-metadata)
- [Obter Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties) (Obter Propriedades do Blob)
- [Obter Metadados Blob](/rest/api/storageservices/get-blob-metadata)
- [Blob de Instantâneo](/rest/api/storageservices/snapshot-blob)

## <a name="rotate-customer-provided-keys"></a>Rode as chaves fornecidas pelo cliente

Para rodar uma chave de encriptação que foi usada para encriptar uma bolha, descarregue a bolha e, em seguida, recarregue-a com a nova chave de encriptação.

> [!IMPORTANT]
> O portal Azure não pode ser utilizado para ler ou escrever para um recipiente ou bolha que seja encriptado com uma chave fornecida no pedido.
>
> Certifique-se de proteger a chave de encriptação que fornece num pedido de armazenamento Blob numa loja de chaves segura como o Azure Key Vault. Se tentar uma operação de escrita num recipiente ou bolha sem a chave de encriptação, a operação falhará e perderá o acesso ao objeto.

## <a name="next-steps"></a>Passos seguintes

- [Especifique uma chave fornecida pelo cliente num pedido de armazenamento blob com .NET](../blobs/storage-blob-customer-provided-key.md)
- [Encriptação azure storage para dados em repouso](storage-service-encryption.md)
