---
title: Como atualizar um serviço de nuvem (clássico) | Microsoft Docs
description: Saiba como atualizar os serviços em nuvem em Azure. Saiba como é que uma atualização de um serviço de nuvem procede para garantir a disponibilidade.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 5d85003ca7b4307c308914484502ae03269f66ac
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98741116"
---
# <a name="how-to-update-an-azure-cloud-service-classic"></a>Como atualizar um Azure Cloud Service (clássico)

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Atualizar um serviço de nuvem, incluindo as suas funções e o SO convidado, é um processo de três etapas. Em primeiro lugar, os binários e ficheiros de configuração para o novo serviço de nuvem ou versão OS devem ser carregados. Em seguida, o Azure reserva recursos de computação e rede para o serviço na nuvem com base nos requisitos da nova versão do serviço cloud. Finalmente, a Azure executa uma atualização rolante para atualizar incrementalmente o inquilino para a nova versão ou so convidado, preservando ao mesmo tempo a sua disponibilidade. Este artigo discute os detalhes deste último passo – a atualização em andamento.

## <a name="update-an-azure-service"></a>Atualizar um Serviço Azure
O Azure organiza os seus exemplos de papel em agrupamentos lógicos chamados domínios de upgrade (UD). Os domínios de atualização (UD) são conjuntos lógicos de instâncias de função que são atualizados como um grupo.  O Azure atualiza um serviço de nuvem um UD de cada vez, o que permite que casos em outras UDs continuem a servir o tráfego.

O número predefinido dos domínios de atualização é 5. Pode especificar um número diferente de domínios de atualização, incluindo o atributo UpgradeDomainCount no ficheiro de definição do serviço (.csdef). Para obter mais informações sobre o atributo UpgradeDomainCount, consulte [Azure Cloud Services Definition Schema (.csdef File)](./schema-csdef-file.md).

