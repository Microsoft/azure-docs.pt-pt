---
title: Azure Media Services codificação de alta disponibilidade
description: Saiba como falhar com uma conta secundária dos Media Services se ocorrer uma falha ou falha regional do datacenter.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "78934199"
---
# <a name="media-services-high-availability-encoding"></a>Codificação de alta disponibilidade dos Serviços de Mídia 

O serviço de codificação Azure Media Services é uma plataforma regional de processamento de lotes e não é atualmente projetado para uma elevada disponibilidade dentro de uma única região. O serviço de codificação não presta atualmente falhas instantâneas do serviço se houver uma falha regional do datacenter ou falha de componentes subjacentes ou serviços dependentes (como armazenamento, SQL). Este artigo explica como implementar serviços de mídia para manter uma arquitetura de alta disponibilidade com failover e garantir a melhor disponibilidade para as suas aplicações.

Seguindo as orientações e as boas práticas descritas no artigo, reduzirá o risco de codificação de falhas, atrasos e minimizar o tempo de recuperação se ocorrer uma paragem numa única região.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Como construir um sistema de codificação trans-regional

* [Criar](create-account-cli-how-to.md) duas (ou mais) contas Azure Media Services.

    As duas contas têm de ser em diferentes regiões. Para mais informações, consulte [as regiões em que o serviço Azure Media Services está implantado.](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)
* Faça o upload dos seus meios de comunicação para a mesma região a partir da qual planeia submeter o trabalho. Para obter mais informações sobre como começar a codificar, consulte [Criar uma entrada de trabalho a partir de um URL HTTPS](job-input-from-http-how-to.md) ou [criar uma entrada de trabalho a partir de um ficheiro local](job-input-from-local-file-how-to.md).

    Se precisar então de reenviar o [trabalho](transforms-jobs-concept.md) para outra região, pode utilizar jobInputHttp ou utilizar [copy-Blob](https://docs.microsoft.com/rest/api/storageservices/Copy-Blob) para copiar os dados do recipiente de ativos de origem para um contentor de Ativos na região alternativa.
* Subscreva as mensagens JobStateChange em cada conta através da Azure Event Grid. Para obter mais informações, consulte:

    * [Amostra de Audio Analytics](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer) que mostra como monitorizar um trabalho com a Azure Event Grid, incluindo adicionar um recuo no caso de as mensagens Azure Event Grid serem adiadas por algum motivo.
    * [Esquemas de grelha de eventos Azure para eventos de Serviços de Mídia](media-services-event-schemas.md)
    * [Registe-se para eventos através do portal Azure ou do CLI](reacting-to-media-services-events.md) (também pode fazê-lo com o EventGrid Management SDK)
    * [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (que suporta eventos de Media Services de forma nativa).

    Também pode consumir eventos de Grade de Eventos através de Funções Azure.
* Quando se cria um [emprego:](transforms-jobs-concept.md)

    * Selecione aleatoriamente uma conta da lista de contas atualmente utilizadas (esta lista normalmente conterá ambas as contas, mas se forem detetadas questões só pode conter uma conta). Se a lista estiver vazia, levante um alerta para que um operador possa investigar.
    * A orientação geral é que precisa de uma unidade reservada por [meio de comunicação](media-reserved-units-cli-how-to.md) (a menos que esteja a usar o [VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) onde são recomendadas 3 unidades reservadas por JobOutput). [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset)
    * Obtenha a contagem de unidades reservadas de mídia (MRUs) para a conta escolhida. Se a contagem de **unidades reservadas** aos meios atuais ainda não estiver no valor máximo, adicione o número de MRUs necessário pelo trabalho e atualize o serviço. Se a sua taxa de submissão de emprego for elevada e estiver frequentemente a consultar as MRUs para descobrir que está no máximo, utilize uma cache distribuída para o valor com um tempo razoável.
    * Mantenha uma contagem do número de trabalhos de voo.

* Quando o seu manipulador JobStateChange receber a notificação de que um trabalho atingiu o estado programado, registe a hora em que entra no estado de programação e na região/conta utilizada.
* Quando o seu manipulador JobStateChange receber a notificação de que um trabalho atingiu o estado de processamento, marque o registo do trabalho como processamento.
* Quando o seu manipulador JobStateChange receber a notificação de que um trabalho atingiu o estado terminado/erro/cancelado, marque o recorde para o trabalho como final e decreta a contagem de trabalho de bordo. Obtenha o número de unidades reservadas para a conta escolhida e compare o número atual de MRU com a sua contagem de trabalho de bordo. Se a sua contagem de voos for inferior à contagem de MRU, então decreta-a e atualize o serviço.
* Tenha um processo separado que periodicamente olha para os seus registos dos trabalhos
    
    * Se tiver empregos no estado programado que não tenham avançado para o estado de processamento num determinado período de tempo para uma determinada região, retire essa região da sua lista de contas atualmente utilizadas.  Dependendo dos requisitos do seu negócio, pode decidir cancelar esses postos de trabalho imediatamente e reencando-os para a outra região. Ou pode dar-lhes mais tempo para se mudarem para o próximo estado.
    * Dependendo do número de Unidades Reservadas de Meios configuradas na conta e na taxa de submissão, também pode haver empregos na fila que o sistema ainda não recolheu para processamento.  Se a lista de postos de trabalho no estado em fila ultrapassar um limite aceitável numa região, esses postos de trabalho podem ser cancelados e submetidos à outra região.  No entanto, este pode ser um sintoma de não ter unidades reservadas de mídia suficientes configuradas na conta para a carga atual.  Pode solicitar uma quota de Unidade Reservada a Meios de Comunicação Social mais elevada através do Suporte Azure, se necessário.
    * Se uma região foi removida da lista de conta, monitorize-a para recuperação antes de a adicionar de volta à lista.  A saúde regional pode ser monitorizada através dos postos de trabalho existentes na região (se não foram cancelados e reenviados), adicionando a conta de volta à lista após um período de tempo, e pelos operadores que monitorizam as comunicações da Azure sobre falhas que podem estar a afetar a Azure Media Services.
    
Se descobrir que a contagem de MRU está a subir e a descer muito, mova a lógica de decréped para a tarefa periódica. Que a lógica de submissão pré-trabalho compare a contagem de voos com a contagem atual de MRU para ver se precisa de atualizar as MRUs.

## <a name="next-steps"></a>Próximos passos

* [Construir streaming de região cruzada vídeo-on-demand](media-services-high-availability-streaming.md)
* Confira as [amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
