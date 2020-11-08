---
title: Backup/restauro completo e restauro seletivo para O HSM gerido Azure
description: Este documento explica a plena cópia de segurança/restauro e restauro seletivo
services: key-vault
author: amitbapat
tags: azure-key-vault
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: e051a36b3c91fadc0c3b602cb4ba8e3dbcff1294
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367136"
---
# <a name="full-backup-and-restore"></a>Cópia de segurança completa e restauro

> [!NOTE]
> Esta funcionalidade só está disponível para o HSM gerido pelo tipo de recurso.

O HSM gerido suporta a criação de uma cópia de segurança completa de todo o conteúdo do HSM, incluindo todas as teclas, versões, atributos, tags e atribuições de funções. A cópia de segurança é encriptada com teclas criptográficas associadas ao domínio de segurança do HSM.

A cópia de segurança é uma operação de avião de dados. O chamador que inicia a operação de backup deve ter permissão para executar dataAction **Microsoft.KeyVault/managedHsm/backup/start/action**.

Só os seguintes papéis incorporados têm permissão para realizar cópias de segurança completas:
- Administrador gerido do HSM
- Backup HSM gerido

Deve fornecer as seguintes informações para executar uma cópia de segurança completa:
- Nome ou URL do HSM
- Nome da conta de armazenamento
- Recipiente de armazenamento de bolhas de conta de armazenamento de conta de armazenamento
- Recipiente de armazenamento SAS token com permissões `crdw`

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="full-backup"></a>Cópia de segurança completa

A cópia de segurança é uma operação de longa duração, mas devolverá imediatamente uma identificação de emprego. Pode verificar o estado do processo de backup utilizando este ID de trabalho. O processo de backup cria uma pasta dentro do recipiente designado com um padrão de nomeação a **`mhsm-{HSM_NAME}-{YYYY}{MM}{DD}{HH}{mm}{SS}`** seguir, onde HSM_NAME é o nome do HSM gerido sendo apoiado e YYYYY, MM, DD, HH, MM, mm, SS são o ano, mês, data, hora, minutos e segundos de data/hora na UTC quando o comando de backup foi recebido.

Enquanto a cópia de segurança estiver em andamento, o HSM pode não funcionar em pleno funcionamento, uma vez que algumas divisórias HSM estarão ocupadas a realizar a operação de backup.

```azurecli-interactive
# time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Create a container

az storage container create --account-name  mhsmdemobackup --name mhsmdemobackupcontainer  --account-key $skey

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions crdw --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Backup HSM

az keyvault backup start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --subscription 361da5d4-a47a-4c79-afdd-d66f684f4070
```

## <a name="full-restore"></a>Restauro total

A restauração total permite-lhe restaurar completamente o conteúdo do HSM com uma cópia de segurança anterior, incluindo todas as teclas, versões, atributos, tags e atribuições de funções. Tudo o que está armazenado no HSM será eliminado, e voltará ao mesmo estado em que estava quando a fonte de apoio foi criada.

> [!IMPORTANT]
> A restauração total é uma operação muito destrutiva e disruptiva. Por isso, é obrigatório ter concluído uma cópia de segurança completa nos últimos 30 minutos antes de `restore` uma operação poder ser realizada.

Restaurar é uma operação de avião de dados. O chamador que inicia a operação de restauro deve ter permissão para executar dataAction **Microsoft.KeyVault/managedHsm/restore/start/action**. A fonte HSM onde a cópia de segurança foi criada e o destino HSM onde a restauração será realizada **deve** ter o mesmo Domínio de Segurança. Veja mais [sobre o Domínio de Segurança gerido do HSM.](security-domain.md)

Deve fornecer as seguintes informações para executar uma restauração completa:
- Nome ou URL do HSM
- Nome da conta de armazenamento
- Recipiente blob de conta de armazenamento
- Recipiente de armazenamento SAS token com permissões `rl`
- Nome da pasta do recipiente de armazenamento onde a cópia de segurança da fonte é armazenada

Restaurar é uma operação de longa duração, mas irá imediatamente devolver uma identificação de trabalho. Pode verificar o estado do processo de restauro utilizando este ID job. Quando o processo de restauro está em curso, o HSM entra num modo de restauro e todo o comando do plano de dados (exceto verificar o estado de restauro) são desativado.

```azurecli-interactive
#### time for 30 minutes later for SAS token expiry

end=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')

# Get storage account key

skey=$(az storage account keys list --query '[0].value' -o tsv --account-name mhsmdemobackup --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)

# Generate a container sas token

sas=$(az storage container generate-sas -n mhsmdemobackupcontainer --account-name mhsmdemobackup --permissions rl --expiry $end --account-key $skey -o tsv --subscription a1ba9aaa-b7f6-4a33-b038-6e64553a6c7b)
```

## <a name="backup-hsm"></a>Backup HSM

```
az keyvault restore start --hsm-name mhsmdemo2 --storage-account-name mhsmdemobackup --blob-container-name mhsmdemobackupcontainer --storage-container-SAS-token $sas --backup-folder mhsm-mhsmdemo-2020083120161860
```

## <a name="next-steps"></a>Passos Seguintes
- Ver [Gerir um HSM gerido utilizando o Azure CLI](key-management.md).
- Saiba mais sobre [o Domínio de Segurança gerido do HSM](security-domain.md)