---
title: Visão geral do serviço de análise de falha
description: Este artigo descreve o serviço de análise de falha em Service Fabric para falhas de induzir e cenários de teste em execução em seus serviços.
author: anmolah
ms.topic: conceptual
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: d5c770a4d823ebe9b2700b081c407c54dd1d18a3
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75465567"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Introdução ao serviço de análise de falha
O serviço de análise de falha foi projetado para serviços de teste criados em Microsoft Azure Service Fabric. Com o serviço de análise de falha, você pode induzir falhas significativas e executar cenários de teste completos em seus aplicativos. Essas falhas e cenários exercitam e validam os inúmeros Estados e as transições que um serviço experimentará durante seu tempo de vida, tudo de maneira controlada, segura e consistente.

As ações são as falhas individuais que visam um serviço para testá-lo. Um desenvolvedor de serviços pode usá-los como blocos de construção para escrever cenários complicados. Por exemplo:

* Reinicie um nó para simular qualquer número de situações em que um computador ou VM é reinicializado.
* Mova uma réplica de seu serviço com estado para simular o balanceamento de carga, failover ou atualização de aplicativo.
* Invoque a perda de quorum em um serviço com estado para criar uma situação em que as operações de gravação não podem continuar porque não há réplicas "backup" ou "secundária" suficientes para aceitar novos dados.
* Invoque a perda de dados em um serviço com estado para criar uma situação em que todo o estado na memória seja completamente apagado.

Cenários são operações complexas compostas de uma ou mais ações. O serviço de análise de falha fornece dois cenários completos internos:

* Cenário de caos
* Cenário de failover

## <a name="testing-as-a-service"></a>Testando como um serviço
O serviço de análise de falha é um serviço de sistema Service Fabric que é iniciado automaticamente com um Cluster Service Fabric. Esse serviço atua como o host para injeção de falha, execução de cenário de teste e análise de integridade. 

![Serviço de análise de falha][0]

Quando um cenário de ação ou teste de falha é iniciado, um comando é enviado para o serviço de análise de falha para executar a ação de falha ou o cenário de teste. O serviço de análise de falha tem monitoração de estado para que possa executar falhas e cenários de forma confiável e validar os resultados. Por exemplo, um cenário de teste de execução longa pode ser executado de forma confiável pelo serviço de análise de falha. E como os testes estão sendo executados dentro do cluster, o serviço pode examinar o estado do cluster e seus serviços para fornecer informações mais detalhadas sobre falhas.

## <a name="testing-distributed-systems"></a>Testando sistemas distribuídos
Service Fabric torna significativamente mais fácil o trabalho de escrever e gerenciar aplicativos escalonáveis distribuídos. O serviço de análise de falha facilita o teste de um aplicativo distribuído de forma semelhante. Há três problemas principais que precisam ser resolvidos durante o teste:

1. Simulando/gerando falhas que podem ocorrer em cenários do mundo real: um dos aspectos importantes do Service Fabric é que ele permite que aplicativos distribuídos se recuperem de várias falhas. No entanto, para testar se o aplicativo é capaz de se recuperar dessas falhas, precisamos de um mecanismo para simular/gerar essas falhas do mundo real em um ambiente de teste controlado.
1. A capacidade de gerar falhas correlacionadas: falhas básicas no sistema, como falhas de rede e falhas de máquina, são fáceis de produzir individualmente. Gerar um número significativo de cenários que podem ocorrer no mundo real como resultado das interações dessas falhas individuais é não trivial.
1. Experiência unificada em vários níveis de desenvolvimento e implantação: há muitos sistemas de injeção de falhas que podem fazer vários tipos de falhas. No entanto, a experiência em todos eles é ruim ao migrar de cenários de desenvolvedor de uma caixa, para executar os mesmos testes em grandes ambientes de teste, para usá-los para testes em produção.

Embora existam muitos mecanismos para resolver esses problemas, um sistema que faz o mesmo com as garantias necessárias – todo o caminho de um ambiente de desenvolvedor de uma caixa, para teste em clusters de produção, está ausente. O serviço de análise de falha ajuda os desenvolvedores de aplicativos a se concentrarem em testar sua lógica de negócios. O serviço de análise de falha fornece todos os recursos necessários para testar a interação do serviço com o sistema distribuído subjacente.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulando/gerando cenários de falha do mundo real
Para testar a robustez de um sistema distribuído contra falhas, precisamos de um mecanismo para gerar falhas. Embora, teoricamente, gerar uma falha como um nó inoperante pareça fácil, ele começa a atingir o mesmo conjunto de problemas de consistência que Service Fabric está tentando resolver. Por exemplo, se quisermos desligar um nó, o fluxo de trabalho necessário é o seguinte:

