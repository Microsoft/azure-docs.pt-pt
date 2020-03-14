---
title: Azure IoT Hub alta disponibilidade e recuperação de desastres Microsoft Docs
description: Descreve as funcionalidades Do Hub Azure e IoT que o ajudam a construir soluções Azure IoT altamente disponíveis com capacidades de recuperação de desastres.
author: rkmanda
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: philmea
ms.openlocfilehash: 173be8207df2f0128dfc9ae3c36aa3c3dc392bee
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271073"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub alta disponibilidade e recuperação de desastres

Como um primeiro passo para a implementação de uma solução IoT resiliente, arquitetos, desenvolvedores e empresários devem definir os objetivos de uptime para as soluções que estão construindo. Estes objetivos podem ser definidos principalmente com base em objetivos de negócio específicos para cada cenário. Neste contexto, o artigo [Azure Business Continuity Technical Guidance](https://docs.microsoft.com/azure/architecture/resiliency/) descreve um quadro geral para ajudá-lo a pensar sobre a continuidade do negócio e a recuperação de desastres. O documento de recuperação de [desastres e elevada disponibilidade para aplicações azure](https://docs.microsoft.com/azure/architecture/reliability/disaster-recovery) fornece orientação de arquitetura sobre estratégias para aplicações Azure para alcançar Alta Disponibilidade (HA) e Recuperação de Desastres (DR).

Este artigo discute as funcionalidades HA e DR oferecidas especificamente pelo serviço IoT Hub. As grandes áreas discutidas neste artigo são:

- Ha intrarregião
- Região transversal DR
- Alcançar a região transversal HA

Dependendo dos objetivos de uptime que define para as suas soluções IoT, deve determinar quais as opções descritas abaixo melhor se adequam aos seus objetivos de negócio. Incorporar qualquer uma destas alternativas HA/DR na sua solução IoT requer uma avaliação cuidadosa das compensações entre:

- Nível de resiliência que você precisa 
- Complexidade de implementação e manutenção
- Impacto do COGS

## <a name="intra-region-ha"></a>Ha intrarregião

O serviço IoT Hub fornece HA intrarregião implementando despedimentos em quase todas as camadas do serviço. O [SLA publicado pelo serviço IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub) é conseguido através da utilização destes despedimentos. Não são necessários trabalhos adicionais pelos desenvolvedores de uma solução IoT para tirar partido destas funcionalidades ha. Embora o IoT Hub ofereça uma garantia de uptime razoavelmente elevada, podem ainda ser esperadas falhas transitórias como em qualquer plataforma de computação distribuída. Se está apenas a começar a migrar as suas soluções para a nuvem a partir de uma solução no local, o seu foco precisa passar de otimizar "tempo médio entre falhas" para "tempo médio para recuperar". Por outras palavras, as falhas transitórias devem ser consideradas normais durante o funcionamento com a nuvem na mistura. Devem ser inseridas políticas de [retenção](iot-hub-reliability-features-in-sdks.md) adequadas aos componentes que interagem com uma aplicação na nuvem para fazer face a falhas transitórias.

> [!NOTE]
> Alguns serviços Azure também fornecem camadas adicionais de disponibilidade dentro de uma região, integrando-se com [Zonas de Disponibilidade (AZs)](../availability-zones/az-overview.md). Atualmente, os AZs não são apoiados pelo serviço IoT Hub.

## <a name="cross-region-dr"></a>Região transversal DR

Pode haver algumas situações raras quando um datacenter experimenta interrupções prolongadas devido a falhas de energia ou outras falhas envolvendo ativos físicos. Tais eventos são raros durante os quais a capacidade ha da região intra descrita acima pode nem sempre ajudar. O IoT Hub fornece múltiplas soluções para recuperar de tais interrupções prolongadas. 

As opções de recuperação disponíveis para os clientes em tal situação são [failover iniciado pela Microsoft](#microsoft-initiated-failover) e [falha manual](#manual-failover). A diferença fundamental entre os dois é que a Microsoft inicia o primeiro e o utilizador inicia este último. Além disso, a falha manual fornece um objetivo de tempo de recuperação mais baixo (RTO) em comparação com a opção de failover iniciada pela Microsoft. As RTOs específicas oferecidas com cada opção são discutidas nas secções abaixo. Quando qualquer uma destas opções para realizar uma falha de um hub IoT da sua região primária é exercida, o centro torna-se totalmente funcional na [região geo-emparelhada azure](../best-practices-availability-paired-regions.md)correspondente.

Ambas estas opções de failover oferecem os seguintes objetivos de ponto de recuperação (RPOs):

| Tipo de dados | Objetivos do ponto de recuperação (RPO) |
| --- | --- |
| Registo de identidade |Perda de dados de 0-5 minutos |
| Dados gémeos do dispositivo |Perda de dados de 0-5 minutos |
| Mensagens cloud-to-device<sup>1</sup> |Perda de dados de 0-5 minutos |
| Trabalhos dos pais<sup>1</sup> e do dispositivo |Perda de dados de 0-5 minutos |
| Mensagens do dispositivo para a cloud |Todas as mensagens não lidas estão perdidas |
| Mensagens de monitorização de operações |Todas as mensagens não lidas estão perdidas |
| Mensagens de feedback cloud-to-device |Todas as mensagens não lidas estão perdidas |

<sup>1</sup> As mensagens cloud-to-device e os trabalhos dos pais não são recuperados como parte da falha manual.

Uma vez concluída a operação de failover para o hub IoT, espera-se que todas as operações a partir do dispositivo e aplicações back-end continuem a funcionar sem necessitar de uma intervenção manual. Isto significa que as mensagens dispositivo-cloud devem continuar a funcionar e todo o registo do dispositivo está intacto. Os eventos emitidos via Event Grid podem ser consumidos através da mesma subscrição configurada anteriormente, desde que essas subscrições da Rede de Eventos continuem disponíveis.

> [!CAUTION]
> - O nome e ponto final compatíveis com o Event Hub do IoT Hub incorporado sem ponto final após a falha. Ao receber mensagens de telemetria do ponto final incorporado utilizando o cliente do hub de eventos ou o anfitrião do processador de eventos, deve utilizar a cadeia de ligação do [hub IoT](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) para estabelecer a ligação. Isto garante que as suas aplicações de back-end continuam a funcionar sem necessitar de falhas nos postos de intervenção manual. Se utilizar o nome e o ponto final compatíveis com o Event Hub na sua aplicação de back-end diretamente, terá de reconfigurar a sua [aplicação, obtendo o novo nome e ponto final compatíveis](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) com o Event Hub após o failover para continuar as operações.
>
> - Ao encaminhar para o armazenamento, recomendamos a listagem das bolhas ou ficheiros e, em seguida, iterando sobre eles, para garantir que todas as bolhas ou ficheiros são lidos sem fazer quaisquer pressupostos de partição. A gama de divisórias pode potencialmente mudar durante uma falha ou falha manual iniciada pela Microsoft. Pode utilizar a [Lista Blobs API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) para enumerar a lista de blobs ou [Lista ADLS Gen2 API](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/path/list) para a lista de ficheiros. 

## <a name="microsoft-initiated-failover"></a>Falha iniciada pela Microsoft

A falha iniciada pela Microsoft é exercida pela Microsoft em situações raras para falhar todos os hubs IoT de uma região afetada para a região geo-emparelhada correspondente. Este processo é uma opção predefinida (não há forma de os utilizadores optarem) e não necessita de qualquer intervenção do utilizador. A Microsoft reserva-se o direito de determinar quando esta opção será exercida. Este mecanismo não envolve o consentimento do utilizador antes de o hub do utilizador ser falhado. O failover iniciado pela Microsoft tem um objetivo de tempo de recuperação (RTO) de 2-26 horas. 

O grande RTO deve-se ao facto de a Microsoft ter de realizar a operação de failover em nome de todos os clientes afetados naquela região. Se está a executar uma solução IoT menos crítica que pode sustentar um tempo de paragem de aproximadamente um dia, não faz mal que tome uma dependência desta opção para satisfazer os objetivos globais de recuperação de desastres para a sua solução IoT. O tempo total para que as operações de tempo de funcionamento se tornem totalmente operacionais uma vez que este processo é desencadeado, é descrito na secção "Tempo de recuperação".

## <a name="manual-failover"></a>Falha manual

Se os objetivos de uptime do seu negócio não estiverem satisfeitos com o RTO que a Microsoft iniciou o failover, considere usar o failover manual para desencadear o processo de failover por si mesmo. O RTO que utiliza esta opção pode estar entre 10 minutos a algumas horas. O RTO é atualmente uma função do número de dispositivos registados contra a instância do hub IoT que está a ser reprovada. Pode esperar que o RTO para um hub que aloja cerca de 100.000 dispositivos esteja no estádio de 15 minutos. O tempo total para que as operações de tempo de funcionamento se tornem totalmente operacionais uma vez que este processo é desencadeado, é descrito na secção "Tempo de recuperação".

A opção de failover manual está sempre disponível para utilização, independentemente de a região primária estar ou não a passar por um tempo de inatividade. Portanto, esta opção poderia potencialmente ser usada para realizar falhas planeadas. Um exemplo de utilização de falhas planeadas é a realização de exercícios periódicos de failover. Uma palavra de precaução, porém, é que uma operação de failover planeada resulta num tempo de paragem para o hub para o período definido pela RTO para esta opção, e também resulta numa perda de dados definida pela tabela RPO acima. Você poderia considerar a criação de um teste ioT exemplo para exercitar periodicamente a opção de failover planejada para ganhar confiança na sua capacidade de obter as suas soluções de ponta a ponta quando um desastre real acontece.

> [!IMPORTANT]
> - As brocas de teste não devem ser efetuadas em centros IoT que estão a ser utilizados nos seus ambientes de produção.
>
> - A falha manual não deve ser utilizada como um mecanismo para migrar permanentemente o seu hub entre as regiões de pares geo-azure. Fazê-lo provocaria um aumento da latência para as operações que estão a ser realizadas contra o centro a partir de dispositivos alojados na antiga região primária.

## <a name="failback"></a>Reativação pós-falha

O regresso à antiga região primária pode ser alcançado desencadeando uma outra vez a ação de failover. Se a operação original de failover foi realizada para recuperar de uma paragem prolongada na região primária original, recomendamos que o centro seja falhado de volta ao local original uma vez que esse local tenha recuperado da situação de paralisação.

> [!IMPORTANT]
> - Os utilizadores só estão autorizados a realizar 2 falhas bem sucedidas e 2 operações de failback bem sucedidas por dia.
>
> - Não são permitidas operações de retrocesso/failback. Deve esperar 1 hora entre estas operações.

## <a name="time-to-recover"></a>Hora de recuperar

Enquanto o FQDN (e, portanto, a cadeia de ligação) da instância do hub IoT permanece o mesmo pós failover, o endereço IP subjacente muda. Por conseguinte, o tempo total para que as operações de tempo de funcionamento sejam realizadas contra a sua instância de hub IoT para ficar totalmente operacional após o processo de failover ser desencadeado pode ser expresso utilizando a seguinte função.

Tempo para recuperar = RTO [10 min - 2 horas para falha manual / 2 - 26 horas para falha iniciada pela Microsoft] + atraso de propagação dNS + Tempo tomado pela aplicação do cliente para atualizar qualquer endereço IP IoT Hub em cache.

> [!IMPORTANT]
> Os SDKs IoT não cache o endereço IP do hub IoT. Recomendamos que o código de utilização entre em interligação com os SDKs não acache o endereço IP do hub IoT.

## <a name="achieve-cross-region-ha"></a>Alcançar a região transversal HA

Se os seus objetivos de uptime de negócio não estiverem satisfeitos com o RTO que as opções de failover ou failover manual iniciadas pela Microsoft fornecem, deve considerar a implementação de um mecanismo de falha automática da região transversal por dispositivo.
Um tratamento completo das topoologias de implantação em soluções IoT está fora do âmbito deste artigo. O artigo discute o modelo regional de implantação de *falhas* para efeitos de elevada disponibilidade e recuperação de desastres.

Num modelo regional de failover, a extremidade traseira da solução funciona principalmente num local de datacenter. Um centro iot secundário e a parte traseira são implantados em outro local do datacenter. Se o centro ioT na região primária sofrer uma paragem ou a conectividade da rede do dispositivo para a região primária for interrompida, os dispositivos usam um ponto final de serviço secundário. Pode melhorar a disponibilidade de solução implementando um modelo de failover transversal em vez de permanecer numa única região. 

A um nível elevado, para implementar um modelo regional de failover com o IoT Hub, você precisa tomar os seguintes passos:

* **Um hub ioT secundário e uma lógica de encaminhamento**do dispositivo : Se o serviço na sua região primária for interrompido, os dispositivos devem começar a ligar-se à sua região secundária. Dada a natureza consciente do Estado da maioria dos serviços envolvidos, é comum que os administradores de soluções desencadeiem o processo de failover inter-região. A melhor forma de comunicar o novo ponto final aos dispositivos, mantendo o controlo do processo, é fazê-los verificar regularmente um serviço de *concierge* para o ponto final ativo atual. O serviço de concierge pode ser uma aplicação web que é replicada e mantida acessível usando técnicas de reorientação DNS (por exemplo, utilizando o Gestor de [Tráfego Azure).](../traffic-manager/traffic-manager-overview.md)

   > [!NOTE]
   > O serviço de hub IoT não é um tipo de ponto final suportado no Azure Traffic Manager. A recomendação é integrar o serviço de concierge proposto com o gestor de tráfego azure, fazendo com que implemente a sonda de saúde de ponta API.

* **Replicação**do registo de identidade : Para ser utilizável, o hub IoT secundário deve conter todas as identidades do dispositivo que possam ligar-se à solução. A solução deve manter cópias de segurança geo-replicadas das identidades do dispositivo e carregá-las para o centro ioT secundário antes de mudar o ponto final ativo para os dispositivos. A funcionalidade de exportação de identidade do ioT Hub é útil neste contexto. Para mais informações, consulte o guia de [desenvolvimento do IoT Hub - registo de identidade](iot-hub-devguide-identity-registry.md).

* **Lógica de fusão**: Quando a região primária voltar a estar disponível, todo o estado e dados que foram criados no local secundário devem ser migrados de volta para a região primária. Este estado e os dados dizem principalmente respeito às identidades do dispositivo e aos metadados de aplicação, que devem ser fundidos com o centro ioT primário e quaisquer outras lojas específicas de aplicação na região primária. 

Para simplificar este passo, deve utilizar operações idempotentes. As operações idempotentes minimizam os efeitos secundários da eventual distribuição consistente de eventos, e de duplicados ou entrega fora de ordem de eventos. Além disso, a lógica da aplicação deve ser concebida para tolerar potenciais inconsistências ou um estado ligeiramente desatualizado. Esta situação pode ocorrer devido ao tempo adicional que o sistema leva para curar com base nos objetivos do ponto de recuperação (RPO).

## <a name="choose-the-right-hadr-option"></a>Escolha a opção HA/DR certa

Aqui está um resumo das opções HA/DR apresentadas neste artigo que podem ser usadas como um quadro de referência para escolher a opção certa que funciona para a sua solução.

| Opção HA/DR | RTO | RPO | Requer intervenção manual? | Complexidade da implementação | Impacto adicional dos custos|
| --- | --- | --- | --- | --- | --- |
| Falha iniciada pela Microsoft |2 - 26 horas|Consulte a tabela RPO acima|Não|Nenhum|Nenhum|
| Falha manual |10 min - 2 horas|Consulte a tabela RPO acima|Sim|Muito baixo. Só precisas de ativar esta operação a partir do portal.|Nenhum|
| Região transversal HA |< 1 min|Depende da frequência de replicação da sua solução HA personalizada|Não|High|> 1x o custo de 1 ioT hub|

## <a name="next-steps"></a>Passos Seguintes

* [O que é o Hub IoT do Azure?](about-iot-hub.md)
* [Comece com IoT Hubs (Quickstart)](quickstart-send-telemetry-dotnet.md)
* [Tutorial: Execute a falha manual para um hub IoT](tutorial-manual-failover.md)