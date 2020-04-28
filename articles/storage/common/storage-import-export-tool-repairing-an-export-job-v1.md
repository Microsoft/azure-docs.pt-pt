---
title: Reparação de um trabalho de exportação de importação/exportação azure - v1 [ Microsoft Docs
description: Aprenda a reparar um trabalho de exportação que foi criado e executado utilizando o serviço azure import/exportação.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: b2ba30bddfc6364c79e1bb01d30cde63b261a07f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74978020"
---
# <a name="repairing-an-export-job"></a>Reparação de uma tarefa de exportação
Depois de concluído um trabalho de exportação, pode executar no local a Ferramenta de Importação/Exportação do Microsoft Azure:  
  
1.  Faça o download de quaisquer ficheiros que o serviço de importação/exportação azure não tenha podido exportar.  
  
2.  Valide que os ficheiros da unidade foram corretamente exportados.  
  
É necessário ter conectividade com o Armazenamento Azure para utilizar esta funcionalidade.  
  
O comando para reparar um trabalho de importação é **a RepairExport**.

## <a name="repairexport-parameters"></a>Parâmetros de reparaçãoExport

Os seguintes parâmetros podem ser especificados com **ReparaçãoExport:**  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|**/r:<RepairFile\>**|Necessário. Caminho para o ficheiro de reparação, que acompanha o progresso da reparação, e permite-lhe retomar uma reparação interrompida. Cada unidade deve ter um e único ficheiro de reparação. Quando iniciar uma reparação para uma determinada unidade, passará no caminho para um ficheiro de reparação que ainda não existe. Para retomar uma reparação interrompida, deverá passar em nome de um ficheiro de reparação existente. O ficheiro de reparação correspondente à unidade-alvo deve ser sempre especificado.|  
|**/logdir:<LogDirectory\>**|Opcional. O diretório de registos. Os ficheiros de registo verbosos serão escritos para este diretório. Se não for especificado o diretório de registo, o diretório atual será utilizado como diretório de registo.|  
|**/d:<TargetDirectory\>**|Necessário. O diretório para validar e reparar. Este é geralmente o principal diretório da unidade de exportação, mas também pode ser uma parte de ficheiro de rede contendo uma cópia dos ficheiros exportados.|  
|**/bk:<BitLockerKey\>**|Opcional. Deve especificar a tecla BitLocker se pretender que a ferramenta desbloqueie uma encriptada onde os ficheiros exportados são armazenados.|  
|**/sn:<StorageAccountName\>**|Necessário. O nome da conta de armazenamento para o trabalho de exportação.|  
|**/sk:<StorageAccountKey\>**|**Necessário** se e apenas se não for especificado um recipiente SAS. A chave da conta para a conta de armazenamento para o trabalho de exportação.|  
|**/csas:<Contentores\>**|**Obrigatório** se e apenas se a chave da conta de armazenamento não for especificada. O contentor SAS para aceder às bolhas associadas ao trabalho de exportação.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|Necessário. O caminho para o ficheiro de registo de cópia de unidade. O ficheiro é gerado pelo serviço de importação/exportação do Windows Azure e pode ser descarregado a partir do armazenamento blob associado ao trabalho. O ficheiro de registo de cópias contém informações sobre bolhas ou ficheiros falhados que devem ser reparados.|  
|**/ManifestoFile:<DriveManifestFile\>**|Opcional. O caminho para o manifesto da unidade de exportação. Este ficheiro é gerado pelo serviço de importação/exportação do Windows Azure e armazenado na unidade de exportação, e opcionalmente numa bolha na conta de armazenamento associada ao trabalho.<br /><br /> O conteúdo dos ficheiros na unidade de exportação será verificado com os hashes MD5 contidos neste ficheiro. Quaisquer ficheiros que estejam determinados a ser corrompidos serão descarregados e reescritos para os directórios-alvo.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Utilização do modo RepairExport para corrigir exportações falhadas  
Pode utilizar a Ferramenta de Importação/Exportação Azure para descarregar ficheiros que não exportaram. O ficheiro de registo de cópias conterá uma lista de ficheiros que não exportaram.  
  
As causas das falhas de exportação incluem as seguintes possibilidades:  
  
-   Unidades danificadas  
  
-   A chave da conta de armazenamento alterada durante o processo de transferência  
  
Para executar a ferramenta no modo **RepairExport,** primeiro é necessário ligar a unidade que contém os ficheiros exportados ao seu computador. Em seguida, executar a Ferramenta de Importação/Exportação Azure, especificando o caminho para essa unidade com o `/d` parâmetro. Também precisa especificar o caminho para o ficheiro de registo de cópias da unidade que descarregou. O exemplo da linha de comando abaixo segue a ferramenta para reparar quaisquer ficheiros que não exportaram:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Segue-se um exemplo de um ficheiro de registo de cópia sinuoso que mostra que um bloco na bolha não exportou:  
  
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
  
O ficheiro de registo de cópias indica que ocorreu uma falha enquanto o serviço de importação/exportação do Windows Azure estava a descarregar um dos blocos da bolha para o ficheiro da unidade de exportação. Os outros componentes do ficheiro foram descarregados com sucesso, e o comprimento do ficheiro foi corretamente definido. Neste caso, a ferramenta abrirá o ficheiro na unidade, descarregará o bloco a partir da conta de armazenamento e escreverá-o para a gama de ficheiros a partir do 65536 com o comprimento 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Utilização de ReparaçõesExport para validar conteúdos de unidade  
Também pode utilizar a Importação/Exportação Azure com a opção **RepairExport** para validar o conteúdo da unidade. O ficheiro manifesto de cada unidade de exportação contém MD5s para o conteúdo da unidade.  
  
O serviço de importação/exportação Azure também pode guardar os ficheiros manifestos para uma conta de armazenamento durante o processo de exportação. A localização dos ficheiros manifestos está disponível através da operação [Get Job](/rest/api/storageimportexport/jobs) quando o trabalho estiver concluído. Consulte o [serviço de importação/exportação Formato de ficheiro manifesto](storage-import-export-file-format-metadata-and-properties.md) para obter mais informações sobre o formato de um ficheiro manifesto de unidade.  
  
O exemplo seguinte mostra como executar a ferramenta de importação/exportação Azure com os parâmetros **/ManifestoFile** e **/CopyLogFile:**  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
Segue-se um exemplo de um ficheiro manifesto:  
  
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
  
Após terminar o processo de reparação, a ferramenta irá ler através de cada ficheiro referenciado no ficheiro manifesto e verificar a integridade do ficheiro com as hashes MD5. Para o manifesto acima, passará pelos seguintes componentes.  

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

Qualquer componente que falhe na verificação será descarregado pela ferramenta e reescrito para o mesmo ficheiro na unidade.  
  
## <a name="next-steps"></a>Passos seguintes
 
* [Configuração da ferramenta de importação/exportação Azure](storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de importação](storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure](storage-import-export-tool-troubleshooting-v1.md)
