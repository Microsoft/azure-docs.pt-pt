---
title: Utilização da Importação/Exportação Azure para transferir dados para ficheiros Azure [ Microsoft Docs
description: Saiba como criar postos de trabalho no portal Azure para transferir dados para ficheiros Azure.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 201d0c0a545c5ba7ae1bb0b5e119f7acb1ae362f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79268304"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Utilizar o serviço Importar/Exportar do Azure para importar dados para ficheiros do Azure

Este artigo fornece instruções passo a passo sobre como utilizar o serviço de importação/exportação Azure para importar de forma segura grandes quantidades de dados em Ficheiros Azure. Para importar dados, o serviço requer que envie discos suportados contendo os seus dados para um datacenter Azure.  

O serviço de importação/exportação apoia apenas a importação de ficheiros Azure para o armazenamento do Azure. A exportação de ficheiros Azure não é suportada.

## <a name="prerequisites"></a>Pré-requisitos

Antes de criar um trabalho de importação para transferir dados para Ficheiros Azure, reveja cuidadosamente e complete a seguinte lista de pré-requisitos. Tens de o fazer:

- Dispor de uma subscrição azure ativa para utilizar com o serviço import/exportação.
- Tenha pelo menos uma conta de Armazenamento Azure. Consulte a lista de contas de armazenamento suportadas e tipos de [armazenamento para o serviço de importação/exportação](storage-import-export-requirements.md). Para obter informações sobre a criação de uma nova conta de armazenamento, consulte [Como Criar uma Conta de Armazenamento](storage-account-create.md).
- Distenha um número adequado de discos de [tipos suportados](storage-import-export-requirements.md#supported-disks).
- Tenha um sistema Windows executando uma [versão Sistema Operativo suportada](storage-import-export-requirements.md#supported-operating-systems).
- Faça o download da versão 2 do [WAImportExport](https://aka.ms/waiev2) no sistema Windows. Desapertar para `waimportexport`a pasta predefinida . Por exemplo, `C:\WaImportExport`.
- Tenha uma conta FedEx/DHL. Se pretender utilizar uma transportadora que não a FedEx/DHL, `adbops@microsoft.com`contacte a equipa de Operações de Dados da Azure em .  
    - A conta deve ser válida, deve ter saldo, e deve ter capacidades de envio de retorno.
    - Gere um número de rastreio para o trabalho de exportação.
    - Cada trabalho deve ter um número de rastreamento separado. Não são apoiados múltiplos trabalhos com o mesmo número de rastreio.
    - Se não tiver uma conta de transportadora, vá a:
        - [Criar uma conta FedEX,](https://www.fedex.com/en-us/create-account.html)ou
        - [Criar uma conta DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).



## <a name="step-1-prepare-the-drives"></a>Passo 1: Preparar as unidades

Este passo gera um ficheiro de diário. O ficheiro do diário armazena informações básicas, tais como número de série de unidade, chave de encriptação e detalhes da conta de armazenamento.

Execute os seguintes passos para preparar as unidades.

1. Ligue as nossas unidades de disco ao sistema Windows através de conectores SATA.
2. Crie um único volume NTFS em cada unidade. Atribuir uma carta de condução ao volume. Não utilize pontos de montagem.
3. Modificar o ficheiro *dataset.csv* na pasta raiz onde a ferramenta reside. Dependendo se pretende importar um ficheiro ou pasta ou ambos, adicione entradas no ficheiro *dataset.csv* semelhante aos seguintes exemplos.  

   - **Para importar um ficheiro**: No seguinte exemplo, os dados a copiar residem no F: unidade. O seu ficheiro *MyFile1.txt* é copiado para a raiz do *MyAzureFileshare1*. Se o *MyAzureFileshare1* não existir, é criado na conta De armazenamento Azure. A estrutura da pasta mantém-se.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **Para importar uma pasta**: Todos os ficheiros e pastas do *MyFolder2* são copiados de forma recursiva para partilha de ficheiros. A estrutura da pasta mantém-se.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Várias entradas podem ser feitas no mesmo ficheiro correspondente a pastas ou ficheiros importados.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     Saiba mais sobre [a preparação do ficheiro CSV do conjunto de dados](storage-import-export-tool-preparing-hard-drives-import.md).


4. Modifique o ficheiro *driveset.csv* na pasta raiz onde a ferramenta reside. Adicione entradas no ficheiro *driveset.csv* semelhante aos seguintes exemplos. O ficheiro driveset tem a lista de discos e letras de acionamento correspondentes para que a ferramenta possa escolher corretamente a lista de discos a preparar.

    Este exemplo pressupõe que dois discos estão ligados e os volumes básicos de NTFS G:\ e H:\ são criados. H:\não é encriptado enquanto G: já está encriptado. Os formatos da ferramenta e encriptam o disco que acolhe H:\ apenas (e\)não G: .

   - **Para um disco que não esteja encriptado:** Especifique *o Encriptar* para ativar a encriptação BitLocker no disco.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **Para um disco já encriptado**: Especifique *já Encriptado* e forneça a tecla BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Várias entradas podem ser feitas no mesmo ficheiro correspondente a várias unidades. Saiba mais sobre [a preparação do ficheiro CSV do conjunto de acionamento](storage-import-export-tool-preparing-hard-drives-import.md).

5. Utilize `PrepImport` a opção de copiar e preparar dados para a unidade do disco. Para a primeira sessão de cópia sessão para copiar diretórios e/ou ficheiros com uma nova sessão de cópia, executar o seguinte comando:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   Um exemplo de importação é mostrado abaixo.

       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```

6. Um ficheiro de diário `/j:` com nome que forneceu com parâmetro, é criado para cada execução da linha de comando. Cada unidade que prepara tem um ficheiro de diário que deve ser carregado quando criar o trabalho de importação. As unidades sem ficheiros de diário não são processadas.

    > [!IMPORTANT]
    > - Não modifique os dados das unidades do disco ou do ficheiro do diário após completar a preparação do disco.

Para obter amostras adicionais, vá a [Amostras para ficheiros de diário](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Passo 2: Criar um emprego de importação

Execute os seguintes passos para criar um trabalho de importação no portal Azure.
1. Iniciar sessão para https://portal.azure.com/.
2. Vá a **Todos os serviços > armazenamento > empregos de importação/exportação.**

    ![Ir para importar/exportar](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Clique em **Criar Import/Exportar Trabalho**.

    ![Clique em importância/exportação](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. No **Básico:**

    - Selecione **Importar para Azure**.
    - Insira um nome descritivo para o trabalho de importação. Use este nome para acompanhar os seus trabalhos enquanto estão em andamento e uma vez concluídos.
        -  Este nome pode conter apenas letras minúsculas, números, hífenes e sublinhados.
        -  O nome deve começar com uma letra, e não pode conter espaços.
    - Selecione uma subscrição.
    - Selecione um grupo de recursos.

        ![Criar emprego de importação - Passo 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. Em **detalhes de trabalho:**

    - Faça upload dos ficheiros do diário que criou durante o [passo 1 anterior: Prepare as unidades](#step-1-prepare-the-drives).
    - Selecione a conta de armazenamento para a a que os dados serão importados.
    - O local de entrega é automaticamente povoado com base na região da conta de armazenamento selecionada.

       ![Criar emprego de importação - Passo 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. Em **Troca informações de envio:**

    - Selecione o porta-aviões da lista de lançamentos. Se quiser utilizar uma transportadora que não a FedEx/DHL, escolha uma opção existente a partir do dropdown. Contacte a equipa de `adbops@microsoft.com` Operações de Caixa de Dados azure com as informações relativas à transportadora que pretende utilizar.
    - Introduza um número de conta transportadora válido que criou com essa transportadora. A Microsoft usa esta conta para enviar as unidades de volta para si assim que o seu trabalho de importação estiver completo.
    - Forneça um nome de contacto completo e válido, telefone, e-mail, endereço de rua, cidade, zip, estado/província e país/região.

        > [!TIP]
        > Em vez de especificar um endereço de e-mail para um único utilizador, forneça um e-mail de grupo. Isto garante que recebe notificações mesmo que um administrador saia.

       ![Criar emprego de importação - Passo 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)


5. No **resumo:**

    - Forneça o endereço de envio do centro de dados Azure para o envio de discos de volta para O Azure. Certifique-se de que o nome do trabalho e o endereço completo são mencionados no rótulo de envio.
    - Clique **em OK** para concluir a criação de emprego de importação.

        ![Criar emprego de importação - Passo 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Passo 3: Envie as unidades para o centro de dados Azure

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Passo 4: Atualizar o trabalho com informações de rastreio

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Passo 5: Verificar o upload de dados para o Azure

Acompanhe o trabalho até à conclusão. Uma vez concluída a função, verifique se os seus dados foram enviados para o Azure. Apague os dados no local apenas depois de ter verificado que o upload foi bem sucedido.

## <a name="samples-for-journal-files"></a>Amostras para ficheiros de diários

Para **adicionar mais unidades,** crie um novo ficheiro driveset e execute o comando como abaixo.

Para sessões de cópia subsequentes às diferentes unidades de disco do que especificadas no ficheiro *InitialDriveset .csv,* especifique um novo ficheiro driveset *.csv* e forneça-o como um valor para o parâmetro `AdditionalDriveSet`. Use o mesmo nome de ficheiro de **diário** e forneça um novo ID de **sessão**. O formato do ficheiro CSV AdicionalDriveset é o mesmo que o formato InitialDriveSet.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

Um exemplo de importação é mostrado abaixo.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Para adicionar dados adicionais ao mesmo driveset, utilize o comando PrepImport para posteriores sessões de cópia para copiar ficheiros/diretórios adicionais.

Para sessões de cópia subsequentes às mesmas unidades de disco rígido especificadas no ficheiro *InitialDriveset.csv,* especifique o mesmo nome de ficheiro do **diário** e forneça um novo ID de **sessão;** não há necessidade de fornecer a chave da conta de armazenamento.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

Um exemplo de importação é mostrado abaixo.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Passos seguintes

* [Ver o estado do trabalho e da unidade](storage-import-export-view-drive-status.md)
* [Rever os requisitos de importação/exportação](storage-import-export-requirements.md)
