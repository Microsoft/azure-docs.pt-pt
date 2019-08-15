---
title: Como atualizar um serviço de nuvem | Microsoft Docs
description: Saiba como atualizar os serviços de nuvem no Azure. Saiba como uma atualização em um serviço de nuvem prossegue para garantir a disponibilidade.
services: cloud-services
author: georgewallace
ms.service: cloud-services
ms.topic: article
ms.date: 04/19/2017
ms.author: gwallace
ms.openlocfilehash: ae9d124391a1b17187ca98964874f681352498da
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945339"
---
# <a name="how-to-update-a-cloud-service"></a>Como atualizar um serviço de nuvem

A atualização de um serviço de nuvem, incluindo suas funções e o SO convidado, é um processo de três etapas. Primeiro, os arquivos binários e de configuração para o novo serviço de nuvem ou versão do sistema operacional devem ser carregados. Em seguida, o Azure reserva recursos de computação e de rede para o serviço de nuvem com base nos requisitos da nova versão do serviço de nuvem. Por fim, o Azure realiza uma atualização sem interrupção para atualizar incrementalmente o locatário para a nova versão ou o sistema operacional convidado, preservando, ao mesmo tempo, a disponibilidade. Este artigo aborda os detalhes dessa última etapa – a atualização sem interrupção.

## <a name="update-an-azure-service"></a>Atualizar um serviço do Azure
O Azure organiza suas instâncias de função em agrupamentos lógicos chamados de UD (domínios de atualização). Domínios de atualização (UD) são conjuntos lógicos de instâncias de função que são atualizados como um grupo.  O Azure atualiza um serviço de nuvem um UD de cada vez, o que permite que instâncias em outros UDs continuem a servir tráfego.

