---
title: A conversão de ativos REST API
description: Descreve como converter um ativo através da API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 889a70005f1cbabaad525147b4661ea04886138a
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445613"
---
# <a name="use-the-model-conversion-rest-api"></a>Utilizar a API REST do modelo de conversão

O serviço [de conversão de modelos](model-conversion.md) é controlado através de uma [API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). Este artigo descreve os detalhes da API do serviço de conversão.

## <a name="regions"></a>Regiões

Consulte a [lista das regiões disponíveis](../../reference/regions.md) para os URLs de base para enviar os pedidos para.

## <a name="common-headers"></a>Cabeçalhos comuns

### <a name="common-request-headers"></a>Cabeçalhos de pedido comuns

Estes cabeçalhos devem ser especificados para todos os pedidos:

- O cabeçalho **de autorização** deve ter o valor de "Bearer [ *TOKEN]",* onde *[TOKEN* ] é um [símbolo de acesso ao serviço](../tokens.md).

### <a name="common-response-headers"></a>Cabeçalhos de resposta comuns

Todas as respostas contêm estes cabeçalhos:

- O **cabeçalho MS-CV** contém uma corda única que pode ser usada para rastrear a chamada dentro do serviço.

## <a name="endpoints"></a>Pontos Finais

O serviço de conversão fornece três pontos finais rest API para:

- iniciar a conversão de modelos utilizando uma conta de armazenamento ligada à sua conta de renderização remota Azure. 
- iniciar a conversão do modelo utilizando *assinaturas de acesso partilhado (SAS) fornecidas.*
- consulta do estado de conversão

### <a name="start-conversion-using-a-linked-storage-account"></a>Iniciar a conversão usando uma conta de armazenamento ligada
A sua Conta de Renderização Remota Azure precisa de ter acesso à conta de armazenamento fornecida seguindo os passos sobre como ligar as [contas de armazenamento](../create-an-account.md#link-storage-accounts).

| Ponto final | Método |
|-----------|:-----------|
| /v1/accounts/ **accountID** /conversões/criar | POST |

Devolve o ID da conversão em curso, embrulhado num documento JSON. O nome de campo é "conversionId".

#### <a name="request-body"></a>Corpo do pedido

> [!NOTE]
> Tudo em baixo `input.folderPath` será recuperado para realizar a conversão no Azure. Se `input.folderPath` não for especificado, todo o conteúdo do recipiente será recuperado. Todas as bolhas e pastas que são recuperadas devem ter [nomes de ficheiros válidos do Windows](/windows/win32/fileio/naming-a-file#naming-conventions).

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Iniciar a conversão usando assinaturas de acesso partilhado fornecidas
Se a sua conta ARR não estiver ligada à sua conta de armazenamento, esta interface REST permite-lhe fornecer acesso através de *Assinaturas de Acesso Partilhado (SAS)*.

| Ponto final | Método |
|-----------|:-----------|
| /v1/accounts/ **accountID** /conversions/createWithSharedAccessSignature | POST |

Devolve o ID da conversão em curso, embrulhado num documento JSON. O nome do campo `conversionId` é.

#### <a name="request-body"></a>Corpo do pedido

O organismo de pedido é o mesmo que na chamada de criação REST acima, mas a entrada e saída contêm *fichas de acesso partilhado (SAS).* Estes tokens fornecem acesso à conta de armazenamento para ler a entrada e escrever o resultado da conversão.

> [!NOTE]
> Estes tokens SAS URI são as cordas de consulta e não o URI completo. 

> [!NOTE]
> Tudo em baixo `input.folderPath` será recuperado para realizar a conversão no Azure. Se `input.folderPath` não for especificado, todo o conteúdo do recipiente será recuperado. Todas as bolhas e pastas que são recuperadas devem ter [nomes de ficheiros válidos do Windows](/windows/win32/fileio/naming-a-file#naming-conventions).

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>Estado de conversão de sondagens
O estado de uma conversão em curso iniciada com uma das chamadas REST acima pode ser consultado utilizando a seguinte interface:


| Ponto final | Método |
|-----------|:-----------|
| /v1/accounts/ **accountID** /conversões/conversãoId **conversionId** | GET |

Devolve um documento JSON com um campo de "status" que pode ter os seguintes valores:

- "Criado"
- "Correr"
- "Sucesso"
- "Falha"

Se o estado for "Falha", haverá um campo adicional de "erro" com um subcampo de "mensagem" contendo informações de erro. Registos adicionais serão enviados para o seu recipiente de saída.

## <a name="list-conversions"></a>Conversões de Listas

Para obter uma lista de todas as conversões para uma conta, utilize a interface:

| Ponto final | Método |
|-----------|:-----------|
| /v1/accounts/ **accountID** /conversões?skiptoken= **skipToken** | GET |

| Parâmetro | Obrigatório |
|-----------|:-----------|
| accountID | Yes |
| skiptoken | No |

Devolve um documento json que contém uma série de conversões e seus detalhes. Esta consulta devolve um máximo de 50 conversões de cada vez. Na situação em que há mais conversões para recuperar, a resposta conterá uma propriedade **nextLink** contendo o skipToken que pode ser consultado para recuperar o próximo conjunto de resultados.

## <a name="next-steps"></a>Passos seguintes

- [Utilizar o Armazenamento de Blobs do Azure para conversão de modelos](blob-storage.md)
- [Conversão de modelo](model-conversion.md)