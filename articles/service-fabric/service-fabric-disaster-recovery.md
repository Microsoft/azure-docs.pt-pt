---
title: Recuperação de desastres de tecido de serviço Azure
description: A Azure Service Fabric oferece capacidades para lidar com desastres. Este artigo descreve os tipos de desastres que podem ocorrer e como lidar com eles.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b29985d40ae3a1bf582099e998e000fed83460f6
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371652"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperação de desastres em Tecido de Serviço Azure
Uma parte crítica da elevada disponibilidade é garantir que os serviços possam sobreviver a todos os tipos de falhas. Isto é especialmente importante para falhas que não são planeadas e fora do seu controlo. 

Este artigo descreve alguns modos comuns de falha que podem ser desastres se não forem modelados e geridos corretamente. Também discute atenuações e ações a tomar se um desastre acontecer de qualquer maneira. O objetivo é limitar ou eliminar o risco de tempo de inatividade ou perda de dados quando ocorrerem falhas, planeadas ou não,.

## <a name="avoiding-disaster"></a>Evitar o desastre
O principal objetivo do Azure Service Fabric é ajudá-lo a modelar tanto o seu ambiente como os seus serviços de modo a que tipos comuns de falhas não sejam desastres. 

Em geral, existem dois tipos de cenários de desastre/falha:
- Falhas no hardware e software
- Falhas operacionais

### <a name="hardware-and-software-faults"></a>Falhas no hardware e software
Falhas no hardware e no software são imprevisíveis. A forma mais fácil de sobreviver a falhas é executar mais cópias do serviço através dos limites de falhas de hardware ou software. 

Por exemplo, se o seu serviço estiver a funcionar apenas numa máquina, a falha dessa máquina é um desastre para esse serviço. A forma simples de evitar este desastre é garantir que o serviço está a funcionar em várias máquinas. Os testes também são necessários para garantir que a falha de uma máquina não interrompa o serviço de funcionamento. O planeamento de capacidades garante que uma instância de substituição pode ser criada noutros locais e que a redução da capacidade não sobrecarrega os restantes serviços. 

O mesmo padrão funciona independentemente do que estás a tentar evitar o fracasso de. Por exemplo, se está preocupado com o fracasso de um SAN, você encontra vários SANs. Se está preocupado com a perda de um cabide de servidores, corre por várias prateleiras. Se está preocupado com a perda de datacenters, o seu serviço deve ser executado em várias regiões do Azure, em várias Zonas de Disponibilidade Azure, ou em todos os seus próprios datacenters. 

Quando um serviço é atravessado por múltiplas instâncias físicas (máquinas, prateleiras, datacenters, regiões), ainda está sujeito a alguns tipos de falhas simultâneas. Mas falhas únicas e mesmo múltiplas de um determinado tipo (por exemplo, uma única máquina virtual ou falha de ligação de rede) são automaticamente manuseadas e, por isso, já não são um "desastre". 

O Service Fabric fornece mecanismos para expandir o cluster e manuseia trazendo nós e serviços falhados de volta. O Service Fabric também permite executar muitos casos dos seus serviços para evitar que falhas não planeadas se transformem em desastres reais.

Pode haver razões para que a execução de uma implantação suficientemente grande para abranger falhas não seja viável. Por exemplo, pode ser preciso mais recursos de hardware do que estádisposto a pagar em relação à hipótese de falhar. Quando se lida com aplicações distribuídas, lúpulo de comunicação adicional ou custos de replicação do Estado em distâncias geográficas pode causar latência inaceitável. Quando esta linha for traçada, difere para cada aplicação. 

Para falhas de software especificamente, a falha pode estar no serviço que está a tentar escalar. Neste caso, mais cópias não impedem o desastre, porque a condição de falha está correlacionada em todos os casos.

### <a name="operational-faults"></a>Falhas operacionais
Mesmo que o seu serviço seja atravessado por todo o mundo com muitos despedimentos, ainda pode experimentar eventos desastrosos. Por exemplo, alguém pode acidentalmente reconfigurar o nome DNS para o serviço, ou apagá-lo imediatamente. 

