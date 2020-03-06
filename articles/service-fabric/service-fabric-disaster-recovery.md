---
title: Recuperação de desastres de tecido de serviço Azure
description: A Azure Service Fabric oferece as capacidades necessárias para lidar com todo o tipo de desastres. Este artigo descreve os tipos de desastres que podem ocorrer e como lidar com eles.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: f9bde0f81dc364aaa09dc9763f2014d83f992371
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78298299"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperação de desastres em Tecido de Serviço Azure
Uma parte crítica da prestação de alta disponibilidade é garantir que os serviços possam sobreviver a todos os tipos de falhas. Isto é especialmente importante para falhas que não são planeadas e fora do seu controlo. Este artigo descreve alguns modos comuns de falha que podem ser desastres se não forem modelados e geridos corretamente. Também discute atenuações e ações a tomar se um desastre acontecesse de qualquer forma. O objetivo é limitar ou eliminar o risco de tempo de inatividade ou perda de dados quando ocorrem falhas, planeadas ou não, ocorrem.

## <a name="avoiding-disaster"></a>Evitar o desastre
O principal objetivo da Service Fabric é ajudá-lo a modelar tanto o seu ambiente como os seus serviços de modo a que tipos comuns de falhas não sejam desastres. 

Em geral, existem dois tipos de cenários de desastre/falha:

1. Falhas no hardware ou no software
2. Falhas operacionais

### <a name="hardware-and-software-faults"></a>Falhas no hardware e software
Falhas no hardware e no software são imprevisíveis. A forma mais fácil de sobreviver a falhas é executar mais cópias do serviço que atravessam os limites de hardware ou de falha de software. Por exemplo, se o seu serviço está a funcionar apenas numa máquina em particular, então a falha dessa máquina é um desastre para esse serviço. A forma simples de evitar este desastre é garantir que o serviço está realmente a funcionar em várias máquinas. Os testes também são necessários para garantir que a falha de uma máquina não interrompa o serviço de funcionamento. O planeamento da capacidade garante que uma instância de substituição pode ser criada noutros locais e que a redução da capacidade não sobrecarrega os restantes serviços. O mesmo padrão funciona independentemente do que estás a tentar evitar o fracasso de. Por exemplo. Se está preocupado com o fracasso de um SAN, você encontra vários SANs. Se está preocupado com a perda de um cabide de servidores, corre por várias prateleiras. Se está preocupado com a perda de datacenters, o seu serviço deve ser executado em várias regiões ou datacenters do Azure. 

Ao correr neste tipo de modo de ligação, ainda está sujeito a alguns tipos de falhas simultâneas, mas falhas individuais e mesmo múltiplas de um tipo específico (ex: uma única VM ou falha de ligação de rede) são automaticamente manuseadas (e por isso já não é um "desastre"). O Service Fabric fornece muitos mecanismos para expandir o cluster e manuseia trazendo nós e serviços falhados de volta. O Service Fabric também permite executar muitos casos dos seus serviços de forma a evitar que este tipo de falhas não planeadas se transformem em desastres reais.

Pode haver razões para que uma implantação suficientemente grande para ultrapassar falhas não seja viável. Por exemplo, pode ser preciso mais recursos de hardware do que está disposto a pagar em relação à hipótese de falha. Quando se trata de aplicações distribuídas, pode ser que o lúpulo de comunicação adicional ou os custos de replicação do Estado em distâncias geográficas causem latência inaceitável. Quando esta linha for traçada, difere para cada aplicação. Para falhas de software especificamente, a falha pode estar no serviço que está a tentar escalar. Neste caso, mais cópias não impedem o desastre, uma vez que a condição de falha está correlacionada em todos os casos.

