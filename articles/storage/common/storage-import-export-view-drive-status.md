---
title: Ver estado de importância dos empregos de importação/exportação da Azure / Microsoft Docs
description: Saiba como ver o estado dos postos de trabalho Azure Import/Export e os impulsos utilizados. Entenda os fatores que afetam o tempo que leva para processar um trabalho.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: dc8c944e1eb665f3f0bc83e28e1e5469d2da501a
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781996"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Ver o estado das tarefas de Importação/Exportação do Azure

Este artigo fornece informações sobre como ver o impulso e o estatuto laboral dos postos de trabalho da Azure Import/Export. O serviço Azure Import/Export é utilizado para transferir de forma segura grandes quantidades de dados para Azure Blobs e Azure Files. O serviço também é utilizado para exportar dados do armazenamento da Azure Blob.  

## <a name="view-job-and-drive-status"></a>Ver situação de trabalho e condução
Pode acompanhar o estado das suas empresas de importação ou exportação a partir do portal Azure selecionando o **separador Importação/Exportação.** Uma lista dos seus trabalhos aparece na página.

![Ver Estado de Trabalho](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Ver o estado de tarefa

Você vê um dos seguintes estatutos de trabalho dependendo de onde a sua unidade está no processo.

| Estatuto do Trabalho | Descrição |
|:--- |:--- |
| Criação | Após a criação de um trabalho, o seu estado está definido para **criar.** Enquanto o trabalho está no estado **de Criação,** o serviço de Importação/Exportação assume que as unidades não foram enviadas para o centro de dados. Um trabalho pode permanecer neste estado por até duas semanas, após o qual é automaticamente apagado pelo serviço. |
| Envio | Depois de enviar o seu pacote, deverá atualizar as informações de rastreio no portal Azure.  Ao fazê-lo, transforma o trabalho em estado **de transporte.** O trabalho permanece no estado **de transporte** por até duas semanas. 
| Recebido | Depois de todas as unidades serem recebidas no centro de dados, o estado de trabalho está definido para **Recebido.** |
| A transferir | Uma vez iniciada a transformação de pelo menos uma unidade, o estado de trabalho está definido para **a transferência.** Para mais informações, vá à [Drive States.](#view-drive-status) |
| Empacotamento | Depois de todas as unidades terem concluído o processamento, o trabalho é colocado no estado **de Embalagem** até que as unidades sejam enviadas de volta para si. |
| Concluído | Depois de todas as unidades forem enviadas de volta para si, se o trabalho tiver sido concluído sem erros, então o trabalho está definido para **Concluído** . O trabalho é automaticamente eliminado após 90 dias no estado **concluído.** |
| Fechada | Depois de todas as unidades serem enviadas de volta para si, se houver erros durante o processamento do trabalho, o trabalho está definido para **fechado** . O trabalho é automaticamente apagado após 90 dias no estado **fechado.** |

## <a name="view-drive-status"></a>Ver estado da unidade

O quadro abaixo descreve o ciclo de vida de uma unidade individual à medida que transita através de um trabalho de importação ou exportação. O estado atual de cada unidade num trabalho é visto no portal Azure.

A tabela a seguir descreve cada estado que cada unidade de um trabalho pode passar.

| Estado de condução | Descrição |
|:--- |:--- |
| Especificado | Para um trabalho de importação, quando o trabalho é criado a partir do portal Azure, o estado inicial para uma unidade é **especificado** . Para um trabalho de exportação, uma vez que não é especificado qualquer unidade quando o trabalho é criado, o estado de unidade inicial é **recebido** . |
| Recebido | A transição de unidade para o estado **recebido** quando o serviço de importação/exportação processou as unidades que foram recebidas da companhia de navegação para um trabalho de importação. Para um trabalho de exportação, o estado de condução inicial é o Estado **Recebido.** |
| Nunca percebido | A unidade move-se para o estado **NeverReceived** quando o pacote para um trabalho chega, mas o pacote não contém a unidade. Uma unidade também se move para este estado se o datacenter ainda não recebeu o pacote, e o serviço recebeu a informação de envio há pelo menos duas semanas. |
| A transferir | Uma unidade move-se para o estado **de Transfering** quando o serviço começa a transferir dados da unidade para o Azure Storage. |
| Concluído | Uma unidade move-se para o estado **concluído** quando o serviço transferiu com sucesso todos os dados sem erros.
| ConcluídoMoreInfo | Uma unidade move-se para o estado **CompletedMoreInfo** quando o serviço tem problemas enquanto copia dados de ou para a unidade. As informações podem incluir erros, avisos ou mensagens informativas sobre bolhas de sobreposição.
| Back enviado | Uma unidade move-se para o estado **ShippedBack** quando foi enviado do centro de dados de volta para o endereço de retorno. |

Esta imagem do portal Azure mostra o estado de unidade de um trabalho de exemplo:

![Ver Estado de Unidade](./media/storage-import-export-service/drivestate.png)

A tabela seguinte descreve os estados de falha de unidade e as ações tomadas para cada estado.

| Estado de condução | Evento | Resolução / Próximo passo |
|:--- |:--- |:--- |
| Nunca percebido | Uma unidade marcada como **NeverReceived** (porque não foi recebida como parte do carregamento do trabalho) chega em outro carregamento. | A equipa de operações move a unidade para **Receber.** |
| N/D | Uma unidade que não faz parte de nenhum trabalho chega ao datacenter como parte de outro trabalho. | A unidade está marcada como uma unidade extra. É devolvido quando o trabalho associado ao pacote original estiver concluído. |

## <a name="time-to-process-job"></a>Hora de processar o trabalho
O tempo necessário para processar um trabalho de importação/exportação varia em função de vários fatores, tais como:

-  Tempo de envio
-  Carregar no datacenter
-  Tipo de trabalho e tamanho dos dados que estão a ser copiados
-  Número de discos num trabalho. 

O serviço de importação/exportação não tem um SLA, mas o serviço esforça-se para completar a cópia em 7 a 10 dias após a recebido dos discos. Além do estado publicado no portal Azure, pode utilizar ASP de REST para acompanhar o progresso do trabalho. Utilize o parâmetro por cento completo na chamada API da operação [Empregos listar](/previous-versions/azure/dn529083(v=azure.100)) para visualizar o progresso da cópia percentual.


## <a name="next-steps"></a>Passos seguintes

* [Dados de transferência com utilitário de linha de comando AzCopy](./storage-use-azcopy-v10.md)
* [Amostra Azure Import Export REST API](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)