Como exemplo, digamos que tinha um serviço de fabricação de serviço e apagou o serviço acidentalmente. A não ser que haja outra mitigação, esse serviço e todo o estado que tinha já se foram. Este tipo de catástrofes operacionais ("oops") requerem diferentes atenuações e passos para a recuperação do que falhas regulares não planeadas. 

As melhores formas de evitar este tipo de falhas operacionais são:
- Restringir o acesso operacional ao ambiente.
- Audite estritamente operações perigosas.
- Imponha a automatização, evite alterações manuais ou fora da banda e valide alterações específicas contra o ambiente antes de as promulgar.
- Certifique-se de que as operações destrutivas são "suaves". As operações suaves não têm efeito imediato ou podem ser desfeitas dentro de um período de tempo.

O Service Fabric fornece mecanismos para prevenir falhas operacionais, tais como o fornecimento de controlo de acesso [baseado em funções](service-fabric-cluster-security-roles.md) para operações de cluster. No entanto, a maioria destas falhas operacionais requer esforços organizacionais e outros sistemas. O Service Fabric fornece mecanismos para a sobrevivência de falhas operacionais, nomeadamente cópia de [segurança e restauro para serviços estatais.](service-fabric-backuprestoreservice-quickstart-azurecluster.md)

## <a name="managing-failures"></a>Gestão de falhas
O objetivo do Service Fabric é a gestão automática de falhas. Mas para lidar com alguns tipos de falhas, os serviços devem ter código adicional. Outros tipos de falhas _não_ devem ser automaticamente abordados por razões de segurança e continuidade das empresas. 

### <a name="handling-single-failures"></a>Manuseamento de falhas individuais
Máquinas simples podem falhar por todos os tipos de razões. Às vezes são causas de hardware, como fontes de energia e falhas de hardware de rede. Outras falhas estão no software. Estas incluem falhas do sistema operativo e do próprio serviço. Serviço O tecido deteta automaticamente este tipo de falhas, incluindo casos em que a máquina fica isolada de outras máquinas devido a problemas de rede.

Independentemente do tipo de serviço, executar uma única instância resulta em tempo de inatividade para esse serviço se essa única cópia do código falhar por qualquer motivo. 

Para lidar com qualquer falha, a coisa mais simples que pode fazer é garantir que os seus serviços funcionam em mais do que um nó por defeito. Para serviços apátridas, certifique-se de que `InstanceCount` é superior a 1. Para os serviços estatais, a recomendação mínima é que `TargetReplicaSetSize` e `MinReplicaSetSize` estão ambos definidos para 3. Executar mais cópias do seu código de serviço garante que o seu serviço pode lidar automaticamente com qualquer falha. 

### <a name="handling-coordinated-failures"></a>Manuseamento de falhas coordenadas
Falhas coordenadas num cluster podem ser devido a falhas e alterações de infraestruturas planeadas ou não planeadas, ou alterações de software planeadas. Service Fabric modela zonas de infraestrutura que experimentam falhas coordenadas como *domínios de avaria.* As áreas que irão experimentar alterações coordenadas de software são modeladas como domínios de *upgrade.* Para obter mais informações sobre domínios de falhas, domínios de upgrade e topologia de cluster, consulte [Descrever um cluster de Tecido de Serviço utilizando](service-fabric-cluster-resource-manager-cluster-description.md)cluster Resource Manager .

Por padrão, o Service Fabric considera domínios de avaria e atualização ao planear onde os seus serviços devem ser executados. Por padrão, o Service Fabric tenta garantir que os seus serviços decorrem em vários domínios de avaria e atualização para que, se acontecerem alterações planeadas ou não planeadas, os seus serviços permaneçam disponíveis. 

Por exemplo, digamos que a falha de uma fonte de energia faz com que todas as máquinas de uma prateleira falhem simultaneamente. Com várias cópias do serviço em execução, a perda de muitas máquinas em falha de domínio de falha torna-se apenas mais um exemplo de uma única falha para um serviço. É por isso que gerir domínios de falha e upgrade é fundamental para garantir uma alta disponibilidade dos seus serviços. 