### <a name="operational-faults"></a>Falhas operacionais
Mesmo que o seu serviço seja atravessado por todo o mundo com muitos despedimentos, ainda pode experimentar eventos desastrosos. Por exemplo, se alguém reconfigurar acidentalmente o nome dns para o serviço, ou apagá-lo imediatamente. Como exemplo, digamos que tinha um serviço de fabricação de serviço e apagou o serviço acidentalmente. A menos que haja outra mitigação, esse serviço e todo o estado que tinha já se foi. Este tipo de catástrofes operacionais ("oops") requerem diferentes atenuações e passos para a recuperação do que falhas regulares não planeadas. 

As melhores formas de evitar este tipo de falhas operacionais são
1. restringir o acesso operacional ao ambiente
2. rigorosamente auditar operações perigosas
3. impor automação, prevenir alterações manuais ou fora da banda, e validar alterações específicas contra o ambiente real antes de as promulgar
4. garantir que as operações destrutivas são "suaves". Operações suaves não têm efeito imediatamente ou podem ser desfeitas dentro de algum tempo

O Service Fabric fornece alguns mecanismos para prevenir falhas operacionais, tais como o fornecimento de controlo de acesso [baseado em funções](service-fabric-cluster-security-roles.md) para operações de cluster. No entanto, a maioria destas falhas operacionais requer esforços organizacionais e outros sistemas. O Service Fabric fornece algum mecanismo para sobreviver a falhas operacionais, nomeadamente cópiade segurança e restauro para serviços estatais.

## <a name="managing-failures"></a>Gestão de falhas
O objetivo do Service Fabric é quase sempre a gestão automática de falhas. No entanto, para lidar com alguns tipos de falhas, os serviços devem ter código adicional. Outros tipos de falhas _não_ devem ser automaticamente abordados devido a razões de segurança e continuidade das empresas. 

### <a name="handling-single-failures"></a>Manuseamento de falhas individuais
Máquinas simples podem falhar por todos os tipos de razões. Algumas delas são causas de hardware, como fontes de energia e falhas de hardware em rede. Outras falhas estão no software. Estas incluem falhas do sistema operativo real e do próprio serviço. Serviço O tecido deteta automaticamente este tipo de falhas, incluindo casos em que a máquina fica isolada de outras máquinas devido a problemas de rede.

Independentemente do tipo de serviço, executar uma única instância resulta em tempo de inatividade para esse serviço se essa única cópia do código falhar por qualquer motivo. 

Para lidar com qualquer falha, a coisa mais simples que pode fazer é garantir que os seus serviços funcionam em mais do que um nó por defeito. Para serviços apátridas, isso pode ser conseguido tendo uma `InstanceCount` superior a 1. Para os serviços estatais, a recomendação mínima é sempre uma `TargetReplicaSetSize` e `MinReplicaSetSize` de pelo menos 3. Executar mais cópias do seu código de serviço garante que o seu serviço pode lidar automaticamente com qualquer falha. 

### <a name="handling-coordinated-failures"></a>Manuseamento de falhas coordenadas
Falhas coordenadas podem ocorrer num cluster devido a falhas e alterações de infraestruturas planeadas ou não planeadas, ou alterações de software planeadas. Service Fabric modela zonas de infraestrutura que experimentam falhas coordenadas como Domínios de Falha. As áreas que irão experimentar alterações de software coordenadas são modeladas como Domínios de Upgrade. Mais informações sobre domínios de falha e upgrade estão [neste documento](service-fabric-cluster-resource-manager-cluster-description.md) que descreve topologia e definição de cluster.

Por padrão, o Service Fabric considera domínios de avaria e atualização ao planear onde os seus serviços devem ser executados. Por padrão, o Service Fabric tenta garantir que os seus serviços decorrem em vários domínios de avaria e atualização, pelo que, se acontecerem alterações planeadas ou não planeadas, os seus serviços permanecem disponíveis. 