Quando executa uma atualização in-place de uma ou mais funções no seu serviço, o Azure atualiza conjuntos de instâncias de função de acordo com o domínio de atualização a que pertencem. O Azure atualiza todas as instâncias num determinado domínio de atualização – parando-os, atualizando-os, trazendo-os de volta on-line – e depois passa para o domínio seguinte. Ao parar apenas as instâncias em execução no domínio de upgrade atual, o Azure garante que ocorre uma atualização com o menor impacto possível no serviço de execução. Para mais informações, consulte [como a atualização prossegue](#howanupgradeproceeds) mais tarde neste artigo.

> [!NOTE]
> Embora a **atualização** e **atualização** de termos tenham um significado ligeiramente diferente no contexto Azure, podem ser usados intercambiavelmente para os processos e descrições das funcionalidades deste documento.
>
>

O seu serviço deve definir pelo menos dois casos de um papel para que esse papel seja atualizado no local sem tempo de inatividade. Se o serviço consistir em apenas uma instância de uma função, o seu serviço estará indisponível até que a atualização no local esteja concluída.

Este tópico abrange as seguintes informações sobre atualizações do Azure:

* [Permitiu alterações de serviço durante uma atualização](#AllowedChanges)
* [Como uma atualização funciona](#howanupgradeproceeds)
* [Reversão de uma atualização](#RollbackofanUpdate)
* [Iniciando múltiplas operações de mutação numa implantação em curso](#multiplemutatingoperations)
* [Distribuição de funções através de domínios de upgrade](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Permitiu alterações de serviço durante uma atualização
A tabela a seguir mostra as alterações permitidas a um serviço durante uma atualização:

| Alterações permitidas ao alojamento, serviços e funções | Atualização no local | Encenado (troca VIP) | Eliminar e relançar |
| --- | --- | --- | --- |
| Versão do sistema operativo |Sim |Sim |Sim |
| .NET nível de confiança |Sim |Sim |Sim |
| Tamanho da máquina virtual<sup>1</sup> |Sim<sup>2</sup> |Sim |Sim |
| Configurações de armazenamento locais |Aumentar apenas<sup>2</sup> |Sim |Sim |
| Adicionar ou remover funções num serviço |Sim |Sim |Sim |
| Número de casos de um papel específico |Sim |Sim |Sim |
| Número ou tipo de pontos finais para um serviço |Sim<sup>2</sup> |Não |Sim |
| Nomes e valores das definições de configuração |Sim |Sim |Sim |
| Valores (mas não nomes) das definições de configuração |Sim |Sim |Sim |
| Adicionar novos certificados |Sim |Sim |Sim |
| Alterar certificados existentes |Sim |Sim |Sim |
| Implementar novo código |Sim |Sim |Sim |

<sup>1</sup> Alteração de tamanho limitada ao subconjunto de tamanhos disponíveis para o serviço de nuvem.

<sup>2</sup> Requer versões Azure SDK 1.5 ou posteriores.

> [!WARNING]
> Mudar o tamanho da máquina virtual destruirá os dados locais.
>
>

Os seguintes itens não são suportados durante uma atualização:

* Mudar o nome de um papel. Retire e adicione a função com o novo nome.
* Alteração da contagem de domínio de atualização.
* Diminuindo o tamanho dos recursos locais.

Se estiver a fazer outras atualizações à definição do seu serviço, como por exemplo diminuir o tamanho do recurso local, terá de realizar uma atualização de troca VIP. Para obter mais informações, consulte [a Implementação do Swap](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Como uma atualização funciona
Pode decidir se pretende atualizar todas as funções do seu serviço ou uma única função no serviço. Em qualquer dos casos, todos os casos de cada papel que está a ser atualizado e pertencem ao primeiro domínio de upgrade são interrompidos, atualizados e repostos online. Uma vez que estejam novamente on-line, os casos no segundo domínio de upgrade são interrompidos, atualizados e trazidos de volta on-line. Um serviço de nuvem pode ter no máximo uma atualização ativa de cada vez. A atualização é sempre realizada com a versão mais recente do serviço em nuvem.

O diagrama a seguir ilustra como a atualização prossegue se estiver a atualizar todas as funções do serviço:

![Serviço de atualização](media/cloud-services-update-azure-service/IC345879.png "Serviço de atualização")

Este próximo diagrama ilustra como a atualização prossegue se estiver a atualizar apenas uma única função:

![Papel de upgrade](media/cloud-services-update-azure-service/IC345880.png "Papel de upgrade")  

Durante uma atualização automática, o Controlador de Tecidos Azure avalia periodicamente a saúde do serviço de nuvem para determinar quando é seguro andar na próxima UD. Esta avaliação de saúde é realizada numa base por função e considera apenas casos na versão mais recente (isto é, casos de UDs que já foram pisados). Verifica-se que um número mínimo de instâncias, para cada papel, atingiu um estado terminal satisfatório.

### <a name="role-instance-start-timeout"></a>Intervalo de início de função
O Controlador de Tecidos aguardará 30 minutos para que cada instância de função atinja um estado iniciado. Se a duração do tempo de duração decorrer, o Controlador de Tecido continuará a caminhar para a próxima instância de função.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Impacto para impulsionar dados durante atualizações do Serviço Cloud

Ao atualizar um serviço de uma única instância para várias instâncias, o seu serviço será reduzido enquanto a atualização é realizada devido à forma como o Azure atualiza os serviços. O contrato de nível de serviço que garante a disponibilidade do serviço só se aplica aos serviços que são implantados com mais de uma instância. A lista a seguir descreve como os dados de cada unidade são afetados por cada cenário de atualização do serviço Azure:

|Scenario|Unidade C|Unidade D|E Drive|
|--------|-------|-------|-------|
|Reinicialização do VM|Preservado|Preservado|Preservado|
|Reinicialização do portal|Preservado|Preservado|Destruído|
|Imagem do portal|Preservado|Destruído|Destruído|
|Atualização In-Place|Preservado|Preservado|Destruído|
|Migração do nó|Destruído|Destruído|Destruído|

Note que, na lista acima, o E: unidade representa o impulso raiz do papel, e não deve ser codificado duramente. Em vez disso, utilize a variável ambiente **%RoleRoot%** para representar a unidade.

Para minimizar o tempo de inatividade ao atualizar um serviço de instância única, implante um novo serviço multi-instância para o servidor de paragem e execute uma troca VIP.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Reversão de uma atualização
O Azure proporciona flexibilidade na gestão de serviços durante uma atualização, permitindo-lhe iniciar operações adicionais num serviço, após o pedido inicial de atualização ser aceite pelo Controlador de Tecidos Azure. Um reversão só pode ser realizado quando uma atualização (alteração de configuração) ou atualização estiver no estado **de progresso** na implementação. Uma atualização ou atualização é considerada como estando em andamento, desde que exista pelo menos uma instância do serviço que ainda não tenha sido atualizada para a nova versão. Para testar se é permitida uma reversão, verifique se o valor da bandeira RollbackAllowed, devolvida pelas operações [Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) e Get Cloud [Service Properties,](/previous-versions/azure/reference/ee460806(v=azure.100)) está definida como verdadeira.

> [!NOTE]
> Só faz sentido ligar para o Rollback numa atualização ou atualização **no local,** porque as atualizações de swaps VIP envolvem a substituição de uma instância completa de execução do seu serviço por outra.
>
>

A reversão de uma atualização em curso tem os seguintes efeitos na implantação:

* Quaisquer instâncias de papel que ainda não tivessem sido atualizadas ou atualizadas para a nova versão não são atualizadas ou atualizadas, uma vez que estas ocorrências já estão a executar a versão alvo do serviço.
* Quaisquer instâncias de função que já tivessem sido atualizadas ou atualizadas para a nova versão do ficheiro do pacote de serviços \* (.cspkg) ou para a configuração do serviço \* (.cscfg) (ou ambos os ficheiros) são revertidas para a versão pré-actualização destes ficheiros.

Isto funcionalmente é fornecido pelas seguintes características:

* A operação [de atualização ou atualização de reversão,](/previous-versions/azure/reference/hh403977(v=azure.100)) que pode ser chamada numa atualização de configuração (desencadeada por chamada [Desembargação de Implementação de Alterações)](/previous-versions/azure/reference/ee460809(v=azure.100))ou uma atualização (desencadeada por chamada [Desembargação de Atualização)](/previous-versions/azure/reference/ee460793(v=azure.100))desde que exista pelo menos uma instância no serviço que ainda não tenha sido atualizada para a nova versão.
* O elemento bloqueado e o elemento RollbackAllowed, que são devolvidos como parte do corpo de resposta das operações [Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) and Get Cloud [Service Properties:](/previous-versions/azure/reference/ee460806(v=azure.100))

  1. O elemento Bloqueado permite detetar quando uma operação de mutação pode ser invocada numa determinada implantação.
  2. O elemento RollbackAllowed permite-lhe detetar quando a operação [de atualização ou atualização](/previous-versions/azure/reference/hh403977(v=azure.100)) de reversão pode ser chamada numa determinada implantação.

  Para efetuar uma reversão, não é preciso verificar tanto os elementos Bloqueados como os elementos Recuados. Basta confirmar que o RollbackAllowed está definido para ser verdadeiro. Estes elementos só são devolvidos se estes métodos forem invocados utilizando o cabeçalho de pedido definido para "versão x-ms: 2011-10-01" ou uma versão posterior. Para obter mais informações sobre os cabeçalhos de versão, consulte [a versão de Gestão de Serviços](/previous-versions/azure/gg592580(v=azure.100)).

Existem algumas situações em que uma reversão de uma atualização ou atualização não é suportada, estas são as seguintes:

* Redução dos recursos locais - Se a atualização aumentar os recursos locais para um papel, a plataforma Azure não permite o retrocesso.
* Limitações de quota - Se a atualização foi uma operação de escala para baixo, pode deixar de ter quota de cálculo suficiente para completar a operação de reversão. Cada subscrição da Azure tem uma quota associada a ela que especifica o número máximo de núcleos que podem ser consumidos por todos os serviços hospedados que pertencem a essa subscrição. Se a realização de uma reversão de uma determinada atualização colocaria a sua subscrição acima da quota, então não será ativada uma reversão.
* Condição da corrida - Se a atualização inicial tiver sido concluída, não é possível um reversão.

Um exemplo de quando a reversão de uma atualização pode ser útil é se estiver a utilizar a operação [de Implementação de Atualização](/previous-versions/azure/reference/ee460793(v=azure.100)) em modo manual para controlar a velocidade a que é lançada uma atualização importante no local para o seu serviço azure hospedado.

Durante o lançamento da atualização, [chama-se Upgrade Deployment](/previous-versions/azure/reference/ee460793(v=azure.100)) em modo manual e começa a percorrer domínios de atualização. Se em algum momento, ao monitorizar a atualização, observar algumas instâncias de função nos primeiros domínios de atualização que examina não tiver resposta, pode ligar para a operação [de atualização ou atualização](/previous-versions/azure/reference/hh403977(v=azure.100)) de reversão na implementação, o que deixará intactas as instâncias que ainda não tinham sido atualizadas e casos de reversão que tinham sido atualizados para o pacote e configuração de serviço anteriores.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Iniciando múltiplas operações de mutação numa implantação em curso
Em alguns casos, poderá querer iniciar múltiplas operações de mutação simultânea numa implantação em curso. Por exemplo, pode executar uma atualização de serviço e, enquanto essa atualização estiver a ser lançada em todo o seu serviço, pretende fazer alguma alteração, por exemplo, para revirar a atualização, aplicar uma atualização diferente ou até mesmo eliminar a implementação. Um caso em que isso pode ser necessário é se uma atualização de serviço contiver código buggy que faz com que uma instância de função atualizada se despenhe repetidamente. Neste caso, o Controlador de Tecidos Azure não poderá fazer progressos na aplicação dessa atualização, uma vez que um número insuficiente de casos no domínio atualizado são saudáveis. Este estado é referido como um *destacamento preso.* Pode desatar a implementação revirando a atualização ou aplicando uma nova atualização sobre a parte superior da falha.

Uma vez que o pedido inicial de atualização ou atualização do serviço tenha sido recebido pelo Controlador de Tecidos Azure, pode iniciar operações de mutação subsequentes. Ou seja, não é preciso esperar que a operação inicial esteja concluída antes de poder iniciar outra operação de mutação.

Iniciar uma segunda operação de atualização enquanto a primeira atualização estiver em curso será semelhante à operação de reversão. Se a segunda atualização estiver em modo automático, o primeiro domínio de atualização será atualizado imediatamente, possivelmente levando a que casos de vários domínios de atualização estejam offline no mesmo ponto do tempo.

As operações de mutação são as seguintes: [Configuração de implementação de alteração](/previous-versions/azure/reference/ee460809(v=azure.100)), [implementação de atualização,](/previous-versions/azure/reference/ee460793(v=azure.100)) [estado de implementação de atualização](/previous-versions/azure/reference/ee460808(v=azure.100)), [eliminação de implementação](/previous-versions/azure/reference/ee460815(v=azure.100))e [atualização ou atualização de reversão](/previous-versions/azure/reference/hh403977(v=azure.100)).

Duas operações, [Get Deployment](/previous-versions/azure/reference/ee460804(v=azure.100)) e Get Cloud [Service Properties,](/previous-versions/azure/reference/ee460806(v=azure.100))devolva a bandeira bloqueada que pode ser examinada para determinar se uma operação de mutação pode ser invocada numa determinada implantação.

Para chamar a versão destes métodos que devolve a bandeira bloqueada, deve definir o cabeçalho do pedido para "versão x-ms: 2011-10-01" ou mais tarde. Para obter mais informações sobre os cabeçalhos de versão, consulte [a versão de Gestão de Serviços](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribuição de funções através de domínios de upgrade
O Azure distribui as instâncias de um papel uniformemente através de um conjunto de domínios de atualização, que podem ser configurados como parte do ficheiro de definição de serviço (.csdef). O número máximo de domínios de atualização é de 20 e o padrão é de 5. Para obter mais informações sobre como modificar o ficheiro de definição de serviço, consulte [o Esquema de Definição de Serviço Azure (.ficheiro csdef)](cloud-services-model-and-package.md#csdef).

Por exemplo, se a sua função tiver dez instâncias, por padrão cada domínio de atualização contém duas instâncias. Se o seu papel tem 14 instâncias, então quatro dos domínios de atualização contêm três instâncias, e um quinto domínio contém dois.

Os domínios de atualização são identificados com um índice baseado em zero: o primeiro domínio de atualização tem um ID de 0, e o segundo domínio de atualização tem um ID de 1, e assim por diante.

O diagrama seguinte ilustra como um serviço do que contém duas funções são distribuídos quando o serviço define dois domínios de atualização. O serviço está a executar oito instâncias da função web e nove instâncias do papel do trabalhador.

![Distribuição de domínios de upgrade](media/cloud-services-update-azure-service/IC345533.png "Distribuição de domínios de upgrade")

> [!NOTE]
> Note que o Azure controla a forma como as instâncias são atribuídas em domínios de atualização. Não é possível especificar quais as instâncias a que domínio.
>
>

## <a name="next-steps"></a>Próximos passos
[Como gerir Serviços Cloud](cloud-services-how-to-manage-portal.md)  
[Como monitorizar os serviços na nuvem](cloud-services-how-to-monitor.md)  
[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)