1. No cliente, emita uma solicitação de desligamento de nó.
1. Envie a solicitação para o nó correto.
   
    a. Se o nó não for encontrado, ele deverá falhar.
   
    b. Se o nó for encontrado, ele deverá retornar somente se o nó for desligado.

Para verificar a falha de uma perspectiva de teste, o teste precisa saber que quando essa falha é induzida, a falha realmente acontece. A garantia de que o Service Fabric fornece é que o nó ficará inativo ou já estará inoperante quando o comando atingir o nó. Em ambos os casos, o teste deve ser capaz de ter um motivo correto sobre o estado e ter êxito ou falhar corretamente em sua validação. Um sistema implementado fora do Service Fabric para fazer o mesmo conjunto de falhas pode atingir muitos problemas de rede, hardware e software, o que impediria que ele fornecesse as garantias anteriores. Na presença dos problemas descritos anteriormente, Service Fabric reconfigurará o estado do cluster para solucionar os problemas e, portanto, o serviço de análise de falha ainda poderá fornecer o conjunto certo de garantias.

### <a name="generating-required-events-and-scenarios"></a>Gerando cenários e eventos necessários
Embora a simulação consistente de uma falha do mundo real seja difícil de começar, a capacidade de gerar falhas correlacionadas é ainda mais difícil. Por exemplo, uma perda de dados ocorre em um serviço persistente com estado quando ocorrem os seguintes itens:

1. Somente um quorum de gravação das réplicas é capturado na replicação. Todas as réplicas secundárias ficam atrasadas por trás do primário.
1. O quorum de gravação fica inativo devido às réplicas ficarem inativas (devido a um pacote de códigos ou a um nó ficar inativo).
1. O quorum de gravação não pode ser revertido porque os dados das réplicas são perdidos (devido à corrupção do disco ou à recriação de imagens da máquina).

Essas falhas correlacionadas acontecem no mundo real, mas não com tanta frequência quanto falhas individuais. A capacidade de testar esses cenários antes que eles ocorram na produção é fundamental. Ainda mais importante é a capacidade de simular esses cenários com cargas de trabalho de produção em circunstâncias controladas (no meio do dia com todos os engenheiros do baralho). Isso é muito melhor do que fazer com que ele aconteça pela primeira vez em produção às 2:00 da manhã

### <a name="unified-experience-across-different-environments"></a>Experiência unificada em diferentes ambientes
Tradicionalmente, a prática tem sido criar três conjuntos diferentes de experiências, um para o ambiente de desenvolvimento, um para testes e outro para produção. O modelo era:

1. No ambiente de desenvolvimento, produzir transições de estado que permitem testes de unidade de métodos individuais.
1. No ambiente de teste, produza falhas para permitir testes de ponta a ponta que exercitam vários cenários de falha.
1. Mantenha o ambiente de produção original para evitar falhas não naturais e para garantir que haja uma resposta humana extremamente rápida para a falha.

Em Service Fabric, por meio do serviço de análise de falha, estamos propondo para ativar isso e usar a mesma metodologia do ambiente de desenvolvedor para a produção. Há duas maneiras de conseguir isso:

1. Para induzir falhas controladas, use as APIs do serviço de análise de falha de um ambiente de uma caixa até os clusters de produção.
1. Para dar ao cluster um febre que cause indução automática de falhas, use o serviço de análise de falha para gerar falhas automáticas. O controle da taxa de falhas por meio da configuração permite que o mesmo serviço seja testado de forma diferente em ambientes diferentes.

Com Service Fabric, embora a escala de falhas seja diferente em ambientes diferentes, os mecanismos reais seriam idênticos. Isso permite um pipeline de código para implantação muito mais rápido e a capacidade de testar os serviços sob cargas do mundo real.

## <a name="using-the-fault-analysis-service"></a>Usando o serviço de análise de falha
**C#**

Os recursos do serviço de análise de falha estão no namespace System. Fabric no pacote NuGet do Microsoft. Service Fabric. Para usar os recursos do serviço de análise de falha, inclua o pacote NuGet como uma referência em seu projeto.

**PowerShell**

Para usar o PowerShell, você deve instalar o SDK do Service Fabric. Após a instalação do SDK, o módulo do PowerShell do infabric é carregado automaticamente para uso.

## <a name="next-steps"></a>Passos seguintes
Para criar serviços verdadeiramente em escala de nuvem, é essencial garantir, antes e depois da implantação, que os serviços possam resistir a falhas reais do mundo. No mundo dos serviços hoje, a capacidade de inovar rapidamente e mover o código para a produção rapidamente é muito importante. O serviço de análise de falha ajuda os desenvolvedores de serviços a fazer exatamente isso.

Comece a testar seus aplicativos e serviços usando os cenários de [teste](service-fabric-testability-scenarios.md)internos ou crie seus próprios cenários de teste usando as [ações de falha](service-fabric-testability-actions.md) fornecidas pelo serviço de análise de falha.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
