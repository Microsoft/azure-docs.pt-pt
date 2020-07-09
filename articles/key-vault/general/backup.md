---
title: Azure Key Vault backup / Microsoft Docs
description: Utilize este documento para ajudar a fazer o back up de um segredo, chave ou certificado armazenado no Cofre da Chave Azure.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: 8a152e2771f0b207e81f42c6ecae2e4d14605051
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86147831"
---
# <a name="azure-key-vault-backup"></a>Backup do Cofre de Chaves Azure

Este documento irá mostrar-lhe como realizar uma cópia de segurança dos segredos, chaves e certificados individuais armazenados no seu cofre de chaves. Esta cópia de segurança destina-se a fornecer-lhe uma cópia offline de todos os seus segredos no caso improvável de perder acesso ao seu cofre de chaves.

## <a name="overview"></a>Descrição geral

O Key Vault fornece automaticamente várias funcionalidades para manter a disponibilidade e prevenir a perda de dados. Este backup só deve ser tentado se houver uma justificação crítica para manter uma cópia de segurança dos seus segredos. O backup de segredos no cofre chave pode introduzir desafios operacionais adicionais, tais como manter vários conjuntos de registos, permissões e backups quando os segredos expiram ou giram.

O Key Vault mantém a disponibilidade em cenários de desastres e falhará automaticamente sobre os pedidos para uma região emparelhada sem qualquer intervenção necessária por parte de um utilizador. Para mais informações, por favor leia as seguintes informações. [Recuperação de desastres no cofre de Azure](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)

O Key Vault protege contra a eliminação acidental e maliciosa dos seus segredos através da proteção de eliminação e purga. Se pretender proteção contra a eliminação acidental ou maliciosa dos seus segredos, por favor, configuure as funcionalidades de proteção para eliminar e limpar o seu cofre. Para mais informações, consulte o seguinte documento. [Recuperação do cofre da chave Azure](https://docs.microsoft.com/azure/key-vault/general/overview-soft-delete)

## <a name="limitations"></a>Limitações

O Azure Key Vault não suporta atualmente uma forma de apoiar um cofre inteiro numa única operação. Qualquer tentativa de utilização dos comandos listados neste documento para realizar uma cópia de segurança automatizada de um cofre de chaves não será suportada pela Microsoft ou pela Azure Key Vault Team.

Tentar utilizar os comandos apresentados no documento abaixo para criar automatização personalizada pode resultar em erros.

* O backup de segredos com várias versões pode causar erros de tempo.
* O backup cria uma imagem pontual. Os segredos podem renovar-se durante uma cópia de segurança causando uma incompatibilidade das chaves de encriptação.
* Exceder os limites de serviço do cofre chave para pedidos por segundo fará com que o cofre da chave seja estrangulado e fará com que a cópia de segurança falhe.

## <a name="design-considerations"></a>Considerações de conceção

Quando faz cópia de segurança de um objeto armazenado no cofre de chaves (segredo, chave ou certificado), a operação de backup descarrega o objeto como uma bolha encriptada. Esta bolha não pode ser desencriptada fora de Azure. Para obter dados utilizáveis desta bolha, você deve restaurar a bolha em um cofre chave dentro da mesma Assinatura Azure e Geografia Azure.
[Geografias Azure](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="prerequisites"></a>Pré-requisitos

* Nível de contribuinte ou permissões mais altas numa Subscrição Azure
* Um cofre principal contendo segredos que quer apoiar
* Um cofre de chaves secundária onde os segredos serão restaurados.

## <a name="back-up-and-restore-with-azure-portal"></a>Recuar e restaurar com o portal Azure

### <a name="back-up"></a>Fazer cópia de segurança

1. Navegue para o portal do Azure.
2. Selecione o cofre da chave.
3. Navegue para o objeto (secreto, chave ou certificado) que pretende fazer recuar.

    ![Imagem](../media/backup-1.png)

4. Selecione o objeto.
5. Selecione 'Baixar Backup'

    ![Imagem](../media/backup-2.png)
    
6. Clique no botão 'Descarregar'.

    ![Imagem](../media/backup-3.png)
    
7. Guarde a bolha encriptada num local seguro.

### <a name="restore"></a>Restauro

1. Navegue para o portal do Azure.
2. Selecione o cofre da chave.
3. Navegue para o tipo de objeto (secreto, chave ou certificado) que pretende restaurar.
4. Selecione 'Restaurar backup'

    ![Imagem](../media/backup-4.png)
    
5. Procure no local onde guardou a bolha encriptada.
6. Selecione "Ok".

## <a name="back-up-and-restore-with-the-azure-cli"></a>Recuar e restaurar com o Azure CLI

```azurecli
## Login To Azure
az login

## Set your Subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a Provider
az provider register -n Microsoft.KeyVault

## Backup a Certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Backup a Secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a Secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Passos seguintes

Ligue o registo e a monitorização do seu Cofre da Chave Azure. [Azure Key Vault Logging](https://docs.microsoft.com/azure/key-vault/general/logging)
