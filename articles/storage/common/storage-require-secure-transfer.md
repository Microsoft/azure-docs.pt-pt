---
title: Exigir transferência segura para garantir conexões seguras
titleSuffix: Azure Storage
description: Saiba como exigir transferência segura para solicitações para o armazenamento do Azure. Quando você precisar de transferência segura para uma conta de armazenamento, todas as solicitações provenientes de uma conexão insegura serão rejeitadas.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 3b2d78bd929e23d49a57f337022f6678114bb5fe
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75457441"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Exigir transferência segura para garantir conexões seguras

Você pode configurar sua conta de armazenamento para aceitar solicitações de conexões seguras somente definindo a propriedade de **transferência segura necessária** para a conta de armazenamento. Quando você precisar de transferência segura, todas as solicitações provenientes de uma conexão insegura serão rejeitadas. A Microsoft recomenda que você sempre precise de transferência segura para todas as suas contas de armazenamento.

Quando a transferência segura é necessária, uma chamada para uma operação da API REST do armazenamento do Azure deve ser feita via HTTPS. Qualquer solicitação feita via HTTP é rejeitada.

A conexão com um compartilhamento de arquivos do Azure via SMB sem criptografia falha quando a transferência segura é necessária para a conta de armazenamento. Exemplos de conexões inseguras incluem as feitas sobre SMB 2,1, SMB 3,0 sem criptografia ou algumas versões do cliente SMB do Linux.

Por padrão, a propriedade de **transferência segura necessária** é habilitada quando você cria uma conta de armazenamento no portal do Azure. No entanto, ele é desabilitado quando você cria uma conta de armazenamento com o SDK.

> [!NOTE]
> Como o armazenamento do Azure não dá suporte a HTTPS para nomes de domínio personalizados, essa opção não é aplicada quando você está usando um nome de domínio personalizado. E contas de armazenamento clássicas não são suportadas.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Exigir transferência segura no portal do Azure

Você pode ativar a propriedade **transferência segura necessária** ao criar uma conta de armazenamento no [portal do Azure](https://portal.azure.com). Você também pode habilitá-lo para contas de armazenamento existentes.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Exigir transferência segura para uma nova conta de armazenamento

1. Abra o painel **criar conta de armazenamento** na portal do Azure.
1. Em **transferência segura necessária**, selecione **habilitado**.

   ![Folha criar conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Exigir transferência segura para uma conta de armazenamento existente

1. Selecione uma conta de armazenamento existente no portal do Azure.
1. No painel de menu da conta de armazenamento, em **configurações**, selecione **configuração**.
1. Em **transferência segura necessária**, selecione **habilitado**.

   ![Painel de menu da conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Exigir transferência segura do código

Para exigir transferência segura de forma programática, defina a propriedade _supportsHttpsTrafficOnly_ na conta de armazenamento. Você pode definir essa propriedade usando a API REST do provedor de recursos de armazenamento, bibliotecas de cliente ou ferramentas:

* [API REST](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/)
* [SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [SDK Ruby](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Exigir transferência segura com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este exemplo requer o módulo Azure PowerShell AZ versão 0,7 ou posterior. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

Execute `Connect-AzAccount` para criar uma ligação com o Azure.

 Use a linha de comando a seguir para verificar a configuração:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Use a seguinte linha de comando para habilitar a configuração:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Exigir transferência segura com CLI do Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Use o seguinte comando para verificar a configuração:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Use o seguinte comando para habilitar a configuração:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Passos seguintes

[Recomendações de segurança para o armazenamento de BLOBs](../blobs/security-recommendations.md)
