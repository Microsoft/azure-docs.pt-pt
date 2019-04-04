---
title: Como atualizar um serviço em nuvem | Documentos da Microsoft
description: Saiba como atualizar serviços cloud no Azure. Saiba como uma atualização num serviço em nuvem prossegue para garantir a disponibilidade.
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: c6a8b5e6-5c99-454c-9911-5c7ae8d1af63
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: jeconnoc
ms.openlocfilehash: ff4dd571911719e4f2ec27952785432960a56d42
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917231"
---
# <a name="how-to-update-a-cloud-service"></a>Como atualizar um serviço em nuvem

Atualizar um serviço cloud, incluindo suas funções e o SO convidado, é um processo em três etapas. Em primeiro lugar, os binários e ficheiros de configuração para o novo serviço cloud ou a versão do SO tem de ser carregados. Em seguida, o Azure reserva de recursos de computação e rede para o serviço em nuvem com base nos requisitos da nova versão do serviço de nuvem. Por fim, o Azure efetua uma atualização sem interrupção para atualizar de forma incremental o inquilino para a nova versão ou o SO convidado, preservando a disponibilidade. Este artigo aborda os detalhes deste último passo – a atualização sem interrupção.

## <a name="update-an-azure-service"></a>Atualizar um serviço do Azure
Azure organiza as instâncias de função em agrupamentos lógicos chamados domínios de atualização (UD). Domínios de atualização (UD) são conjuntos lógicos de instâncias de função que são atualizadas como um grupo.  Atualizações do Azure, uma cloud service um UD de cada vez, que permite que instâncias em outros domínios de atualização para continuar a enviar tráfego.

O número predefinido de domínios de atualização é 5. Pode especificar um número diferente de domínios de atualização, incluindo o atributo de upgradeDomainCount no ficheiro de definição do serviço (. csdef). Para obter mais informações sobre o atributo upgradeDomainCount, consulte [esquema de WebRole](/previous-versions/azure/reference/gg557553(v=azure.100)) ou [esquema de WorkerRole](/previous-versions/azure/reference/gg557552(v=azure.100)).

