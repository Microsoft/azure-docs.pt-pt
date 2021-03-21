---
title: Reparação de um trabalho de importação/exportação da Azure importe - v1 | Microsoft Docs
description: Saiba como reparar um trabalho de importação que foi criado e executado utilizando o serviço Azure Import/Export.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: af6234a81e8f79424cc3b1ec4178a175d9ff0146
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98706712"
---
# <a name="repairing-an-import-job"></a>Reparação de uma tarefa de importação
O serviço Importar/Exportar do Microsoft Azure pode não copiar alguns dos seus ficheiros ou partes de um ficheiro para o serviço Blob do Windows Azure. Alguns motivos de falhas incluem:  
  
-   Ficheiros danificados  
  
-   Unidades danificadas  
  
-   A chave da conta de armazenamento mudou enquanto o ficheiro estava a ser transferido.  
  
Pode executar a Ferramenta de Importação/Exportação do Microsoft Azure com os ficheiros de registo de cópias do trabalho de importação. A ferramenta envia os ficheiros em falta, ou partes de um ficheiro, para a sua conta de armazenamento Do Windows Azure para completar o trabalho de importação.  
  
## <a name="repairimport-parameters"></a>ReparaçãoParessos

Os seguintes parâmetros podem ser especificados com **RepairImport:** 
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**Necessário.** Caminho para o ficheiro de reparação, que acompanha o progresso da reparação, e permite-lhe retomar uma reparação interrompida. Cada unidade deve ter um e único ficheiro de reparação. Quando iniciar uma reparação para uma determinada unidade, passe no caminho para um ficheiro de reparação, que ainda não existe. Para retomar uma reparação interrompida, deverá passar em nome de um ficheiro de reparação existente. Especifique sempre o ficheiro de reparação correspondente à unidade do alvo.|  
|**/logdir:**<LogDirectory\>|**É opcional.** O diretório de registos. Os ficheiros de registo verboso são escritos neste diretório. Se não for especificado nenhum diretório de registos, o diretório atual é utilizado como diretório de registo.|  
|**/d:**<TargetDirectories\>|**Necessário.** Um ou mais diretórios separados por pontos de separá-se de pontos que contêm os ficheiros originais que foram importados. A unidade de importação também pode ser usada, mas não é necessária se estiverem disponíveis localizações alternativas de ficheiros originais.|  
|**/bk:**<BitLockerKey\>|**É opcional.** Especifique a tecla BitLocker se pretender que a ferramenta desbloqueie uma unidade encriptada onde os ficheiros originais estão disponíveis.|  
|**/sn:**<StorageAccountName\>|**Necessário.** O nome do armazém é responsável pelo trabalho de importação.|  
|**/sk:**<StorageAccountKey\>|**Obrigatório** se e somente se um contentor SAS não for especificado. A chave da conta para o armazém é a conta do trabalho de importação.|  
|**/csas:**<ContainerSas\>|**Necessário** se e somente se a chave da conta de armazenamento não for especificada. O contentor SAS para acesso às bolhas associadas ao trabalho de importação.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Necessário.** Caminho para o ficheiro de registo de cópia de unidade (ou registo verboso ou registo de erro). O ficheiro é gerado pelo serviço De importação/Exportação do Windows Azure e pode ser descarregado a partir do armazenamento de bolhas associado ao trabalho. O ficheiro de registo de cópias contém informações sobre bolhas ou ficheiros falhados, que devem ser reparados.|  
|**/PathMapFile:**<DrivePathMapFile\>|**É opcional.** Caminho para um ficheiro de texto usado para resolver ambiguidades se tiver vários ficheiros com o mesmo nome que estava a importar no mesmo trabalho. A primeira vez que a ferramenta é executada, pode povoar este ficheiro com todos os nomes ambíguos. Posteriormente, executa a ferramenta, utilize este ficheiro para resolver as ambiguidades.|  
  
## <a name="using-the-repairimport-command"></a>Utilizando o comando RepairImport  
Para reparar os dados de importação através do streaming dos dados através da rede, deve especificar os diretórios que contêm os ficheiros originais que estava a importar usando o `/d` parâmetro. Especificar também o ficheiro de registo de cópia que descarregou a partir da sua conta de armazenamento. Uma linha de comando típica para reparar um trabalho de importação com falhas parciais parece:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
No exemplo seguinte de um ficheiro de registo de cópia, uma peça de 64-K de um ficheiro foi corrompida na unidade que foi enviada para o trabalho de importação. Uma vez que esta falha é a única indicada, o resto das bolhas no trabalho foram importadas com sucesso.  
  
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
  
Quando este registo de cópia é passado para a Ferramenta de Importação/Exportação Azure, a ferramenta tenta terminar a importação deste ficheiro copiando os conteúdos em falta em toda a rede. Seguindo o exemplo acima, a ferramenta procura o ficheiro original `\animals\koala.jpg` dentro dos dois diretórios `C:\Users\bob\Pictures` e `X:\BobBackup\photos` . Se o ficheiro `C:\Users\bob\Pictures\animals\koala.jpg` existir, a Ferramenta de Importação/Exportação Azure copia a gama de dados em falta para a bolha correspondente `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg` .  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Resolução de conflitos ao utilizar o RepairImport  
Em algumas situações, a ferramenta pode não ser capaz de encontrar ou abrir o ficheiro necessário por uma das seguintes razões: o ficheiro não foi encontrado, o ficheiro não está acessível, o nome do ficheiro é ambíguo ou o conteúdo do ficheiro já não está correto.  
  
Pode ocorrer um erro ambíguo se a ferramenta estiver a tentar localizar `\animals\koala.jpg` e houver um ficheiro com esse nome em ambos e `C:\Users\bob\pictures` `X:\BobBackup\photos` . Ou seja, tanto `C:\Users\bob\pictures\animals\koala.jpg` e `X:\BobBackup\photos\animals\koala.jpg` existem nas unidades de emprego de importação.  
  
A `/PathMapFile` opção permite-lhe resolver estes erros. Pode especificar o nome do ficheiro, que contém a lista de ficheiros que a ferramenta não conseguiu identificar corretamente. O exemplo da linha de comando a seguir `9WM35C2V_pathmap.txt` povoa:  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
A ferramenta escreverá então os caminhos problemáticos do ficheiro para `9WM35C2V_pathmap.txt` , um em cada linha. Por exemplo, o ficheiro pode conter as seguintes entradas após a execução do comando:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Para cada ficheiro da lista, deverá tentar localizar e abrir o ficheiro para garantir que está disponível para a ferramenta. Se desejar dizer explicitamente à ferramenta onde encontrar um ficheiro, modificar o ficheiro do mapa do caminho e adicionar o caminho a cada ficheiro na mesma linha, separado por um caractere de separador:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Depois de disponibilizar os ficheiros necessários à ferramenta ou de atualizar o ficheiro do mapa de caminhos, pode voltar a completar a ferramenta para concluir o processo de importação.  
  
## <a name="next-steps"></a>Passos seguintes
 
* [Criação da ferramenta de importação/exportação Azure](storage-import-export-tool-setup-v1.md)   
* [Preparar as unidades de disco rígido para uma tarefa de importação](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
* [Revisão do estado da tarefa com ficheiros de registo de cópia](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Reparação de uma tarefa de exportação](./storage-import-export-tool-repairing-an-export-job-v1.md)