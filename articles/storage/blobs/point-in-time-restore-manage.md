---
title: Execute uma restauração pontual em dados de blob de bloco
titleSuffix: Azure Storage
description: Aprenda a usar o ponto-a-tempo restaurar para restaurar um conjunto de bolhas de bloco para o seu estado anterior num dado momento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 828b5c34aaccf2a53aa197f921a8ef02d46821ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91280475"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Execute uma restauração pontual em dados de blob de bloco

Pode utilizar o restauro pontual para restaurar um ou mais conjuntos de bolhas de bloco para um estado anterior. Este artigo descreve como permitir a restauração pontual para uma conta de armazenamento e como executar uma operação de restauro.

Para saber mais sobre a restauração pontual, consulte [o restauro do ponto-a-tempo para as bolhas de blocos](point-in-time-restore-overview.md).

> [!CAUTION]
> O ponto-a-tempo restaura os suportes de restauração apenas em blobs de blocos. As operações em contentores não podem ser restauradas. Se eliminar um recipiente da conta de armazenamento chamando a operação [do Recipiente delete,](/rest/api/storageservices/delete-container) esse recipiente não pode ser restaurado com uma operação de restauro. Em vez de apagar um recipiente, elimine as bolhas individuais se desejar restaurá-las.

## <a name="enable-and-configure-point-in-time-restore"></a>Permitir e configurar o ponto-a-tempo restaurar

Antes de ativar e configurar a restauração pontual, ative os seus pré-requisitos para a conta de armazenamento: eliminação suave, alteração do feed e versão blob. Para obter mais informações sobre a ativação de cada uma destas funcionalidades, consulte estes artigos:

- [Ativar a eliminação recuperável para blobs](soft-delete-enable.md)
- [Ativar e desativar o feed de alteração](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Ativar e gerir a versão blob](versioning-enable.md)

> [!IMPORTANT]
> Permitir a eliminação suave, alterar o feed e a versão blob pode resultar em custos adicionais. Para obter mais informações, consulte [Soft delete para blobs](soft-delete-blob-overview.md), [Altere o suporte de alimentação em Azure Blob Storage](storage-blob-change-feed.md)e na versão [Blob](versioning-overview.md).

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para configurar o ponto-a-tempo restaurar com o portal Azure, siga estes passos:

1. Navegue para a sua conta de armazenamento no portal do Azure.
1. Em **Definições,** escolha **a Proteção de Dados**.
1. Selecione **Ligue o ponto no tempo** de restauração. Quando selecionar esta opção, a exclusão suave para bolhas, versões e feed de alteração também estão ativadas.
1. Desaponte o ponto máximo de restauro para a restauração pontual, em dias. Este número deve ser pelo menos um dia a menos do que o período de retenção especificado para a eliminação suave da bolha.
1. Guarde as alterações.

