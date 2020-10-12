---
title: Requerer transferência segura para garantir ligações seguras
titleSuffix: Azure Storage
description: Saiba como exigir transferência segura para pedidos para o Azure Storage. Quando necessita de transferência segura para uma conta de armazenamento, quaisquer pedidos originários de uma ligação insegura são rejeitados.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 22e012c36f5c2c6f195a7e3b21afe9001a4cad0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89077964"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Requerer transferência segura para garantir ligações seguras

Pode configurar a sua conta de armazenamento para aceitar pedidos de ligações seguras apenas definindo a propriedade necessária para a **transferência Secure** necessária para a conta de armazenamento. Quando necessita de transferência segura, quaisquer pedidos originários de uma ligação insegura são rejeitados. A Microsoft recomenda que necessite sempre de transferência segura para todas as suas contas de armazenamento.

Quando for necessária uma transferência segura, deve ser efetuada uma chamada para uma operação API AZure Storage REST sobre HTTPS. Qualquer pedido feito sobre HTTP é rejeitado.

A ligação a uma partilha de FicheiroS Azure sobre SMB sem falha de encriptação quando é necessária uma transferência segura para a conta de armazenamento. Exemplos de ligações inseguras incluem aqueles feitos sobre SMB 2.1, SMB 3.0 sem encriptação, ou algumas versões do cliente Linux SMB.

Por predefinição, a propriedade **requerida por transferência Secure** é ativada quando cria uma conta de armazenamento.

> [!NOTE]
> Como o Azure Storage não suporta HTTPS para nomes de domínio personalizados, esta opção não é aplicada quando se utiliza um nome de domínio personalizado. E as contas clássicas de armazenamento não são suportadas.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Exigir transferência segura no portal Azure

Pode ligar a propriedade necessária para **a transferência Secure** quando criar uma conta de armazenamento no portal [Azure.](https://portal.azure.com) Também pode ative-lo para as contas de armazenamento existentes.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Requerer transferência segura para uma nova conta de armazenamento

1. Abra o painel **de conta de armazenamento Criar** no portal Azure.
1. Em **Transferência Segura necessária**, selecione **Ativado**.

   ![Criar lâmina de conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Requerer transferência segura para uma conta de armazenamento existente

1. Selecione uma conta de armazenamento existente no portal Azure.
1. No painel de menus da conta de armazenamento, em **DEFINIÇÕES,** selecione **Configuração**.
1. Em **Transferência Segura necessária**, selecione **Ativado**.

   ![Painel de menu de conta de armazenamento](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Requerer transferência segura do código

Para exigir transferência segura programáticamente, desloque a propriedade _enableHttpsTrafficOnly_ para _True_ na conta de armazenamento. Pode definir esta propriedade utilizando o Fornecedor de Recursos de Armazenamento REST API, bibliotecas de clientes ou ferramentas:

* [API REST](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [Nodejs](https://www.npmjs.com/package/azure-arm-storage/)
* [SDK do .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [SDK Ruby](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Requerer transferência segura com a PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Esta amostra requer o módulo Azure PowerShell versão 0.7 ou posterior. Executar `Get-Module -ListAvailable Az` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-Az-ps)(Instalar o módulo do Azure PowerShell).

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

## <a name="require-secure-transfer-with-azure-cli"></a>Exigir transferência segura com Azure CLI

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
