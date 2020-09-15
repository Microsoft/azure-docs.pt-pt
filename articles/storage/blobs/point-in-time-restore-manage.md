---
title: Ativar e gerir o restauro pontual para as bolhas de bloco (pré-visualização)
titleSuffix: Azure Storage
description: Aprenda a utilizar o ponto de restauração (pré-visualização) para restaurar um conjunto de bolhas de bloco para um estado anterior.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 140e1203a29dcebec9d6483e73e906591b2213fb
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068522"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Ativar e gerir o restauro pontual para as bolhas de bloco (pré-visualização)

Pode utilizar o restauro pontual (pré-visualização) para restaurar um conjunto de bolhas de bloco para um estado anterior. Este artigo descreve como permitir a restauração pontual para uma conta de armazenamento com PowerShell. Também mostra como executar uma operação de restauro com PowerShell.

Para obter mais informações e para aprender a registar-se para a pré-visualização, consulte [o restauro do ponto no tempo para as bolhas de bloco (pré-visualização)](point-in-time-restore-overview.md).

> [!CAUTION]
> O ponto-a-tempo restaura os suportes de restauração apenas em blobs de blocos. As operações em contentores não podem ser restauradas. Se eliminar um recipiente da conta de armazenamento chamando a operação [do Recipiente de Eliminação](/rest/api/storageservices/delete-container) durante a pré-visualização do restauro pontual, esse recipiente não pode ser restaurado com uma operação de restauro. Durante a pré-visualização, em vez de apagar um recipiente, elimine as bolhas individuais se desejar restaurá-las.

> [!IMPORTANT]
> A pré-visualização de restauro pontual destina-se apenas à utilização não-produção.

## <a name="enable-and-configure-point-in-time-restore"></a>Permitir e configurar o ponto-a-tempo restaurar

Antes de ativar e configurar a restauração pontual, ative os seus pré-requisitos para a conta de armazenamento: eliminação suave, alteração do feed e versão blob. Para obter mais informações sobre a ativação de cada uma destas funcionalidades, consulte estes artigos:

- [Ativar a eliminação recuperável para blobs](soft-delete-enable.md)
- [Ativar e desativar o feed de alteração](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Ativar e gerir a versão blob](versioning-enable.md)

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

Para configurar o ponto-a-tempo restaurar com o PowerShell, instale primeiro a versão do módulo de pré-visualização Az.Storage 1.14.1 ou uma versão posterior do módulo de pré-visualização. Remova quaisquer outras versões do módulo Az.Storage.

Verifique se instalou a versão 2.2.4.1 ou posterior do PowerShellGet. Para determinar que versão instalou atualmente, execute o seguinte comando:

```powershell
Get-InstalledModule PowerShellGet
```

