---
title: Reparação de uma empresa de exportação Azure Import/Export - v1 ! Microsoft Docs
description: Saiba como reparar um trabalho de exportação que foi criado e executado utilizando o serviço Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/23/2017
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 67d1979ccbfbffc17ba450600e605a96911c8331
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056344"
---
# <a name="repairing-an-export-job"></a>Reparação de uma tarefa de exportação
Após a conclusão de uma missão de exportação, pode executar a Ferramenta de Importação/Exportação do Microsoft Azure no local para:  
  
1.  Faça o download de quaisquer ficheiros que o serviço Azure Import/Export não pôde exportar.  
  
2.  Valide que os ficheiros da unidade foram corretamente exportados.  
  
Você deve ter conectividade com o Azure Storage para usar esta funcionalidade.  
  
O comando para reparar um trabalho de importação é **a RepairExport.**

## <a name="repairexport-parameters"></a>ReparaçãoExport parâmetros

Os seguintes parâmetros podem ser especificados com **RepairExport:**  
  
|Parâmetro|Descrição|  
|---------------|-----------------|  
|**/r:<RepairFile\>**|Obrigatório. Caminho para o ficheiro de reparação, que acompanha o progresso da reparação, e permite-lhe retomar uma reparação interrompida. Cada unidade deve ter um e único ficheiro de reparação. Quando se inicia uma reparação para uma determinada unidade, passa-se o caminho para um ficheiro de reparação, que ainda não existe. Para retomar uma reparação interrompida, deverá passar em nome de um ficheiro de reparação existente. Especifique sempre o ficheiro de reparação correspondente à unidade do alvo.|  
|**/logdir:<LogDirectory\>**|Opcional. O diretório de registos. Os ficheiros de registo verbose serão escritos a este diretório. Se não for especificado nenhum diretório de registo, o diretório atual será utilizado como diretório de registo.|  
|**/d:<TargetDirectory\>**|Obrigatório. O diretório para validar e reparar. Este diretório é geralmente o diretório principal da unidade de exportação, mas também pode ser uma partilha de ficheiros de rede contendo uma cópia dos ficheiros exportados.|  
|**/bk:<BitLockerKey\>**|Opcional. Especifique a tecla BitLocker se pretender que a ferramenta desbloqueie um ficheiro encriptado onde os ficheiros exportados são armazenados.|  
|**/sn:<StorageAccountName\>**|Obrigatório. O nome do armazém é responsável pelo trabalho de exportação.|  
|**/sk:<StorageAccountKey\>**|**Obrigatório** se e somente se um contentor SAS não for especificado. A conta-chave para o armazenamento é a conta do trabalho de exportação.|  
|**/csas:<ContainerSas\>**|**Necessário** se e somente se a chave da conta de armazenamento não for especificada. O contentor SAS para acesso às bolhas associadas ao trabalho de exportação.|  
|**/CopyLogFile:<DriveCopyLogFile\>**|Obrigatório. O caminho para o ficheiro de registo de cópia de unidade. O ficheiro é gerado pelo serviço De importação/Exportação do Windows Azure e pode ser descarregado a partir do armazenamento de bolhas associado ao trabalho. O ficheiro de registo de cópias contém informações sobre bolhas ou ficheiros falhados que devem ser reparados.|  
|**/ManifestFile:<DriveManifestFile\>**|Opcional. O caminho para o ficheiro manifesto do disco de exportação. Este ficheiro é gerado pelo serviço De importação/Exportação do Windows Azure e armazenado na unidade de exportação. Opcionalmente, numa bolha na conta de armazenamento associada ao trabalho.<br /><br /> O conteúdo dos ficheiros sobre a unidade de exportação será verificado com os hashes MD5 contidos neste ficheiro. Quaisquer ficheiros corrompidos serão descarregados e reescritos para os directórios-alvo.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>Utilizar o modo RepairExport para corrigir as exportações falhadas  
Pode utilizar a Ferramenta de Importação/Exportação Azure para descarregar ficheiros que não conseguiram exportar. O ficheiro de registo de cópias conterá uma lista de ficheiros que não foram exportados.  
  
As causas das falhas de exportação incluem as seguintes possibilidades:  
  
-   Unidades danificadas  
  
-   A chave da conta de armazenamento alterada durante o processo de transferência  
  
Para executar a ferramenta no modo **RepairExport,** primeiro tem de ligar a unidade que contém os ficheiros exportados ao computador. Em seguida, executar a Ferramenta de Importação/Exportação Azure, especificando o caminho para essa unidade com o `/d` parâmetro. Também precisa de especificar o caminho para o ficheiro de registo de cópias da unidade que descarregou. O exemplo seguinte da linha de comando abaixo executa a ferramenta para reparar quaisquer ficheiros que não exportassem:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
O exemplo a seguir é um ficheiro de registo de cópia que mostra que um bloco na bolha não conseguiu exportar:  
  
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
  
O ficheiro de registo de cópia indica que ocorreu uma falha enquanto o serviço de importação/exportação do Windows Azure estava a descarregar um dos blocos da bolha para o ficheiro na unidade de exportação. Os outros componentes do ficheiro descarregados com sucesso, e o comprimento do ficheiro foi corretamente definido. Neste caso, a ferramenta abrirá o ficheiro na unidade, descarregará o bloco da conta de armazenamento e escreverá para a gama de ficheiros a partir do offset 65536 com comprimento 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>Utilização de RepairExport para validar conteúdos de unidade  
Também pode utilizar a Azure Import/Export com a opção **RepairExport** para validar o conteúdo da unidade. O ficheiro manifesto em cada unidade de exportação contém MD5s para o conteúdo da unidade.  
  
O serviço Azure Import/Export também pode guardar os ficheiros manifestos para uma conta de armazenamento durante o processo de exportação. A localização dos ficheiros manifestos está disponível através da operação [Get Job](/rest/api/storageimportexport/jobs) quando o trabalho estiver concluído. Para obter mais informações sobre o formato de um ficheiro manifesto de unidade, consulte [o formato manifesto do serviço de importação/exportação.](storage-import-export-file-format-metadata-and-properties.md)  
  
O exemplo a seguir mostra como executar a Ferramenta de Importação/Exportação Azure com os parâmetros **/ManifestFile** e **/CopyLogFile:**  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
O exemplo a seguir mostra um ficheiro manifesto:  
  
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
  
Após terminar o processo de reparação, a ferramenta irá ler cada ficheiro referenciado no ficheiro manifesto e verificar a integridade do ficheiro com os hashes MD5. Para o manifesto acima, passará pelos seguintes componentes.  

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

Qualquer componente que falhe a verificação será descarregado pela ferramenta e reescrito para o mesmo ficheiro na unidade.  
  
## <a name="next-steps"></a>Passos seguintes
 
* [Criação da ferramenta de importação/exportação Azure](storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de importação](storage-import-export-tool-repairing-an-import-job-v1.md)
