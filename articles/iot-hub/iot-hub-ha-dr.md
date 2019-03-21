---
title: O Azure IoT Hub elevada disponibilidade e recuperação após desastre | Documentos da Microsoft
description: Descreve os recursos do Azure e o IoT Hub que ajudam a criar soluções de IoT do Azure de elevada disponibilidade com que desastre capacidades de recuperação.
author: rkmanda
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/07/2018
ms.author: rkmanda
ms.openlocfilehash: ac47ad10f394eaf31a9f7c12d7a2a03ea23283f6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58092655"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub elevada disponibilidade e recuperação após desastre

Como primeiro passo em direção à implementação de um IoT resiliente a solução, arquitetos, desenvolvedores e proprietários de empresas tem de definir os objetivos de tempo de atividade para as soluções que eles desenvolvem. Essas metas podem ser definidas principalmente com base nos objetivos de negócio específicos de cada cenário. Neste contexto, o artigo [orientações técnicas de continuidade do negócio de Azure](https://docs.microsoft.com/azure/architecture/resiliency/) descreve uma estrutura geral para o ajudar a pensar sobre recuperação de desastre e continuidade de negócio. O [recuperação após desastre e elevada disponibilidade para aplicações do Azure](https://msdn.microsoft.com/library/dn251004.aspx) documento fornece orientações de arquitetura sobre estratégias para aplicações do Azure alcançar a elevada disponibilidade (HA) e a recuperação após desastre (DR).

Este artigo aborda os recursos HA e DR oferecidos especificamente pelo serviço IoT Hub. As áreas gerais discutidas neste artigo são:

- HA intra-região
- Entre regiões DR
- Obtenção de região cruzada HA

Consoante os objetivos de tempo de atividade que definir para as suas soluções de IoT, deve determinar qual das opções descritos abaixo se adequar melhor às seus objetivos empresariais. Incorporar qualquer uma dessas alternativas HA/DR na sua solução de IoT requer uma avaliação cuidadosa dos compromissos entre o:

- Nível de resiliência que necessita 
- Complexidade da implementação e manutenção
- Impacto COGS

## <a name="intra-region-ha"></a>HA intra-região

O serviço IoT Hub fornece intra-região HA implementando redundâncias em quase todas as camadas do serviço. O [SLA publicados pelo serviço do IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub) é conseguido fazendo uso destes redundâncias. Nenhum trabalho adicional é necessária para os desenvolvedores de uma solução de IoT para tirar partido destas funcionalidades de HA. Embora o IoT Hub oferece uma garantia de tempo de atividade razoavelmente alto, as falhas transitórias ainda podem ser esperadas tal como acontece com qualquer plataforma de computação distribuída. Se estiver apenas começando com migrar suas soluções para a cloud de uma solução no local, o foco precisa mudar de otimizar o "tempo médio entre falhas" para "tempo médio para recuperar". Em outras palavras, as falhas transitórias devem ser consideradas normal durante a operação com a cloud em sua composição. Apropriado [políticas de repetição](iot-hub-reliability-features-in-sdks.md) deve ser criado para os componentes de interagir com uma aplicação na cloud para lidar com falhas transitórias.

> [!NOTE]
> Alguns serviços do Azure também fornecem camadas adicionais de disponibilidade numa região integrando [zonas de disponibilidade (AZs)](../availability-zones/az-overview.md). AZs não são atualmente suportadas pelo serviço do IoT Hub.

## <a name="cross-region-dr"></a>Entre regiões DR

Pode haver algumas raras situações quando um centro de dados sofre falhas expandidas devido a falhas de energia elétrica ou outras falhas que envolvem ativos físicos. Tais eventos são raros durante o qual a região de intra recurso de alta disponibilidade descrito acima pode não sempre ajudar. IoT Hub fornece várias soluções para recuperar a partir de tais falhas expandidas. 

As opções de recuperação disponíveis para clientes em tal situação são ""iniciadas pelo Microsoft ativação pós-falha e "ativação pós-falha manual". A diferença fundamental entre os dois é que o Microsoft inicia o primeiro e o utilizador inicia a última opção. Além disso, a ativação pós-falha manual fornece um inferior objetivo de tempo de recuperação (RTO) em comparação comparado a opção de ativação pós-falha iniciada pelo Microsoft. O Rto específico oferecido com cada opção é abordado nas secções abaixo. Quando uma destas opções para efetuar a ativação pós-falha de um hub IoT da respetiva região primária fracasso ser executado, o hub torna-se totalmente funcional nas correspondentes [do Azure região geograficamente emparelhada](../best-practices-availability-paired-regions.md).

Estas opções de ativação pós-falha oferecem os seguintes objetivos de ponto de recuperação (RPOs):

| Tipo de dados | Objetivos de ponto de recuperação (RPO) |
| --- | --- |
| Registo de identidade |0 a 5 minutos perda de dados |
| Dados do dispositivo duplo |0 a 5 minutos perda de dados |
| Mensagens da cloud para dispositivo<sup>1</sup> |0 a 5 minutos perda de dados |
| Principal<sup>1</sup> e tarefas de dispositivo |0 a 5 minutos perda de dados |
| Mensagens do dispositivo para a cloud |Todas as mensagens não lidas são perdidas |
| Mensagens de monitorização de operações |Todas as mensagens não lidas são perdidas |
| Mensagens de comentários do cloud-para-dispositivo |Todas as mensagens não lidas são perdidas |

<sup>1</sup>mensagens cloud para o dispositivo e tarefas de principal não obter recuperadas como parte da ativação pós-falha manual a oferta de pré-visualização desta funcionalidade.

Depois de concluída a operação de ativação pós-falha para o hub IoT, todas as operações a partir do dispositivo e aplicações de back-end devem continuar a trabalhar sem a necessidade de uma intervenção manual.

> [!CAUTION]
> - O nome compatível com o Event Hub e o ponto final do ponto de extremidade de eventos interna do IoT Hub alterações após a ativação pós-falha. Se receber mensagens de telemetria de ponto final incorporado com o cliente do hub de eventos ou o anfitrião do processador de eventos, deve [utilizar a cadeia de ligação do hub IoT](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) para estabelecer a ligação. Isto garante que as aplicações de back-end continuam a funcionar sem a necessidade de intervenção manual após a ativação pós-falha. Se utilizar o nome compatível com o Event Hub e o ponto final na sua aplicação de back-end diretamente, terá de reconfigurar a sua aplicação por [a obter o novo nome compatível com o Event Hub e o ponto final](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) após a ativação pós-falha para continuar operações.
>
> - Após a ativação pós-falha, os eventos emitidos através do Event Grid podem ser consumidos por meio das mesmas subscrição ou subscrições configuradas anteriormente, desde que essas subscrições do Event Grid continuam a estar disponível.
>
> - Quando o encaminhamento para o armazenamento de BLOBs, recomendamos que inscrever os blobs e, em seguida, iterar sobre os mesmos, para garantir que todos os contentores são lidas sem fazer suposições de partição. O intervalo de partição potencialmente poderia alterar durante uma ativação pós-falha iniciada pelo Microsoft ou a ativação pós-falha manual. Para saber como enumere a lista de blobs consulte [encaminhamento para o armazenamento de BLOBs](iot-hub-devguide-messages-d2c.md#azure-blob-storage).

### <a name="microsoft-initiated-failover"></a>Ativação pós-falha iniciada pelo Microsoft

Ativação pós-falha iniciada pelo Microsoft fracasso é exercida pela Microsoft em raras situações de ativação pós-falha de todos o IoT hubs de uma região afetado para a região geograficamente emparelhada correspondente. Este processo é uma opção de predefinido (nenhuma forma dos utilizadores ao excluir) e não requer intervenção do usuário. A Microsoft se reserva o direito de fazer a determinação de quando esta opção irá ser executada. Esse mecanismo não envolve um consentimento do utilizador antes de hub do usuário é a ativação pós-falha. Ativação pós-falha iniciada pelo Microsoft tem um objetivo de tempo de recuperação (RTO) de 26 de 2 horas. 

O RTO grandes é porque a Microsoft tem de efetuar a operação de ativação pós-falha em nome de todos os clientes afetados nessa região. Se estiver a executar uma solução de IoT menos crítica, que pode sustentar um período de indisponibilidade de aproximadamente um dia, há problema que tome uma dependência sobre esta opção para satisfazer os objetivos de recuperação após desastre geral para a sua solução de IoT. O tempo total de tempo de execução operações fique totalmente operacional, uma vez que este processo é acionado, é descrito na secção "de tempo recuperar".

### <a name="manual-failover-preview"></a>Ativação pós-falha manual (pré-visualização)

Se não forem satisfeitos seus objetivos de tempo de atividade de negócios ao passo que o RTO que iniciou a Microsoft fornece ativação pós-falha, deve considerar a utilização de ativação pós-falha manual para acionar o processo de ativação pós-falha por conta própria. O RTO usando esta opção pode ser em qualquer lugar entre 10 minutos para algumas horas. O RTO é, atualmente, uma função do número de dispositivos registados em relação a instância do hub IoT que está a ser realizada a ativação pós-falha. Pode esperar que o RTO de um hub de aproximadamente 100 000 dispositivos de alojamento em aproximados de 15 minutos. O tempo total de tempo de execução operações fique totalmente operacional, uma vez que este processo é acionado, é descrito na secção "de tempo recuperar".

A opção de ativação pós-falha manual está sempre disponível para utilização, independentemente se a região primária está a sofrer um período de indisponibilidade ou não. Por conseguinte, esta opção potencialmente poderia ser usada para executar as ativações pós-falha planeadas. É um exemplo de utilização de ativações pós-falha planeadas para realizar testes de ativação pós-falha periódica. Porém, uma palavra de advertência é que um resultados da operação de ativação pós-falha planeada num tempo de inatividade para o hub para o período definido pelo RTO para esta opção e também resulta numa perda de dados, conforme definido pela tabela de RPO acima. Poderia considerar a configuração de um teste de instância do hub IoT para a opção de ativação pós-falha planeada periodicamente para ganhar confiança na sua habilidade de obter as suas soluções de ponta a cópia de segurança e em execução quando um desastre real acontece.

> [!IMPORTANT]
> - As explorações de teste não devem ser efetuadas em hubs IoT que estão a ser utilizados nos seus ambientes de produção.
>
> - Ativação pós-falha manual não deve ser utilizada como um mecanismo para migrar permanentemente o seu hub entre as regiões do Azure geo emparelhado. Se o fizer, fará com que uma maior latência para operações que está a ser efetuado o hub a partir de dispositivos que se encontrem na região primária antiga.
>
> - Ativação pós-falha manual está atualmente em pré-visualização e não está disponível nas seguintes regiões do Azure. E.U.A. leste, E.U.A. oeste, Norte Europa, Europa Ocidental, sul do Brasil, dos E.U.A. centro-Sul.

### <a name="failback"></a>Reativação pós-falha

Realizar a ativação pós-falha para a região primária antiga pode ser alcançada ao acionar a ação de ativação pós-falha outra hora. Se a operação de ativação pós-falha original foi efetuada para recuperar a partir de um período de indisponibilidade expandido na região primária original, recomendamos que o hub deve falhou para a localização original, assim que esse local foi recuperado da situação de indisponibilidade.

> [!IMPORTANT]
> - Os utilizadores só têm permissão para efetuar a ativação pós-falha com êxito 2 e 2 operações de reativação pós-falha concluída com êxito por dia.
>
> - Seguidos não são permitidas operações de ativação pós-falha/reativação pós-falha. Os utilizadores terão de aguardar por 1 hora entre estas operações.

### <a name="time-to-recover"></a>Tempo para recuperar

Enquanto o FQDN (e, portanto, a cadeia de ligação) da instância de hub IoT permanecem a mesmo após a ativação pós-falha, o endereço IP subjacente será alterado. Por isso o tempo geral para as operações de tempo de execução a ser executadas em relação a sua instância do hub IoT para se tornar totalmente operacional após o processo de ativação pós-falha é acionado pode ser expresso usando a função seguinte.

Tempo para recuperar = RTO [10 min - 2 horas para ativação pós-falha manual | 26 de 2 horas para a ativação pós-falha iniciada pelo Microsoft] + atraso de propagação de DNS + tempo que a aplicação de cliente para atualizar qualquer em cache o endereço IP do IoT Hub.

> [!IMPORTANT]
> Os SDKs IoT não colocar em cache o endereço IP do IoT hub. Recomendamos que fazer interface com os SDKs de código do usuário deve coloca em cache o endereço IP do IoT hub.

## <a name="achieve-cross-region-ha"></a>Obter entre regiões HA

Se os objetivos de tempo de atividade de negócios não forem satisfeitos pelo RTO iniciada pelo Microsoft ativação pós-falha ou opções de ativação pós-falha manual fornecem, deve considerar a implementação de um mecanismo de ativação pós-falha automática entre várias regiões por dispositivo.
Um tratamento completo das topologias de implementação em soluções de IoT está fora do escopo deste artigo. O artigo aborda os *ativação pós-falha regional* modelo de implementação para fins de elevada disponibilidade e recuperação após desastre.

Num modelo de ativação pós-falha, a solução, faça uma cópia final execuções principalmente num único datacenter local. Um hub de IoT secundário e o back-end são implementadas em outro datacenter local. Se o hub IoT na região primária sofre uma falha ou a conectividade de rede do dispositivo para a região primária for interrompida, os dispositivos utilizam um ponto de extremidade de serviço secundário. Pode melhorar a disponibilidade da solução através da implementação de um modelo de ativação pós-falha entre regiões, em vez de manter-se sempre numa única região. 

Num alto nível, implementar um modelo de ativação pós-falha com o IoT Hub, terá de seguir estes passos:

* **Um IoT hub e lógica de roteamento de dispositivo secundário**: Se o serviço na sua região primária é interrompido, dispositivos tem de começar a ligar para a região secundária. Devido à natureza com deteção de estado da maioria dos serviços de envolvidos, é comum para os administradores de solução acionar o processo de ativação pós-falha entre regiões. A melhor maneira para comunicar o novo ponto final para dispositivos, mantendo o controle do processo, é tê-los verificar regularmente uma *concierge* serviço para o ponto final atual do Active Directory. O serviço de concierge pode ser uma aplicação web que é replicada e mantida acessível usando técnicas de redirecionamento de DNS (por exemplo, utilizando [Gestor de tráfego do Azure](../traffic-manager/traffic-manager-overview.md)).

   > [!NOTE]
   > O serviço hub IoT não é um tipo de ponto de extremidade suportados no Gestor de tráfego do Azure. A recomendação é integrar o serviço proposto concierge com o Gestor de tráfego do Azure, tornando-se a implementar a sonda de estado de funcionamento do ponto final API.

* **Replicação do registo de identidade**: Para ser utilizável, o hub IoT secundário tem de conter todas as identidades de dispositivos que podem ligar à solução. A solução deve manter cópias de segurança georreplicado de identidades de dispositivos e carregá-los para o hub IoT secundário antes de mudar o ponto final do Active Directory para os dispositivos. A funcionalidade de exportação de identidade de dispositivo do IoT Hub é útil neste contexto. Para obter mais informações, consulte [Guia do programador do IoT Hub - registo de identidade](iot-hub-devguide-identity-registry.md).

* **Lógica de mesclagem**: Quando a região primária fica novamente disponível, todos os dados que foram criados no site secundário e Estado devem ser migrados para a região primária. Este estado e os dados em grande parte relacionadas com identidades do dispositivo e metadados de aplicação, que devem ser mesclados com o principal hub de IoT e outros arquivos de específicas da aplicação na região primária. 

Para simplificar este passo, deve usar operações idempotentes. Operações Idempotentes minimizar os efeitos de colaterais de distribuição eventual consistente de eventos e de duplicados ou fora de ordem entrega de eventos. Além disso, a lógica do aplicativo deve ser concebida para tolerar possíveis inconsistências ou estado ligeiramente desatualizado. Esta situação pode ocorrer devido ao tempo adicional necessário para o sistema tratá-lo com base nos objetivos de ponto de recuperação (RPO).

## <a name="choose-the-right-hadr-option"></a>Escolher a opção de HA/DR certa

Aqui está um resumo das opções de HA/DR apresentado neste artigo que pode ser utilizado como uma arquitetura de referência para escolher a opção certa que funciona para a sua solução.

| Opção de HA/DR | RTO | RPO: | Requer intervenção manual? | Complexidade da implementação | Impacto de custos adicionais|
| --- | --- | --- | --- | --- | --- |
| Ativação pós-falha iniciada pelo Microsoft |2 - 26 horas|Consulte a tabela RPO acima|Não|Nenhuma|Nenhuma|
| Ativação pós-falha manual |10 min - 2 horas|Consulte a tabela RPO acima|Sim|Muito baixa. Só tem de acionar esta operação a partir do portal.|Nenhuma|
| Cross região HA |< 1 min|Depende da frequência de replicação de sua solução personalizada de HA|Não|Elevado|> 1 x o custo de 1 hub de IoT|

## <a name="next-steps"></a>Passos Seguintes

Siga estas ligações para saber mais sobre o IoT Hub do Azure:

* [Introdução aos Hubs de IoT (início rápido)](quickstart-send-telemetry-dotnet.md)
* [O que é o Hub IoT do Azure?](about-iot-hub.md)