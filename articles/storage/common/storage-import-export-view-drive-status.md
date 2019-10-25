---
title: Exibir o status dos trabalhos de importação/exportação do Azure | Microsoft Docs
description: Saiba como exibir o status de trabalhos de importação/exportação e as unidades usadas.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 222c893f06d9adf77f8a8124af18bc03c5d20bdf
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821437"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Exibir o status dos trabalhos de importação/exportação do Azure

Este artigo fornece informações sobre como exibir o status da unidade e do trabalho para trabalhos de importação/exportação do Azure. O serviço de importação/exportação do Azure é usado para transferir com segurança grandes quantidades de dados para BLOBs do Azure e arquivos do Azure. O serviço também é usado para exportar dados do armazenamento de BLOBs do Azure.  

## <a name="view-job-and-drive-status"></a>Exibir o status do trabalho e da unidade
Você pode acompanhar o status de seus trabalhos de importação ou exportação do portal do Azure. Clique na guia **importar/exportar** . Uma lista de seus trabalhos é exibida na página.

![Exibir estado do trabalho](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Ver o estado de tarefa

Você verá um dos seguintes status de trabalho, dependendo de onde a unidade está no processo.

| Status do trabalho | Descrição |
|:--- |:--- |
| Criação | Depois que um trabalho é criado, seu estado é definido como **criando**. Enquanto o trabalho está no estado **criando** , o serviço de importação/exportação pressupõe que as unidades não foram enviadas para o Data Center. Um trabalho pode permanecer nesse estado por até duas semanas, após o qual ele é excluído automaticamente pelo serviço. |
| Distribuído | Depois de enviar seu pacote, você deve atualizar as informações de rastreamento no portal do Azure.  Isso transforma o trabalho em estado de **envio** . O trabalho permanece no estado de **envio** por até duas semanas. 
| Recebido | Depois que todas as unidades forem recebidas na data center, o estado do trabalho será definido como **recebido**. |
| Transferir | Depois que pelo menos uma unidade tiver iniciado o processamento, o estado do trabalho será definido como **transferindo**. Para obter mais informações, vá para [Estados da unidade](#view-drive-status). |
| Empacotamento | Depois que todas as unidades tiverem concluído o processamento, o trabalho será colocado no estado de **empacotamento** até que as unidades sejam enviadas de volta para você. |
| Concluído | Depois que todas as unidades forem enviadas de volta para você, se o trabalho for concluído sem erros, o trabalho será definido como **concluído**. O trabalho é excluído automaticamente após 90 dias no estado **concluído** . |
| Legenda | Depois que todas as unidades forem enviadas de volta para você, se houver erros durante o processamento do trabalho, o trabalho será definido como **fechado**. O trabalho é excluído automaticamente após 90 dias no estado **fechado** . |

## <a name="view-drive-status"></a>Ver estado da unidade

A tabela a seguir descreve o ciclo de vida de uma unidade individual à medida que ela faz a transição por meio de um trabalho de importação ou exportação. O estado atual de cada unidade em um trabalho é visto na portal do Azure.

A tabela a seguir descreve cada Estado em que cada unidade em um trabalho pode passar.

| Estado da unidade | Descrição |
|:--- |:--- |
| Determinado | Para um trabalho de importação, quando o trabalho é criado no portal do Azure, o estado inicial de uma unidade é **especificado**. Para um trabalho de exportação, como nenhuma unidade é especificada quando o trabalho é criado, o estado inicial da unidade é **recebido**. |
| Recebido | A unidade faz a transição para o estado **recebido** quando o serviço de importação/exportação tiver processado as unidades que foram recebidas da empresa de remessa para um trabalho de importação. Para um trabalho de exportação, o estado inicial da unidade é o estado **recebido** . |
| Nunca recebido | A unidade passa para o estado **nunca recebido** quando o pacote de um trabalho chega, mas o pacote não contém a unidade. Uma unidade também se moverá para esse Estado se tiver sido duas semanas desde que o serviço recebeu as informações de envio, mas o pacote ainda não chegou ao datacenter. |
| Transferir | Uma unidade passa para o estado de **transferência** quando o serviço começa a transferir dados da unidade para o armazenamento do Azure. |
| Concluído | Uma unidade passa para o estado **concluído** quando o serviço transferiu com êxito todos os dados sem erros.
| Concluído mais informações | Uma unidade passa para o estado **concluído mais informações** quando o serviço tiver encontrado alguns problemas ao copiar dados de ou para a unidade. As informações podem incluir erros, avisos ou mensagens informativas sobre a substituição de BLOBs.
| Enviado | Uma unidade passa para o estado **enviado** quando ela é enviada do datacenter de volta para o endereço de retorno. |

Essa imagem da portal do Azure exibe o estado da unidade de um trabalho de exemplo:

![Exibir estado da unidade](./media/storage-import-export-service/drivestate.png)

A tabela a seguir descreve os Estados de falha da unidade e as ações executadas para cada Estado.

| Estado da unidade | Evento | Resolução/próxima etapa |
|:--- |:--- |:--- |
| Nunca recebido | Uma unidade marcada como **nunca recebido** (porque ela não foi recebida como parte da remessa do trabalho) chega em outra remessa. | A equipe de operações move a unidade para o **recebimento**. |
| N/A | Uma unidade que não faz parte de nenhum trabalho chega ao datacenter como parte de outro trabalho. | A unidade é marcada como uma unidade extra e é retornada a você quando o trabalho associado ao pacote original é concluído. |

## <a name="time-to-process-job"></a>Tempo para processar o trabalho
A quantidade de tempo que leva para processar um trabalho de importação/exportação varia de acordo com vários fatores, como:

-  Tempo de envio
-  Carregar no datacenter
-  Tipo de trabalho e tamanho dos dados que estão sendo copiados
-  Número de discos em um trabalho. 

O serviço de importação/exportação não tem um SLA, mas o serviço se esforça para concluir a cópia em 7 a 10 dias depois que os discos são recebidos. Além do status lançado no portal do Azure, as APIs REST podem ser usadas para acompanhar o progresso do trabalho. O parâmetro de porcentagem concluída na chamada da API da operação [listar trabalhos](/previous-versions/azure/dn529083(v=azure.100)) fornece o progresso da cópia percentual.


## <a name="next-steps"></a>Passos seguintes

* [Configurar a ferramenta WAImportExport](storage-import-export-tool-how-to.md)
* [Transferir dados com o utilitário de linha de comando AzCopy](storage-use-azcopy.md)
* [Exemplo de API REST de importação e exportação do Azure](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
