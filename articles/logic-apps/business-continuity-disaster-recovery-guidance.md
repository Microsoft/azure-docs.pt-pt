---
title: Continuidade de negócio e recuperação após desastre
description: Desenhe a sua estratégia para proteger os dados, recupere rapidamente de eventos disruptivos, restaure os recursos exigidos pelas funções críticas do negócio e mantenha a continuidade do negócio para a Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: 0a36cb468ebcb77c0614bffd0afc392df3655c20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89658211"
---
# <a name="business-continuity-and-disaster-recovery-for-azure-logic-apps"></a>Continuidade de negócios e recuperação de desastres para Azure Logic Apps

Para ajudar a reduzir o impacto e os efeitos que os eventos imprevisíveis têm no seu negócio e clientes, certifique-se de que tem uma solução [ *de recuperação de desastres* (DR)](https://en.wikipedia.org/wiki/Disaster_recovery) para que possa proteger os dados, restaurar rapidamente os recursos que suportam funções comerciais críticas, e manter as operações em funcionamento para manter a [ *continuidade do negócio* (BC)](https://en.wikipedia.org/wiki/Business_continuity_planning). Por exemplo, as perturbações podem incluir interrupções, perdas em infraestruturas ou componentes subjacentes, tais como armazenamento, rede ou recursos de computação, falhas de aplicação irrecuperáveis ou até mesmo uma perda completa do datacenter. Ao ter uma solução de continuidade de negócios e recuperação de desastres (BCDR) pronta, a sua empresa ou organização pode responder mais rapidamente a interrupções, planeadas ou não planeadas, e reduzir o tempo de inatividade para os seus clientes.

Este artigo fornece orientações e estratégias bcdr que pode aplicar quando constrói fluxos de trabalho automatizados utilizando [aplicações Azure Logic.](../logic-apps/logic-apps-overview.md) Os fluxos de trabalho de aplicações lógicas ajudam-no a integrar e a orquestrar mais facilmente dados entre apps, serviços na nuvem e sistemas no local, reduzindo o código que tem de escrever. Quando planeia para o BCDR, certifique-se de que não considera apenas as suas aplicações lógicas, mas também estes recursos Azure que utiliza com as suas aplicações lógicas:

* [Ligações](../connectors/apis-list.md) que cria de aplicações lógicas para outras apps, serviços e sistemas. Para mais informações, consulte [Ligações aos recursos](#resource-connections) mais tarde neste tópico.

* [Gateways de dados no local](../logic-apps/logic-apps-gateway-connection.md) que são recursos Azure que cria e utiliza nas suas aplicações lógicas para aceder a dados em sistemas no local. Cada recurso de gateway representa uma instalação separada [do gateway de dados](../logic-apps/logic-apps-gateway-install.md) num computador local. Para mais informações, consulte [as portas de dados no local](#on-premises-data-gateways) mais tarde neste tópico.

* [Contas de integração](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) onde define e armazena os artefactos que as aplicações lógicas usam para cenários [de integração empresarial (B2B).](../logic-apps/logic-apps-enterprise-integration-overview.md) Por exemplo, pode [configurar a recuperação de desastres entre regiões para contas de integração.](../logic-apps/logic-apps-enterprise-integration-b2b-business-continuity.md)

* [Ambientes de serviço de integração (ISEs)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) onde cria aplicações lógicas que funcionam numa instância de execução de Aplicações Lógicas isolada dentro de uma rede virtual Azure. Estas aplicações lógicas podem então aceder a recursos protegidos por trás de uma firewall naquela rede virtual.

<a name="primary-secondary-locations"></a>

## <a name="primary-and-secondary-locations"></a>Locais primários e secundários

Cada aplicação lógica precisa de especificar a localização que pretende utilizar para implementação. Esta localização é ou uma região pública no Azure multi-inquilino global, como "West US", ou um ambiente de serviço de integração (ISE) que você criou e implantou anteriormente numa rede virtual Azure. Executar aplicações lógicas num ISE é semelhante ao funcionamento de aplicações lógicas numa região global de Azure, o que significa que a sua estratégia de recuperação de desastres pode aplicar-se a ambos os cenários. No entanto, as ISE têm outras considerações, tais como a configuração do acesso aos recursos que estão disponíveis apenas para as ISE.

> [!NOTE]
> Se a sua aplicação lógica também funcionar com artefactos B2B, como parceiros de trading, acordos, esquemas, mapas e certificados, que estão armazenados numa conta de integração, tanto a sua conta de integração como as aplicações lógicas devem especificar a mesma localização.

Esta estratégia de recuperação de desastres centra-se na criação da sua aplicação de lógica primária para [*falhar*](https://en.wikipedia.org/wiki/Failover) numa aplicação lógica de standby ou backup num local alternativo onde as Azure Logic Apps também estão disponíveis. Assim, se as primárias sofrerem perdas, perturbações ou falhas, o secundário pode assumir o trabalho. Esta estratégia requer que a sua app de lógica secundária e recursos dependentes já estejam implantados e prontos na localização alternativa.

Se seguir boas práticas de DevOps, já utiliza [modelos do Azure Resource Manager](../azure-resource-manager/management/overview.md) para definir e implementar as suas aplicações lógicas e os seus recursos dependentes. Os modelos de Gestor de Recursos dão-lhe a capacidade de usar uma única definição de implementação e, em seguida, usar ficheiros de parâmetros para fornecer os valores de configuração a utilizar para cada destino de implantação. Esta capacidade significa que pode implementar a mesma aplicação lógica para diferentes ambientes, por exemplo, desenvolvimento, teste e produção. Também pode implementar a mesma aplicação lógica para diferentes regiões ou ISEs do Azure, que suporta estratégias de recuperação de desastres que utilizam [regiões emparelhadas.](../best-practices-availability-paired-regions.md)

Para a estratégia de failover, as suas aplicações lógicas e locais devem satisfazer estes requisitos:

* A instância de aplicações lógicas secundárias tem acesso às mesmas aplicações, serviços e sistemas que a instância de aplicação lógica primária.

* Ambas as instâncias de aplicações lógicas têm o mesmo tipo de anfitrião. Assim, ou ambas as instâncias são implementadas em regiões no Azure multi-inquilino global, ou ambas as instâncias são implementadas para ISEs, que permitem que as suas aplicações lógicas acedam diretamente a recursos numa rede virtual Azure. Para as melhores práticas e mais informações sobre as regiões emparelhadas para o BCDR, consulte [a continuidade do Negócio e a recuperação de desastres (BCDR): regiões emparelhadas azure](../best-practices-availability-paired-regions.md).

  Por exemplo, tanto as localizações primárias como secundárias devem ser ISEs quando a aplicação de lógica primária é executada num ISE e utiliza [conectores com versão ISE](../connectors/apis-list.md#ise-connectors), ações HTTP para chamar recursos na rede virtual Azure, ou ambos. Neste cenário, a sua aplicação de lógica secundária também deve ter uma configuração semelhante no local secundário como a aplicação lógica primária.

  > [!NOTE]
  > Para cenários mais avançados, você pode misturar tanto o Azure multi-inquilino como um ISE como locais. No entanto, certifique-se de que considera e compreende as [diferenças entre a forma como as aplicações lógicas funcionam num ISE contra o multi-inquilino Azure.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference)

* Se utilizar ISEs, [certifique-se de que são dimensionados ou têm capacidade suficiente](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) para manusear a carga.

#### <a name="example-multi-tenant-azure"></a>Exemplo: Multi-inquilino Azure

Este exemplo mostra casos de aplicações lógicas primárias e secundárias, que são implementadas para regiões separadas no multi-inquilino global Azure para este cenário. Um único [modelo de Gestor de Recursos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) define tanto as instâncias de aplicações lógicas como os recursos dependentes exigidos por essas aplicações lógicas. Ficheiros de parâmetros separados especificam os valores de configuração a utilizar para cada local de implantação:

![Casos de aplicações lógicas primárias e secundárias em locais separados](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations.png)

#### <a name="example-integration-service-environment"></a>Exemplo: Ambiente de serviço de integração

Este exemplo mostra as instâncias de aplicações lógicas primárias e secundárias anteriores, mas implementadas para separar as ISEs. Um único modelo de Gestor de Recursos define ambas as instâncias de aplicações lógicas, os recursos dependentes exigidos por essas aplicações lógicas, e as ISEs como locais de implementação. Os ficheiros de parâmetros separados definem os valores de configuração a utilizar para implantação em cada local:

![Aplicativos de lógica primária e secundária em diferentes locais](./media/business-continuity-disaster-recovery-guidance/primary-secondary-locations-ise.png)

<a name="resource-connections"></a>

## <a name="connections-to-resources"></a>Ligações aos recursos

A Azure Logic Apps fornece [gatilhos e ações incorporados, além de centenas de conectores geridos](../connectors/apis-list.md) que a sua aplicação lógica pode usar para trabalhar com outras aplicações, serviços, sistemas e outros recursos, como contas de Armazenamento Azure, bases de dados do SQL Server, contas de e-mail de trabalho ou escola, e assim por diante. Se a sua aplicação lógica necessitar de acesso a estes recursos, cria ligações que autenticam o acesso a estes recursos. Cada ligação é um recurso Azure separado que existe num local específico e não pode ser usado por recursos em outros locais.

Para a sua estratégia de recuperação de desastres, considere as localizações onde existem recursos dependentes em relação às suas instâncias de aplicações lógicas:

* A sua primeira instância e recursos dependentes existem em diferentes locais. Neste caso, o seu caso secundário pode ligar-se aos mesmos recursos dependentes ou pontos finais. No entanto, deve criar ligações especificamente para a sua instância secundária. Assim, se a sua localização primária ficar indisponível, as ligações do secundário não serão afetadas.

  Por exemplo, suponha que a sua aplicação de lógica primária se conecta a um serviço externo como o Salesforce. Normalmente, a disponibilidade e localização do serviço externo são independentes da disponibilidade da sua aplicação lógica. Neste caso, a sua instância secundária pode ligar-se ao mesmo serviço, mas deve ter a sua própria ligação.

* Tanto a sua instância primária como os recursos dependentes existem no mesmo local. Neste caso, os recursos dependentes devem ter backups ou versões replicadas num local diferente para que a sua instância secundária ainda possa aceder a esses recursos.

  Por exemplo, suponha que a sua aplicação de lógica primária se conecta a um serviço que está no mesmo local ou região, por exemplo, Azure SQL Database. Se toda esta região ficar indisponível, o serviço de Base de Dados Azure SQL nessa região também é provavelmente indisponível. Neste caso, gostaria que a sua instância secundária usasse uma base de dados replicada ou de reserva, juntamente com uma ligação separada à base de dados.

<a name="on-premises-data-gateways"></a>

## <a name="on-premises-data-gateways"></a>Gateways de dados no local

Se a sua aplicação lógica funciona no Azure multi-inquilino e precisa de acesso a recursos no local, como bases de dados do SQL Server, precisa de instalar o [portal de dados no local](../logic-apps/logic-apps-gateway-install.md) num computador local. Pode então criar um recurso de gateway de dados no portal Azure para que a sua aplicação lógica possa utilizar o gateway quando criar uma ligação ao recurso.

O recurso de gateway de dados está associado a uma localização ou região de Azure, tal como o seu recurso de aplicação lógica. Na sua estratégia de recuperação de desastres, certifique-se de que o gateway de dados permanece disponível para a sua aplicação lógica para usar. Pode [ativar uma alta disponibilidade para o seu gateway](../logic-apps/logic-apps-gateway-install.md#high-availability) quando tiver várias instalações de gateway.

> [!NOTE]
> Se a sua aplicação lógica funciona num ambiente de serviço de integração (ISE) e utiliza apenas conectores com versão ISE para fontes de dados no local, não precisa do gateway de dados porque os conectores ISE fornecem acesso direto ao recurso no local.
>
> Se nenhum conector versão ISE estiver disponível para o recurso no local que você deseja, a sua aplicação lógica ainda pode criar a ligação usando um conector não ISE, que funciona no Azure multi-inquilino global, e não no seu ISE. No entanto, esta ligação requer o portal de dados no local.

<a name="roles"></a>

## <a name="active-active-versus-active-passive-roles"></a>Funções ativas versus ativas passivas

Pode configurar as suas localizações primárias e secundárias para que as instâncias de aplicações lógicas nestes locais possam desempenhar estes papéis:

| Papel primário-secundário | Description |
|------------------------|-------------|
| *Ativo ativo* | As instâncias de aplicações lógicas primárias e secundárias em ambos os locais lidam ativamente com os pedidos seguindo qualquer um destes padrões: <p><p>- *Equilíbrio de carga*: Pode fazer com que ambas as instâncias ouçam um ponto final e carreguem o tráfego de equilíbrio para cada instância, se necessário. <p>- *Consumidores concorrentes*: Pode fazer com que ambas as instâncias atuem como consumidores concorrentes para que os casos concorram por mensagens de uma fila. Se um caso falhar, o outro caso assume a carga de trabalho. |
| *Ativo-passivo* | A instância da aplicação lógica primária lida ativamente com toda a carga de trabalho, enquanto a instância secundária é passiva (desativada ou inativa). O secundário aguarda um sinal de que o primário está indisponível ou não funciona devido a perturbação ou falha e assume a carga de trabalho como o caso ativo. |
| Combinação | Algumas aplicações lógicas desempenham um papel ativo, enquanto outras aplicações lógicas desempenham um papel ativo-passivo. |
|||

<a name="active-active-examples"></a>

### <a name="active-active-examples"></a>Exemplos ativos

Estes exemplos mostram a configuração ativa ativa onde ambas as instâncias lógicas de aplicações lidam ativamente com pedidos ou mensagens. Algum outro sistema ou serviço distribui os pedidos ou mensagens entre instâncias, por exemplo, uma destas opções:

* Um equilibrador de carga "físico", como uma peça de hardware que encaminha o tráfego

* Um equilibrador de carga "macio", como [o Azure Load Balancer](../load-balancer/load-balancer-overview.md) ou [o Azure API Management](../api-management/api-management-key-concepts.md). Com a API Management, pode especificar políticas que determinam como carregar o equilíbrio do tráfego de entrada. Ou, você pode usar um serviço que suporta rastreio estatal, por exemplo, [Azure Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

  Embora este exemplo mostre principalmente o Azure Load Balancer, pode utilizar a opção que melhor se adequa às necessidades do seu cenário:

  ![Configuração "active-active" que usa um equilibrador de carga ou serviço estatal](./media/business-continuity-disaster-recovery-guidance/active-active-setup-load-balancer.png)

* Cada instância de aplicação lógica funciona como um consumidor e tem ambas as instâncias a competir por mensagens de uma fila:

  ![Configuração "ativa" que utiliza "consumidores concorrentes"](./media/business-continuity-disaster-recovery-guidance/active-active-competing-consumers-pattern.png)

<a name="active-passive-examples"></a>

### <a name="active-passive-examples"></a>Exemplos passivos ativos

Este exemplo mostra a configuração activa-passiva onde a instância da aplicação lógica primária está ativa num local, enquanto a instância secundária permanece inativa noutro local. Se o primário sofrer uma perturbação ou falha, pode ter um operador a executar um script que ativa o secundário para assumir a carga de trabalho.

![Configuração "activa-passiva" que utiliza "consumidores concorrentes"](./media/business-continuity-disaster-recovery-guidance/active-passive-setup.png)

<a name="active-active-active-passive-examples"></a>

### <a name="combination-with-active-active-and-active-passive"></a>Combinação com ativo-ativo e activo-passivo

Este exemplo mostra uma configuração combinada onde a localização primária tem ambos os casos de aplicações lógicas ativas, enquanto a localização secundária tem instâncias de aplicações lógicas ativas passivas. Se a localização primária sofrer uma perturbação ou falha, a aplicação lógica ativa no local secundário, que já está a lidar com uma carga de trabalho parcial, pode assumir toda a carga de trabalho.

* Na localização primária, uma aplicação lógica ativa ouve uma fila de mensagens do Azure Service Bus, enquanto outra aplicação lógica ativa verifica os e-mails utilizando um gatilho de sondagem do Office 365 Outlook.

* Na localização secundária, uma aplicação lógica ativa funciona com a aplicação lógica na localização primária, ouvindo e competindo por mensagens da mesma fila de Service Bus. Enquanto isso, uma aplicação lógica passiva inativa aguarda em espera para verificar se a localização primária fica indisponível, mas é *desativada* para evitar reler e-mails.

![Combinação "activa-passiva" e "activa-passiva" que utiliza gatilhos de recorrência](./media/business-continuity-disaster-recovery-guidance/combo-active-active-active-passive-setup.png)

<a name="state-history"></a>

## <a name="logic-app-state-and-history"></a>Estado e história da aplicação lógica

Quando a sua aplicação lógica é ativada e começa a funcionar, o estado da aplicação é armazenado no mesmo local onde a aplicação começou e não é transferível para outro local. Se ocorrer uma falha ou perturbação, quaisquer casos de fluxo de trabalho em curso são abandonados. Quando se tem uma configuração de localizações primárias e secundárias, novas instâncias de fluxo de trabalho começam a funcionar no local secundário.

<a name="reduce-abandoned-in-progress"></a>

### <a name="reduce-abandoned-in-progress-instances"></a>Reduzir casos em curso abandonados

Para minimizar o número de casos de fluxo de trabalho abandonados em curso, pode escolher entre vários padrões de mensagem que pode implementar, por exemplo:

* [Padrão de deslizamento de encaminhamento fixo](/biztalk/esb-toolkit/message-routing-patterns#routing-slip)

  Este padrão de mensagem da empresa que divide um processo de negócio em estágios menores. Para cada etapa, cria-se uma aplicação lógica que trata da carga de trabalho para esse palco. Para comunicar uns com os outros, as suas aplicações lógicas usam um protocolo de mensagens assíncronas, como filas de autocarros da Azure Service Ou tópicos. Quando se divide um processo em fases mais pequenas, reduz-se o número de processos de negócio que podem ficar presos numa instância de aplicação lógica falhada. Para obter informações mais gerais sobre este padrão, consulte [os padrões de integração da Enterprise - Deslize de encaminhamento](https://www.enterpriseintegrationpatterns.com/patterns/messaging/RoutingTable.html).

  Este exemplo mostra um padrão de deslizamento de encaminhamento onde cada aplicação lógica representa um palco e usa uma fila de Service Bus para comunicar com a próxima aplicação lógica no processo.

  ![Divida um processo de negócio em estágios representados por apps lógicas, que comunicam entre si usando as filas do Azure Service Bus](./media/business-continuity-disaster-recovery-guidance/fixed-routing-slip-pattern.png)

  Se as instâncias de aplicações lógicas primárias e secundárias seguirem o mesmo padrão de deslizamento de encaminhamento nas suas localizações, pode implementar o [padrão de consumidores concorrentes,](/azure/architecture/patterns/competing-consumers) criando [papéis ativos](#roles) para esses casos.

* [Padrão de gestor de processo (corretor)](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ProcessManager.html)

* [Peek-lock sem padrão de tempo limite](https://social.technet.microsoft.com/wiki/contents/articles/50022.azure-service-bus-how-to-peek-lock-a-message-from-queue-using-azure-logic-apps.aspx)

<a name="access-trigger-runs-history"></a>

### <a name="access-to-trigger-and-runs-history"></a>Acesso ao gatilho e execução da história

Para obter mais informações sobre as execuções de fluxo de trabalho passadas da sua aplicação lógica, pode rever o gatilho da aplicação e executar o histórico. O histórico de execução de uma aplicação lógica está armazenado no mesmo local ou região onde essa aplicação lógica foi executada, o que significa que não se pode migrar esta história para um local diferente. Se a sua instância primária falhar numa instância secundária, só pode aceder ao gatilho de cada instância e executar a história nos respetivos locais onde essas ocorrências foram executados. No entanto, pode obter informações agnósticas sobre o histórico da sua aplicação lógica, configurando as suas aplicações lógicas para enviar eventos de diagnóstico para um espaço de trabalho do Azure Log Analytics. Em seguida, pode rever a saúde e a história através de aplicações lógicas que funcionam em vários locais.

<a name="trigger-types-guidance"></a>

## <a name="trigger-type-guidance"></a>Orientação do tipo de gatilho

O tipo de gatilho que utiliza nas suas aplicações lógicas determina as suas opções para configurar aplicações lógicas em locais da sua estratégia de recuperação de desastres. Aqui estão os tipos de gatilho disponíveis que pode usar em aplicações lógicas:

* [Gatilho de recorrência](#recurrence-trigger)
* [Gatilho das sondagens](#polling-trigger)
* [Pedido de gatilho](#request-trigger)
* [Gatilho webhook](#webhook-trigger)

<a name="recurrence-trigger"></a>

### <a name="recurrence-trigger"></a>Gatilho de recorrência

O gatilho **de recorrência** é independente de qualquer serviço ou ponto final específico, e os incêndios baseiam apenas um calendário especificado e nenhum outro critério, por exemplo:

* Uma frequência e intervalo fixos, como a cada 10 minutos
* Um horário mais avançado, como a última segunda-feira de cada mês às 17:00

Quando a sua aplicação lógica começa com um gatilho de Recorrência, precisa de configurar as suas instâncias de aplicação lógica primária e secundária com as [funções activa-passivas](#roles). Para reduzir o objetivo do *tempo de recuperação* (RTO), que se refere à duração-alvo para restaurar um processo de negócio após uma interrupção ou desastre, pode configurar as suas instâncias de aplicações lógicas com uma combinação de [funções passivo-ativas](#roles) e [funções passivas-activas.](#roles) Nesta configuração, divide o horário em locais.

Por exemplo, suponha que tem uma aplicação lógica que precisa de ser executada a cada 10 minutos. Pode configurar as suas aplicações lógicas e localizações para que, se a localização primária ficar indisponível, a localização secundária possa assumir o trabalho:

![Combinação "activa-passiva" e "passiva-activa" que utiliza gatilhos de recorrência](./media/business-continuity-disaster-recovery-guidance/combo-active-passive-passive-active-setup.png)

* Na localização primária, crie [papéis passivos ativos](#roles) para estas aplicações lógicas:

  * Para a aplicação lógica *ativa* ativa, desacorda o gatilho de Recorrência para começar no topo da hora e repita a cada 20 minutos, por exemplo, 9:00 AM, 9:20 AM, e assim por diante.

  * Para a aplicação lógica *passiva* desativada, desagreie o gatilho de Recorrência para o mesmo horário, mas comece a 10 minutos depois da hora e repita a cada 20 minutos, por exemplo, 9:10 AM, 9:30 AM, e assim por diante.

* Na localização secundária, crie [passiva-ativa](#roles) para estas aplicações lógicas:

  * Para a aplicação lógica *passiva* desativada, desagreie o gatilho de Recorrência para o mesmo horário que a aplicação lógica ativa na localização primária, que está no topo da hora e repita a cada 20 minutos, por exemplo, 9:00 AM, 9:10 AM, e assim por diante.

  * Para a aplicação lógica *ativa* ativa, desagreie o gatilho de Recorrência para o mesmo horário que a aplicação lógica passiva na localização primária, que deve começar a 10 minutos depois da hora e repetir a cada 20 minutos, por exemplo, 9:10 AM, 9:20 AM, e assim por diante.

Agora, se um evento disruptivo acontecer na localização primária, ative a aplicação lógica passiva na localização alternativa. Desta forma, se encontrar a falha leva tempo, esta configuração limita o número de recorrências perdidas durante esse atraso.

<a name="polling-trigger"></a>

### <a name="polling-trigger"></a>Gatilho das sondagens

Para verificar regularmente se novos dados para o processamento estão disponíveis a partir de um serviço específico ou ponto final, a sua aplicação lógica pode usar um gatilho de *sondagens* que liga repetidamente para o serviço ou ponto final com base num calendário de recorrência fixo. Os dados que o serviço ou o ponto final fornecem podem ter qualquer um destes tipos:

* Dados estáticos, que descrevem dados que estão sempre disponíveis para leitura
* Dados voláteis, que descrevem dados que já não estão disponíveis após a leitura

Para evitar ler repetidamente os mesmos dados, a sua aplicação lógica precisa de se lembrar quais os dados que foram previamente lidos mantendo o estado no lado do cliente ou no lado do servidor, serviço ou sistema.

* Aplicativos lógicos que trabalham com o estado do lado do cliente usam gatilhos que podem manter o estado.

  Por exemplo, um gatilho que lê uma nova mensagem a partir de uma caixa de entrada de e-mail requer que o gatilho se lembre da mensagem mais recente. Desta forma, o gatilho inicia a aplicação lógica apenas quando a próxima mensagem não lida chegar.

* As aplicações lógicas que funcionam com o servidor, o serviço ou o estado do estado do sistema utilizam valores ou configurações de propriedade que estão no lado do servidor, serviço ou sistema.

  Por exemplo, um gatilho baseado em consultas que lê uma linha de uma base de dados requer que a linha tenha uma `isRead` coluna definida para `FALSE` . Sempre que o gatilho lê uma linha, a aplicação lógica atualiza essa linha alterando a `isRead` coluna de `FALSE` `TRUE` .

  Esta abordagem do lado do servidor funciona da mesma forma para as filas de Service Bus ou tópicos que têm semântica em fila onde um gatilho pode ler e bloquear uma mensagem enquanto a aplicação lógica processa a mensagem. Quando a aplicação lógica termina o processamento, o gatilho elimina a mensagem da fila ou do tópico.

Do ponto de vista da recuperação de desastres, quando configurar as instâncias primárias e secundárias da sua aplicação lógica, certifique-se de que contabiliza estes comportamentos com base no facto de a sua aplicação lógica seguir o estado do lado do cliente ou do lado do servidor:

* Para uma aplicação lógica que funciona com o estado do lado do cliente, certifique-se de que a sua aplicação lógica não lê a mesma mensagem mais do que uma vez. Apenas uma localização pode ter uma aplicação lógica ativa em qualquer momento específico. Certifique-se de que a instância da aplicação lógica na localização alternativa é inativa ou desativada até que a instância principal falhe na localização alternativa.

  Por exemplo, o gatilho do Office 365 Outlook mantém o estado do lado do cliente e rastreia o horário para o e-mail mais recentemente lido para evitar a leitura de uma duplicação.

* Para uma aplicação lógica que funcione com o estado do lado do servidor, pode configurar as suas instâncias de aplicação lógica para desempenhar [papéis ativos](#roles) onde funcionam como consumidores concorrentes ou [funções passivas ativas](#roles) onde a instância alternativa aguarda até que a instância principal falhe na localização alternativa.

  Por exemplo, a leitura de uma fila de mensagens, como uma fila de autocarros do Serviço Azure, utiliza o estado do lado do servidor porque o serviço de fila mantém bloqueios em mensagens para evitar que outros clientes leiam as mesmas mensagens.

  > [!NOTE]
  > Se a sua aplicação lógica precisar de ler mensagens numa ordem específica, por exemplo, a partir de uma fila de Autocarros de Serviço, pode utilizar o padrão de consumidor concorrente, mas apenas quando combinado com sessões de Service Bus, que também é conhecido como o [padrão *de comboio sequencial.*](/azure/architecture/patterns/sequential-convoy) Caso contrário, deve configurar as suas instâncias de aplicações lógicas com as funções activa-passivas.

<a name="request-trigger"></a>

### <a name="request-trigger"></a>Pedido de gatilho

O gatilho **Request** torna a sua aplicação lógica callable a partir de outras aplicações, serviços e sistemas e é normalmente utilizado para fornecer estas capacidades:

* Uma API rest direta para a sua app lógica que outros podem chamar

  Por exemplo, utilize o gatilho Request para iniciar a sua aplicação lógica para que outras aplicações lógicas possam ligar para o gatilho utilizando o **fluxo de trabalho de Chamada - Ação de Aplicações Lógicas.**

* Um [webhook](#webhook-trigger) ou mecanismo de retorno para a sua aplicação lógica

* Uma forma de executar manualmente operações ou rotinas de utilizador para ligar para a sua aplicação lógica, por exemplo, usando um script PowerShell que executa uma tarefa específica

Do ponto de vista da recuperação de desastres, o gatilho Request é um recetor passivo porque a aplicação lógica não faz qualquer trabalho e espera até que algum outro serviço ou sistema chame explicitamente o gatilho. Como ponto final passivo, pode configurar as suas instâncias primárias e secundárias desta forma:

* [Ativo:](#roles)Ambas as instâncias lidam ativamente com pedidos ou chamadas. O chamador ou router equilibra ou distribui o tráfego entre essas instâncias.

* [Active-pass:](#roles)Apenas a instância primária está ativa e trata de todo o trabalho, enquanto a instância secundária aguarda até que as primeiras experiências se disrupções ou falhas. O chamador ou router determina quando deve ligar para a instância secundária.

Como uma arquitetura recomendada, você pode usar a Azure API Management como um proxy para as aplicações lógicas que usam triggers request. A API Management proporciona [resiliência trans-regional incorporada e a capacidade de encaminhar o tráfego através de vários pontos finais.](../api-management/api-management-howto-deploy-multi-region.md)

<a name="webhook-trigger"></a>

### <a name="webhook-trigger"></a>Gatilho webhook

Um gatilho *webhook* fornece a capacidade para a sua aplicação lógica subscrever um serviço, passando um *URL de retorno* para esse serviço. A sua aplicação lógica pode então ouvir e esperar que um evento específico aconteça nesse ponto final de serviço. Quando o evento acontece, o serviço chama o gatilho webhook usando o URL de retorno, que executa então a aplicação lógica. Quando ativado, o gatilho webhook subscreve o serviço. Quando desativado, o gatilho não se subscreve do serviço.

Do ponto de vista da recuperação de desastres, crie casos primários e secundários que utilizem os gatilhos webhook para desempenhar papéis passivos ativos, porque apenas uma instância deve receber eventos ou mensagens do ponto final subscrito.

## <a name="assess-primary-instance-health"></a>Avaliar a saúde dos casos primários

Para que a sua estratégia de recuperação de desastres funcione, a sua solução precisa de formas de executar estas tarefas:

* [Verifique a disponibilidade da instância primária](#check-primary-availability)
* [Monitorizar a saúde da instância primária](#monitor-primary-health)
* [Ativar a instância secundária](#activate-secondary)

Esta secção descreve uma solução que pode usar de imediato ou como base para o seu próprio design. Aqui está uma visão visual de alto nível para esta solução:

![Crie uma aplicação lógica de cão de guarda que monitorize uma aplicação lógica de verificação de saúde na localização primária](./media/business-continuity-disaster-recovery-guidance/check-location-health-watchdog.png)

<a name="check-primary-availability"></a>

### <a name="check-primary-instance-availability"></a>Verifique a disponibilidade de instâncias primárias

Para determinar se a instância principal está disponível, em execução e capaz de funcionar, pode criar uma aplicação lógica de "verificação de saúde" que está no mesmo local que a instância principal. Em seguida, pode ligar para esta aplicação de verificação de saúde a partir de um local alternativo. Se a aplicação de verificação de saúde responder com sucesso, a infraestrutura subjacente ao serviço Azure Logic Apps naquela região está disponível e a funcionar. Se a aplicação de verificação de saúde não responder, pode assumir que a localização já não é saudável.

Para esta tarefa, crie uma aplicação lógica básica de verificação de saúde que execute estas tarefas:

1. Recebe uma chamada da aplicação watchdog utilizando o gatilho 'Pedido'.

1. Responda com um estado que indique se a aplicação lógica verificada ainda funciona utilizando a ação Resposta.

   > [!IMPORTANT]
   > A aplicação lógica de verificação de saúde deve usar uma ação de Resposta para que a aplicação responda de forma sincronizada e não assíncronea.

1. Opcionalmente, para determinar ainda mais se a localização primária é saudável, pode ter em conta a saúde de quaisquer outros serviços que interajam com a aplicação lógica alvo neste local. Basta expandir a aplicação lógica de verificação de saúde para avaliar a saúde para estes outros serviços também.

<a name="monitor-primary-health"></a>

### <a name="create-a-watchdog-logic-app"></a>Criar uma aplicação lógica de cão de guarda

Para monitorizar a saúde da instância primária e ligar para a aplicação lógica de verificação de saúde, crie uma aplicação lógica "watchdog" num *local alternativo*. Por exemplo, pode configurar a aplicação lógica watchdog para que, se ligar para a lógica de verificação de saúde falhar, o cão de guarda pode enviar um alerta para a sua equipa de operações para que eles possam investigar a falha e por que a instância principal não responde.

> [!IMPORTANT]
> Certifique-se de que a sua aplicação lógica watchdog está num *local diferente da localização primária.* Se o serviço De Aplicações Lógicas na localização primária tiver problemas, a sua aplicação lógica watchdog pode não funcionar.

Para esta tarefa, na localização secundária, crie uma aplicação lógica de cão de guarda que execute estas tarefas:

1. Executar com base numa recorrência fixa ou programada utilizando o gatilho de Recorrência.

   Pode definir a recorrência para um valor inferior ao nível de tolerância para o seu objetivo de tempo de recuperação (RTO).

1. Ligue para a aplicação lógica de verificação de saúde na localização primária utilizando a ação HTTP, por exemplo:

<a name="activate-secondary"></a>

### <a name="activate-your-secondary-instance"></a>Ativar a sua instância secundária

Para ativar automaticamente a instância secundária, pode criar uma aplicação lógica que ligue para a API de gestão, como o [conector Azure Resource Manager](/connectors/arm/) para ativar as aplicações lógicas apropriadas na localização secundária. Pode expandir a sua aplicação watchdog para chamar esta aplicação lógica de ativação depois de um número específico de falhas acontecerem.

<a name="collect-diagnostic-data"></a>

## <a name="collect-diagnostic-data"></a>Recolher dados de diagnóstico

Pode configurar o registo para a sua aplicação lógica e enviar os dados de diagnóstico resultantes para serviços como Azure Storage, Azure Event Hubs e Azure Log Analytics para posterior tratamento e processamento.

* Se pretender utilizar estes dados com o Azure Log Analytics, pode disponibilizar os dados tanto para as localizações primárias como secundárias, configurando **as definições** de Diagnóstico da sua aplicação lógica e enviando os dados para vários espaços de trabalho do Log Analytics. Para obter mais informações, consulte [configurar registos do Azure Monitor e recolher dados de diagnóstico para Apps Azure Logic](../logic-apps/monitor-logic-apps-log-analytics.md).

* Se pretender enviar os dados para Azure Storage ou Azure Event Hubs, pode disponibilizar os dados tanto para as localizações primárias como secundárias, criando geo-redundância. Para obter mais informações, veja estes artigos:<p>

  * [Recuperação de desastres de armazenamento Azure Blob e falha na conta](../storage/common/storage-disaster-recovery-guidance.md)
  * [Azure Event Hubs recuperação de geo-desastres](../event-hubs/event-hubs-geo-dr.md)

## <a name="next-steps"></a>Passos seguintes

* [Visão geral da resiliência para Azure](/azure/architecture/framework/resiliency/overview)
* [Lista de verificação de resiliência para serviços específicos do Azure](/azure/architecture/checklist/resiliency-per-service)
* [Gestão de dados para a resiliência em Azure](/azure/architecture/framework/resiliency/data-management)
* [Backup e recuperação de desastres para aplicações da Azure](/azure/architecture/framework/resiliency/backup-and-recovery)
* [Recupere de uma interrupção de serviço ao nível da região](/azure/architecture/resiliency/recovery-loss-azure-region)
* [Microsoft Service Level Agreements (SLAs) para serviços Azure](https://azure.microsoft.com/support/legal/sla/)
