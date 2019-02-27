---
title: Recuperação de desastres do Service Fabric do Azure | Documentos da Microsoft
description: O Azure Service Fabric oferece as capacidades necessárias para lidar com todos os tipos de desastres. Este artigo descreve os tipos de desastres que podem ocorrer e como lidar com eles.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: a074f0f9c08803e7227bcfb218863a5f0f094306
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56875400"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Recuperação após desastre no Azure Service Fabric
Uma parte crítica do fornecimento de elevada disponibilidade é garantir que os serviços podem sobreviver todos os diferentes tipos de falhas. Isto é especialmente importante para as falhas são não planeadas e fora do seu controlo. Este artigo descreve alguns modos de falha comuns que podem ser desastres se não for modelados e gerida corretamente. Como Abordaremos atenuações e ações de caso de qualquer forma, um desastre. O objetivo é limitar ou eliminar o risco de tempo de inatividade ou perda de dados quando estes ocorrerem falhas planeadas ou caso contrário, ocorrer.

## <a name="avoiding-disaster"></a>Evitando após desastre
Objetivo principal do Service Fabric é que o ajudarão a modelar o ambiente e os serviços de forma que os tipos comuns de falha não são desastres. 

Em geral, existem dois tipos de cenários de desastre/falha:

1. Falhas de hardware ou software
2. Falhas operacionais

### <a name="hardware-and-software-faults"></a>Falhas de hardware e software
Falhas de hardware e software são imprevisíveis. A maneira mais fácil para resistir a falhas está em execução mais cópias do serviço estendido em limites de falhas de hardware ou software. Por exemplo, se o seu serviço executar apenas numa determinada máquina, em seguida, a falha de uma máquina é um desastre para esse serviço. A forma simple de evitar desse desastre é garantir que o serviço está sendo executado em várias máquinas. Teste também é necessário para garantir que a falha de uma máquina não interrompe o serviço em execução. Planeamento de capacidade garante uma instância de substituição pode ser criada em outro lugar e que a redução de capacidade não sobrecarregar os restantes serviços. O mesmo padrão funciona independentemente do que deseja evitar a falha de. Por exemplo. Se estiver preocupado com a falha de uma SAN, que a encontrar vários SANs. Se estiver preocupado com a perda de um bastidor de servidores, execute por vários suportes. Se estiver preocupado com a perda de datacenters, seu serviço deve executar em várias regiões do Azure ou centros de dados. 

Quando em execução neste tipo de modo distribuído, ainda pode ser alguns tipos de falhas em simultâneo, mas único e até mesmo várias falhas de um tipo específico (ex: uma única falha de ligação VM ou de rede) são manipulados automaticamente (e por isso, já não é um "desastre"). O Service Fabric fornece vários mecanismos para expandir o cluster e identificadores trazer falha nós e os serviços de volta. Service Fabric também permite a execução de muitas instâncias dos seus serviços para evitar esses tipos de falhas não planeadas de decisivo para desastres real.

Pode haver motivos por que motivo o executar uma implantação grande o suficiente para englobar falhas não é viável. Por exemplo, pode demorar mais recursos de hardware do que está disposto a pagar em relação a possibilidade de falha. Ao lidar com aplicações distribuídas, é possível que saltos de comunicação adicional ou a replicação de estado de custos numa latência inaceitável causas distâncias geográfica. Onde essa linha é desenhada é diferente para cada aplicação. Para falhas de software, especificamente, a falha pode ser no serviço que está a tentar dimensionar. Neste caso mais cópias não impedir que desastre, uma vez que a condição de falha está correlacionada em todas as instâncias.

### <a name="operational-faults"></a>Falhas operacionais
Mesmo que o seu serviço é abrangido por todo o mundo com várias redundâncias, ainda pode assistir a eventos desastrosos. Por exemplo, se alguém acidentalmente reconfigura o nome de dns para o serviço ou elimina-a imediatamente. Por exemplo, digamos que tivesse um serviço do Service Fabric com monitorização de estado e alguém eliminado acidentalmente esse serviço. A menos que há alguns outra mitigação e todos os Estado em que estava esse serviço é agora ficaram no passado. Esses tipos de desastres operacionais ("UPS") exigem atenuações diferentes e os passos para a recuperação de falhas não planeadas regulares. 

