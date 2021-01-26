---
title: Recuperação de desastres em tecido de serviço Azure
description: A Azure Service Fabric oferece capacidades para lidar com desastres. Este artigo descreve os tipos de desastres que podem ocorrer e como lidar com eles.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 8d99b4d1fbf227d850de387b7ca24dcd3fd40646
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791160"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperação de desastres no Tecido de Serviço Azure
Uma parte crítica da prestação de alta disponibilidade é garantir que os serviços possam sobreviver a todos os tipos de falhas. Isto é especialmente importante para falhas que não são planeadas e fora do seu controlo. 

Este artigo descreve alguns modos de falha comuns que podem ser desastres se não forem modelados e geridos corretamente. Também discute mitigações e ações a tomar se um desastre acontecer de qualquer forma. O objetivo é limitar ou eliminar o risco de inatividade ou perda de dados quando ocorrem falhas, planeadas ou não.

## <a name="avoiding-disaster"></a>Evitando o desastre
O principal objetivo da Azure Service Fabric é ajudá-lo a modelar tanto o seu ambiente como os seus serviços de modo a que os tipos comuns de falhas não sejam desastres. 

Em geral, existem dois tipos de cenários de catástrofe/falha:
- Falhas de hardware e software
- Falhas operacionais

### <a name="hardware-and-software-faults"></a>Falhas de hardware e software
Falhas de hardware e software são imprevisíveis. A forma mais fácil de sobreviver a falhas é executar mais cópias do serviço através dos limites de falhas de hardware ou software. 

Por exemplo, se o seu serviço estiver a funcionar apenas numa máquina, a falha dessa máquina é um desastre para esse serviço. A forma simples de evitar este desastre é garantir que o serviço está a funcionar em várias máquinas. Os testes também são necessários para garantir que a falha de uma máquina não perturbe o serviço de funcionamento. O planeamento da capacidade garante que uma instância de substituição pode ser criada noutro local e que a redução de capacidade não sobrecarrega os restantes serviços. 

O mesmo padrão funciona independentemente do que estás a tentar evitar o fracasso. Por exemplo, se está preocupado com o fracasso de uma SAN, encontra-se com várias SANs. Se está preocupado com a perda de um rack de servidores, encontra-se em várias prateleiras. Se está preocupado com a perda de datacenters, o seu serviço deve correr através de várias regiões do Azure, através de várias Zonas de Disponibilidade Azure ou através dos seus próprios datacenters. 

Quando um serviço é distribuído por múltiplas instâncias físicas (máquinas, racks, datacenters, regiões), ainda está sujeito a alguns tipos de falhas simultâneas. Mas falhas individuais e mesmo múltiplas de um determinado tipo (por exemplo, uma única falha de ligação virtual ou de ligação de rede) são automaticamente manuseadas e, por isso, já não são um "desastre". 

A Service Fabric fornece mecanismos para expandir o cluster e as pegas trazem os nós e serviços falhados de volta. A Service Fabric também permite executar muitas instâncias dos seus serviços para evitar que falhas não planeadas se transformem em desastres reais.

Pode haver razões para que a execução de uma implantação suficientemente grande para evitar falhas não seja viável. Por exemplo, pode ser preciso mais recursos de hardware do que está disposto a pagar em relação à possibilidade de falhar. Quando se lida com aplicações distribuídas, os custos adicionais de comunicação ou replicação do estado através de distâncias geográficas podem causar uma latência inaceitável. Quando esta linha é traçada difere para cada aplicação. 

Para falhas de software especificamente, a falha pode estar no serviço que está a tentar escalar. Neste caso, mais cópias não impedem o desastre, porque a condição de falha está correlacionada em todos os casos.

### <a name="operational-faults"></a>Falhas operacionais
Mesmo que o seu serviço seja espalhado por todo o mundo com muitos despedimentos, ainda pode experimentar eventos desastrosos. Por exemplo, alguém pode acidentalmente reconfigurar o nome DNS para o serviço, ou eliminá-lo imediatamente. 

