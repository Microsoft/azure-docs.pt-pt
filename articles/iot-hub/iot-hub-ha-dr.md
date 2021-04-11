---
title: Azure IoT Hub alta disponibilidade e recuperação de desastres | Microsoft Docs
description: Descreve as funcionalidades do Azure e IoT Hub que o ajudam a construir soluções Azure IoT altamente disponíveis com capacidades de recuperação de desastres.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: philmea
ms.openlocfilehash: 9d2ffac813456398c02066c978c37bdb09501aeb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628990"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Elevada disponibilidade e recuperação após desastre do Hub IoT

Como primeiro passo para a implementação de uma solução IoT resiliente, arquitetos, desenvolvedores e empresários devem definir os objetivos de uptime para as soluções que estão construindo. Estes objetivos podem ser definidos principalmente com base em objetivos específicos de negócio para cada cenário. Neste contexto, o artigo [Azure Business Continuity Technical Guidance](/azure/architecture/resiliency/) descreve um quadro geral para ajudá-lo a pensar na continuidade do negócio e na recuperação de desastres. O trabalho [de recuperação de desastres e alta disponibilidade para aplicações Azure](/azure/architecture/reliability/disaster-recovery) fornece orientação de arquitetura sobre estratégias para aplicações Azure para alcançar alta disponibilidade (HA) e recuperação de desastres (DR).

Este artigo discute as funcionalidades HA e DR oferecidas especificamente pelo serviço IoT Hub. As grandes áreas discutidas neste artigo são:

- HA intrarregião
- Região transversal DR
- Alcançar a região transversal HA

Dependendo dos objetivos de uptime que define para as suas soluções IoT, deverá determinar quais das opções descritas abaixo se adequam melhor aos seus objetivos de negócio. A incorporação de qualquer uma destas alternativas HA/DR na sua solução IoT requer uma avaliação cuidadosa das trocas entre:

- Nível de resiliência que precisa 
- Complexidade de implementação e manutenção
- Impacto COGS

## <a name="intra-region-ha"></a>HA intrarregião

O serviço IoT Hub fornece HA intrarregião implementando despedimentos em quase todas as camadas do serviço. O [SLA publicado pelo serviço IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub) é conseguido recorrendo a estes despedimentos. Não são necessários trabalhos adicionais pelos desenvolvedores de uma solução IoT para tirar partido destas funcionalidades ha. Embora o IoT Hub ofereça uma garantia razoavelmente elevada de tempo, ainda se podem esperar falhas transitórias como em qualquer plataforma de computação distribuída. Se está apenas a começar a migrar as suas soluções para a nuvem a partir de uma solução no local, o seu foco precisa de passar de otimizar "o tempo médio entre falhas" para "significar tempo para recuperar". Por outras palavras, as falhas transitórias devem ser consideradas normais durante o funcionamento com a nuvem na mistura. Devem ser incorporadas políticas de [reorientação](iot-hub-reliability-features-in-sdks.md) adequadas aos componentes que interagem com uma aplicação em nuvem para lidar com falhas transitórias.

> [!NOTE]
> Alguns serviços Azure também fornecem camadas adicionais de disponibilidade dentro de uma região, integrando-se com [Zonas de Disponibilidade (AZs)](../availability-zones/az-overview.md). Atualmente, as AZs não são suportadas pelo serviço IoT Hub.

## <a name="cross-region-dr"></a>Região transversal DR

Pode haver algumas situações raras quando um datacenter experimenta interrupções prolongadas devido a falhas de energia ou outras falhas envolvendo ativos físicos. Tais eventos são raros durante os quais a capacidade ha intrarregião acima descrita pode nem sempre ajudar. O IoT Hub fornece múltiplas soluções para a recuperação de tais interrupções prolongadas. 