Melhores formas de evitar esses tipos de falhas operacionais estão a
1. restringir o acesso operacional para o ambiente
2. estritamente auditar operações perigosas
3. impor a automatização, a evitar manual ou para fora de alterações de banda e a validar alterações específicas contra o ambiente real antes de aplicá-los
4. Certifique-se de que as operações destrutivas são "soft". Operações de forma recuperável não entrem em vigor imediatamente ou podem ser anuladas dentro de determinada janela de tempo

O Service Fabric fornece alguns mecanismos para evitar falhas operacionais, como a prestação [baseado em funções](service-fabric-cluster-security-roles.md) controlo para operações de cluster de acesso. No entanto, a maioria dessas falhas operacionais exige esforços organizacionais e de outros sistemas. Service Fabric fornecem um mecanismo para sobreviverem à falhas operacionais, mais notavelmente cópia de segurança e restaurar para serviços com estado.

## <a name="managing-failures"></a>Gestão de falhas
O objetivo do Service Fabric é quase sempre gestão automática de falhas. No entanto, para lidar com alguns tipos de falhas, serviços tem de ter código adicional. Outros tipos de falhas devem _não_ automaticamente resolvido devido a motivos de continuidade do negócio e de segurança. 

### <a name="handling-single-failures"></a>Processamento de falhas únicas
Máquinas únicas podem falhar por diversos motivos. Algumas são causas de hardware, como fontes de alimentação e falhas de hardware de rede. Outras falhas são em software. Estes incluem falhas de sistema de operativo real e o próprio serviço. Recursos de infraestrutura do serviço Deteta automaticamente esses tipos de falhas, incluindo casos em que a máquina fica isolada de outras máquinas devido a problemas de rede.

Independentemente do tipo de serviço, executar uma única instância resultados em tempo de inatividade para esse serviço se essa cópia única do código falhar por algum motivo. 

Para lidar com qualquer falha única, a coisa mais simples, que pode fazer é garantir que seus serviços são executados em mais de um nó por predefinição. Para serviços sem estado, isso pode ser feito ao ter uma `InstanceCount` superior a 1. Para serviços com estado, a recomendação mínima é sempre uma `TargetReplicaSetSize` e `MinReplicaSetSize` de, pelo menos, 3. Executar mais cópias do seu código de serviço garante que seu serviço pode processar a falha de qualquer único automaticamente. 

### <a name="handling-coordinated-failures"></a>Falhas de processamento de coordenada
Falhas de coordenada podem acontecer num cluster devido a uma planeada ou falhas de infraestrutura não planeadas e alterações ou as alterações de software planeada. Service Fabric modela zonas de infraestrutura que experienciar falhas de coordenada como domínios de falha. Áreas que se vão refletir as alterações de software coordenada são modeladas como os domínios de atualização. Obter mais informações sobre domínios de falha e a atualização estão em [este documento](service-fabric-cluster-resource-manager-cluster-description.md) que descreve a topologia do cluster e a definição.

Por predefinição o Service Fabric considera os domínios de atualização e de falha quando planear onde os serviços devem ser executados. Por predefinição, o Service Fabric tenta garantir que seus serviços são executados por vários domínios de atualização e de falha para que se acontecem de alterações planeadas ou os serviços permanecem disponíveis. 

Por exemplo, digamos que a falha de uma fonte de energia faz com que um rack de máquinas para falhem em simultâneo. Com várias cópias do serviço em execução a perda de número de máquinas no caso de falha de domínio de falhas transforma em apenas outro exemplo de falha único para um determinado serviço. É por isso o gerenciamento de domínios de falha é fundamental para garantir a elevada disponibilidade dos seus serviços. Ao executar o Service Fabric no Azure, os domínios de falha são geridos automaticamente. Em outros ambientes, podem não ser. Se estiver a criar seus próprios clusters no local, certifique-se de que mapear e planejar o layout do domínio de falhas corretamente.

