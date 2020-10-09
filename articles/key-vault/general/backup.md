---
title: Apoie um segredo, chave ou certificado armazenado no Cofre da Chave Azure Microsoft Docs
description: Utilize este documento para ajudar a fazer o back up de um segredo, chave ou certificado armazenado no Cofre da Chave Azure.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: sudbalas
ms.openlocfilehash: a1c07432dcf90759662e8f4aaedc760abd18157c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88585938"
---
# <a name="azure-key-vault-backup"></a>Backup do Cofre de Chaves Azure

Este documento mostra-lhe como fazer o back up secrets, chaves e certificados armazenados no seu cofre de chaves. Uma cópia de segurança destina-se a fornecer-lhe uma cópia offline de todos os seus segredos no caso improvável de perder acesso ao seu cofre de chaves.

## <a name="overview"></a>Descrição geral

O Azure Key Vault fornece automaticamente funcionalidades para o ajudar a manter a disponibilidade e a prevenir a perda de dados. Faça o reforço dos segredos apenas se tiver uma justificação crítica de negócio. O backup de segredos no cofre chave pode introduzir desafios operacionais, tais como manter vários conjuntos de registos, permissões e backups quando os segredos expiram ou giram.

O Key Vault mantém a disponibilidade em cenários de desastres e falhará automaticamente sobre os pedidos a uma região emparelhada sem qualquer intervenção de um utilizador. Para mais informações, consulte [a disponibilidade e redundância do Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance).

Se pretender proteção contra a eliminação acidental ou maliciosa dos seus segredos, configuure as funcionalidades de proteção para eliminar e limpar o seu cofre. Para obter mais informações, consulte [a visão geral do Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/general/soft-delete-overview)

## <a name="limitations"></a>Limitações

O Cofre-Chave não fornece uma maneira de apoiar um cofre inteiro numa única operação. Qualquer tentativa de usar os comandos listados neste documento para fazer uma cópia de segurança automatizada de um cofre de chaves pode resultar em erros e não será suportada pela Microsoft ou pela equipa Azure Key Vault. 

Considere também as seguintes consequências:

* O backup de segredos que têm várias versões pode causar erros de tempo.
* Um backup cria uma foto pontual. Os segredos podem renovar-se durante uma cópia de segurança, causando uma incompatibilidade de chaves de encriptação.
* Se exceder os limites de serviço do cofre para pedidos por segundo, o cofre da chave será acelerado e a cópia de segurança falhará.

## <a name="design-considerations"></a>Considerações de conceção

Quando fizer uma cópia de segurança de um objeto de cofre, como um segredo, chave ou certificado, a operação de backup descarregará o objeto como uma bolha encriptada. Esta bolha não pode ser desencriptada fora de Azure. Para obter dados utilizáveis desta bolha, você deve restaurar a bolha em um cofre chave dentro da mesma assinatura Azure e [geografia Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="prerequisites"></a>Pré-requisitos

Para apoiar um objeto de cofre chave, deve ter: 

* Permissões de nível de contribuinte ou superior numa subscrição do Azure.
* Um cofre primário que contém os segredos que queres apoiar.
* Um cofre de chaves secundária onde os segredos serão restaurados.

## <a name="back-up-and-restore-from-the-azure-portal"></a>Recuar e restaurar a partir do portal Azure

Siga os passos desta secção para recuar e restaurar os objetos utilizando o portal Azure.

### <a name="back-up"></a>Fazer cópia de segurança

1. Aceda ao portal do Azure.
2. Selecione o cofre da chave.
3. Vá ao objeto (secreto, chave ou certificado) que pretende fazer recuar.

    ![Screenshot mostrando onde selecionar a definição de Chaves e um objeto num cofre de chaves.](../media/backup-1.png)

4. Selecione o objeto.
5. Selecione **Baixar Backup**.

    ![Screenshot mostrando onde selecionar o botão de Cópia de Segurança de Descarregamento num cofre de chaves.](../media/backup-2.png)
    
6. Selecione **Transferir**.

    ![Screenshot mostrando onde selecionar o botão Descarregar num cofre de chaves.](../media/backup-3.png)
    
7. Guarde a bolha encriptada num local seguro.

### <a name="restore"></a>Restauro

1. Aceda ao portal do Azure.
2. Selecione o cofre da chave.
3. Aceda ao tipo de objeto (secreto, chave ou certificado) que pretende restaurar.
4. Selecione **Restaurar a cópia de segurança**.

    ![Screenshot mostrando onde selecionar Restaurar backup em um cofre de chaves.](../media/backup-4.png)
    
5. Vá ao local onde guardou a bolha encriptada.
6. Selecione **OK**.

## <a name="back-up-and-restore-from-the-azure-cli"></a>Recuar e restaurar a partir do CLI Azure

```azurecli
## Log in to Azure
az login

## Set your subscription
az account set --subscription {AZURE SUBSCRIPTION ID}

## Register Key Vault as a provider
az provider register -n Microsoft.KeyVault

## Back up a certificate in Key Vault
az keyvault certificate backup --file {File Path} --name {Certificate Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a key in Key Vault
az keyvault key backup --file {File Path} --name {Key Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Back up a secret in Key Vault
az keyvault secret backup --file {File Path} --name {Secret Name} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a certificate in Key Vault
az keyvault certificate restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a key in Key Vault
az keyvault key restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

## Restore a secret in Key Vault
az keyvault secret restore --file {File Path} --vault-name {Key Vault Name} --subscription {SUBSCRIPTION ID}

```

## <a name="next-steps"></a>Passos seguintes

Ligue [o registo e a monitorização](https://docs.microsoft.com/azure/key-vault/general/logging) do Key Vault.
