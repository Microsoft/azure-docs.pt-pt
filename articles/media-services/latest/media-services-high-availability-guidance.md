---
title: Alta disponibilidade dos Serviços de Mídia Azure
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
ms.openlocfilehash: e2212c2ad5a0ab1899d281e99a89b33a2fc0298f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78202289"
---
# <a name="azure-media-services-high-availability-guidance"></a>Orientação de alta disponibilidade dos Serviços De Comunicação Social Azure

O serviço de codificação da Azure Media Services é uma plataforma regional de processamento de lotes e não é atualmente projetado para uma elevada disponibilidade numa única região. O serviço de codificação atualmente não fornece falha instantânea do serviço se houver uma falha regional do datacenter ou falha de serviços de componentes ou dependentes subjacentes (tais como armazenamento, SQL, etc.)  Este artigo explica como implementar serviços de media para manter uma arquitetura de alta disponibilidade com failover e garantir a disponibilidade ideal para as suas aplicações. 

Seguindo as orientações e as melhores práticas descritas no artigo, reduzirá o risco de codificar falhas, atrasos e minimizar o tempo de recuperação se ocorrer uma paragem numa única região.

## <a name="how-to-build-a-cross-regional-encoding-system"></a>Como construir um sistema de codificação inter-regional

* [Crie](create-account-cli-how-to.md) duas (ou mais) contas azure media.
* Inscreva-se nas mensagens **JobStateChange** em cada conta.

    * Nos Media Services v3, é feito via Azure Event Grid. Para obter mais informações, consulte:
    
       * [Exemplos da Grelha de Eventos,](../../event-grid/receive-events.md) 
       * Rede de [Eventos Azure para eventos](media-services-event-schemas.md)de Media Services, 
       * [Registe-se para eventos através do portal Azure ou do CLI](reacting-to-media-services-events.md) (pode também fazê-lo com o SDK de Gestão EventGrid)
       * [Microsoft.Azure.EventGrid SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (que suporta eventos de Media Services de forma nativa). 
       
        Também pode consumir eventos da Grelha de Eventos através de Funções Azure.
    * Nos Serviços de Media v2, isto é feito através de [NotificationEndpoints](../previous/media-services-dotnet-check-job-progress-with-webhooks.md).
* Quando [se cria um emprego:](transforms-jobs-concept.md)

    * Selecione aleatoriamente uma conta da lista de contas atualmente utilizadas (esta lista normalmente conterá ambas as contas, mas se os problemas forem detetados, pode conter apenas uma conta). Se a lista estiver vazia, levante um alerta para que um operador possa investigar.
    * A orientação geral é que precisa de uma unidade reservada a meios de [comunicação](media-reserved-units-cli-how-to.md) por tarefa ou [JobOutput](https://docs.microsoft.com/rest/api/media/jobs/create#joboutputasset) (a menos que esteja a utilizar [o VideoAnalyzerPreset](analyzing-video-audio-files-concept.md) em v3).
    * Obtenha a contagem de [unidades reservadas](media-reserved-units-cli-how-to.md) de meios de comunicação (MrUs) para a conta escolhida. Se a contagem de **unidades reservadas** aos meios de comunicação não estiver já no valor máximo, adicione o número de MRUs necessários pelo trabalho e atualize o serviço. Se a sua taxa de submissão de emprego for elevada e estiver frequentemente a consultar as MrUs para descobrir que está no máximo, use uma cache distribuída para o valor com um tempo de tempo razoável.
    * Mantenha uma contagem do número de postos de trabalho a bordo.
* Quando o seu responsável pela JobStateChange receber uma notificação de que um trabalho chegou ao estado programado, registe a hora de entrar no estado de agenda e na região/conta utilizada.    
* Quando o seu responsável pela JobStateChange receber uma notificação de que um trabalho chegou ao estado de processamento, marque o recorde para o trabalho como processamento.
* Quando o seu manipulador JobStateChange receber uma notificação de que um trabalho chegou ao estado acabado/errado/cancelado, marque o recorde para o trabalho como final e decreção da contagem de empregos de bordo. Obtenha o número de unidades reservadas para os meios de comunicação reservados para a conta escolhida e compare o número atual de MRU com a sua contagem de trabalho de bordo. Se a sua contagem de bordo for inferior à contagem de MRU, então decrete-a e atualize o serviço.
* Tenha um processo separado que analise periodicamente os seus registos dos trabalhos. Se tiver empregos no estado programado que não tenham avançado para o estado de processamento em um período razoável de tempo para uma determinada região, remova essa região da sua lista de contas usadas atualmente.

    * Dependendo dos seus requisitos de negócio, pode decidir cancelar esses postos de trabalho imediatamente e resubmetê-los para a outra conta. Ou pode dar-lhes mais tempo para se mudarem para o próximo estado.   
    * Após um período de tempo, adicione a conta de volta à lista atualmente utilizada (com o pressuposto de que a região recuperou).
    
Se descobrir que a contagem de MRU está a bater muito, mova a lógica de decreção para a tarefa periódica. Fazer com que a lógica de submissão pré-trabalho compare a contagem de voo com a contagem atual de MRU para ver se precisa de atualizar as MrUs.

## <a name="how-to-build-video-on-demand-cross-region-streaming"></a>Como construir o streaming de regiões transversais de vídeo a pedido 

* O streaming de regiões transversais de vídeo a pedido envolve a duplicação de [Ativos,](assets-concept.md) [Políticas chave](content-key-policy-concept.md) de conteúdo (se utilizados), Políticas [de Streaming](streaming-policy-concept.md)e Localizadores de [Streaming.](streaming-locators-concept.md) 
* Terá de criar as políticas em ambas as regiões e mantê-las atualizadas. 
* Quando criar os localizadores de streaming, irá querer utilizar o mesmo valor de Id do Localizador, valor Id contentKey e valor ContentKey.  
* Se estiver a codificar o conteúdo, é aconselhável codificar o conteúdo na região A e publicá-lo, em seguida, copiar o conteúdo codificado para a região B e publicá-lo usando os mesmos valores da região A.
* Pode utilizar o gestor de tráfego nos nomes do anfitrião para a origem e o serviço de entrega de chaves (na configuração dos Media Services isto será um URL de servidor de chaves personalizado).

## <a name="next-steps"></a>Passos seguintes

* [Criar uma conta](create-account-cli-how-to.md)
* Confira [amostras de código](https://docs.microsoft.com/samples/browse/?products=azure-media-services)
