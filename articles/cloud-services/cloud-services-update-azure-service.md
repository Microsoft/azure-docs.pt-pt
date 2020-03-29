---
title: Como atualizar um serviço de nuvem / Microsoft Docs
description: Saiba como atualizar os serviços de cloud em Azure. Saiba como uma atualização de um serviço na nuvem prossegue para garantir a disponibilidade.
services: cloud-services
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: tagore
ms.openlocfilehash: 731f4e8cc8a93f33d6887f44fc8d09585e92a75a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75360349"
---
# <a name="how-to-update-a-cloud-service"></a>Como atualizar um serviço de nuvem

Atualizar um serviço na nuvem, incluindo tanto as suas funções como o so convidado, é um processo de três etapas. Em primeiro lugar, os binários e ficheiros de configuração para o novo serviço de nuvem ou versão S devem ser carregados. Em seguida, o Azure reserva a computação e os recursos de rede para o serviço na nuvem com base nos requisitos da nova versão do serviço na nuvem. Por fim, o Azure realiza uma atualização rolante para atualizar incrementalmente o inquilino para a nova versão ou o S O de hóspedes, preservando ao mesmo tempo a sua disponibilidade. Este artigo discute os detalhes deste último passo – a atualização em andamento.

## <a name="update-an-azure-service"></a>Atualizar um Serviço Azure
O Azure organiza as suas instâncias em agrupamentos lógicos chamados domínios de atualização (UD). Os domínios de upgrade (UD) são conjuntos lógicos de instâncias de papéis que são atualizados como um grupo.  O Azure atualiza um serviço na nuvem uma UD de cada vez, o que permite que casos em outros UDs continuem a servir o tráfego.

