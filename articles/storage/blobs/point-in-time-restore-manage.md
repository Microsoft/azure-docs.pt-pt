---
title: Ativar e gerir a restauração ponto-a-tempo para bolhas de bloco (pré-visualização)
titleSuffix: Azure Storage
description: Aprenda a usar a restauração pontual (pré-visualização) para restaurar as bolhas de bloco saem para um estado num ponto mais precoce.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cbfc5667fb35b8f807a3a806dda4647af10e9392
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118215"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Ativar e gerir a restauração ponto-a-tempo para bolhas de bloco (pré-visualização)

Pode utilizar a restauração pontual (pré-visualização) para restaurar as bolhas de bloco sinuosas no seu estado num momento anterior. Este artigo descreve como permitir a restauração ponto-a-tempo para uma conta de armazenamento com a PowerShell. Também mostra como realizar uma operação de restauro com a PowerShell.

Para mais informações e para aprender a registar-se para a pré-visualização, consulte a [restauração ponto-a-tempo para as bolhas de bloco (pré-visualização)](point-in-time-restore-overview.md).

> [!CAUTION]
> O restauro pontual suporta a restauração das operações apenas em blocos. As operações nos contentores não podem ser restauradas. Se eliminar um recipiente da conta de armazenamento, ligando para o funcionamento do [recipiente eliminar](/rest/api/storageservices/delete-container) durante a pré-visualização de restauro pontual, esse recipiente não pode ser restaurado com uma operação de restauro. Durante a pré-visualização, em vez de apagar um recipiente, elimine as bolhas individuais se desejar restaurá-las.

> [!IMPORTANT]
> A pré-visualização de restauro pontual destina-se apenas à utilização não produção. Os acordos de nível de serviço de produção (SLAs) não estão atualmente disponíveis.

## <a name="install-the-preview-module"></a>Instale o módulo de pré-visualização

Para configurar o restauro ponto-a-tempo do Azure com o PowerShell, instale primeiro a versão [1.14.1-preview](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) do módulo Az.Storage PowerShell. Siga estes passos para instalar o módulo de pré-visualização:

1. Desinstale quaisquer instalações anteriores do Azure PowerShell a partir do Windows utilizando as **aplicações & definição** de funcionalidades em **Definições**.

1. Certifique-se de que tem a versão mais recente do PowerShellGet instalada. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Feche e reabra a janela PowerShell depois de instalar o PowerShellGet.

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instale o módulo de pré-visualização Az.Storage:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Para mais informações sobre a instalação do Azure PowerShell, consulte [Instalar o Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Ativar e configurar a restauração ponto-a-tempo

Antes de ativar e configurar a restauração ponto-a-tempo, ative os seus pré-requisitos: eliminação suave, alteração do feed e versão blob. Para obter mais informações sobre a ativação de cada uma destas funcionalidades, consulte estes artigos:

- [Ativar a eliminação suave para bolhas](soft-delete-enable.md)
- [Ativar e desativar o feed de mudança](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Ativar e gerir a versão blob](versioning-enable.md)

Para configurar o restauro ponto-a-tempo do Azure com a PowerShell, ligue para o comando Enable-AzStorageBlobRestorePolicy. O exemplo seguinte permite eliminar suavemente e define o período de retenção de eliminação suave, permite alterar o feed e, em seguida, permite a restauração ponto-a-tempo. Antes de executar o exemplo, utilize o portal Azure ou um modelo de Gestor de Recursos Azure para permitir também a versão blob.

Ao executar o exemplo, lembre-se de substituir os valores em suportes angulares por valores próprios:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>Realizar uma operação de restauro

Para iniciar uma operação de restauro, ligue para o comando Restore-AzStorageBlobRange, especificando o ponto de restauro como um valor UTC **DateTime.** Pode especificar uma ou mais gamas lexicográficas de bolhas para restaurar, ou omitir uma gama para restaurar todas as bolhas em todos os recipientes da conta de armazenamento. A operação de restauro pode demorar alguns minutos a ser concluída.

Tenha em mente as seguintes regras ao especificar uma gama de bolhas para restaurar:

- O padrão do recipiente especificado para o intervalo de início e o intervalo de fim deve incluir um mínimo de três caracteres. O corte dianteiro (/) utilizado para separar um nome de recipiente de um nome blob não conta para este mínimo.
- Apenas uma gama pode ser especificada por operação de restauro.
- Os caracteres wildcard não são suportados. São tratados como caracteres padrão.
- Pode restaurar as bolhas nos `$root` recipientes e `$web` contentores especificando-as explicitamente numa gama passada para uma operação de restauro. Os `$root` `$web` recipientes e recipientes só são restaurados se forem explicitamente especificados. Outros contentores do sistema não podem ser restaurados.

### <a name="restore-all-containers-in-the-account"></a>Restaurar todos os recipientes na conta

Para restaurar todos os recipientes e bolhas na conta de armazenamento, ligue para o comando Restore-AzStorageBlobRange, omitindo o `-BlobRestoreRange` parâmetro. O exemplo seguinte restaura os contentores na conta de armazenamento ao seu estado 12 horas antes do presente momento:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Restaurar uma única gama de bolhas de bloco

Para restaurar uma gama de bolhas, ligue para o comando Restore-AzStorageBlobRange e especifique uma gama lexicográfica de nomes de recipientes e blob para o `-BlobRestoreRange` parâmetro. O início da gama é inclusivo, e o fim da gama é exclusivo.

Por exemplo, para restaurar as bolhas num único recipiente chamado *recipiente de amostras,* pode especificar uma gama que começa com *um recipiente de amostras* e termina com *um recipiente de amostra1*. Não existe qualquer exigência para que os recipientes nomeados nos intervalos de início e fim existam. Uma vez que o fim da gama é exclusivo, mesmo que a conta de armazenamento inclua um recipiente chamado *recipiente de amostra1,* apenas o recipiente chamado *recipiente de amostras* será restaurado:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Para especificar um subconjunto de bolhas num recipiente para restaurar, utilize uma barra dianteira (/) para separar o nome do recipiente do padrão de bolha. Por exemplo, a seguinte gama seleciona bolhas num único recipiente cujos nomes começam com as letras *d* através *de f:*

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Em seguida, forneça o alcance para o comando Restore-AzStorageBlobRange. Especifique o ponto de restauro fornecendo um valor UTC **DateTime** para o `-TimeToRestore` parâmetro. O exemplo seguinte restaura bolhas na gama especificada ao seu estado 3 dias antes do presente momento:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Restaurar várias gamas de bolhas de blocos

Para restaurar várias gamas de bolhas de blocos, especifique um conjunto de gamas para o `-BlobRestoreRange` parâmetro. O exemplo que se segue restaura o conteúdo completo do *contentor1* e do *contentor4:*

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>Passos seguintes

- [Restauro pontual para bolhas de bloco (pré-visualização)](point-in-time-restore-overview.md)
- [Eliminação recuperável](soft-delete-overview.md)
- [Alterar o feed (pré-visualização)](storage-blob-change-feed.md)
- [Versão blob (pré-visualização)](versioning-overview.md)