Por exemplo, digamos que a falha de uma fonte de energia faz com que um cabide de máquinas falhe simultaneamente. Com várias cópias do serviço a executar a perda de muitas máquinas em falha de domínio de falha torna-se apenas mais um exemplo de falha única para um determinado serviço. É por isso que gerir domínios de falhas é fundamental para garantir uma alta disponibilidade dos seus serviços. Ao executar o Tecido de Serviço em Azure, os domínios de avaria são geridos automaticamente. Noutros ambientes, podem não estar. Se estiver a construir os seus próprios clusters nas instalações, certifique-se de mapear e planear corretamente o seu esquema de domínio de falha.

Os domínios de upgrade são úteis para modelar áreas onde o software vai ser atualizado ao mesmo tempo. Por isso, os Domínios de Upgrade também definem frequentemente os limites onde o software é retirado durante as atualizações planeadas. As atualizações tanto do Tecido de Serviço como dos seus serviços seguem o mesmo modelo. Para mais informações sobre atualizações de rolling, domínios de upgrade e o modelo de saúde Service Fabric que ajuda a evitar alterações não intencionais de impacto no cluster e no seu serviço, consulte estes documentos:

 - [Upgrade de aplicações](service-fabric-application-upgrade.md)
 - [Tutorial de Upgrade de Aplicações](service-fabric-application-upgrade-tutorial.md)
 - [Modelo de saúde de tecido de serviço](service-fabric-health-introduction.md)

Pode visualizar o layout do seu cluster utilizando o mapa de cluster fornecido no [Service Fabric Explorer:](service-fabric-visualizing-your-cluster.md)

<center>

![Nós espalhados por domínios de falha no Explorador de Tecidos de Serviço][sfx-cluster-map]
</center>

> [!NOTE]
> Modelação de áreas de falha, atualizações rolantes, execução de muitas instâncias do seu código de serviço e estado, regras de colocação para garantir que os seus serviços são executados através de domínios de falha e upgrade, e monitorização de saúde incorporada são apenas **algumas** das funcionalidades que o Service Fabric fornece para evitar que problemas operacionais normais e falhas se transformem em desastres. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Manuseamento de falhas de hardware ou software simultâneas
Acima falamos de falhanços individuais. Como pode ver, são fáceis de manusear tanto para serviços apátridas como audais apenas mantendo mais cópias do código (e estado) que atravessam domínios de falha e atualização. Múltiplas falhas aleatórias simultâneas também podem acontecer. Estes são mais propensos a levar a um desastre real.


### <a name="random-failures-leading-to-service-failures"></a>Falhas aleatórias que conduzem a falhas de serviço

#### <a name="stateless-services"></a>Serviços Apátridas
`InstanceCount` para o Serviço indicam o número desejado de casos que precisam de ser em execução. Quando qualquer (ou todos) dos casos falhar, o Tecido de Serviço responderá criando automaticamente instâncias de substituição noutros nós. O Service Fabric continuará a criar substituições até que o serviço volte à contagem de tempo desejada.
Outro exemplo, se o serviço apátrida tiver `InstanceCount` de -1, o que significa que uma instância deve estar em execução em cada um dos nós do cluster. Se algumas dessas instâncias falharem, então a Service Fabric detetará que o serviço não está no seu estado desejado e tentará criar os casos nos nós onde estão desaparecidos.

#### <a name="stateful-services"></a>Serviços Estatais
Existem dois tipos de serviços estatais:
1. Audais com estado persistido
2. Estado com estado não persistiu (estado está armazenado na memória)

A recuperação da falha de serviço depende do tipo de serviço estatal e de quantas réplicas o serviço tinha e quantas falharam.

O que é Quorum?
Num serviço imponente, os dados de entrada são replicados entre réplicas (Primary e ActiveSecondary). Se a maioria das réplicas receber os dados, os dados são considerados quórum comprometidos (para 5 réplicas, 3 serão _quórum)._ Isto significa que, em qualquer momento, é garantido que haverá pelo menos quórum de réplicas com dados mais recentes. Se as réplicas falharem (digamos, 2 em cada 5 réplicas falharam), podemos usar o valor quórum para calcular se podemos recuperar (uma vez que 3 em cada 5 réplicas ainda estão em cima, é garantido que pelo menos 1 réplica terá dados completos).