A imagem a seguir mostra uma conta de armazenamento configurada para restauro pontual com um ponto de restauração de há sete dias, e um período de retenção para a eliminação suave da bolha de 14 dias.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Screenshot mostrando como configurar o ponto-a-tempo restaurado no portal Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para configurar o ponto-a-tempo restaurar com o PowerShell, instale primeiro a versão 2.6.0 ou posterior do módulo [Az.Storage.](https://www.powershellgallery.com/packages/Az.Storage) Em seguida, ligue para o comando Enable-AzStorageBlobRestorePolicy para permitir a restauração pontual para a conta de armazenamento.

O exemplo a seguir permite a eliminação suave e define o período de retenção de eliminação suave, permite alterar o feed e a versão e, em seguida, permite a restauração pontual.    Ao executar o exemplo, lembre-se de substituir os valores em suportes angulares pelos seus próprios valores:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

---

## <a name="perform-a-restore-operation"></a>Realizar uma operação de restauro

Quando efetuar uma operação de restauro, deve especificar o ponto de restauro como um valor UTC **DateTime.** Os contentores e as bolhas serão restaurados no seu estado nesse dia e hora. A operação de restauro pode demorar vários minutos a ser concluída.

Pode restaurar todos os recipientes na conta de armazenamento, ou pode restaurar uma gama de bolhas em um ou mais recipientes. Uma gama de bolhas é definida lexicograficamente, o que significa em ordem do dicionário. Até dez gamas lexicográficas são suportadas por operação de restauro. O início da gama é inclusivo, e o fim da gama é exclusivo.

O padrão do recipiente especificado para a gama de início e a gama final deve incluir um mínimo de três caracteres. O corte dianteiro (/) que é utilizado para separar um nome de recipiente de um nome blob não conta para este mínimo.

Os caracteres wildcard não são suportados numa gama lexicográfica. Quaisquer caracteres wildcard são tratados como caracteres padrão.

Pode restaurar as bolhas nos `$root` contentores e `$web` contentores especificando-as explicitamente num intervalo passado para uma operação de restauro. Os `$root` `$web` contentores e os recipientes só são restaurados se forem explicitamente especificados. Outros recipientes do sistema não podem ser restaurados.

Apenas bolhas de blocos são restauradas. As bolhas de página e as bolhas de apêndice não estão incluídas numa operação de restauro. Para obter mais informações sobre limitações relacionadas com as bolhas dos apêndices, consulte [a restauração do ponto-a-tempo para as bolhas de bloco .](point-in-time-restore-overview.md)

> [!IMPORTANT]
> Quando executa uma operação de restauro, o Azure Storage bloqueia as operações de dados nas bolhas das gamas que estão a ser restauradas durante a operação. As operações de leitura, escrita e eliminação estão bloqueadas na localização primária. Por esta razão, as operações como a listagem de contentores no portal Azure podem não funcionar como esperado enquanto a operação de restauro estiver em curso.
>
> As operações de leitura a partir do local secundário podem prosseguir durante a operação de restauro se a conta de armazenamento for geo-replicada.

### <a name="restore-all-containers-in-the-account"></a>Restaurar todos os contentores na conta

Pode restaurar todos os recipientes na conta de armazenamento para devolvê-los ao estado anterior num dado momento.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para restaurar todos os recipientes e bolhas na conta de armazenamento com o portal Azure, siga estes passos:

1. Navegue para a lista de contentores para a sua conta de armazenamento.
1. Na barra de ferramentas, escolha **Restaurar os recipientes**e, em seguida, **restaurar tudo**.
1. No **painel Restaurar todos os recipientes, especifique** o ponto de restauro fornecendo uma data e hora.
1. Confirme se deseja proceder verificando a caixa.
1. **Selecione Restaurar** para iniciar a operação de restauro.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Screenshot mostrando como configurar o ponto-a-tempo restaurado no portal Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para restaurar todos os recipientes e bolhas na conta de armazenamento com o PowerShell, ligue para o comando **Restore-AzStorageBlobRange.** Por predefinição, o comando **Restore-AzStorageBlobRange** funciona assíncronamente e devolve um objeto do tipo **PSBlobRestoreStatus** que pode utilizar para verificar o estado da operação de restauro.

O exemplo a seguir restaura assíncronamente os contentores na conta de armazenamento para o seu estado 12 horas antes do presente momento, e verifica algumas das propriedades da operação de restauro:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Para executar a operação de restauro sincronizada, inclua o parâmetro **-WaitForComplete** no comando. Quando o parâmetro **-WaitForComplete** está presente, o PowerShell apresenta uma mensagem que inclui o ID de restauro para a operação e bloqueia a execução até que a operação de restauro esteja concluída. Tenha em mente que o tempo necessário para uma operação de restauro depende da quantidade de dados a restaurar, e uma grande operação de restauro pode demorar até uma hora para ser concluída.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

---

### <a name="restore-ranges-of-block-blobs"></a>Restaurar gamas de bolhas de blocos

Pode restaurar uma ou mais gamas lexicográficas de bolhas dentro de um único recipiente ou em vários recipientes para devolver essas bolhas ao seu estado anterior num dado momento.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para restaurar uma gama de bolhas em um ou mais recipientes com o portal Azure, siga estes passos:

1. Navegue para a lista de contentores para a sua conta de armazenamento.
1. Selecione o recipiente ou recipientes para restaurar.
1. Na barra de ferramentas, escolha **Restaurar os recipientes**e, em seguida, restaurar os **selecionados**.
1. No painel de **recipientes selecionados Restaurar, especifique** o ponto de restauro fornecendo uma data e hora.
1. Especifique os intervalos para restaurar. Utilize um corte para a frente (/) para delinear o nome do recipiente a partir do prefixo do blob.
1. Por predefinição, o painel de **recipientes selecionados Restaurar** especifica um intervalo que inclui todas as bolhas no recipiente. Elimine este alcance se não quiser restaurar todo o recipiente. O intervalo predefinido é mostrado na imagem seguinte.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Screenshot mostrando como configurar o ponto-a-tempo restaurado no portal Azure":::

1. Confirme se deseja proceder verificando a caixa.
1. **Selecione Restaurar** para iniciar a operação de restauro.

A imagem a seguir mostra uma operação de restauro num conjunto de intervalos.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Screenshot mostrando como configurar o ponto-a-tempo restaurado no portal Azure":::

A operação de restauro mostrada na imagem realiza as seguintes ações:

- Restaura o conteúdo completo do *contentor1*.
- Restaura bolhas na gama lexicográfica *blob1* através da *bolha5* em *recipiente2*. Esta gama restaura bolhas com nomes como *blob1,* *blob11,* *blob100,* *blob2*, e assim por diante. Como o fim da gama é exclusivo, restaura bolhas cujos nomes começam com *blob4,* mas não restaura blobs cujos nomes começam com *blob5*.
- Restaura todas as bolhas em *recipiente3* e *contentor4*. Como o fim da gama é exclusivo, esta gama não restaura *o contentor5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para restaurar uma única gama de bolhas, ligue para o comando **Restore-AzStorageBlobRange** e especifique uma gama lexicográfica de contentores e nomes blob para o `-BlobRestoreRange` parâmetro. Por exemplo, para restaurar as bolhas num único recipiente denominado *contentor1,* pode especificar uma gama que começa com *o contentor1* e termina com *o contentor2*. Não existe qualquer requisito para que os recipientes nomeados no início e nos intervalos finais existam. Uma vez que o fim da gama é exclusivo, mesmo que a conta de armazenamento inclua um contentor denominado *contentor2,* apenas o contentor denominado *contentor1* será restaurado:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Para especificar um subconjunto de bolhas num recipiente para restaurar, utilize um corte para a frente (/) para separar o nome do recipiente do padrão do prefixo blob. Por exemplo, a seguinte gama seleciona bolhas num único recipiente cujos nomes começam com as letras *d* através *de f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Em seguida, forneça o alcance para o comando **Restore-AzStorageBlobRange.** Especifique o ponto de restauro fornecendo um valor UTC **DateTime** para o `-TimeToRestore` parâmetro. O exemplo a seguir restaura as bolhas na gama especificada ao seu estado 3 dias antes do momento presente:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Por predefinição, o comando **Restore-AzStorageBlobRange** funciona assíncronamente. Quando inicia uma operação de restauro assíncronea, a PowerShell apresenta imediatamente uma tabela de propriedades para a operação:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Para restaurar várias gamas de bolhas de bloco, especifique uma matriz de intervalos para o `-BlobRestoreRange` parâmetro. O exemplo a seguir especifica duas gamas para restaurar o conteúdo completo do *contentor1* e *do contentor4* para o seu estado há 24 horas, e poupa o resultado a uma variável:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

Para executar a operação de restauro sincronizada e bloquear a execução até estar concluída, inclua o parâmetro **-WaitForComplete** no comando.

---

## <a name="next-steps"></a>Passos seguintes

- [Restauro pontual para bolhas de bloco](point-in-time-restore-overview.md)
- [Eliminação recuperável](soft-delete-overview.md)
- [Alterar alimentação](storage-blob-change-feed.md)
- [Versão blob](versioning-overview.md)
