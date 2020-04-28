---
title: Ver estado dos postos de trabalho de importação/exportação de Azure [ Microsoft Docs
description: Saiba como ver o estado dos postos de trabalho de importação/exportação e os impulsos utilizados.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 222c893f06d9adf77f8a8124af18bc03c5d20bdf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "72821437"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Ver o estado das tarefas de Importação/Exportação do Azure

Este artigo fornece informações sobre como ver o estado de condução e o estatuto de emprego para os postos de trabalho de importação/exportação de Azure. O serviço azure import/exportação é utilizado para transferir de forma segura grandes quantidades de dados para os Ficheiros Azure Blobs e Azure. O serviço também é usado para exportar dados do armazenamento Azure Blob.  

## <a name="view-job-and-drive-status"></a>Ver estado de trabalho e unidade
Pode acompanhar o estado dos seus postos de trabalho de importação ou exportação a partir do portal Azure. Clique no separador **Import/Export.** Uma lista dos seus trabalhos aparece na página.

![Ver Estado de Trabalho](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Ver o estado de tarefa

Você vê um dos seguintes estatutos de trabalho dependendo de onde a sua unidade está em processo.

| Estatuto do Emprego | Descrição |
|:--- |:--- |
| Criação | Depois de um trabalho ser criado, o seu estado está definido para **criar**. Enquanto o trabalho está no estado **de criação,** o serviço de importação/exportação assume que as unidades não foram enviadas para o centro de dados. Um emprego pode permanecer neste estado até duas semanas, após o qual é automaticamente apagado pelo serviço. |
| Shipping | Depois de enviar o seu pacote, deverá atualizar as informações de rastreio no portal Azure.  Isto transforma o trabalho em **estado de transporte.** O trabalho permanece no estado de **navegação** por até duas semanas. 
| Recebido | Depois de todas as unidades serem recebidas no centro de dados, o estado de trabalho está definido para **Receber**. |
| Transferência | Uma vez iniciada pelo menos uma unidade, o estado de trabalho está definido para **a transferência**. Para mais informações, vá aos [Estados de Condução.](#view-drive-status) |
| Empacotamento | Depois de todas as unidades terem concluído o processamento, o trabalho é colocado em estado de **embalagem** até que as unidades sejam enviadas de volta para si. |
| Concluído | Depois de todas as unidades serem enviadas de volta para si, se o trabalho tiver concluído sem erros, então o trabalho está definido para **Concluído**. O trabalho é automaticamente apagado após 90 dias no estado **concluído.** |
| Fechado | Depois de todas as unidades serem enviadas de volta para si, se houver algum erro durante o processamento do trabalho, o trabalho está definido para **Fechado**. O trabalho é automaticamente apagado após 90 dias no estado **fechado.** |

## <a name="view-drive-status"></a>Ver estado da unidade

O quadro abaixo descreve o ciclo de vida de uma unidade individual à medida que transita através de um emprego de importação ou exportação. O estado atual de cada unidade num trabalho é visto no portal Azure.

A tabela seguinte descreve cada estado que cada unidade de um trabalho pode passar.

| Estado de condução | Descrição |
|:--- |:--- |
| Especificado | Para um trabalho de importação, quando o trabalho é criado a partir do portal Azure, o estado inicial para uma unidade é **especificado**. Para um trabalho de exportação, uma vez que não é especificada nenhuma unidade quando o trabalho é criado, o estado de condução inicial é **recebido**. |
| Recebido | A transição de unidade para o Estado **recebido** quando o serviço de importação/exportação processou as unidades que foram recebidas da companhia marítima para um trabalho de importação. Para um trabalho de exportação, o estado de condução inicial é o Estado **Recebido.** |
| Nunca Recebido | A unidade desloca-se para o estado **nunca recebido** quando o pacote para um trabalho chega, mas o pacote não contém a unidade. Uma unidade também se desloca para este estado se já passaram duas semanas desde que o serviço recebeu a informação de envio, mas o pacote ainda não chegou ao datacenter. |
| Transferência | Uma unidade desloca-se para o estado **de transferência** quando o serviço começa a transferir dados da unidade para o Armazenamento Azure. |
| Concluído | Uma unidade desloca-se para o estado **completo** quando o serviço tiver transferido com sucesso todos os dados sem erros.
| CompletedMoreInfo | Uma unidade desloca-se para o estado **CompletedMoreInfo** quando o serviço encontrou alguns problemas enquanto copiava dados, quer da unidade. A informação pode incluir erros, avisos ou mensagens informativas sobre bolhas de sobreposição.
| ShippedBack | Uma unidade desloca-se para o estado **ShippedBack** quando foi enviada do datacenter de volta para o endereço de devolução. |

Esta imagem do portal Azure exibe o estado de condução de um trabalho de exemplo:

![Ver Drive State](./media/storage-import-export-service/drivestate.png)

A tabela seguinte descreve os estados de falha de unidade e as ações tomadas para cada Estado.

| Estado de condução | Evento | Resolução / Próximo passo |
|:--- |:--- |:--- |
| Nunca Recebido | Uma unidade que é marcada como **NeverReceived** (porque não foi recebida como parte do carregamento do trabalho) chega em outro carregamento. | A equipa de operações move a unidade para **Received**. |
| N/D | Um impulso que não faz parte de nenhum trabalho chega ao datacenter como parte de outro trabalho. | A unidade é marcada como uma unidade extra e é devolvida a você quando o trabalho associado ao pacote original estiver concluído. |

## <a name="time-to-process-job"></a>Hora de processar o trabalho
O tempo que demora a processar um emprego de importação/exportação varia com base em vários fatores, tais como:

-  Tempo de envio
-  Carregar no datacenter
-  Tipo de trabalho e tamanho dos dados que estão a ser copiados
-  Número de discos num trabalho. 

O serviço de importação/exportação não tem um SLA, mas o serviço esforça-se por completar a cópia em 7 a 10 dias após a sua recebido. Além do estado publicado no Portal Azure, as APIs rest podem ser usadas para acompanhar o progresso do trabalho. O parâmetro completo por cento da operação [List Jobs](/previous-versions/azure/dn529083(v=azure.100)) a chamada API fornece o progresso percentual da cópia.


## <a name="next-steps"></a>Passos seguintes

* [Configurar a ferramenta WAImportExport](storage-import-export-tool-how-to.md)
* [Transferir dados com utilitário de linha de comando AzCopy](storage-use-azcopy.md)
* [Amostra de API de exportação de importação de azure](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
