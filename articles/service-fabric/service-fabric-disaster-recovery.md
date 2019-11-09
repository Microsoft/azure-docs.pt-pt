---
title: Recuperação de desastre do Azure Service Fabric | Microsoft Docs
description: O Azure Service Fabric oferece os recursos necessários para lidar com todos os tipos de desastres. Este artigo descreve os tipos de desastres que podem ocorrer e como lidar com eles.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 1be891d38eb918d65cd8efda86e9a81fa629cf38
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73883998"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperação de desastres no Azure Service Fabric
Uma parte crítica da entrega de alta disponibilidade é garantir que os serviços possam sobreviver a todos os diferentes tipos de falhas. Isso é especialmente importante para falhas não planejadas e fora do seu controle. Este artigo descreve alguns modos de falha comuns que poderiam ser desastres se não forem modelados e gerenciados corretamente. Ele também aborda atenuações e ações a serem tomadas se um desastre ocorrer mesmo assim. O objetivo é limitar ou eliminar o risco de tempo de inatividade ou perda de dados quando ocorrem falhas, planejadas ou de outra forma.

## <a name="avoiding-disaster"></a>Evitando desastres
O objetivo principal do Service Fabric é ajudá-lo a modelar seu ambiente e seus serviços de forma que os tipos de falha comuns não sejam desastres. 

Em geral, há dois tipos de cenários de desastre/falha:

1. Falhas de hardware ou software
2. Falhas operacionais

### <a name="hardware-and-software-faults"></a>Falhas de hardware e software
As falhas de hardware e software são imprevisíveis. A maneira mais fácil de sobreviver a falhas é executar mais cópias do serviço distribuídas entre limites de falha de hardware ou software. Por exemplo, se o serviço estiver sendo executado apenas em um determinado computador, a falha desse computador será um desastre para esse serviço. A maneira simples de evitar esse desastre é garantir que o serviço esteja realmente em execução em vários computadores. O teste também é necessário para garantir que a falha de um computador não interrompa o serviço em execução. O planejamento de capacidade garante que uma instância de substituição possa ser criada em outro lugar e que a redução na capacidade não sobrecarregue os serviços restantes. O mesmo padrão funciona independentemente do que você está tentando evitar a falha do. Por exemplo,. Se você estiver preocupado com a falha de uma SAN, será executado em várias SANs. Se você estiver preocupado com a perda de um rack de servidores, será executado em vários racks. Se você estiver preocupado com a perda de data centers, seu serviço deverá ser executado em várias regiões ou data centers do Azure. 

Ao ser executado nesse tipo de modo estendido, você ainda está sujeito a alguns tipos de falhas simultâneas, mas uma única falha e até várias falhas de um tipo específico (por exemplo: uma única VM ou um link de rede falhar) são manipuladas automaticamente (e, portanto, não é mais um "desastre"). O Service Fabric fornece muitos mecanismos para expandir o cluster e os identificadores que trazem os nós e os serviços com falha de volta. Service Fabric também permite a execução de muitas instâncias de seus serviços para evitar que esses tipos de falhas não planejadas entrem em desastres reais.

Pode haver motivos pelos quais a execução de uma implantação grande o suficiente para ultrapassar falhas não é viável. Por exemplo, pode levar mais recursos de hardware do que você está disposto a pagar em relação à possibilidade de falha. Ao lidar com aplicativos distribuídos, pode ser que os saltos de comunicação adicionais ou os custos de replicação de estado entre distâncias geográficas causam latência inaceitável. O local em que essa linha é desenhada difere para cada aplicativo. Para falhas de software especificamente, a falha pode estar no serviço que você está tentando dimensionar. Nesse caso, mais cópias não impedem o desastre, pois a condição de falha está correlacionada em todas as instâncias.

### <a name="operational-faults"></a>Falhas operacionais
Mesmo que seu serviço seja distribuído em todo o mundo com muitas redundâncias, ele ainda poderá experimentar eventos desastrosos. Por exemplo, se alguém reconfigurar acidentalmente o nome DNS para o serviço ou excluí-lo imediatamente. Por exemplo, digamos que você tenha um serviço de Service Fabric com estado e alguém tenha excluído o serviço acidentalmente. A menos que haja alguma outra mitigação, esse serviço e todo o estado que ele tinha foram agora ausentes. Esses tipos de desastres operacionais ("Ops") exigem diferentes mitigações e etapas para recuperação do que falhas regulares não planejadas. 

