---
title: Descrição geral do serviço de análise de falhas | Documentos da Microsoft
description: Este artigo descreve o serviço de análise de falhas no Service Fabric para induzir a falhas e execução de cenários de teste em relação a seus serviços.
services: service-fabric
documentationcenter: .net
author: anmolah
manager: chackdan
editor: vturecek
ms.assetid: 1f064276-293a-4989-a513-e0d0b9fdf703
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: anmola
ms.openlocfilehash: 3581550779b2387515b4f300d211b4e0a894edc7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544829"
---
# <a name="introduction-to-the-fault-analysis-service"></a>Introdução ao serviço de análise de falhas
O serviço de análise de falhas foi concebido para fins de teste de serviços que são criados no Microsoft Azure Service Fabric. Com o serviço de análise de falhas pode induza falhas significativas e executar cenários de teste completa contra as suas aplicações. Estas falhas e cenários de exercitem e validar os diversos Estados e transições que um serviço irá ocorrer em toda a sua vida, tudo de forma consistente, controlada e segura.

As ações são as falhas individuais visando um serviço de teste. Um desenvolvedor de serviço pode utilizar como blocos de construção para cenários complicados de escrever. Por exemplo:

* Reinicie o nó para simular a qualquer número de situações em que uma máquina ou VM é reiniciada.
* Mova uma réplica do seu serviço com monitorização de estado para simular o balanceamento de carga, ativação pós-falha ou atualização da aplicação.
* Invoca perda de quórum no serviço com estado para criar uma situação em que as operações de escrita não podem continuar porque não existe suficiente réplicas "backup" ou "secundárias" para aceitar novos dados.
* Invoca perda de dados num serviço com monitorização de estado para criar uma situação em que todos os Estados de dentro da memória é completamente eliminados.

Os cenários são operações complexas, compostas por uma ou mais ações. O serviço de análise de falhas fornece dois cenários completos incorporados:

* Cenário de Chaos
* Cenário de ativação pós-falha

## <a name="testing-as-a-service"></a>Teste como um serviço
O serviço de análise de falhas é um serviço de sistema do Service Fabric que é iniciado automaticamente com um cluster do Service Fabric. Este serviço funciona como host para inserção de falhas, a execução do cenário de teste e a análise do Estado de funcionamento. 

![Serviço de análise de falhas][0]

Quando é iniciado um cenário de teste ou de ação de falhas, um comando é enviado para o serviço de análise de falhas para executar o cenário de teste ou de ação de falhas. O serviço de análise de falhas é com monitoração de estado para que possa fiável executar cenários e as falhas e validar os resultados. Por exemplo, um cenário de teste de execução longa pode ser executado com confiança pelo serviço de análise de falhas. E porque estão sendo executados testes de dentro do cluster, o serviço pode examinar o estado do cluster e seus serviços para fornecer informações mais detalhadas sobre falhas.

## <a name="testing-distributed-systems"></a>Teste de sistemas distribuídos
O Service Fabric garante que a tarefa de criação e gestão de aplicações dimensionáveis distribuídas significativamente mais fácil. O serviço de análise de falhas torna o teste de uma aplicação distribuída da mesma forma mais fácil. Existem três principais problemas que precisam ser resolvidos ao testar:

1. Simulando/gerar falhas que podem ocorrer em cenários reais: Um dos aspectos importantes do Service Fabric é o que permite que as aplicações distribuídas recuperar a partir de várias falhas. No entanto, para testar que o aplicativo é capaz de recuperar a partir destas falhas, é necessário um mecanismo para simular/gerar essas falhas do mundo real num ambiente de teste controlado.
1. A capacidade de gerar falhas correlacionadas: Básicas falhas no sistema, como falhas de rede e falhas de máquina, são fáceis de produzir individualmente. Gerar um número significativo de cenários que podem ocorrer no mundo real, como resultado as interações destas falhas individuais não é simples.
1. Experiência unificada em vários níveis de desenvolvimento e implementação: Existem vários sistemas de injeção de falha que podem fazer a vários tipos de falhas. No entanto, a experiência em todas elas é ruim quando mudar de cenários de programação de one box, para executar os mesmos testes em ambientes de teste grande, para as utilizar para testes em produção.

Embora haja vários mecanismos para resolver estes problemas, um sistema que faz o mesmo com garantias necessárias, desde um ambiente de desenvolvimento de one box, para testar em clusters de produção – está em falta. O serviço de análise de falhas ajuda os desenvolvedores de aplicativos se concentrar na respetiva lógica de negócio de teste. O serviço de análise de falhas fornece todas as funções necessárias para testar a interação do serviço com o sistema distribuído subjacente.

### <a name="simulatinggenerating-real-world-failure-scenarios"></a>Simulando/geração de cenários de falha do mundo real
Para testar a robustez de um sistema distribuído contra falhas, precisamos de um mecanismo para gerar falhas. Embora, em teoria, gerar uma falha, como um nó inativo parece fácil, ele começa a atingir o mesmo conjunto de problemas de consistência que o Service Fabric está a tentar resolver. Por exemplo, se quisermos encerrar um nó, o fluxo de trabalho necessário é a seguinte:

