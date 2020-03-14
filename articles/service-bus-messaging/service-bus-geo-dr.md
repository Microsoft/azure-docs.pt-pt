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
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79259581"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Recuperação geo-desastre do azure service bus

Quando regiões ou centros de dados inteiros do Azure (se não forem [utilizadas zonas](../availability-zones/az-overview.md) de disponibilidade) experimentam tempo de inatividade, é fundamental que o processamento de dados continue a operar numa região ou datacenter diferente. Como tal, a *recuperação geo-desastre* é uma característica importante para qualquer empresa. O Azure Service Bus apoia a recuperação geo-desastre a nível do espaço de nome.

A funcionalidade de recuperação de geo-desastres está globalmente disponível para o Service Bus Premium SKU. 

>[!NOTE]
> A recuperação geo-desastre atualmente apenas garante que os metadados (Filas, Tópicos, Subscrições, Filtros) são copiados do espaço de nome principal para o espaço de nome secundário quando emparelhados.

## <a name="outages-and-disasters"></a>Interrupções e desastres

É importante observar a distinção entre "falhas" e "desastres." 

Uma *paralisação* é a indisponibilidade temporária do Azure Service Bus, e pode afetar alguns componentes do serviço, como uma loja de mensagens, ou mesmo todo o datacenter. No entanto, depois de o problema ser corrigido, o Service Bus volta a estar disponível. Normalmente, uma indisponibilidade não irá causar a perda de mensagens ou outros dados. Um exemplo de um período de indisponibilidade, pode ser uma falha de energia no Centro de dados. Algumas falhas são apenas as perdas de ligação abreviada devido a problemas de rede ou transitório. 

Uma *catástrofe* é definida como a perda permanente, ou a longo prazo, de um cluster de ônibus de serviço, região de Azure ou datacenter. A região ou o Centro de dados poderá ou pode não se tornar disponível novamente ou pode estar inativa para horas ou dias. Exemplos de tais desastres são fire, sobrecarregar ou sismo. Um desastre que se torna permanente poderão causar a perda de algumas mensagens, eventos ou outros dados. No entanto, na maioria dos casos, não se deve haver sem perda de dados e as mensagens podem ser recuperadas depois do Centro de dados de cópia de segurança.

