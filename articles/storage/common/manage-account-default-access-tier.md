---
title: Gerir o nível de acesso predefinido de uma conta de Armazenamento Azure
description: Saiba como alterar o nível de acesso predefinido de uma conta de Armazenamento de GPv2 ou Blob
author: twooley
ms.author: twooley
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 026ab6be1fd4ef79f818f796c4725f6613a9bc6d
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277390"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Gerir o nível de acesso predefinido de uma conta de Armazenamento Azure

Cada conta Azure Storage tem um nível de acesso predefinido, quente ou fresco. Atribua o nível de acesso quando cria uma conta de armazenamento. O nível de acesso predefinido é quente.

Pode alterar o nível de conta predefinido definindo o atributo **de nível De acesso** na conta de armazenamento. A alteração do nível de conta aplica-se a todos os objetos armazenados na conta que não tenham um conjunto de nível explícito. O toggling do nível da conta de operações de escrita quentes a frio incorre (por 10.000) para todas as bolhas sem um nível definido apenas nas contas GPv2 e toggling de cool para quente incorre tanto operações de leitura (por 10.000) como taxas de recuperação de dados (por GB) para todos os blobs em Blob Storage e contas GPv2.

Para bolhas com o nível definido ao nível do objeto, o nível de conta não se aplica. A camada de arquivo só pode ser aplicada ao nível do blob. Pode alternar entre os níveis de acesso a qualquer momento.

Pode alterar o nível de acesso predefinido depois de ter sido criada uma conta de armazenamento seguindo os passos abaixo.

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Alterar o nível de acesso à conta padrão de uma conta de Armazenamento de GPv2 ou Blob

Os seguintes cenários utilizam o portal Azure ou PowerShell:

# <a name="portal"></a>[Portal](#tab/portal)

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione **Todos os Recursos.**

1. Selecione a sua conta de armazenamento.

1. Em **Definições**, selecione **Configuração** para visualizar e alterar a configuração da conta.

1. Selecione o nível de acesso certo para as suas necessidades: Desa estale o **nível de acesso** para **Cool** ou **Hot**.

1. Clique em **Guardar** na parte superior.

![Alterar o nível de conta padrão no portal Azure](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

O seguinte script PowerShell pode ser usado para alterar o nível de conta. A `$rgName` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento.

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>Passos seguintes

- [Como gerir o nível de uma bolha numa conta de Armazenamento Azure](../blobs/manage-access-tier.md)
- [Determine se o desempenho premium beneficiaria a sua app](../blobs/storage-blob-performance-tiers.md)
- [Avaliar a utilização das suas contas do Storage atuais ao ativar as métricas do Storage do Azure](../blobs/monitor-blob-storage.md)