Quando estiver a executar o Tecido de Serviço em Azure, os domínios de avaria e os domínios de atualização são geridos automaticamente. Noutros ambientes, podem não estar. Se estiver a construir os seus próprios clusters no local, certifique-se de mapear e planear corretamente o seu esquema de domínio de falha.

Os domínios de upgrade são úteis para modelar áreas onde o software será atualizado ao mesmo tempo. Por isso, os domínios de atualização também definem frequentemente os limites onde o software é retirado durante as atualizações planeadas. As atualizações tanto do Tecido de Serviço como dos seus serviços seguem o mesmo modelo. Para obter mais informações sobre atualizações em rolo, domínios de atualização e o modelo de saúde service Fabric que ajuda a evitar alterações não intencionais que afetam o cluster e o seu serviço, consulte:

 - [Atualização da aplicação](service-fabric-application-upgrade.md)
 - [Tutorial de atualização de aplicações](service-fabric-application-upgrade-tutorial.md)
 - [Modelo de saúde de tecido de serviço](service-fabric-health-introduction.md)

Pode visualizar o layout do seu cluster utilizando o mapa de cluster fornecido no [Service Fabric Explorer:](service-fabric-visualizing-your-cluster.md)

<center>

![Nós espalhados por domínios de falha no Explorador de Tecidos de Serviço][sfx-cluster-map]
</center>

> [!NOTE]
> Modelação de áreas de falha, atualizações rolantes, execução de muitas instâncias do seu código de serviço e estado, regras de colocação para garantir que os seus serviços passam por domínios de falha e upgrade, e monitorização de saúde incorporada são apenas *algumas* das funcionalidades que o Service Fabric fornece para evitar que problemas operacionais normais e falhas se transformem em desastres. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Manuseamento de falhas de hardware ou software simultâneas
Temos falado de falhanços individuais. Como pode ver, são fáceis de manusear tanto para serviços apátridas como audais, mantendo mais cópias do código (e estado) que atravessam domínios de falha e atualização. 

Múltiplas falhas aleatórias simultâneas também podem acontecer. Estes são mais propensos a causar tempo de inatividade ou a um desastre real.


#### <a name="stateless-services"></a>Serviços apátridas
A contagem por exemplo para um serviço apátrida indica o número desejado de casos que precisam de ser em execução. Quando qualquer (ou todos) dos casos falha, o Tecido de Serviço responde criando automaticamente instâncias de substituição noutros nós. O Serviço Fabric continua a criar substituições até que o serviço volte à contagem de tempo desejada.

Por exemplo, assuma que o serviço apátrida tem um valor `InstanceCount` de -1. Este valor significa que uma instância deve estar em execução em cada nó do cluster. Se alguns desses casos falharem, o Service Fabric detetará que o serviço não está no seu estado desejado e tentará criar as instâncias nos nós onde estão desaparecidos.

#### <a name="stateful-services"></a>Serviços estatais
Existem dois tipos de serviços estatais:
- Audais com estado persponiado.
- Audais com estado não persistiu. (O Estado é armazenado na memória.)

A recuperação do fracasso de um serviço audacis depende do tipo de serviço estatal, quantas réplicas o serviço tinha, e quantas réplicas falharam.

Num serviço imponente, os dados de entrada são replicados entre réplicas (as primárias e quaisquer secundárias ativas). Se a maioria das réplicas receber os dados, os dados são considerados *quórum* cometidos. (Para cinco réplicas, três serão um quórum.) Isto significa que, em qualquer momento, haverá pelo menos um quórum de réplicas com os dados mais recentes. Se as réplicas falharem (digamos duas em cada cinco), podemos usar o valor quórum para calcular se podemos recuperar. (Como as três restantes de cinco réplicas ainda estão em alta, é garantido que pelo menos uma réplica terá dados completos.)

