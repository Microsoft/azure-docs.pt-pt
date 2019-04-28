---
title: Ver o estado das tarefas de importação/exportação do Azure | Documentos da Microsoft
description: Saiba como ver o estado das tarefas de importação/exportação e as unidades utilizadas.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 225164fe00f70839446f8b74155cd3959f745a49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61478050"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Ver o estado das tarefas de importação/exportação do Azure

Este artigo fornece informações sobre como visualizar o estado de unidade e de tarefas para tarefas de importação/exportação do Azure. O serviço importar/exportar do Azure é utilizado para transferir grandes quantidades de dados de forma segura para os Blobs do Azure e ficheiros do Azure. O serviço também é utilizado para exportar dados do armazenamento de Blobs do Azure.  

## <a name="view-job-and-drive-status"></a>Ver tarefa e a unidade de estado
Pode controlar o estado da importação ou exportar tarefas a partir do portal do Azure. Clique nas **importação/exportação** separador. É apresentada uma lista das suas tarefas na página.

![Vista de estado da tarefa](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Ver o estado de tarefa

Veja um dos seguintes Estados de tarefa, dependendo de onde sua unidade está no processo.

| Estado da Tarefa | Descrição |
|:--- |:--- |
| Criação | Depois de uma tarefa é criada, o seu estado está definido como **Creating**. Enquanto a tarefa está no **Creating** de estado, o serviço importar/exportar pressupõe que as unidades não foram enviadas para o Centro de dados. Uma tarefa pode permanecer neste estado durante até duas semanas, após o qual é automaticamente eliminado pelo serviço. |
| Envio | Depois de enviar seu pacote, deve atualizar as informações de controle no portal do Azure.  Isso transforma o trabalho em **envio** estado. A tarefa continuará a ser o **envio** estado para duas semanas. 
| Recebido | Depois de todas as unidades são recebidas no Centro de dados, o estado da tarefa está definido como **recebidos**. |
| A transferir | Depois de pelo menos uma unidade iniciou o processamento, o estado da tarefa está definido como **Transferring**. Para obter mais informações, aceda a [Estados de unidade](#view-drive-status). |
| Empacotamento | Depois de concluíram todas as unidades de processamento, a tarefa é colocada num **empacotamento** estado até que as unidades são enviadas novamente para si. |
| Concluído | Depois de todas as unidades são fornecidas para, se a tarefa for concluído sem erros, a tarefa com a, em seguida, é definida como **concluído**. A tarefa é eliminada automaticamente após 90 dias no **concluído** estado. |
| Fechado | Depois de todas as unidades são fornecidas para, se ocorreram erros durante o processamento da tarefa, a tarefa está definida como **fechado**. A tarefa é eliminada automaticamente após 90 dias no **fechado** estado. |

## <a name="view-drive-status"></a>Ver estado da unidade

A tabela abaixo descreve o ciclo de vida de uma unidade individual, como faz a transição por meio de uma tarefa de importação ou exportação. O estado atual de cada unidade numa tarefa é visto no portal do Azure.

A tabela seguinte descreve cada Estado de cada unidade numa tarefa pode pass-through.

| Unidade de estado | Descrição |
|:--- |:--- |
| Especificado | Para uma tarefa de importação, quando a tarefa é criada a partir do portal do Azure, o estado inicial para uma unidade é **especificado**. Para uma tarefa de exportação, uma vez que for especificada nenhuma unidade quando a tarefa é criada, o estado inicial de unidade é **recebidos**. |
| Recebido | A unidade faz a transição para o **recebidos** quando o serviço importar/exportar processou as unidades que foram recebidas da empresa de transporte para uma tarefa de importação de estado. Para uma tarefa de exportação, o estado da unidade inicial é o **recebidos** estado. |
| NeverReceived | A unidade se move para o **NeverReceived** quando chega o pacote para uma tarefa, mas o pacote não contém a unidade de estado. Uma unidade também passa para neste estado se já tiverem passado duas semanas, uma vez que o serviço recebeu as informações de envio, mas o pacote ainda não está disponível no datacenter. |
| A transferir | Move de uma unidade para o **Transferring** de estado quando o serviço começa a transferir os dados da unidade para o armazenamento do Azure. |
| Concluído | Move de uma unidade para o **concluído** quando o serviço foi transferida com êxito todos os dados sem erros de estado.
| CompletedMoreInfo | Move de uma unidade para o **CompletedMoreInfo** quando o serviço encontrou alguns problemas ao copiar dados a partir ou para a unidade de estado. As informações podem incluir erros, avisos ou mensagens informativas sobre Sobrescrever blobs.
| ShippedBack | Move de uma unidade para o **ShippedBack** estado quando ele foi enviado do Centro de dados para o endereço do remetente. |

Esta imagem a partir do portal do Azure apresenta o estado de unidade de uma tarefa de exemplo:

![Estado da unidade de exibição](./media/storage-import-export-service/drivestate.png)

A tabela seguinte descreve os Estados de falha de unidade e as ações executadas para cada Estado.

| Unidade de estado | Evento | Resolução / passo seguinte |
|:--- |:--- |:--- |
| NeverReceived | Uma unidade que está marcada como **NeverReceived** (porque não foi recebido como parte do envio da tarefa) é recebido no envio de outro. | A equipe de operações move a unidade para **recebidos**. |
| N/A | Uma unidade que não faz parte de qualquer tarefa chega ao centro de dados como parte de outra tarefa. | A unidade está marcada como uma unidade extra e retornada para quando a tarefa associada ao pacote original é concluída. |

## <a name="time-to-process-job"></a>Tempo para a tarefa de processo
A quantidade de tempo que demora a processar uma tarefa de importação/exportação varia com base em vários fatores, tais como:

-  Hora de envio
-  Carregar no datacenter
-  Tipo de tarefa e o tamanho dos dados a ser copiados
-  Número de discos numa tarefa. 

O serviço importar/exportar não tem um SLA, mas o serviço se esforça para concluir a cópia dentro de 7 a 10 dias após os discos são recebidos. Para além do estado publicado no Portal do Azure, as REST APIs pode ser utilizadas para controlar o progresso da tarefa. A percentagem de conclusão parâmetro na [lista de tarefas](/previous-versions/azure/dn529083(v=azure.100)) chamada de operação de API fornece o progresso de cópia de percentagem.


## <a name="next-steps"></a>Passos Seguintes

* [Configurar a ferramenta de WAImportExport](storage-import-export-tool-how-to.md)
* [Transferir dados com o utilitário da linha de comandos AzCopy](storage-use-azcopy.md)
* [Exemplo de importar exportar a API de REST do Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)
