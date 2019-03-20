---
title: A utilização de importar/exportar do Azure para transferir dados para ficheiros do Azure | Documentos da Microsoft
description: Aprenda a criar tarefas de importação no portal do Azure para transferir dados para ficheiros do Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 12/13/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: e3cd9d0036a55a3e6de49988dddcd6a91b81b078
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58088660"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Utilize o serviço importar/exportar do Azure para importar dados para ficheiros do Azure

Este artigo fornece instruções passo a passo sobre como utilizar o serviço importar/exportar do Azure para importar com segurança grandes quantidades de dados para ficheiros do Azure. Para importar dados, o serviço exige que envie suportadas unidades de disco que contém os dados num Datacenter do Azure.  

O suporte do serviço de importação/exportação importa apenas dos ficheiros do Azure para o armazenamento do Azure. Exportar ficheiros do Azure não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar uma tarefa de importação para transferir dados para ficheiros do Azure, com cuidado reveja e conclua a lista seguinte de pré-requisitos. Tem de:

- Ter uma subscrição do Azure Active Directory para utilizar com o serviço importar/exportar.
- Ter, pelo menos, uma conta de armazenamento do Azure. Ver a lista de [contas de armazenamento e tipos de armazenamento suportadas para o serviço importar/exportar](storage-import-export-requirements.md). Para obter informações sobre como criar uma nova conta de armazenamento, consulte [como criar uma conta de armazenamento](storage-quickstart-create-account.md).
- Têm um número adequado de discos [tipos suportados](storage-import-export-requirements.md#supported-disks). 
- Ter um sistema Windows em execução uma [versão do SO suportado](storage-import-export-requirements.md#supported-operating-systems).
- [Baixe o versão 2 do WAImportExport](https://aka.ms/waiev2) no sistema Windows. Deszipe o para a pasta predefinida `waimportexport`. Por exemplo, `C:\WaImportExport`.
- Ter uma conta de FedEx/DHL. 
    - A conta tem de ser válida, deve ter o saldo e tem de ter capacidades de envio de devolução.
    - Gere um número de controlo para a tarefa de exportação.
    - Cada tarefa deve ter um número de controlo separado. Várias tarefas com o mesmo número de controlo não são suportadas.
    - Se não tiver uma conta da transportadora, aceda a:
        - [Criar uma conta de FedEX](https://www.fedex.com/en-us/create-account.html), ou 
        - [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).
 


## <a name="step-1-prepare-the-drives"></a>Passo 1: Preparar as unidades

Este passo gera um ficheiro de diário. O ficheiro de diário armazena informações básicas, tais como o número de série da unidade, chave de encriptação e detalhes da conta de armazenamento.

Execute os seguintes passos para preparar as unidades.

1. Ligue-se nosso unidades de disco para o sistema do Windows através de conectores SATA.
2. Crie um único volume NTFS em cada unidade. Atribua uma letra de unidade ao volume. Não utilize mountpoints.
3. Modificar a *dataset.csv* ficheiro na pasta raiz onde a ferramenta reside. Dependendo se deseja importar um ficheiro ou pasta ou ambos, adicionar entradas no *dataset.csv* de ficheiros semelhantes aos exemplos seguintes.  

   - **Para importar um ficheiro**: No exemplo a seguir, os dados a copiar residem na unidade c:. O ficheiro *MyFile1.txt* é copiado para a raiz do *MyAzureFileshare1*. Se o *MyAzureFileshare1* não existir, é criado na conta de armazenamento do Azure. Estrutura de pastas é mantida.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
       ```
   - **Para importar uma pasta**: Todos os ficheiros e pastas sob *MyFolder2* são copiado para a partilha de ficheiros de recursivamente. Estrutura de pastas é mantida.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
       ```
     Várias entradas podem ser feitas no mesmo ficheiro correspondente pastas ou ficheiros que são importados. 

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
                        
       ```
     Saiba mais sobre [preparar o ficheiro CSV de conjunto de dados](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    

4. Modificar a *driveset.csv* ficheiro na pasta raiz onde a ferramenta reside. Adicionar entradas no *driveset.csv* de ficheiros semelhantes aos exemplos seguintes. O ficheiro de driveset tem a lista de discos e as letras de unidade correspondente, para que a ferramenta corretamente pode escolher a lista de discos para estar preparado.

    Este exemplo assume que dois discos estão anexados e volumes NTFS básicas G:\ e H:\ são criados. H:\is não encriptados quando g: já está encriptado. A ferramenta formata e encripta o disco que aloja o H:\ apenas (e não g:\).

   - **Para um disco que não está encriptado**: Especifique *Encrypt* para ativar a encriptação de disco BitLocker no disco.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```
    
   - **Para um disco que já é criptografado**: Especifique *AlreadyEncrypted* e fornecer a chave do BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Várias entradas podem ser feitas no mesmo ficheiro correspondente a várias unidades. Saiba mais sobre [preparar o ficheiro CSV de driveset](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file). 

5. Utilize o `PrepImport` opção para copiar e preparar dados para a unidade de disco. Para a primeira sessão de cópia copiar diretórios e/ou ficheiros com uma nova sessão de cópia, execute o seguinte comando:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Um exemplo de importação é mostrado abaixo.
  
       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```
 
6. Um ficheiro de diário com nome que forneceu com `/j:` parâmetro, é criada para cada execução da linha de comandos. Cada unidade de que preparar-se tem um ficheiro de diário que têm de ser carregado quando cria a tarefa de importação. Unidades sem diário de ficheiros não são processados.

    > [!IMPORTANT]
    > - Não modifique os dados em unidades de disco ou o ficheiro de diário depois de concluir a preparação do disco.

Para obter exemplos adicionais, aceda a [exemplos para ficheiros de diário](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Passo 2: Criar uma tarefa de importação 

Execute os seguintes passos para criar uma tarefa de importação no portal do Azure.
1. Inicie sessão no https://portal.azure.com/.
2. Aceda a **todos os serviços > armazenamento > tarefas de importação/exportação**. 

    ![Vá para importar/exportar](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Clique em **criar tarefa de importação/exportação**.

    ![Clique em tarefa de importação/exportação](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. Na **Noções básicas**:

    - Selecione **importar para o Azure**.
    - Introduza um nome descritivo para a tarefa de importação. Utilize este nome para controlar as tarefas enquanto estiverem em curso e depois que estejam concluídos.
        -  Este nome pode conter apenas letras minúsculas, números, hífenes e carateres de sublinhado.
        -  O nome tem de começar com uma letra e não pode conter espaços. 
    - Selecione uma subscrição.
    - Selecione um grupo de recursos. 

        ![Criar tarefa de importação - passo 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. Na **detalhes da tarefa**:
    
    - Carregue os ficheiros de diário que criou durante anterior [passo 1: Preparar as unidades](#step-1-prepare-the-drives). 
    - Selecione a conta de armazenamento que serão importados para os dados. 
    - A localização de redução é preenchida automaticamente com base na região da conta de armazenamento selecionada.
   
       ![Criar tarefa de importação - passo 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. Na **devolver informações sobre o envio**:

    - Selecione a deteção de carrier da lista pendente.
    - Introduza um número de conta de operadora válida que tenha criado com esse operadora. A Microsoft utiliza esta conta para enviar as unidades-se ao assim que a tarefa de importação estiver concluída. 
    - Forneça um nome de contato completo e válido, telefone, e-mail, rua, cidade, zip, estado/província e país/região.

        > [!TIP] 
        > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe as notificações, mesmo que sai de um administrador.

       ![Criar tarefa de importação - passo 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. Na **resumo**:

    - Forneça o endereço para envio discos novamente para o Azure de envio do datacenter do Azure. Certifique-se de que o nome da tarefa e o endereço completo são mencionadas na etiqueta de envio.
    - Clique em **OK** para concluir a criação da tarefa de importação.

        ![Criar tarefa de importação - passo 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Passo 3: Envie os discos para o datacenter do Azure 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Passo 4: Atualizar a tarefa com informações de registo

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Passo 5: Verificar o carregamento de dados para o Azure

Controle a tarefa até à conclusão. Quando a tarefa estiver concluída, certifique-se de que os seus dados tem carregado para o Azure. Elimine os dados no local apenas após ter verificado que o carregamento foi concluída com êxito.

## <a name="samples-for-journal-files"></a>Exemplos para ficheiros de diário

Para **adicionar unidades mais**, crie um novo ficheiro de driveset e execute o comando conforme mostrado a seguir. 

Para sessões de cópia subsequentes para as diferentes unidades de disco que o especificado na *InitialDriveset.-csv* do ficheiro, especifique um novo driveset *. csv* de arquivos e fornecê-la como um valor para o parâmetro `AdditionalDriveSet`. Utilize o **mesmo ficheiro de diário** dê um nome e fornecer um **ID de sessão novo**. O formato do ficheiro CSV de AdditionalDriveset é igual ao formato InitialDriveSet.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Um exemplo de importação é mostrado abaixo.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Para adicionar dados adicionais para o mesmo driveset, utilize o comando de PrepImport nas sessões subsequentes de cópia para copiar os ficheiros/diretório adicional.

Para sessões de cópia subsequentes para as unidades de disco rígido mesmo especificadas na *InitialDriveset.csv* do ficheiro, especifique a **mesmo ficheiro de diário** dê um nome e fornecer uma **novo ID de sessão**; Não é necessário para fornecer a chave de conta de armazenamento.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Um exemplo de importação é mostrado abaixo.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Passos Seguintes

* [Ver o estado de tarefa e a unidade](storage-import-export-view-drive-status.md)
* [Reveja os requisitos de importação/exportação](storage-import-export-requirements.md)


