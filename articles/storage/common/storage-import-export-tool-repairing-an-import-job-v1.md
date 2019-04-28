---
title: Reparação de uma tarefa de importação de importar/exportar do Azure - v1 | Documentos da Microsoft
description: Saiba como reparar uma tarefa de importação que foi criada e executada com o serviço importar/exportar do Azure.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: fda1d3d626c91ba984f08b96c79ab6a2fd2ec74b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477591"
---
# <a name="repairing-an-import-job"></a>Reparação de uma tarefa de importação
O serviço de importação/exportação do Microsoft Azure poderá não conseguir copiar alguns dos seus arquivos ou partes de um ficheiro para o serviço de Blobs do Windows Azure. Algumas razões para falhas incluem:  
  
-   Arquivos corrompidos  
  
-   Unidades danificadas  
  
-   A chave de conta de armazenamento alterada enquanto o arquivo estava a ser transferido.  
  
Pode executar a ferramenta de importação/exportação do Microsoft Azure com a importação ficheiros de registo de cópia da tarefa e a ferramenta carrega os ficheiros em falta (ou partes de um ficheiro) para sua conta de armazenamento do Windows Azure para concluir a tarefa de importação.  
  
## <a name="repairimport-parameters"></a>Parâmetros de RepairImport

Os seguintes parâmetros podem ser especificados com **RepairImport**: 
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**É necessário.** Caminho para o ficheiro de reparação, que controla o progresso do reparo e permite-lhe retomar uma reparação de interrompida. Cada unidade tem de ter apenas um ficheiro de reparação. Quando iniciar uma reparação de uma determinada unidade, transmita o caminho para um ficheiro de reparação, que ainda não existe. Para retomar uma reparação de interrompido, deve passar no nome de um ficheiro existente de reparação. Sempre é necessário especificar o ficheiro de reparação correspondente para a unidade de destino.|  
|**/logdir:**<LogDirectory\>|**Opcional.** O diretório de registo. Arquivos de log detalhado são gravados para este diretório. Se não for especificado nenhum diretório de registo, o diretório atual é utilizado como o diretório de registo.|  
|**/d:**<TargetDirectories\>|**É necessário.** Um ou mais separados por ponto e vírgula diretórios que contêm os arquivos originais que foram importados. A unidade de importação também pode ser utilizada, mas não é necessária se alternativo localizações de ficheiros originais estão disponíveis.|  
|**/bk:**<BitLockerKey\>|**Opcional.** Deve especificar a chave do BitLocker, se pretender que a ferramenta para desbloquear uma unidade encriptada, onde os arquivos originais estão disponíveis.|  
|**/sn:**<StorageAccountName\>|**É necessário.** O nome da conta de armazenamento para a tarefa de importação.|  
|**/sk:**<StorageAccountKey\>|**Necessário** se e apenas se não for especificado um SAS de contentor. A chave de conta para a conta de armazenamento para a tarefa de importação.|  
|**/csas:**<ContainerSas\>|**Necessário** se e apenas se a chave de conta de armazenamento não está especificado. O contentor de SAS para aceder aos blobs associados à tarefa de importação.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**É necessário.** Caminho para o ficheiro de registo de cópia de unidade (de qualquer log ou erro log detalhado). O ficheiro é gerado pelo serviço importar/exportar do Azure Windows e pode ser baixado do armazenamento de BLOBs associado à tarefa. O ficheiro de registo de cópia contém informações sobre blobs com falhas ou arquivos, que estão a ser reparado.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Opcional.** Caminho para um ficheiro de texto que pode ser utilizado para resolver ambiguidades se tiver vários ficheiros com o mesmo nome que foram importar na mesma tarefa. Na primeira vez que a ferramenta é executada, pode preencher este ficheiro com todos os nomes ambíguos. As execuções posteriores da ferramenta de utilizam este ficheiro para resolver a ambigüidade.|  
  
## <a name="using-the-repairimport-command"></a>Com o comando RepairImport  
Para reparar a importar dados por transmissão em fluxo os dados através da rede, tem de especificar os diretórios que contêm os arquivos originais que foram importar com o `/d` parâmetro. Também tem de especificar o ficheiro de registo de cópia que transferiu a partir da sua conta de armazenamento. Uma linha de comando típica para reparar uma tarefa de importação com falhas parciais é semelhante a:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
No exemplo a seguir um cópia do ficheiro de registo, uma parte de 64-K de um ficheiro estava danificada na unidade que foi lançada para a tarefa de importação. Como essa é a única falha indicada, o restante dos blobs na tarefa foram importados com êxito.  
  
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
  
Quando este registo de cópia é passado para a ferramenta de importação/exportação do Azure, a ferramenta tenta concluir a importação para este ficheiro ao copiar o conteúdo em falta em toda a rede. Seguir o exemplo acima, a ferramenta de procura o ficheiro original `\animals\koala.jpg` dentro de dois diretórios `C:\Users\bob\Pictures` e `X:\BobBackup\photos`. Se o ficheiro `C:\Users\bob\Pictures\animals\koala.jpg` existir, a ferramenta de importação/exportação do Azure copia o intervalo em falta de dados para o blob correspondente `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Resolver conflitos, ao utilizar RepairImport  
Em algumas situações, a ferramenta poderá não conseguir encontrar ou abrir o arquivo necessário para uma das seguintes razões: não foi possível encontrar o ficheiro, o ficheiro não está acessível, o nome de ficheiro é ambíguo ou o conteúdo do ficheiro já não está correto.  
  
Um erro ambíguo pode ocorrer se a ferramenta está a tentar localizar `\animals\koala.jpg` e não existe um ficheiro com esse nome em ambos `C:\Users\bob\pictures` e `X:\BobBackup\photos`. Ou seja, ambos `C:\Users\bob\pictures\animals\koala.jpg` e `X:\BobBackup\photos\animals\koala.jpg` existe nas unidades de tarefa de importação.  
  
O `/PathMapFile` opção permite-lhe resolver estes erros. Pode especificar o nome do arquivo, que contém a lista de ficheiros que a ferramenta não conseguiu identificar corretamente. Exemplo de linha de comando a seguir preenche `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
A ferramenta, em seguida, escreve-os caminhos de ficheiro problemático para `9WM35C2V_pathmap.txt`, um por linha. Por exemplo, o ficheiro pode conter as seguintes entradas depois de executar o comando:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Para cada ficheiro da lista, deve tentar localizar e abrir o ficheiro para garantir que está disponível para a ferramenta. Se quiser informar a ferramenta explicitamente, onde encontrar um ficheiro, pode modificar o ficheiro de mapa do caminho e adicione o caminho para cada ficheiro na mesma linha, separado por um caractere de tabulação:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Depois de disponibilizar os ficheiros necessários para a ferramenta ou atualizar o ficheiro de mapa do caminho, pode voltar a executar a ferramenta para concluir o processo de importação.  
  
## <a name="next-steps"></a>Passos Seguintes
 
* [Configurar a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