As melhores maneiras de evitar esses tipos de falhas operacionais são
1. restringir o acesso operacional ao ambiente
2. estritamente auditar operações perigosas
3. impor a automação, impedir alterações manuais ou fora de banda e validar alterações específicas no ambiente real antes de enagir-las
4. Verifique se as operações destrutivas são "soft". As operações flexíveis não entram em vigor imediatamente ou podem ser desfeitas dentro de alguma janela de tempo

O Service Fabric fornece alguns mecanismos para evitar falhas operacionais, como fornecer controle de acesso [baseado em função](service-fabric-cluster-security-roles.md) para operações de cluster. No entanto, a maioria dessas falhas operacionais requer esforços organizacionais e outros sistemas. O Service Fabric fornece algum mecanismo para sobreviver a falhas operacionais, mais notavelmente backup e restauração para serviços com estado.

## <a name="managing-failures"></a>Gerenciando falhas
O objetivo do Service Fabric é quase sempre o gerenciamento automático de falhas. No entanto, para lidar com alguns tipos de falhas, os serviços devem ter código adicional. Outros tipos de falhas _não_ devem ser tratados automaticamente devido a motivos de segurança e continuidade de negócios. 

### <a name="handling-single-failures"></a>Tratamento de falhas únicas
Máquinas únicas podem falhar por todos os tipos de motivos. Algumas delas são causas de hardware, como fontes de alimentação e falhas de hardware de rede. Outras falhas estão no software. Isso inclui falhas do sistema operacional real e do próprio serviço. Service Fabric detecta automaticamente esses tipos de falhas, incluindo casos em que o computador se torna isolado de outros computadores devido a problemas de rede.

Independentemente do tipo de serviço, a execução de uma única instância resultará em tempo de inatividade para esse serviço se essa cópia única do código falhar por qualquer motivo. 

Para lidar com qualquer falha única, a coisa mais simples que você pode fazer é garantir que seus serviços sejam executados em mais de um nó por padrão. Para serviços sem estado, isso pode ser feito com um `InstanceCount` maior que 1. Para serviços com estado, a recomendação mínima é sempre uma `TargetReplicaSetSize` e `MinReplicaSetSize` de pelo menos 3. A execução de mais cópias do seu código de serviço garante que o serviço possa lidar automaticamente com uma única falha. 

### <a name="handling-coordinated-failures"></a>Tratamento de falhas coordenadas
Falhas coordenadas podem ocorrer em um cluster devido a falhas de infraestrutura planejadas ou não planejadas, alterações ou alterações de software planejadas. O Service Fabric modela zonas de infraestrutura que experimentam falhas coordenadas como domínios de falha. As áreas que apresentarão alterações de software coordenadas serão modeladas como domínios de atualização. Mais informações sobre domínios de falha e atualização estão neste [documento](service-fabric-cluster-resource-manager-cluster-description.md) que descreve a topologia e a definição do cluster.

Por padrão Service Fabric considera os domínios de falha e de atualização ao planejar onde os serviços devem ser executados. Por padrão, o Service Fabric tenta garantir que seus serviços sejam executados em vários domínios de falha e de atualização, portanto, se as alterações planejadas ou não planejadas acontecerem, os serviços permanecerão disponíveis. 

Por exemplo, digamos que a falha de uma fonte de energia faça com que um rack de máquinas falhe simultaneamente. Com várias cópias do serviço que executam a perda de vários computadores na falha de domínio de falha, vamos para apenas outro exemplo de falha única para um determinado serviço. É por isso que gerenciar domínios de falha é essencial para garantir a alta disponibilidade de seus serviços. Ao executar Service Fabric no Azure, os domínios de falha são gerenciados automaticamente. Em outros ambientes, eles podem não ser. Se você estiver criando seus próprios clusters localmente, certifique-se de mapear e planejar seu layout de domínio de falha corretamente.

