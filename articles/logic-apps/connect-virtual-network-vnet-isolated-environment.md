---
title: Ligue-se a redes virtuais Azure com um ISE
description: Criar um ambiente de serviço de integração (ISE) que possa aceder às redes virtuais Azure (VNETs) a partir de Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 6683c1b78b0e7ecba162026708c83843e2c08180
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478882"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Ligue-se a redes virtuais Azure a partir de Aplicações Lógicas Azure utilizando um ambiente de serviço de integração (ISE)

Para cenários em que as suas aplicações lógicas e contas de integração precisam de acesso a uma [rede virtual Azure, crie](../virtual-network/virtual-networks-overview.md)um ambiente de serviço de [ *integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Um ISE é um ambiente isolado que utiliza armazenamento dedicado e outros recursos que são mantidos separados do serviço "global" de Aplicações Lógicas Multi-Inquilinos. Esta separação também reduz qualquer impacto que outros inquilinos do Azure possam ter no desempenho das suas apps. Um ISE também lhe fornece os seus próprios endereços IP estáticos. Estes endereços IP são separados dos endereços IP estáticos que são partilhados pelas aplicações lógicas no serviço público, multi-inquilino.

Quando cria um ISE, o Azure *injeta* esse ISE na sua rede virtual Azure, que depois implanta o serviço De aplicações lógicas na sua rede virtual. Quando criar uma aplicação lógica ou uma conta de integração, selecione o ise como localização. A sua aplicação lógica ou conta de integração pode então aceder diretamente a recursos, tais como máquinas virtuais (VMs), servidores, sistemas e serviços, na sua rede virtual.

![Selecione ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Para que as aplicações lógicas e as contas de integração trabalhem em conjunto num ISE, ambos devem usar o *mesmo ISE* que a sua localização.

Um ISE aumentou os limites na duração do percurso, retenção de armazenamento, saída, tempos de pedido e resposta http, tamanhos de mensagens e pedidos de conector personalizados. Para mais informações, consulte [Limites e configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md). Para saber mais sobre o ISEs, consulte [o Acesso aos recursos da Rede Virtual Azure a partir de Aplicações Lógicas Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Este artigo mostra-lhe como completar estas tarefas utilizando o portal Azure:

* Ative o acesso ao seu ISE.
* Crie o seu ISE.
* Adicione capacidade extra ao seu ISE.

Também pode criar um ISE utilizando o [modelo de quickstart do Gestor](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) de Recursos Azure ou utilizando as Aplicações Lógicas REST API, incluindo a configuração de chaves geridas pelo cliente:

* [Criar um ambiente de serviço de integração (ISE) utilizando as Aplicações Lógicas REST API](../logic-apps/create-integration-service-environment-rest-api.md)
* [Configurar chaves geridas pelo cliente para encriptar dados em repouso para ISEs](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Aplicativos lógicos, gatilhos incorporados, ações incorporadas e conectores que funcionam no seu ISE usam um plano de preços diferente do plano de preços baseado no consumo. Para saber como funcionam os preços e a faturação para os ISEs, consulte o modelo de preços das [Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para preços, consulte [preços de Apps Lógicas.](../logic-apps/logic-apps-pricing.md)

* Uma [rede virtual Azure.](../virtual-network/virtual-networks-overview.md) Se não tiver uma rede virtual, aprenda a [criar uma rede virtual Azure.](../virtual-network/quick-create-portal.md)

  * A sua rede virtual precisa de ter quatro subredes *vazias* para criar e implantar recursos no seu ISE. Cada subnet suporta um componente de Aplicações Lógicas diferente que é usado no seu ISE. Pode criar estas subredes com antecedência, ou pode esperar até criar o ise onde pode criar subredes ao mesmo tempo. Saiba mais sobre [os requisitos da subnet](#create-subnet).

  * Os nomes da subnet precisam de começar com um carácter alfabético `%` `&`ou `\\` `?`um `/`sublinhado e não podem usar estes caracteres: `<`, `>`, , , , . 
  
  * Se quiser implementar o ISE através de um modelo de Gestor de Recursos Azure, certifique-se primeiro de que delega uma subnet vazia para a Microsoft.Logic/integrationServiceEnvironment. Não precisa fazer esta delegação quando se implanta através do portal Azure.

  * Certifique-se de que a sua rede virtual [permite o acesso ao ISE para](#enable-access) que o ISE possa funcionar corretamente e manter-se acessível.

  * Se utilizar o [ExpressRoute](../expressroute/expressroute-introduction.md), que fornece uma ligação privada aos serviços de nuvem da Microsoft que é facilitado pelo fornecedor de conectividade, deve [criar uma tabela](../virtual-network/manage-route-table.md) de rotas que tenha a seguinte rota e ligar essa tabela a cada subnet que é utilizada pelo seu ISE:

    **Nome**: <*nome da rota*><br>
    **Prefixo de endereço**: 0.0.0.0/0<br>
    **Próximo salto**: Internet

* Se pretender utilizar servidores DNS personalizados para a sua rede virtual Azure, [instale esses servidores seguindo estes passos](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) antes de implementar o seu ISE para a sua rede virtual. Para obter mais informações sobre a gestão das definições do servidor DNS, consulte [Criar, alterar ou eliminar uma rede virtual](../virtual-network/manage-virtual-network.md#change-dns-servers).

  > [!NOTE]
  > Se alterar as definições do servidor DNS ou do servidor DNS, tem de reiniciar o ISE para que o ISE possa recolher essas alterações. Para mais informações, consulte [Reiniciar o seu ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Ativar acesso para o ISE

Quando se utiliza um ISE com uma rede virtual Azure, um problema comum de configuração é ter uma ou mais portas bloqueadas. Os conectores que utiliza para criar ligações entre o ISE e os sistemas de destino também podem ter os seus próprios requisitos portuários. Por exemplo, se comunicar com um sistema FTP utilizando o conector FTP, a porta que utiliza no seu sistema FTP precisa de estar disponível, por exemplo, na porta 21 para o envio de comandos.

Para garantir que o seu ISE está acessível e que as aplicações lógicas em que o ISE podem comunicar através de cada subnet na sua rede virtual, [abram as portas descritas nesta tabela para cada subnet](#network-ports-for-ise). Se as portas necessárias não estiverem disponíveis, o ISE não funcionará corretamente.

* Se tiver várias instâncias ISE que necessitem de acesso a outros pontos finais que tenham restrições IP, coloque um [Firewall Azure](../firewall/overview.md) ou um [aparelho virtual](../virtual-network/virtual-networks-overview.md#filter-network-traffic) de rede na sua rede virtual e encaminhe o tráfego de saída através dessa firewall ou do aparelho virtual da rede. Em seguida, pode [configurar um único endereço IP, outbound, público, estático e previsível](connect-virtual-network-vnet-set-up-single-ip-address.md) que todos os casos ise na sua rede virtual podem usar para comunicar com sistemas de destino. Dessa forma, não é preciso configurar aberturas adicionais de firewall nesses sistemas de destino para cada ISE.

   > [!NOTE]
   > Pode utilizar esta abordagem para um único ISE quando o seu cenário requer limitar o número de endereços IP que precisam de acesso. Considere se os custos adicionais para a firewall ou o aparelho de rede virtual fazem sentido para o seu cenário. Saiba mais sobre [os preços da Firewall Azure.](https://azure.microsoft.com/pricing/details/azure-firewall/)

* Se criou uma nova rede virtual Azure e subnets sem quaisquer restrições, não precisa de configurar grupos de segurança de [rede (NSGs)](../virtual-network/security-overview.md#network-security-groups) na sua rede virtual para controlar o tráfego através de subredes.

* Numa rede virtual existente, pode configurar *opcionalmente* NSGs [filtrando o tráfego](../virtual-network/tutorial-filter-network-traffic.md)da rede através de subredes . Se quiser seguir esta rota, ou se já estiver a utilizar NSGs, certifique-se de que [abre as portas nesta tabela](#network-ports-for-ise) na rede virtual onde tem NSGs ou quer configurar NSGs.

  > [!NOTE]
  > Se utilizar as regras de [segurança do NSG,](../virtual-network/security-overview.md#security-rules)tem de utilizar *os* protocolos TCP e UDP. As regras de segurança do NSG descrevem as portas que deve abrir para os endereços IP que precisam de acesso a essas portas. Certifique-se de que quaisquer firewalls, routers ou outros itens que existam entre estes pontos finais também mantêm essas portas acessíveis a esses endereços IP.

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Portas de rede utilizadas pelo seu ISE

Esta tabela descreve as portas da sua rede virtual Azure que o seu ISE utiliza e onde essas portas são utilizadas. Para ajudar a reduzir a complexidade quando cria regras de segurança, as [etiquetas](../virtual-network/service-tags-overview.md) de serviço na tabela representam grupos de prefixos de endereçoIP para um serviço Azure específico.

> [!IMPORTANT]
> As portas de origem são efémeras, por isso `*` certifique-se de que as define para todas as regras. Sempre que se nota, ise interno e ISE externo referem-se ao [ponto final que é selecionado na criação do ISE.](connect-virtual-network-vnet-isolated-environment.md#create-environment) Para mais informações, consulte [o acesso ao Ponto Final](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). 

| Objetivo | Direção | Portas de destino | Etiqueta de serviço de origem | Etiqueta do serviço de destino | Notas |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Comunicação intersubnet dentro da sua rede virtual | Entrada & saída | * | O espaço de endereço para a rede virtual que tem as subnets do ISE | O espaço de endereço para a rede virtual que tem as subnets do ISE | Necessário para que o tráfego flua *entre* as subredes da sua rede virtual. <p><p>**Importante**: Para que o tráfego flua entre os *componentes* de cada sub-rede, certifique-se de que abre todas as portas dentro de cada sub-rede. |
| Comunicação à sua aplicação lógica | Entrada | 443 | ISE interno: <br>VirtualNetwork <p><p>ISE externa: <br>Internet <br>(ver coluna **de notas)** | VirtualNetwork | Em vez de utilizar a etiqueta do serviço de **Internet,** pode especificar o endereço IP de origem para o computador ou serviço que liga para quaisquer gatilhos de pedido ou webhooks na sua aplicação lógica. <p><p>**Importante**: Fechar ou bloquear esta porta impede chamadas http para aplicações lógicas que tenham gatilhos de pedido. |
| História de execução de aplicativológico | Entrada | 443 | ISE interno: <br>VirtualNetwork <p><p>ISE externa: <br>Internet <br>(ver coluna **de notas)** | VirtualNetwork | Em vez de utilizar a etiqueta do serviço de **Internet,** pode especificar o endereço IP de origem para o computador ou serviço de onde pretende ver o histórico de execução da sua aplicação lógica. <p><p>**Importante**: Embora fechar ou bloquear esta porta não o impeça de ver o histórico de execução, não pode ver as inputs e saídas para cada passo na história da execução. |
| Logic Apps Designer - propriedades dinâmicas | Entrada | 454 | LogicAppsManagement | VirtualNetwork | Os pedidos provêm dos endereços IP de acesso de ponto final de [acesso](../logic-apps/logic-apps-limits-and-config.md#inbound) às Aplicações Lógicas para aquela região. |
| Implantação do conector | Entrada | 454 | Ligadores Azure | VirtualNetwork | Necessário para a implantação e atualização dos conectores. Fechar ou bloquear esta porta faz com que as implementações do ISE falhem e impeça atualizações ou correções do conector. |
| Verificação de saúde da rede | Entrada | 454 | LogicApps | VirtualNetwork | Os pedidos provêm do ponto final de acesso das Aplicações Lógicas para endereços IP de [entrada](../logic-apps/logic-apps-limits-and-config.md#inbound) e [saída](../logic-apps/logic-apps-limits-and-config.md#outbound) para aquela região. |
| Dependência da Gestão de Serviços de Aplicações | Entrada | 454, 455 | Gestão de Serviços de Aplicação | VirtualNetwork | |
| Comunicação do Gestor de Tráfego Azure | Entrada | ISE Interno: 454 <p><p>ISE externa: 443 | AzureTrafficManager | VirtualNetwork | |
| Gestão API - ponto final de gestão | Entrada | 3443 | Gestão API | VirtualNetwork | |
| Implantação da política do conector | Entrada | 3443 | Gestão API | VirtualNetwork | Necessário para a implantação e atualização dos conectores. Fechar ou bloquear esta porta faz com que as implementações do ISE falhem e impeça atualizações ou correções do conector. |
| Comunicação da sua aplicação lógica | Saída | 80, 443 | VirtualNetwork | Varia com base no destino | Os pontos finais para o serviço externo com o qual a sua aplicação lógica precisa de comunicar. |
| Azure Active Directory | Saída | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Gestão de ligações | Saída | 443 | VirtualNetwork  | AppService | |
| Publicar registos de diagnóstico & métricas | Saída | 443 | VirtualNetwork  | AzureMonitor | |
| Dependência de Armazenamento Azure | Saída | 80, 443, 445 | VirtualNetwork | Armazenamento | |
| Dependência Azure SQL | Saída | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Saída | 1886 | VirtualNetwork | AzureMonitor | Necessário para a publicação do estado de saúde à Saúde dos Recursos |
| Dependência do Log para event hub política e agente de monitorização | Saída | 5672 | VirtualNetwork | EventHub | |
| Access Azure Cache for Redis Instances between Role Instances | Entrada <br>Saída | 6379 - 6383 | VirtualNetwork | VirtualNetwork | Além disso, para que o ISE trabalhe com o Azure Cache for Redis, deve abrir estes portos de [saída e de entrada descritos no Azure Cache para Redis FAQ](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Criar o seu ISE

1. No [portal Azure,](https://portal.azure.com)na caixa de pesquisa `integration service environments` azure principal, introduza como filtro e selecione Ambientes de Serviço de **Integração**.

   ![Localizar e selecionar "Ambientes de Serviço de Integração"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. No painel ambientes de **integração,** selecione **Adicionar**.

   ![Localizar e selecionar "Ambientes de Serviço de Integração"](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Forneça estes detalhes para o seu ambiente e, em seguida, selecione **Review + criar,** por exemplo:

   ![Fornecer detalhes ambientais](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Subscrição** | Sim | <*Nome de assinatura Azure*> | A subscrição Azure para usar para o seu ambiente |
   | **Grupo de recursos** | Sim | <*Nome do grupo azure-recursos*> | Um novo ou já existente grupo de recursos Azure onde você quer criar o seu ambiente |
   | **Nome do ambiente do serviço de integração** | Sim | <*nome ambiente*> | O seu nome ISE, que pode conter apenas letras,`_`números,`.`hífenes (),`-`sublinha ( e períodos ). |
   | **Localização** | Sim | <*Região do centro de dados azure*> | A região do datacenter azure onde implantar o seu ambiente |
   | **SKU** | Sim | **Premium** ou **Desenvolvedor (Sem SLA)** | O ISE SKU para criar e usar. Para obter diferenças entre estas UsS, consulte [ISE SKUs](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Importante**: Esta opção está disponível apenas na criação do ISE e não pode ser alterada mais tarde. |
   | **Capacidade adicional** | Premium: <br>Sim <p><p>Desenvolvedor: <br>Não aplicável | Premium: <br>0 a 10 <p><p>Desenvolvedor: <br>Não aplicável | O número de unidades de processamento adicionais a utilizar para este recurso ISE. Para aumentar a capacidade após a criação, consulte [adicionar capacidade ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Ponto final de acesso** | Sim | **Interna** ou **Externa** | O tipo de pontos finais de acesso a utilizar para o seu ISE. Estes pontos finais determinam se o pedido ou webhook dispara em aplicações lógicas no seu ISE pode receber chamadas de fora da sua rede virtual. <p><p>A sua seleção também afeta a forma como pode ver e aceder a inputs e saídas na sua aplicação lógica corre o histórico. Para mais informações, consulte [o acesso ao ponto final do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Importante**: Esta opção está disponível apenas na criação do ISE e não pode ser alterada mais tarde. |
   | **Rede virtual** | Sim | <*Azure-virtual-network-name*> | A rede virtual Azure onde pretende injetar o seu ambiente para que as aplicações lógicas nesse ambiente possam aceder à sua rede virtual. Se não tiver uma rede, [crie primeiro uma rede virtual Azure](../virtual-network/quick-create-portal.md). <p><p>**Importante**: *Só* pode efetuar esta injeção quando criar o seu ISE. |
   | **Sub-redes** | Sim | <*lista de recursos subnet*> | Um ISE requer quatro subredes *vazias* para criar e implantar recursos no seu ambiente. Para criar cada sub-rede, [siga os passos por baixo desta tabela](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Criar sub-rede**

   Para criar e implementar recursos no seu ambiente, o seu ISE precisa de quatro subredes *vazias* que não são delegadas em nenhum serviço. Cada subnet suporta um componente de Aplicações Lógicas diferente que é usado no seu ISE. *Não pode* alterar estes endereços de sub-rede depois de criar o seu ambiente. Cada subnet a necessitar de satisfazer estes requisitos:

   * Tem um nome que começa com um carácter alfabético ou um sublinhado (sem `?` `/`números), e não usa estes caracteres: `<`, `>` `%`, `&`, `\\`, , .

   * Utiliza o formato de [encaminhamento inter-domínio de classe (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e um espaço de endereço classe B.

   * Utiliza pelo `/27` menos um no espaço de endereço porque cada sub-rede requer 32 endereços no *mínimo*. Por exemplo:

     * `10.0.0.0/28`tem apenas 16 endereços e é muito pequeno porque 2<sup>(32-28)</sup> é 2<sup>4</sup> ou 16.

     * `10.0.0.0/27`tem 32 endereços porque 2<sup>(32-27)</sup> é 2<sup>5</sup> ou 32.

     * `10.0.0.0/24`tem 256 endereços porque 2<sup>(32-24)</sup> é 2<sup>8</sup> ou 256. No entanto, mais endereços não oferecem benefícios adicionais.

     Para saber mais sobre o cálculo de endereços, consulte [os blocos CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se utilizar o [ExpressRoute,](../expressroute/expressroute-introduction.md)tem de [criar uma tabela](../virtual-network/manage-route-table.md) de rotas que tenha a seguinte rota e ligar essa tabela a cada subnet que seja utilizada pelo seu ISE:

     **Nome**: <*nome da rota*><br>
     **Prefixo de endereço**: 0.0.0.0/0<br>
     **Próximo salto**: Internet

   1. Na lista **de sub-redes,** selecione Gerir a **configuração da sub-rede**.

      ![Gerir a configuração da sub-rede](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. No painel **sub-redes,** selecione **Subnet**.

      ![Adicione quatro subredes vazias](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. No painel **adicionar sub-rede,** forneça esta informação.

      * **Nome**: O nome da sua sub-rede
      * **Gama de endereços (bloco CIDR)**: A gama da sua subnet na sua rede virtual e no formato CIDR

      ![Adicione detalhes da subnet](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Quando tiver terminado, selecione **OK**.

   1. Repita estes passos para mais três subredes.

      > [!NOTE]
      > Se as subredes que tenta criar não forem válidas, o portal Azure mostra uma mensagem, mas não bloqueia o seu progresso.

   Para obter mais informações sobre a criação de subredes, consulte [Adicionar uma subnet de rede virtual](../virtual-network/virtual-network-manage-subnet.md).

1. Depois do Azure validar com sucesso as suas informações ise, selecione **Create**, por exemplo:

   ![Após validação bem sucedida, selecione "Criar"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   O Azure começa a implantar o seu ambiente, que normalmente demora dentro de duas horas a terminar. Ocasionalmente, o destacamento pode demorar até quatro horas. Para verificar o estado de implementação, na sua barra de ferramentas Azure, selecione o ícone de notificações, que abre o painel de notificações.

   ![Verifique o estado de implantação](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se a implementação terminar com sucesso, o Azure mostra esta notificação:

   ![Implantação conseguiu](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Caso contrário, siga as instruções do portal Azure para a implantação de problemas.

   > [!NOTE]
   > Se a implementação falhar ou eliminar o ise, o Azure pode demorar até uma hora antes de libertar as suas subredes. Este atraso significa que pode ter de esperar antes de reutilizar as subredes noutro ISE.
   >
   > Se eliminar a sua rede virtual, o Azure geralmente demora até duas horas antes de libertar as suas subredes, mas esta operação pode demorar mais tempo. 
   > Ao apagar redes virtuais, certifique-se de que não há recursos ainda ligados. 
   > Ver [Eliminar rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Para ver o seu ambiente, selecione **Ir a recursos** se o Azure não for automaticamente ao seu ambiente após os acabamentos de implantação.

1. Para verificar a saúde da rede para o seu ISE, consulte Gerir o seu ambiente de serviço de [integração.](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

1. Para começar a criar aplicações lógicas e outros artefactos no seu ISE, consulte Adicionar recursos aos ambientes de [serviço de integração.](../logic-apps/add-artifacts-integration-service-environment-ise.md)

   > [!IMPORTANT]
   > Os conectores ISE geridos que ficam disponíveis depois de criar o seu ISE não aparecem automaticamente no conector no Logic App Designer. Antes de poder utilizar estes conectores ISE, tem de adicionar manualmente [esses conectores ao ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) para que apareçam no Logic App Designer.

## <a name="next-steps"></a>Passos seguintes

* [Adicionar recursos aos ambientes de serviçode integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerir ambientes do serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Saiba mais sobre [a Rede Virtual Azure](../virtual-network/virtual-networks-overview.md)
* Conheça a [integração de redes virtuais para serviços Azure](../virtual-network/virtual-network-for-azure-services.md)
