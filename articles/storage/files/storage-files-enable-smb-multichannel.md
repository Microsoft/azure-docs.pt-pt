---
title: Ativar o multicanal SMB
description: Saiba como ativar o SMB Multichannel em ações de ficheiros premium Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2f867fa6d4b7e1d864a85106b5d957a53d38eb76
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732555"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Ativar o SMB Multicanal numa conta de arquiteta (pré-visualização) 

As contas Azure FileStorage suportam o SMB Multichannel (pré-visualização), o que aumenta o desempenho de um cliente SMB 3.x, estabelecendo múltiplas ligações de rede às suas ações de ficheiros premium. Este artigo fornece orientações passo a passo para permitir o Multicanal SMB numa conta de armazenamento existente. Para obter informações detalhadas sobre o Azure Files SMB Multichannel, consulte o desempenho do SMB Multichannel.

## <a name="limitations"></a>Limitações

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Disponibilidade regional

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Pré-requisitos

- [Criar uma conta de arquitetorage](./storage-how-to-create-file-share.md).
- Se pretender utilizar o módulo Azure PowerShell, [instale a versão de pré-visualização 3.0.1 do módulo](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Introdução

Abra uma janela PowerShell e assine na sua assinatura Azure. A partir daí, pode inscrever-se para a pré-visualização do SMB Multicanal com os seguintes comandos.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> As inscrições podem demorar até uma hora.

### <a name="verify-that-feature-registration-is-complete"></a>Verifique se o registo de funcionalidades está completo

Uma vez que pode demorar até uma hora para ativar a funcionalidade na sua conta de armazenamento, pode querer utilizar o seguinte comando para validar que está registado para a sua subscrição:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Ativar o SMB Multicanal 
Uma vez criada uma conta FileStorage, pode seguir as instruções para atualizar as definições do SMB Multicanal para a sua conta de armazenamento.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Inscreva-se no portal Azure e navegue para a conta de armazenamento de filestorage em que pretende configurar o SMB Multichannel.
1. Selecione **as ações do Ficheiro** no serviço De **ficheiro** e, em seguida, selecione **definições de partilha de ficheiros**.
1. Toggle **SMB Multicanal** para **dentro** (ou **desligado** para desativar) e selecione **guardar**.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Screenshot da conta de armazenamento, smb multicanal é alternado.":::

Se a opção Multicanal SMB não estiver visível nas **definições de partilha de ficheiros** ou se não conseguir atualizar o erro de definição durante a atualização da configuração, certifique-se de que a sua subscrição está registada e que a sua conta está numa das [regiões suportadas](#regional-availability) com tipo de conta e replicação suportadas.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para ativar o SMB Multichannel utilizando o módulo Azure PowerShell, tem de instalar a versão de [pré-visualização 3.0.1](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) do módulo.

Desaprote as variáveis `$resourceGroupName` e a sua conta de grupo de recursos e de armazenamento antes de executar estes `$storageAccountName` comandos PowerShell.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
O Azure CLI ainda não suporta a configuração do Multicanal SMB. Consulte as instruções do portal para configurar o SMB Multichannel na conta de armazenamento.

---

> [!NOTE]
> Quaisquer alterações às definições de configuração do SMB Multicanal aplicar-se-ão a todas as ações de ficheiros na conta de armazenamento. No entanto, terá de remontar a participação no seu cliente para que as alterações entrem em vigor.


## <a name="next-steps"></a>Passos seguintes 

- [Remonte a sua parte](storage-how-to-use-files-windows.md) de ficheiro para tirar partido do SMB Multichannel.
- [Resolva quaisquer problemas relacionados com o SMB Multichannel](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- Para saber mais sobre as melhorias, consulte o [desempenho do SMB Multicanal](storage-files-smb-multichannel-performance.md)
 - Para saber mais sobre a funcionalidade Multicanal do Windows SMB, consulte [Manage SMB Mulitchannel](/azure-stack/hci/manage/manage-smb-multichannel).