Em seguida, instale o módulo de pré-visualização Az.Storage. O seguinte comando instala a versão [2.5.2-pré-visualização](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview) do módulo Az.Storage:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
```

Para obter mais informações sobre a instalação do Azure PowerShell, consulte [instalar o PowerShellGet](/powershell/scripting/gallery/installing-psget) e [instalar o Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

Para configurar o ponto de azure no tempo com o PowerShell, ligue para o comando Enable-AzStorageBlobRestorePolicy. O exemplo a seguir permite a eliminação suave e define o período de retenção de eliminação suave, permite alterar o feed e, em seguida, permite a restauração pontual. Antes de executar o exemplo, utilize o portal Azure ou um modelo Azure Resource Manager para também permitir a versão blob.

Ao executar o exemplo, lembre-se de substituir os valores em suportes angulares pelos seus próprios valores:

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

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

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

Apenas bolhas de blocos são restauradas. As bolhas de página e as bolhas de apêndice não estão incluídas numa operação de restauro. Para obter mais informações sobre limitações relacionadas com as bolhas dos apêndices, consulte [questões conhecidas](#known-issues).

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

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Screenshot mostrando como restaurar todos os recipientes para um ponto de restauro especificado":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para restaurar todos os recipientes e bolhas na conta de armazenamento com o PowerShell, ligue para o comando **Restore-AzStorageBlobRange,** omitindo o `-BlobRestoreRange` parâmetro. O exemplo a seguir restaura os contentores na conta de armazenamento ao seu estado 12 horas antes do momento presente:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
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

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Screenshot mostrando a gama de blob padrão para eliminar antes de especificar a gama personalizada":::

1. Confirme se deseja proceder verificando a caixa.
1. **Selecione Restaurar** para iniciar a operação de restauro.

A imagem a seguir mostra uma operação de restauro num conjunto de intervalos.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Screenshot mostrando como restaurar gamas de bolhas em um ou mais recipientes":::

A operação de restauro mostrada na imagem realiza as seguintes ações:

- Restaura o conteúdo completo do *contentor1*.
- Restaura bolhas na gama lexicográfica *blob1* através da *bolha5* em *recipiente2*. Esta gama restaura bolhas com nomes como *blob1,* *blob11,* *blob100,* *blob2*, e assim por diante. Como o fim da gama é exclusivo, restaura bolhas cujos nomes começam com *blob4,* mas não restaura blobs cujos nomes começam com *blob5*.
- Restaura todas as bolhas em *recipiente3* e *contentor4*. Como o fim da gama é exclusivo, esta gama não restaura *o contentor5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para restaurar uma única gama de bolhas, ligue para o comando **Restore-AzStorageBlobRange** e especifique uma gama lexicográfica de contentores e nomes blob para o `-BlobRestoreRange` parâmetro. Por exemplo, para restaurar as bolhas num único recipiente denominado *recipiente de amostras,* pode especificar uma gama que começa com *o recipiente de amostra* e termina com o recipiente de *amostra1*. Não existe qualquer requisito para que os recipientes nomeados no início e nos intervalos finais existam. Uma vez que o fim da gama é exclusivo, mesmo que a conta de armazenamento inclua um recipiente denominado *amostra-contentor1,* apenas o contentor denominado *recipiente com nome de amostra será* restaurado:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container `
    -EndRange sample-container1
```

Para especificar um subconjunto de bolhas num recipiente para restaurar, utilize um corte para a frente (/) para separar o nome do recipiente do padrão do prefixo blob. Por exemplo, a seguinte gama seleciona bolhas num único recipiente cujos nomes começam com as letras *d* através *de f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d `
    -EndRange sample-container/g
```

Em seguida, forneça o alcance para o comando **Restore-AzStorageBlobRange.** Especifique o ponto de restauro fornecendo um valor UTC **DateTime** para o `-TimeToRestore` parâmetro. O exemplo a seguir restaura as bolhas na gama especificada ao seu estado 3 dias antes do momento presente:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Para restaurar várias gamas de bolhas de bloco, especifique uma matriz de intervalos para o `-BlobRestoreRange` parâmetro. O exemplo a seguir especifica duas gamas para restabelecer o conteúdo completo dos *contentores1* e *dos contentores4:*

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

---

### <a name="restore-block-blobs-asynchronously-with-powershell"></a>Restaurar blobs de bloco assíncronosamente com PowerShell

Para executar uma operação de restauro assíncrona, adicione o `-AsJob` parâmetro à chamada para **Restaurar-AzStorageBlobRange** e armazenar o resultado da chamada numa variável. O comando **Restore-AzStorageBlobRange** devolve um objeto do tipo **AzureLongRunningJob**. Pode verificar a propriedade **do Estado** deste objeto para determinar se a operação de restauro foi concluída. O valor da propriedade **do Estado** pode ser **Executar** ou **Concluído.**

O exemplo a seguir mostra como chamar uma operação de restauro assíncronea:

```powershell
$job = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-5) `
    -AsJob

# Check the state of the job.
$job.State
```

Para aguardar a conclusão da operação de restauro após o seu funcionamento, ligue para o comando [Wait-Job,](/powershell/module/microsoft.powershell.core/wait-job) como mostra o seguinte exemplo:

```powershell
$job | Wait-Job
```

## <a name="known-issues"></a>Problemas conhecidos

Para um subconjunto de operações de restauro onde estão presentes bolhas de apêndice, a operação de restauro falhará. A Microsoft recomenda que não efetue uma restauração pontual durante a pré-visualização se os blobs do apêndice estiverem presentes na conta.

## <a name="next-steps"></a>Passos seguintes

- [Restauro pontual para bolhas de bloco (pré-visualização)](point-in-time-restore-overview.md)
- [Excluir suave](soft-delete-overview.md)
- [Alterar feed (pré-visualização)](storage-blob-change-feed.md)
- [Versão blob](versioning-overview.md)
