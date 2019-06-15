---
title: Reparação de uma tarefa de exportação de importar/exportar do Azure - v1 | Documentos da Microsoft
description: Saiba como reparar uma tarefa de exportação que foi criada e executada com o serviço importar/exportar do Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 915cf1e66ec400e0d2461873d9fb3d66be9883fb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61477948"
---
# <a name="repairing-an-export-job"></a>Reparação de uma tarefa de exportação
Depois de uma tarefa de exportação foi concluída, pode executar a ferramenta de importação/exportação do Microsoft Azure no local para:  
  
1.  Baixe todos os ficheiros que não foi possível exportar o serviço importar/exportar do Azure.  
  
2.  Valide que os ficheiros na unidade corretamente foram exportados.  
  
Tem de ter conectividade ao armazenamento do Azure para utilizar esta funcionalidade.  
  
O comando de reparação de uma tarefa de importação é **RepairExport**.

## <a name="repairexport-parameters"></a>Parâmetros de RepairExport

Os seguintes parâmetros podem ser especificados com **RepairExport**:  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|**/r:<RepairFile\>**|Necessário. Caminho para o ficheiro de reparação, que controla o progresso do reparo e permite-lhe retomar uma reparação de interrompida. Cada unidade tem de ter apenas um ficheiro de reparação. Quando iniciar uma reparação de uma determinada unidade, passará no caminho para um ficheiro de reparação que ainda não existe. Para retomar uma reparação de interrompido, deve passar no nome de um ficheiro existente de reparação. Sempre é necessário especificar o ficheiro de reparação correspondente para a unidade de destino.|  
|**/logdir:<LogDirectory\>**|Opcional. O diretório de registo. Ficheiros de registo verboso serão escritos para este diretório. Se não for especificado nenhum diretório de registo, será utilizado o diretório atual como o diretório de registo.|  
|**/d:<TargetDirectory\>**|Necessário. O diretório para validar e reparar. Isto é normalmente o diretório de raiz da unidade de exportação, mas poderia também ser uma partilha de ficheiros de rede que contém uma cópia dos ficheiros exportados.|  
|**/bk:<BitLockerKey\>**|Opcional. Deve especificar a chave do BitLocker, se pretender que a ferramenta para desbloquear um encriptados onde estão armazenados os ficheiros exportados.|  
|**/sn:<StorageAccountName\>**|Necessário. O nome da conta de armazenamento para a tarefa de exportação.|  
|**/sk:<StorageAccountKey\>**|**Necessário** se e apenas se não for especificado um SAS de contentor. A chave de conta para a conta de armazenamento para a tarefa de exportação.|  
|**/csas:<ContainerSas\>**|**Necessário** se e apenas se a chave de conta de armazenamento não está especificado. O contentor de SAS para aceder aos blobs associados com a tarefa de exportação.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|Necessário. O caminho para o ficheiro de registo de cópia de unidade. O ficheiro é gerado pelo serviço importar/exportar do Azure Windows e pode ser baixado do armazenamento de BLOBs associado à tarefa. O ficheiro de registo de cópia contém informações sobre blobs com falhas ou ficheiros que estão a ser reparado.|  
|**/ManifestFile:<DriveManifestFile\>**|Opcional. O caminho para o ficheiro de manifesto a unidade de exportação. Este ficheiro é gerado pelo serviço importar/exportar do Azure Windows e armazenado na unidade de exportação e, opcionalmente, num blob na conta de armazenamento associado à tarefa.<br /><br /> O conteúdo dos ficheiros na unidade de exportação será verificado com os hashes MD5 contidos neste ficheiro. Todos os ficheiros que são determinados estar danificado serão transferidos e reescritos para os diretórios de destino.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Utilizar o modo de RepairExport para corrigir o exporta com falhas  
Pode utilizar a ferramenta de importação/exportação do Azure para transferir os ficheiros que falha ao exportar. O ficheiro de registo de cópia irá conter uma lista de ficheiros que falha ao exportar.  
  
As causas de falhas de exportação incluem as seguintes possibilidades:  
  
-   Unidades danificadas  
  
-   A chave de conta de armazenamento foi alterado durante o processo de transferência  
  
Para executar a ferramenta **RepairExport** modo, primeiro tem de se ligar a unidade que contém os ficheiros exportados para o seu computador. Em seguida, execute a ferramenta de importação/exportação do Azure, especificando o caminho para essa unidade com o `/d` parâmetro. Também tem de especificar o caminho do ficheiro de registo de cópia da unidade que transferiu. O exemplo de linha de comandos seguinte abaixo executa a ferramenta para reparar todos os ficheiros que falha ao exportar:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Segue-se um exemplo de um ficheiro de registo de cópia que mostra que um bloco no blob Falha ao exportar:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
O ficheiro de registo de cópia indica que ocorreu uma falha durante o serviço importar/exportar do Azure Windows foi baixar um dos bloco BLOBs de para o ficheiro na unidade de exportação. Os outros componentes do ficheiro transferido com êxito e o comprimento do ficheiro foi definido corretamente. Neste caso, a ferramenta irá abrir o ficheiro na unidade, transferir o bloco da conta de armazenamento e gravá-lo para o intervalo de ficheiro a partir de deslocamento 65536 com comprimento 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Uso RepairExport para validar o conteúdo da unidade  
Também pode utilizar Importar/exportar do Azure com o **RepairExport** opção para validar o conteúdo na unidade estão corretos. O ficheiro de manifesto em cada unidade de exportação contém MD5s para o conteúdo da unidade.  
  
O serviço importar/exportar do Azure também pode guardar os ficheiros de manifestos para uma conta de armazenamento durante o processo de exportação. A localização dos ficheiros de manifestos está disponível através da [Get Job de](/rest/api/storageimportexport/jobs) operação quando a tarefa for concluída. Ver [serviço importar/exportar formato de ficheiro de manifesto](storage-import-export-file-format-metadata-and-properties.md) para obter mais informações sobre o formato de um arquivo de manifesto de unidade.  
  
O exemplo seguinte mostra como executar a ferramenta de importação/exportação do Azure com o **/ManifestFile** e **/CopyLogFile** parâmetros:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Segue-se um exemplo de um arquivo de manifesto:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
Depois de concluir o processo de reparação, a ferramenta irá ler cada arquivo referenciado no arquivo de manifesto e verificar a integridade do ficheiro com os hashes MD5. Para o manifesto acima, esta irá passar pelos seguintes componentes.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Qualquer componente falhar a verificação será transferido pela ferramenta e reescrito para o mesmo ficheiro na unidade.  
  
## <a name="next-steps"></a>Passos Seguintes
 
* [Configurar a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de importação](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
