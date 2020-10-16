---
title: Azure Functions recuperação de geo-desastres e alta disponibilidade
description: Como utilizar as regiões geográficas para redundância e falhar nas Funções Azure.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 08/29/2019
ms.openlocfilehash: 446a76b823ce3592a83d0c2f898041951361b47e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86506286"
---
# <a name="azure-functions-geo-disaster-recovery"></a>Azure Functions recuperação de geo-desastre

Quando regiões inteiras de Azure ou centros de dados experimentam tempo de inatividade, é fundamental que o cálculo continue a ser processado numa região diferente.  Este artigo explicará algumas das estratégias que pode usar para implementar funções para permitir a recuperação de desastres.

## <a name="basic-concepts"></a>Conceitos básicos

As Funções Azure funcionam numa região específica.  Para obter uma maior disponibilidade, pode implementar as mesmas funções em várias regiões.  Quando em várias regiões pode ter as suas funções a funcionar no padrão *ativo/ativo* ou no padrão *ativo/passivo.*  

* Modo ativo/ativo. Ambas as regiões estão ativas e recebem eventos (duplicados ou rotativamente). Ativa/ativa é recomendada para funções HTTPS em combinação com Azure Front Door.
* Ativo/passivo. Uma região está ativa e recebe eventos, enquanto um secundário está inativo.  Quando é necessário o failover, a região secundária é ativada e assume o processamento.  Isto é recomendado para funções não-HTTP como Service Bus e Event Hubs.

Leia como [executar aplicativos em várias regiões](/azure/architecture/reference-architectures/app-service-web-app/multi-region) para obter mais informações sobre implementações multi-regiões.

## <a name="activeactive-for-https-functions"></a>Ativo/ativo para funções HTTPS

Para conseguir implementações ativas/ativas de funções, requer algum componente que possa coordenar os eventos entre ambas as regiões.  Para funções HTTPS, esta coordenação é realizada utilizando [a Porta Frontal Azure](../frontdoor/front-door-overview.md).  Azure Front Door pode encaminhar e pedir https round-robin entre várias funções regionais.  Também verifica periodicamente a saúde de cada ponto final.  Se uma função regional deixar de responder aos controlos de saúde, a Porta Frontal Azure irá tirá-la da rotação e apenas encaminhar o tráfego para funções saudáveis.  

![Arquitetura para Azure Front Door e Função](media/functions-geo-dr/front-door.png)  

## <a name="activeactive-for-non-https-functions"></a>Ativo/ativo para funções não-HTTPS

Ainda é possível obter implementações ativas/ativas para funções não-HTTPS.  No entanto, é necessário refletir sobre a forma como as duas regiões irão interagir ou coordenar-se entre si.  Se implementasse a mesma aplicação de função em duas regiões, cada uma desencadeando na mesma fila de autocarros de serviço, atuariam como consumidores concorrentes na des filas dessa fila.  Embora isto signifique que cada mensagem está apenas a ser processada por uma das instâncias, também significa que ainda há um único ponto de falha no autocarro de serviço único.  Se implementar duas filas de Autocarros de Serviço (uma numa região primária, outra numa região secundária) e as duas aplicações de função apontadas para a sua fila de região, o desafio surge agora na forma como as mensagens de fila são distribuídas entre as duas regiões.  Muitas vezes isto significa que cada editor tenta publicar uma mensagem para *ambas as* regiões, e cada mensagem é processada por ambas as aplicações de funções ativas.  Embora isto crie um padrão ativo/ativo, cria outros desafios em torno da duplicação do cálculo e quando ou como os dados são consolidados.  Por estas razões, recomenda-se que os gatilhos não-HTTPS utilizem o padrão ativo/passivo.

## <a name="activepassive-for-non-https-functions"></a>Ativo/passivo para funções não-HTTPS

Ativo/passivo fornece uma forma de apenas uma função processar cada mensagem, mas fornece um mecanismo para falhar para uma região secundária em caso de desastre.  A Azure Functions trabalha ao lado [da geo-recuperação do autocarro da Azure Service](../service-bus-messaging/service-bus-geo-dr.md) e [da geo-recuperação do Azure Event Hubs.](../event-hubs/event-hubs-geo-dr.md)

A utilização de hubs de eventos Azure como exemplo, o padrão ativo/passivo envolveria o seguinte:

* Azure Event Hub implantado para uma região primária e secundária.
* Geo-desastre habilitado a emparelhar o centro de eventos primário e secundário.  Isto também cria um "pseudónimo" que pode usar para ligar aos centros de eventos e mudar de primário para secundário sem alterar a informação de ligação.
* Aplicativos de função implantados para uma região primária e secundária.
* As aplicações de função estão a ativar a cadeia de ligação *direta* (não-alias) para o seu respetivo centro de eventos. 
* Os editores do centro de eventos devem publicar para a cadeia de conexão de pseudónimos. 

![Arquitetura de exemplo activo-passiva](media/functions-geo-dr/active-passive.png)

Antes do failover, os editores que enviam para o pseudónimo partilhado irão para o centro de eventos primários.  A aplicação de função primária está a ouvir exclusivamente o centro de eventos primário.  A aplicação de função secundária será passiva e inativa.  Assim que o failover for iniciado, os editores que enviam para o pseudónimo partilhado vão agora encaminhar-se para o centro de eventos secundário.  A aplicação de função secundária passará a estar ativa e começará a ativar-se automaticamente.  A eficaz falha para uma região secundária pode ser conduzida inteiramente a partir do centro de eventos, com as funções a tornarem-se ativas apenas quando o respetivo centro de eventos estiver ativo.

Leia mais sobre informações e considerações para failover com [Service Bus](../service-bus-messaging/service-bus-geo-dr.md) e centros [de eventos.](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Passos seguintes

* [Criar porta frontal azul](../frontdoor/quickstart-create-front-door.md)
* [Event Hubs failover considerações](../event-hubs/event-hubs-geo-dr.md#considerations)
