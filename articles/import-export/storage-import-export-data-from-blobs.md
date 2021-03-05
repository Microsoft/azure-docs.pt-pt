---
title: Utilizando a Azure Import/Export para exportar dados da Azure Blobs | Microsoft Docs
description: Saiba como criar empregos de exportação no portal Azure para transferir dados da Azure Blobs.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 03/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: e878be5351362923e163c0a6f617b96ab72a36d8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102177548"
---
# <a name="use-the-azure-importexport-service-to-export-data-from-azure-blob-storage"></a>Utilizar o serviço Importar/Exportar do Microsoft Azure para exportar dados do Armazenamento de blobs do Azure

Este artigo fornece instruções passo a passo sobre como utilizar o serviço Azure Import/Export para exportar de forma segura grandes quantidades de dados a partir do armazenamento da Azure Blob. O serviço requer que envie unidades vazias para o datacenter Azure. O serviço exporta dados da sua conta de armazenamento para as unidades e, em seguida, envia os discos de volta.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma tarefa de exportação para transferir dados para fora do Azure Blob Storage, reveja cuidadosamente e complete a seguinte lista de pré-requisitos para este serviço.
Tens de o fazer:

- Tenha uma subscrição ativa do Azure que pode ser usada para o serviço de importação/exportação.
- Tenha pelo menos uma conta de Armazenamento Azure. Consulte a lista de contas de [armazenamento suportadas e tipos de armazenamento para o serviço de importação/exportação.](storage-import-export-requirements.md) Para obter informações sobre a criação de uma nova conta de armazenamento, consulte [Como Criar uma Conta de Armazenamento.](../storage/common/storage-account-create.md)
- Dispor de um número adequado de discos de [tipos suportados.](storage-import-export-requirements.md#supported-disks)
- Tenha uma conta FedEx/DHL. Se pretender utilizar uma transportadora que não seja a FedEx/DHL, contacte a equipa de Operações da Caixa de Dados da Azure `adbops@microsoft.com` em .
  - A conta deve ser válida, deve ter equilíbrio, e deve ter capacidades de envio de retorno.
  - Gere um número de rastreio para o trabalho de exportação.
  - Cada tarefa deve ter um número de controlo separado. Não são apoiados várias tarefas com o mesmo número de controlo.
  - Se não tiver uma conta transportadora, vá a:
    - [Criar uma conta FedEx,](https://www.fedex.com/en-us/create-account.html)ou
    - [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>Passo 1: Criar um emprego de exportação

### <a name="portal"></a>[Portal](#tab/azure-portal)

Execute os seguintes passos para criar uma função de exportação no portal Azure.

1. Faça login em <https://portal.azure.com/> .
2. Procura de **postos de trabalho de importação/exportação.**

    ![Procura de postos de trabalho de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. Selecione **+ Novo**.

    ![Selecione + Novo para criar um novo ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. No **Básico:**

   1. Selecione uma subscrição.
   1. Selecione um grupo de recursos ou selecione **Criar novo** e criar um novo.
   1. Introduza um nome descritivo para o trabalho de importação. Use o nome para acompanhar o progresso dos seus trabalhos.
       * O nome pode conter apenas letras minúsculas, números e hífenes.
       * O nome deve começar com uma letra, e pode não conter espaços.

   1. Selecione **Exportação de Azure**.

    ![Opções básicas para uma encomenda de exportação](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    Selecione **Seguinte: Detalhes do trabalho >** prosseguir.

5. Em **detalhes de trabalho:**

   1. Selecione a região Azure onde os seus dados estão atualmente.
   1. Selecione a conta de armazenamento de onde pretende exportar dados. Utilize uma conta de armazenamento perto da sua localização.

      O local de entrega é automaticamente povoado com base na região da conta de armazenamento selecionada.

   1. Especifique os dados do blob para exportar da sua conta de armazenamento para a sua unidade ou unidades em branco. Escolha um dos três métodos seguintes.

      - Opte por **exportar todos os** dados blob na conta de armazenamento.

        ![Exportar tudo](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - Escolha **recipientes e bolhas selecionados** e especifique recipientes e bolhas para exportar. Pode utilizar mais do que um dos métodos de seleção. Selecionar uma opção **Adicionar** abre um painel à direita onde pode adicionar as suas cordas de seleção.

        |Opção|Descrição|
        |------|-----------|      
        |**Adicionar recipientes**|Exporte todas as bolhas num recipiente.<br>Selecione Adicione recipientes e **insira** o nome de cada recipiente.|
        |**Adicionar bolhas**|Especifique as bolhas individuais para exportar.<br>**Selecione Adicionar bolhas**. Em seguida, especifique o caminho relativo para a bolha, começando com o nome do recipiente. Utilize *$root* para especificar o recipiente de raiz.<br>Tem de fornecer os caminhos blob em formato válido para evitar erros durante o processamento, como mostra esta imagem. Para mais informações, consulte [exemplos de caminhos de bolhas válidos.](#examples-of-valid-blob-paths)|
        |**Adicionar prefixos**|Utilize um prefixo para selecionar um conjunto de recipientes com nome semelhante ou bolhas com nome semelhante num recipiente. O prefixo pode ser o prefixo do nome do recipiente, o nome completo do recipiente ou um nome completo do recipiente seguido do prefixo do nome blob. |

        ![Exportar recipientes e bolhas selecionados](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - Escolha **Exportar do ficheiro da lista blob (formato XML)** e selecione um ficheiro XML que contenha uma lista de caminhos e prefixos para que as bolhas sejam exportadas da conta de armazenamento. Deve construir o ficheiro XML e guardá-lo num recipiente para a conta de armazenamento. O ficheiro não pode estar vazio.

      > [!IMPORTANT]
      > Se utilizar um ficheiro XML para selecionar as bolhas para exportar, certifique-se de que o XML contém caminhos e/ou prefixos válidos. Se o ficheiro for inválido ou nenhum dado corresponder aos caminhos especificados, a encomenda termina com dados parciais ou sem dados exportados.

       Para ver como adicionar um ficheiro XML a um recipiente, consulte [a ordem de exportação utilizando o ficheiro XML](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file).

      ![Exportação a partir de ficheiro de lista de bolhas](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > Se uma bolha a exportar estiver a ser utilizada durante a cópia de dados, o serviço Azure Import/Export tira uma foto da bolha e copia o instantâneo.

   Selecione **Seguinte: Envio >** para prosseguir.

6. No **transporte** marítimo:

    - Selecione o transportador da lista de retirada. Se quiser utilizar uma transportadora diferente da FedEx/DHL, escolha uma opção existente a partir do dropdown. Contacte a equipa de Operações da Caixa de Dados da Azure `adbops@microsoft.com`  com as informações relativas à transportadora que pretende utilizar.
    - Introduza um número de conta transportadora válido que criou com essa transportadora. A Microsoft utiliza esta conta para enviar as unidades de volta para si assim que o seu trabalho de exportação estiver concluído.
    - Forneça um nome de contacto completo e válido, telefone, e-mail, endereço de rua, cidade, zip, estado/província e país/região.

        > [!TIP]
        > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe notificações mesmo que um administrador saia.

    Selecione **Review + criar** para prosseguir.

7. Em **Review + criar**:

   1. Reveja os detalhes do trabalho.
   1. Tome nota do nome do trabalho e disponibilize o endereço de envio do datacenter Azure para envio de discos para Azure.

      > [!NOTE]
      > Envie sempre os discos para o datacenter indicado no portal Azure. Se os discos forem enviados para o centro de dados errado, o trabalho não será processado.

   1. Reveja os **Termos** da sua encomenda para a eliminação de dados de privacidade e origem. Se concordar com os termos, selecione a caixa de verificação abaixo dos termos. A validação da ordem começa.

   ![Reveja e crie a sua encomenda de exportação](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. Após os passes de validação, **selecione Criar**.

<!--Replaced text: Steps 4 - end of "Create an export job." Wizard design changes required both screen and text updates.

4. In **Basics**:

    - Select **Export from Azure**.
    - Enter a descriptive name for the export job. Use the name you choose to track the progress of your jobs.
        - The name may contain only lowercase letters, numbers, hyphens, and underscores.
        - The name must start with a letter, and may not contain spaces.
    - Select a subscription.
    - Enter or select a resource group.

        ![Basics](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

5. In **Job details**:

    - Select the storage account where the data to be exported resides. Use a storage account close to where you are located.
    - The dropoff location is automatically populated based on the region of the storage account selected.
    - Specify the blob data you wish to export from your storage account to your blank drive or drives.
    - Choose to **Export all** blob data in the storage account.

         ![Export all](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

    - You can specify which containers and blobs to export.
        - **To specify a blob to export**: Use the **Equal To** selector. Specify the relative path to the blob, beginning with the container name. Use *$root* to specify the root container.
        - **To specify all blobs starting with a prefix**: Use the **Starts With** selector. Specify the prefix, beginning with a forward slash '/'. The prefix may be the prefix of the container name, the complete container name, or the complete container name followed by the prefix of the blob name. You must provide the blob paths in valid format to avoid errors during processing, as shown in this screenshot. For more information, see [Examples of valid blob paths](#examples-of-valid-blob-paths).

           ![Export selected containers and blobs](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - You can export from  the blob list file.

        ![Export from blob list file](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > If the blob to be exported is in use during data copy, Azure Import/Export service takes a snapshot of the blob and copies the snapshot.

6. In **Return shipping info**:

    - Select the carrier from the dropdown list. If you want to use a carrier other than FedEx/DHL, choose an existing option from the dropdown. Contact Azure Data Box Operations team at `adbops@microsoft.com`  with the information regarding the carrier you plan to use.
    - Enter a valid carrier account number that you have created with that carrier. Microsoft uses this account to ship the drives back to you once your export job is complete.
    - Provide a complete and valid contact name, phone, email, street address, city, zip, state/province, and country/region.

        > [!TIP]
        > Instead of specifying an email address for a single user, provide a group email. This ensures that you receive notifications even if an admin leaves.

7. In **Summary**:

    - Review the details of the job.
    - Make a note of the job name and provided Azure datacenter shipping address for shipping disks to Azure.

        > [!NOTE]
        > Always send the disks to the datacenter noted in the Azure portal. If the disks are shipped to the wrong datacenter, the job will not be processed.

    - Click **OK** to complete export job creation.
-->

### <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize os seguintes passos para criar uma tarefa de exportação no portal Azure.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>Criar uma tarefa

1. Utilize o comando [de adicionar extensão az](/cli/azure/extension#az_extension_add) para adicionar a extensão [de importação e exportação az:](/cli/azure/ext/import-export/import-export)

    ```azurecli
    az extension add --name import-export
    ```

1. Para obter uma lista dos locais a partir dos quais pode receber discos, utilize o comando [da lista de localização de importação e exportação az:](/cli/azure/ext/import-export/import-export/location#ext_import_export_az_import_export_location_list)

    ```azurecli
    az import-export location list
    ```

1. Executar o seguinte [az import-export criar](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_create) comando para criar uma tarefa de exportação que utilize a sua conta de armazenamento existente:

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe notificações mesmo que um administrador saia.

   Este trabalho exporta todas as bolhas na sua conta de armazenamento. Pode especificar uma bolha para exportação substituindo este valor para **--exportação**:

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   Este valor de parâmetro exporta a bolha nomeada *logo.bmp* no recipiente raiz.

   Também tem a opção de selecionar todas as bolhas num recipiente utilizando um prefixo. Substitua este valor por **--exportação:**

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   Para mais informações, consulte [exemplos de caminhos de bolhas válidos.](#examples-of-valid-blob-paths)

   > [!NOTE]
   > Se a bolha a exportar estiver a ser utilizada durante a cópia de dados, o serviço Azure Import/Export tira uma foto da bolha e copia o instantâneo.

1. Utilize o comando [da lista de importação e exportação az](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_list) para ver todos os postos de trabalho para o grupo de recursos myierg:

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. Para atualizar o seu trabalho ou cancelar o seu trabalho, executar o comando [az de atualização de importação e exportação:](/cli/azure/ext/import-export/import-export#ext_import_export_az_import_export_update)

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Utilize os seguintes passos para criar uma tarefa de exportação na Azure PowerShell.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> Enquanto o módulo **Az.ImportExport** PowerShell estiver em pré-visualização, deve instalá-lo separadamente utilizando o `Install-Module` cmdlet. Quando este módulo do PowerShell entrar em disponibilidade geral, fará parte das versões futuras do módulo Az PowerShell e estará disponível por predefinição a partir do Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>Criar uma tarefa

1. Para obter uma lista das localizações a partir das quais pode receber discos, utilize o cmdlet [Get-AzImportExportLocation:](/powershell/module/az.importexport/get-azimportexportlocation)

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. Executar o seguinte exemplo [new-AzImportExport](/powershell/module/az.importexport/new-azimportexport) para criar uma empresa de exportação que utilize a sua conta de armazenamento existente:

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
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
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe notificações mesmo que um administrador saia.

   Este trabalho exporta todas as bolhas na sua conta de armazenamento. Pode especificar uma bolha para exportação substituindo este valor por **-ExportBlobListblobPath**:

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   Este valor de parâmetro exporta a bolha nomeada *logo.bmp* no recipiente raiz.

   Também tem a opção de selecionar todas as bolhas num recipiente utilizando um prefixo. Substitua este valor por **-ExportBlobListblobPath:**

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   Para mais informações, consulte [exemplos de caminhos de bolhas válidos.](#examples-of-valid-blob-paths)

   > [!NOTE]
   > Se a bolha a exportar estiver a ser utilizada durante a cópia de dados, o serviço Azure Import/Export tira uma foto da bolha e copia o instantâneo.

1. Utilize o [cmdlet Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) para ver todos os trabalhos para o grupo de recursos myierg:

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. Para atualizar o seu trabalho ou cancelar o seu trabalho, execute o [cmdlet Update-AzImportExport:](/powershell/module/az.importexport/update-azimportexport)

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

<!--## (Optional) Step 2: -->

## <a name="step-2-ship-the-drives"></a>Passo 2: Envie as unidades

Se não souber o número de unidades necessárias, vá ao [Verificar o número de unidades](#check-the-number-of-drives). Se souber o número de unidades, dirija-se aos carros.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>Passo 3: Atualizar o trabalho com informações de rastreio

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>Passo 4: Receber os discos

Quando o painel de instrumentos informa que o trabalho está concluído, os discos são enviados para si e o número de rastreio do envio está disponível no portal.

1. Depois de receber as unidades com dados exportados, precisa de obter as teclas BitLocker para desbloquear as unidades. Vá para o trabalho de exportação no portal Azure. Clique no **separador Importação/Exportação.**
2. Selecione e clique no seu trabalho de exportação da lista. Vá à **Encriptação** e copie as chaves.

   ![Ver chaves BitLocker para trabalho de exportação](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. Utilize as teclas BitLocker para desbloquear os discos.

A exportação está completa.

## <a name="step-5-unlock-the-disks"></a>Passo 5: Desbloquear os discos

Utilize o seguinte comando para desbloquear a unidade:

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

Aqui está um exemplo da entrada da amostra.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

Neste momento, pode apagar o trabalho ou deixá-lo. Os empregos são automaticamente apagados após 90 dias.

## <a name="check-the-number-of-drives"></a>Verifique o número de unidades

Este passo *opcional* ajuda-o a determinar o número de unidades necessárias para o trabalho de exportação. Execute este passo num sistema Windows que executa uma [versão de SISTEMA Suportado](storage-import-export-requirements.md#supported-operating-systems).

1. [Descarregue a versão 1 do WAImportExport](https://www.microsoft.com/download/details.aspx?id=42659) no sistema Windows.
2. Desaperte a pasta predefinido `waimportexportv1` . Por exemplo, `C:\WaImportExportV1`.
3. Abra uma janela powerShell ou linha de comando com privilégios administrativos. Para alterar o diretório para a pasta desapertado, execute o seguinte comando:

   `cd C:\WaImportExportV1`

4. Para verificar o número de discos necessários para as bolhas selecionadas, verifique o seguinte comando:

   `WAImportExport.exe PreviewExport /sn:<Storage account name> /sk:<Storage account key> /ExportBlobListFile:<Path to XML blob list file> /DriveSize:<Size of drives used>`

    Os parâmetros são descritos no quadro seguinte:

    |Parâmetro de linha de comando|Descrição|
    |--------------------------|-----------------|
    |**/logdir:**|Opcional. O diretório de registos. Os ficheiros de registo verboso são escritos neste diretório. Se não for especificado, o diretório atual é utilizado como diretório de registo.|
    |**/sn:**|Obrigatório. O nome do armazém é responsável pelo trabalho de exportação.|
    |**/sk:**|Só é necessário se não for especificado um SAS de contentor. A conta-chave para o armazenamento é a conta do trabalho de exportação.|
    |**/csas:**|Só é necessário se não for especificada uma chave de conta de armazenamento. O contentor SAS para a listagem das bolhas a exportar na laboral de exportação.|
    |**/ExportBlobListFile:**|Obrigatório. Caminho para o ficheiro XML que contém lista de caminhos blob ou prefixos de caminhos blob para as bolhas a exportar. O formato de ficheiro utilizado no `BlobListBlobPath` elemento na operação [Put Job](/rest/api/storageimportexport/jobs) da API do serviço de importação/exportação REST.|
    |**/DriveSize:**|Obrigatório. O tamanho das unidades a utilizar para uma tarefa de exportação, *por exemplo,* 500 GB, 1,5 TB.|

    Consulte um [exemplo do comando PreviewExport](#example-of-previewexport-command).

5. Verifique se pode ler/escrever para as unidades que serão enviadas para o trabalho de exportação.

### <a name="example-of-previewexport-command"></a>Exemplo do comando PreviewExport

O exemplo a seguir demonstra o `PreviewExport` comando:

```powershell
    WAImportExport.exe PreviewExport /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /ExportBlobListFile:C:\WAImportExport\mybloblist.xml /DriveSize:500GB
```

O ficheiro da lista de bolhas de exportação pode conter nomes blob e prefixos blob, como mostrado aqui:

```xml
<?xml version="1.0" encoding="utf-8"?>
<BlobList>
<BlobPath>pictures/animals/koala.jpg</BlobPath>
<BlobPathPrefix>/vhds/</BlobPathPrefix>
<BlobPathPrefix>/movies/</BlobPathPrefix>
</BlobList>
```

A Ferramenta de Importação/Exportação Azure lista todas as bolhas a exportar e calcula como embalá-las em unidades do tamanho especificado, tendo em conta as despesas necessárias, estimando depois o número de unidades necessárias para manter as bolhas e impulsionar as informações de utilização.

Aqui está um exemplo da saída, com registos informativos omitidos:

```powershell
Number of unique blob paths/prefixes:   3
Number of duplicate blob paths/prefixes:        0
Number of nonexistent blob paths/prefixes:      1

Drive size:     500.00 GB
Number of blobs that can be exported:   6
Number of blobs that cannot be exported:        2
Number of drives needed:        3
        Drive #1:       blobs = 1, occupied space = 454.74 GB
        Drive #2:       blobs = 3, occupied space = 441.37 GB
        Drive #3:       blobs = 2, occupied space = 131.28 GB
```

## <a name="examples-of-valid-blob-paths"></a>Exemplos de caminhos de bolhas válidos

A tabela a seguir mostra exemplos de caminhos de bolhas válidos:

   | Seletor | Caminho da Bolha | Descrição |
   | --- | --- | --- |
   | Começa com |/ |Exporta todas as bolhas na conta de armazenamento |
   | Começa com |/$root/ |Exporta todas as bolhas no recipiente raiz |
   | Começa com |/livro |Exporta todas as bolhas em qualquer recipiente que comece com **livro** de prefixos |
   | Começa com |/música/ |Exporta todas as bolhas na **música** de contentores |
   | Começa com |/música/amor |Exporta todas as bolhas na **música** de recipiente que começam com **amor** prefixo |
   | Igual a |$root/logo.bmp |Exportações blob **logo.bmp** no recipiente raiz |
   | Igual a |vídeos/story.mp4 |Exportações blob **story.mp4** em **vídeos** de contentores |

## <a name="next-steps"></a>Passos seguintes

- [Ver o estado do trabalho e da unidade](storage-import-export-view-drive-status.md)
- [Rever os requisitos de importação/exportação](storage-import-export-requirements.md)
