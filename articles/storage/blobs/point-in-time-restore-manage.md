---
title: Ativar e gerir o restauro pontual para as bolhas de bloco (pré-visualização)
titleSuffix: Azure Storage
description: Aprenda a utilizar o ponto de restauração (pré-visualização) para restaurar as bolhas de bloqueio a um estado num ponto anterior no tempo.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: fe98e04c37172dc6b91c86fab8200022ed860d4f
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170108"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Ativar e gerir o restauro pontual para as bolhas de bloco (pré-visualização)

Pode utilizar o restauro pontual (pré-visualização) para restaurar as bolhas de bloqueio no seu estado num momento anterior. Este artigo descreve como permitir a restauração pontual para uma conta de armazenamento com PowerShell. Também mostra como executar uma operação de restauro com PowerShell.

Para obter mais informações e para aprender a registar-se para a pré-visualização, consulte [o restauro do ponto no tempo para as bolhas de bloco (pré-visualização)](point-in-time-restore-overview.md).

> [!CAUTION]
> O ponto-a-tempo restaura os suportes de restauração apenas em blobs de blocos. As operações em contentores não podem ser restauradas. Se eliminar um recipiente da conta de armazenamento chamando a operação [do Recipiente de Eliminação](/rest/api/storageservices/delete-container) durante a pré-visualização do restauro pontual, esse recipiente não pode ser restaurado com uma operação de restauro. Durante a pré-visualização, em vez de apagar um recipiente, elimine as bolhas individuais se desejar restaurá-las.

> [!IMPORTANT]
> A pré-visualização de restauro pontual destina-se apenas à utilização não-produção. Os contratos de serviços de produção (SLAs) não estão atualmente disponíveis.

## <a name="install-the-preview-module"></a>Instale o módulo de pré-visualização

Para configurar o ponto Azure no tempo de restauração com PowerShell, instale pela primeira vez a versão [1.14.1-pré-visualização](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) do módulo Az.Storage PowerShell. Siga estes passos para instalar o módulo de pré-visualização:

1. Desinstalar quaisquer instalações anteriores do Azure PowerShell a partir do Windows utilizando as **funcionalidades & aplicações** em **Definições**.

1. Certifique-se de que tem a versão mais recente do PowerShellGet instalada. Abra uma janela Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

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

Para obter mais informações sobre a instalação do Azure PowerShell, consulte [instalar a Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Permitir e configurar o ponto-a-tempo restaurar

Antes de ativar e configurar a restauração pontual, ative os seus pré-requisitos: eliminação suave, alteração do feed e versão blob. Para obter mais informações sobre a ativação de cada uma destas funcionalidades, consulte estes artigos:

- [Ativar a eliminação suave para bolhas](soft-delete-enable.md)
- [Ativar e desativar o feed de alteração](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Ativar e gerir a versão blob](versioning-enable.md)

Para configurar o ponto de azure no tempo com o PowerShell, ligue para o comando Enable-AzStorageBlobRestorePolicy. O exemplo a seguir permite a eliminação suave e define o período de retenção de eliminação suave, permite alterar o feed e, em seguida, permite a restauração pontual. Antes de executar o exemplo, utilize o portal Azure ou um modelo Azure Resource Manager para também permitir a versão blob.

Ao executar o exemplo, lembre-se de substituir os valores em suportes angulares pelos seus próprios valores:

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

Para iniciar uma operação de restauro, ligue para o comando Restore-AzStorageBlobRange, especificando o ponto de restauro como um valor UTC **DateTime.** Pode especificar gamas lexicográficas de bolhas para restaurar, ou omitir um intervalo para restaurar todas as bolhas em todos os recipientes na conta de armazenamento. Até 10 gamas lexicográficas são suportadas por operação de restauro. A operação de restauro pode demorar vários minutos a ser concluída.

Tenha em mente as seguintes regras ao especificar uma gama de bolhas para restaurar:

- O padrão do recipiente especificado para a gama de início e a gama final deve incluir um mínimo de três caracteres. O corte dianteiro (/) que é utilizado para separar um nome de recipiente de um nome blob não conta para este mínimo.
- Podem ser especificados até 10 intervalos por operação de restauro.
- Os caracteres wildcard não são suportados. São tratados como caracteres padrão.
- Pode restaurar as bolhas nos `$root` contentores e `$web` contentores especificando-as explicitamente num intervalo passado para uma operação de restauro. Os `$root` `$web` contentores e os recipientes só são restaurados se forem explicitamente especificados. Outros recipientes do sistema não podem ser restaurados.

> [!IMPORTANT]
> Quando executa uma operação de restauro, o Azure Storage bloqueia as operações de dados nas bolhas das gamas que estão a ser restauradas durante a operação. As operações de leitura, escrita e eliminação estão bloqueadas na localização primária. Por esta razão, as operações como a listagem de contentores no portal Azure podem não funcionar como esperado enquanto a operação de restauro estiver em curso.
>
> As operações de leitura a partir do local secundário podem prosseguir durante a operação de restauro se a conta de armazenamento for geo-replicada.

### <a name="restore-all-containers-in-the-account"></a>Restaurar todos os contentores na conta

Para restaurar todos os recipientes e bolhas na conta de armazenamento, ligue para o comando Restore-AzStorageBlobRange, omitindo o `-BlobRestoreRange` parâmetro. O exemplo a seguir restaura os contentores na conta de armazenamento ao seu estado 12 horas antes do momento presente:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Restaurar uma única gama de bolhas de blocos

Para restaurar uma gama de bolhas, ligue para o comando Restore-AzStorageBlobRange e especifique uma gama lexicográfica de contentores e nomes blob para o `-BlobRestoreRange` parâmetro. O início da gama é inclusivo, e o fim da gama é exclusivo.

Por exemplo, para restaurar as bolhas num único recipiente denominado *recipiente de amostras,* pode especificar uma gama que começa com *o recipiente de amostra* e termina com o recipiente de *amostra1*. Não existe qualquer requisito para que os recipientes nomeados no início e nos intervalos finais existam. Uma vez que o fim da gama é exclusivo, mesmo que a conta de armazenamento inclua um recipiente denominado *amostra-contentor1,* apenas o contentor denominado *recipiente com nome de amostra será* restaurado:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Para especificar um subconjunto de bolhas num recipiente para restaurar, utilize um corte para a frente (/) para separar o nome do recipiente do padrão blob. Por exemplo, a seguinte gama seleciona bolhas num único recipiente cujos nomes começam com as letras *d* através *de f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Em seguida, forneça o alcance para o comando Restore-AzStorageBlobRange. Especifique o ponto de restauro fornecendo um valor UTC **DateTime** para o `-TimeToRestore` parâmetro. O exemplo a seguir restaura as bolhas na gama especificada ao seu estado 3 dias antes do momento presente:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Restaurar várias gamas de bolhas de blocos

Para restaurar várias gamas de bolhas de bloco, especifique uma matriz de intervalos para o `-BlobRestoreRange` parâmetro. São suportadas até 10 gamas por operação de restauro. O exemplo a seguir especifica duas gamas para restabelecer o conteúdo completo dos *contentores1* e *dos contentores4:*

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>Próximos passos

- [Restauro pontual para bolhas de bloco (pré-visualização)](point-in-time-restore-overview.md)
- [Eliminação recuperável](soft-delete-overview.md)
- [Alterar feed (pré-visualização)](storage-blob-change-feed.md)
- [Veragem blob (pré-visualização)](versioning-overview.md)