O que é a perda do Quorum?
Quando o quórum das réplicas falha, a partição é declarada como estando no estado de Perda de Quórum. Digamos que uma partição tem 5 réplicas, o que significa que pelo menos 3 têm dados completos. Se o quórum (3 em 5) das réplicas falhar, então o Tecido de Serviço não pode determinar se as réplicas restantes (2 em 5) têm dados suficientes para restaurar a divisória.

A determinação de um desastre ocorreu para um serviço estatal e geri-lo segue três fases:

1. Determinar se houve perda de quórum ou não
    - A perda de quórum é declarada quando a maioria das réplicas de um serviço estatal estão em baixo ao mesmo tempo.
2. Determinar se a perda do quórum é permanente ou não
   - Na maior parte do tempo, as falhas são transitórias. Os processos são reiniciados, os nós são reiniciados, os VMs são relançados, as divisórias de rede curam. Às vezes, as falhas são permanentes. 
     - Para serviços sem estado persponificado, uma falha de um quórum ou mais de réplicas resulta _imediatamente_ em perda permanente de quórum. Quando o Tecido de Serviço detetar a perda de quórum num serviço declarado não persistente, procederá imediatamente ao passo 3 declarando perda de dados (potencial). Prosseguir com a perda de dados faz sentido porque o Service Fabric sabe que não vale a pena esperar que as réplicas voltem, porque mesmo que estivessem a recuperar, devido à natureza não persistente do serviço os dados perderam-se.
     - Para serviços persistentes audais, uma falha de um quórum ou mais de réplicas faz com que o Tecido de Serviço espere que as réplicas voltem e restaure o quórum. Isto resulta numa paragem de serviço para qualquer _escrita_ para a partição afetada (ou "conjunto de réplicas") do serviço. No entanto, as leituras podem ainda ser possíveis com garantias de consistência reduzidas. A quantidade padrão de tempo que o Tecido de Serviço espera pela restauração do quórum é infinita, uma vez que o processo é um (potencial) evento de perda de dados e apresenta outros riscos.
3. Determinar se houve perda de dados reais, e restaurar de backups
   
   Quando o Service Fabric chama o método `OnDataLossAsync`, é sempre devido a _suspeitas_ de perda de dados. O Service Fabric garante que esta chamada é entregue à _melhor_ réplica restante. Esta é a réplica que mais progressofez. A razão pela qual sempre dizemos _que a perda_ de dados suspeita é que é possível que a réplica restante tenha o mesmo estado que a Primária quando caiu. No entanto, sem esse estado para comparar, não há uma boa maneira de o Service Fabric ou os operadores saberem com certeza. Neste ponto, a Service Fabric também sabe que as outras réplicas não estão a voltar. Foi essa a decisão tomada quando deixámos de esperar que a perda do quórum se resolvesse. O melhor caminho para o serviço é geralmente congelar e esperar por uma intervenção administrativa específica. Então, o que faz uma implementação típica do método `OnDataLossAsync`?
   1. Primeiro, o registo que `OnDataLossAsync` foi acionado, e disparou todos os alertas administrativos necessários.
   1. Normalmente, neste momento, para parar e esperar por novas decisões e ações manuais a serem tomadas. Isto porque, mesmo que existam cópias de segurança, podem ter de estar preparadas. Por exemplo, se dois serviços diferentes coordenarem a informação, esses backups podem ter de ser modificados para garantir que, uma vez que o restabelecimento acontece, a informação de que esses dois serviços se preocupam é consistente. 
   1. Muitas vezes também há outra telemetria ou exaustão do serviço. Estes metadados podem estar contidos noutros serviços ou em registos. Estas informações podem ser utilizadas para determinar se houve chamadas recebidas e processadas na primária que não estavam presentes na cópia de segurança ou replicadas a esta réplica em particular. Estes podem ter de ser repetidos ou adicionados à cópia de segurança antes que a restauração seja viável.  
   1. Comparações do estado da réplica restante com as contidas em quaisquer cópias de segurança disponíveis. Se utilizar as coleções fiáveis do Tecido de Serviço, existem ferramentas e processos disponíveis para o fazer, [descritos neste artigo.](service-fabric-reliable-services-backup-restore.md) O objetivo é ver se o estado dentro da réplica é suficiente, ou também o que o backup pode estar faltando.
   1. Uma vez feita a comparação e, se necessário, a restauração concluída, o código de serviço deve ser verdadeiro se forem feitas alterações de Estado. Se a réplica determinou que era a melhor cópia disponível do Estado e não fez alterações, então devolva falsas. O verdadeiro indica que _quaisquer outras_ réplicas restantes podem agora ser inconsistentes com esta. Serão largados e reconstruídos a partir desta réplica. Falsoindica que não foram feitas alterações de estado, para que as outras réplicas possam manter o que têm. 

