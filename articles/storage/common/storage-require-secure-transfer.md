---
title: Requer transferência segura no armazenamento do Azure | Documentos da Microsoft
description: Saiba mais sobre o recurso "Transferência segura necessária" para o armazenamento do Azure e como ativá-lo.
services: storage
author: fhryo-msft
ms.service: storage
ms.topic: article
ms.date: 06/20/2017
ms.author: fryu
ms.subservice: common
ms.openlocfilehash: e75cd873b780f514b24ee254dd491b6aa779c420
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58101929"
---
# <a name="require-secure-transfer-in-azure-storage"></a>Requer transferência segura no armazenamento do Azure

A opção "Transferência segura necessária" reforça a segurança da sua conta de armazenamento, permitindo apenas pedidos para a conta de ligações seguras. Por exemplo, quando estiver chamando as APIs REST para aceder à sua conta de armazenamento, tem de ligar através de HTTPS. "Transferência segura necessária" rejeita pedidos que utilizam HTTP.

Quando utiliza o serviço de ficheiros do Azure, qualquer ligação sem encriptação falha quando a "Transferência segura necessária" está ativada. Isto inclui cenários que utilizam SMB 2.1, SMB 3.0 sem encriptação e algumas versões do cliente Linux SMB. 

Por predefinição, a opção "Transferência segura necessária" está desativada quando cria uma conta de armazenamento com o SDK. E ele é habilitado por padrão, quando cria uma conta de armazenamento no Portal do Azure.

> [!NOTE]
> Porque o armazenamento do Azure não suporta o HTTPS para nomes de domínio personalizados, esta opção não é aplicada quando estiver a utilizar um nome de domínio personalizado. E contas de armazenamento clássicas não são suportadas.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Ativar a "Transferência segura necessária" no portal do Azure

Pode ativar a "transferência segura necessária" definição quando criar uma conta de armazenamento na [portal do Azure](https://portal.azure.com). Também pode ativá-la para as contas de armazenamento existentes.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Requer transferência segura para uma nova conta de armazenamento

1. Abra o **criar conta de armazenamento** painel no portal do Azure.
1. Sob **transferência segura necessária**, selecione **ativado**.

   ![Criar o painel de conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Requer transferência segura para uma conta de armazenamento existente

1. Selecione uma conta de armazenamento existente no portal do Azure.
1. O armazenamento de conta em Painel de menu, **configurações**, selecione **configuração**.
1. Sob **transferência segura necessária**, selecione **ativado**.

   ![Painel de menu de conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Ativar a "Transferência segura necessária" por meio de programação

Para requer transferência segura através de programação, utilize a definição _supportsHttpsTrafficOnly_ nas propriedades da conta de armazenamento com a REST API, ferramentas ou bibliotecas:

* [REST API](https://docs.microsoft.com/rest/api/storagerp/storageaccounts) (versão: 2016-12-01)
* [PowerShell](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) (versão: 0.7)
* [CLI](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (versão: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (versão: 1.1.0)
* [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (versão: 6.3.0)
* [SDK de Python](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (versão: 1.1.0)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (versão: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Ativar a "Transferência segura necessária" definição com o PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este exemplo requer o módulo Azure PowerShell Az versão 0,7 ou posterior. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

Execute `Connect-AzAccount` para criar uma ligação com o Azure.

 Utilize a seguinte linha de comando para verificar a definição:

```powershell
> Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Utilize a seguinte linha de comando para ativar a definição:

```powershell
> Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Ativar a "Transferência segura necessária" definição com a CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Utilize a seguinte linha de comando para verificar a definição:

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Utilize a seguinte linha de comando para ativar a definição:

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Passos Seguintes
Armazenamento do Azure fornece um conjunto abrangente de recursos de segurança, que juntos permitem aos desenvolvedores criar aplicativos seguros. Para obter mais detalhes, vá para o [guia de segurança de armazenamento](storage-security-guide.md).
