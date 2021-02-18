---
title: Utilizar a Azure Import/Export para transferir dados para a Azure Files | Microsoft Docs
description: Saiba como criar postos de trabalho no portal Azure para transferir dados para a Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 02/16/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 44473efbfb1c07c628c939fd05805ed92e691736
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651868"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Utilizar o serviço Importar/Exportar do Azure para importar dados para ficheiros do Azure

Este artigo fornece instruções passo a passo sobre como utilizar o serviço Azure Import/Export para importar de forma segura grandes quantidades de dados em Ficheiros Azure. Para importar dados, o serviço requer que envie unidades de disco suportadas contendo os seus dados para um datacenter Azure.

O serviço de importação/exportação suporta apenas a importação de Ficheiros Azure para o Armazenamento Azure. A exportação de Ficheiros Azure não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma função de importação para transferir dados para a Azure Files, reveja cuidadosamente e complete a seguinte lista de pré-requisitos. Tens de o fazer:

- Tenha uma subscrição ativa da Azure para utilizar com o serviço de importação/exportação.
- Tenha pelo menos uma conta de Armazenamento Azure. Consulte a lista de contas de [armazenamento suportadas e tipos de armazenamento para o serviço de importação/exportação.](storage-import-export-requirements.md) Para obter informações sobre a criação de uma nova conta de armazenamento, consulte [Como Criar uma Conta de Armazenamento.](../storage/common/storage-account-create.md)
- Dispor de um número adequado de discos de [tipos suportados.](storage-import-export-requirements.md#supported-disks)
- Dispor de um sistema Windows com uma [versão de SISTEMA Suportada.](storage-import-export-requirements.md#supported-operating-systems)
- [Descarregue a versão 2 do WAImportExport](https://aka.ms/waiev2) no sistema Windows. Desaperte a pasta predefinido `waimportexport` . Por exemplo, `C:\WaImportExport`.
- Tenha uma conta FedEx/DHL. Se pretender utilizar uma transportadora que não seja a FedEx/DHL, contacte a equipa de Operações da Caixa de Dados da Azure `adbops@microsoft.com` em .
    - A conta deve ser válida, deve ter equilíbrio, e deve ter capacidades de envio de retorno.
    - Gere um número de rastreio para o trabalho de exportação.
    - Cada tarefa deve ter um número de controlo separado. Não são apoiados várias tarefas com o mesmo número de controlo.
    - Se não tiver uma conta transportadora, vá a:
        - [Criar uma conta FedEx,](https://www.fedex.com/en-us/create-account.html)ou
        - [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>Passo 1: Preparar as unidades

Este passo gera um ficheiro de diário. O ficheiro do diário armazena informações básicas, tais como o número de série de unidade, a chave de encriptação e os detalhes da conta de armazenamento.

Execute os seguintes passos para preparar as unidades.

1. Ligue as nossas unidades de disco ao sistema Windows através de conectores SATA.
2. Crie um único volume NTFS em cada unidade. Atribua uma carta de unidade ao volume. Não utilize pontos de montagem.
3. Modifique o ficheiro *dataset.csv* na pasta raiz onde a ferramenta reside. Dependendo se pretende importar um ficheiro ou uma pasta ou ambos, adicione entradas no ficheiro *dataset.csv* semelhantes aos seguintes exemplos.

   - **Importar um ficheiro**: No seguinte exemplo, os dados a copiar residem no F: unidade. O seu ficheiro *MyFile1.txt*  é copiado para a raiz do *MyAzureFileshare1*. Se o *MyAzureFileshare1* não existir, é criado na conta Azure Storage. A estrutura da pasta mantém-se.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Para importar uma pasta**: Todos os ficheiros e pastas no *MyFolder2* são copiados novamente para o fileshare. A estrutura da pasta mantém-se.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Podem ser feitas múltiplas entradas no mesmo ficheiro correspondente a pastas ou ficheiros que são importados.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Saiba mais sobre [a preparação do ficheiro CSV do conjunto de dados](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).


4. Modifique o ficheiro *driveset.csv* na pasta raiz onde a ferramenta reside. Adicione entradas no ficheiro *driveset.csv* semelhante aos seguintes exemplos. O ficheiro driveset tem a lista de discos e letras de unidade correspondentes para que a ferramenta possa escolher corretamente a lista de discos a preparar.

    Este exemplo pressupõe que dois discos estão ligados e os volumes básicos de NTFS G:\ e H:\ são criados. H:\não é encriptado enquanto G: já está encriptado. Os formatos de ferramenta e encripta o disco que hospeda H:\ apenas (e não G: \) .

   - **Para um disco que não esteja encriptado**: Especifique *o encriptação* para ativar a encriptação do BitLocker no disco.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Para um disco já encriptado**: Especifique *já esclacorado* e forneça a tecla BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Podem ser feitas múltiplas entradas no mesmo ficheiro correspondente a múltiplas unidades. Saiba mais sobre [a preparação do ficheiro CSV driveset](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import).

5. Utilize a `PrepImport` opção para copiar e preparar dados para a unidade do disco. Para a primeira sessão de cópia para copiar diretórios e/ou ficheiros com uma nova sessão de cópia, execute o seguinte comando:

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   Um exemplo de importação é mostrado abaixo.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. Um ficheiro de diário com o nome que forneceu com `/j:` parâmetro, é criado para cada execução da linha de comando. Cada unidade que prepara tem um ficheiro de diário que deve ser carregado quando cria o trabalho de importação. As unidades sem ficheiros de diário não são processadas.

    > [!IMPORTANT]
    > - Não modifique os dados nas unidades do disco ou no ficheiro do diário após completar a preparação do disco.

Para obter amostras adicionais, aceda a [amostras para obter ficheiros de diário.](#samples-for-journal-files)

## <a name="step-2-create-an-import-job"></a>Passo 2: Criar um trabalho de importação

### <a name="portal"></a>[Portal](#tab/azure-portal)

Execute os seguintes passos para criar uma função de importação no portal Azure.
1. Faça login em https://portal.azure.com/ .
2. Procura de **postos de trabalho de importação/exportação.**

    ![Pesquisa de postos de trabalho de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Selecione **+ Novo**.

    ![Selecione Novo para criar um novo ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. No **Básico:**

    - Selecione **Import into Azure**.
    - Introduza um nome descritivo para o trabalho de importação. Use este nome para rastrear os seus trabalhos enquanto estão em curso e uma vez concluídos.
        -  Este nome pode conter apenas letras minúsculas, números, hífens e sublinhados.
        -  O nome deve começar com uma letra, e pode não conter espaços.
    - Selecione uma subscrição.
    - Selecione um grupo de recursos.

        ![Criar trabalho de importação - Passo 1](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

3. Em **detalhes de trabalho:**

    - Faça o upload dos ficheiros de diário que criou durante o [passo anterior 1: Prepare as unidades](#step-1-prepare-the-drives).
    - Selecione a conta de armazenamento para a quais os dados serão importados.
    - A localização de entrega é automaticamente povoada com base na região da conta de armazenamento selecionada.

       ![Criar trabalho de importação - Passo 2](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

4. Informações **de envio em troca:**

    - Selecione o transportador da lista de desistências. Se quiser utilizar uma transportadora diferente da FedEx/DHL, escolha uma opção existente a partir do dropdown. Contacte a equipa de Operações da Caixa de Dados da Azure `adbops@microsoft.com`  com as informações relativas à transportadora que pretende utilizar.
    - Introduza um número de conta transportadora válido que criou com essa transportadora. A Microsoft utiliza esta conta para enviar as unidades de volta para si assim que o seu trabalho de importação estiver concluído.
    - Forneça um nome de contacto completo e válido, telefone, e-mail, endereço de rua, cidade, zip, estado/província e país/região.

        > [!TIP]
        > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe notificações mesmo que um administrador saia.

       ![Criar trabalho de importação - Passo 3](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)


5. No **Resumo:**

    - Forneça o endereço de envio do datacenter Azure para o envio de discos de volta para Azure. Certifique-se de que o nome do trabalho e o endereço completo são mencionados na etiqueta de envio.
    - Clique **em OK** para concluir a criação de emprego de importação.

        ![Criar trabalho de importação - Passo 4](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize os seguintes passos para criar um trabalho de importação no Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Criar uma tarefa

1. Utilize o comando [de adicionar extensão az](/cli/azure/extension#az_extension_add) para adicionar a extensão [de importação e exportação az:](/cli/azure/ext/import-export/import-export)

    ```azurecli
    az extension add --name import-export
    ```

1. Pode utilizar um grupo de recursos existente ou criar um. Para criar um grupo de recursos, execute o comando [az group create](/cli/azure/group#az_group_create):

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. Pode utilizar uma conta de armazenamento existente ou criar uma. Para criar uma conta de armazenamento, executar a [conta de armazenamento az criar](/cli/azure/storage/account#az_storage_account_create) comando:

    ```azurecli
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
    ```

1. Para obter uma lista dos locais para os quais pode enviar discos, utilize o comando [da lista de localização de importação e exportação az:](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list)

    ```azurecli
    az import-export location list
    ```

1. Utilize o comando [de localização de importação e exportação az](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_show) para obter localizações para a sua região:

    ```azurecli
    az import-export location show --location "West US"
    ```

1. Executar os [seguintes az importação-exportação criar](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) comando para criar um trabalho de importação:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe notificações mesmo que um administrador saia.


1. Utilize o comando [da lista de importação e exportação az](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) para ver todos os postos de trabalho para o grupo de recursos myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Para atualizar o seu trabalho ou cancelar o seu trabalho, executar o comando [az de atualização de importação e exportação:](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update)

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Utilize os seguintes passos para criar um trabalho de importação na Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Enquanto o módulo **Az.ImportExport** PowerShell estiver em pré-visualização, deve instalá-lo separadamente utilizando o `Install-Module` cmdlet. Quando este módulo do PowerShell entrar em disponibilidade geral, fará parte das versões futuras do módulo Az PowerShell e estará disponível por predefinição a partir do Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Criar uma tarefa

1. Pode utilizar um grupo de recursos existente ou criar um. Para criar um grupo de recursos, executar o cmdlet [New-AzResourceGroup:](/powershell/module/az.resources/new-azresourcegroup)

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. Pode utilizar uma conta de armazenamento existente ou criar uma. Para criar uma conta de armazenamento, gere o [cmdlet New-AzStorageAccount:](/powershell/module/az.storage/new-azstorageaccount)

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. Para obter uma lista das localizações para as quais pode enviar discos, utilize o cmdlet [Get-AzImportExportLocation:](/powershell/module/az.importexport/get-azimportexportlocation)

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Use o `Get-AzImportExportLocation` cmdlet com o `Name` parâmetro para obter localizações para a sua região:

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. Executar o seguinte exemplo [new-AzImportExport](/powershell/module/az.importexport/new-azimportexport) para criar um trabalho de importação:

   ```azurepowershell-interactive
   $driveList = @(@{
     DriveId = '9CA995BA'
     BitLockerKey = '439675-460165-128202-905124-487224-524332-851649-442187'
     ManifestFile = '\\DriveManifest.xml'
     ManifestHash = '69512026C1E8D4401816A2E5B8D7420D'
     DriveHeaderHash = 'AZ31BGB1'
   })

   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'MyIEjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      DriveList = $driveList
      JobType = 'Import'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      ReturnShippingCarrierName = 'FedEx'
      ReturnShippingCarrierAccountNumber = '123456789'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

   > [!TIP]
   > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe notificações mesmo que um administrador saia.

1. Utilize o [cmdlet Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) para ver todos os trabalhos para o grupo de recursos myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Para atualizar o seu trabalho ou cancelar o seu trabalho, execute o [cmdlet Update-AzImportExport:](/powershell/module/az.importexport/update-azimportexport)

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Passo 3: Envie as unidades para o datacenter Azure

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Passo 4: Atualizar o trabalho com informações de rastreio

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Passo 5: Verificar o upload de dados para o Azure

Acompanhe o trabalho até à conclusão. Uma vez concluído o trabalho, verifique se os seus dados foram enviados para a Azure. Elimine os dados no local apenas depois de ter verificado que o upload foi bem sucedido.

## <a name="samples-for-journal-files"></a>Amostras para ficheiros de diários

Para **adicionar mais unidades,** crie um novo ficheiro driveset e execute o comando como abaixo.

Para sessões de cópia subsequentes às diferentes unidades de disco do que as especificadas no ficheiro *InitialDriveset .csv,* especifique um novo driveset *.csv* ficheiro e forneça-o como um valor para o parâmetro `AdditionalDriveSet` . Utilize o mesmo nome **de ficheiro do diário** e forneça um novo **ID de sessão**. O formato do ficheiro CSV AdicionalDriveset é o mesmo que o formato InitialDriveSet.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

Um exemplo de importação é mostrado abaixo.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


Para adicionar dados adicionais ao mesmo driveset, utilize o comando PrepImport para sessões de cópia subsequentes para copiar ficheiros/diretórios adicionais.

Para sessões de cópia subsequentes às mesmas unidades de disco rígido especificadas no ficheiro *InitialDriveset.csv,* especifique o mesmo nome **de ficheiro de diário** e forneça um novo **ID de sessão;** não há necessidade de fornecer a chave da conta de armazenamento.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

Um exemplo de importação é mostrado abaixo.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>Passos seguintes

* [Ver o estado do trabalho e da unidade](storage-import-export-view-drive-status.md)
* [Rever os requisitos de importação/exportação](storage-import-export-requirements.md)