Os domínios de atualização são úteis para modelar áreas em que o software será atualizado ao mesmo tempo. Por causa disso, os domínios de atualização geralmente definem os limites em que o software é desativado durante as atualizações planejadas. As atualizações de Service Fabric e seus serviços seguem o mesmo modelo. Para obter mais informações sobre atualizações sem interrupção, domínios de atualização e o modelo de integridade de Service Fabric que ajuda a impedir que alterações não intencionais afetem o cluster e seu serviço, consulte estes documentos:

 - [Atualização do aplicativo](service-fabric-application-upgrade.md)
 - [Tutorial de atualização de aplicativo](service-fabric-application-upgrade-tutorial.md)
 - [Modelo de integridade Service Fabric](service-fabric-health-introduction.md)

Você pode visualizar o layout do cluster usando o mapa de cluster fornecido no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>

![nós se espalham entre domínios de falha no Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Modelando áreas de falha, atualizações sem interrupção, executando muitas instâncias de seu código de serviço e estado, regras de posicionamento para garantir que seus serviços sejam executados em domínios de falha e de atualização, e o monitoramento de integridade interno são apenas **alguns** dos recursos que o Service Fabric fornece para manter os problemas operacionais e as falhas normais de entrar em desastres. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Tratamento de falhas de hardware ou software simultâneas
Acima, falamos sobre falhas únicas. Como você pode ver, é fácil lidar com serviços com e sem estado, apenas mantendo mais cópias do código (e do estado) em execução nos domínios de falha e de atualização. Várias falhas aleatórias simultâneas também podem acontecer. É mais provável que eles levem a um desastre real.


### <a name="random-failures-leading-to-service-failures"></a>Falhas aleatórias que levam a falhas de serviço
Digamos que o serviço tenha uma `InstanceCount` de 5, e vários nós executando essas instâncias falharam ao mesmo tempo. Service Fabric responde criando automaticamente instâncias de substituição em outros nós. Ele continuará criando substituições até que o serviço volte à contagem de instâncias desejada. Como outro exemplo, digamos que houvesse um serviço sem estado com um `InstanceCount`de-1, o que significa que ele é executado em todos os nós válidos no cluster. Digamos que algumas dessas instâncias tenham falhado. Nesse caso, Service Fabric observa que o serviço não está em seu estado desejado e tenta criar as instâncias nos nós em que estão ausentes. 

Para serviços com estado, a situação depende se o serviço tem estado persistente ou não. Ele também depende de quantas réplicas o serviço tinha e quantas falharam. Determinar se um desastre ocorreu para um serviço com estado e gerenciá-lo segue três estágios:

1. Determinando se houve perda de quorum ou não
   - Uma perda de quorum é sempre que a maioria das réplicas de um serviço com estado está inativa ao mesmo tempo, incluindo a primária.
2. Determinando se a perda de quorum é permanente ou não
   - Na maioria das vezes, as falhas são transitórias. Os processos são reiniciados, os nós são reiniciados, as VMs são reiniciadas, as partições de rede são reparadas. Às vezes, no entanto, as falhas são permanentes. 
     - Para serviços sem estado persistente, uma falha de um quorum ou mais de réplicas resulta _imediatamente_ na perda de quorum permanente. Quando Service Fabric detecta perda de quorum em um serviço não persistente com estado, ele passa imediatamente para a etapa 3 declarando (potencial) perda de dados. Prosseguir com a perda de dados faz sentido porque Service Fabric sabe que não há nenhum ponto em esperar que as réplicas voltem, porque mesmo que elas tenham sido recuperadas, elas ficarão vazias.
     - Para serviços persistentes com estado, uma falha de um quorum ou mais de réplicas faz Service Fabric começar a esperar que as réplicas voltem e restaurem o quorum. Isso resulta em uma interrupção de serviço para qualquer _gravação_ na partição afetada (ou "conjunto de réplicas") do serviço. No entanto, as leituras ainda podem ser possíveis com garantias de consistência reduzidas. A quantidade de tempo padrão que Service Fabric aguarda até que o quorum seja restaurado é infinito, pois continuar é um evento de perda de dados (potencial) e transporta outros riscos. Substituir o valor de `QuorumLossWaitDuration` padrão é possível, mas não é recomendado. Em vez disso, no momento, todos os esforços devem ser feitos para restaurar as réplicas inativas. Isso requer trazer os nós que estão inativos para o backup e garantir que eles possam remontar as unidades em que armazenaram o estado persistente local. Se a perda de quorum for causada por uma falha de processo, Service Fabric tentará recriar os processos automaticamente e reiniciar as réplicas dentro deles. Se isso falhar, o Service Fabric relatará erros de integridade. Se eles puderem ser resolvidos, as réplicas normalmente retornarão. Às vezes, no entanto, as réplicas não podem ser trazidas de volta. Por exemplo, as unidades podem ter falhado ou os computadores foram fisicamente destruídos de alguma forma. Nesses casos, agora temos um evento de perda de quorum permanente. Para informar Service Fabric para parar de esperar que as réplicas invertidas voltem, um administrador de cluster deve determinar quais partições de quais serviços são afetadas e chamar a API `Repair-ServiceFabricPartition -PartitionId` ou `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)`.  Essa API permite especificar a ID da partição a ser movida do QuorumLoss e a potencial perda de data.

   > [!NOTE]
   > _Nunca_ é seguro usar essa API, a não ser de forma direcionada a partições específicas. 
   >

3. Determinando se houve perda de dados real e restaurando de backups
   - Quando Service Fabric chama o método `OnDataLossAsync`, sempre é devido à _suspeita_ de perda de dados. Service Fabric garante que essa chamada seja entregue para a _melhor_ réplica restante. Isso é qualquer que a réplica tenha feito mais progresso. O motivo pelo qual sempre dizem a perda de dados _suspeita_ é que é possível que a réplica restante realmente tenha todo o mesmo estado que o primário fazia quando ficou inoperante. No entanto, sem esse estado para compará-lo, não há uma boa maneira de Service Fabric ou operadores saber com certeza. Neste ponto, Service Fabric também sabe que as outras réplicas não estão retornando. Essa foi a decisão tomada quando interrompemos a espera pela perda de quorum para se resolverem. O melhor curso de ação para o serviço é, em geral, congelar e aguardar a intervenção administrativa específica. Então, o que faz uma implementação típica do método `OnDataLossAsync`?
   - Primeiro, registre que `OnDataLossAsync` foi disparado e acione todos os alertas administrativos necessários.
   - Geralmente, neste ponto, pausar e aguardar a tomada de mais decisões e ações manuais. Isso ocorre porque, mesmo que os backups estejam disponíveis, talvez eles precisem ser preparados. Por exemplo, se dois serviços diferentes coordenarem informações, talvez esses backups precisem ser modificados para garantir que, quando a restauração ocorrer, as informações que esses dois serviços se preocupam sejam consistentes. 
   - Muitas vezes, também há alguma outra telemetria ou esgotamento do serviço. Esses metadados podem estar contidos em outros serviços ou em logs. Essas informações podem ser usadas para determinar se houve alguma chamada recebida e processada no primário que não estavam presentes no backup ou replicadas para essa réplica específica. Talvez eles precisem ser reproduzidos ou adicionados ao backup antes que a restauração seja viável.  
   - Comparações do estado da réplica restante com o que está contido em todos os backups disponíveis. Se estiver usando as Service Fabric coleções confiáveis, há ferramentas e processos disponíveis para fazer isso, descritos neste [artigo](service-fabric-reliable-services-backup-restore.md). O objetivo é ver se o estado dentro da réplica é suficiente ou também o que o backup pode estar faltando.
   - Depois que a comparação for feita e, se necessário, a restauração for concluída, o código de serviço deverá retornar true se alguma alteração de estado tiver sido feita. Se a réplica determinou que era a melhor cópia disponível do estado e não fez alterações, retorne FALSE. Verdadeiro indica que qualquer _outra_ réplica restante agora pode ser inconsistente com esta. Eles serão descartados e recriados a partir dessa réplica. False indica que nenhuma alteração de estado foi feita, portanto, as outras réplicas podem manter o que têm. 

É extremamente importante que os autores de serviço pratiquem a potencial perda de dados e cenários de falha antes que os serviços sejam implantados na produção. Para proteger contra a possibilidade de perda de dados, é importante [fazer backup periódico do estado](service-fabric-reliable-services-backup-restore.md) de qualquer um dos seus serviços com estado para um armazenamento com redundância geográfica. Você também deve garantir que você tenha a capacidade de restaurá-lo. Como os backups de vários serviços diferentes são feitos em momentos diferentes, você precisa garantir que, após uma restauração, seus serviços tenham uma exibição consistente entre si. Por exemplo, considere uma situação em que um serviço gera um número e o armazena e, em seguida, o envia para outro serviço que também o armazena. Após uma restauração, você pode descobrir que o segundo serviço tem o número, mas o primeiro não, porque o backup não incluiu essa operação.

Se você descobrir que as réplicas restantes são insuficientes para continuar em um cenário de perda de dados e não puder reconstruir o estado do serviço da telemetria ou do esgotamento, a frequência dos backups determinará o melhor RPO (objetivo de ponto de recuperação) possível. O Service Fabric fornece muitas ferramentas para testar vários cenários de falha, incluindo quorum permanente e perda de dados que exigem a restauração de um backup. Esses cenários são incluídos como parte das ferramentas de teste de Service Fabric, gerenciadas pelo serviço de análise de falha. Mais informações sobre essas ferramentas e padrões estão disponíveis [aqui](service-fabric-testability-overview.md). 

> [!NOTE]
> Os serviços do sistema também podem sofrer perda de quorum, com o impacto específico do serviço em questão. Por exemplo, a perda de quorum no serviço de cadastramento afeta a resolução de nomes, enquanto que a perda de quorum no serviço do Gerenciador de failover bloqueia novos failovers e criação de serviço. Embora os serviços do sistema Service Fabric sigam o mesmo padrão que seus serviços de gerenciamento de estado, não é recomendável que você tente movê-los para fora da perda de quorum e para uma possível perda de dados. A recomendação é, em vez disso, [buscar suporte](service-fabric-support.md) para determinar uma solução que seja destinada à sua situação específica.  Normalmente, é preferível simplesmente esperar até que as réplicas inativas sejam retornadas.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilidade do cluster de Service Fabric
Em termos gerais, o próprio cluster de Service Fabric é um ambiente altamente distribuído sem pontos únicos de falha. Uma falha de um nó não causará problemas de disponibilidade ou confiabilidade para o cluster, principalmente porque os serviços do sistema Service Fabric seguem as mesmas diretrizes fornecidas anteriormente: eles sempre executam com três ou mais réplicas por padrão, e esses sistemas serviços que são executados sem monitoração de estado em todos os nós. As camadas de detecção de falha e rede Service Fabric subjacentes são totalmente distribuídas. A maioria dos serviços do sistema pode ser recriada a partir de metadados no cluster ou saber como ressincronizar seu estado de outros locais. A disponibilidade do cluster poderá ficar comprometida se os serviços do sistema entrarem em situações de perda de quorum como as descritas acima. Nesses casos, talvez você não consiga executar determinadas operações no cluster, como iniciar uma atualização ou implantar novos serviços, mas o próprio cluster ainda está ativo. Os serviços no já em execução permanecerão em execução nessas condições, a menos que exijam gravações nos serviços do sistema para continuar funcionando. Por exemplo, se o Gerenciador de Failover estiver na perda de quorum, todos os serviços continuarão a ser executados, mas todos os serviços que falharem não poderão ser reiniciados automaticamente, pois isso requer o envolvimento do Gerenciador de Failover. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Falhas de um datacenter ou de uma região do Azure
Em casos raros, um data center físico pode ficar temporariamente indisponível devido à perda de energia ou conectividade de rede. Nesses casos, seus Service Fabric clusters e serviços nesse Datacenter ou na região do Azure ficarão indisponíveis. No entanto, _seus dados são preservados_. Para clusters em execução no Azure, você pode exibir atualizações sobre interrupções na [página de status do Azure][azure-status-dashboard]. No evento altamente improvável de que um data center físico seja parcialmente ou totalmente destruído, qualquer Cluster Service Fabric hospedado ali ou os serviços dentro deles podem ser perdidos. Isso inclui qualquer Estado cujo backup não seja feito fora do Datacenter ou da região.

Há duas estratégias diferentes para sobreviver à falha permanente ou contínua de um único datacenter ou região. 

1. Execute clusters Service Fabric separados em várias dessas regiões e utilize um pouco de mecanismo para failover e failback entre esses ambientes. Esse tipo de modelo ativo-passivo de vários clusters ou Active-Passive requer gerenciamento adicional e código de operações. Isso também requer a coordenação de backups dos serviços em um datacenter ou região para que eles estejam disponíveis em outros datacenters ou regiões quando houver falha. 
2. Execute um único cluster Service Fabric que abranja vários datacenters ou regiões. A configuração mínima com suporte para isso é de três datacenters ou regiões. O número recomendado de regiões ou data centers é cinco. Isso requer uma topologia de cluster mais complexa. No entanto, o benefício desse modelo é que a falha de um datacenter ou região é convertida de um desastre em uma falha normal. Essas falhas podem ser tratadas pelos mecanismos que funcionam para clusters em uma única região. Os domínios de falha, os domínios de atualização e as regras de posicionamento do Service Fabric garantem que as cargas de trabalho sejam distribuídas para tolerar falhas normais. Para obter mais informações sobre políticas que podem ajudar a operar serviços nesse tipo de cluster, leia sobre [as políticas de posicionamento](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Falhas aleatórias que levam a falhas de cluster
Service Fabric tem o conceito de nós de semente. Esses são nós que mantêm a disponibilidade do cluster subjacente. Esses nós ajudam a garantir que o cluster permaneça ativo estabelecendo concessões com outros nós e servindo como desempates durante determinados tipos de falhas de rede. Se as falhas aleatórias removerem a maioria dos nós de semente no cluster e eles não forem devolvidos, o anel de Federação do cluster será recolhido conforme você perdeu o quorum do nó de propagação e o cluster falhará. No Azure, o provedor de recursos Service Fabric gerencia Service Fabric configurações de cluster e, por padrão, distribui os nós de semente entre os domínios de falha e de atualização do tipo de nó primário; Se o NodeType primário estiver marcado como durabilidade prata ou ouro, quando você remover um nó semente, seja dimensionando em seu NodeType primário ou removendo manualmente um nó semente, o cluster tentará promover outro nó não-semente do NodeType primário disponível capacidade e falhará se você tiver menos capacidade disponível do que o nível de confiabilidade do cluster exigir para o tipo de nó primário.

Em clusters de Service Fabric autônomos e no Azure, o "tipo de nó primário" é aquele que executa as sementes. Ao definir um tipo de nó primário, Service Fabric se beneficiará automaticamente do número de nós fornecidos pela criação de até 9 nós de semente e 7 réplicas de cada um dos serviços do sistema. Se um conjunto de falhas aleatórias usar a maioria dessas réplicas de serviço do sistema simultaneamente, os serviços do sistema entrarão em perda de quorum, como descrito acima. Se a maioria dos nós de semente for perdida, o cluster será desligado logo após.

## <a name="next-steps"></a>Passos seguintes
- Saiba como simular várias falhas usando a [estrutura](service-fabric-testability-overview.md) de possibilidade de teste
- Leia outros recursos de recuperação de desastres e alta disponibilidade. A Microsoft publicou uma grande quantidade de diretrizes sobre esses tópicos. Embora alguns desses documentos se confiram em técnicas específicas para uso em outros produtos, eles contêm muitas práticas recomendadas gerais que você também pode aplicar no contexto de Service Fabric:
  - [Lista de verificação de disponibilidade](/azure/architecture/checklist/resiliency-per-service)
  - [Executando uma análise de recuperação de desastre](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Recuperação após desastre e elevada disponibilidade para aplicações do Azure][dr-ha-guide]
- Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