As opções de recuperação disponíveis para os clientes em tal situação são falha [de falha iniciada pela Microsoft](#microsoft-initiated-failover) e falha [manual.](#manual-failover) A diferença fundamental entre os dois é que a Microsoft inicia a primeira e o utilizador inicia este último. Além disso, o failover manual fornece um objetivo de tempo de recuperação mais baixo (RTO) em comparação com a opção de failover iniciada pela Microsoft. Os RTOs específicos oferecidos com cada opção são discutidos nas secções abaixo. Quando qualquer uma destas opções para executar o failover de um hub IoT da sua região primária é exercida, o hub torna-se totalmente funcional na [região geo-emparelhada Azure](../best-practices-availability-paired-regions.md)correspondente.

Ambas as opções de failover oferecem os seguintes objetivos de ponto de recuperação (RPOs):

| Tipo de dados | Objetivos do ponto de recuperação (RPO) |
| --- | --- |
| Registo de identidade |Perda de dados de 0 a 5 minutos |
| Dados gémeos do dispositivo |Perda de dados de 0 a 5 minutos |
| Mensagens nuvem-para-dispositivo<sup>1</sup> |Perda de dados de 0 a 5 minutos |
| Trabalhos dos pais<sup>1</sup> e dos dispositivos |Perda de dados de 0 a 5 minutos |
| Mensagens do dispositivo para a cloud |Todas as mensagens não lidas estão perdidas |
| Mensagens de monitorização de operações |Todas as mensagens não lidas estão perdidas |
| Mensagens de feedback nuvem-para-dispositivo |Todas as mensagens não lidas estão perdidas |

<sup>1</sup> As mensagens em nuvem-para-dispositivo e os trabalhos dos pais não são recuperados como parte do failover manual.

Uma vez concluída a operação de failover para o hub IoT, espera-se que todas as operações do dispositivo e aplicações de back-end continuem a funcionar sem necessidade de uma intervenção manual. Isto significa que as suas mensagens dispositivo-nuvem devem continuar a funcionar e todo o registo do dispositivo está intacto. Os eventos emitidos através da Grade de Eventos podem ser consumidos através das mesmas subscrições configuradas mais cedo, desde que essas subscrições da Grelha de Eventos continuem disponíveis. Não é necessário manuseamento adicional para pontos finais personalizados.

> [!CAUTION]
> - O nome e ponto final compatíveis com o Event Hub do IoT Hub incorporado alteram-se após a falha. Ao receber mensagens de telemetria do ponto final incorporado utilizando o cliente Event Hub ou o anfitrião do processador de eventos, deverá [utilizar o fio de ligação do hub IoT](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) para estabelecer a ligação. Isto garante que as suas aplicações de back-end continuem a funcionar sem necessitar de um pós de intervenção manual que falhe. Se utilizar o nome e o ponto final compatíveis com o Event Hub na sua aplicação diretamente, terá de [ir buscar o novo ponto final compatível com o Event Hub](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) após a falha para continuar as operações. 
>
> - Se utilizar funções Azure ou Azure Stream Analytics para ligar o ponto final de Eventos incorporados, poderá ter de realizar um **Restart**. Isto porque durante o failover anteriores as compensações deixaram de ser válidas.
>
> - Ao encaminhar para o armazenamento, recomendamos listar as bolhas ou ficheiros e, em seguida, iterar sobre eles, para garantir que todas as bolhas ou ficheiros são lidos sem fazer quaisquer suposições de partição. O intervalo de partição pode potencialmente mudar durante uma falha de falha iniciada pela Microsoft ou falha manual. Pode utilizar a [Lista Blobs API](/rest/api/storageservices/list-blobs) para enumerar a lista de blobs ou [Lista ADLS Gen2 API](/rest/api/storageservices/datalakestoragegen2/filesystem/listpaths) para a lista de ficheiros. Para saber mais, consulte [o Azure Storage como um ponto final de encaminhamento](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint).

## <a name="microsoft-initiated-failover"></a>Falha iniciada pela Microsoft

O failover iniciado pela Microsoft é exercido pela Microsoft em situações raras para falhar todos os hubs IoT de uma região afetada para a região geo-emparelhada correspondente. Este processo é uma opção padrão (não há forma de os utilizadores optarem por não optar) e não requer qualquer intervenção do utilizador. A Microsoft reserva-se o direito de determinar quando esta opção será exercida. Este mecanismo não envolve o consentimento do utilizador antes do hub do utilizador ser falhado. O failover iniciado pela Microsoft tem um objetivo de tempo de recuperação (RTO) de 2-26 horas. 

O grande RTO deve-se ao facto de a Microsoft ter de realizar a operação de failover em nome de todos os clientes afetados naquela região. Se estiver a executar uma solução IoT menos crítica que possa suportar um tempo de paragem de aproximadamente um dia, não faz mal que tome uma dependência desta opção para satisfazer os objetivos globais de recuperação de desastres para a sua solução IoT. O tempo total para as operações de tempo de execução ficarem totalmente operacionais uma vez que este processo é desencadeado, é descrito na secção "Tempo para recuperar".

## <a name="manual-failover"></a>Ativação pós-falha manual

Se os objetivos de uptime do seu negócio não forem satisfeitos pelo RTO que a Microsoft iniciou a falha fornece, considere usar o failover manual para desencadear o processo de failover. O RTO que usa esta opção pode estar entre 10 minutos e algumas horas. O RTO é atualmente uma função do número de dispositivos registados contra a instância do hub IoT que está a ser chumbada. Você pode esperar que o RTO para um hub que hospeda aproximadamente 100.000 dispositivos esteja no estádio de 15 minutos. O tempo total para as operações de tempo de execução ficarem totalmente operacionais uma vez que este processo é desencadeado, é descrito na secção "Tempo para recuperar".

A opção de failover manual está sempre disponível para utilização, independentemente de a região primária estar ou não a passar por tempo de inatividade. Por conseguinte, esta opção poderia potencialmente ser utilizada para executar falhas planeadas. Um exemplo de utilização de falhas planeadas é a realização de exercícios periódicos de failover. Uma palavra de precaução, porém, é que uma operação de failover planeada resulta numa paragem para o centro para o período definido pelo RTO para esta opção, e também resulta numa perda de dados tal como definida pelo quadro de RPO acima. Você poderia considerar a criação de um teste ioT hub instância para exercer periodicamente a opção de failover planeada para ganhar confiança na sua capacidade de colocar as suas soluções de ponta a ponta em funcionamento quando um verdadeiro desastre acontece.

O failover manual está disponível sem custos adicionais para os hubs IoT criados após 18 de maio de 2017

Para obter instruções passo a passo, consulte [Tutorial: Execute o failover manual para um hub IoT](tutorial-manual-failover.md)

### <a name="running-test-drills"></a>Exercícios de teste de execução

Os exercícios de ensaio não devem ser efetuados em centros IoT que estão a ser utilizados nos seus ambientes de produção.

### <a name="dont-use-manual-failover-to-migrate-iot-hub-to-a-different-region"></a>Não use o failover manual para migrar o hub IoT para uma região diferente

O failover manual *não* deve ser utilizado como um mecanismo para migrar permanentemente o seu hub entre as regiões geo emparelhadas Azure. Ao fazê-lo, aumenta a latência para as operações que estão a ser realizadas contra o hub IoT a partir de dispositivos aoacalado na antiga região primária.

## <a name="failback"></a>Reativação pós-falha

O regresso à antiga região primária pode ser conseguido desencadeando outra vez a ação de incumprimento. Se a operação original de failover foi realizada para recuperar de uma paragem prolongada na região primária original, recomendamos que o hub seja falhado de volta ao local original uma vez que esse local tenha recuperado da situação de paragem.

> [!IMPORTANT]
> - Os utilizadores só podem realizar 2 falhas bem sucedidas e 2 operações de failback bem sucedidas por dia.
>
> - Não são permitidas operações de recuo/failback. Tens de esperar 1 hora entre estas operações.

## <a name="time-to-recover"></a>Hora de recuperar

Enquanto o FQDN (e, portanto, a cadeia de ligação) da instância do hub IoT permanece o mesmo registo de falha, o endereço IP subjacente altera. Portanto, o tempo geral para que as operações de tempo de execução que estão a ser realizadas contra a sua instância do hub IoT se tornem totalmente operacionais após o processo de failover ser desencadeado pode ser expresso usando a seguinte função.

Tempo para recuperar = RTO [10 min - 2 horas para falha manual | 2 - 26 horas para failover iniciado pela Microsoft] + atraso de propagação de DNS + Tempo tomado pela aplicação do cliente para refrescar qualquer endereço IP IoT Hub em cache.

> [!IMPORTANT]
> Os SDKs IoT não cache o endereço IP do hub IoT. Recomendamos que o código de utilizador que inter-rela contrário com os SDKs não cache o endereço IP do hub IoT.

## <a name="achieve-cross-region-ha"></a>Alcance a região transversal HA

Se os objetivos de uptime do seu negócio não forem satisfeitos pelo RTO que as opções de failover iniciadas pela Microsoft ou as opções manuais de failover fornecem, deve considerar a implementação de um mecanismo de falhação automática por dispositivo.
Um tratamento completo das topologias de implantação em soluções IoT está fora do âmbito deste artigo. O artigo discute o modelo regional de implantação de *failover* com o propósito de elevada disponibilidade e recuperação de desastres.

Num modelo regional de failover, a solução back end funciona principalmente num local de datacenter. Um centro de IoT secundário e a parte traseira são implantados em outro local do datacenter. Se o hub IoT na região primária sofrer uma falha ou a conectividade da rede do dispositivo para a região primária for interrompida, os dispositivos usam um ponto final de serviço secundário. Pode melhorar a disponibilidade da solução implementando um modelo de failover transversal em vez de permanecer numa única região. 

A um nível elevado, para implementar um modelo regional de failover com o IoT Hub, você precisa tomar os seguintes passos:

* **Uma lógica de encaminhamento de ioT secundária e de encaminhamento de dispositivos**: Se o serviço na sua região primária for interrompido, os dispositivos devem começar a ligar-se à sua região secundária. Dada a natureza consciente do Estado da maioria dos serviços envolvidos, é comum que os administradores de soluções desencadeiem o processo de failover inter-região. A melhor forma de comunicar o novo ponto final aos dispositivos, mantendo o controlo do processo, é fazê-los verificar regularmente um serviço *de concierge* para o atual ponto final ativo. O serviço de concierge pode ser uma aplicação web que é replicada e mantida acessível usando técnicas de redirecionamento DNS (por exemplo, usando [O Gestor de Tráfego Azure).](../traffic-manager/traffic-manager-overview.md)

   > [!NOTE]
   > O serviço de hub IoT não é um tipo de ponto final suportado em Azure Traffic Manager. A recomendação é integrar o serviço de concierge proposto com o gestor de tráfego Azure, tornando-o implementar a sonda de saúde final API.

* **Replicação do registo de identidade**: Para ser utilizável, o hub secundário de IoT deve conter todas as identidades do dispositivo que possam ligar-se à solução. A solução deve manter cópias de segurança geo-replicadas das identidades do dispositivo e carregá-las para o centro IoT secundário antes de mudar o ponto final ativo para os dispositivos. A funcionalidade de exportação de identidade do dispositivo do IoT Hub é útil neste contexto. Para mais informações, consulte o [guia de desenvolvimento do IoT Hub - registo de identidade](iot-hub-devguide-identity-registry.md).

* **Lógica de fusão**: Quando a região primária voltar a estar disponível, todo o estado e dados que foram criados no local secundário devem ser migrados de volta para a região primária. Este estado e os dados dizem principalmente respeito às identidades dos dispositivos e metadados de aplicação, que devem ser fundidos com o centro de IoT primário e quaisquer outras lojas específicas da aplicação na região primária. 

Para simplificar este passo, deve utilizar operações idempotentes. As operações idempotentes minimizam os efeitos secundários da eventual distribuição consistente de eventos, e de duplicados ou entregas fora de encomenda de eventos. Além disso, a lógica da aplicação deve ser concebida para tolerar potenciais inconsistências ou estado ligeiramente desatualizado. Esta situação pode ocorrer devido ao tempo adicional que o sistema leva para curar com base em objetivos de ponto de recuperação (RPO).

## <a name="choose-the-right-hadr-option"></a>Escolha a opção HA/DR certa

Aqui está um resumo das opções HA/DR apresentadas neste artigo que pode ser usado como um quadro de referência para escolher a opção certa que funciona para a sua solução.

| Opção HA/DR | RTO | RPO | Requer intervenção manual? | Complexidade de implementação | Impacto adicional dos custos|
| --- | --- | --- | --- | --- | --- |
| Falha iniciada pela Microsoft |2 - 26 horas|Consulte a tabela RPO acima|No|Nenhum|Nenhum|
| Ativação pós-falha manual |10 min - 2 horas|Consulte a tabela RPO acima|Yes|Muito baixo. Só precisas de ativar esta operação a partir do portal.|Nenhum|
| Região transversal HA |< 1 min|Depende da frequência de replicação da sua solução ha personalizada|No|Alto|> 1x o custo de 1 hub IoT|

## <a name="next-steps"></a>Passos seguintes

* [O que é o IoT Hub do Azure?](about-iot-hub.md)
* [Começa com o IoT Hubs (Quickstart)](quickstart-send-telemetry-dotnet.md)
* [Tutorial: Executar failover manual para um hub IoT](tutorial-manual-failover.md)
