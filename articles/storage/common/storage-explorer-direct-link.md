---
title: Ligação direta do Azure Storage Explorer | Microsoft Docs
description: Documentação da ligação direta do Azure Storage Explorer
services: storage
author: chuye
ms.service: storage
ms.topic: article
ms.date: 02/24/2021
ms.author: chuye
ms.openlocfilehash: 121df1e1c3ab6d0741d3e4da1144a86938da70ed
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582335"
---
# <a name="azure-storage-explorer-direct-link"></a>Ligação direta do Azure Storage Explorer

No Windows e macOS, o Storage Explorer suporta URIs com o `storageexplorer://` protocolo. Estes URIs são referidos como links diretos. Uma ligação direta aponta para um recurso de armazenamento Azure no Storage Explorer. Seguindo uma ligação direta abrirá o Storage Explorer e navegará para o recurso a que aponta. Este artigo descreve como as ligações diretas funcionam e como pode usá-las.

## <a name="how-direct-links-work"></a>Como funcionam os links diretos

O Storage Explorer usa a vista da árvore para visualizar recursos em Azure. Um link direto contém a informação hierárquica para o nó de recurso ligado na árvore. Quando uma ligação direta é seguida, o Storage Explorer abre e recebe os parâmetros no link direto. O Explorador de Armazenamento utiliza então estes parâmetros para navegar para o recurso ligado na vista da árvore.

> [!IMPORTANT]
> Tem de ser inscrito e ter as permissões necessárias para aceder ao recurso ligado para uma ligação direta ao trabalho.

## <a name="parameters"></a>Parâmetros

Uma ligação direta do Explorador de Armazenamento começa sempre com o `storageexplorer://` protocolo. A tabela seguinte explica cada um dos parâmetros possíveis num link direto.

Parâmetro | Descrição
:---------| :---------
`v`         | Versão do protocolo de ligação direta.
`accountid` | O ID de recursos do Azure Resource Manager da conta de armazenamento para o recurso ligado. Se o recurso ligado for uma conta de armazenamento, este ID será o ID de recursos Azure Resource Manager dessa conta de armazenamento. Caso contrário, este ID será o ID de recurso Azure Resource Manager da conta de armazenamento a que pertence o recurso ligado.
`resourcetype` | Opcional. Só utilizado quando o recurso ligado é um recipiente blob, uma partilha de ficheiros, uma fila ou uma mesa. Deve ser um dos "Azure.BlobContainer", "Azure.FileShare", "Azure.Queue", "Azure.FileShare".
`resourcename` | Opcional. Só utilizado quando o recurso ligado é um recipiente blob, uma partilha de ficheiros, uma fila ou uma mesa. O nome do recurso ligado.

Aqui está um exemplo de ligação direta a um recipiente de bolhas. 
`storageexplorer://v=1&accountid=/subscriptions/the_subscription_id/resourceGroups/the_resource_group_name/providers/Microsoft.Storage/storageAccounts/the_storage_account_name&subscriptionid=the_subscription_id&resourcetype=Azure.BlobContainer&resourcename=the_blob_container_name`

## <a name="get-a-direct-link-from-storage-explorer"></a>Obtenha uma ligação direta do Storage Explorer

Pode utilizar o Storage Explorer para obter uma ligação direta para um recurso. Abra o menu de contexto do nó para o recurso na vista da árvore. Em seguida, utilize a ação "Copy Direct Link" para copiar a sua ligação direta à área de transferência.

## <a name="direct-link-limitations"></a>Limitações de ligação direta

As ligações diretas só são suportadas para recursos sob nós de subscrição. Além disso, apenas são suportados os seguintes tipos de recursos:

- Contas de Armazenamento
- Contentores de blob
- Filas
- Partilhas de Ficheiros
- Tables