É extremamente importante que os autores de serviços pratiquem potenciais cenários de perda de dados e falhas antes de os serviços serem alguma vez implantados na produção. Para proteger contra a possibilidade de perda de dados, é importante apoiar periodicamente [o estado](service-fabric-reliable-services-backup-restore.md) de qualquer um dos seus serviços audais a uma loja geo-redundante. Deve também garantir que tem a capacidade de restaurá-la. Uma vez que os backups de muitos serviços diferentes são tomados em momentos diferentes, você precisa garantir que depois de um restauro os seus serviços têm uma visão consistente uns dos outros. Por exemplo, considere uma situação em que um serviço gera um número e o armazena, em seguida, envia-o para outro serviço que também o armazena. Depois de um restauro, pode descobrir que o segundo serviço tem o número, mas o primeiro não, porque o seu backup não incluiu essa operação.

Se descobrir que as réplicas restantes são insuficientes para continuar num cenário de perda de dados, e não pode reconstruir o estado de serviço a partir de telemetria ou exaustão, a frequência das suas cópias de segurança determina o seu melhor objetivo de ponto de recuperação possível (RPO). O Service Fabric fornece muitas ferramentas para testar vários cenários de falha, incluindo quórum permanente e perda de dados que requerem a restauração de uma cópia de segurança. Estes cenários estão incluídos como parte das ferramentas de testability do Service Fabric, geridas pelo Serviço de Análise de Falhas. Mais informações sobre essas ferramentas e padrões estão disponíveis [aqui.](service-fabric-testability-overview.md) 

> [!NOTE]
> Os serviços de sistema também podem sofrer perdas de quórum, sendo o impacto específico do serviço em questão. Por exemplo, a perda de quórum no serviço de nomeação tem impacto na resolução de nomes, enquanto a perda de quórum no serviço de gestor de failover bloqueia a criação de novos serviços e falhas. Embora os serviços do sistema Service Fabric sigam o mesmo padrão que os seus serviços de gestão do Estado, não é aconselhável que tente movê-los para fora da Perda de Quorum e para uma potencial perda de dados. A recomendação [é,](service-fabric-support.md) em vez disso, procurar apoio para determinar uma solução que seja direcionada para a sua situação específica.  Normalmente é preferível esperar até que as réplicas de baixo regressem.
>

#### <a name="troubleshooting-quorum-loss"></a>Resolução de problemas perda de Quorum

