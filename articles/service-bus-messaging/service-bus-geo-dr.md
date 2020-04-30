---
title: Recuperação geo-desastre do Azure Service Bus Microsoft Docs
description: Como utilizar as regiões geográficas para falhar e realizar a recuperação de desastres no Azure Service Bus
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 49748006baf779e6aea4322068ca3bd07a03a0a3
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209405"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Recuperação geo-desastre do azure service bus

Quando regiões ou centros de dados inteiros do Azure (se não forem [utilizadas zonas](../availability-zones/az-overview.md) de disponibilidade) experimentam tempo de inatividade, é fundamental que o processamento de dados continue a operar numa região ou datacenter diferente. Como tal, a *recuperação geo-desastre* é uma característica importante para qualquer empresa. O Azure Service Bus apoia a recuperação geo-desastre a nível do espaço de nome.

A funcionalidade de recuperação de geo-desastres está globalmente disponível para o Service Bus Premium SKU. 

>[!NOTE]
> A recuperação geo-desastre atualmente apenas garante que os metadados (Filas, Tópicos, Subscrições, Filtros) são copiados do espaço de nome principal para o espaço de nome secundário quando emparelhados.

## <a name="outages-and-disasters"></a>Interrupções e desastres

É importante notar a distinção entre "interrupções" e "desastres". 

Uma *paralisação* é a indisponibilidade temporária do Azure Service Bus, e pode afetar alguns componentes do serviço, como uma loja de mensagens, ou mesmo todo o datacenter. No entanto, depois de o problema ser corrigido, o Service Bus volta a estar disponível. Normalmente, uma interrupção não causa a perda de mensagens ou outros dados. Um exemplo de tal falha pode ser uma falha de energia no datacenter. Algumas interrupções são apenas perdas de ligação curtas devido a problemas transitórios ou de rede. 

Uma *catástrofe* é definida como a perda permanente, ou a longo prazo, de um cluster de ônibus de serviço, região de Azure ou datacenter. A região ou datacenter pode ou não voltar a estar disponível, ou pode ficar parada durante horas ou dias. Exemplos de tais desastres são incêndios, inundações ou terramotos. Um desastre que se torna permanente pode causar a perda de algumas mensagens, eventos ou outros dados. No entanto, na maioria dos casos não deve haver perda de dados e as mensagens podem ser recuperadas uma vez que o centro de dados está de volta.

