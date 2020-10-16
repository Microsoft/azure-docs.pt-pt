---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: twooley
ms.service: storage
ms.topic: include
ms.date: 09/30/2020
ms.author: twooley
ms.custom: include file
ms.openlocfilehash: 358de5761f3535b5f1e75d82539ee8e3ecdbd289
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665637"
---
**A azure Data Lake Storage Gen2** não é um tipo de serviço ou conta de armazenamento dedicado. É o mais recente lançamento de capacidades que são dedicadas à análise de big data.  Estas capacidades estão disponíveis numa conta de armazenamento v2 ou BlockBlobStorage para fins gerais, e pode obtê-las, permitindo a funcionalidade **de espaço hierárquico** da conta. Para alvos de escala, consulte estes artigos. 

- [Alvos de escala para armazenamento Blob](https://docs.microsoft.com/azure/storage/blobs/scalability-targets#scale-targets-for-blob-storage).
- [Metas de escala para contas de armazenamento padrão](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json#scale-targets-for-standard-storage-accounts).

**A azure Data Lake Storage Gen1** é um serviço dedicado. É um repositório de hiperesciente em toda a empresa para grandes cargas analíticas de dados. Você pode usar Data Lake Storage Gen1 para capturar dados de qualquer tamanho, tipo e velocidade de ingestão em um único lugar para análise operacional e exploratória. Não há limite para a quantidade de dados que pode armazenar numa conta da Data Lake Storage Gen1.

| **Recurso** | **Limite** | **Comentários** |
| --- | --- | --- |
| Número máximo de contas gen1 de armazenamento de data lake, por subscrição, por região |10 | Para solicitar um aumento para este limite, contacte o suporte. |
| Número máximo de ACLs de acesso, por ficheiro ou pasta |32 | Este é um limite difícil. Utilize grupos para gerir o acesso com menos entradas. |
| Número máximo de ACLs predefinidos, por ficheiro ou pasta |32 | Este é um limite difícil. Utilize grupos para gerir o acesso com menos entradas. |