Como exemplo, digamos que teve um serviço stateful Service Fabric, e alguém apagou esse serviço acidentalmente. A menos que haja outra mitigação, esse serviço e todo o estado que tinha já se foram. Este tipo de desastres operacionais ("oops") requerem diferentes mitigações e passos para a recuperação do que falhas não planeadas regulares. 

As melhores formas de evitar este tipo de falhas operacionais são:
- Restringir o acesso operacional ao ambiente.
- Auditoria estritamente operações perigosas.
- Impor automatização, evitar alterações manuais ou fora de banda e validar alterações específicas contra o ambiente antes de as decretar.
- Certifique-se de que as operações destrutivas são "suaves". As operações suaves não fazem efeito imediatamente ou podem ser desfeitas dentro de uma janela de tempo.

O Service Fabric fornece mecanismos para prevenir falhas operacionais, tais como o controlo de acesso [baseado em funções](service-fabric-cluster-security-roles.md) para operações de cluster. No entanto, a maioria destas falhas operacionais requerem esforços organizacionais e outros sistemas. A Service Fabric fornece mecanismos para sobreviver a falhas operacionais, nomeadamente [cópias de segurança e restauro para serviços estatais.](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

## <a name="managing-failures"></a>Gestão de falhas
O objetivo da Service Fabric é a gestão automática de falhas. Mas para lidar com alguns tipos de falhas, os serviços devem ter código adicional. Outros tipos de avarias _não_ devem ser automaticamente abordados por razões de segurança e continuidade das empresas. 

### <a name="handling-single-failures"></a>Manipulação de falhas únicas
Máquinas individuais podem falhar por todos os tipos de razões. Às vezes são causas de hardware, como fontes de energia e falhas de hardware de rede. Outras falhas estão no software. Estas incluem falhas no sistema operativo e no próprio serviço. O Tecido de Serviço deteta automaticamente este tipo de avarias, incluindo casos em que a máquina fica isolada de outras máquinas devido a problemas de rede.

Independentemente do tipo de serviço, executar um único caso resulta em tempo de inatividade para esse serviço se essa única cópia do código falhar por qualquer motivo. 

Para lidar com qualquer falha, a coisa mais simples que pode fazer é garantir que os seus serviços funcionam em mais do que um nó por defeito. Para serviços apátridas, certifique-se de que `InstanceCount` é maior que 1. Para os serviços estatais, a recomendação mínima é esta `TargetReplicaSetSize` e `MinReplicaSetSize` ambos estão definidos para 3. A execução de mais cópias do seu código de serviço garante que o seu serviço pode lidar automaticamente com qualquer falha. 

### <a name="handling-coordinated-failures"></a>Manipulação de falhas coordenadas
Falhas coordenadas num cluster podem ser devidos a falhas e alterações de infraestrutura planeadas ou não planeadas, ou a alterações planeadas do software. O Service Fabric modela zonas de infraestrutura que experimentam falhas coordenadas como *domínios de avaria.* As áreas que irão experimentar alterações coordenadas do software são modeladas como *domínios de upgrade*. Para obter mais informações sobre domínios de falhas, domínios de upgrade e topologia de cluster, consulte [descrever um cluster de tecido de serviço utilizando o Cluster Resource Manager](service-fabric-cluster-resource-manager-cluster-description.md).

Por predefinição, o Service Fabric considera os domínios de avaria e de atualização ao planear onde os seus serviços devem ser executados. Por predefinição, o Service Fabric tenta garantir que os seus serviços funcionam em vários domínios de avaria e atualização para que, se ocorrerem alterações planeadas ou não planeadas, os seus serviços continuem disponíveis. 

Por exemplo, digamos que a falha de uma fonte de energia faz com que todas as máquinas de um cesto falhem simultaneamente. Com várias cópias do serviço em funcionamento, a perda de muitas máquinas em falha de domínio transforma-se em apenas mais um exemplo de uma única falha para um serviço. É por isso que gerir os domínios de falha e upgrade é fundamental para garantir uma elevada disponibilidade dos seus serviços. 

Quando está a executar o Tecido de Serviço em Azure, os domínios de avaria e os domínios de atualização são geridos automaticamente. Noutros ambientes, podem não estar. Se estiver a construir os seus próprios clusters no local, certifique-se de mapear e planear corretamente o layout do seu domínio de falha.

Os domínios de upgrade são úteis para áreas de modelação onde o software será atualizado ao mesmo tempo. Por isso, os domínios de upgrade também definem frequentemente os limites onde o software é retirado durante as atualizações planeadas. As atualizações tanto do Service Fabric como dos seus serviços seguem o mesmo modelo. Para obter mais informações sobre atualizações, upgrade de domínios e o modelo de saúde Service Fabric que ajuda a evitar que alterações não intencionais afetem o cluster e o seu serviço, consulte:

 - [Atualização da aplicação](service-fabric-application-upgrade.md)
 - [Tutorial de atualização de aplicações](service-fabric-application-upgrade-tutorial.md)
 - [Modelo de saúde do tecido de serviço](service-fabric-health-introduction.md)

Pode visualizar o layout do seu cluster utilizando o mapa de cluster fornecido no [Service Fabric Explorer:](service-fabric-visualizing-your-cluster.md)

<center>

![Os nódes espalhados por domínios de avaria no Explorador de Tecidos de Serviço][sfx-cluster-map]
</center>

> [!NOTE]
> Áreas de modelação de falhas, atualizações rolantes, execução de muitas instâncias do seu código de serviço e estado, regras de colocação para garantir que os seus serviços funcionam em domínios de falha e upgrade, e monitorização de saúde incorporada são apenas *algumas* das funcionalidades que o Service Fabric fornece para evitar que problemas operacionais normais e falhas se transformem em desastres. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Manipulação simultânea de falhas de hardware ou software
Temos falado de falhas individuais. Como pode ver, são fáceis de manusear tanto para os serviços apátridas como para os estados apenas mantendo mais cópias do código (e estado) a correr em domínios de falha e de upgrade. 

Podem também ocorrer múltiplas falhas aleatórias simultâneas. Estes são mais propensos a levar a tempo de inatividade ou a um desastre real.


#### <a name="stateless-services"></a>Serviços apátridas
A contagem de exemplos para um serviço apátrida indica o número desejado de casos que precisam de estar em execução. Quando qualquer (ou todos) das ocorrências falham, o Service Fabric responde criando automaticamente instâncias de substituição noutros nós. O Service Fabric continua a criar substituições até que o serviço volte à contagem de instâncias desejada.

Por exemplo, assuma que o serviço apátrida tem um `InstanceCount` valor de -1. Este valor significa que um caso deve estar a funcionar em cada nó do cluster. Se algumas dessas ocorrências falharem, a Service Fabric detetará que o serviço não está no estado pretendido e tentará criar as instâncias nos nós onde estão desaparecidos.

#### <a name="stateful-services"></a>Serviços estatais
Existem dois tipos de serviços estatais:
- Imponente com estado persistido.
- Imponente com estado não persistido. (O estado é armazenado na memória.)

A recuperação da falha de um serviço estatal depende do tipo de serviço estatal, de quantas réplicas o serviço tinha e de quantas réplicas falharam.

Num serviço estatal, os dados de entrada são replicados entre réplicas (as primárias e quaisquer secundários ativos). Se a maioria das réplicas receber os dados, os dados são considerados *quórum* comprometidos. (Para cinco réplicas, três serão um quórum.) Isto significa que, em qualquer momento, haverá pelo menos um quórum de réplicas com os dados mais recentes. Se as réplicas falharem (digamos duas em cada cinco), podemos usar o valor do quórum para calcular se podemos recuperar. (Como as três restantes de cinco réplicas ainda estão de pé, é garantido que pelo menos uma réplica terá dados completos.)

Quando um quórum de réplicas falha, a partição é declarada como estando num estado *de perda de quórum.* Digamos que uma partição tem cinco réplicas, o que significa que pelo menos três têm dados completos. Se um quórum (três em cinco) de réplicas falhar, o Tecido de Serviço não pode determinar se as réplicas restantes (duas em cinco) têm dados suficientes para restaurar a partição. Nos casos em que o Service Fabric deteta a perda de quórum, o seu comportamento padrão é evitar escritas adicionais para a partição, declarar perda de quórum e esperar que um quórum de réplicas seja restaurado.

Determinar se ocorreu um desastre para um serviço estatal e, em seguida, geri-lo segue três fases:

1. Determinar se houve perda de quórum ou não.
   
   A perda do quórum é declarada quando a maioria das réplicas de um serviço estatal estão em baixo ao mesmo tempo.
2. Determinar se a perda do quórum é permanente ou não.
   
   Na maior parte do tempo, os fracassos são transitórios. Os processos são reiniciados, os nós são reiniciados, as máquinas virtuais são relançadas e as divisórias de rede curam-se. Às vezes, porém, os fracassos são permanentes. Se as falhas são permanentes ou não depende se o serviço estatal persiste no seu estado ou se o mantém apenas na memória: 
   
   - Para serviços sem estado persistente, uma falha de um quórum ou mais de réplicas resulta _imediatamente_ em perda permanente de quórum. Quando o Service Fabric deteta a perda de quórum num serviço não persistente, procede imediatamente à etapa 3 declarando (potencial) perda de dados. Continuar a perder dados faz sentido porque o Service Fabric sabe que não faz sentido esperar que as réplicas voltem. Mesmo que recuperem, os dados serão perdidos devido à natureza não persistiua do serviço.
   - Para serviços persistentes e imponentes, uma falha de um quórum ou mais de réplicas faz com que o Tecido de Serviço espere que as réplicas voltem e restaurá o quórum. Isto resulta numa paragem de serviço para qualquer _escrita_ para a partição afetada (ou "conjunto de réplica") do serviço. No entanto, as leituras ainda podem ser possíveis com garantias de consistência reduzidas. O período de tempo predefinido que o Service Fabric espera para que o quórum seja restaurado é *infinito,* porque o processo é um (potencial) evento de perda de dados e apresenta outros riscos. Isto significa que o Service Fabric não avançará para o passo seguinte a menos que um administrador tome medidas para declarar perda de dados.
3. Determinar se os dados são perdidos e restaurar a partir de backups.

   Se a perda do quórum tiver sido declarada (automaticamente ou através de uma ação administrativa), a Service Fabric e os serviços passam a determinar se os dados foram efetivamente perdidos. Neste momento, a Service Fabric também sabe que as outras réplicas não vão voltar. Foi essa a decisão tomada quando deixámos de esperar que a perda do quórum se resolvesse. O melhor caminho para o serviço é normalmente congelar e aguardar uma intervenção administrativa específica.
   
   Quando o Service Fabric chama o `OnDataLossAsync` método, é sempre por causa da perda de dados _suspeita._ O Service Fabric garante que esta chamada é entregue à _melhor_ réplica restante. Esta é a réplica que mais progrediu. 
   
   A razão pela qual sempre dizemos _que a perda de_ dados é que é possível que a réplica restante tenha o mesmo estado que as primárias quando o quórum foi perdido. No entanto, sem esse estado para comparar, não há uma boa maneira de o Service Fabric ou os operadores saberem com certeza.     
   
   Então, o que faz uma implementação típica do `OnDataLossAsync` método?
   1. Os registos de implementação que `OnDataLossAsync` foram desencadeados, e disparam todos os alertas administrativos necessários.
   1. Normalmente, a implementação para e aguarda novas decisões e ações manuais a serem tomadas. Isto porque, mesmo que os backups estejam disponíveis, podem ter de estar preparados. 
   
      Por exemplo, se dois serviços diferentes coordenarem informações, esses backups poderão ter de ser modificados para garantir que, após a restauração, a informação que esses dois serviços preocupam é consistente. 
   1. Muitas vezes há alguma outra telemetria ou exaustão do serviço. Estes metadados podem estar contidos noutros serviços ou em registos. Estas informações podem ser usadas conforme necessário para determinar se houve alguma chamada recebida e processada na primária que não estivessem presentes na cópia de segurança ou replicadas a esta réplica em particular. Estas chamadas podem ter de ser reproduzidas ou adicionadas à cópia de segurança antes de a restauração ser viável.  
   1. A implementação compara o estado da réplica restante com o contido em quaisquer backups disponíveis. Se estiver a utilizar coleções fiáveis do Service Fabric, existem [ferramentas e processos](service-fabric-reliable-services-backup-restore.md) disponíveis para o fazer. O objetivo é ver se o estado dentro da réplica é suficiente, e ver o que o backup pode estar faltando.
   1. Após a comparação ser feita, e após a restauração ser concluída (se necessário), o código de serviço deve voltar **verdadeiro** se alguma alteração do estado for feita. Se a réplica determinou que era a melhor cópia disponível do Estado e não fez alterações, o código volta **a ser falso**. 
   
      Um valor **verdadeiro** indica que quaisquer _outras_ réplicas restantes podem agora ser inconsistentes com esta. Serão largados e reconstruídos a partir desta réplica. Um valor **falso** indica que não foram feitas alterações de estado, para que as outras réplicas possam manter o que têm. 

É fundamental que os autores de serviços pratiquem potenciais cenários de perda de dados e falhas antes de os serviços serem implantados na produção. Para proteger contra a possibilidade de perda de dados, é importante apoiar periodicamente [o estado](service-fabric-reliable-services-backup-restore.md) de qualquer um dos seus serviços imponentes para uma loja geo-redundante. 

Também deve garantir que tem a capacidade de restaurar o estado. Como os backups de muitos serviços diferentes são feitos em momentos diferentes, você precisa garantir que após uma restauração, seus serviços têm uma visão consistente uns dos outros. 

Por exemplo, considere uma situação em que um serviço gere um número e o armazena, e depois envia-o para outro serviço que também o armazena. Depois de uma restauração, poderá descobrir que o segundo serviço tem o número, mas o primeiro não, porque a sua cópia de segurança não incluiu essa operação.

Se descobrir que as réplicas restantes são insuficientes para continuar num cenário de perda de dados e não conseguir reconstruir o estado de serviço a partir de telemetria ou exaustão, a frequência das suas cópias de segurança determina o seu melhor objetivo de ponto de recuperação possível (RPO). O Service Fabric fornece muitas ferramentas para testar vários cenários de falha, incluindo quórum permanente e perda de dados que requer restauro de uma cópia de segurança. Estes cenários são incluídos como parte das ferramentas de testabilidade no Tecido de Serviço, geridas pelo Serviço de Análise de Falhas. Para obter mais informações sobre estas ferramentas e padrões, consulte [Introdução ao Serviço de Análise de Falhas.](service-fabric-testability-overview.md) 

> [!NOTE]
> Os serviços de sistema também podem sofrer perdas de quórum. O impacto é específico do serviço em questão. Por exemplo, a perda de quórum no serviço de nomeação afeta a resolução de nomes, enquanto a perda de quórum no serviço Failover Manager bloqueia a criação de novos serviços e as falhas. 
> 
> Os serviços de sistema Service Fabric seguem o mesmo padrão que os seus serviços para gestão do Estado, mas não recomendamos que tente movê-los para fora da perda de quórum e para a perda de dados potenciais. Em vez disso, recomendamos que  [procure apoio](service-fabric-support.md) para encontrar uma solução direcionada para a sua situação. Normalmente é preferível simplesmente esperar até que as réplicas de baixo voltem.
>

#### <a name="troubleshooting-quorum-loss"></a>Perda de quórum de resolução de problemas

As réplicas podem estar em baixo intermitentemente por causa de uma falha transitória. Espere um pouco enquanto o Service Fabric tenta trazê-los para cima. Se as réplicas tiverem caído por mais de uma duração esperada, siga estas ações de resolução de problemas:
- As réplicas podem estar a despenhar-se. Verifique os relatórios de saúde de nível de réplica e os registos da sua aplicação. Recolher depósitos de colisão e tomar as medidas necessárias para recuperar.
- O processo de réplica pode ter ficado sem resposta. Inspecione os registos da sua aplicação para verificar isto. Recolher os despejos de processo e, em seguida, parar o processo sem resposta. O Service Fabric criará um processo de substituição e tentará trazer a réplica de volta.
- Os nós que acolhem as réplicas podem estar em baixo. Reinicie a máquina virtual subjacente para levantar os nós.

Às vezes, pode não ser possível recuperar réplicas. Por exemplo, as unidades falharam ou as máquinas fisicamente não estão a responder. Nestes casos, o Service Fabric precisa de ser instruído para não esperar pela recuperação de réplicas.

*Não* utilize estes métodos se a perda de dados potencial for inaceitável para colocar o serviço online. Nesse caso, devem ser enloucos todos os esforços para a recuperação das máquinas físicas.

As seguintes ações podem resultar em perda de dados. Verifique antes de segui-los.
   
> [!NOTE]
> Nunca _é_ seguro usar estes métodos a não ser de uma forma direcionada contra divisórias específicas. 
>

- Use a `Repair-ServiceFabricPartition -PartitionId` API ou `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API. Esta API permite especificar o ID da partição para sair da perda de quórum e para a perda de dados potenciais.
- Se o seu cluster encontrar falhas frequentes que fazem com que os serviços entrem num estado de perda de quórum e a perda de dados potenciais _for aceitável,_ especificando um valor [qurumLossWaitDuration](/powershell/module/servicefabric/update-servicefabricservice) apropriado pode ajudar o seu serviço a recuperar automaticamente. O Tecido de Serviço aguardará o valor fornecido `QuorumLossWaitDuration` (o padrão é infinito) antes de realizar a recuperação. *Não* recomendamos este método porque pode causar perdas inesperadas de dados.

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilidade do cluster de tecido de serviço
Em geral, o cluster De Tecido de Serviço é um ambiente altamente distribuído sem pontos únicos de falha. Uma falha de um nó não causará problemas de disponibilidade ou fiabilidade para o cluster, principalmente porque os serviços do sistema Service Fabric seguem as mesmas orientações fornecidas anteriormente. Ou seja, funcionam sempre com três ou mais réplicas por defeito, e os serviços de sistema que são apátridas funcionam em todos os nós. 

As camadas subjacentes de rede de tecidos de serviço e de deteção de falhas estão totalmente distribuídas. A maioria dos serviços de sistema pode ser reconstruída a partir de metadados no cluster, ou saber ressincronizar o seu estado de outros lugares. A disponibilidade do cluster pode ficar comprometida se os serviços de sistema entrarem em situações de perda de quórum como as descritas anteriormente. Nestes casos, pode não ser capaz de realizar certas operações no cluster (como iniciar uma atualização ou implantar novos serviços), mas o cluster em si ainda está em pé. 

Os serviços num cluster de funcionamento continuarão a funcionar nestas condições, a menos que exijam que os serviços do sistema continuem a funcionar. Por exemplo, se o Failover Manager estiver em perda de quórum, todos os serviços continuarão a funcionar. Mas quaisquer serviços que falhem não serão capazes de reiniciar automaticamente, porque isso requer o envolvimento do Failover Manager. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Falhas de um datacenter ou de uma região de Azure
Em casos raros, um datacenter físico pode ficar temporariamente indisponível devido à perda de energia ou conectividade da rede. Nestes casos, os seus clusters e serviços de Tecido de Serviço nesse datacenter ou região de Azure estarão indisponíveis. No entanto, _os seus dados estão preservados._ 

Para os clusters em execução em Azure, pode ver atualizações sobre interrupções na página de estado do [Azure][azure-status-dashboard]. No caso altamente improvável de que um datacenter físico seja parcial ou totalmente destruído, quaisquer clusters de Tecidos de Serviço alojados lá, ou os serviços dentro deles, podem ser perdidos. Esta perda inclui qualquer estado não apoiado fora desse datacenter ou região.

Existem duas estratégias diferentes para sobreviver à falha permanente ou sustentada de um único datacenter ou região: 

- Executar agrupamentos de tecido de serviço separados em várias regiões, e usar algum mecanismo para falhar e falhar entre estes ambientes. Este tipo de modelo ativo/ativo ou ativo/passivo de múltiplos clusters requer código de gestão e operações adicionais. Este modelo também requer coordenação de backups dos serviços de um centro de dados ou região para que estejam disponíveis em outros centros de dados ou regiões quando um deles falha. 
- Executar um único cluster de tecido de serviço que abrange vários datacenters ou regiões. A configuração mínima suportada para esta estratégia são três datacenters ou regiões. O número recomendado de regiões ou centros de dados é de cinco. 
  
  Este modelo requer uma topologia de cluster mais complexa. No entanto, o benefício é que a falha de um datacenter ou região é convertida de uma catástrofe para uma falha normal. Estas falhas podem ser tratadas pelos mecanismos que funcionam para aglomerados numa única região. Os domínios de avaria, os domínios de atualização e as regras de colocação do Tecido de Serviço asseguram que as cargas de trabalho são distribuídas de modo a tolerar falhas normais. 
  
  Para obter mais informações sobre políticas que possam ajudar a operar serviços neste tipo de cluster, consulte as políticas de [colocação dos serviços de Tecido de Serviço.](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-that-lead-to-cluster-failures"></a>Falhas aleatórias que levam a falhas no cluster
O Tecido de Serviço tem o conceito de nós de *sementes.* Estes são nós que mantêm a disponibilidade do cluster subjacente. 

Os nós de sementes ajudam a garantir que o cluster se mantém, estabelecendo arrendamentos com outros nós e servindo como desempates durante certos tipos de falhas. Se falhas aleatórias removerem a maioria dos nós de sementes no cluster e não forem trazidas de volta rapidamente, o seu cluster desliga-se automaticamente. O aglomerado falha então. 

Em Azure, o Service Fabric Resource Provider gere as configurações do cluster de tecido de serviço. Por predefinição, o Fornecedor de Recursos distribui nós de sementes através de domínios de avaria e de atualização para o *tipo de nó primário*. Se o tipo de nó primário for marcado como durabilidade prateada ou dourada, quando remover um nó de sementes (quer escalando no tipo de nó primário, quer removendo-o manualmente), o cluster tentará promover outro nó não semente da capacidade disponível do tipo de nó primário. Esta tentativa falhará se tiver menos capacidade disponível do que o seu nível de fiabilidade do cluster requer para o seu tipo de nó primário.

Tanto em clusters de tecido de serviço autónomo como em Azure, o tipo de nó primário é o que executa as sementes. Ao definir um tipo de nó primário, o Service Fabric tirará automaticamente partido do número de nós fornecidos através da criação de até nove nós de sementes e sete réplicas de cada serviço do sistema. Se um conjunto de falhas aleatórias eliminar a maioria dessas réplicas simultaneamente, os serviços do sistema entrarão em perda de quórum. Se a maioria dos nós de sementes forem perdidos, o aglomerado desligar-se-á logo depois.

## <a name="next-steps"></a>Próximos passos
- Saiba como simular várias falhas utilizando a [estrutura de testabilidade.](service-fabric-testability-overview.md)
- Leia outros recursos de recuperação de desastres e alta disponibilidade. A Microsoft publicou uma grande quantidade de orientação sobre estes tópicos. Embora alguns destes recursos se refiram a técnicas específicas de utilização noutros produtos, contêm muitas boas práticas gerais que pode aplicar no contexto do Tecido de Serviço:
  - [Lista de verificação de disponibilidade](/azure/architecture/checklist/resiliency-per-service)
  - [Realizando um exercício de recuperação de desastres](../azure-sql/database/disaster-recovery-drills.md)
  - [Recuperação após desastre e elevada disponibilidade para aplicações do Azure][dr-ha-guide]
- Saiba mais sobre [as opções de suporte do Tecido de Serviço.](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: /windows/win32/perfctrs/specifying-a-counter-path
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: /previous-versions/azure/dn251004(v=azure.100)


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
