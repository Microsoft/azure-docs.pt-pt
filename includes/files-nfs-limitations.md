---
title: incluir ficheiro
description: incluir ficheiro
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/04/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6e819e1078ac90ef16070702e7961122b06c1d6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107880047"
---
Durante a pré-visualização, a NFS tem as seguintes limitações:

- Atualmente, o NFS 4.1 suporta apenas a maioria das características da especificação do [protocolo.](https://tools.ietf.org/html/rfc5661) Algumas funcionalidades, como delegações e chamadas de todos os tipos, atualizações e downgrades de bloqueio, autenticação de Kerberos e encriptação não são suportadas.
- Se a maioria dos seus pedidos forem centrados em metadados, então a latência será pior quando comparada com as operações de leitura/escrita/atualização.
- As ações NFS só podem ser ativadas/criadas em novas contas de armazenamento e não nas existentes
- Apenas o plano de gestão REST APIs são suportados. As APIs do plano de dados não estão disponíveis, o que significa que ferramentas como o Storage Explorer não funcionarão com ações NFS nem poderá navegar em dados de partilha de NFS no portal Azure.
- A azCopy não é suportada atualmente.
- Disponível apenas para o nível premium.
- As ações da NFS só aceitam uid/GID numérico. Para evitar que os seus clientes enviem UID/GID alfanumérico, deve desativar o mapeamento de ID.
- As ações só podem ser montadas a partir de uma conta de armazenamento num VM individual, quando se utilizam links privados. A tentativa de montar ações de outras contas de armazenamento falhará.
- É melhor confiar nas permissões atribuídas ao grupo primário. Por vezes, as permissões atribuídas ao grupo não primário do utilizador podem resultar no acesso negado devido a um bug conhecido.

### <a name="azure-storage-features-not-yet-supported"></a>Funcionalidades de Armazenamento Azure ainda não suportadas

Além disso, as seguintes funcionalidades do Azure Files não estão disponíveis com ações NFS:

- Autenticação baseada em identidade
- Suporte de backup Azure
- Instantâneos
- Eliminação recuperável
- Suporte completo de encriptação em trânsito (para mais detalhes ver [segurança NFS)](../articles/storage/files/storage-files-compare-protocols.md#security)
- Azure File Sync (disponível apenas para clientes Windows, que o NFS 4.1 não suporta)