As réplicas podem ser baixas intermitentemente devido a uma falha transitória. Espere algum tempo enquanto o Tecido de Serviço tentará trazê-los para cima. Se as réplicas tiverem estado em baixo durante mais do que o esperado, siga os passos abaixo de resolução de problemas.
- As réplicas podem estar a despenhar-se. Verifique os relatórios de saúde de nível de réplica e os registos de aplicação. Recolha os depósitos de lixo e tome as medidas necessárias para recuperar.
- O processo de réplica pode ter ficado sem resposta. Inspecione os registos de inscrição para verificar isto. Recolher o despejo do processo e, em seguida, matar o processo sem resposta. O Service Fabric criará um processo de substituição e tentará trazer a réplica de volta.
- Os nódosos que acolhem as réplicas podem estar em baixo. Reinicie o VM subjacente para levantar os nódosos.

Pode haver situações em que não é possível recuperar réplicas, por exemplo, as unidades podem ter falhado ou as máquinas fisicamente não respondem. Nestes casos, o Service Fabric precisa de ser instruído a não esperar pela recuperação da réplica.
NÃO utilize estes métodos se a perda de dados potencial não for aceitável para colocar o serviço online, caso em que devem ser envidados todos os esforços para recuperar máquinas físicas.

Os seguintes passos podem resultar em perda de dados – certifique-se antes de os seguir:
   
> [!NOTE]
> _Nunca_ é seguro utilizar estes métodos que não seja de forma direcionada contra divisórias específicas. 
>