Quando um quórum de réplicas falha, a partição é declarada em estado de perda de *quórum.* Digamos que uma partição tem cinco réplicas, o que significa que pelo menos três têm dados completos. Se um quórum (três em cinco) de réplicas falhar, o Tecido de Serviço não consegue determinar se as réplicas restantes (duas em cinco) têm dados suficientes para restaurar a partição. Nos casos em que o Tecido de Serviço deteta a perda de quórum, o seu comportamento padrão é prevenir escritos adicionais à partição, declarar perda de quórum e esperar que um quórum de réplicas seja restaurado.

Determinar se ocorreu um desastre para um serviço estatal e, em seguida, geri-lo segue três fases:

1. Determinar se houve perda de quórum ou não.
   
   A perda de quórum é declarada quando a maioria das réplicas de um serviço estatal estão em baixo ao mesmo tempo.
2. Determinar se a perda do quórum é permanente ou não.
   
   Na maior parte do tempo, as falhas são transitórias. Os processos são reiniciados, os nós são reiniciados, as máquinas virtuais são relançadas e as divisórias de rede curam. Às vezes, porém, os fracassos são permanentes. Se as falhas são permanentes ou não depende se o serviço de estado persiste o seu estado ou se o mantém apenas na memória: 
   
   - Para serviços sem estado persponificado, uma falha de um quórum ou mais de réplicas resulta _imediatamente_ em perda permanente de quórum. Quando o Tecido de Serviço deteta perda de quórum num serviço declarado não persistente, procede imediatamente ao passo 3 declarando perda de dados (potencial). Prosseguir com a perda de dados faz sentido porque o Service Fabric sabe que não vale a pena esperar que as réplicas voltem. Mesmo que recuperem, os dados serão perdidos devido à natureza não persistiu do serviço.
   - Para serviços persistentes audais, uma falha de um quórum ou mais de réplicas faz com que o Tecido de Serviço espere que as réplicas voltem e restaure o quórum. Isto resulta numa paragem de serviço para qualquer _escrita_ para a partição afetada (ou "conjunto de réplicas") do serviço. No entanto, as leituras poderão ainda ser possíveis com garantias de coerência reduzidas. A quantidade de tempo padrão que o Tecido de Serviço espera que o quórum seja restaurado é *infinita,* porque o processo é um (potencial) evento de perda de dados e acarreta outros riscos. Isto significa que o Tecido de Serviço não avançará para o próximo passo, a menos que um administrador tome medidas para declarar a perda de dados.
3. Determinar se os dados são perdidos e restaurar as cópias de segurança.

   Se a perda de quórum tiver sido declarada (automaticamente ou através de uma ação administrativa), a Service Fabric e os serviços passam a determinar se os dados foram efetivamente perdidos. Neste ponto, a Service Fabric também sabe que as outras réplicas não vão voltar. Foi essa a decisão tomada quando deixámos de esperar que a perda do quórum se resolvesse. O melhor caminho para o serviço é geralmente congelar e esperar por uma intervenção administrativa específica.
   
   Quando o Service Fabric chama o método `OnDataLossAsync`, é sempre por suspeita _de_ perda de dados. O Service Fabric garante que esta chamada é entregue à _melhor_ réplica restante. Esta é a réplica que mais progressofez. 
   
   A razão pela qual sempre dizemos _que a suspeita_ de perda de dados é que é possível que a réplica restante tenha o mesmo estado que a primária quando o quórum foi perdido. No entanto, sem esse estado para comparar, não há uma boa maneira de o Service Fabric ou os operadores saberem com certeza.     
   
   Então, o que faz uma implementação típica do método `OnDataLossAsync`?
   1. Os registos de implementação que `OnDataLossAsync` foram acionados, e dispara todos os alertas administrativos necessários.
   1. Normalmente, a implementação para e aguarda por novas decisões e ações manuais a serem tomadas. Isto porque, mesmo que existam cópias de segurança, podem ter de estar preparadas. 
   
      Por exemplo, se dois serviços diferentes coordenarem a informação, esses backups poderão ter de ser modificados para garantir que, após o restabelecimento, a informação que estes dois serviços se preocupam é consistente. 
   1. Muitas vezes há outra telemetria ou exaustão do serviço. Estes metadados podem estar contidos noutros serviços ou em registos. Estas informações podem ser utilizadas conforme necessário para determinar se houve chamadas recebidas e processadas na primária que não estavam presentes na cópia de segurança ou replicadas a esta réplica em particular. Estas chamadas podem ter de ser reproduzidas ou adicionadas à cópia de segurança antes que a restauração seja viável.  
   1. A implementação compara o estado da réplica restante com o contido em quaisquer cópias de segurança disponíveis. Se estiver a utilizar coleções fiáveis de Tecido de Serviço, existem [ferramentas e processos](service-fabric-reliable-services-backup-restore.md) disponíveis para o fazer. O objetivo é ver se o estado dentro da réplica é suficiente, e para ver o que o backup pode estar faltando.
   1. Após a comparação e após a restauração ser concluída (se necessário), o código de serviço deve ser **verdadeiro** se forem feitas alterações de Estado. Se a réplica determinou que era a melhor cópia disponível do Estado e não fez alterações, o código devolve **falsos**. 
   
      Um valor **verdadeiro** indica que quaisquer _outras_ réplicas restantes podem agora ser inconsistentes com esta. Serão largados e reconstruídos a partir desta réplica. Um valor **falso** indica que não foram feitas alterações de estado, para que as outras réplicas possam manter o que têm. 