Ao efetuar uma atualização direta de uma ou mais funções no seu serviço, o Azure atualiza conjuntos de instâncias de função, de acordo com o domínio de atualização ao qual pertencem. Atualizações do Azure, todas as instâncias num determinado domínio de atualização – como pará-los, atualizá-las, trazendo-os de fazer uma cópia online – passa, então, o domínio seguinte. Ao parar apenas as instâncias em execução no domínio de atualização atual, o Azure torna-se de que uma atualização ocorre com o menor impacto possível no serviço em execução. Para obter mais informações, consulte [como a atualização continua](#howanupgradeproceeds) mais adiante neste artigo.

> [!NOTE]
> Embora os termos **atualizar** e **atualizar** têm significado um pouco diferente no contexto do Azure, pode ser utilizados alternadamente para os processos e as descrições das funcionalidades neste documento.
>
>

O serviço tem de definir, pelo menos, duas instâncias de uma função para essa função ser atualizado no local sem período de indisponibilidade. Se o serviço consiste em apenas uma instância de uma função, seu serviço estará indisponível até que a atualização no local foi concluída.

Este tópico abrange as seguintes informações sobre atualizações do Azure:

* [Permitidas alterações no serviço durante uma atualização](#AllowedChanges)
* [Como uma atualização continua](#howanupgradeproceeds)
* [Reversão de uma atualização](#RollbackofanUpdate)
* [Iniciar várias operações de mutação numa implementação em curso](#multiplemutatingoperations)
* [Distribuição das funções em domínios de atualização](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Permitidas alterações no serviço durante uma atualização
A tabela seguinte mostra as alterações permitidas para um serviço durante uma atualização:

| Alterações de permissão para alojamento, serviços e funções | Atualização no local | Em etapas (troca de VIP) | Eliminar e voltar a implementar |
| --- | --- | --- | --- |
| Versão do sistema operativo |Sim |Sim |Sim |
| Nível de fidedignidade de .NET |Sim |Sim |Sim |
| Tamanho da máquina virtual<sup>1</sup> |Sim<sup>2</sup> |Sim |Sim |
| Definições de armazenamento local |Aumentar apenas<sup>2</sup> |Sim |Sim |
| Adicionar ou remover funções num serviço |Sim |Sim |Sim |
| Número de instâncias de uma função específica |Sim |Sim |Sim |
| Número ou tipo de pontos finais para um serviço |Sim<sup>2</sup> |Não |Sim |
| Nomes e os valores das definições de configuração |Sim |Sim |Sim |
| Os valores (mas não nomes) das definições de configuração |Sim |Sim |Sim |
| Adicionar novos certificados |Sim |Sim |Sim |
| Alterar certificados existentes |Sim |Sim |Sim |
| Implementar o novo código |Sim |Sim |Sim |

<sup>1</sup> tamanho limitada ao subconjunto de tamanhos disponíveis para o serviço cloud de alteração.

<sup>2</sup> requer o Azure SDK 1.5 ou versões posteriores.

> [!WARNING]
> Alterar o tamanho da máquina virtual irá destruir dados locais.
>
>

Os itens seguintes não são suportados durante uma atualização:

* Alterar o nome de uma função. Remover e, em seguida, adicione a função com o novo nome.
* Alterar-se de que a contagem de domínios de atualização.
* Diminuir o tamanho dos recursos locais.

Se estiver a efetuar outras atualizações à definição do seu serviço, por exemplo, a diminuir o tamanho do recurso local, tem de efetuar uma atualização de troca de VIP em vez disso. Para obter mais informações, consulte [trocar implementação](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Como uma atualização continua
Pode decidir se pretende atualizar todas as funções no seu serviço ou uma única função no serviço. Em ambos os casos, todas as instâncias de cada função que está a ser atualizado e pertencem ao primeiro domínio de atualização são paradas, atualizadas e novamente colocadas online. Quando estiverem novamente online, as instâncias do segundo domínio de atualização são paradas, atualizadas e novamente colocadas online. Um serviço em nuvem pode ter no máximo uma atualização do Active Directory ao mesmo tempo. A atualização é sempre executada com a versão mais recente do serviço cloud.

O diagrama seguinte ilustra como a atualização continua se estiver a atualizar todas as funções no serviço:

![Atualizar o serviço](media/cloud-services-update-azure-service/IC345879.png "atualizar serviço")

O diagrama seguinte ilustra como a atualização continua se estiver a atualizar apenas uma única função:

![Função de atualização](media/cloud-services-update-azure-service/IC345880.png "função de atualização")  

Durante uma atualização automática, o Azure Fabric Controller avalia periodicamente o estado de funcionamento do serviço cloud para determinar quando é seguro movimentar o UD seguinte. Essa avaliação de estado de funcionamento é executada numa base por função e considera apenas instâncias na versão mais recente (ou seja, instâncias de domínios de atualização que já tenham sido movimentados). Ele verifica se um número mínimo de instâncias de função, para cada função, de obteve um Estado terminal satisfatório.

### <a name="role-instance-start-timeout"></a>Tempo limite de início de instância de função
O controlador de malha será Aguarde 30 minutos para cada instância de função atingir um Estado iniciado. Se a duração de tempo limite decorrida, o controlador de malha continua a movimentar-se para a instância de função seguinte.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Impacto para os dados do disco durante o serviço em nuvem é atualizado

Ao atualizar um serviço de uma única instância para várias instâncias de seu serviço será desativado enquanto a atualização é executada devido aos serviços de atualizações do Azure de forma. A disponibilidade de serviço de guaranteeing de contrato de nível do serviço aplica-se apenas a serviços que são implementados com mais de uma instância. A lista seguinte descreve como os dados em cada unidade são afetados por cada cenário de atualização de serviço do Azure:

|Cenário|C Drive|Unidade D|E Drive|
|--------|-------|-------|-------|
|Reinício da VM|Preservados|Preservados|Preservados|
|Reinício de portal|Preservados|Preservados|Destruída|
|Recriação de imagem de portal|Preservados|Destruída|Destruída|
|Atualização no local|Preservados|Preservados|Destruída|
|Migração de nó|Destruída|Destruída|Destruída|

Tenha em atenção que, na lista acima, a unidade e representa a unidade de raiz da função e não deve ser codificada. Em alternativa, utilize o **% RoleRoot %** variável de ambiente para representar a unidade.

Para minimizar o período de indisponibilidade ao atualizar um serviço de instância única, implemente um novo serviço de várias instâncias para o servidor de preparação e efetuar uma alternância de VIP.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Reversão de uma atualização
O Azure fornece flexibilidade na gestão de serviços durante uma atualização, permitindo-lhe iniciar operações adicionais num serviço, depois do pedido de atualização inicial é aceite pelo controlador de malha do Azure. Uma reversão só pode ser executada quando uma atualização (alteração de configuração) ou a atualização está no **em curso** estado na implementação. Uma atualização ou a atualização é considerada em curso, desde que existe pelo menos uma instância do serviço que ainda não foi atualizado para a nova versão. Para testar se é permitida uma reversão, verifique o valor do sinalizador RollbackAllowed, devolvido pelo [implementação Obtenha](/previous-versions/azure/reference/ee460804(v=azure.100)) e [obter propriedades do serviço Cloud](/previous-versions/azure/reference/ee460806(v=azure.100)) operações, é definido como true.

> [!NOTE]
> Apenas faz sentido chamar reversão num **in-loco** de atualização porque as atualizações de troca de VIP envolvem a substituição de uma instância em execução toda do seu serviço com o outro.
>
>

Reversão de uma atualização em curso tem os seguintes efeitos sobre a implementação:

* Quaisquer instâncias de função que ainda não tinham sido atualizadas ou atualizadas para a nova versão não são atualizadas ou atualizadas, porque essas instâncias já estão a executar a versão de destino do serviço.
* Quaisquer instâncias de função que já tinham sido atualizadas ou atualizadas para a nova versão do pacote de serviço (\*. cspkg) ou a configuração do serviço de ficheiro (\*. cscfg) ficheiro (ou ambos os ficheiros) são revertidos para a versão de pré-atualização desses arquivos.

Isto é funcionalmente fornecido pelas seguintes funcionalidades:

* O [reversão de atualização ou atualizar](/previous-versions/azure/reference/hh403977(v=azure.100)) operação, o que pode ser chamada numa atualização de configuração (acionada chamando [alterar configuração da implementação](/previous-versions/azure/reference/ee460809(v=azure.100))) ou uma atualização (acionada chamando [ Atualizar implementação](/previous-versions/azure/reference/ee460793(v=azure.100))), desde que existe pelo menos uma instância no serviço que ainda não foi atualizado para a nova versão.
* O elemento de bloqueado e o elemento de RollbackAllowed, que são devolvidas como parte do corpo de resposta a [implementação Obtenha](/previous-versions/azure/reference/ee460804(v=azure.100)) e [obter propriedades do serviço Cloud](/previous-versions/azure/reference/ee460806(v=azure.100)) operações:

  1. O elemento de bloqueado permite-lhe detectar quando uma operação de mutação pode ser invocada numa determinada implantação.
  2. O elemento de RollbackAllowed permite-lhe detetar quando o [reversão atualização ou atualizar](/previous-versions/azure/reference/hh403977(v=azure.100)) operação pode ser chamada numa determinada implantação.

  Para efetuar uma reversão, não é necessário que verificar o bloqueado e os elementos de RollbackAllowed. Ele é suficiente para confirmar que RollbackAllowed está definido como true. Estes elementos são obtidos apenas se esses métodos são invocados utilizando o cabeçalho de pedido definido como "x-ms-version: 2011-10-01 "ou uma versão posterior. Para obter mais informações sobre cabeçalhos de controle de versão, consulte [controle de versão do serviço de gestão](/previous-versions/azure/gg592580(v=azure.100)).

Existem algumas situações em que uma reversão de uma atualização ou não é suportada a atualização, estes são os seguintes:

* Redução nos recursos locais - se a atualização aumenta os recursos locais para uma função a plataforma do Azure não permite a reversão.
* Limitações de quota - se a atualização foi um reduzir verticalmente a operação poderá já não tem quota de computação suficiente para concluir a operação de reversão. Cada subscrição do Azure tem uma quota associada a ele, que especifica o número máximo de núcleos que podem ser consumidos por todos os serviços alojados que pertencem a essa subscrição. Se efetuar uma reversão de uma determinada atualização colocaria a sua subscrição através da quota, em seguida, que não será ativada uma reversão.
* Condição de corrida - se a atualização inicial foi concluída, não é possível uma reversão.

Um exemplo de quando a reversão de uma atualização pode ser útil é se estiver a utilizar o [atualizar implementação](/previous-versions/azure/reference/ee460793(v=azure.100)) operação no modo manual para controlar a velocidade a que uma importante atualização in-loco para o seu Azure alojado o serviço é implementada.

Durante a implementação da atualização de chamar [atualizar implementação](/previous-versions/azure/reference/ee460793(v=azure.100)) no modo manual e começar a percorrer os domínios de atualização. Se em algum momento, para monitorizar a atualização, observar algumas instâncias de função nos domínios de atualização do primeiro que examine tem deixar de responder, pode chamar o [reversão atualização ou atualizar](/previous-versions/azure/reference/hh403977(v=azure.100)) operação na implementação, que irá deixar inalterados as instâncias que ainda não tinham sido atualizadas e as instâncias de reversão que atualizaram para o anterior pacote de serviço e a configuração.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Iniciar várias operações de mutação numa implementação em curso
Em alguns casos poderá querer iniciar várias operações simultâneas de mutação numa implementação em curso. Por exemplo, pode efetuar uma atualização de serviço e, embora essa atualização será implementada em seu serviço, o que pretende fazer alguma alteração, por exemplo, para implementar novamente a atualização, aplicar uma atualização de diferente ou até mesmo eliminar a implementação. Um caso em que isso poderá ser necessário é se uma atualização de serviço contém um código que faz com que uma instância de função atualizada repetidamente falhe. Neste caso, o controlador de malha do Azure não será capaz de progresso na aplicação de que uma vez que um número insuficiente de instâncias no domínio atualizado está em bom estado de atualização. Este estado é referido como um *preso implementação*. Pode unstick a implantação por reverter a atualização ou aplicar uma nova atualização ao longo da parte superior de falha de um.

Depois do pedido inicial para o serviço de atualização foi recebido pelo controlador de malha do Azure, pode começar a mutação as operações subsequentes. Ou seja, não é necessário aguardar que a operação inicial seja concluída antes de começar a outra operação de mutação.

Iniciar uma segunda operação de atualização, embora a primeira atualização está em curso irá efetuar semelhante para a operação de reversão. Se a segunda atualização estiver em modo automático, o primeiro domínio de atualização será atualizado imediatamente, possivelmente levando a instâncias de vários domínios de atualização estar offline no mesmo ponto no tempo.

As operações de mutação são os seguintes: [Alterar a configuração de implementação](/previous-versions/azure/reference/ee460809(v=azure.100)), [atualizar implementação](/previous-versions/azure/reference/ee460793(v=azure.100)), [Atualizar estado da implementação](/previous-versions/azure/reference/ee460808(v=azure.100)), [eliminar implementação](/previous-versions/azure/reference/ee460815(v=azure.100)), e [reversão A atualização](/previous-versions/azure/reference/hh403977(v=azure.100)).

Duas operações, [implementação Obtenha](/previous-versions/azure/reference/ee460804(v=azure.100)) e [obter propriedades do serviço Cloud](/previous-versions/azure/reference/ee460806(v=azure.100)), retornar o sinalizador de bloqueado que pode ser examinado para determinar se uma operação de mutação pode ser invocada numa determinada implantação.

Para chamar a versão destes métodos, que retorna o sinalizador de bloqueado, tem de definir o cabeçalho do pedido como "x-ms-version: 2011-10-01 "ou posterior. Para obter mais informações sobre cabeçalhos de controle de versão, consulte [controle de versão do serviço de gestão](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribuição das funções em domínios de atualização
Azure distribui instâncias de uma função uniformemente por um número definido de domínios de atualização, que pode ser configurado como parte do ficheiro de definição (. csdef) do serviço. O número máximo de domínios de atualização é 20 e a predefinição é 5. Para obter mais informações sobre como modificar o ficheiro de definição de serviço, consulte [esquema de definição de serviço do Azure (. csdef ficheiro)](cloud-services-model-and-package.md#csdef).

Por exemplo, se a sua função tiver dez instâncias, por predefinição cada domínio de atualização contém duas instâncias. Se a sua função tem 14 instâncias, em seguida, quatro dos domínios de atualização contêm três instâncias e um domínio quinto contém dois.

Domínios de atualização são identificados com um índice baseado em zero: o primeiro domínio de atualização tem um ID de 0 e o segundo domínio de atualização tem um ID de 1 e assim por diante.

O diagrama seguinte ilustra como um serviço que contém duas funções são distribuídos quando o serviço define dois domínios de atualização. O serviço está em execução oito instâncias da função da web e as nove instâncias de função de trabalho.

![Distribuição de domínios de atualização](media/cloud-services-update-azure-service/IC345533.png "distribuição de domínios de atualização")

> [!NOTE]
> Tenha em atenção que o Azure controla como as instâncias são alocadas em domínios de atualização. Não é possível especificar quais as instâncias são alocadas para o qual o domínio.
>
>

## <a name="next-steps"></a>Passos Seguintes
[Como gerir serviços Cloud](cloud-services-how-to-manage-portal.md)  
[Como Monitorizar os Serviços Cloud](cloud-services-how-to-monitor.md)  
[Como configurar os serviços Cloud](cloud-services-how-to-configure-portal.md)  
