---
title: Envie dados para hot, cold, archive blob tier via Azure Data Box/Azure Data Box Heavy
description: Descreve como utilizar a Caixa de Dados Azure ou a Caixa de Dados Azure Heavy para enviar dados para um nível de armazenamento de blocos apropriado, como quente, frio ou arquivo
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 31178284313ad7dafb094b109a75d4550cabd39c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560376"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Utilize a Caixa de Dados Azure ou a Caixa de Dados Azure Heavy para enviar dados para o nível de blob de armazenamento azure apropriado

A Azure Data Box transporta grandes quantidades de dados para o Azure, enviando-lhe um dispositivo de armazenamento proprietário. Enche-se o dispositivo com dados e devolve-o. Os dados da Data Box são enviados para um nível predefinido associado à conta de armazenamento. Em seguida, pode mover os dados para outro nível de armazenamento.

Este artigo descreve como os dados que são enviados pela Data Box podem ser movidos para um nível de blob Quente, Frio ou Arquivo. Este artigo aplica-se tanto à Caixa de Dados Azure como à Caixa de Dados Azure Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Escolha o nível de armazenamento correto para os seus dados

O armazenamento azure permite que três camadas diferentes armazenem dados da forma mais rentável - Hot, Cold ou Archive. O nível de armazenamento quente é otimizado para armazenar dados que são acedidos com frequência. O armazenamento em brasa tem custos de armazenamento mais elevados do que o armazenamento Cool e Archive, mas os custos de acesso mais baixos.

O nível de armazenamento cool é para dados pouco frequentemente acedidos que precisam de ser armazenados durante um mínimo de 30 dias. O custo de armazenamento para o nível frio é inferior ao do nível de armazenamento quente, mas os custos de acesso aos dados são elevados quando comparados com o nível Hot.

O nível Azure Archive está offline e oferece os custos de armazenamento mais baixos, mas também os custos de acesso mais elevados. Este nível destina-se a dados que permanecem em armazenamento de arquivo por um período mínimo de 180 dias. Para obter detalhes de cada um destes níveis e do modelo de preços, vá à [Comparação dos níveis de armazenamento.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)

Os dados da Caixa de Dados ou caixa de dados Heavy são enviados para um nível de armazenamento associado à conta de armazenamento. Quando criar uma conta de armazenamento, pode especificar o nível de acesso como Quente ou Frio. Dependendo do padrão de acesso da sua carga de trabalho e custo, pode mover estes dados do nível padrão para outro nível de armazenamento.

Só pode diferenciar os dados de armazenamento de objetos nas contas de armazenamento blob ou General Purpose v2 (GPv2). As contas de Fins Gerais (GPv1) não suportam as camadas. Para escolher o nível de armazenamento correto para os seus dados, reveja as considerações detalhadas no [armazenamento do Blob Azure: Premium, Hot, Cool e Archive.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)

## <a name="set-a-default-blob-tier"></a>Definir um nível de bolha padrão

O nível de bolha padrão é especificado quando a conta de armazenamento é criada no portal Azure. Uma vez selecionado um tipo de armazenamento como armazenamento GPv2 ou Blob, então o atributo do nível de acesso pode ser especificado. Por padrão, o nível Hot é selecionado.

Os níveis não podem ser especificados se estiver a tentar criar uma nova conta ao encomendar uma Caixa de Dados ou Caixa de Dados Pesada. Após a criação da conta, pode modificar a conta no portal para definir o nível de acesso predefinido.

Em alternativa, cria-se primeiro uma conta de armazenamento com o atributo do nível de acesso especificado. Ao criar a caixa de dados ou caixa de dados Encomenda pesada, selecione a conta de armazenamento existente. Para obter mais informações sobre como definir o nível de bolha padrão durante a criação de conta de armazenamento, vá para Criar uma conta de [armazenamento no portal Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Mover dados para um nível não predefinido

Uma vez que os dados do dispositivo Data Box sejam enviados para o nível predefinido, poderá querer mover os dados para um nível não predefinido. Existem duas formas de mover estes dados para um nível não padrão.

- Gestão do ciclo de vida do **armazenamento Azure Blob** - Pode utilizar uma abordagem baseada em políticas para escalonar automaticamente dados ou expirar no final do seu ciclo de vida. Para mais informações, vá à Gestão do ciclo de [vida de armazenamento De Armazenamento Azure Blob.](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts)
- **Scripting** - Você poderia usar uma abordagem scripted via Azure PowerShell para permitir o nivelamento de nível de blob. Pode ligar `SetBlobTier` para a operação para definir o nível na bolha.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Use o Azure PowerShell para definir o nível de bolha

Os passos seguintes descrevem como pode definir o nível de blob para Archive usando um script Azure PowerShell.

1. Abra uma sessão elevada do Windows PowerShell. Certifique-se de que o seu PowerShell 5.0 ou superior. Escreva:

   `$PSVersionTable.PSVersion`     

2. Assine no Azure PowerShell. 

   `Login-AzAccount`  

3. Defina as variáveis para a conta de armazenamento, chave de acesso, contentor e o contexto de armazenamento.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Pegue todas as bolhas no recipiente.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Coloque o nível de todas as bolhas no recipiente para o Arquivo.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Uma saída de amostra é mostrada abaixo:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Se pretender que os dados sejam arquivados sobre a ingestão, detete o nível de conta predefinido para Hot. Se o nível padrão for Cool, então existe uma penalização de 30 dias de eliminação antecipada se os dados forem imediatamente para o Arquivo.

## <a name="next-steps"></a>Passos seguintes

-  Saiba como abordar os [cenários comuns](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples) de tiering de dados com regras políticas de ciclo de vida