1. A partir do cliente, emita um pedido de nó de encerramento.
1. Envie o pedido para o nó correto.
   
    a. Se o nó não for encontrado, ele falhará.
   
    b. Se o nó for encontrado, ele deverá retornar apenas se o nó é encerrado.

Para verificar se a falha de uma perspectiva de teste, o teste precisa saber que quando esta falha é induzidas pelo, a falha, na verdade, acontece. A garantia de que o Service Fabric fornece é que qualquer um do nó irá descer, ou já estava pendente quando o comando atingido o nó. Em ambos os casos, o teste deve ser capaz de ponderar sobre o estado corretamente e ter êxito ou falhar corretamente na sua validação. Um sistema implementado fora do Service Fabric para fazer o mesmo conjunto de falhas conseguiu acertar muitos rede, hardware e problemas de software, que iria impedi-lo de fornecer as garantias anteriores. Na ocorrência de problemas de afirmado anteriormente, Service Fabric do componente irá reconfigurar o estado do cluster para resolver os problemas, e, por conseguinte, o serviço de análise de falhas ainda será capaz de lhe dar o conjunto certo de garantias.

### <a name="generating-required-events-and-scenarios"></a>A gerar eventos necessários e cenários
Embora a simular uma falha de mundo real de forma consistente é difícil para começar, a capacidade de gerar falhas correlacionadas é cada vez mais difícil. Por exemplo, uma perda de dados ocorrerá num serviço com estado persistente quando ocorrem os seguintes procedimentos:

1. Apenas um quórum de escrita das réplicas são processadas na replicação. Todas as réplicas secundárias de desfasamento da primária.
1. O quórum de escrita fica inativo por causa das réplicas ficará indisponível (devido a um pacote do código ou na direção para baixo do nó).
1. O quórum de escrita não é possível se levante porque os dados para as réplicas são perdidos (devido à corrupção de disco ou da recriação da imagem de máquina).

Estas falhas correlacionadas acontecer no mundo real, mas não como falhas individuais frequentemente como. A capacidade de teste para esses cenários antes de ocorrerem na produção é fundamental. Ainda mais importante é a capacidade de simular estes cenários com cargas de trabalho de produção em circunstâncias controladas (no meio do dia com todos os engenheiros na mesa de jogos). Que é muito melhor do que acontecer pela primeira vez na produção às 2:00h

### <a name="unified-experience-across-different-environments"></a>Experiência unificada entre ambientes diferentes
A prática tem sido tradicionalmente criar três diferentes conjuntos de experiências, uma para o ambiente de desenvolvimento, uma para testes e outra para produção. O modelo foi:

1. No ambiente de desenvolvimento, produza as transições de estado que permitem que os testes de unidade de métodos individuais.
1. No ambiente de teste, produza falhas para permitir testes de ponta a ponta que exerçam vários cenários de falha.
1. Manter o ambiente de produção original para evitar eventuais falhas não natural e para garantir que existe extremamente rápida resposta humana a falha.

No Service Fabric, através do serviço de análise de falhas, estamos propondo transformar isso em torno e utilizar a mesma metodologia de ambiente de programação para produção. Existem duas formas de fazer isso:

1. Para induza falhas controladas, utilize as APIs de serviço da análise de falhas, a partir de um ambiente de uma caixa para clusters de produção.
1. Para dar uma febre que faz com que induction automática de falhas de cluster, utilize o serviço de análise de falhas para gerar falhas automática. Controlar a taxa de falhas através da configuração permite que o mesmo serviço para ser testada de maneira diferente em ambientes diferentes.

Com o Service Fabric, embora a escala de falhas deve ser diferente em ambientes diferentes, os mecanismos de reais seriam idênticos. Isso permite que um pipeline muito mais rápida código de implantação e a capacidade de testar os serviços em cargas reais.

## <a name="using-the-fault-analysis-service"></a>Utilizar o serviço de análise de falhas
**C#**

Funcionalidades do serviço de análise de falhas estão no namespace System. Fabric no pacote Microsoft.ServiceFabric NuGet. Para usar os recursos de serviço de análise de falhas, inclua o pacote nuget como referência no seu projeto.

**PowerShell**

Para utilizar o PowerShell, tem de instalar o SDK do Service Fabric. Após a instalação do SDK, o módulo do ServiceFabric PowerShell é automaticamente carregado para que possa utilizar.

## <a name="next-steps"></a>Passos Seguintes
Para realmente criar serviços de escala da cloud, é fundamental para garantir, ambos antes e após a implementação, que serviços podem suportar a falhas do mundo real. No mundo dos serviços hoje em dia, a capacidade de inovar rapidamente e mover rapidamente o código para produção é muito importante. O serviço de análise de falhas ajuda os desenvolvedores de serviço para fazer exatamente isso.

Começar a testar seus aplicativos e serviços usando o incorporado [testar cenários](service-fabric-testability-scenarios.md), ou criar seus próprios cenários de teste usando a [ações de falha](service-fabric-testability-actions.md) fornecidas pelo serviço de análise de falhas.

<!--Image references-->
[0]: ./media/service-fabric-testability-overview/faultanalysisservice.png
