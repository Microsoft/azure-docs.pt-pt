---
title: Codificação de alta disponibilidade dos Serviços De Comunicação Social Azure
description: Aprenda a falhar numa conta secundária de Serviços de Media se ocorrer uma falha ou falha no centro de dados regional.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: afaa7545fbcbab016249e73a2247817310c5cdfc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78934199"
---
# <a name="media-services-high-availability-encoding"></a>Codificação de alta disponibilidade dos Serviços de Media 

O serviço de codificação da Azure Media Services é uma plataforma regional de processamento de lotes e não é atualmente projetado para uma elevada disponibilidade numa única região. O serviço de codificação atualmente não fornece falha instantânea do serviço se houver uma falha regional do datacenter ou falha de serviços de componentes ou dependentes subjacentes (como armazenamento, SQL). Este artigo explica como implementar serviços de media para manter uma arquitetura de alta disponibilidade com failover e garantir a disponibilidade ideal para as suas aplicações.

Seguindo as orientações e as melhores práticas descritas no artigo, reduzirá o risco de codificar falhas, atrasos e minimizar o tempo de recuperação se ocorrer uma paragem numa única região.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Como construir um sistema de codificação inter-regional

* [Crie](create-account-cli-how-to.md) duas (ou mais) contas azure media.

    As duas contas têm de estar em regiões diferentes. Para mais informações, consulte [as regiões em que o serviço Azure Media Services está implantado.](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)
* Faça o upload dos seus meios de comunicação para a mesma região de onde planeia submeter o trabalho. Para obter mais informações sobre como começar a codificar, consulte [Criar uma entrada de trabalho a partir de um URL HTTPS](job-input-from-http-how-to.md) ou Criar uma entrada de trabalho a partir de um ficheiro [local](job-input-from-local-file-how-to.md).

    Se precisar de reenviar o [trabalho](transforms-jobs-concept.md) para outra região, pode utilizar jobInputHttp ou utilizar [copy-blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) para copiar os dados do recipiente Source Asset para um contentor de Ativo na região alternativa.
* Inscreva-se nas mensagens JobStateChange em cada conta através da Grelha de Eventos Azure. Para obter mais informações, consulte:

    * [A amostra audio analytics](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) que mostra como monitorizar um trabalho com a Azure Event Grid, incluindo a adição de um recuo no caso de as mensagens da Rede de Eventos Azure serem adiadas por alguma razão.
    * [Rede de Eventos Azure schemas para eventos de Serviços de Media](media-services-event-schemas.md)
    * [Registe-se para eventos através do portal Azure ou do CLI](reacting-to-media-services-events.md) (pode também fazê-lo com o SDK de Gestão EventGrid)
    * [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (que suporta eventos de Media Services de forma nativa).

    Também pode consumir eventos da Grelha de Eventos através de Funções Azure.
* Quando se cria um [emprego:](transforms-jobs-concept.md)

    * Selecione aleatoriamente uma conta da lista de contas atualmente utilizadas (esta lista normalmente conterá ambas as contas, mas se os problemas forem detetados, pode conter apenas uma conta). Se a lista estiver vazia, levante um alerta para que um operador possa investigar.
    * A orientação geral é que precisa de uma unidade reservada a meios de [comunicação](media-reserved-units-cli-how-to.md) por [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (a menos que esteja a utilizar [o VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) onde são recomendadas 3 unidades reservadas por meio de trabalho).
    * Obtenha a contagem de unidades reservadas de meios de comunicação (MrUs) para a conta escolhida. Se a contagem de **unidades reservadas** aos meios de comunicação não estiver já no valor máximo, adicione o número de MRUs necessários pelo trabalho e atualize o serviço. Se a sua taxa de submissão de emprego for elevada e estiver frequentemente a consultar as MrUs para descobrir que está no máximo, use uma cache distribuída para o valor com um tempo de tempo razoável.
    * Mantenha uma contagem do número de postos de trabalho a bordo.

* Quando o seu responsável pela JobStateChange receber uma notificação de que um trabalho chegou ao estado programado, registe a hora de entrar no estado de agenda e na região/conta utilizada.
* Quando o seu responsável pela JobStateChange receber uma notificação de que um trabalho chegou ao estado de processamento, marque o recorde para o trabalho como processamento.
* Quando o seu manipulador JobStateChange receber uma notificação de que um trabalho chegou ao estado acabado/errado/cancelado, marque o recorde para o trabalho como final e decreção da contagem de empregos de bordo. Obtenha o número de unidades reservadas para os meios de comunicação reservados para a conta escolhida e compare o número atual de MRU com a sua contagem de trabalho de bordo. Se a sua contagem de bordo for inferior à contagem de MRU, então decrete-a e atualize o serviço.
* Tenha um processo separado que periodicamente olhe para os seus registos dos trabalhos
    
    * Se tiver empregos no estado programado que não tenham avançado para o estado de processamento em um período razoável de tempo para uma determinada região, remova essa região da sua lista de contas usadas atualmente.  Dependendo dos seus requisitos de negócio, pode decidir cancelar esses postos de trabalho imediatamente e resubmetê-los para a outra região. Ou pode dar-lhes mais tempo para se mudarem para o próximo estado.
    * Dependendo do número de Unidades Reservadas de Meios configuradas na conta e na taxa de submissão, também pode haver empregos no estado em fila que o sistema ainda não apanhou para processamento.  Se a lista de postos de trabalho no Estado em fila for para além de um limite aceitável numa região, esses postos de trabalho podem ser cancelados e submetidos à outra região.  No entanto, este pode ser um sintoma de não ter unidades reservadas de mídia suficientes configuradas na conta para a carga atual.  Pode solicitar uma quota unidade reservada aos meios de comunicação através do Suporte Azure, se necessário.
    * Se uma região foi removida da lista de contas, monitorize-a para recuperação antes de a adicionar de volta à lista.  A saúde regional pode ser monitorizada através dos postos de trabalho existentes na região (se não foram cancelados e resubmetidos), adicionando a conta de volta à lista após um período de tempo, e por operadores que monitorizam as comunicações do Azure sobre interrupções que podem estar a afetar Serviços Azure Media.
    
Se descobrir que a contagem de MRU está a bater muito, mova a lógica de decreção para a tarefa periódica. Fazer com que a lógica de submissão pré-trabalho compare a contagem de voo com a contagem atual de MRU para ver se precisa de atualizar as MrUs.

## <a name="next-steps"></a>Passos seguintes

* [Construir streaming de região transversal a vídeo-on-demand](media-services-high-availability-streaming.md)
* Confira [amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
