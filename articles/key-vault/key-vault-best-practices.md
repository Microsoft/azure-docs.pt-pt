---
title: Práticas recomendadas para usar o Key Vault-Azure Key Vault | Microsoft Docs
description: Este documento explica algumas das práticas recomendadas para usar Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 654a9bb772c8a7426a335c98dfeca69515b9ce67
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881629"
---
# <a name="best-practices-to-use-key-vault"></a>Práticas recomendadas para usar Key Vault

## <a name="control-access-to-your-vault"></a>Controlar o acesso ao seu cofre

Azure Key Vault é um serviço de nuvem que protege chaves de criptografia e segredos como certificados, cadeias de conexão e senhas. Como esses dados são confidenciais e críticos para os negócios, você precisa proteger o acesso aos seus cofres de chaves, permitindo apenas aplicativos e usuários autorizados. Este [artigo](key-vault-secure-your-key-vault.md) fornece uma visão geral do modelo de acesso Key Vault. Ele explica a autenticação e a autorização e descreve como proteger o acesso aos seus cofres de chaves.

As sugestões ao controlar o acesso ao cofre são as seguintes:
1. Bloquear o acesso à sua assinatura, grupo de recursos e cofres de chaves (RBAC)
2. Criar políticas de acesso para cada cofre
3. Use a entidade de acesso de privilégios mínimos para conceder acesso
4. Ativar [pontos de extremidade de serviço de rede virtual](key-vault-overview-vnet-service-endpoints.md) e firewall

## <a name="use-separate-key-vault"></a>Usar Key Vault separadas

Nossa recomendação é usar um cofre por aplicativo por ambiente (desenvolvimento, pré-produção e produção). Isso ajuda você a não compartilhar segredos entre ambientes e também reduz a ameaça em caso de uma violação.

## <a name="backup"></a>Criar cópia de segurança

Certifique-se de fazer backups regulares de seu [cofre](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) em atualizar/excluir/criar objetos em um cofre.

## <a name="turn-on-logging"></a>Ativar registro em log

[Ative o registro em log](key-vault-logging.md) para seu cofre. Configure também alertas.

## <a name="turn-on-recovery-options"></a>Ativar opções de recuperação

1. Ative a [exclusão reversível](key-vault-ovw-soft-delete.md).
2. Ative a proteção de limpeza se você quiser se proteger contra a exclusão forçada do segredo/cofre mesmo depois que a exclusão reversível estiver ativada.
