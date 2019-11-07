---
title: Alta disponibilidade e recuperação de desastre do Hub IoT do Azure | Microsoft Docs
description: Descreve os recursos do Hub IoT e do Azure que ajudam a criar soluções de IoT do Azure altamente disponíveis com recursos de recuperação de desastre.
author: rkmanda
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: philmea
ms.openlocfilehash: 533a199f75baa5a27ed06698f22d4d046be45507
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607878"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Alta disponibilidade e recuperação de desastre do Hub IoT

Como uma primeira etapa na implementação de uma solução de IoT resiliente, arquitetos, desenvolvedores e proprietários de negócios devem definir as metas de tempo de atividade para as soluções que estão compilando. Essas metas podem ser definidas principalmente com base em objetivos de negócios específicos para cada cenário. Neste contexto, o artigo [orientação técnica da continuidade de negócios do Azure](https://docs.microsoft.com/azure/architecture/resiliency/) descreve uma estrutura geral para ajudá-lo a pensar na continuidade dos negócios e na recuperação de desastres. O artigo [recuperação de desastres e alta disponibilidade para aplicativos do Azure](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery) fornece diretrizes de arquitetura sobre estratégias para aplicativos do Azure para atingir alta disponibilidade (ha) e recuperação de desastres (Dr).

Este artigo aborda os recursos de HA e DR oferecidos especificamente pelo serviço do Hub IoT. As áreas amplas discutidas neste artigo são:

- HA de região interna
- DR de região cruzada
- Obtendo alta disponibilidade entre regiões

Dependendo das metas de tempo de atividade que você definir para suas soluções de IoT, você deve determinar quais das opções descritas abaixo melhor se adaptam aos seus objetivos de negócios. Incorporar qualquer uma dessas alternativas de HA/DR em sua solução de IoT requer uma avaliação cuidadosa das compensações entre:

- Nível de resiliência que você precisa 
- Complexidade de implementação e manutenção
- Impacto de COGS

## <a name="intra-region-ha"></a>HA de região interna

O serviço de Hub IoT fornece HA de região interna implementando redundâncias em quase todas as camadas do serviço. O [SLA publicado pelo serviço de Hub IOT](https://azure.microsoft.com/support/legal/sla/iot-hub) é obtido por meio da utilização dessas redundâncias. Nenhum trabalho adicional é exigido pelos desenvolvedores de uma solução de IoT para aproveitar esses recursos de alta disponibilidade. Embora o Hub IoT ofereça uma garantia de tempo de atividade razoavelmente alta, as falhas transitórias ainda podem ser esperadas como com qualquer plataforma de computação distribuída. Se você estiver apenas começando a migrar suas soluções para a nuvem de uma solução local, seu foco precisará mudar da otimização "tempo médio entre falhas" para "tempo médio de recuperação". Em outras palavras, as falhas transitórias devem ser consideradas normais enquanto operam com a nuvem na combinação. [As políticas de repetição](iot-hub-reliability-features-in-sdks.md) apropriadas devem ser internas aos componentes que interagem com um aplicativo de nuvem para lidar com falhas transitórias.

> [!NOTE]
> Alguns serviços do Azure também fornecem camadas adicionais de disponibilidade dentro de uma região, integrando-se ao [zonas de disponibilidade (AZs)](../availability-zones/az-overview.md). No momento, não há suporte para AZs no serviço do Hub IoT.

## <a name="cross-region-dr"></a>DR de região cruzada

Pode haver algumas situações raras em que um datacenter experimenta interrupções extensas devido a falhas de energia ou outras falhas envolvendo ativos físicos. Esses eventos são raros durante os quais o recurso de HA da região, descrito acima, nem sempre pode ajudar. O Hub IoT fornece várias soluções para a recuperação de tais interrupções estendidas. 

As opções de recuperação disponíveis para clientes nessa situação são o [failover iniciado pela Microsoft e o](#microsoft-initiated-failover) [failover manual](#manual-failover). A diferença fundamental entre os dois é que a Microsoft inicia o primeiro e o usuário inicia o último. Além disso, o failover manual fornece um RTO (objetivo de tempo de recuperação) menor em comparação com a opção de failover iniciada pela Microsoft. Os RTOs específicos oferecidos com cada opção são discutidos nas seções a seguir. Quando qualquer uma dessas opções para executar o failover de um hub IoT de sua região primária for exercitada, o Hub se tornará totalmente funcional na [região do emparelhada geográfica do Azure](../best-practices-availability-paired-regions.md)correspondente.

Ambas as opções de failover oferecem os seguintes RPOs (objetivos de ponto de recuperação):

| Data type | RPOs (objetivos de ponto de recuperação) |
| --- | --- |
| Registro de identidade |perda de dados de 0-5 minutos |
| Dados de entrelaçamento do dispositivo |perda de dados de 0-5 minutos |
| Mensagens da nuvem para o dispositivo<sup>1</sup> |perda de dados de 0-5 minutos |
| Pai<sup>1</sup> e trabalhos do dispositivo |perda de dados de 0-5 minutos |
| Mensagens do dispositivo para a cloud |Todas as mensagens não lidas são perdidas |
| Mensagens de monitoramento de operações |Todas as mensagens não lidas são perdidas |
| Mensagens de comentários da nuvem para o dispositivo |Todas as mensagens não lidas são perdidas |

<sup>1</sup> As mensagens da nuvem para o dispositivo e os trabalhos pai não são recuperados como parte do failover manual.

Depois que a operação de failover para o Hub IoT for concluída, todas as operações do dispositivo e dos aplicativos de back-end deverão continuar trabalhando sem a necessidade de uma intervenção manual. Isso significa que as mensagens do dispositivo para a nuvem devem continuar funcionando e todo o registro do dispositivo está intacto. Os eventos emitidos por meio da grade de eventos podem ser consumidos por meio da mesma assinatura (s) configurada anteriormente, desde que as assinaturas da grade de eventos continuem disponíveis.

> [!CAUTION]
> - O nome compatível com o Hub de eventos e o ponto de extremidade do ponto de extremidade de eventos internos do Hub IoT são alterados após o failover. Ao receber mensagens de telemetria do ponto de extremidade interno usando o cliente do hub de eventos ou o host do processador de eventos, você deve [usar a cadeia de conexão do Hub IOT](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) para estabelecer a conexão. Isso garante que seus aplicativos de back-end continuem a funcionar sem a necessidade de uma intervenção manual após o failover. Se você usar o nome compatível com o Hub de eventos e o ponto de extremidade em seu aplicativo de back-end diretamente, será necessário reconfigurar seu aplicativo [buscando o novo nome compatível com o Hub de eventos e o ponto de extremidade](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) após o failover para continuar as operações.
>
> - Ao rotear para o armazenamento, é recomendável inlistar o contêiner de armazenamento e iterar sobre eles, para garantir que todos os contêineres sejam lidos sem fazer nenhuma suposição de partição. O intervalo de partição pode ser alterado durante um failover iniciado pela Microsoft ou um failover manual. Para saber como enumerar a lista de BLOBs, consulte [Roteamento para o armazenamento do Azure](iot-hub-devguide-messages-d2c.md#azure-storage).

## <a name="microsoft-initiated-failover"></a>Failover iniciado pela Microsoft

O failover iniciado pela Microsoft é exercido pela Microsoft em raras situações para realizar o failover de todos os hubs IoT de uma região afetada para a região emparelhada geograficamente correspondente. Esse processo é uma opção padrão (não há como recusar os usuários) e não requer nenhuma intervenção do usuário. A Microsoft se reserva o direito de fazer uma determinação de quando essa opção será exercitada. Esse mecanismo não envolve um consentimento do usuário antes que o Hub do usuário seja reprovado. O failover iniciado pela Microsoft tem um RTO (objetivo de tempo de recuperação) de 2-26 horas. 

O RTO grande é porque a Microsoft deve executar a operação de failover em nome de todos os clientes afetados nessa região. Se você estiver executando uma solução de IoT menos crítica que possa sustentar um tempo de inatividade de aproximadamente um dia, é certo que você tenha uma dependência dessa opção para atender aos objetivos gerais de recuperação de desastres para sua solução de IoT. O tempo total para que as operações de tempo de execução se tornem totalmente operacionais quando esse processo é disparado, é descrito na seção "tempo para recuperar".

## <a name="manual-failover"></a>Failover manual

Se suas metas de tempo de atividade de negócios não forem satisfeitas pelo RTO fornecido pelo failover do Microsoft, considere usar o failover manual para disparar o processo de failover por conta própria. O RTO que usa essa opção pode estar em qualquer lugar entre 10 minutos e algumas horas. Atualmente, o RTO é uma função do número de dispositivos registrados na instância do Hub IoT que está sendo realizado o failover. Você pode esperar que o RTO de um Hub que hospeda aproximadamente 100.000 dispositivos esteja em aproximada de 15 minutos. O tempo total para que as operações de tempo de execução se tornem totalmente operacionais quando esse processo é disparado, é descrito na seção "tempo para recuperar".

A opção de failover manual está sempre disponível para uso, independentemente de a região primária estar com tempo de inatividade ou não. Portanto, essa opção pode ser usada potencialmente para executar failovers planejados. Um exemplo de uso de failovers planejados é executar análises de failover periódicas. No entanto, uma observação é que uma operação de failover planejada resulta em um tempo de inatividade para o Hub para o período definido pelo RTO para essa opção e também resulta em uma perda de dados, conforme definido pela tabela de RPO acima. Você poderia considerar a configuração de uma instância de Hub IoT de teste para exercitar a opção de failover planejado periodicamente para obter confiança em sua capacidade de colocar suas soluções de ponta a ponta em funcionamento quando ocorrer um desastre real.

> [!IMPORTANT]
> - As análises de teste não devem ser executadas em hubs IoT que estão sendo usados em seus ambientes de produção.
>
> - O failover manual não deve ser usado como um mecanismo para migrar permanentemente seu hub entre as regiões emparelhadas do Azure Geo. Isso causaria uma latência maior para as operações executadas no hub a partir de dispositivos hospedados na região primária antiga.

## <a name="failback"></a>Reativação pós-falha

O failback para a região primária antiga pode ser obtido disparando a ação de failover outra vez. Se a operação de failover original foi executada para recuperar-se de uma interrupção estendida na região primária original, recomendamos que o Hub faça failback para o local original quando esse local for recuperado da situação de interrupção.

> [!IMPORTANT]
> - Os usuários só têm permissão para executar 2 failover bem-sucedido e 2 operações de failback bem-sucedidas por dia.
>
> - Voltar para operações de failover/failback não são permitidos. Você deve aguardar uma hora entre essas operações.

## <a name="time-to-recover"></a>Tempo para recuperar

Embora o FQDN (e, portanto, a cadeia de conexão) da instância do Hub IoT permaneça o mesmo failover pós, o endereço IP subjacente é alterado. Portanto, o tempo geral para as operações de tempo de execução que estão sendo executadas em sua instância do Hub IoT ficar totalmente operacional após o disparo do processo de failover pode ser expresso usando a seguinte função.

Tempo de recuperação = RTO [10 min-2 horas para failover manual | 2-26 horas para failover iniciado pela Microsoft] + atraso de propagação de DNS + tempo gasto pelo aplicativo cliente para atualizar qualquer endereço IP do Hub IoT em cache.

> [!IMPORTANT]
> Os SDKs de IoT não armazenam em cache o endereço IP do Hub IoT. Recomendamos que o código do usuário que está fazendo a interface com os SDKs não deva armazenar em cache o endereço IP do Hub IoT.

## <a name="achieve-cross-region-ha"></a>Obter alta disponibilidade entre regiões

Se suas metas de tempo de atividade de negócios não forem satisfeitas pelo RTO que o failover iniciado pela Microsoft ou as opções de failover manual fornecem, você deverá considerar a implementação de um mecanismo de failover de região cruzada automática por dispositivo.
Um tratamento completo de topologias de implantação em soluções de IoT está fora do escopo deste artigo. O artigo aborda o modelo de implantação de *failover regional* para fins de alta disponibilidade e recuperação de desastres.

Em um modelo de failover regional, o back-end da solução é executado principalmente em um único local de datacenter. Um hub IoT secundário e back-end são implantados em outro local de datacenter. Se o Hub IoT na região primária sofrer uma interrupção ou a conectividade de rede do dispositivo para a região primária for interrompida, os dispositivos usarão um ponto de extremidade de serviço secundário. Você pode melhorar a disponibilidade da solução implementando um modelo de failover entre regiões em vez de permanecer dentro de uma única região. 

Em um alto nível, para implementar um modelo de failover regional com o Hub IoT, você precisa executar as seguintes etapas:

* **Uma lógica de roteamento de dispositivo e um hub IOT secundário**: se o serviço em sua região primária for interrompido, os dispositivos deverão começar a se conectar à sua região secundária. Considerando a natureza com reconhecimento de estado da maioria dos serviços envolvidos, é comum que os administradores de soluções disparem o processo de failover entre regiões. A melhor maneira de comunicar o novo ponto de extremidade aos dispositivos, mantendo o controle do processo, é fazer com que eles verifiquem regularmente um serviço do *concierge* para o ponto de extremidade ativo atual. O serviço do concierge pode ser um aplicativo Web que é replicado e mantido acessível usando técnicas de redirecionamento de DNS (por exemplo, usando o [Gerenciador de tráfego do Azure](../traffic-manager/traffic-manager-overview.md)).

   > [!NOTE]
   > O serviço Hub IoT não é um tipo de ponto de extremidade com suporte no Gerenciador de tráfego do Azure. A recomendação é integrar o serviço de concierge proposto ao Gerenciador de tráfego do Azure, fazendo com que ele implemente a API de investigação de integridade do ponto de extremidade.

* **Replicação de registro de identidade**: para ser utilizável, o Hub IOT secundário deve conter todas as identidades de dispositivo que podem se conectar à solução. A solução deve manter backups replicados geograficamente de identidades de dispositivo e carregá-las no Hub IoT secundário antes de alternar o ponto de extremidade ativo para os dispositivos. A funcionalidade de exportação de identidade do dispositivo do Hub IoT é útil neste contexto. Para obter mais informações, consulte [Guia do desenvolvedor do Hub IOT – registro de identidade](iot-hub-devguide-identity-registry.md).

* **Lógica de mesclagem**: quando a região primária fica disponível novamente, todos os Estados e dados que foram criados no site secundário devem ser migrados de volta para a região primária. Esse Estado e os dados estão principalmente relacionados a identidades de dispositivo e metadados de aplicativo, que devem ser mesclados com o Hub IoT primário e quaisquer outros armazenamentos específicos do aplicativo na região primária. 

Para simplificar essa etapa, você deve usar operações idempotentes. Operações idempotentes minimizam os efeitos colaterais da distribuição eventual consistente de eventos e de duplicatas ou entregas de eventos fora de ordem. Além disso, a lógica do aplicativo deve ser projetada para tolerar possíveis inconsistências ou um estado ligeiramente desatualizado. Essa situação pode ocorrer devido ao tempo adicional que leva para o sistema reparar com base nos objetivos de ponto de recuperação (RPO).

## <a name="choose-the-right-hadr-option"></a>Escolha a opção correta de HA/DR

Aqui está um resumo das opções de HA/DR apresentadas neste artigo que podem ser usadas como um quadro de referência para escolher a opção correta que funciona para sua solução.

| Opção HA/DR | RTO | RPO | Requer intervenção manual? | Complexidade da implementação | Impacto adicional no custo|
| --- | --- | --- | --- | --- | --- |
| Failover iniciado pela Microsoft |2-26 horas|Consulte a tabela de RPO acima|Não|Nenhum|Nenhum|
| Failover manual |10 min-2 horas|Consulte a tabela de RPO acima|Sim|Muito baixo. Você só precisa disparar essa operação no Portal.|Nenhum|
| Alta disponibilidade entre regiões |< 1 min|Depende da frequência de replicação da sua solução de HA personalizada|Não|Elevado|> 1x o custo de 1 Hub IoT|

## <a name="next-steps"></a>Passos seguintes

* [O que é o Hub IoT do Azure?](about-iot-hub.md)
* [Introdução aos hubs IoT (início rápido)](quickstart-send-telemetry-dotnet.md)
* [Tutorial: executar o failover manual para um hub IoT](tutorial-manual-failover.md)