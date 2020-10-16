---
title: Envie dados para hot, cold, archive blob tier via Azure Data Box/Azure Data Box Heavy
description: Descreve como usar a Caixa de Dados Azure ou Azure Data Box Heavy para enviar dados para um nível apropriado de armazenamento de blob de bloco, como quente, frio ou arquivo
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: a68793d893d8eb8de681eb438de39afc212370c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84608728"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Utilize a Caixa de Dados Azure ou caixa de dados Azure Heavy para enviar dados para o nível de blob de armazenamento Azure apropriado

A Azure Data Box move grandes quantidades de dados para a Azure enviando-lhe um dispositivo de armazenamento proprietário. Enche-se o dispositivo com dados e devolve-o. Os dados da Data Box são enviados para um nível predefinido associado à conta de armazenamento. Em seguida, pode mover os dados para outro nível de armazenamento.

Este artigo descreve como os dados que são enviados pela Data Box podem ser transferidos para um nível de blob quente, frio ou arquivado. Este artigo aplica-se tanto à Caixa de Dados Azure como à Caixa de Dados Azure Heavy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Escolha o nível de armazenamento correto para os seus dados

O armazenamento Azure permite que três níveis diferentes armazenem dados da forma mais rentável - Quente, Frio ou Arquivo. O nível de armazenamento quente é otimizado para armazenar dados que são acedidos frequentemente. O armazenamento quente tem custos de armazenamento mais elevados do que o armazenamento Cool e Archive, mas os custos de acesso mais baixos.

O nível de armazenamento fresco é para dados pouco acedidos que precisam de ser armazenados por um período mínimo de 30 dias. O custo de armazenamento para o nível frio é inferior ao do nível de armazenamento quente, mas os encargos de acesso a dados são elevados quando comparados com o nível quente.

O nível Azure Archive está offline e oferece os custos de armazenamento mais baixos, mas também os custos de acesso mais elevados. Este nível destina-se a dados que permanecem em armazenamento de arquivo por um mínimo de 180 dias. Para mais detalhes sobre cada um destes níveis e o modelo de preços, vá à [Comparação dos níveis de armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

Os dados da Caixa de Dados ou caixa de dados pesados são enviados para um nível de armazenamento que está associado à conta de armazenamento. Quando criar uma conta de armazenamento, pode especificar o nível de acesso como Quente ou Frio. Dependendo do padrão de acesso da sua carga de trabalho e do seu custo, pode mover estes dados do nível padrão para outro nível de armazenamento.

Só pode nivelar os dados de armazenamento de objetos nas contas Blob ou Finalidade Geral v2 (GPv2). As contas de Fins Gerais (GPv1) não suportam as camadas. Para escolher o nível de armazenamento correto para os seus dados, reveja as considerações detalhadas no [armazenamento Azure Blob: Níveis de armazenamento Premium, Hot, Cool e Archive](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

## <a name="set-a-default-blob-tier"></a>Descreva um nível de bolha padrão

O nível de bolha padrão é especificado quando a conta de armazenamento é criada no portal Azure. Uma vez selecionado um tipo de armazenamento como armazenamento de GPv2 ou Blob, então o atributo de nível access pode ser especificado. Por predefinição, o nível Hot é selecionado.

Os níveis não podem ser especificados se estiver a tentar criar uma nova conta ao encomendar uma Caixa de Dados ou Caixa de Dados Pesada. Após a criação da conta, pode modificar a conta no portal para definir o nível de acesso predefinido.

Em alternativa, cria-se primeiro uma conta de armazenamento com o atributo de nível de acesso especificado. Ao criar a caixa de dados ou a caixa de dados encomendada, selecione a conta de armazenamento existente. Para obter mais informações sobre como definir o nível de blob predefinido durante a criação de conta de armazenamento, vá para [Criar uma conta de armazenamento no portal Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Mover dados para um nível não-padrão

Uma vez que os dados do dispositivo Data Box são carregados para o nível predefinido, pode querer mover os dados para um nível não padrão. Há duas maneiras de mover estes dados para um nível não-padrão.

- **Gestão do ciclo de vida de armazenamento Azure Blob** - Pode utilizar uma abordagem baseada em políticas para eliminar automaticamente dados de nível ou expirar no final do seu ciclo de vida. Para mais informações, aceda ao [ciclo de vida de armazenamento Azure Blob](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts).
- **Scripting** - Você poderia usar uma abordagem scripted via Azure PowerShell para permitir o nível de nível de blob. Pode ligar para a `SetBlobTier` operação para definir o nível na bolha.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Use Azure PowerShell para definir o nível blob

Os passos seguintes descrevem como pode definir o nível blob para Archive usando um script Azure PowerShell.

1. Abra uma sessão elevada do Windows PowerShell. Certifique-se de que o seu powershell 5.0 ou superior. Escreva:

   `$PSVersionTable.PSVersion`     

2. Inscreva-se no Azure PowerShell. 

   `Login-AzAccount`  

3. Defina as variáveis para conta de armazenamento, chave de acesso, contentor e o contexto de armazenamento.

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

    Abaixo é mostrada uma saída de amostra:

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
   > Se quiser que os dados arquivam sobre a ingestão, desave o nível de conta predefinido para Hot. Se o nível predefinido for Cool, então existe uma penalização de eliminação antecipada de 30 dias se os dados forem imediatamente para Archive.

## <a name="next-steps"></a>Passos seguintes

-  Saiba como abordar os [cenários comuns de tiering de dados com regras de política do ciclo de vida](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples)