- Utilize `Repair-ServiceFabricPartition -PartitionId` ou `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API. Esta API permite especificar o ID da partição para recuperar do QuorumLoss com potencial perda de dados.
- Se o seu cluster encontrar falhas frequentes fazendo com que os serviços entrem no estado de Perda de Quorum e a perda de dados potencial _seja aceitável,_ então especificar um valor de [Duração quorumLossWait adequado](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) pode ajudar o seu serviço a recuperar automaticamente. O tecido de serviço aguardará desde que quorumLossWaitDura (predefinido é infinito) antes de realizar a recuperação. Este método não é _recomendado_ porque isto pode causar perdas inesperadas de dados.

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilidade do cluster De Tecido de Serviço
De um modo geral, o cluster Service Fabric em si é um ambiente altamente distribuído, sem um único ponto de falha. Uma falha de qualquer nó não causará problemas de disponibilidade ou fiabilidade para o cluster, principalmente porque os serviços do sistema Service Fabric seguem as mesmas orientações fornecidas anteriormente: funcionam sempre com três ou mais réplicas por padrão, e esses sistemas serviços que são apátridas executar em todos os nós. As camadas subjacentes de rede de tecido de serviço e deteção de falhas estão totalmente distribuídas. A maioria dos serviços do sistema pode ser reconstruída a partir de metadados no cluster, ou saber como ressincronizar o seu estado a partir de outros lugares. A disponibilidade do cluster pode ficar comprometida se os serviços do sistema entrarem em situações de perda de quórum, como as descritas acima. Nestes casos, pode não ser capaz de realizar determinadas operações no cluster, como iniciar uma atualização ou implementar novos serviços, mas o próprio cluster ainda está em pé. Os serviços em funcionamento continuarão a funcionar nestas condições, a menos que exijam que os serviços do sistema continuem a funcionar. Por exemplo, se o Failover Manager estiver em perda de quórum todos os serviços continuarão a ser executados, mas quaisquer serviços que falhem não poderão reiniciar automaticamente, uma vez que isso requer o envolvimento do Gestor failover. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Falhas de um datacenter ou região de Azure
Em casos raros, um centro de dados físicos pode ficar temporariamente indisponível devido à perda de energia ou conectividade da rede. Nestes casos, os seus clusters e serviços de Tecido de Serviço naquela região de datacenter ou Azure não estarão disponíveis. No entanto, _os seus dados são preservados._ Para clusters em execução em Azure, pode ver atualizações sobre falhas na [página de estado do Azure][azure-status-dashboard]. No caso altamente improvável de um centro de dados físicos estar parcialmente ou totalmente destruído, quaisquer clusters de Tecido de Serviço hospedados lá ou os serviços dentro deles podem ser perdidos. Isto inclui qualquer estado não apoiado fora desse datacenter ou região.

Há duas estratégias diferentes para sobreviver à falha permanente ou sustentada de um único datacenter ou região. 

1. Executar clusters de tecido de serviço separados em várias dessas regiões, e utilizar algum mecanismo para falhar e falhar entre estes ambientes. Este tipo de modelo multi-cluster ativo ou passivo ativo requer um código adicional de gestão e operações. Isto também requer coordenação de backups dos serviços de um centro de dados ou região para que estejam disponíveis em outros centros de dados ou regiões quando um falha. 
2. Executar um único cluster de Tecido de Serviço que abrange vários datacenters ou regiões. A configuração mínima suportada para isto são três datacenters ou regiões. O número recomendado de regiões ou centros de dados é de cinco. Isto requer uma topologia de cluster mais complexa. No entanto, o benefício deste modelo é que a falha de um datacenter ou região é convertida de uma catástrofe numa falha normal. Estas falhas podem ser tratadas pelos mecanismos que funcionam para aglomerados numa única região. Domínios de falhas, domínios de atualização e regras de colocação do Service Fabric asseguram a distribuição de cargas de trabalho para que tolerem falhas normais. Para obter mais informações sobre políticas que possam ajudar a operar serviços neste tipo de cluster, leia-se sobre as políticas de [colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Falhas aleatórias que conduzem a falhas de cluster
O Tecido de Serviço tem o conceito de Nódeos de Sementes. Estes são nós que mantêm a disponibilidade do cluster subjacente. Estes nós ajudam a garantir que o cluster permaneça em pé, estabelecendo arrendamentos com outros nós e servindo como desempates durante certos tipos de falhas de rede. Se falhas aleatórias removerem a maioria dos nós de sementes no aglomerado e não forem trazidos de volta, então o anel da federação de agrupamentos colapsa à medida que perdeu o quórum do nó de sementes e o aglomerado falha. Em Azure, o Prestador de Recursos de Tecido de Serviço gere as configurações de cluster de tecido de serviço, e por padrão distribui nós de sementes através de domínios de falha e atualização do tipo de nó primário; Se o nótipo primário for marcado como durabilidade de prata ou ouro, quando remover um nó de sementes, quer escalando no nódotipo primário ou removendo manualmente um nó de sementes, o cluster tentará promover outro nó não-semente do nó primário disponível capacidade, e falhará se tiver menos capacidade disponível do que o nível de fiabilidade do cluster requer para o seu Tipo de Nó Primário.

Tanto em clusters de tecido de serviço autónomo como em Azure, o "Tipo de Nó Primário" é o que executa as sementes. Ao definir um tipo de nó primário, o Tecido de Serviço tirará automaticamente partido do número de nós fornecidos através da criação de até 9 nós de sementes e 7 réplicas de cada um dos serviços do sistema. Se um conjunto de falhas aleatórias eliminar simultaneamente a maioria dessas réplicas do serviço de sistema, os serviços do sistema entrarão em perda de quórum, como descrevemos acima. Se a maioria dos nós de sementes for em perdem-se, o aglomerado será encerrado logo depois.

## <a name="next-steps"></a>Passos seguintes
- Aprenda a simular várias falhas utilizando o quadro de [testability](service-fabric-testability-overview.md)
- Leia outros recursos de recuperação de desastres e de alta disponibilidade. A Microsoft publicou uma grande quantidade de orientação sobre estes tópicos. Embora alguns destes documentos se refiram a técnicas específicas para utilização noutros produtos, eles contêm muitas boas práticas gerais que você pode aplicar no contexto do Tecido de Serviço também:
  - [Lista de verificação de disponibilidade](/azure/architecture/checklist/resiliency-per-service)
  - [Realizar um exercício de recuperação de desastres](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Recuperação após desastre e elevada disponibilidade para aplicações do Azure][dr-ha-guide]
- Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
