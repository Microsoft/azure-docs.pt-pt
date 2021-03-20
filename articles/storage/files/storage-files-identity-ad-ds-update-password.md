---
title: Atualizar palavra-passe de conta de armazenamento AD DS
description: Saiba como atualizar a palavra-passe da conta Ative Directory Domain Services que representa a sua conta de armazenamento. Isto evita que a conta de armazenamento seja limpa quando a palavra-passe expira, evitando falhas de autenticação.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: d16f11a85c6b370b0187975cce965bf3e1b5ba3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "85510259"
---
# <a name="update-the-password-of-your-storage-account-identity-in-ad-ds"></a>Atualize a palavra-passe da sua identidade de conta de armazenamento em DS AD

Se registou a identidade/conta dos Serviços de Domínio do Diretório Ativo (AD DS) que representa a sua conta de armazenamento numa unidade organizacional ou domínio que impõe o tempo de validade da palavra-passe, tem de alterar a palavra-passe antes da idade máxima da palavra-passe. A sua organização poderá executar scripts de limpeza automatizados que apaguem as contas assim que a sua palavra-passe expire. Por isso, se não alterar a sua palavra-passe antes de expirar, a sua conta poderá ser eliminada, o que fará com que perca acesso às suas ações de ficheiroS Azure.

Para ativar a rotação da palavra-passe, pode executar o `Update-AzStorageAccountADObjectPassword` comando a partir do módulo [AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases). Este comando deve ser executado num ambiente de ad DS no local utilizando um utilizador híbrido com permissão do proprietário para a conta de armazenamento e permissões de DS AD para alterar a palavra-passe da identidade que representa a conta de armazenamento. O comando executa ações semelhantes à rotação da chave da conta de armazenamento. Especificamente, obtém a segunda chave Kerberos da conta de armazenamento, e usa-a para atualizar a palavra-passe da conta registada em DS AD. Em seguida, regenera a chave kerberos alvo da conta de armazenamento, e atualiza a palavra-passe da conta registada em DS AD. Você deve executar este comando em um ambiente ligado à DS no local.

```PowerShell
# Update the password of the AD DS account registered for the storage account
# You may use either kerb1 or kerb2
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```
