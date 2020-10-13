---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 866640d90c66dd82e8be61d221bc903907575454
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91642295"
---
Durante a pré-visualização, a NFS tem as seguintes limitações:

- Atualmente, o NFS 4.1 apenas suporta as características obrigatórias da especificação do [protocolo.](https://tools.ietf.org/html/rfc5661) Funcionalidades opcionais como delegações e callback de todos os tipos, atualizações e downgrades de bloqueio, e a autenticação e encriptação kerberos não são suportadas.
- Se a maioria dos seus pedidos forem centrados em metadados, então a latência será pior quando comparada com as operações de leitura/escrita/atualização.
- Deve criar uma nova conta de armazenamento para criar uma quota NFS.
- Apenas o plano de gestão REST APIs são suportados. As APIs do plano de dados não estão disponíveis, o que significa que ferramentas como o Explorador de Armazenamento não funcionarão com ações NFS nem poderá navegar em NFS partilhar dados no portal Azure.
- Disponível apenas para o nível premium.
- Atualmente apenas disponível com armazenamento localmente redundante (LRS).

### <a name="azure-storage-features-not-yet-supported"></a>Funcionalidades de Armazenamento Azure ainda não suportadas

Além disso, as seguintes funcionalidades do Azure Files não estão disponíveis com ações NFS:

- Autenticação baseada em identidade
- Suporte de backup Azure
- Instantâneos
- Eliminação recuperável
- Suporte completo de encriptação em trânsito (para mais detalhes ver [segurança NFS)](../articles/storage/files/storage-files-compare-protocols.md#security)
- Azure File Sync (disponível apenas para clientes Windows, que o NFS 4.1 não suporta)