É extremamente importante que os autores de serviços pratiquem potenciais cenários de perda de dados e falhas antes de os serviços serem implantados na produção. Para proteger contra a possibilidade de perda de dados, é importante apoiar periodicamente [o estado](service-fabric-reliable-services-backup-restore.md) de qualquer um dos seus serviços audais a uma loja geo-redundante. 

Deve também garantir que tem a capacidade de restaurar o estado. Como os backups de muitos serviços diferentes são tomados em momentos diferentes, você precisa garantir que depois de um restauro, os seus serviços têm uma visão consistente uns dos outros. 

Por exemplo, considere uma situação em que um serviço gera um número e o armazena, e depois envia-o para outro serviço que também o armazena. Depois de um restauro, pode descobrir que o segundo serviço tem o número, mas o primeiro não, porque o seu backup não incluiu essa operação.

Se descobrir que as réplicas restantes são insuficientes para continuar num cenário de perda de dados, e não pode reconstruir o estado de serviço a partir de telemetria ou exaustão, a frequência das suas cópias de segurança determina o seu melhor objetivo de ponto de recuperação possível (RPO). O Service Fabric fornece muitas ferramentas para testar vários cenários de falha, incluindo quórum permanente e perda de dados que requer a restauração de uma cópia de segurança. Estes cenários estão incluídos como parte das ferramentas de testabilidade no Tecido de Serviço, geridos pelo Serviço de Análise de Falhas. Para obter mais informações sobre essas ferramentas e padrões, consulte [Introdução ao Serviço](service-fabric-testability-overview.md)de Análise de Falhas . 

> [!NOTE]
> Os serviços de sistema também podem sofrer perdas de quórum. O impacto é específico do serviço em questão. Por exemplo, a perda de quórum no serviço de nomeação afeta a resolução de nomes, enquanto a perda de quórum no serviço Failover Manager bloqueia a criação de novos serviços e falhas. 
> 
> Os serviços do sistema Service Fabric seguem o mesmo padrão que os seus serviços para a gestão do Estado, mas não recomendamos que tente movê-los para fora da perda de quórum e para uma potencial perda de dados. Em vez disso, recomendamos que [procure apoio](service-fabric-support.md) para encontrar uma solução direcionada para a sua situação. É geralmente preferível esperar até que as réplicas regressem.
>

#### <a name="troubleshooting-quorum-loss"></a>Perda de quórum de resolução de problemas

