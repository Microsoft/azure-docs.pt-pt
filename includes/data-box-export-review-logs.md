---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 06/19/2020
ms.author: alkohli
ms.openlocfilehash: 526b3ad89e128d264b5d14d8cc87d9a81d431a9c
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86210469"
---
Antes de iniciar a cópia de dados:

1. Descarregue o registo de *cópias*. Na página **'Ligar e copiar',** selecione **Copy log**. Quando solicitado, guarde o registo no seu sistema. 

    ![Baixar registo de cópia 1](../articles/databox/media/data-box-deploy-export-copy-data/download-copy-log-1.png)

  
    Repita os passos para descarregar o *registo verbose*. 

2. Reveja o *registo verboso*. O *registo verboso* contém uma lista de todos os ficheiros que foram exportados com sucesso da conta de Armazenamento Azure. O registo também contém o tamanho do ficheiro e a computação checkum.

    ```powershell
    <File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3.4" Size="1024" crc64="7573843669953104266">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/helloEndWithDot..txt" Size="11" crc64="7320094093915972193">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/test..txt" Size="12" crc64="17906086011702236012">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/test1" Size="1024" crc64="7573843669953104266">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/test1.2.3" Size="1024" crc64="7573843669953104266">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/.......txt" Size="11" crc64="7320094093915972193">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/copylogb08fa3095564421bb550d775fff143ed====..txt" Size="53638" crc64="1147139997367113454">
    </File><File CloudFormat="BlockBlob" Path="validblobdata/testmaxChars-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-123456790-12345679" Size="1024" crc64="7573843669953104266">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/file0" Size="0" crc64="0">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/file1" Size="0" crc64="0">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000001" Size="4096" crc64="16969371397892565512">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/file4096_000000" Size="4096" crc64="16969371397892565512">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed10.dat" Size="65536" crc64="10746682179555216785">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/LiveSiteReport_Oct.xlsx" Size="7028" crc64="6103506546789189963">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/NE_Oct_GeoReport.xlsx" Size="103197" crc64="13305485882546035852">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/64KB-Seed1.dat" Size="65536" crc64="3140622834011462581">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-0" Size="1048576" crc64="16086591317856295272">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000001" Size="524288" crc64="8908547729214703832">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-0" Size="4194304" crc64="1339017920798612765">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/file524288_000000" Size="524288" crc64="8908547729214703832">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-1" Size="8388608" crc64="3963298606737216548">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/1mbfiles-0-1" Size="1048576" crc64="11061759121415905887">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/XLS-10MB.xls" Size="1199104" crc64="2218419493992437463">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/8mbfiles-0-0" Size="8388608" crc64="1072783424245035917">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/4mbfiles-0-1" Size="4194304" crc64="9991307204216370812">
    </File><File CloudFormat="BlockBlob" Path="export-ut-container/VL_Piracy_Negtive10_TPNameAndGCS.xlsx" Size="12398699" crc64="13526033021067702820">
    </File>
    ``````

3. Reveja o registo de *cópias* para eventuais erros. Este registo indica os ficheiros que não puderam ser copiados devido a erros.

    Aqui está uma saída de amostra de registo de cópia quando não houve erros e todos os ficheiros foram copiados durante a cópia de dados do dispositivo Azure para data box.

    ```powershell
    <CopyLog Summary="Summary">
      <Status>Succeeded</Status>
      <TotalFiles_Blobs>5521</TotalFiles_Blobs>
      <FilesErrored>0</FilesErrored>
    </CopyLog>
    ``` 
    Aqui está uma saída de amostra quando o registo de cópia sofre erros e alguns dos ficheiros não copiaram do Azure.

    ```powershell
    <ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/wastorage.v140.3.0.2.nupkg">
      <Category>UploadErrorCloudHttp</Category>
      <ErrorCode>400</ErrorCode>
      <ErrorMessage>UnsupportBlobType</ErrorMessage>
      <Type>File</Type>
    </ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444.hcsml">
      <Category>UploadErrorCloudHttp</Category>
      <ErrorCode>400</ErrorCode>
      <ErrorMessage>UnsupportBlobType</ErrorMessage>
      <Type>File</Type>
    </ErroredEntity><ErroredEntity CloudFormat="AppendBlob" Path="export-ut-appendblob/xunit.console.Primary_2020-05-07_03-54-42-PM_27444 (1).hcsml">
      <Category>UploadErrorCloudHttp</Category>
      <ErrorCode>400</ErrorCode>
      <ErrorMessage>UnsupportBlobType</ErrorMessage>
      <Type>File</Type>
    </ErroredEntity><CopyLog Summary="Summary">
      <Status>Failed</Status>
      <TotalFiles_Blobs>4</TotalFiles_Blobs>
      <FilesErrored>3</FilesErrored>
    </CopyLog>    
    ```

    Tem as seguintes opções para exportar esses ficheiros: 

    - Pode transferir os ficheiros que não puderam ser copiados pela rede. 
    - Se o seu tamanho de dados for maior do que a capacidade do dispositivo utilizável, então ocorre uma cópia parcial e todos os ficheiros que não foram copiados estão listados neste registo. Pode utilizar este registo como um XML de entrada para criar uma nova encomenda de Caixa de Dados e, em seguida, copiar sobre estes ficheiros.