A funcionalidade de recuperação de geo-desastres do Azure Service Bus é uma solução de recuperação de desastres. Os conceitos e o fluxo de trabalho descrito neste artigo aplicam-se para cenários de desastre e não para as falhas transitórias ou temporárias. Para uma discussão detalhada sobre a recuperação de desastres no Microsoft Azure, consulte [este artigo](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Conceitos e termos básicos

O recurso de recuperação de desastres implementa a recuperação após desastre de metadados e baseia-se nos espaços de nomes de recuperação de desastres primário e secundário. Note que a funcionalidade de recuperação de geo-desastres está disponível apenas para o [Premium SKU.](service-bus-premium-messaging.md) Não é necessário efetuar quaisquer alterações de cadeia de ligação, como a conexão é feita por meio de um alias.

Os termos seguintes são utilizados neste artigo:

-  *Pseudónimo*: O nome para uma configuração de recuperação de desastres que configura. O alias fornece uma única cadeia de ligação de domínio completamente qualificado nome (FQDN) estável. Aplicações utilizam esta cadeia de ligação de alias para ligar a um espaço de nomes. A utilização de um pseudónimo garante que a cadeia de ligação se mantém inalterada quando a falha é ativada.

-  Espaço de *nome primário/secundário*: Os espaços de nome que correspondem ao pseudónimo. O espaço de nomes principal está "ativo" e recebe mensagens (pode ser um espaço de nomes novo ou existente). O espaço de nomes secundário é "passivo" e não a receber mensagens. Os metadados entre ambos estão em sincronização, para que ambos forma totalmente integrada podem aceitar mensagens sem quaisquer alterações de cadeia de ligação ou código da aplicação. Para garantir que apenas o espaço de nomes ativo recebe mensagens, tem de utilizar o alias. 

-  *Metadados*: Entidades como filas, tópicos e subscrições; e as suas propriedades do serviço que estão associados ao espaço de nome. Tenha em atenção que apenas as entidades e suas configurações são replicadas automaticamente. As mensagens não são replicadas.

-  *Failover*: O processo de ativação do espaço de nome secundário.

## <a name="setup"></a>Configurar

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

## <a name="failover-flow"></a>Fluxo de ativação pós-falha

Uma falha é desencadeada manualmente pelo cliente (seja explicitamente através de um comando, ou através da lógica comercial do cliente que desencadeia o comando) e nunca pelo Azure. Isto dá ao cliente total propriedade e visibilidade para resolução de paralisação na espinha dorsal do Azure.

![4][]

Depois que a falha é desencadeada -

1. A cadeia de ligação ***do pseudónimo*** é atualizada para apontar para o espaço de nome secundário Premium.

2. Os clientes (remetentes e recetores) ligam-se automaticamente ao espaço de nome secundário.

3. O emparelhamento existente entre o espaço de nome sal superior primário e secundário está quebrado.

Uma vez iniciada a falha -

1. Se ocorrer outra paragem, poderá falhar novamente. Por conseguinte, configurar outro espaço de nomes passivo e atualize o emparelhamento. 

2. Extraem as mensagens de espaço de nomes primário primeiro quando estiver disponível novamente. Depois disso, utilizar esse espaço de nomes para mensagens regular fora da sua configuração de recuperação georredundante ou eliminar o espaço de nomes principal antigo.

> [!NOTE]
> É suportada apenas semântica encaminhamento de ativação. Neste cenário, a ativação pós-falha e, em seguida, novamente, emparelhe com um novo espaço de nomes. Não é suportada a reativação pós-falha; Por exemplo, num cluster de SQL. 

Pode automatizar a ativação pós-falha com sistemas de monitorização ou com soluções de monitorização personalizadas. No entanto, essa automatização demora planejamento adicional e de trabalho, o que está fora do escopo deste artigo.

![2][]

## <a name="management"></a>Gestão

Se tiver efetuado um erro; Por exemplo, emparelhado as regiões de errado durante a configuração inicial, pode quebrar o emparelhamento dos dois espaços de nomes em qualquer altura. Se pretender utilizar espaços de nomes emparelhados como espaços de nomes normais, elimine o alias.

## <a name="use-existing-namespace-as-alias"></a>Use o espaço de nome existente como pseudónimo

Se tiver um cenário em que não possa alterar as ligações de produtores e consumidores, pode reutilizar o nome do seu nome de espaço de nome como nome de pseudónimo. Veja aqui o código da [amostra no GitHub.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name)

## <a name="samples"></a>Amostras

As [amostras no GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) mostram como configurar e iniciar uma falha. Estas amostras demonstram os seguintes conceitos:

- Uma amostra .NET e configurações que são necessárias no Diretório Ativo Azure para usar o Gestor de Recursos Azure com o Service Bus, para configurar e permitir a recuperação de geo-desastres.
- Passos necessários para executar o código de exemplo.
- Como usar um espaço de nome existente como pseudónimo.
- Passos para permitir a recuperação de geo-desastres via PowerShell ou CLI.
- [Envie e receba](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) do atual espaço de nomeprimário ou secundário utilizando o pseudónimo.

## <a name="considerations"></a>Considerações

Tenha em atenção as seguintes considerações a ter em conta com esta versão:

1. No seu planeamento de ativação pós-falha, também deve considerar o fator de tempo. Por exemplo, se perder a conectividade durante mais de 15 a 20 minutos, pode decidir iniciar a ativação pós-falha.

2. O fato de que não existem dados são replicados significa que atualmente sessões ativas não são replicadas. Além disso, deteção de duplicados e de mensagens agendadas poderão não funcionar. Novas sessões, novas mensagens agendadas e novos duplicados funcionarão. 

3. A falta de uma infraestrutura distribuída complexa deve ser [ensaiada](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) pelo menos uma vez.

4. Sincronização de entidades pode demorar algum tempo, aproximadamente 50 a 100 entidades por minuto. As assinaturas e regras também contam como entidades.

## <a name="availability-zones"></a>Zonas de Disponibilidade

O Service Bus Premium SKU também suporta Zonas de [Disponibilidade,](../availability-zones/az-overview.md)fornecendo localizações isoladas de falhas dentro de uma região do Azure.

> [!NOTE]
> O suporte das Zonas de Disponibilidade para o Azure Service Bus Premium só está disponível nas [regiões do Azure](../availability-zones/az-overview.md#services-support-by-region) onde estão presentes zonas de disponibilidade.

Pode ativar as zonas de disponibilidade nos novos espaços de nomes apenas, com o portal do Azure. A Service Bus não suporta a migração de espaços de nome existentes. Não é possível desativar a redundância de zona após ativá-la no seu espaço de nomes.

![3][]

## <a name="next-steps"></a>Passos Seguintes

- Consulte aqui a referência da Recuperação Geo-desastre [REST API](/rest/api/servicebus/disasterrecoveryconfigs).
- Execute a amostra de recuperação de geo-desastres [no GitHub.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2)
- Veja a amostra de recuperação de geo-desastres [que envia mensagens para um pseudónimo.](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1)

Para saber mais sobre as mensagens de ônibus de serviço, consulte os seguintes artigos:

* [Filas, tópicos e subscrições do Service Bus](service-bus-queues-topics-subscriptions.md)
* [Introdução às filas do Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Como utilizar os tópicos e as subscrições do Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [API de descanso](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
