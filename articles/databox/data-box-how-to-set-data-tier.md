---
title: Utilizar o Azure Data Box para enviar dados para a camada de blob de blocos - frequente, esporádica, arquivo - | Microsoft Docs nos dados
description: Descreve como utilizar o Azure Data Box para enviar dados para uma camada de armazenamento de BLOBs de bloco adequado, como acesso frequente, esporádico ou arquivo
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 01/10/2019
ms.author: alkohli
ms.openlocfilehash: dc84d5850656a0898fba328b652d86f3d8995bef
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58118379"
---
# <a name="use-azure-data-box-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Utilizar o Azure Data Box para enviar dados para a camada de blob de armazenamento do Azure adequada

O Azure Data Box move grandes quantidades de dados para o Azure ao enviar-lhe um dispositivo de armazenamento proprietárias. Preencha o dispositivo com dados e retorná-lo. Os dados de caixa de dados são carregados para um escalão padrão associado com a conta de armazenamento. Em seguida, pode mover os dados para outra camada de armazenamento.

Este artigo descreve como os dados que são carregados por caixa de dados podem ser movidos para uma camada de blob de acesso frequente, esporádico ou arquivo.  

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Escolha a camada de armazenamento correta para os seus dados

O armazenamento do Azure permite que as três camadas diferentes armazenar dados da maneira mais econômica – frequente, esporádico ou arquivo. Camada de armazenamento frequente está otimizada para armazenar dados que são acedidos com frequência. Armazenamento frequente tem custos de armazenamento superiores de acesso esporádico e arquivo de armazenamento, mas os custos de acesso mais baixos.

Camada de armazenamento esporádico destina-se com pouca frequência os dados acedidos que precisam de ser armazenados durante um período mínimo de 30 dias. O custo de armazenamento para o escalão de frio é menor do que a camada de armazenamento frequente, mas os custos de acesso de dados são alto quando comparado com o escalão de acesso frequente.

A camada de armazenamento do Azure está offline e oferece os custos de armazenamento mais baixos, mas também os custos de acesso a mais altos. Esta camada destina-se a dados que permanecem no armazenamento de arquivo por um mínimo de 180 dias. Para obter detalhes de cada uma dessas camadas e o modelo de preços, aceda a [comparação das camadas de armazenamento](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#comparison-of-the-storage-tiers).

Os dados da caixa de dados são carregados para uma camada de armazenamento que estão associada com a conta de armazenamento. Quando cria uma conta de armazenamento, pode especificar a camada de acesso como frequente ou esporádico. Consoante o padrão de acesso da sua carga de trabalho e o custo, pode mover estes dados da camada predefinida para uma camada de armazenamento.

Pode apenas da camada seus dados de armazenamento de objetos no armazenamento de BLOBs ou fins gerais v2 (GPv2) contas. As contas de Fins Gerais (GPv1) não suportam as camadas. Para escolher a camada de armazenamento correta para os seus dados, reveja as considerações detalhadas na [armazenamento de Blobs do Azure: Premium, as camadas de armazenamento frequente, esporádico e arquivo](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

## <a name="set-a-default-blob-tier"></a>Definir uma camada de blob predefinido

A camada de blob predefinido é especificada quando a conta de armazenamento é criada no portal do Azure. Assim que for selecionado um tipo de armazenamento como armazenamento de BLOBs ou GPv2, em seguida, o atributo do escalão de acesso pode ser especificado. Por predefinição, o escalão de acesso frequente está selecionado.

Os escalões não podem ser especificada se estiver a tentar criar uma nova conta ao solicitar uma caixa de dados. Depois da conta é criada, pode modificar a conta no portal para definir a camada de acesso predefinida.

Em alternativa, vai criar uma conta de armazenamento primeiro com o atributo do escalão de acesso especificado. Ao criar a encomenda do Data Box, selecione a conta de armazenamento existente. Para obter mais informações sobre como definir a camada de blob predefinida durante a criação de conta de armazenamento, aceda a [criar uma conta de armazenamento no portal do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Mover dados para um escalão de não-padrão

Depois dos dados de caixa de dados são carregados para a camada predefinida, pode querer mover os dados para um escalão de não-padrão. Existem duas formas de passar esses dados para um escalão de não-padrão.

- **A gestão de ciclo de vida de armazenamento de Blobs do Azure** -pode usar uma abordagem baseada em política para automaticamente dados de camada ou expirar no final do seu ciclo de vida. Para obter mais informações, aceda a [gerir o ciclo de vida de armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts).
- **Criação de scripts** -poderia usar uma abordagem com script através do Azure PowerShell para ativar a criação de camadas ao nível do blob. Pode chamar o `SetBlobTier` operação para definir a camada de blob.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>Utilizar o Azure PowerShell para definir a camada de blob

Passos seguintes descrevem como pode definir a camada de blob de arquivo com um script do PowerShell do Azure.

1. Abra uma sessão elevada do Windows PowerShell. Certifique-se de que sua execução PowerShell 5.0 ou posterior. Escreva:

   `$PSVersionTable.PSVersion`     

2. Inicie sessão no Azure PowerShell. 

   `Login-AzureRmAccount`  

3. Defina as variáveis para a conta de armazenamento, a chave de acesso, o contentor e o contexto de armazenamento.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Obter todos os blobs no contentor.

    `$blobs = Get-AzureStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Defina o escalão de todos os blobs no contentor para o arquivo.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Uma saída de exemplo é mostrada abaixo:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzureRmAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzureStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Se pretender que os dados para arquivar na ingestão, definir a camada predefinida para acesso frequente. Se a camada predefinida é útil, em seguida, há uma penalidade de eliminação antecipada de 30 dias se os dados são movidos para o arquivo imediatamente.

## <a name="next-steps"></a>Passos Seguintes

-  Saiba como abordar a [cenários camadas de dados comuns com regras de política de ciclo de vida](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples)

