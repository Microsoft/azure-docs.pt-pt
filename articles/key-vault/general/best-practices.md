---
title: Boas Práticas para usar Cofre chave - Cofre chave Azure / Microsoft Docs
description: Este documento explica algumas das melhores práticas para usar o Cofre-Chave
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-key-vault
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 16828435dc8913fff8da4717ee7f77cc701504ed
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85213079"
---
# <a name="best-practices-to-use-key-vault"></a>Melhores práticas para usar o Cofre-Chave

## <a name="control-access-to-your-vault"></a>Controle acesso ao seu cofre

Azure Key Vault é um serviço de nuvem que protege chaves de encriptação e segredos como certificados, cadeias de conexão e senhas. Como estes dados são sensíveis e críticos do negócio, é necessário garantir o acesso aos cofres das chaves, permitindo apenas aplicações e utilizadores autorizados. Este [artigo](secure-your-key-vault.md) fornece uma visão geral do modelo de acesso ao Cofre de Chaves. Explica a autenticação e a autorização e descreve como garantir o acesso aos cofres das chaves.

As sugestões ao controlar o acesso ao seu cofre são as seguintes:
1. Bloqueie o acesso à sua subscrição, grupo de recursos e Cofres-Chave (RBAC)
2. Crie políticas de acesso para cada abóbada
3. Use o menor acesso privilegiado principal para conceder acesso
4. Ligue os [pontos finais do serviço](overview-vnet-service-endpoints.md) Firewall e VNET

## <a name="use-separate-key-vault"></a>Use cofre de chaves separado

A nossa recomendação é utilizar um cofre por aplicação por ambiente (Desenvolvimento, Pré-Produção e Produção). Isto ajuda-o a não partilhar segredos em ambientes e também reduz a ameaça em caso de violação.

## <a name="backup"></a>Backup

Certifique-se de que faz back ups regulares do seu cofre em atualização/exclusão/criação de objetos dentro de um Cofre.

### <a name="azure-powershell-backup-commands"></a>Comandos de backup Azure PowerShell

* [Certificado de Reserva](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultCertificate?view=azurermps-6.13.0)
* [Chave de reserva](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultKey?view=azurermps-6.13.0)
* [Segredo de Reserva](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Backup-AzureKeyVaultSecret?view=azurermps-6.13.0)

### <a name="azure-cli-backup-commands"></a>Comandos de backup Azure CLI

* [Certificado de Reserva](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-backup)
* [Chave de reserva](https://docs.microsoft.com/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-backup)
* [Segredo de Reserva](https://docs.microsoft.com/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-backup)


## <a name="turn-on-logging"></a>Ligue o Registo

[Ligue o registo para](logging.md) o seu Cofre. Também crie alertas.

## <a name="turn-on-recovery-options"></a>Ligue as opções de recuperação

1. Ligue [soft Delete](overview-soft-delete.md).
2. Ligue a proteção contra a purga se quiser proteger-se contra a eliminação forçada do segredo/cofre mesmo depois de ser ligado o apagamento suave.
