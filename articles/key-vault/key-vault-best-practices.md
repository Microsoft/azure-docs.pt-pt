---
title: Melhores práticas para utilizar o Key Vault, Azure Key Vault | Documentos da Microsoft
description: Este documento explica algumas das melhores práticas para utilizar o Key Vault
services: key-vault
documentationcenter: ''
author: yvprashanth
manager: barbkess
tags: azure-key-vault
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: prashanthyv
ms.openlocfilehash: 85bd1858bc3f2d505e1e4d0a88e8c77a46ae4447
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2019
ms.locfileid: "57882038"
---
# <a name="best-practices-to-use-key-vault"></a>Melhores práticas para utilizar o Key Vault

## <a name="control-access-to-your-vault"></a>Controlar o acesso ao seu Cofre

O Azure Key Vault é um serviço cloud que salvaguarda as chaves de encriptação e os segredos, como certificados, cadeias de ligação e palavras-passe. Uma vez que estes dados são confidenciais e crítico para a empresa, terá de proteger o acesso aos seus cofres de chaves ao permitir que apenas aplicativos autorizados e utilizadores. Isso [artigo](key-vault-secure-your-key-vault.md) fornece uma descrição geral do modelo de acesso do Cofre de chaves. Ele explica a autenticação e autorização e descreve como proteger o acesso aos seus cofres de chaves.

Sugestões ao controlar o acesso ao seu Cofre são os seguintes:
1. Bloquear o acesso à sua subscrição, grupo de recursos e os cofres de chave (RBAC)
2. Criar políticas de acesso para cada Cofre
3. Utilizar o principal de acesso do menor privilégio para conceder acesso
4. Ativar a Firewall e [pontos finais de serviço VNET](key-vault-overview-vnet-service-endpoints.md)

## <a name="use-separate-key-vault"></a>Utilizar o Cofre de chave separado

A nossa recomendação é usar um cofre por aplicação por ambiente (desenvolvimento, pré-produção e produção). Isto ajuda-o a não partilham os segredos em ambientes e também reduz o risco em caso de uma violação.

## <a name="backup-your-vault"></a>O Cofre de cópia de segurança

Certifique-se de que voltar regular cumulativos de atualizações para seus [cofre](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) no update/delete/criação de objetos dentro de um cofre.

## <a name="turn-on-logging"></a>Ativar o registo

[Ativar o registo](key-vault-logging.md) para o cofre. Também posso configurar alertas.

## <a name="turn-on-recovery-options"></a>Ativar opções de recuperação

1. Ative [eliminação de forma recuperável](key-vault-ovw-soft-delete.md).
2. Ative a proteção contra remoção para proteção contra força a eliminação do segredo do / cofre mesmo após a eliminação de forma recuperável estiver ativada.
