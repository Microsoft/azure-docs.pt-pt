---
title: Usando a importação/exportação do Azure para transferir dados para os arquivos do Azure | Microsoft Docs
description: Saiba como criar trabalhos de importação no portal do Azure para transferir dados para os arquivos do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 201d0c0a545c5ba7ae1bb0b5e119f7acb1ae362f
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029966"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Usar o serviço de importação/exportação do Azure para importar dados para arquivos do Azure

Este artigo fornece instruções passo a passo sobre como usar o serviço de importação/exportação do Azure para importar com segurança grandes quantidades de dados para arquivos do Azure. Para importar dados, o serviço exige que você envie unidades de disco com suporte contendo seus dados para um datacenter do Azure.  

O serviço de importação/exportação dá suporte apenas à importação de arquivos do Azure para o armazenamento do Azure. Não há suporte para a exportação de arquivos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um trabalho de importação para transferir dados para os arquivos do Azure, Examine cuidadosamente e conclua a lista de pré-requisitos a seguir. Você deve:

- Ter uma assinatura ativa do Azure para usar com o serviço de importação/exportação.
- Ter pelo menos uma conta de armazenamento do Azure. Consulte a lista de [contas de armazenamento e tipos de armazenamento com suporte para o serviço de importação/exportação](storage-import-export-requirements.md). Para obter informações sobre como criar uma nova conta de armazenamento, consulte [como criar uma conta de armazenamento](storage-account-create.md).
- Ter um número adequado de discos de [tipos com suporte](storage-import-export-requirements.md#supported-disks).
- Ter um sistema Windows executando uma [versão de sistema operacional com suporte](storage-import-export-requirements.md#supported-operating-systems).
- [Baixe a versão 2 do WAImportExport](https://aka.ms/waiev2) no sistema do Windows. Descompacte para a pasta padrão `waimportexport`. Por exemplo, `C:\WaImportExport`.
- Ter uma conta do FedEx/DHL. Se você quiser usar uma operadora diferente de FedEx/DHL, contate a equipe de operações Azure Data Box em `adbops@microsoft.com`.  
    - A conta deve ser válida, deve ter um saldo e deve ter recursos de envio de retorno.
    - Gerar um número de rastreamento para o trabalho de exportação.
    - Cada trabalho deve ter um número de controle separado. Não há suporte para vários trabalhos com o mesmo número de acompanhamento.
    - Se você não tiver uma conta da operadora, vá para:
        - [Criar uma conta do FedEx](https://www.fedex.com/en-us/create-account.html)ou
        - [Crie uma conta da DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>Etapa 1: preparar as unidades

Esta etapa gera um arquivo de diário. O arquivo de diário armazena informações básicas, como o número de série da unidade, a chave de criptografia e os detalhes da conta de armazenamento.

Execute as etapas a seguir para preparar as unidades.

1. Conecte nossas unidades de disco ao sistema Windows por meio de conectores SATA.
2. Crie um único volume NTFS em cada unidade. Atribua uma letra de unidade ao volume. Não use montagem.
3. Modifique o arquivo *DataSet. csv* na pasta raiz onde reside a ferramenta. Dependendo se você deseja importar um arquivo ou uma pasta ou ambos, adicione entradas no arquivo *DataSet. csv* semelhante aos exemplos a seguir.  

   - **Para importar um arquivo**: no exemplo a seguir, os dados a serem copiados residem na unidade F:. O arquivo *myarquivo1. txt* é copiado para a raiz do *MyAzureFileshare1*. Se o *MyAzureFileshare1* não existir, ele será criado na conta de armazenamento do Azure. A estrutura de pastas é mantida.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Para importar uma pasta**: todos os arquivos e pastas em *MyFolder2* são copiados recursivamente para FileShare. A estrutura de pastas é mantida.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Várias entradas podem ser feitas no mesmo arquivo correspondente a pastas ou arquivos que são importados.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Saiba mais sobre como [preparar o arquivo CSV de conjunto de](storage-import-export-tool-preparing-hard-drives-import.md)informações.


4. Modifique o arquivo *driveset. csv* na pasta raiz onde reside a ferramenta. Adicione entradas no arquivo *driveset. csv* semelhante aos exemplos a seguir. O arquivo driveset tem a lista de discos e as letras de unidade correspondentes para que a ferramenta possa selecionar corretamente a lista de discos a serem preparados.

    Este exemplo supõe que dois discos estejam anexados e volumes NTFS básicos G:\ e H:\ são criados. H:\is não criptografado enquanto G: já está criptografado. A ferramenta formata e criptografa o disco que hospeda H:\ somente (e não G:\).

   - **Para um disco que não está criptografado**: especifique *criptografar* para habilitar a criptografia BitLocker no disco.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Para um disco que já está criptografado**: especifique *AlreadyEncrypted* e forneça a chave do BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Várias entradas podem ser feitas no mesmo arquivo correspondente a várias unidades. Saiba mais sobre como [preparar o arquivo CSV do driveset](storage-import-export-tool-preparing-hard-drives-import.md).

5. Use a opção `PrepImport` para copiar e preparar dados para a unidade de disco. Para que a primeira sessão de cópia Copie diretórios e/ou arquivos com uma nova sessão de cópia, execute o seguinte comando:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Um exemplo de importação é mostrado abaixo.

       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```

6. Um arquivo de diário com o nome fornecido com `/j:` parâmetro é criado para cada execução da linha de comando. Cada unidade preparada tem um arquivo de diário que deve ser carregado quando você cria o trabalho de importação. Unidades sem arquivos de diário não são processadas.

    > [!IMPORTANT]
    > - Não modifique os dados nas unidades de disco ou no arquivo de diário depois de concluir a preparação do disco.

Para obter exemplos adicionais, vá para [exemplos de arquivos de diário](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Etapa 2: criar um trabalho de importação

Execute as etapas a seguir para criar um trabalho de importação no portal do Azure.
1. Faça logon no https://portal.azure.com/.
2. Vá para **todos os serviços > armazenamento > trabalhos de importação/exportação**.

    ![Ir para importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Clique em **criar trabalho de importação/exportação**.

    ![Clique em trabalho de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Em **noções básicas**:

    - Selecione **importar no Azure**.
    - Insira um nome descritivo para o trabalho de importação. Use esse nome para acompanhar seus trabalhos enquanto eles estiverem em andamento e quando forem concluídos.
        -  Esse nome pode conter apenas letras minúsculas, números, hifens e sublinhados.
        -  O nome deve começar com uma letra e não pode conter espaços.
    - Selecionar uma subscrição.
    - Selecione um grupo de recursos.

        ![Criar trabalho de importação – etapa 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. Em **detalhes do trabalho**:

    - Carregue os arquivos de diário que você criou durante a [etapa 1 anterior: preparar as unidades](#step-1-prepare-the-drives).
    - Selecione a conta de armazenamento na qual os dados serão importados.
    - O local chegada é preenchido automaticamente com base na região da conta de armazenamento selecionada.

       ![Criar trabalho de importação – etapa 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. Em **informações de envio de retorno**:

    - Selecione a transportadora na lista suspensa. Se você quiser usar uma operadora diferente de FedEx/DHL, escolha uma opção existente na lista suspensa. Contate a equipe de operações de Azure Data Box em `adbops@microsoft.com` com as informações sobre a transportadora que você planeja usar.
    - Insira um número de conta da transportadora válido que você criou com essa portadora. A Microsoft usa essa conta para enviar as unidades de volta para você quando o trabalho de importação for concluído.
    - Forneça um nome de contato completo e válido, telefone, email, endereço, cidade, CEP, estado/província e país/região.

        > [!TIP]
        > Em vez de especificar um endereço de email para um único usuário, forneça um email do grupo. Isso garante que você receba notificações mesmo que um administrador saia.

       ![Criar trabalho de importação – etapa 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. No **Resumo**:

    - Forneça o endereço de envio do datacenter do Azure para enviar discos de volta para o Azure. Verifique se o nome do trabalho e o endereço completo são mencionados no rótulo de envio.
    - Clique em **OK** para concluir a criação do trabalho de importação.

        ![Criar trabalho de importação – etapa 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Etapa 3: enviar as unidades para o datacenter do Azure

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Etapa 4: atualizar o trabalho com informações de rastreamento

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Etapa 5: verificar o carregamento de dados no Azure

Acompanhe a conclusão do trabalho. Quando o trabalho for concluído, verifique se seus dados foram carregados no Azure. Exclua os dados locais somente depois de verificar se o upload foi bem-sucedido.

## <a name="samples-for-journal-files"></a>Exemplos de arquivos de diário

Para **adicionar mais unidades**, crie um novo arquivo driveset e execute o comando como mostrado abaixo.

Para sessões de cópia subsequentes para diferentes unidades de disco do que o especificado no arquivo *InitialDriveset. csv* , especifique um novo arquivo driveset *. csv* e forneça-o como um valor para o parâmetro `AdditionalDriveSet`. Use o **mesmo** nome de arquivo de diário e forneça uma **nova ID de sessão**. O formato do arquivo CSV AdditionalDriveset é o mesmo que o formato InitialDriveSet.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Um exemplo de importação é mostrado abaixo.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Para adicionar dados adicionais ao mesmo driveset, use o comando Prepimport da para sessões de cópia subsequentes para copiar arquivos/diretórios adicionais.

Para sessões de cópia subsequentes para as mesmas unidades de disco rígido especificadas no arquivo *InitialDriveset. csv* , especifique o mesmo nome de **arquivo de diário** e forneça uma **nova ID de sessão**; Não é necessário fornecer a chave da conta de armazenamento.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Um exemplo de importação é mostrado abaixo.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Passos seguintes

* [Exibir o status do trabalho e da unidade](storage-import-export-view-drive-status.md)
* [Examinar os requisitos de importação/exportação](storage-import-export-requirements.md)
