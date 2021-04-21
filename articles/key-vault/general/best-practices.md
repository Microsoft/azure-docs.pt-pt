---
title: Boas Práticas para usar Key Vault - Azure Key Vault | Microsoft Docs
description: Saiba mais sobre as melhores práticas para o Azure Key Vault, incluindo o controlo do acesso, quando usar cofres-chave separados, backup, registo e opções de recuperação.
services: key-vault
author: msmbaldwin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: mbaldwin
ms.openlocfilehash: 5e4ec2749cec8444382e2b6e7c856e74c57ebb59
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777172"
---
# <a name="best-practices-to-use-key-vault"></a>Melhores práticas para usar o Cofre-Chave

## <a name="use-separate-key-vaults"></a>Use cofres-chave separados

A nossa recomendação é utilizar um cofre por aplicação por ambiente (Desenvolvimento, Pré-Produção e Produção). Isto ajuda-o a não partilhar segredos em ambientes e também reduz a ameaça em caso de violação.

## <a name="control-access-to-your-vault"></a>Controle acesso ao seu cofre

Azure Key Vault é um serviço de nuvem que protege chaves de encriptação e segredos como certificados, cadeias de conexão e senhas. Como estes dados são sensíveis e críticos do negócio, é necessário garantir o acesso aos cofres das chaves, permitindo apenas aplicações e utilizadores autorizados. Este [artigo](security-overview.md) fornece uma visão geral do modelo de acesso ao Cofre de Chaves. Explica a autenticação e a autorização e descreve como garantir o acesso aos cofres das chaves.

As sugestões ao controlar o acesso ao seu cofre são as seguintes:
1. Bloqueie o acesso à sua subscrição, grupo de recursos e Cofres chave (Azure RBAC)
2. Crie políticas de acesso para cada abóbada
3. Use o menor acesso privilegiado principal para conceder acesso
4. Ligue os [pontos finais do serviço](overview-vnet-service-endpoints.md) Firewall e VNET

## <a name="backup"></a>Backup

Certifique-se de que faz back ups regulares do seu cofre em atualização/exclusão/criação de objetos dentro de um Cofre.

### <a name="azure-powershell-backup-commands"></a>Comandos de backup Azure PowerShell

* [Certificado de Reserva](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate)
* [Chave de reserva](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey)
* [Segredo de Reserva](/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret)

### <a name="azure-cli-backup-commands"></a>Comandos de backup Azure CLI

* [Certificado de Reserva](/cli/azure/keyvault/certificate#az_keyvault_certificate_backup)
* [Chave de reserva](/cli/azure/keyvault/key#az_keyvault_key_backup)
* [Segredo de Reserva](/cli/azure/keyvault/secret#az_keyvault_secret_backup)


## <a name="turn-on-logging"></a>Ligue o Registo

[Ligue o registo para](logging.md) o seu Cofre. Também crie alertas.

## <a name="turn-on-recovery-options"></a>Ligue as opções de recuperação

1. Ligue [soft Delete](soft-delete-overview.md).
2. Ligue a proteção contra a purga se quiser proteger-se contra a eliminação forçada do segredo/cofre mesmo depois de ser ligado o apagamento suave.