Domínios de atualização são úteis para modelar as áreas onde software vai ser atualizado ao mesmo tempo. Por este motivo, os domínios de atualização também, muitas vezes, defina os limites em que o software é desativado durante as atualizações planejadas. Atualizações do Service Fabric e seus serviços, siga o mesmo modelo. Para saber mais sobre implementar atualizações, os domínios de atualização e o modelo de estado de funcionamento do Service Fabric que ajuda a impedir que as alterações indesejadas afetar o cluster e o seu serviço, consulte estes documentos:

 - [Atualização da aplicação](service-fabric-application-upgrade.md)
 - [Tutorial de atualização de aplicação](service-fabric-application-upgrade-tutorial.md)
 - [Modelo de serviço de estado de funcionamento de recursos de infraestrutura](service-fabric-health-introduction.md)

É possível visualizar o layout do seu cluster com o mapa de cluster fornecido no [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>
![Nós-se distribuídas por domínios de falha no Service Fabric Explorer][sfx-cluster-map]
</center>

> [!NOTE]
> Áreas de falha, atualizações, executar muitas instâncias de seu código de serviço e o estado, sem interrupção de modelagem regras de colocação para garantir que seus serviços é executado em vários domínios de atualização e de falha e monitorização de estado de funcionamento incorporado são simplesmente **alguns** do funcionalidades de que o Service Fabric fornece para evitar problemas operacionais normais e falhas decisivo para desastres. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Processamento de falhas de hardware ou software simultâneas
Acima, falamos sobre falhas de únicas. Como pode ver, são fáceis de lidar com para os serviços com e sem monitoração de estado apenas mantendo mais cópias do código (e estado) em execução em falha e domínios de atualização. Também podem acontecer múltiplas falhas simultâneas de aleatórias. Estes têm maior probabilidade de levar a um desastre real.


### <a name="random-failures-leading-to-service-failures"></a>Falhas aleatórias levavam a falhas de serviço
Vamos supor que o serviço tinha um `InstanceCount` de 5 e vários nós em execução dessas instâncias de todos os falhou ao mesmo tempo. Service Fabric responde ao criar automaticamente instâncias de substituição nos outros nós. Irá continuar a criar substituições até que o serviço seja novamente para a sua contagem de instância desejada. Como outro exemplo, digamos que havia um serviço sem estado com um `InstanceCount`de -1, que significa que ele é executado em todos os nós válida no cluster. Digamos que algumas dessas instâncias eram a falhar. Neste caso, o Service Fabric avisos que o serviço não está no estado pretendido e tenta criar as instâncias em nós de onde eles estão em falta. 

Para serviços com estado, a situação depende se o serviço se manteve Estado ou não. Ele também depende da forma como o serviço tinha de muitas réplicas e quantas tentativas. Determinar se a ocorrência de um desastre para um serviço com monitorização de estado e gerenciá-lo segue as três fases:

1. Determinar se houve perda de quórum ou não
 - Uma perda de quórum é sempre que a maioria das réplicas de um serviço com monitorização de estado são baixo, ao mesmo tempo, incluindo as primárias.
2. Determinar se a perda de quórum está ou não permanente
 - A maioria das vezes, as falhas são transitórias. Processos são reiniciados, nós são reiniciados, as VMs são reinicializadas, as partições de rede tratá-lo. Por vezes, no entanto, as falhas são permanentes. 
    - Para serviços persistentes sem estado, uma falha de um quórum ou mais dos resultados de réplicas _imediatamente_ em perda de quórum permanente. Quando o Service Fabric Deteta a perda de quórum no serviço não persistentes com estado, que imediatamente avança para o passo 3 com a declaração de perda de dados (potenciais). Continuar para dados perda faz sentido porque o Service Fabric sabe que não há nenhuma razão para aguardar que as réplicas voltar atrás, porque, mesmo que eles foram recuperados deve estar vazios.
    - Para serviços persistentes com estado, uma falha de um quórum ou mais das réplicas faz com que o Service Fabric iniciar a aguardar que as réplicas voltar atrás e restaurar o quórum. Isso resulta numa indisponibilidade do serviço para qualquer _escreve_ para a partição afetada (ou "conjunto de réplicas") do serviço. No entanto, leituras ainda será possíveis com garantias de consistência reduzida. O período de tempo que o Service Fabric aguarda para quórum para serem restaurados predefinido é infinito, uma vez que continuar é um evento de perda de dados (potenciais) e carrega outros riscos. Substituir a predefinição `QuorumLossWaitDuration` valor é possível, mas não é recomendado. Em vez disso, neste momento, todos os esforços devem ser feitos para restaurar as réplicas de baixo. Isto requer trazer os nós que estão indisponíveis de cópia de segurança e garantir que eles podem voltar a montar as unidades onde armazenados o estado persistente local. Se a perda de quórum for provocada por falhas de processo, o Service Fabric tenta automaticamente recriar os processos e reiniciar as réplicas dentro deles. Se isto falhar, o Service Fabric reporta erros de estado de funcionamento. Se eles poderão ser resolvidos em seguida, as réplicas normalmente voltar. Às vezes, no entanto, as réplicas não podem ser recuperadas. Por exemplo, as unidades de tudo ter falhado ou as máquinas fisicamente destruída alguma forma. Nestes casos, temos agora um evento de perda de quórum permanente. Para informar ao Service Fabric para parar a aguardar que as réplicas de baixo voltar atrás, um administrador de cluster tem de determinar quais partições dos quais serviços são afetados e chamarem o `Repair-ServiceFabricPartition -PartitionId` ou ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API.  Esta API permite especificar o ID da partição para mover fora QuorumLoss e em potencial de perda.

  > [!NOTE]
  > É _nunca_ seguro utilizar esta API que de forma direcionada em partições específicas. 
  >

3. Determinar se houve perda de dados real e restaurar a partir de cópias de segurança
  - Quando o Service Fabric chama o `OnDataLossAsync` método, é sempre devido _suspeita_ perda de dados. Service Fabric garante que essa chamada é enviada para o _melhor_ réplica restante. Isso é que qualquer réplica fez o progresso da maioria dos. O motivo pelo qual sempre Dizemos _suspeita_ perda de dados é que é possível que a réplica restante tem, na verdade, todos os Estados do mesmo como o principal fez quando ele foi desativado. No entanto, sem esse Estado ao compará-lo para, não existe nenhuma boa maneira de Service Fabric ou operadores saber com certeza. Neste momento, o Service Fabric também sabe a que outras réplicas não estão voltando. Essa era a decisão tomada quando estamos parado a aguardar a perda de quórum seja resolvido automaticamente. O melhor curso de ação para o serviço é normalmente congelar e aguarde pela intervenção administrativa específica. Portanto, o que faz uma implementação típica do `OnDataLossAsync` método fazer?
  - Primeiro, inicie a sessão que `OnDataLossAsync` foi acionada e disparar alertas administrativas necessárias.
   - Normalmente, nesse momento, pause e aguarde para decisões mais e ações manuais a tomar. Isto acontece porque, mesmo que as cópias de segurança estão disponíveis poderão ter de ser preparado. Por exemplo, se dois serviços diferentes coordenam informações, essas cópias de segurança poderão ter de ser modificados para garantir que depois que o restauro, acontece que as informações de cuidados de saúde esses dois serviços sobre são consistentes. 
  - Muitas vezes, há também alguns outra telemetria ou esgotar do serviço. Estes metadados poderão estar contido nos outros serviços ou nos registos. Estas informações podem ser utilizadas necessárias para determinar se houve todas as chamadas recebidas e processadas em primário que não estavam presentes na cópia de segurança ou replicadas para esta réplica específica. Isso poderão ter de ser reproduzidos ou adicionada à cópia de segurança antes de restauração é viável.  
   - Comparações de estado da réplica restantes àqueles contidos em qualquer cópias de segurança que estão disponíveis. Se utilizar as coleções fiáveis do Service Fabric, em seguida, há ferramentas e processos disponível para fazer isso, descrito em [este artigo](service-fabric-reliable-services-backup-restore.md). O objetivo é saber se o estado dentro da réplica é suficiente ou também que a cópia de segurança pode estar em falta.
  - Depois de fazer a comparação e, se necessário, o restauro foi concluído, o código do serviço deve retornar true se foram efetuadas quaisquer alterações de estado. Se a réplica for determinado que era a melhor cópia do Estado e feito sem alterações, em seguida, retorne false. "Verdadeiro" indica que qualquer _outros_ restantes réplicas agora podem ser incompatíveis com esta. Irá ser removidos e reconstruídas nesta réplica. FALSE indica que foram feitas sem alterações de estado, para que as outras réplicas podem manter o que eles têm. 

É extremamente importante que os autores de serviços praticar potenciais cenários de perda e de falha de dados antes dos serviços nunca são implantados na produção. Para proteger contra a possibilidade de perda de dados, é importante periodicamente [cópia de segurança do estado](service-fabric-reliable-services-backup-restore.md) de qualquer um dos seus serviços com monitoração de estado para um armazenamento georredundante. Também tem de garantir que tem a capacidade de restaurá-lo. Uma vez que as cópias de segurança de vários serviços diferentes são efetuadas em alturas diferentes, precisa garantir que, após um restauro, os serviços têm uma exibição consistente entre si. Por exemplo, considere uma situação em que um serviço gera um número e armazena-a, em seguida, envia-os para outro serviço que armazena-as também. Após um restauro, poderá descobrir que o serviço de segundo tem o número, mas o primeiro faz não, porque se trata de cópia de segurança não incluiu essa operação.

Se descobrir que as réplicas restantes não são suficientes para continuar a partir de um cenário de perda de dados e não é possível reconstruir o estado do serviço de telemetria ou esgotar, a frequência das suas cópias de segurança determina o melhor objetivo de ponto de recuperação (RPO). O Service Fabric fornece várias ferramentas para vários cenários de falha, incluindo o quórum permanente e perda de dados que requerem o restauro a partir de uma cópia de segurança de teste. Estes cenários são incluídos como parte das ferramentas de capacidade de teste do Service Fabric, gerido pelo serviço de análise de falhas. Obter mais informações sobre essas ferramentas e padrões [aqui](service-fabric-testability-overview.md). 

> [!NOTE]
> Serviços do sistema também podem ser afetado perda de quórum, com o impacto seja específico para o serviço em questão. Por exemplo, perda de quórum no serviço nomenclatura tem impacto sobre a resolução de nomes, ao passo que a perda de quórum no serviço de Gestor de ativação pós-falha bloqueia a criação do novo serviço e as ativações pós-falha. Enquanto os serviços de sistema do Service Fabric seguem o mesmo padrão dos serviços de gerenciamento de estado, não é recomendável que deve tentar movê-los sem perda de quórum e em potencial perda de dados. A recomendação é em vez disso [buscar suporte](service-fabric-support.md) para determinar uma solução que está direcionada a sua situação específica.  Normalmente, é preferível simplesmente aguardar até que as réplicas baixo retornam.
>

## <a name="availability-of-the-service-fabric-cluster"></a>Disponibilidade do cluster do Service Fabric
Em termos gerais, o próprio cluster do Service Fabric é um ambiente altamente distribuído com sem pontos únicos de falha. Uma falha de um nó não causará problemas de fiabilidade para o cluster, ou de disponibilidade principalmente porque os serviços de sistema do Service Fabric seguem as mesmas diretrizes fornecidas anteriormente: são sempre executados com três ou mais réplicas por padrão e esses sistema serviços sem estado são executados em todos os nós. Os Service Fabric funcionamento em rede e de falha de deteção camadas subjacentes totalmente são distribuídas. A maioria dos serviços de sistema pode ser reconstruídos a partir dos metadados do cluster ou como ressincronizar o respetivo estado de outros locais. Pode tornar-se comprometido a disponibilidade do cluster se os serviços do sistema entrar em quórum perda situações como as descritas acima. Nestes casos poderá não conseguir executar determinadas operações no cluster, como iniciar uma atualização ou implementação de novos serviços, mas o próprio cluster é ainda uma cópia de segurança. Os serviços no já em execução irão permanecer em execução nessas condições, a menos que necessitam de escrita para os serviços do sistema para continuar a funcionar. Por exemplo, se o Gestor de ativação pós-falha está em perda de quórum todos os serviços continuará a ser executado, mas todos os serviços que não cumpram não será possível reiniciar automaticamente, uma vez que isso requer o envolvimento do Gestor de ativação pós-falha. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Falhas de um datacenter ou região do Azure
Em casos raros, um centro de dados físico pode tornar-se temporariamente indisponível devido a perda de energia ou conectividade de rede. Nestes casos, os clusters do Service Fabric e serviços desse datacenter ou região do Azure estará disponíveis. No entanto, _seus dados são preservados_. Para clusters em execução no Azure, pode ver as atualizações em falhas no [página de estado do Azure][azure-status-dashboard]. No altamente improvável evento que é um centro de dados física total ou parcialmente destruído, quaisquer clusters do Service Fabric alojados lá ou os serviços dentro deles poderão ser perdidos. Isto inclui qualquer Estado não uma cópia de segurança fora desse datacenter ou região.

Há duas estratégias diferentes para sobreviverem à falha de um único datacenter ou região permanente ou constante. 

1. Executar clusters do Service Fabric separadas em várias dessas regiões e utilizar algum mecanismo de ativação pós-falha e falhas de retorno entre esses ambientes. Esse tipo de modelo de cluster multi ativa-ativa ou ativa-passiva requer código adicional de gerenciamento e operações. Isso também exige a coordenação de cópias de segurança dos serviços num datacenter ou região, para que fiquem disponíveis em outros datacenters ou regiões quando um falha. 
2. Execute um único cluster do Service Fabric que abranja vários datacenters ou regiões. A configuração mínima suportada para isso é três datacenters ou regiões. O número recomendado de regiões ou centros de dados é cinco. Isto requer uma topologia de cluster mais complexa. No entanto, a vantagem desse modelo é que a falha de um datacenter ou região é convertida de um desastre numa falha normal. Estas falhas podem ser processadas pelos mecanismos que funcionam para clusters numa única região. Domínios de falha, domínios de atualização e regras de colocação do Service Fabric Certifique-se de que as cargas de trabalho são distribuídas para que eles toleram falhas normais. Para obter mais informações sobre as políticas que podem ajudar a operar serviços neste tipo de cluster, ler sobre [as políticas de colocação](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Falhas aleatórias levavam a falhas de cluster
Service Fabric tem o conceito de nós de semente. Estes são os nós que manter a disponibilidade do cluster subjacente. Estes nós ajudam a garantir que o cluster permanecer cópia de segurança, estabelecendo concessões com outros nós e atuando como tiebreakers durante determinados tipos de falhas de rede. Se a falhas aleatórias remover uma maioria de nós de semente no cluster e não são encaminhados back, sua cadência de Federação do cluster reduz como perdeu o quórum de nós de semente e o cluster de falha. No Azure, o fornecedor de recursos do Service Fabric gere as configurações de cluster do Service Fabric e, por predefinição, distribui nós de semente por falhas de tipo de nó primário e domínios; de atualização e Se o nodetype primário estiver marcado como Gold ou Silver durabilidade, ao remover um nó de semente, ao dimensionamento no seu nodetype primário ou remover manualmente um nó de semente, o cluster irá tentar promover outro nó não seed de nodetype primário disponível capacidade e irá falhar se tiver a capacidade de menos disponível que requer que o nível de fiabilidade de cluster para o seu tipo de nó primário.

Em ambos os clusters do Service Fabric autónomo e o Azure, o "tipo de nó primário" é aquele que executa as sementes. Ao definir um tipo de nó principal, o Service Fabric irá automaticamente tirar partido do número de nós fornecida através da criação de um máximo de nós de semente 9 e 7 réplicas de cada um dos serviços do sistema. Se um conjunto de falhas aleatórias demora horizontalmente a maioria dessas réplicas do serviço de sistema ao mesmo tempo, os serviços do sistema passarão a perda de quórum, conforme é descrito acima. Se perdem-se a maioria de nós de semente, o cluster será encerrado logo após.

## <a name="next-steps"></a>Passos Seguintes
- Saiba como simular várias falhas com a [framework de capacidade de teste](service-fabric-testability-overview.md)
- Outros recursos de recuperação após desastre e elevada disponibilidade de leitura. A Microsoft publicou uma grande quantidade de documentação de orientação sobre esses tópicos. Embora alguns destes documentos consultar técnicas específicas para uso em outros produtos, que contêm várias práticas recomendadas gerais, que pode aplicar no contexto do Service Fabric:
  - [Lista de verificação de disponibilidade](../best-practices-availability-checklist.md)
  - [Executar um teste de recuperação após desastre](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Recuperação após desastre e elevada disponibilidade para aplicações do Azure][dr-ha-guide]
- Saiba mais sobre as [opções de suporte do Service Fabric](service-fabric-support.md)


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