A funcionalidade de recuperação de geo-desastres do Azure Service Bus é uma solução de recuperação de desastres. Os conceitos e fluxos de trabalho descritos neste artigo aplicam-se a cenários de catástrofe, e não a interrupções transitórias ou temporárias. Para uma discussão detalhada sobre a recuperação de desastres no Microsoft Azure, consulte [este artigo](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Conceitos e termos básicos

O recurso de recuperação de desastres implementa a recuperação de desastres de metadados e baseia-se em espaços de recuperação de desastres primários e secundários. Note que a funcionalidade de recuperação de geo-desastres está disponível apenas para o [Premium SKU.](service-bus-premium-messaging.md) Não é necessário efazer alterações nas cordas de ligação, uma vez que a ligação é feita através de um pseudónimo.

Neste artigo são utilizados os seguintes termos:

-  *Pseudónimo*: O nome para uma configuração de recuperação de desastres que configura. O pseudónimo fornece uma única linha de ligação estável de nome de domínio totalmente qualificado (FQDN). As aplicações utilizam esta cadeia de ligação de pseudónimo para se ligar a um espaço de nome. A utilização de um pseudónimo garante que a cadeia de ligação se mantém inalterada quando a falha é ativada.

-  Espaço de *nome primário/secundário*: Os espaços de nome que correspondem ao pseudónimo. O espaço de nome principal é "ativo" e recebe mensagens (este pode ser um espaço de nome existente ou novo). O espaço de nome secundário é "passivo" e não recebe mensagens. Os metadados entre ambos estão sincronizados, para que ambos possam aceitar sem problemas sem qualquer código de aplicação ou alterações nas cordas de ligação. Para garantir que apenas o espaço de nome ativo recebe mensagens, deve utilizar o pseudónimo. 

-  *Metadados*: Entidades como filas, tópicos e subscrições; e as suas propriedades do serviço que estão associados ao espaço de nome. Note que apenas as entidades e as suas definições são replicadas automaticamente. As mensagens não são replicadas.

-  *Failover*: O processo de ativação do espaço de nome secundário.

## <a name="setup"></a>Configuração

A secção seguinte é uma visão geral para configurar o emparelhamento entre os espaços de nome.

![1][]

O processo de configuração é o seguinte -

1. Fornecer um espaço de nome premium de ônibus de serviço ***primário.***

2. Provisão de um espaço de nome premium de ônibus de serviço ***secundário*** numa região diferente da qual o espaço de *nome principal é provisionado*. Isto ajudará a permitir o isolamento de falhas em diferentes regiões de datacenter.

3. Crie emparelhamento entre o espaço de nome principal e o espaço de nome secundário para obter o ***pseudónimo***.

    >[!NOTE] 
    > Se [emigrou o seu espaço de nome Azure Service Bus Standard para o Azure Service Bus Premium,](service-bus-migrate-standard-premium.md)então deve utilizar o pseudónimo pré-existente (isto é, a sua cadeia de ligação de nome Standard de Ônibus) para criar a configuração de recuperação de desastres através da **API PS/CLI** ou **REST**.
    >
    >
    > Isto porque, durante a migração, o seu nome de ligação padrão de ligação de ônibus de serviço Azure Standard/DNS em si torna-se um pseudónimo para o seu espaço de nome Azure Service Bus Premium.
    >
    > As suas aplicações de clientes devem utilizar este pseudónimo (isto é, a cadeia de ligação de nome Sem Nome Azure Service Bus Standard) para se ligar ao espaço de nome Premium onde foi configurado o emparelhamento de recuperação de desastres.
    >
    > Se utilizar o Portal para configurar a configuração de recuperação de desastres, então o portal irá abstrair esta ressalva de si.


4. Utilize o ***pseudónimo*** obtido no passo 3 para ligar as aplicações do seu cliente ao espaço de nome primário ativado pelo Geo-DR. Inicialmente, o pseudónimo aponta para o espaço de nome principal.

5. [Opcional] Adicione um pouco de monitorização para detetar se for necessária uma falha.

## <a name="failover-flow"></a>Fluxo de failover

Uma falha é desencadeada manualmente pelo cliente (seja explicitamente através de um comando, ou através da lógica comercial do cliente que desencadeia o comando) e nunca pelo Azure. Isto dá ao cliente total propriedade e visibilidade para resolução de paralisação na espinha dorsal do Azure.

![4][]

Depois que a falha é desencadeada -

1. A cadeia de ligação ***do pseudónimo*** é atualizada para apontar para o espaço de nome secundário Premium.

2. Os clientes (remetentes e recetores) ligam-se automaticamente ao espaço de nome secundário.

3. O emparelhamento existente entre o espaço de nome sal superior primário e secundário está quebrado.

Uma vez iniciada a falha -

1. Se ocorrer outra paragem, poderá falhar novamente. Por isso, configurar outro espaço de nome passivo e atualizar o emparelhamento. 

2. Retire as mensagens do antigo espaço de nome primário uma vez que esteja disponível novamente. Depois disso, utilize esse espaço de nome para mensagens regulares fora da sua configuração de geo-recuperação ou elimine o antigo espaço de nome primário.

> [!NOTE]
> Só a semântica falhada para a frente é apoiada. Neste cenário, falha-se e depois reemparelha-se com um novo espaço de nome. A reprovação não é suportada; por exemplo, num cluster SQL. 

Pode automatizar a falha, quer com sistemas de monitorização, quer com soluções de monitorização personalizadas. No entanto, essa automatização requer planeamento e trabalho extra, o que está fora do âmbito deste artigo.

![2][]

## <a name="management"></a>Gestão

Se cometeu um erro; por exemplo, emparelhou as regiões erradas durante a configuração inicial, pode quebrar o emparelhamento dos dois espaços de nome a qualquer momento. Se pretender utilizar os espaços de nome emparelhados como espaços de nome regulares, elimine o pseudónimo.

## <a name="use-existing-namespace-as-alias"></a>Use o espaço de nome existente como pseudónimo

Se tiver um cenário em que não possa alterar as ligações de produtores e consumidores, pode reutilizar o nome do seu nome de espaço de nome como nome de pseudónimo. Veja aqui o código da [amostra no GitHub.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name)

## <a name="samples"></a>Amostras

As [amostras no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) mostram como configurar e iniciar uma falha. Estas amostras demonstram os seguintes conceitos:

- Uma amostra .NET e configurações que são necessárias no Diretório Ativo Azure para usar o Gestor de Recursos Azure com o Service Bus, para configurar e permitir a recuperação de geo-desastres.
- Passos necessários para executar o código da amostra.
- Como usar um espaço de nome existente como pseudónimo.
- Passos para permitir a recuperação de geo-desastres via PowerShell ou CLI.
- [Envie e receba](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) do atual espaço de nomeprimário ou secundário utilizando o pseudónimo.

## <a name="considerations"></a>Considerações

Note as seguintes considerações a ter em conta com esta libertação:

1. No seu planeamento falhado, também deve considerar o fator tempo. Por exemplo, se perder conectividade por mais de 15 a 20 minutos, poderá decidir iniciar a falha.

2. O facto de não serem replicados dados significa que as sessões atualmente ativas não são replicadas. Além disso, a deteção duplicada e as mensagens programadas podem não funcionar. Novas sessões, novas mensagens agendadas e novos duplicados funcionarão. 

3. A falta de uma infraestrutura distribuída complexa deve ser [ensaiada](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) pelo menos uma vez.

4. Entidades sincronizadoras podem demorar algum tempo, aproximadamente 50-100 entidades por minuto. As assinaturas e regras também contam como entidades.

## <a name="availability-zones"></a>Zonas de Disponibilidade

O Service Bus Premium SKU também suporta Zonas de [Disponibilidade,](../availability-zones/az-overview.md)fornecendo localizações isoladas de falhas dentro de uma região do Azure.

> [!NOTE]
> O suporte das Zonas de Disponibilidade para o Azure Service Bus Premium só está disponível nas [regiões do Azure](../availability-zones/az-region.md) onde estão presentes zonas de disponibilidade.

Só pode ativar Zonas de Disponibilidade em novos espaços de nome, utilizando o portal Azure. A Service Bus não suporta a migração de espaços de nome existentes. Não é possível desativar a redundância da zona depois de a ter habilitado para o seu espaço de nome.

![3][]

## <a name="next-steps"></a>Passos seguintes

- Consulte aqui a referência da Recuperação Geo-desastre [REST API](/rest/api/servicebus/disasterrecoveryconfigs).
- Execute a amostra de recuperação de geo-desastres [no GitHub.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)
- Veja a amostra de recuperação de geo-desastres [que envia mensagens para um pseudónimo.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)

Para saber mais sobre as mensagens de ônibus de serviço, consulte os seguintes artigos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Começar com as filas de ônibus de serviço](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API de descanso](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