As réplicas podem ser baixas intermitentemente devido a uma falha transitória. Espere algum tempo enquanto o Tecido de Serviço tenta trazê-los para cima. Se as réplicas tiverem estado em baixo por mais do que uma duração esperada, siga estas ações de resolução de problemas:
- As réplicas podem estar a despenhar-se. Verifique os relatórios de saúde de nível de réplica e os registos de aplicação. Recolha lixeiras e tome as medidas necessárias para recuperar.
- O processo de réplica pode ter ficado sem resposta. Inspecione os registos de inscrição para verificar isto. Recolha despejos de processos e, em seguida, pare o processo sem resposta. O Service Fabric criará um processo de substituição e tentará trazer a réplica de volta.
- Os nódosos que acolhem as réplicas podem estar em baixo. Reinicie a máquina virtual subjacente para levantar os nódosos.

Às vezes, pode não ser possível recuperar réplicas. Por exemplo, as unidades falharam ou as máquinas fisicamente não estão a responder. Nestes casos, o Service Fabric precisa de ser instruído a não esperar pela recuperação da réplica.

*Não* utilize estes métodos se a perda potencial de dados for inaceitável para colocar o serviço online. Nesse caso, devem ser envidados todos os esforços para recuperar as máquinas físicas.

As seguintes ações podem resultar na perda de dados. Verifique antes de segui-los.
   
> [!NOTE]
> Nunca _é_ seguro usar estes métodos a não ser de uma forma direcionada contra divisórias específicas. 
>