O número padrão de domínios de atualização é 5. Você pode especificar um número diferente de domínios de atualização, incluindo o atributo upgradeDomainCount no arquivo de definição do serviço (. csdef). Para obter mais informações sobre o atributo upgradeDomainCount, consulte [esquema de definição de serviços de nuvem do Azure (arquivo. csdef)](https://docs.microsoft.com/azure/cloud-services/schema-csdef-file).

Quando você executa uma atualização in-loco de uma ou mais funções em seu serviço, o Azure atualiza conjuntos de instâncias de função de acordo com o domínio de atualização ao qual eles pertencem. O Azure atualiza todas as instâncias em um determinado domínio de atualização – interrompendo-as, atualizando-as, colocando-as online novamente e, em seguida, passa para o próximo domínio. Ao interromper somente as instâncias em execução no domínio de atualização atual, o Azure garante que uma atualização ocorra com o menor impacto possível no serviço em execução. Para obter mais informações, consulte [como a atualização continuará](#howanupgradeproceeds) mais adiante neste artigo.

> [!NOTE]
> Embora os termos **atualização** e **atualização** tenham significado ligeiramente diferente no contexto do Azure, eles podem ser usados de maneira intercambiável para os processos e descrições dos recursos deste documento.
>
>

Seu serviço deve definir pelo menos duas instâncias de uma função para que essa função seja atualizada in-loco sem tempo de inatividade. Se o serviço consistir em apenas uma instância de uma função, seu serviço ficará indisponível até que a atualização in-loco seja concluída.

Este tópico aborda as seguintes informações sobre as atualizações do Azure:

* [Alterações de serviço permitidas durante uma atualização](#AllowedChanges)
* [Como uma atualização prossegue](#howanupgradeproceeds)
* [Reversão de uma atualização](#RollbackofanUpdate)
* [Iniciando várias operações de mutação em uma implantação em andamento](#multiplemutatingoperations)
* [Distribuição de funções entre domínios de atualização](#distributiondfroles)

<a name="AllowedChanges"></a>

## <a name="allowed-service-changes-during-an-update"></a>Alterações de serviço permitidas durante uma atualização
A tabela a seguir mostra as alterações permitidas em um serviço durante uma atualização:

| Alterações permitidas para hospedar, serviços e funções | Atualização in-loco | Preparado (permuta de VIP) | Excluir e reimplantar |
| --- | --- | --- | --- |
| Versão do sistema operativo |Sim |Sim |Sim |
| Nível de confiança do .NET |Sim |Sim |Sim |
| Tamanho da máquina virtual<sup>1</sup> |Sim<sup>2</sup> |Sim |Sim |
| Configurações de armazenamento local |Aumentar somente<sup>2</sup> |Sim |Sim |
| Adicionar ou remover funções em um serviço |Sim |Sim |Sim |
| Número de instâncias de uma função específica |Sim |Sim |Sim |
| Número ou tipo de pontos de extremidade para um serviço |Sim<sup>2</sup> |Não |Sim |
| Nomes e valores de parâmetros de configuração |Sim |Sim |Sim |
| Valores (mas não nomes) das definições de configuração |Sim |Sim |Sim |
| Adicionar novos certificados |Sim |Sim |Sim |
| Alterar certificados existentes |Sim |Sim |Sim |
| Implantar novo código |Sim |Sim |Sim |

<sup>1</sup> alteração de tamanho limitada ao subconjunto de tamanhos disponíveis para o serviço de nuvem.

<sup>2</sup> requer o SDK do Azure 1,5 ou versões posteriores.

> [!WARNING]
> A alteração do tamanho da máquina virtual destruirá os dados locais.
>
>

Os itens a seguir não têm suporte durante uma atualização:

* Alterando o nome de uma função. Remova e adicione a função com o novo nome.
* Alteração da contagem de domínios de atualização.
* Diminuindo o tamanho dos recursos locais.

Se você estiver fazendo outras atualizações para a definição do serviço, como diminuir o tamanho do recurso local, você deverá executar uma atualização de permuta de VIP em vez disso. Para obter mais informações, consulte a [implantação de permuta](/previous-versions/azure/reference/ee460814(v=azure.100)).

<a name="howanupgradeproceeds"></a>

## <a name="how-an-upgrade-proceeds"></a>Como uma atualização prossegue
Você pode decidir se deseja atualizar todas as funções em seu serviço ou uma única função no serviço. Em ambos os casos, todas as instâncias de cada função que está sendo atualizada e que pertencem ao primeiro domínio de atualização são interrompidas, atualizadas e colocadas online novamente. Quando eles estiverem online novamente, as instâncias no segundo domínio de atualização serão interrompidas, atualizadas e colocadas online novamente. Um serviço de nuvem pode ter no máximo uma atualização ativa por vez. A atualização é sempre executada em relação à versão mais recente do serviço de nuvem.

O diagrama a seguir ilustra como a atualização procede se você estiver atualizando todas as funções no serviço:

![Atualizar serviço](media/cloud-services-update-azure-service/IC345879.png "Atualizar serviço")

Este próximo diagrama ilustra como a atualização procede se você estiver atualizando apenas uma única função:

![Função de atualização](media/cloud-services-update-azure-service/IC345880.png "Função de atualização")  

Durante uma atualização automática, o controlador de malha do Azure avalia periodicamente a integridade do serviço de nuvem para determinar quando é seguro percorrer o próximo UD. Essa avaliação de integridade é executada por função e considera apenas instâncias na versão mais recente (ou seja, instâncias de UDs que já foram percorridos). Ele verifica se um número mínimo de instâncias de função, para cada função, obteve um estado de terminal satisfatório.

### <a name="role-instance-start-timeout"></a>Tempo limite de início da instância de função
O controlador de malha aguardará 30 minutos para que cada instância de função atinja um estado iniciado. Se a duração do tempo limite expirar, o controlador de malha continuará a passar para a próxima instância de função.

### <a name="impact-to-drive-data-during-cloud-service-upgrades"></a>Impacto na unidade de dados durante atualizações do serviço de nuvem

Ao atualizar um serviço de uma única instância para várias instâncias, seu serviço será desativado enquanto a atualização for executada devido à maneira como o Azure atualiza os serviços. O contrato de nível de serviço que garante a disponibilidade do serviço só se aplica a serviços que são implantados com mais de uma instância. A lista a seguir descreve como os dados em cada unidade são afetados por cada cenário de atualização de serviço do Azure:

|Cenário|Unidade C|Unidade D|Unidade E|
|--------|-------|-------|-------|
|Reinicialização da VM|Preservação|Preservação|Preservação|
|Reinicialização do portal|Preservação|Preservação|Destruído|
|Reimagem do portal|Preservação|Destruído|Destruído|
|Atualização in-loco|Preservação|Preservação|Destruído|
|Migração de nó|Destruído|Destruído|Destruído|

Observe que, na lista acima, a unidade E: representa a unidade raiz da função e não deve ser embutida em código. Em vez disso, use a variável de ambiente **% RoleRoot%** para representar a unidade.

Para minimizar o tempo de inatividade ao atualizar um serviço de instância única, implante um novo serviço de várias instâncias no servidor de preparo e execute uma permuta de VIP.

<a name="RollbackofanUpdate"></a>

## <a name="rollback-of-an-update"></a>Reversão de uma atualização
O Azure fornece flexibilidade no gerenciamento de serviços durante uma atualização, permitindo que você inicie operações adicionais em um serviço, após a solicitação de atualização inicial ser aceita pelo controlador de malha do Azure. Uma reversão só pode ser executada quando uma atualização (alteração de configuração) ou atualização está no estado **em andamento** na implantação. Uma atualização ou atualização é considerada em andamento, desde que haja pelo menos uma instância do serviço que ainda não tenha sido atualizada para a nova versão. Para testar se uma reversão é permitida, verifique o valor do sinalizador RollbackAllowed, retornado pelas operações [obter implantação](/previous-versions/azure/reference/ee460804(v=azure.100)) e [obter propriedades do serviço de nuvem](/previous-versions/azure/reference/ee460806(v=azure.100)) , está definido como true.

> [!NOTE]
> Apenas faz sentido chamar Rollback em uma atualização in-loco ou atualizar, pois as atualizações **de** permuta de VIP envolvem a substituição de uma instância inteira em execução do seu serviço por outra.
>
>

A reversão de uma atualização em andamento tem os seguintes efeitos na implantação:

* Todas as instâncias de função que ainda não foram atualizadas ou atualizadas para a nova versão não são atualizadas ou atualizadas, pois essas instâncias já estão executando a versão de destino do serviço.
* Todas as instâncias de função que já foram atualizadas ou atualizadas para a nova versão do arquivo\*de pacote de serviço (. cspkg) ou\*do arquivo de configuração de serviço (. cscfg) (ou ambos os arquivos) são revertidas para a versão de pré-atualização desses arquivos.

Essa funcionalidade é fornecida pelos seguintes recursos:

* A operação de reversão de [atualização ou](/previous-versions/azure/reference/hh403977(v=azure.100)) atualização, que pode ser chamada em uma atualização de configuração (disparada chamando a [configuração de implantação de alteração](/previous-versions/azure/reference/ee460809(v=azure.100))) ou uma atualização (disparada chamando a implantação de [atualização](/previous-versions/azure/reference/ee460793(v=azure.100))) contanto que haja pelo menos uma instância no serviço que ainda não foi atualizada para a nova versão.
* O elemento bloqueado e o elemento RollbackAllowed, que são retornados como parte do corpo da resposta das operações [obter implantação](/previous-versions/azure/reference/ee460804(v=azure.100)) e [obter propriedades do serviço de nuvem](/previous-versions/azure/reference/ee460806(v=azure.100)) :

  1. O elemento bloqueado permite detectar quando uma operação de mutação pode ser chamada em uma determinada implantação.
  2. O elemento RollbackAllowed permite detectar quando a operação de [atualização de reversão ou](/previous-versions/azure/reference/hh403977(v=azure.100)) atualização pode ser chamada em uma determinada implantação.

  Para executar uma reversão, você não precisa verificar os elementos Locked e RollbackAllowed. É suficiente confirmar se RollbackAllowed está definido como true. Esses elementos só serão retornados se esses métodos forem invocados usando o cabeçalho de solicitação definido como "x-MS-Version: 2011-10-01 "ou uma versão posterior. Para obter mais informações sobre cabeçalhos de versão, consulte [controle de versão de gerenciamento de serviços](/previous-versions/azure/gg592580(v=azure.100)).

Há algumas situações em que não há suporte para a reversão de uma atualização ou atualização, estas são as seguintes:

* Redução nos recursos locais – se a atualização aumentar os recursos locais para uma função, a plataforma do Azure não permitirá a reversão.
* Limitações de cota – se a atualização foi uma operação de redução vertical, talvez você não tenha mais uma cota de computação suficiente para concluir a operação de reversão. Cada assinatura do Azure tem uma cota associada que especifica o número máximo de núcleos que podem ser consumidos por todos os serviços hospedados que pertencem a essa assinatura. Se a execução de uma reversão de uma determinada atualização colocar sua assinatura sobre a cota, a reversão não será habilitada.
* Condição de corrida – se a atualização inicial for concluída, uma reversão não será possível.

Um exemplo de quando a reversão de uma atualização pode ser útil é se você estiver usando a operação de [implantação de atualização](/previous-versions/azure/reference/ee460793(v=azure.100)) no modo manual para controlar a taxa na qual uma atualização in-loco principal para o serviço hospedado do Azure é distribuída.

Durante a distribuição da atualização, você chama a [implantação de atualização](/previous-versions/azure/reference/ee460793(v=azure.100)) no modo manual e começa a percorrer os domínios de atualização. Em algum momento, conforme você monitora a atualização, você observa que algumas instâncias de função nos primeiros domínios de atualização que você examina não respondem, você pode chamar a operação de reversão de [atualização ou atualização](/previous-versions/azure/reference/hh403977(v=azure.100)) na implantação, o que deixará de ser inalterado o instâncias que ainda não foram atualizadas e reversão instâncias que foram atualizadas para o pacote de serviço e a configuração anteriores.

<a name="multiplemutatingoperations"></a>

## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Iniciando várias operações de mutação em uma implantação em andamento
Em alguns casos, talvez você queira iniciar várias operações de mutação simultâneas em uma implantação em andamento. Por exemplo, você pode executar uma atualização de serviço e, enquanto essa atualização está sendo distribuída em todo o serviço, você deseja fazer alguma alteração, por exemplo, para reverter a atualização, aplicar uma atualização diferente ou até mesmo excluir a implantação. Um caso em que isso pode ser necessário é se uma atualização de serviço contiver código de bugs, o que faz com que uma instância de função atualizada falhe repetidamente. Nesse caso, o controlador de malha do Azure não poderá fazer o progresso na aplicação dessa atualização, pois um número insuficiente de instâncias no domínio atualizado será íntegro. Esse estado é conhecido como uma *implantação*paralisada. Você pode desfazer a implantação revertendo a atualização ou aplicando uma atualização atualizada sobre a falha de uma.

Depois que a solicitação inicial para atualizar ou atualizar o serviço tiver sido recebida pelo controlador de malha do Azure, você poderá iniciar as operações de mutação subsequentes. Ou seja, você não precisa aguardar a conclusão da operação inicial antes de poder iniciar outra operação de mutação.

Iniciar uma segunda operação de atualização enquanto a primeira atualização está em andamento será executado de forma semelhante à operação de reversão. Se a segunda atualização estiver no modo automático, o primeiro domínio de atualização será atualizado imediatamente, possivelmente levando a instâncias de vários domínios de atualização estarem offline no mesmo momento.

As operações de mutação são as seguintes: [Alterar configuração de implantação](/previous-versions/azure/reference/ee460809(v=azure.100)), [implantação](/previous-versions/azure/reference/ee460793(v=azure.100))de atualização, status de [implantação de atualização](/previous-versions/azure/reference/ee460808(v=azure.100)), [excluir implantação](/previous-versions/azure/reference/ee460815(v=azure.100))e [reverter atualização ou atualização](/previous-versions/azure/reference/hh403977(v=azure.100)).

Duas operações, [obter implantação](/previous-versions/azure/reference/ee460804(v=azure.100)) e [obter propriedades do serviço de nuvem](/previous-versions/azure/reference/ee460806(v=azure.100)), retornam o sinalizador bloqueado que pode ser examinado para determinar se uma operação de mutação pode ser chamada em uma determinada implantação.

Para chamar a versão desses métodos que retorna o sinalizador bloqueado, você deve definir o cabeçalho de solicitação como "x-MS-Version: 2011-10-01 "ou posterior. Para obter mais informações sobre cabeçalhos de versão, consulte [controle de versão de gerenciamento de serviços](/previous-versions/azure/gg592580(v=azure.100)).

<a name="distributiondfroles"></a>

## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribuição de funções entre domínios de atualização
O Azure distribui instâncias de uma função uniformemente em um número definido de domínios de atualização, que podem ser configurados como parte do arquivo de definição de serviço (. csdef). O número máximo de domínios de atualização é 20 e o padrão é 5. Para obter mais informações sobre como modificar o arquivo de definição de serviço, consulte [esquema de definição de serviço do Azure (arquivo. csdef)](cloud-services-model-and-package.md#csdef).

Por exemplo, se sua função tiver dez instâncias, por padrão, cada domínio de atualização conterá duas instâncias. Se sua função tiver 14 instâncias, quatro dos domínios de atualização conterão três instâncias e um quinto domínio conterá dois.

Os domínios de atualização são identificados com um índice de base zero: o primeiro domínio de atualização tem uma ID de 0, e o segundo domínio de atualização tem uma ID de 1 e assim por diante.

O diagrama a seguir ilustra como um serviço do que contém duas funções são distribuídas quando o serviço define dois domínios de atualização. O serviço está executando oito instâncias da função Web e nove instâncias da função de trabalho.

![Distribuição de domínios de atualização](media/cloud-services-update-azure-service/IC345533.png "Distribuição de domínios de atualização")

> [!NOTE]
> Observe que o Azure controla como as instâncias são alocadas entre domínios de atualização. Não é possível especificar quais instâncias são alocadas a qual domínio.
>
>

## <a name="next-steps"></a>Passos Seguintes
[Como gerir Serviços Cloud](cloud-services-how-to-manage-portal.md)  
[Como monitorar serviços de nuvem](cloud-services-how-to-monitor.md)  
[Como configurar um Serviços Cloud](cloud-services-how-to-configure-portal.md)  
