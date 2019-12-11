---
title: Reparando um trabalho de importação/exportação do Azure-v1 | Microsoft Docs
description: Saiba como reparar um trabalho de importação que foi criado e executado usando o serviço de importação/exportação do Azure.
author: twooley
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: f5db321d8c4a6e42591a82b0ed8eb6bc6e93bad4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973888"
---
# <a name="repairing-an-import-job"></a>Reparação de uma tarefa de importação
O serviço de Importação/Exportação do Microsoft Azure pode falhar ao copiar alguns dos arquivos ou partes de um arquivo para o serviço blob do Windows Azure. Alguns motivos para falhas incluem:  
  
-   Arquivos corrompidos  
  
-   Unidades danificadas  
  
-   A chave da conta de armazenamento foi alterada enquanto o arquivo estava sendo transferido.  
  
Você pode executar a ferramenta de Importação/Exportação do Microsoft Azure com os arquivos de log de cópia do trabalho de importação e a ferramenta carrega os arquivos ausentes (ou partes de um arquivo) em sua conta de armazenamento do Windows Azure para concluir o trabalho de importação.  
  
## <a name="repairimport-parameters"></a>Parâmetros de RepairImport

Os parâmetros a seguir podem ser especificados com **RepairImport**: 
  
|||  
|-|-|  
|**/r:** <RepairFile\>|**Obrigatório.** Caminho para o arquivo de reparo, que controla o progresso do reparo e permite que você retome um reparo interrompido. Cada unidade deve ter apenas um arquivo de reparo. Ao iniciar um reparo para uma determinada unidade, passe o caminho para um arquivo de reparo, que ainda não existe. Para retomar um reparo interrompido, você deve passar o nome de um arquivo de reparo existente. O arquivo de reparo correspondente à unidade de destino deve sempre ser especificado.|  
|**/logdir:** <LogDirectory\>|**Opcional.** O diretório de log. Os arquivos de log detalhados são gravados nesse diretório. Se nenhum diretório de log for especificado, o diretório atual será usado como o diretório de log.|  
|**/d:** <TargetDirectories\>|**Obrigatório.** Um ou mais diretórios separados por ponto e vírgula que contêm os arquivos originais que foram importados. A unidade de importação também pode ser usada, mas não será necessária se locais alternativos de arquivos originais estiverem disponíveis.|  
|**/bk:** <BitLockerKey\>|**Opcional.** Você deve especificar a chave do BitLocker se quiser que a ferramenta desbloqueie uma unidade criptografada onde os arquivos originais estão disponíveis.|  
|**/sn:** <StorageAccountName\>|**Obrigatório.** O nome da conta de armazenamento para o trabalho de importação.|  
|**/sk:** <StorageAccountKey\>|**Necessário** se e somente se uma SAS do contêiner não for especificada. A chave de conta da conta de armazenamento para o trabalho de importação.|  
|**/csas:** <ContainerSas\>|**Necessário** se e somente se a chave da conta de armazenamento não for especificada. A SAS do contêiner para acessar os BLOBs associados ao trabalho de importação.|  
|**/CopyLogFile:** <DriveCopyLogFile\>|**Obrigatório.** Caminho para o arquivo de log da cópia da unidade (log detalhado ou log de erros). O arquivo é gerado pelo serviço de importação/exportação do Windows Azure e pode ser baixado do armazenamento de blob associado ao trabalho. O arquivo de log de cópia contém informações sobre os arquivos ou BLOBs com falha que devem ser reparados.|  
|**/PathMapFile:** <DrivePathMapFile\>|**Opcional.** Caminho para um arquivo de texto que pode ser usado para resolver ambiguidades se você tiver vários arquivos com o mesmo nome que você estava importando no mesmo trabalho. Na primeira vez em que a ferramenta é executada, ela pode preencher esse arquivo com todos os nomes ambíguos. Execuções subsequentes da ferramenta usam esse arquivo para resolver as ambiguidades.|  
  
## <a name="using-the-repairimport-command"></a>Usando o comando RepairImport  
Para reparar os dados de importação transmitindo os dados pela rede, você deve especificar os diretórios que contêm os arquivos originais que você estava importando usando o parâmetro `/d`. Você também deve especificar o arquivo de log de cópia que você baixou de sua conta de armazenamento. Uma linha de comando típica para reparar um trabalho de importação com falhas parciais é semelhante a:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
No exemplo a seguir de um arquivo de log de cópia, 1 64-K parte de um arquivo foi corrompida na unidade que foi enviada para o trabalho de importação. Como essa é a única falha indicada, o restante dos BLOBs no trabalho foi importado com êxito.  
  
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
  
Quando esse log de cópia é passado para a ferramenta de importação/exportação do Azure, a ferramenta tenta concluir a importação para esse arquivo copiando o conteúdo ausente na rede. Seguindo o exemplo acima, a ferramenta procura o arquivo original `\animals\koala.jpg` nos dois diretórios `C:\Users\bob\Pictures` e `X:\BobBackup\photos`. Se o arquivo `C:\Users\bob\Pictures\animals\koala.jpg` existir, a ferramenta de importação/exportação do Azure copiará o intervalo de dados ausente para o `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg`de blob correspondente.  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Resolvendo conflitos ao usar o RepairImport  
Em algumas situações, a ferramenta pode não conseguir localizar ou abrir o arquivo necessário por um dos seguintes motivos: o arquivo não foi encontrado, o arquivo não está acessível, o nome do arquivo é ambíguo ou o conteúdo do arquivo não está mais correto.  
  
Um erro ambíguo pode ocorrer se a ferramenta estiver tentando localizar `\animals\koala.jpg` e houver um arquivo com esse nome em `C:\Users\bob\pictures` e `X:\BobBackup\photos`. Ou seja, os `C:\Users\bob\pictures\animals\koala.jpg` e `X:\BobBackup\photos\animals\koala.jpg` existem nas unidades de trabalho de importação.  
  
A opção `/PathMapFile` permite que você resolva esses erros. Você pode especificar o nome do arquivo, que contém a lista de arquivos que a ferramenta não conseguiu identificar corretamente. O exemplo de linha de comando a seguir popula `9WM35C2V_pathmap.txt`:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Em seguida, a ferramenta gravará os caminhos de arquivo problemáticos para `9WM35C2V_pathmap.txt`, um em cada linha. Por exemplo, o arquivo pode conter as seguintes entradas depois de executar o comando:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Para cada arquivo na lista, você deve tentar localizar e abrir o arquivo para garantir que ele esteja disponível para a ferramenta. Se você quiser dizer à ferramenta explicitamente onde encontrar um arquivo, poderá modificar o arquivo de mapa de caminho e adicionar o caminho a cada arquivo na mesma linha, separado por um caractere de tabulação:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Depois de disponibilizar os arquivos necessários para a ferramenta ou atualizar o arquivo de mapa de caminho, você poderá executar novamente a ferramenta para concluir o processo de importação.  
  
## <a name="next-steps"></a>Passos seguintes
 
* [Configurando a ferramenta de importação/exportação do Azure](storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de exportação](../storage-import-export-tool-repairing-an-export-job-v1.md)   
* [Resolver problemas da Ferramenta de Importação /Exportação do Azure (Troubleshooting the Azure Import/Export Tool)](storage-import-export-tool-troubleshooting-v1.md)
