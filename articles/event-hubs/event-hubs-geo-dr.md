---
title: Recuperação após desastre geográfico - Event Hubs do Azure | Documentos da Microsoft
description: Como usar regiões geográficas para ativação pós-falha e efetuar a recuperação de desastres nos Hubs de eventos do Azure
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 56077d018c1ae62809d51fc66d7f5aff93fb4c02
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002702"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Hubs de eventos do Azure - recuperação após desastre geográfico 

Quando todo regiões do Azure ou de centros de dados (se não [zonas de disponibilidade](../availability-zones/az-overview.md) servem) sofrer períodos de inatividade, é fundamental para processamento de dados continuar a funcionar numa região diferente ou datacenter. Como tal, *recuperação após desastre geográfico* e *georreplicação* são recursos importantes para todas as empresas. Os Hubs de eventos do Azure suporta a recuperação após desastre geográfico e georreplicação, ao nível do espaço de nomes. 

O recurso de recuperação após desastre geográfico é globalmente disponível para o SKU Standard de Hubs de eventos.

## <a name="outages-and-disasters"></a>Interrupções e desastres

É importante observar a distinção entre "falhas" e "desastres." Uma *indisponibilidade* é a indisponibilidade temporária de Event Hubs do Azure e pode afetar alguns componentes do serviço, como um arquivo de mensagens ou até mesmo todo o datacenter. No entanto, depois de corrigido o problema, os Hubs de eventos ficar novamente disponível. Normalmente, uma indisponibilidade não irá causar a perda de mensagens ou outros dados. Um exemplo de um período de indisponibilidade, pode ser uma falha de energia no Centro de dados. Algumas falhas são apenas as perdas de ligação abreviada devido a problemas de rede ou transitório. 

R *desastre* é definido como a perda permanente ou longo prazo de um cluster dos Hubs de eventos, a região do Azure ou o Centro de dados. A região ou o Centro de dados poderá ou pode não se tornar disponível novamente ou pode estar inativa para horas ou dias. Exemplos de tais desastres são fire, sobrecarregar ou sismo. Um desastre que se torna permanente poderão causar a perda de algumas mensagens, eventos ou outros dados. No entanto, na maioria dos casos, não se deve haver sem perda de dados e as mensagens podem ser recuperadas depois do Centro de dados de cópia de segurança.