O número padrão de domínios de atualização é de 5. Pode especificar um número diferente de domínios de atualização, incluindo o atributo do DomainCount de upgrade no ficheiro de definição do serviço (.csdef). Para obter mais informações sobre o atributo do UpgradeDomainCount, consulte [Azure Cloud Services Definition Schema (.csdef File)](https://docs.microsoft.com/azure/cloud-services/schema-csdef-file).

Quando executa uma atualização no local de uma ou mais funções no seu serviço, o Azure atualiza conjuntos de instâncias de funções de acordo com o domínio de atualização a que pertencem. O Azure atualiza todas as ocorrências num determinado domínio de upgrade – impedindo-os, atualizando-os, trazendo-os de volta à linha – e depois passa para o próximo domínio. Ao parar apenas as instâncias em execução no domínio atual de atualização, o Azure garante que ocorre uma atualização com o menor impacto possível no serviço de funcionamento. Para mais informações, consulte [como a atualização prossegue](#howanupgradeproceeds) mais tarde neste artigo.

> [!NOTE]
> Embora a **atualização** e **atualização** de termos tenham um significado ligeiramente diferente no contexto Azure, podem ser utilizados intercambiavelmente para os processos e descrições das funcionalidades deste documento.
>
>

O seu serviço deve definir pelo menos dois casos de um papel para que esse papel seja atualizado no local sem tempo de inatividade. Se o serviço consistir em apenas uma instância de uma função, o seu serviço estará indisponível até que a atualização no local esteja concluída.

Este tópico aborda as seguintes informações sobre as atualizações do Azure:

* [Permitida seleções de serviço durante uma atualização](#AllowedChanges)
* [Como uma atualização procede](#howanupgradeproceeds)
* [Reversão de uma atualização](#RollbackofanUpdate)
* [Início de múltiplas operações de mutação numa implantação em curso](#multiplemutatingoperations)
* [Distribuição de funções em domínios de upgrade](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Permitida seleções de serviço durante uma atualização
A tabela seguinte mostra as alterações permitidas a um serviço durante uma atualização:

| Alterações permitidas ao alojamento, serviços e funções | Atualização no local | Encenação (troca VIP) | Eliminar e reutilizar |
| --- | --- | --- | --- |
| Versão do sistema operativo |Sim |Sim |Sim |
| nível de confiança .NET |Sim |Sim |Sim |
| Tamanho da máquina virtual<sup>1</sup> |Sim<sup>2</sup> |Sim |Sim |
| Configurações de armazenamento local |Aumentar apenas<sup>2</sup> |Sim |Sim |
| Adicionar ou remover funções num serviço |Sim |Sim |Sim |
| Número de casos de um papel específico |Sim |Sim |Sim |
| Número ou tipo de pontos finais para um serviço |Sim<sup>2</sup> |Não |Sim |
| Nomes e valores das definições de configuração |Sim |Sim |Sim |
| Valores (mas não nomes) de configurações de configuração |Sim |Sim |Sim |
| Adicionar novos certificados |Sim |Sim |Sim |
| Alterar os certificados existentes |Sim |Sim |Sim |
| Implementar novo código |Sim |Sim |Sim |

<sup>1</sup> Alteração de tamanho limitada ao subconjunto de tamanhos disponíveis para o serviço de nuvem.

<sup>2</sup> Requer versões Azure SDK 1.5 ou posteriores.

> [!WARNING]
> Mudar o tamanho da máquina virtual destruirá os dados locais.
>
>

Os seguintes itens não são suportados durante uma atualização:

* Mudar o nome de um papel. Retire e adicione o papel com o novo nome.
* Alteração da contagem de domínio de atualização.
* Diminuindo o tamanho dos recursos locais.

Se estiver a fazer outras atualizações à definição do seu serviço, como a diminuição do tamanho do recurso local, tem de realizar uma atualização de swap VIP. Para mais informações, consulte [A Implementação de Swap](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Como uma atualização procede
Pode decidir se pretende atualizar todas as funções do seu serviço ou uma única função no serviço. Em qualquer dos casos, todos os casos de cada função que está a ser atualizado e pertencem ao primeiro domínio de atualização são parados, atualizados e trazidos de volta online. Uma vez que estejam novamente on-line, as instâncias no segundo domínio de upgrade são paradas, atualizadas e trazidas de volta on-line. Um serviço de nuvem pode ter, no máximo, um upgrade ativo de cada vez. A atualização é sempre realizada contra a versão mais recente do serviço de cloud.

O diagrama seguinte ilustra como a atualização funciona se estiver a atualizar todas as funções do serviço:

![Serviço de upgrade](media/cloud-services-update-azure-service/IC345879.png "Serviço de upgrade")

Este próximo diagrama ilustra como a atualização funciona se estiver a atualizar apenas uma única função:

![Papel de upgrade](media/cloud-services-update-azure-service/IC345880.png "Papel de upgrade")  

Durante uma atualização automática, o Controlador de Tecidos Azure avalia periodicamente a saúde do serviço de nuvem para determinar quando é seguro andar na próxima UD. Esta avaliação de saúde é realizada por função e considera apenas casos na versão mais recente (ou seja, casos de UDs que já foram andados). Verifica que um número mínimo de exemplos, para cada papel, atingiram um estado terminal satisfatório.

### <a name="role-instance-start-timeout"></a>Role Instance Iniciar Timeout
O Controlador de Tecidos esperará 30 minutos para que cada instância de funções chegue a um estado iniciado. Se a duração do tempo decorrido, o Controlador de Tecidos continuará a caminhar para a próxima instância de funções.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Impacto para impulsionar dados durante atualizações do Serviço cloud

Ao atualizar um serviço de uma única instância para várias instâncias, o seu serviço será desativado enquanto a atualização é realizada devido à forma como o Azure atualiza os serviços. O acordo de nível de serviço que garante a disponibilidade de serviços só se aplica aos serviços que são implantados com mais de um caso. A lista seguinte descreve como os dados de cada unidade são afetados por cada cenário de atualização de serviço seleções do Azure:

|Cenário|Unidade C|Unidade D|E Drive|
|--------|-------|-------|-------|
|VM reboot|Preservado|Preservado|Preservado|
|Reinicialização do portal|Preservado|Preservado|Destruído|
|Reimagem do portal|Preservado|Destruído|Destruído|
|Upgrade no local|Preservado|Preservado|Destruído|
|Migração do nó|Destruído|Destruído|Destruído|

Note que, na lista acima, o E: unidade representa o impulso radicular do papel, e não deve ser codificado em código. Em vez disso, utilize a variável ambiente **%RoleRoot%** para representar a unidade.

Para minimizar o tempo de inatividade ao atualizar um serviço de uma instância única, implemente um novo serviço de várias instâncias para o servidor de encenação e realize uma troca VIP.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Reversão de uma atualização
O Azure oferece flexibilidade na gestão de serviços durante uma atualização, permitindo-lhe iniciar operações adicionais num serviço, depois de o pedido inicial de atualização ser aceite pelo Controlador de Tecidos Azure. Uma reversão só pode ser realizada quando uma atualização (alteração de configuração) ou atualização estiver em **andamento** na implementação. Uma atualização ou atualização é considerada em curso desde que haja pelo menos uma instância do serviço que ainda não foi atualizada para a nova versão. Para testar se é permitida uma reversão, verifique o valor da bandeira RollbackAllowed, devolvida pelas operações [Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) e Get Cloud [Service Properties,](/previous-versions/azure/reference/ee460806(v=azure.100)) está definida como verdadeira.

> [!NOTE]
> Só faz sentido ligar para o Rollback numa atualização ou atualização **no local** porque as atualizações de swapVIP envolvem substituir uma instância inteira do seu serviço por outra.
>
>

O reversão de uma atualização em curso tem os seguintes efeitos na implantação:

* Quaisquer casos de funções que ainda não tivessem sido atualizados ou atualizados para a nova versão não são atualizados ou atualizados, uma vez que esses casos já estão a executar a versão-alvo do serviço.
* Quaisquer casos de funções que já tivessem sido atualizados\*ou atualizados para a nova\*versão do pacote de serviços (.cspkg) ou a configuração do serviço (.cscfg) (ou ambos os ficheiros) são revertidos para a versão pré-actualização destes ficheiros.

Isto funcionalmente é fornecido pelas seguintes características:

* A operação [de atualização de atualização ou atualização de atualização,](/previous-versions/azure/reference/hh403977(v=azure.100)) que pode ser chamada numa atualização de configuração (desencadeada por chamada de Configuração de Implementação de [Alterações)](/previous-versions/azure/reference/ee460809(v=azure.100))ou uma atualização (desencadeada por chamada de Atualização de [Atualização),](/previous-versions/azure/reference/ee460793(v=azure.100))desde que haja pelo menos uma instância no serviço que ainda não tenha sido atualizada para a nova versão.
* O elemento Lock e o elemento RollbackAllowed, que são devolvidos como parte do corpo de resposta das operações [get deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) e get Cloud [Service Properties:](/previous-versions/azure/reference/ee460806(v=azure.100))

  1. O elemento 'Locked' permite detetar quando uma operação mutante pode ser invocada numa determinada implantação.
  2. O elemento RollbackAllowed permite-lhe detetar quando a operação [de atualização de atualização ou atualização](/previous-versions/azure/reference/hh403977(v=azure.100)) pode ser chamada numa determinada implementação.

  Para efetuar uma reversão, não tem de verificar tanto os elementos Locked como os RollbackAllowed. Basta confirmar que o RollbackAllowed está pronto para ser verdade. Estes elementos só são devolvidos se estes métodos forem invocados utilizando o cabeçalho de pedido definido para "x-ms-versão: 2011-10-01" ou uma versão posterior. Para obter mais informações sobre cabeçalhos de versão, consulte versão [de Gestão de Serviços](/previous-versions/azure/gg592580(v=azure.100)).

Existem algumas situações em que não é suportada uma reversão de uma atualização ou atualização, estas são as seguintes:

* Redução dos recursos locais - Se a atualização aumentar os recursos locais para um papel, a plataforma Azure não permite o retrocesso.
* Limitações de quota - Se a atualização tiver sido uma operação de redução de escala, pode deixar de ter quota de cálculo suficiente para completar a operação de reversão. Cada subscrição do Azure tem uma quota associada a ela que especifica o número máximo de núcleos que podem ser consumidos por todos os serviços hospedados que pertencem a essa subscrição. Se a execução de uma atualização de uma determinada atualização colocar a sua subscrição sobre a quota, então não será ativada uma reversão.
* Condição da corrida - Se a atualização inicial tiver sido concluída, não é possível um retrocesso.

Um exemplo de quando a reversão de uma atualização pode ser útil é se estiver a utilizar a operação de implementação de [atualização](/previous-versions/azure/reference/ee460793(v=azure.100)) em modo manual para controlar a taxa a que é lançada uma grande atualização no local para o seu serviço hospedado no Azure.

Durante o lançamento da atualização, ligue para a [atualização](/previous-versions/azure/reference/ee460793(v=azure.100)) em modo manual e comece a andar em domínios de atualização. Se em algum momento, ao monitorizar a atualização, notar algumas instâncias de função nos primeiros domínios de atualização que examina não responderam, pode ligar para a operação [Derollback Update ou Upgrade](/previous-versions/azure/reference/hh403977(v=azure.100)) na implementação, o que deixará intactas as instâncias que ainda não tinham sido atualizadas e as instâncias de reversão que tinham sido atualizadas para o pacote e configuração de serviço anteriores.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Início de múltiplas operações de mutação numa implantação em curso
Em alguns casos, pode querer iniciar múltiplas operações de mutação simultânea numa implantação em curso. Por exemplo, pode efetuar uma atualização de serviço e, enquanto essa atualização estiver a ser lançada em todo o seu serviço, pretende efetuar algumas alterações, por exemplo, revera a atualização, aplicar uma atualização diferente ou até mesmo apagar a implementação. Um caso em que isso possa ser necessário é se uma atualização de serviço contiver um código buggy que faz com que uma instância de funções atualizada se despenhe repetidamente. Neste caso, o Controlador de Tecidos Azure não poderá fazer progressos na aplicação dessa atualização, uma vez que um número insuficiente de casos no domínio atualizado são saudáveis. Este estado é referido como uma *implantação presa.* Pode desmarcar a implementação religando a atualização ou aplicando uma nova atualização sobre a falha.

Uma vez que o pedido inicial de atualização ou atualização do serviço tenha sido recebido pelo Controlador de Tecidos Azure, pode iniciar operações de mutação subsequentes. Ou seja, não precisa esperar que a operação inicial termine antes de iniciar outra operação em mutação.

O início de uma segunda operação de atualização enquanto a primeira atualização estiver em curso funcionará de forma semelhante à operação de reversão. Se a segunda atualização estiver em modo automático, o primeiro domínio de atualização será atualizado imediatamente, possivelmente levando a que casos de vários domínios de atualização estejam offline no mesmo momento.

As operações de mutação são as seguintes: Configuração de [implementação de alterações,](/previous-versions/azure/reference/ee460809(v=azure.100)) [implementação de atualização,](/previous-versions/azure/reference/ee460793(v=azure.100))estado de implementação de [atualização,](/previous-versions/azure/reference/ee460808(v=azure.100)) [eliminação](/previous-versions/azure/reference/ee460815(v=azure.100))de implementação e [atualização ou atualização](/previous-versions/azure/reference/hh403977(v=azure.100))de reversão .

Duas operações, [Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) e [Get Cloud Service Properties,](/previous-versions/azure/reference/ee460806(v=azure.100))devolvam a bandeira bloqueada que pode ser examinada para determinar se uma operação mutante pode ser invocada numa determinada implantação.

Para chamar a versão destes métodos que devolvem a bandeira bloqueada, deve definir o cabeçalho de pedido para "x-ms-versão: 2011-10-01" ou um posterior. Para obter mais informações sobre cabeçalhos de versão, consulte versão [de Gestão de Serviços](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribuição de funções em domínios de upgrade
O Azure distribui casos de uma função uniformemente através de um conjunto de domínios de atualização, que podem ser configurados como parte do ficheiro de definição de serviço (.csdef). O número máximo de domínios de atualização é de 20 e o padrão é de 5. Para obter mais informações sobre como modificar o ficheiro de definição de serviço, consulte a Definição de [Serviço Azure Schema (.csdef File)](cloud-services-model-and-package.md#csdef).

Por exemplo, se o seu papel tiver dez instâncias, por padrão cada domínio de atualização contém duas instâncias. Se o seu papel tem 14 instâncias, então quatro dos domínios de atualização contêm três instâncias, e um quinto domínio contém dois.

Os domínios de atualização são identificados com um índice de base zero: o primeiro domínio de atualização tem uma identificação de 0, e o segundo domínio de atualização tem uma identificação de 1, e assim por diante.

O diagrama seguinte ilustra como um serviço do que contém duas funções são distribuídos quando o serviço define dois domínios de atualização. O serviço está a executar oito instâncias do papel web e nove casos do papel do trabalhador.

![Distribuição de Domínios de Upgrade](media/cloud-services-update-azure-service/IC345533.png "Distribuição de Domínios de Upgrade")

> [!NOTE]
> Note que o Azure controla a forma como os casos são atribuídos em domínios de atualização. Não é possível especificar quais as instâncias atribuídas a que domínio.
>
>

## <a name="next-steps"></a>Passos seguintes
[Como gerir Serviços Cloud](cloud-services-how-to-manage-portal.md)  
[Como monitorizar os serviços de cloud](cloud-services-how-to-monitor.md)  
[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)  