- Utilize a `Repair-ServiceFabricPartition -PartitionId` ou `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API. Esta API permite especificar a identificação da partição para sair da perda de quórum e para uma potencial perda de dados.
- Se o seu cluster encontrar falhas frequentes que fazem com que os serviços entrem num estado de perda de quórum e a perda de dados potencial _seja aceitável,_ especificando um valor de [Duração quorumLossWait adequado](https://docs.microsoft.com/powershell/module/servicefabric/update-servicefabricservice?view=azureservicefabricps) pode ajudar o seu serviço a recuperar automaticamente. O Tecido de Serviço aguardará o valor de `QuorumLossWaitDuration` fornecido (o padrão é infinito) antes de realizar a recuperação. *Não* recomendamos este método porque pode causar perdas inesperadas de dados.

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilidade do cluster De Tecido de Serviço
Em geral, o cluster Service Fabric é um ambiente altamente distribuído, sem um único ponto de falha. Uma falha de qualquer nó não causará problemas de disponibilidade ou fiabilidade para o cluster, principalmente porque os serviços do sistema Service Fabric seguem as mesmas orientações fornecidas anteriormente. Ou seja, funcionam sempre com três ou mais réplicas por defeito, e serviços de sistema que são apátridas funcionam em todos os nós. 

As camadas subjacentes de rede de tecido de serviço e deteção de falhas estão totalmente distribuídas. A maioria dos serviços do sistema pode ser reconstruída a partir de metadados no cluster, ou saber como ressincronizar o seu estado a partir de outros lugares. A disponibilidade do cluster pode ficar comprometida se os serviços do sistema entrarem em situações de perda de quórum, como as descritas anteriormente. Nestes casos, pode não ser capaz de realizar determinadas operações no cluster (como iniciar uma atualização ou implementar novos serviços), mas o próprio cluster ainda está em pé. 

Os serviços num cluster em funcionamento continuarão a funcionar nestas condições, a menos que exijam que os serviços do sistema continuem a funcionar. Por exemplo, se o Failover Manager estiver em perda de quórum, todos os serviços continuarão a funcionar. Mas quaisquer serviços que falhem não poderão reiniciar automaticamente, porque isso requer o envolvimento do Failover Manager. 

### <a name="failures-of-a-datacenter-or-an-azure-region"></a>Falhas de um datacenter ou de uma região de Azure
Em casos raros, um datacenter físico pode ficar temporariamente indisponível devido à perda de energia ou conectividade da rede. Nestes casos, os seus clusters e serviços de Tecido de Serviço naquela região de datacenter ou Azure não estarão disponíveis. No entanto, _os seus dados são preservados._ 

Para clusters em execução em Azure, pode ver atualizações sobre falhas na [página de estado do Azure][azure-status-dashboard]. No caso altamente improvável de um datacenter físico estar parcialmente ou totalmente destruído, quaisquer clusters de Tecido de Serviço hospedados lá, ou os serviços dentro deles, podem ser perdidos. Esta perda inclui qualquer estado que não seja apoiado fora desse datacenter ou região.

Existem duas estratégias diferentes para sobreviver à falha permanente ou sustentada de um único datacenter ou região: 

- Executar aglomerados de tecido de serviço separados em várias dessas regiões, e usar algum mecanismo para falhar e falhar entre estes ambientes. Este tipo de modelo ativo/ativo ou ativo/passivo de múltiplos clusters requer um código adicional de gestão e operações. Este modelo também requer coordenação de backups dos serviços de um centro de dados ou região para que estejam disponíveis em outros centros de dados ou regiões quando um falha. 
- Executar um único cluster de Tecido de Serviço que abrange vários datacenters ou regiões. A configuração mínima suportada para esta estratégia são três datacenters ou regiões. O número recomendado de regiões ou centros de dados é de cinco. 
  
  Este modelo requer uma topologia de cluster mais complexa. No entanto, o benefício é que a falha de um datacenter ou região é convertida de uma catástrofe numa falha normal. Estas falhas podem ser tratadas pelos mecanismos que funcionam para aglomerados numa única região. Domínios de avaria, domínios de atualização e regras de colocação de tecidos de serviço garantem que as cargas de trabalho são distribuídas de modo a tolerar falhas normais. 
  
  Para obter mais informações sobre políticas que possam ajudar a operar serviços neste tipo de cluster, consulte políticas de [colocação para serviços de tecido](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)de serviço.

### <a name="random-failures-that-lead-to-cluster-failures"></a>Falhas aleatórias que levam a falhas de cluster
Serviço Tecido tem o conceito de *nósodes*de sementes. Estes são nós que mantêm a disponibilidade do cluster subjacente. 

Os nós de sementes ajudam a garantir que o cluster se mantenha acordado estabelecendo arrendamentos com outros nós e servindo como desempates durante certos tipos de falhas. Se falhas aleatórias removerem a maioria dos nós de sementes no cluster e não forem trazidos de volta rapidamente, o seu cluster desliga-se automaticamente. O aglomerado falha então. 

Em Azure, o Prestador de Recursos de Tecido de Serviço gere as configurações de cluster de tecido de serviço. Por padrão, o Fornecedor de Recursos distribui nós de sementes através de domínios de falha e atualização para o *tipo de nó primário*. Se o tipo de nó primário estiver marcado como durabilidade prateada ou dourada, quando remover um nó de sementes (quer por escalano no seu nó primário, quer removendo-o manualmente), o cluster tentará promover outro nó não semente a partir da capacidade disponível do nó primário. Esta tentativa falhará se tiver menos capacidade disponível do que o nível de fiabilidade do cluster necessário para o seu tipo principal de nó.

Em ambos os clusters autónomos de tecido de serviço e Azure, o tipo principal de nó é o que executa as sementes. Ao definir um nó primário, o Tecido de Serviço tirará automaticamente partido do número de nós fornecidos criando até nove nós de sementes e sete réplicas de cada serviço de sistema. Se um conjunto de falhas aleatórias eliminar a maioria dessas réplicas simultaneamente, os serviços do sistema entrarão em perda de quórum. Se a maioria dos nós de sementes for em perdem-se, o aglomerado será encerrado logo depois.

## <a name="next-steps"></a>Passos seguintes
- Aprenda a simular várias falhas utilizando a estrutura de [testability](service-fabric-testability-overview.md).
- Leia outros recursos de recuperação de desastres e de alta disponibilidade. A Microsoft publicou uma grande quantidade de orientação sobre estes tópicos. Embora alguns destes recursos se refiram a técnicas específicas para uso noutros produtos, eles contêm muitas boas práticas gerais que você pode aplicar no contexto do Tecido de Serviço:
  - [Lista de verificação de disponibilidade](/azure/architecture/checklist/resiliency-per-service)
  - [Realizar um exercício de recuperação de desastres](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Recuperação após desastre e elevada disponibilidade para aplicações do Azure][dr-ha-guide]
- Saiba mais sobre as opções de suporte do [Tecido de Serviço.](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