O recurso de recuperação após desastre geográfico dos Hubs de eventos do Azure é uma solução de recuperação após desastre. Os conceitos e o fluxo de trabalho descrito neste artigo aplicam-se para cenários de desastre e não para as falhas transitórias ou temporárias. Para obter uma discussão detalhada da recuperação de desastres no Microsoft Azure, consulte [este artigo](/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="basic-concepts-and-terms"></a>Conceitos e termos básicos

O recurso de recuperação de desastres implementa a recuperação após desastre de metadados e baseia-se nos espaços de nomes de recuperação de desastres primário e secundário. Tenha em atenção que o recurso de recuperação após desastre geográfico está disponível para o [Standard SKU](https://azure.microsoft.com/pricing/details/event-hubs/) apenas. Não é necessário efetuar quaisquer alterações de cadeia de ligação, como a conexão é feita por meio de um alias.

Os termos seguintes são utilizados neste artigo:

-  *Alias*: O nome de uma configuração de recuperação após desastre que configurou. O alias fornece uma única cadeia de ligação de domínio completamente qualificado nome (FQDN) estável. Aplicações utilizam esta cadeia de ligação de alias para ligar a um espaço de nomes. 

-  *Espaço de nomes de primária/secundária*: Os espaços de nomes que correspondem para o alias. O espaço de nomes principal está "ativo" e recebe mensagens (pode ser um espaço de nomes novo ou existente). O espaço de nomes secundário é "passivo" e não a receber mensagens. Os metadados entre ambos estão em sincronização, para que ambos forma totalmente integrada podem aceitar mensagens sem quaisquer alterações de cadeia de ligação ou código da aplicação. Para garantir que apenas o espaço de nomes ativo recebe mensagens, tem de utilizar o alias. 

-  *Metadados*: Entidades como os hubs de eventos e os grupos de consumidores e as respetivas propriedades do serviço que estão associadas com o espaço de nomes. Tenha em atenção que apenas as entidades e suas configurações são replicadas automaticamente. Mensagens e os eventos não são replicados. 

-  *Ativação pós-falha*: O processo de ativação do espaço de nomes secundário.

## <a name="setup-and-failover-flow"></a>Configuração e ativação pós-falha do fluxo

A seção a seguir uma visão geral do processo de ativação pós-falha e explica como configurar a ativação pós-falha inicial. 

![1][]

### <a name="setup"></a>Configurar

Primeiro cria ou utiliza um espaço de nomes de principal existente e um novo espaço de nomes secundário, em seguida, emparelhe os dois. Esse emparelhamento dá-lhe um alias que pode utilizar para ligar. Uma vez que utilizar um alias, não é necessário que alterar as cadeias de ligação. Apenas novos namespaces podem ser adicionados a seu emparelhamento de ativação pós-falha. Por fim, deve adicionar alguma monitorização para detetar se uma ativação pós-falha é necessária. Na maioria dos casos, o serviço é uma parte de um vasto ecossistema, portanto, os failovers automáticos são raramente possíveis, como, com muita frequência as ativações pós-falha devem ser executadas em sincronia com o subsistema de restantes ou a infraestrutura.

### <a name="example"></a>Exemplo

Um exemplo deste cenário, considere uma solução de ponto de venda (POS) que emite mensagens ou eventos. Os Hubs de eventos passa esses eventos para alguns solução reformatação ou de mapeamento, que, em seguida, encaminha dados mapeados para outro sistema para processamento adicional. Nessa altura, todos esses sistemas podem ser hospedados na mesma região do Azure. A decisão sobre quando e quais parte para efetuar a ativação pós-falha depende do fluxo de dados na sua infraestrutura. 

Pode automatizar a ativação pós-falha com sistemas de monitorização ou com soluções de monitorização personalizadas. No entanto, essa automatização demora planejamento adicional e de trabalho, o que está fora do escopo deste artigo.

### <a name="failover-flow"></a>Fluxo de ativação pós-falha

Se iniciar a ativação pós-falha, as duas etapas são necessárias:

1. Se ocorrer outro tipo, quer poder para ativação pós-falha novamente. Por conseguinte, configurar outro espaço de nomes passivo e atualize o emparelhamento. 

2. Extraem as mensagens de espaço de nomes primário primeiro quando estiver disponível novamente. Depois disso, utilizar esse espaço de nomes para mensagens regular fora da sua configuração de recuperação georredundante ou eliminar o espaço de nomes principal antigo.

> [!NOTE]
> É suportada apenas semântica encaminhamento de ativação. Neste cenário, a ativação pós-falha e, em seguida, novamente, emparelhe com um novo espaço de nomes. Não é suportada a reativação pós-falha; Por exemplo, num cluster de SQL. 

![2][]

## <a name="management"></a>Gestão

Se tiver efetuado um erro; Por exemplo, emparelhado as regiões de errado durante a configuração inicial, pode quebrar o emparelhamento dos dois espaços de nomes em qualquer altura. Se pretender utilizar espaços de nomes emparelhados como espaços de nomes normais, elimine o alias.

## <a name="samples"></a>Amostras

O [exemplo no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) mostra como configurar e iniciar uma ativação pós-falha. Este exemplo demonstra os seguintes conceitos:

- Definições necessárias no Azure Active Directory para utilizar o Azure Resource Manager com os Hubs de eventos. 
- Passos necessários para executar o código de exemplo. 
- Enviar e receber de espaço de nomes primário atual. 

## <a name="considerations"></a>Considerações

Tenha em atenção as seguintes considerações a ter em conta com esta versão:

1. No seu planeamento de ativação pós-falha, também deve considerar o fator de tempo. Por exemplo, se perder a conectividade durante mais de 15 a 20 minutos, pode decidir iniciar a ativação pós-falha. 
 
2. O fato de que não existem dados são replicados significa que atualmente sessões ativas não são replicadas. Além disso, deteção de duplicados e de mensagens agendadas poderão não funcionar. Novas sessões de mensagens agendadas e duplicados novo irão funcionar. 

3. Realizar a ativação pós-falha de uma infraestrutura distribuída complexa deve estar [rehearsed](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) , pelo menos, uma vez. 

4. Sincronização de entidades pode demorar algum tempo, aproximadamente 50 a 100 entidades por minuto.

## <a name="availability-zones"></a>Zonas de Disponibilidade 

Suporta o SKU Standard dos Hubs de eventos [zonas de disponibilidade](../availability-zones/az-overview.md), fornecer localizações isoladas de falhas dentro de uma região do Azure. 

> [!NOTE]
> O suporte de zonas de disponibilidade para o padrão de Hubs de eventos do Azure só está disponível no [regiões do Azure](../availability-zones/az-overview.md#services-support-by-region) onde as zonas de disponibilidade estão presentes.

Pode ativar as zonas de disponibilidade nos novos espaços de nomes apenas, com o portal do Azure. Os Hubs de eventos não suporta a migração de espaços de nomes existentes. Não é possível desativar a redundância de zona após ativá-la no seu espaço de nomes.

![3][]

## <a name="next-steps"></a>Passos Seguintes

* O [exemplo no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/GeoDRClient) percorre um fluxo de trabalho simple que cria uma combinação de georreplicação e inicia uma ativação pós-falha para um cenário de recuperação de desastres.
* O [referência da REST API](/rest/api/eventhub/disasterrecoveryconfigs) descreve as APIs para executar a configuração da recuperação após desastre geográfico.

Para obter mais informações sobre os Hubs de Eventos, visite as seguintes ligações:

* Introdução a um [Tutorial dos Event Hubs](event-hubs-dotnet-standard-getstarted-send.md)
* [FAQ dos Hubs de Eventos](event-hubs-faq.md)
* [Aplicações de exemplo que utilizam Hubs de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png