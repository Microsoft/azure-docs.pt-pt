---
title: Exigir transferência segura para garantir ligações seguras
titleSuffix: Azure Storage
description: Saiba como exigir transferência segura para pedidos para o Armazenamento Azure. Quando necessita de transferência segura para uma conta de armazenamento, quaisquer pedidos originários de uma ligação insegura são rejeitados.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 125f4188ed3f12f366c619af9efe3aa203987c19
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870521"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Exigir transferência segura para garantir ligações seguras

Só pode configurar a sua conta de armazenamento para aceitar pedidos de ligações seguras, definindo a propriedade necessária à **transferência Segura** para a conta de armazenamento. Quando necessita de transferência segura, quaisquer pedidos originários de uma ligação insegura são rejeitados. A Microsoft recomenda que necessite sempre de transferência segura para todas as suas contas de armazenamento.

Quando for necessária uma transferência segura, uma chamada para uma operação API de Depósito Azure deve ser feita em HTTPS. Qualquer pedido feito sobre http é rejeitado.

Ligar-se a uma partilha de Ficheiros Azure sobre SMB sem falhas de encriptação quando é necessária transferência segura para a conta de armazenamento. Exemplos de ligações inseguras incluem as feitas sobre SMB 2.1, SMB 3.0 sem encriptação ou algumas versões do cliente Linux SMB.

Por predefinição, a propriedade necessária para a **transferência Segura** está ativada quando cria uma conta de armazenamento.

> [!NOTE]
> Uma vez que o Azure Storage não suporta HTTPS para nomes de domínio personalizados, esta opção não é aplicada quando está a usar um nome de domínio personalizado. E as contas de armazenamento clássicas não são suportadas.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Exigir transferência segura no portal Azure

Pode ativar a **propriedade necessária** à transferência Segura quando criar uma conta de armazenamento no portal [Azure.](https://portal.azure.com) Também pode permitir as contas de armazenamento existentes.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Exigir transferência segura para uma nova conta de armazenamento

1. Abra o painel de conta de **armazenamento Create** no portal Azure.
1. Sob **transferência segura necessária,** selecione **Ativado**.

   ![Criar lâmina de conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Exigir transferência segura para uma conta de armazenamento existente

1. Selecione uma conta de armazenamento existente no portal Azure.
1. No painel de menuda da conta de armazenamento, em **DEFINIÇÕES,** **selecione Configuração**.
1. Sob **transferência segura necessária,** selecione **Ativado**.

   ![Painel de menu de conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Exigir transferência segura do código

Para exigir transferência segura programáticamente, coloque a propriedade _de suporteSHttpsTrafficOnly_ na conta de armazenamento. Pode definir esta propriedade utilizando a API REST, bibliotecas de clientes ou ferramentas:

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/)
* [SDK .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [SDK Python](https://pypi.org/project/azure-mgmt-storage)
* [SDK Ruby](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Exigir transferência segura com a PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Esta amostra requer o módulo Azure PowerShell Az versão 0.7 ou posterior. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

Execute `Connect-AzAccount` para criar uma ligação com o Azure.

 Utilize a seguinte linha de comando para verificar a definição:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Utilize a seguinte linha de comando para ativar a definição:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Exigir transferência segura com o Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Utilize o seguinte comando para verificar a definição:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Utilize o seguinte comando para ativar a definição:

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

[Recomendações de segurança para armazenamento blob](../blobs/security-recommendations.md)
