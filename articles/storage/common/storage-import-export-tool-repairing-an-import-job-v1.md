---
title: Reparação de um trabalho de importação/exportação azure - v1 [ Microsoft Docs
description: Aprenda a reparar um trabalho de importação que foi criado e executado utilizando o serviço azure import/exportação.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: f5db321d8c4a6e42591a82b0ed8eb6bc6e93bad4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74973888"
---
# <a name="repairing-an-import-job"></a>Reparação de uma tarefa de importação
O serviço de importação/exportação do Microsoft Azure pode não copiar alguns dos seus ficheiros ou partes de um ficheiro para o serviço Windows Azure Blob. Algumas razões para falhas incluem:  
  
-   Ficheiros corrompidos  
  
-   Unidades danificadas  
  
-   A chave da conta de armazenamento mudou enquanto o ficheiro estava a ser transferido.  
  
Pode executar a Ferramenta de Importação/Exportação Microsoft Azure com os ficheiros de registo de cópias do trabalho de importação, e a ferramenta envia os ficheiros em falta (ou partes de um ficheiro) para a sua conta de armazenamento Windows Azure para completar o trabalho de importação.  
  
## <a name="repairimport-parameters"></a>Parâmetros de ReparaçãoImport

Os seguintes parâmetros podem ser especificados com **ReparaçãoImport:** 
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**Necessário.** Caminho para o ficheiro de reparação, que acompanha o progresso da reparação, e permite-lhe retomar uma reparação interrompida. Cada unidade deve ter um e único ficheiro de reparação. Quando iniciar uma reparação para uma determinada unidade, passe no caminho para um ficheiro de reparação, que ainda não existe. Para retomar uma reparação interrompida, deverá passar em nome de um ficheiro de reparação existente. O ficheiro de reparação correspondente à unidade-alvo deve ser sempre especificado.|  
|**/logdir:**<LogDirectory\>|**Opcional.** O diretório de registos. Os ficheiros de registo verbosos estão escritos para este diretório. Se não for especificado o diretório de registo, o diretório atual é utilizado como diretório de registo.|  
|**/d:**<TargetDirectories\>|**Necessário.** Um ou mais diretórios separados do ponto-e-vírgula que contêm os ficheiros originais que foram importados. A unidade de importação também pode ser utilizada, mas não é necessária se existirem localizações alternativas de ficheiros originais.|  
|**/bk:**<BitLockerKey\>|**Opcional.** Deve especificar a tecla BitLocker se pretender que a ferramenta desbloqueie uma unidade encriptada onde os ficheiros originais estão disponíveis.|  
|**/sn:**<StorageAccountName\>|**Necessário.** O nome da conta de armazenamento para o trabalho de importação.|  
|**/sk:**<StorageAccountKey\>|**Necessário** se e apenas se não for especificado um recipiente SAS. A chave da conta para a conta de armazenamento para o trabalho de importação.|  
|**/csas:**<ContainerSas\>|**Obrigatório** se e apenas se a chave da conta de armazenamento não for especificada. O recipiente SAS para aceder às bolhas associadas ao trabalho de importação.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Necessário.** Caminho para o ficheiro de registo de cópia de unidade (ou registo verboso ou registo de erros). O ficheiro é gerado pelo serviço de importação/exportação do Windows Azure e pode ser descarregado a partir do armazenamento blob associado ao trabalho. O ficheiro de registo de cópias contém informações sobre bolhas ou ficheiros falhados, que devem ser reparados.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Opcional.** Caminho para um ficheiro de texto que pode ser usado para resolver ambiguidades se tiver vários ficheiros com o mesmo nome que estava a importar no mesmo trabalho. A primeira vez que a ferramenta é executada, pode povoar este ficheiro com todos os nomes ambíguos. As execuções subsequentes da ferramenta utilizam este ficheiro para resolver as ambiguidades.|  
  
## <a name="using-the-repairimport-command"></a>Utilização do comando RepairImport  
Para reparar os dados de importação através do streaming dos dados através da rede, `/d` deve especificar os diretórios que contêm os ficheiros originais que estava a importar utilizando o parâmetro. Também deve especificar o ficheiro de registo de cópia saqueado da sua conta de armazenamento. Uma linha de comando típica para reparar um trabalho de importação com falhas parciais parece:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
No exemplo seguinte de um ficheiro de registo de cópia, um pedaço de um ficheiro de 64-K foi corrompido na unidade que foi enviada para o trabalho de importação. Uma vez que esta é a única falha indicada, o resto das bolhas no trabalho foram importadas com êxito.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Quando este registo de cópia supor para a Ferramenta de Importação/Exportação Azure, a ferramenta tenta terminar a importação para este ficheiro copiando os conteúdos em falta em toda a rede. Seguindo o exemplo acima, a ferramenta `\animals\koala.jpg` procura o `C:\Users\bob\Pictures` ficheiro `X:\BobBackup\photos`original dentro dos dois diretórios e . Se o `C:\Users\bob\Pictures\animals\koala.jpg` ficheiro existir, a Ferramenta de Importação/Exportação Azure `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`copia a gama de dados em falta para a bolha correspondente .  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Resolução de conflitos ao utilizar o RepairImport  
Em algumas situações, a ferramenta pode não ser capaz de encontrar ou abrir o ficheiro necessário por uma das seguintes razões: o ficheiro não foi encontrado, o ficheiro não está acessível, o nome do ficheiro é ambíguo ou o conteúdo do ficheiro já não está correto.  
  
Pode ocorrer um erro ambíguo se a ferramenta estiver a tentar localizar `\animals\koala.jpg` e houver um ficheiro com esse nome em ambos os `C:\Users\bob\pictures` e `X:\BobBackup\photos`. Ou seja, `C:\Users\bob\pictures\animals\koala.jpg` `X:\BobBackup\photos\animals\koala.jpg` ambos e existem nos postos de trabalho de importação.  
  
A `/PathMapFile` opção permite-lhe resolver estes erros. Pode especificar o nome do ficheiro, que contém a lista de ficheiros que a ferramenta não conseguiu identificar corretamente. O exemplo da linha `9WM35C2V_pathmap.txt`de comando que se segue preenche:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Em seguida, a ferramenta escreverá `9WM35C2V_pathmap.txt`os caminhos problemáticos dos ficheiros para, um em cada linha. Por exemplo, o ficheiro pode conter as seguintes entradas após a execução do comando:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Para cada ficheiro da lista, deve tentar localizar e abrir o ficheiro para garantir que está disponível para a ferramenta. Se desejar dizer explicitamente à ferramenta onde encontrar um ficheiro, pode modificar o ficheiro do mapa do caminho e adicionar o caminho a cada ficheiro na mesma linha, separado por um caracteres de separador:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Depois de disponibilizar os ficheiros necessários à ferramenta ou atualizar o ficheiro do mapa do caminho, pode reexecutar a ferramenta para completar o processo de importação.  
  
## <a name="next-steps"></a>Passos seguintes
 
* [Configuração da ferramenta de importação/exportação Azure](storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure](storage-import-export-tool-troubleshooting-v1.md)
