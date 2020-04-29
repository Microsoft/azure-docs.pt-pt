---
title: Continuidade de negócio e recuperação após desastre
description: Desenhe a sua estratégia para proteger dados, recupere rapidamente de eventos disruptivos, restaure recursos exigidos por funções empresariais críticas e mantenha a continuidade do negócio para as Apps Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 7bf71ce7c44229ccf19022e9cfb0162f9d77cd97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437706"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Continuidade de negócios e recuperação de desastres para apps da Azure Logic

Para ajudar a reduzir o impacto e os efeitos que os eventos imprevisíveis têm no seu negócio e clientes, certifique-se de que tem uma solução de recuperação de [ *desastres* (DR)](https://en.wikipedia.org/wiki/Disaster_recovery) em vigor para que possa proteger os dados, restaurar rapidamente os recursos que suportam funções empresariais críticas, e manter as operações em funcionamento para manter [ *a continuidade do negócio* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning). Por exemplo, as perturbações podem incluir interrupções, perdas em infraestruturas subjacentes ou componentes tais como armazenamento, rede ou recursos de computação, falhas de aplicação irrecuperáveis ou até mesmo uma perda total de datacenter. Ao ter uma solução de continuidade de negócios e recuperação de desastres (BCDR) pronta, a sua empresa ou organização pode responder mais rapidamente a interrupções, planeadas ou não planeadas, e reduzir o tempo de inatividade para os seus clientes.

Este artigo fornece orientações e estratégias BCDR que pode aplicar quando constrói fluxos de trabalho automatizados utilizando [aplicações lógicas azure](../logic-apps/logic-apps-overview.md). Os fluxos de trabalho de aplicações lógicas ajudam-no a integrar e orquestrar dados mais facilmente entre apps, serviços na nuvem e sistemas no local, reduzindo o código que tem para escrever. Quando planeia para o BCDR, certifique-se de que considera não só as suas aplicações lógicas, mas também estes recursos Azure que utiliza com as suas aplicações lógicas:

* [Ligações](../connectors/apis-list.md) que cria desde aplicações lógicas a outras apps, serviços e sistemas. Para mais informações, consulte [Ligações aos recursos](#resource-connections) mais tarde neste tópico.

* [No local,](../logic-apps/logic-apps-gateway-connection.md) os gateways de dados que são os recursos do Azure que cria e utiliza nas suas aplicações lógicas para aceder a dados em sistemas no local. Cada recurso gateway representa uma [instalação](../logic-apps/logic-apps-gateway-install.md) separada de gateway de dados num computador local. Para mais informações, consulte as [portas de dados](#on-premises-data-gateways) no local mais tarde neste tópico.

* Contas de [integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) onde define e armazena os artefactos que as aplicações lógicas usam para cenários [de integração empresarial (B2B).](../logic-apps/logic-apps-enterprise-integration-overview.md) Por exemplo, pode criar uma [recuperação de desastres transversais para contas de integração.](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)

* [Ambientes de serviço de integração (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) onde cria aplicações lógicas que funcionam numa instância de tempo de execução de Aplicações Lógicas isoladas dentro de uma rede virtual Azure. Estas aplicações lógicas podem então aceder a recursos protegidos por trás de uma firewall nessa rede virtual.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Locais primários e secundários

Cada aplicação lógica precisa especificar a localização que pretende utilizar para a implementação. Esta localização é ou uma região pública em azure multi-inquilino global, como "West US", ou um ambiente de serviço de integração (ISE) que criou e implantou anteriormente numa rede virtual Azure. Executar aplicações lógicas num ISE é semelhante a aplicações lógicas de execução numa região global do Azure, o que significa que a sua estratégia de recuperação de desastres pode aplicar-se a ambos os cenários. No entanto, as ISEs têm outras considerações, tais como configurar o acesso aos recursos que estão disponíveis apenas para ises.

> [!NOTE]
> Se a sua aplicação lógica também funcionar com artefactos B2B, tais como parceiros comerciais, acordos, schemas, mapas e certificados, que são armazenados numa conta de integração, tanto a sua conta de integração como as aplicações lógicas devem especificar a mesma localização.

Esta estratégia de recuperação de desastres centra-se na criação da sua aplicação lógica primária para [*falhar*](https://en.wikipedia.org/wiki/Failover) numa aplicação lógica de standby ou backup num local alternativo onde as Aplicações Lógicas do Azure também estão disponíveis. Assim, se as primárias sofrerem perdas, perturbações ou falhas, o secundário pode assumir o trabalho. Esta estratégia requer que a sua aplicação de lógica secundária e recursos dependentes já estejam implantados e prontos no local alternativo.

Se seguir boas práticas de DevOps, já utiliza modelos do Gestor de [Recursos Azure](../azure-resource-manager/management/overview.md) para definir e implementar as suas aplicações lógicas e os seus recursos dependentes. Os modelos do Gestor de Recursos dão-lhe a capacidade de usar uma única definição de implementação e, em seguida, usar ficheiros de parâmetros para fornecer os valores de configuração a utilizar para cada destino de implementação. Esta capacidade significa que pode implementar a mesma aplicação lógica para diferentes ambientes, por exemplo, desenvolvimento, teste e produção. Também pode implementar a mesma aplicação lógica para diferentes regiões do Azure ou ISEs, que suporta estratégias de recuperação de desastres que utilizam [regiões emparelhadas.](../best-practices-availability-paired-regions.md)

Para a estratégia de failover, as suas aplicações e localizações lógicas devem satisfazer estes requisitos:

* A instância de aplicações lógicasecundária tem acesso às mesmas aplicações, serviços e sistemas como a instância de aplicações lógica seleções primárias.

* Ambas as instâncias de aplicações lógicas têm o mesmo tipo de hospedeiro. Assim, ou ambas as instâncias são implementadas para regiões do multi-inquilino global Azure, ou ambas as instâncias são implementadas para ISEs, que permitem que as suas aplicações lógicas acedam diretamente a recursos numa rede virtual Azure. Para boas práticas e mais informações sobre regiões emparelhadas para o BCDR, consulte a continuidade do negócio e a recuperação de [desastres (BCDR): Regiões emparelhadas de Azure](../best-practices-availability-paired-regions.md).

  Por exemplo, tanto os locais primários como secundários devem ser ISEs quando a aplicação lógica primária funciona num ISE e utiliza [conectores versão ISE](../connectors/apis-list.md#ise-connectors), ações HTTP para chamar recursos na rede virtual Azure, ou ambos. Neste cenário, a sua aplicação lógica secundária também deve ter uma configuração semelhante no local secundário como a aplicação lógica primária.

  > [!NOTE]
  > Para cenários mais avançados, pode misturar tanto o multi-inquilino Azure como um ISE como localizações. No entanto, certifique-se de que considera e compreende as [diferenças entre a forma como as aplicações lógicas funcionam num ISE contra o multi-inquilino Azure.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

* Se utilizar ISEs, [certifique-se de que estão dimensionados ou que têm capacidade suficiente](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) para lidar com a carga.

#### <a name="example-multi-tenant-azure"></a>Exemplo: Azure multi-inquilino

Este exemplo mostra casos de aplicações lógicas primárias e secundárias, que são implantados para regiões separadas no multi-inquilino global Azure para este cenário. Um único [modelo de Gestor](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) de Recursos define tanto as instâncias de aplicações lógicas como os recursos dependentes exigidos por essas aplicações lógicas. Ficheiros de parâmetros separados especificam os valores de configuração a utilizar para cada local de implementação:

![Instâncias de aplicativos de lógica primária e secundária em locais separados](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Exemplo: Ambiente de serviço de integração

Este exemplo mostra as instâncias de aplicações lógicas primárias e secundárias anteriores, mas implementadas para separar ises. Um único modelo de Gestor de Recursos define ambas as instâncias de aplicações lógicas, os recursos dependentes exigidos por essas aplicações lógicas, e as ISEs como os locais de implementação. Ficheiros parâmetros separados definem os valores de configuração a utilizar para a implantação em cada local:

![Aplicativos lógicos primários e secundários em diferentes locais](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Conexões com recursos

As Aplicações Lógicas Azure fornecem [gatilhos e ações incorporados, além de centenas de conectores geridos](../connectors/apis-list.md) que a sua aplicação lógica pode usar para trabalhar com outras aplicações, serviços, sistemas e outros recursos, tais como contas de Armazenamento Azure, bases de dados do SQL Server, contas de email do Office 365 Outlook, e assim por diante. Se a sua aplicação lógica precisa de acesso a estes recursos, cria ligações que autenticam o acesso a estes recursos. Cada ligação é um recurso Azure separado que existe num local específico e não pode ser usado por recursos em outros locais.

Para a sua estratégia de recuperação de desastres, considere os locais onde existem recursos dependentes em relação aos casos de aplicações lógicas:

* A sua principal instância e recursos dependentes existem em diferentes locais. Neste caso, a sua instância secundária pode ligar-se aos mesmos recursos dependentes ou pontos finais. No entanto, deve criar ligações especificamente para a sua instância secundária. Assim, se a sua localização principal ficar indisponível, as ligações do secundário não são afetadas.

  Por exemplo, suponha que a sua aplicação lógica primária se conecta a um serviço externo como o Salesforce. Normalmente, a disponibilidade e localização do serviço externo são independentes da disponibilidade da sua aplicação lógica. Neste caso, a sua instância secundária pode ligar-se ao mesmo serviço, mas deve ter a sua própria ligação.

* Tanto a sua instância primária como os recursos dependentes existem no mesmo local. Neste caso, os recursos dependentes devem ter backups ou versões replicadas num local diferente para que a sua instância secundária ainda possa aceder a esses recursos.

  Por exemplo, suponha que a sua aplicação lógica primária se conecta a um serviço que está no mesmo local ou região, por exemplo, azure SQL Database. Se toda esta região ficar indisponível, o serviço de base de dados Azure SQL naquela região também não está disponível. Neste caso, gostaria que a sua instância secundária usasse uma base de dados replicada ou de cópia de segurança, juntamente com uma ligação separada a essa base de dados.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Gateways de dados no local

Se a sua aplicação lógica funciona em Azure multi-inquilino e precisa de acesso a recursos no local, como bases de dados do SQL Server, precisa de instalar a [porta de dados no local](../logic-apps/logic-apps-gateway-install.md) num computador local. Em seguida, pode criar um recurso de gateway de dados no portal Azure para que a sua aplicação lógica possa usar o portal quando criar uma ligação ao recurso.

O recurso gateway de dados está associado a uma localização ou região azure, tal como o seu recurso de aplicação lógica. Na sua estratégia de recuperação de desastres, certifique-se de que o portal de dados permanece disponível para a sua aplicação lógica para usar. Pode [ativar uma alta disponibilidade para o seu portal](../logic-apps/logic-apps-gateway-install.md#high-availability) quando tiver várias instalações de gateway.

> [!NOTE]
> Se a sua aplicação lógica funcionar num ambiente de serviço de integração (ISE) e utilizar apenas conectores versão ISE para fontes de dados no local, não precisa da porta de dados porque os conectores ISE fornecem acesso direto ao recurso no local.
>
> Se não houver conector versão ISE disponível para o recurso no local que deseja, a sua aplicação lógica ainda pode criar a ligação utilizando um conector não ISE, que funciona no multi-inquilino global Azure, e não no seu ISE. No entanto, esta ligação requer a porta de dados no local.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Papéis ativos contra ativos passivos

Pode configurar as suas localizações primárias e secundárias para que as instâncias de aplicações lógicas nestes locais possam desempenhar estes papéis:

| Papel primário-secundário | Descrição |
|------------------------|-------------|
| *Ativo ativo* | As instâncias de aplicações lógicas primárias e secundárias em ambos os locais lidam ativamente com pedidos seguindo qualquer um destes padrões: <p><p>- *Balanço de carga*: Pode ter ambas as instâncias a ouvir um ponto final e carregar o tráfego de equilíbrio em cada instância, se necessário. <p>- *Consumidores concorrentes*: Pode ter ambos os casos a atuar como consumidores concorrentes para que os casos concorram para mensagens de uma fila. Se uma instância falhar, a outra instância assume a carga de trabalho. |
| *Passivo ativo* | A instância de aplicação lógica primária lida ativamente com toda a carga de trabalho, enquanto a instância secundária é passiva (desativada ou inativa). O secundário aguarda por um sinal de que o primário está indisponível ou não funciona devido a perturbação ou falha e assume a carga de trabalho como a instância ativa. |
| Combinação | Algumas aplicações lógicas desempenham um papel ativo, enquanto outras aplicações lógicas desempenham um papel passivo ativo. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Exemplos ativos

Estes exemplos mostram a configuração ativa onde ambas as instâncias de aplicações lógicas lidam ativamente com pedidos ou mensagens. Algum outro sistema ou serviço distribui os pedidos ou mensagens entre casos, por exemplo, uma dessas opções:

* Um equilibrador de carga "físico", como uma peça de hardware que encaminha o tráfego

* Um equilibrador de carga "macio", como o Equilíbrio de [Carga Azure](../load-balancer/load-balancer-overview.md) ou [a Gestão API Azure](../api-management/api-management-key-concepts.md). Com a API Management, pode especificar políticas que determinam como carregar o tráfego de entrada de equilíbrio. Ou, você pode usar um serviço que suporta rastreio estatal, por exemplo, [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

  Embora este exemplo mostre principalmente o Azure Load Balancer, pode utilizar a opção que melhor se adequa às necessidades do seu cenário:

  ![Configuração "ativa" que utiliza um equilíbrior de carga ou um serviço de estado](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Cada instância de aplicação lógica funciona como um consumidor e ambos os casos competem por mensagens de uma fila:

  ![Configuração "ativa" que utiliza "consumidores concorrentes"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Exemplos activos-passivos

Este exemplo mostra a configuração activa-passiva onde a instância de aplicação lógica primária está ativa num local, enquanto a instância secundária permanece inativa noutro local. Se as primárias experimentarem uma perturbação ou falha, pode ter um operador a executar um script que ativa o secundário para assumir a carga de trabalho.

![Configuração "activa-passiva" que utiliza "consumidores concorrentes"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Combinação com ativo ativo e ativo-passivo

Este exemplo mostra uma configuração combinada onde a localização primária tem ambos os casos de aplicações de lógica ativa, enquanto a localização secundária tem instâncias de aplicações lógicas passivas ativas. Se a localização primária experimentar uma perturbação ou falha, a aplicação lógica ativa no local secundário, que já está a lidar com uma carga de trabalho parcial, pode assumir toda a carga de trabalho.

* Na localização primária, uma aplicação de lógica ativa ouve uma fila de ônibus de serviço Azure para mensagens, enquanto outra aplicação de lógica ativa verifica por e-mails usando um gatilho de sondagem Do Office 365 Outlook.

* No local secundário, uma aplicação de lógica ativa funciona com a aplicação lógica no local primário, ouvindo e competindo por mensagens da mesma fila de Ônibus de Serviço. Entretanto, uma aplicação de lógica passiva inativa aguarda em espera para verificar se há e-mails quando a localização principal fica indisponível, mas está *desativada* para evitar a releitura de e-mails.

![Combinação "activa-passiva" e "activa-passiva" que usa gatilhos de recorrência](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Estado de aplicação lógica e história

Quando a sua aplicação lógica é desencadeada e começa a funcionar, o estado da aplicação é armazenado no mesmo local onde a aplicação começou e não é transferível para outro local. Se ocorrer uma falha ou perturbação, quaisquer casos de fluxo de trabalho em curso são abandonados. Quando se tem uma localização primária e secundária, novos casos de fluxo de trabalho começam a funcionar no local secundário.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Reduzir casos abandonados em curso

Para minimizar o número de casos de fluxo de trabalho abandonados em curso, pode escolher entre vários padrões de mensagem que pode implementar, por exemplo:

* [Padrão de deslizamento de encaminhamento fixo](https://docs.microsoft.com/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Este padrão de mensagem empresarial que divide um processo de negócio em fases menores. Para cada etapa, cria-se uma aplicação lógica que lida com a carga de trabalho para esse palco. Para comunicar entre si, as suas aplicações lógicas utilizam um protocolo de mensagens assíncronos, como filas de autocarros ou tópicos do Azure Service Bus. Quando se divide um processo em fases mais pequenas, reduz-se o número de processos de negócio que podem ficar presos numa instância de aplicações lógicas falhadas. Para obter informações mais gerais sobre este padrão, consulte os padrões de [integração da Enterprise - Routing slip](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Este exemplo mostra um padrão de deslizamento de encaminhamento onde cada aplicação lógica representa um palco e usa uma fila de Ônibus de serviço para comunicar com a próxima aplicação lógica no processo.

  ![Divida um processo de negócio em fases representadas por aplicações lógicas, que comunicam entre si usando filas de ônibus de serviço Azure](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Se tanto as instâncias de aplicações de lógica primária como secundária seguirem o mesmo padrão de deslizamento de encaminhamento nas suas localizações, pode implementar o padrão de [consumidores concorrentes,](https://docs.microsoft.com/azure/architecture/patterns/competing-consumers) criando [funções ativas](#roles) para esses casos.

* [Padrão de gestor de processos (corretor)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Peek-lock sem padrão de tempo-out](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Acesso ao gatilho e corre a história

Para obter mais informações sobre as execuções passadas da sua aplicação lógica, pode rever o gatilho da aplicação e executar o histórico. O histórico de execução de uma aplicação lógica é armazenado no mesmo local ou região onde a aplicação lógica funcionou, o que significa que não se pode migrar esta história para um local diferente. Se a sua instância primária falhar numa instância secundária, só pode aceder ao gatilho de cada instância e fazer história nos respetivos locais onde esses casos foram executados. No entanto, pode obter informações agnósticas sobre o histórico da sua aplicação lógica, configurando as suas aplicações lógicas para enviar eventos de diagnóstico para um espaço de trabalho Azure Log Analytics. Em seguida, pode rever a saúde e a história através de aplicações lógicas que funcionam em vários locais.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Orientação do tipo de gatilho

O tipo de gatilho que utiliza nas suas aplicações lógicas determina as suas opções de como pode configurar aplicações lógicas em todos os locais da sua estratégia de recuperação de desastres. Aqui estão os tipos de gatilho disponíveis que pode usar em aplicações lógicas:

* [Gatilho de recorrência](#recurrence-trigger)
* [Gatilho de sondagens](#polling-trigger)
* [Gatilho de pedido](#request-trigger)
* [Gatilho webhook](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Gatilho de recorrência

O gatilho **recorrível** é independente de qualquer serviço ou ponto final específico, e os incêndios baseiam-se unicamente num horário especificado e sem outros critérios, por exemplo:

* Uma frequência fixa e intervalo, como a cada 10 minutos
* Um horário mais avançado, como a última segunda-feira de cada mês às 17:00

Quando a sua aplicação lógica começa com um gatilho de Recorrência, precisa de configurar as suas instâncias de aplicações lógicas primárias e secundárias com os [papéis passivo-activos](#roles). Para reduzir o objetivo do tempo de *recuperação* (RTO), que se refere à duração-alvo para restaurar um processo de negócio após uma perturbação ou desastre, pode configurar as suas instâncias de aplicações lógicas com uma combinação de [papéis passivo-activos](#roles) e [papéis passivo-activos.](#roles) Nesta configuração, divide o horário em todos os locais.

Por exemplo, suponha que tem uma aplicação lógica que precisa de ser executada a cada 10 minutos. Pode configurar as suas aplicações e localizações lógicas para que, se a localização primária ficar indisponível, a localização secundária possa assumir o trabalho:

![Combinação "activa-passiva" e "passiva-activa" que usa gatilhos de recorrência](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Na localização primária, configurar [papéis passivo-activos](#roles) para estas aplicações lógicas:

  * Para a aplicação lógica *ativa* ativa, detete o gatilho de Recorrência para começar no topo da hora e repetir a cada 20 minutos, por exemplo, 9:00 AM, 9:20 AM, e assim por diante.

  * Para a aplicação lógica *passiva* para deficientes, detete o gatilho de Recorrência no mesmo horário, mas comece a 10 minutos da hora e repita a cada 20 minutos, por exemplo, 9:10 am, 9:30 am, e assim por diante.

* No local secundário, configurar [passiva-ativo](#roles) para estas aplicações lógicas:

  * Para a aplicação lógica *passiva* de si bilhética, detete tede o gatilho da Recurrence para o mesmo horário que a aplicação lógica ativa no local primário, que está no topo da hora e se repete a cada 20 minutos, por exemplo, 9:00 am, 9:10 am, e assim por diante.

  * Para a aplicação lógica *ativa,* detete o gatilho de Recorrência para o mesmo horário que a aplicação lógica passiva no local primário, que deve começar em 10 minutos após a hora e repetir a cada 20 minutos, por exemplo, 9:10 am, 9:20 am, e assim por diante.

Agora, se um evento disruptivo ocorrer no local principal, ative a aplicação lógica passiva no local alternativo. Dessa forma, se encontrar a falha leva tempo, esta configuração limita o número de recorrências perdidas durante esse atraso.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Gatilho de sondagens

Para verificar regularmente se novos dados para o tratamento estão disponíveis a partir de um serviço ou ponto final específico, a sua aplicação lógica pode usar um gatilho *de sondagem* que chama repetidamente o serviço ou ponto final com base num calendário fixo de recorrência. Os dados que o serviço ou o ponto final fornecem podem ter qualquer um destes tipos:

* Dados estáticos, que descreve dados que estão sempre disponíveis para leitura
* Dados voláteis, que descreve dados que já não estão disponíveis após a leitura

Para evitar a leitura repetida dos mesmos dados, a sua aplicação lógica precisa de se lembrar quais os dados que foram previamente lidos mantendo o estado, quer do lado do cliente, quer do lado do servidor, do serviço ou do sistema.

* Aplicativos lógicos que trabalham com gatilhos de uso do estado do cliente que podem manter o estado.

  Por exemplo, um gatilho que lê uma nova mensagem a partir de uma caixa de entrada de e-mail requer que o gatilho se lembre da mensagem mais recentemente lida. Dessa forma, o gatilho só inicia a aplicação lógica quando a próxima mensagem não lida chegar.

* Aplicações lógicas que funcionam com servidor, serviço ou estado do lado do sistema usam valores de propriedade ou configurações que estão no lado do servidor, serviço ou sistema.

  Por exemplo, um gatilho baseado em consulta que lê uma linha `isRead` de uma base `FALSE`de dados requer que a linha tenha uma coluna definida para . Sempre que o gatilho lê uma linha, a aplicação `isRead` lógica `FALSE` `TRUE`atualiza essa linha mudando a coluna de para .

  Esta abordagem do lado do servidor funciona da mesma forma para as filas de ônibus de serviço ou tópicos que têm semântica em fila onde um gatilho pode ler e bloquear uma mensagem enquanto a aplicação lógica processa a mensagem. Quando a aplicação lógica termina o processamento, o gatilho elimina a mensagem da fila ou tópico.

Do ponto de vista da recuperação de desastres, quando configurar as instâncias primárias e secundárias da sua aplicação lógica, certifique-se de que contabiliza estes comportamentos com base no facto de a sua aplicação lógica seguir o estado do lado do cliente ou do lado do servidor:

* Para uma aplicação lógica que funciona com o estado do cliente, certifique-se de que a sua aplicação lógica não lê a mesma mensagem mais de uma vez. Apenas um local pode ter uma instância de aplicação lógica ativa em qualquer momento específico. Certifique-se de que a instância lógica da aplicação no local alternativo está inativa ou desativada até que a instância primária falhe na localização alternativa.

  Por exemplo, o gatilho do Office 365 Outlook mantém o estado do cliente e rastreia a marca de tempo para o e-mail mais recente mente lido para evitar ler um duplicado.

* Para uma aplicação lógica que funciona com o estado do servidor, pode configurar as instâncias lógicas das suas aplicações lógicas para desempenhar [funções ativas](#roles) onde funcionam como consumidores concorrentes ou [papéis passivos ativos](#roles) onde a instância alternativa aguarda até que a instância primária falhe na localização alternativa.

  Por exemplo, a leitura de uma fila de mensagens, como uma fila de ônibus de serviço Azure, usa o estado do servidor porque o serviço de fila mantém bloqueios nas mensagens para impedir que outros clientes leiam as mesmas mensagens.

  > [!NOTE]
  > Se a sua aplicação lógica precisa de ler mensagens numa ordem específica, por exemplo, a partir de uma fila de ônibus de serviço, você pode usar o padrão de consumidor concorrente, mas apenas quando combinado com sessões de ônibus de serviço, que também é conhecido como o padrão de [ *comboio sequencial* ](https://docs.microsoft.com/azure/architecture/patterns/sequential-convoy). Caso contrário, deve configurar as suas instâncias de aplicações lógicas com os papéis passivo-activos.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Gatilho de pedido

O trigger **Request** torna a sua aplicação lógica callable de outras aplicações, serviços e sistemas e é normalmente usada para fornecer essas capacidades:

* Uma API de REST direto para a sua aplicação lógica que outros podem chamar

  Por exemplo, utilize o gatilho do Pedido para iniciar a sua aplicação lógica para que outras aplicações lógicas possam ligar para o gatilho utilizando o fluxo de **trabalho call - Logic Apps.**

* Um [webhook](#webhook-trigger) ou mecanismo de callback para a sua aplicação lógica

* Uma forma de executar manualmente as operações ou rotinas do utilizador para ligar para a sua aplicação lógica, por exemplo, utilizando um script PowerShell que executa uma tarefa específica

Do ponto de vista da recuperação de desastres, o gatilho do Pedido é um recetor passivo porque a aplicação lógica não faz qualquer trabalho e aguarda até que algum outro serviço ou sistema ligue explicitamente ao gatilho. Como ponto final passivo, pode configurar as suas instâncias primárias e secundárias desta forma:

* [Ativo ativo](#roles): Ambas as instâncias lidam ativamente com pedidos ou chamadas. O chamador ou router equilibra ou distribui o tráfego entre essas instâncias.

* [Activa-passiva](#roles): Apenas a instância primária está ativa e trata de todo o trabalho, enquanto a instância secundária aguarda até que as experiências primárias se insuem ou não. O chamador ou router determina quando ligar para a instância secundária.

Como uma arquitetura recomendada, você pode usar a Azure API Management como um proxy para as aplicações lógicas que usam triggers Request. A API Management proporciona resiliência transversal incorporada e a capacidade de [encaminhar o tráfego através de vários pontos finais.](https://docs.microsoft.com/azure/api-management/api-management-howto-deploy-multi-region)

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Gatilho webhook

Um gatilho *webhook* fornece a capacidade para a sua aplicação lógica subscrever um serviço, passando um URL de *callback* para esse serviço. A sua aplicação lógica pode então ouvir e esperar que um evento específico aconteça nesse ponto final de serviço. Quando o evento acontece, o serviço chama o gatilho do webhook utilizando o URL de callback, que executa a aplicação lógica. Quando ativado, o gatilho do gancho de teia subscreve o serviço. Quando desativado, o gatilho desabre as inscrições do serviço.

Do ponto de vista da recuperação de desastres, crie instâncias primárias e secundárias que utilizem gatilhos webhook para desempenhar papéis passivos ativos, porque apenas uma instância deve receber eventos ou mensagens do ponto final subscrito.

## <a name="assess-primary-instance-health"></a>Avaliar a saúde da instância primária

Para que a sua estratégia de recuperação de desastres funcione, a sua solução precisa de formas de executar estas tarefas:

* [Verifique a disponibilidade da instância primária](#check-primary-availability)
* [Monitorizar a saúde da instância primária](#monitor-primary-health)
* [Ativar a instância secundária](#activate-secondary)

Esta secção descreve uma solução que pode usar de imediato ou como base para o seu próprio design. Aqui está uma visão visual de alto nível para esta solução:

![Crie aplicativo lógico de cão de guarda que monitorize uma aplicação lógica de verificação de saúde no local primário](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Verifique a disponibilidade da instância primária

Para determinar se a instância primária está disponível, em execução e capaz de funcionar, pode criar uma aplicação lógica de "verificação de saúde" que esteja no mesmo local que a instância primária. Em seguida, pode ligar para esta aplicação de verificação de saúde a partir de um local alternativo. Se a aplicação de verificação de saúde responder com sucesso, a infraestrutura subjacente ao serviço de Aplicações Azure Logic naquela região está disponível e a funcionar. Se a aplicação de verificação de saúde não responder, pode assumir que a localização já não é saudável.

Para esta tarefa, crie uma aplicação lógica básica de verificação de saúde que execute estas tarefas:

1. Recebe uma chamada da aplicação watchdog utilizando o gatilho do Pedido.

1. Responda com um estado que indique se a aplicação lógica verificada ainda funciona utilizando a ação Resposta.

   > [!IMPORTANT]
   > A aplicação lógica de verificação de saúde deve usar uma ação de Resposta para que a app responda sincronizadamente, não assíncronamente.

1. Opcionalmente, para determinar se a localização primária é saudável, pode ter em conta a saúde de quaisquer outros serviços que interajam com a aplicação lógica alvo neste local. Basta expandir a aplicação lógica de verificação de saúde para avaliar a saúde destes outros serviços também.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Criar uma aplicação lógica de cão de guarda

Para monitorizar a saúde da instância primária e ligar para a aplicação lógica de verificação de saúde, crie uma aplicação lógica de "cão de guarda" num *local alternativo*. Por exemplo, pode configurar a aplicação lógica de vigilância para que, se a chamada para a lógica do controlo de saúde falhar, o cão de guarda possa enviar um alerta à sua equipa de operações para que possa investigar a falha e por que razão a instância primária não responde.

> [!IMPORTANT]
> Certifique-se de que a sua aplicação lógica de cão de guarda está num *local que difere da localização primária*. Se o serviço de Aplicações Lógicas na localização primária tiver problemas, a sua aplicação lógica de cão de guarda pode não funcionar.

Para esta tarefa, no local secundário, criar uma aplicação lógica de cão de guarda que execute estas tarefas:

1. Executar com base numa recorrência fixa ou programada utilizando o gatilho recorrência.

   Pode definir a recorrência para um valor que abaixo do nível de tolerância para o seu objetivo de tempo de recuperação (RTO).

1. Ligue para a aplicação lógica de verificação de saúde no local primário, utilizando a ação HTTP, por exemplo:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Ativar a sua instância secundária

Para ativar automaticamente a instância secundária, pode criar uma aplicação lógica que chama a API de gestão, como o [conector Do Gestor de Recursos Azure,](https://docs.microsoft.com/connectors/arm/) para ativar as aplicações lógicas apropriadas no local secundário. Pode expandir a sua aplicação de cão de guarda para chamar esta aplicação lógica de ativação depois de um número específico de falhas acontecerem.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Recolher dados de diagnóstico

Pode configurar o registo de registos para as suas aplicações lógicas e enviar os dados de diagnóstico resultantes para serviços como o Azure Storage, Azure Event Hubs e Azure Log Analytics para posterior manuseamento e processamento.

* Se pretender utilizar estes dados com o Azure Log Analytics, pode disponibilizar os dados tanto para os locais primários como secundários, configurando as **definições** de Diagnóstico da sua aplicação lógica e enviando os dados para vários espaços de trabalho do Log Analytics. Para mais informações, consulte os [registos do Azure Monitor e recolha dados de diagnóstico para aplicações lógicas do Azure](../logic-apps/monitor-logic-apps-log-analytics.md).

* Se pretender enviar os dados para o Azure Storage ou Azure Event Hubs, pode disponibilizar os dados tanto para os locais primários como secundários, configurando georedundância. Para obter mais informações, veja estes artigos:<p>

  * [Recuperação de desastres de armazenamento de Blob Azure e falha de conta](../storage/common/storage-disaster-recovery-guidance.md)
  * [Recuperação geo-desastre do Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da resiliência para Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview)
* [Lista de verificação de resiliência para serviços específicos do Azure](https://docs.microsoft.com/azure/architecture/checklist/resiliency-per-service)
* [Gestão de dados para resiliência em Azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/data-management)
* [Backup e recuperação de desastres para aplicações azure](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery)
* [Recupere de uma interrupção de serviço ao nível da região](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region)
* [Acordos de Nível de Serviço da Microsoft (SLAs) para serviços Azure](https://azure.microsoft.com/support/legal/sla/)
