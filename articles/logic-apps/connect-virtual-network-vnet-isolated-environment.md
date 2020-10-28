---
title: Ligue-se a redes virtuais Azure com um ISE
description: Criar um ambiente de serviço de integração (ISE) que possa aceder a redes virtuais Azure (VNETs) a partir de Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 10/25/2020
ms.openlocfilehash: cf8ce541c069f78adbb138fa38e2efc506e095ea
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675190"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Conecte-se às redes virtuais Azure a partir de Azure Logic Apps utilizando um ambiente de serviço de integração (ISE)

Para cenários em que as suas aplicações lógicas e contas de integração precisam de acesso a uma [rede virtual Azure,](../virtual-network/virtual-networks-overview.md)crie um [ *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). O ISE é um ambiente dedicado que utiliza o armazenamento dedicado e outros recursos que são mantidos separados do serviço Logic Apps multi-inquilino “global”. Esta separação também reduz qualquer impacto que outros inquilinos do Azure possam ter no desempenho das suas apps. Um ISE também lhe proporciona os seus próprios endereços IP estáticos. Estes endereços IP são separados dos endereços IP estáticos que são partilhados pelas aplicações lógicas no serviço público, multi-inquilino.

Quando cria um ISE, o Azure *injeta* o ISE na sua rede virtual Azure, que depois implanta o serviço De aplicações lógicas na sua rede virtual. Quando criar uma aplicação lógica ou conta de integração, selecione o ISE como sua localização. A sua aplicação lógica ou conta de integração pode então aceder diretamente a recursos, como máquinas virtuais (VMs), servidores, sistemas e serviços, na sua rede virtual.

![Selecione ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Para que as aplicações lógicas e contas de integração funcionem em conjunto num ISE, ambos devem usar o *mesmo ISE que* a sua localização.

Um ISE tem limites aumentados na duração de execução, retenção de armazenamento, produção, pedidos HTTP e intervalos de resposta, tamanhos de mensagem e pedidos de conector personalizado. Para obter mais informações, consulte [Limites e configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md). Para saber mais sobre ises, consulte os [recursos da Rede Virtual Azure a partir de Aplicações Azure Logic.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

Este artigo mostra-lhe como completar estas tarefas utilizando o portal Azure:

* Ativar o acesso ao seu ISE.
* Crie o seu ISE.
* Adicione capacidade extra ao seu ISE.

Também pode criar um ISE utilizando o modelo de arranque rápido do [Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) da amostra ou utilizando a API de Apps Lógicas REST, incluindo a configuração de chaves geridas pelo cliente:

* [Criar um ambiente de serviço de integração (ISE) utilizando a API de Apps Lógicas REST](../logic-apps/create-integration-service-environment-rest-api.md)
* [Configurar chaves geridas pelo cliente para encriptar dados em repouso para ises](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma conta e subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Aplicações lógicas, gatilhos incorporados, ações incorporadas e conectores que funcionam no seu ISE usam um plano de preços diferente do plano de preços baseado no consumo. Para aprender como os preços e a faturação funcionam para as ISEs, consulte o [modelo de preços de Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Para taxas de preços, consulte [os preços das Aplicações Lógicas.](../logic-apps/logic-apps-pricing.md)

* Uma [rede virtual Azure.](../virtual-network/virtual-networks-overview.md) A sua rede virtual precisa de ter quatro sub-redes *vazias,* que são necessárias para criar e implantar recursos no seu ISE e são utilizadas por estes componentes internos e ocultos:

  * Cálculo de Apps Lógicas
  * Ambiente de Serviço de Aplicações Internas (conectores)
  * Gestão Interna da API (conectores)
  * Redis Interno para caching e desempenho
  
  Pode criar as sub-redes com antecedência ou esperar até criar o ise para que possa criar sub-redes ao mesmo tempo. No entanto, antes de criar as suas sub-redes, reveja os [requisitos da sub-rede](#create-subnet).

  > [!IMPORTANT]
  >
  > Não utilize os seguintes espaços de endereço IP para a sua rede virtual ou sub-redes porque não são resolúveis por Azure Logic Apps:<p>
  > 
  > * 0.0.0.0/8
  > * 100.64.0.0/10
  > * 127.0.0.0/8
  > * 168.63.129.16/32
  > * 169.254.169.254/32

  * Certifique-se de que a sua rede virtual [permite o acesso ao seu ISE para](#enable-access) que o seu ISE possa funcionar corretamente e permanecer acessível.

  * Se utilizar ou pretender utilizar o [ExpressRoute](../expressroute/expressroute-introduction.md) juntamente com [um túnel forçado,](../firewall/forced-tunneling.md)deve [criar uma tabela de rotas](../virtual-network/manage-route-table.md) com a seguinte rota específica e ligar a tabela de rotas a cada sub-rede que é utilizada pelo seu ISE:

    **Nome** : < *nome de rota*><br>
    **Prefixo do endereço** : 0.0.0.0/0<br>
    **Próximo salto** : Internet
    
    Esta tabela de rotas específica é necessária para que os componentes da Logic Apps possam comunicar com outros Serviços Azure dependentes, tais como Azure Storage e Azure SQL DB. Para obter mais informações sobre esta rota, consulte [o prefixo do endereço 0.0.0/0](../virtual-network/virtual-networks-udr-overview.md#default-route). Se não usar um túnel forçado com o ExpressRoute, não precisa desta tabela de rotas específica.
    
    O ExpressRoute permite-lhe estender as suas redes no local para a nuvem da Microsoft e ligar-se aos serviços de cloud da Microsoft através de uma ligação privada que é facilitada pelo fornecedor de conectividade. Especificamente, o ExpressRoute é uma rede privada virtual que liga o tráfego através de uma rede privada, em vez de através da internet pública. As suas aplicações lógicas podem ligar-se a recursos no local que estão na mesma rede virtual quando se conectam através do ExpressRoute ou de uma rede privada virtual.
   
  * Se utilizar um [aparelho virtual de rede (NVA)](../virtual-network/virtual-networks-udr-overview.md#user-defined), certifique-se de que não ativa a rescisão TLS/SSL ou altera o tráfego TLS/SSL de saída. Além disso, certifique-se de que não permite a inspeção do tráfego originário da sub-rede do ISE. Para obter mais informações, consulte [o encaminhamento de tráfego de rede virtual.](../virtual-network/virtual-networks-udr-overview.md)

  * Se pretender utilizar servidores DNS personalizados para a sua rede virtual Azure, [confiem desses servidores seguindo estes passos](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) antes de implementar o ISE na sua rede virtual. Para obter mais informações sobre a gestão das definições do servidor DNS, consulte [Criar, alterar ou eliminar uma rede virtual.](../virtual-network/manage-virtual-network.md#change-dns-servers)

    > [!NOTE]
    > Se alterar as definições do servidor DNS ou do servidor DNS, tem de reiniciar o ISE para que o ISE possa recolher essas alterações. Para mais informações, consulte [Reinício do seu ISE](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE).

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Ativar acesso para o ISE

Quando utiliza um ISE com uma rede virtual Azure, um problema comum de configuração é ter uma ou mais portas bloqueadas. Os conectores que utiliza para criar ligações entre o ise e os sistemas de destino também podem ter os seus próprios requisitos portuários. Por exemplo, se comunicar com um sistema FTP utilizando o conector FTP, a porta que utiliza no seu sistema FTP tem de estar disponível, por exemplo, a porta 21 para o envio de comandos.

Para garantir que o ise está acessível e que as aplicações lógicas em que o ISE pode comunicar em cada sub-rede da sua rede virtual, [abra as portas descritas nesta tabela para cada sub-rede](#network-ports-for-ise). Se alguma porta necessária não estiver disponível, o seu ISE não funcionará corretamente.

* Se tiver várias instâncias ISE que precisam de acesso a outros pontos finais que tenham restrições IP, coloque um [Azure Firewall](../firewall/overview.md) ou um [aparelho virtual](../virtual-network/virtual-networks-overview.md#filter-network-traffic) de rede na sua rede virtual e encaminhe o tráfego de saída através dessa firewall ou aparelho virtual de rede. Em seguida, pode [configurar um único endereço IP, de saída, público, estático e previsível](connect-virtual-network-vnet-set-up-single-ip-address.md) que todos os casos ise da sua rede virtual podem usar para comunicar com os sistemas de destino. Desta forma, não é preciso configurar aberturas de firewall adicionais nesses sistemas de destino para cada ISE.

   > [!NOTE]
   > Pode utilizar esta abordagem para um único ISE quando o seu cenário requer limitar o número de endereços IP que precisam de acesso. Considere se os custos adicionais para a firewall ou para o aparelho de rede virtual fazem sentido para o seu cenário. Saiba mais sobre [os preços do Azure Firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Se criou uma nova rede virtual Azure e sub-redes sem quaisquer constrangimentos, não precisa de configurar grupos de segurança de [rede (NSGs)](../virtual-network/network-security-groups-overview.md#network-security-groups) na sua rede virtual para controlar o tráfego através de sub-redes.

* Para uma rede virtual existente, pode configurar *opcionalmente* [grupos de segurança de rede (NSGs)](../virtual-network/network-security-groups-overview.md#network-security-groups) para [filtrar o tráfego de rede através de sub-redes](../virtual-network/tutorial-filter-network-traffic.md). Se quiser seguir esta rota, ou se já estiver a utilizar NSGs, certifique-se de que [abre as portas descritas nesta tabela](#network-ports-for-ise) para esses NSGs.

  Ao configurar [as regras de segurança NSG,](../virtual-network/network-security-groups-overview.md#security-rules)tem de utilizar *os* protocolos **TCP** e **UDP,** ou pode selecionar **Qualquer** em vez disso para que não tenha de criar regras separadas para cada protocolo. As regras de segurança NSG descrevem as portas que deve abrir para os endereços IP que precisam de acesso a essas portas. Certifique-se de que quaisquer firewalls, routers ou outros itens existentes entre estes pontos finais também mantêm essas portas acessíveis a esses endereços IP.

* Se configurar um túnel forçado através da sua firewall para redirecionar o tráfego ligado à Internet, reveja os [requisitos adicionais de túneis forçados](#forced-tunneling).

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Portas de rede utilizadas pelo ISE

Esta tabela descreve as portas que o seu ISE necessita para serem acessíveis e o propósito para essas portas. Para ajudar a reduzir a complexidade quando configura as regras de segurança, a tabela utiliza tags de serviço que representam [grupos](../virtual-network/service-tags-overview.md) de prefixos de endereço IP para um serviço Azure específico. Quando se nota, *o ISE interno* e o *ISE externo* referem-se ao ponto final de acesso selecionado durante a [criação do ISE.](connect-virtual-network-vnet-isolated-environment.md#create-environment) Para mais informações, consulte [o acesso ao Endpoint.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)

> [!IMPORTANT]
> Para todas as regras, certifique-se de que define as portas de origem porque as `*` portas de origem são efémeras.

#### <a name="inbound-security-rules"></a>Regras de segurança de entrada

| Objetivo | Etiqueta de serviço de origem ou endereços IP | Portas de origem | Etiqueta de serviço de destino ou endereços IP | Portas de destino | Notas |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Comunicação intersubnet dentro da rede virtual | Espaço de endereço para a rede virtual com sub-redes ISE | * | Espaço de endereço para a rede virtual com sub-redes ISE | * | É necessário que o tráfego flua *entre* as sub-redes da sua rede virtual. <p><p>**Importante** : Para que o tráfego flua entre os *componentes* de cada sub-rede, certifique-se de que abre todas as portas dentro de cada sub-rede. |
| Ambos: <p>Comunicação à sua aplicação lógica <p><p>Executa a história para app lógica| Ise interno: <br>**VirtualNetwork** <p><p>ISE Externo: **Internet** ou ver **Notas** | * | **VirtualNetwork** | 443 | Em vez de utilizar a etiqueta de serviço de **Internet,** pode especificar o endereço IP de origem para estes itens: <p><p>- O computador ou serviço que chama qualquer pedido de gatilhos ou webhooks na sua aplicação lógica <p>- O computador ou serviço de onde pretende aceder a aplicações lógicas executa o histórico <p><p>**Importante:** Fechar ou bloquear esta porta impede chamadas para aplicações lógicas que tenham gatilhos de pedido ou webhooks. Também está impedido de aceder a entradas e saídas para cada passo na história das corridas. No entanto, não está impedido de aceder a aplicações lógicas que executa o histórico.|
| Designer de Apps Lógicas - propriedades dinâmicas | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | Os pedidos vêm das Aplicações [Lógicas](../logic-apps/logic-apps-limits-and-config.md#inbound) que acedem aos endereços IP de entrada para aquela região. |
| Implantação do conector | **AzureConnectors** | * | **VirtualNetwork** | 454 | Necessário para implantar e atualizar conectores. Fechar ou bloquear esta porta faz com que as implementações do ISE falhem e impeça atualizações e correções do conector. |
| Verificação de saúde em rede | **LogicApps** | * | **VirtualNetwork** | 454 | Os pedidos vêm das Aplicações [Lógicas](../logic-apps/logic-apps-limits-and-config.md#inbound) que acedem aos endereços IP de entrada e [endereços IP de saída](../logic-apps/logic-apps-limits-and-config.md#outbound) para aquela região. |
| Dependência de Gestão de Serviços de Aplicações | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Comunicação do Gestor de Tráfego da Azure | **AzureTrafficManager** | * | **VirtualNetwork** | Ise Interno: 454 <p><p>ISE Externo: 443 ||
| Ambos: <p>Implementação da política do conector <p>API Management - ponto final de gestão | **APIManagement** | * | **VirtualNetwork** | 3443 | Para a implementação da política do conector, é necessário ter acesso à porta para implantar e atualizar os conectores. Fechar ou bloquear esta porta faz com que as implementações do ISE falhem e impeça atualizações e correções do conector. |
| Acesso Azure Cache para Redis Instances entre Instâncias de Papel | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383, mais ver **Notas**| Para que o ISE trabalhe com a Azure Cache para Redis, tem de abrir estas [portas de saída e de entrada descritas pela Cache Azure para redis FAQ](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
|||||||

#### <a name="outbound-security-rules"></a>Regras de segurança de saída

| Objetivo | Etiqueta de serviço de origem ou endereços IP | Portas de origem | Etiqueta de serviço de destino ou endereços IP | Portas de destino | Notas |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Comunicação intersubnet dentro da rede virtual | Espaço de endereço para a rede virtual com sub-redes ISE | * | Espaço de endereço para a rede virtual com sub-redes ISE | * | É necessário que o tráfego flua *entre* as sub-redes da sua rede virtual. <p><p>**Importante** : Para que o tráfego flua entre os *componentes* de cada sub-rede, certifique-se de que abre todas as portas dentro de cada sub-rede. |
| Comunicação a partir da sua app lógica | **VirtualNetwork** | * | Varia com base no destino | 80, 443 | O destino varia em função dos pontos finais para o serviço externo com o qual a sua aplicação lógica precisa de comunicar. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Dependência de armazenamento Azure | **VirtualNetwork** | * | **Armazenamento** | 80, 443, 445 ||
| Gestão de conexão | **VirtualNetwork** | * | **AppService** | 443 ||
| Publicar registos de diagnóstico & métricas | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Dependência Azure SQL | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Necessário para a publicação do estado de saúde à Resource Health. |
| Dependência da política de Log para Event Hub e agente de monitorização | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Acesso Azure Cache para Redis Instances entre Instâncias de Papel | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383, mais ver **Notas**| Para que o ISE trabalhe com a Azure Cache para Redis, tem de abrir estas [portas de saída e de entrada descritas pela Cache Azure para redis FAQ](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Resolução de nomes DNS | **VirtualNetwork** | * | Endereços IP para quaisquer servidores personalizados do Sistema de Nome de Domínio (DNS) na sua rede virtual | 53 | Requerido apenas quando utiliza servidores DNS personalizados na sua rede virtual |
|||||||

Além disso, você precisa adicionar regras de saída para o Ambiente de Serviço de [Aplicações (ASE)](../app-service/environment/intro.md):

* Se utilizar o Azure Firewall, tem de configurar a sua firewall com a [etiqueta de domínio (ASE) totalmente qualificada (FQDN),](../firewall/fqdn-tags.md#current-fqdn-tags)que permite o acesso de saída ao tráfego da plataforma ASE.

* Se utilizar um aparelho de firewall que não seja o Azure Firewall, tem de configurar a sua firewall com *todas as* [regras listadas](../app-service/environment/firewall-integration.md#dependencies) nas dependências de integração de firewall que são necessárias para o Ambiente de Serviço de Aplicações.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Requisitos de túneis forçados

Se configurar ou utilizar [um túnel forçado](../firewall/forced-tunneling.md) através da sua firewall, tem de permitir dependências externas adicionais para o seu ISE. O túnel forçado permite redirecionar o tráfego ligado à Internet para um próximo lúpulo designado, como a sua rede privada virtual (VPN) ou para um aparelho virtual, em vez de para a Internet, para que possa inspecionar e auditar o tráfego da rede de saída.

Normalmente, todo o tráfego de dependência de saída do ISE viaja através do endereço IP virtual (VIP) que é a provisionado com o seu ISE. No entanto, se alterar o encaminhamento de tráfego de ou para o seu ISE, tem de permitir as seguintes dependências de saída na sua firewall, definindo o seu próximo salto para `Internet` . Se utilizar o Azure Firewall, siga as [instruções para configurar a sua firewall com o seu Ambiente de Serviço de Aplicações](../app-service/environment/firewall-integration.md#configuring-azure-firewall-with-your-ase).

Se não permitir o acesso a estas dependências, a sua implantação ise falha e o ise implantado deixa de funcionar:

* [Endereços de gestão de Ambiente de Serviço de Aplicações](../app-service/environment/management-addresses.md)

* [Endereços de gestão da API da Azure](../api-management/api-management-using-with-vnet.md#control-plane-ips)

* [Endereços de gestão do Gestor de Tráfego Azure](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)

* [Endereços de entrada e saída de Apps lógicas para a região do ISE](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)

* [Endereços IP Azure para conectores na região do ISE, que estão neste ficheiro de descarregamento](https://www.microsoft.com/download/details.aspx?id=56519)

* Você precisa ativar os pontos finais de serviço para Azure SQL, Storage, Service Bus e Event Hub porque você não pode enviar tráfego através de uma firewall para estes serviços.

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Criar o seu ISE

1. No [portal Azure,](https://portal.azure.com)na caixa de pesquisa principal do Azure, insira `integration service environments` como filtro e selecione **Ambientes de Serviço de Integração** .

   ![Localizar e selecionar "Ambientes de Serviço de Integração"](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. No painel **de ambientes de serviço de integração,** selecione **Adicionar** .

   ![Selecione "Adicionar" para criar ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Forneça estes detalhes para o seu ambiente e, em seguida, selecione **Review + create** , por exemplo:

   ![Fornecer detalhes ambientais](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Subscrição** | Sim | <*Nome de subscrição Azure*> | A subscrição Azure para usar para o seu ambiente |
   | **Grupo de recursos** | Sim | <*Nome de grupo Azure-recursos*> | Um novo ou existente grupo de recursos Azure onde pretende criar o seu ambiente |
   | **Nome do ambiente do serviço de integração** | Sim | <*nome do ambiente*> | O seu nome ISE, que pode conter apenas letras, números, hífens `-` ( ), sublinha `_` (, e períodos ( `.` ). |
   | **Localização** | Sim | <*Azure-datacenter-região*> | A região do centro de dados Azure onde implementar o seu ambiente |
   | **SKU** | Sim | **Premium** ou **Developer (Sem SLA)** | O ISE SKU para criar e utilizar. Para obter diferenças entre estes SKUs, consulte [os SKUs ise.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) <p><p>**Importante:** Esta opção está disponível apenas na criação do ISE e não pode ser alterada mais tarde. |
   | **Capacidade adicional** | Premium: <br>Sim <p><p>Desenvolvedor: <br>Não aplicável | Premium: <br>0 a 10 <p><p>Desenvolvedor: <br>Não aplicável | O número de unidades de processamento adicionais a utilizar para este recurso ISE. Para aumentar a capacidade após a criação, consulte [a capacidade do Add ISE](../logic-apps/ise-manage-integration-service-environment.md#add-capacity). |
   | **Ponto final de acesso** | Sim | **Interno** ou **Externo** | O tipo de pontos finais de acesso a utilizar para o seu ISE. Estes pontos finais determinam se o pedido ou o webhook desencadeiam aplicações lógicas no seu ISE podem receber chamadas de fora da sua rede virtual. <p><p>A sua seleção também afeta a forma como pode ver e aceder a entradas e saídas na sua aplicação lógica. Para mais informações, consulte [o acesso ao ponto final do ISE.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access) <p><p>**Importante** : Pode selecionar o ponto final de acesso apenas durante a criação do ISE e não pode alterar esta opção mais tarde. |
   | **Rede virtual** | Sim | <*Nome de rede virtual Azure*> | A rede virtual Azure onde pretende injetar o seu ambiente para que as aplicações lógicas nesse ambiente possam aceder à sua rede virtual. Se não tiver uma rede, [crie primeiro uma rede virtual Azure](../virtual-network/quick-create-portal.md). <p><p>**Importante** : *Só* pode efetuar esta injeção quando criar o ise. |
   | **Sub-redes** | Sim | <*lista de sub-recursos*> | Um ISE requer quatro sub-redes *vazias,* que são necessárias para criar e implantar recursos no seu ISE e são usadas por componentes de Aplicações Lógicas internas, tais como conectores e caching para desempenho. <p>**Importante** : Certifique-se de que [revê os requisitos da sub-rede antes de continuar com estes passos para criar as suas sub-redes](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Criar sub-redes**

   O seu ISE necessita de quatro sub-redes *vazias,* que são necessárias para criar e implantar recursos no seu ISE e são utilizadas por componentes internos da Logic Apps, tais como conectores e caching para desempenho. Não *pode* alterar estes endereços de sub-rede depois de criar o seu ambiente. Se criar e implementar o seu ISE através do portal Azure, certifique-se de que não delega estas sub-redes em quaisquer serviços Azure. No entanto, se criar e implementar o seu ISE através da API REST, Azure PowerShell ou um modelo de Gestor de Recursos Azure, tem de [delegar](../virtual-network/manage-subnet-delegation.md) uma sub-rede vazia para `Microsoft.integrationServiceEnvironment` . Para mais informações, consulte [Adicionar uma delegação de sub-redes.](../virtual-network/manage-subnet-delegation.md)

   Cada sub-rede tem de satisfazer estes requisitos:

   * Usa um nome que começa com um carácter alfabético ou um sublinhado (sem números), e não usa estes caracteres: . . . . . . . . . . . . . `<` . . . . . . . . . . . . . . . . . . . . . . . . . . . . `>` . . . . `%` `&` `\\` `?` `/` . . . . . . . . . . . . . . . . . . . . . . . . . . . . . .

   * Utiliza o [formato de encaminhamento de Inter-Domain (CIDR) sem classe](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e um espaço de endereço classe B.
   
     > [!IMPORTANT]
     >
     > Não utilize os seguintes espaços de endereço IP para a sua rede virtual ou sub-redes porque não são resolúveis por Azure Logic Apps:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * Usa um `/27` no espaço de endereço porque cada sub-rede requer 32 endereços. Por exemplo, `10.0.0.0/27` tem 32 endereços porque 2<sup>(32-27)</sup> é 2<sup>5</sup> ou 32. Mais endereços não proporcionarão benefícios adicionais. Para saber mais sobre o cálculo de endereços, consulte [os blocos CIDR IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se utilizar [o ExpressRoute,](../expressroute/expressroute-introduction.md)tem de [criar uma tabela](../virtual-network/manage-route-table.md) de rotas que tenha a seguinte rota e ligue essa tabela a cada sub-rede que é utilizada pelo seu ISE:

     **Nome** : < *nome de rota*><br>
     **Prefixo do endereço** : 0.0.0.0/0<br>
     **Próximo salto** : Internet

   1. Na lista **de sub-redes,** selecione **Gerir a configuração da sub-rede** .

      ![Gerir a configuração da sub-rede](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. No painel **sub-redes,** selecione **Sub-rede** .

      ![Adicione quatro sub-redes vazias](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. No painel **de sub-redes Add,** forneça estas informações.

      * **Nome** : O nome da sua sub-rede
      * **Intervalo de endereços (bloco CIDR)** : A gama da sua sub-rede na sua rede virtual e no formato CIDR

      ![Adicionar detalhes da sub-rede](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Quando tiver terminado, selecione **OK** .

   1. Repita estes passos para mais três sub-redes.

      > [!NOTE]
      > Se as sub-redes que tenta criar não forem válidas, o portal Azure mostra uma mensagem, mas não bloqueia o seu progresso.

   Para obter mais informações sobre a criação de sub-redes, consulte [Adicionar uma sub-rede de rede virtual.](../virtual-network/virtual-network-manage-subnet.md)

1. Depois de a Azure validar com sucesso as suas informações ISE, selecione **Criar,** por exemplo:

   ![Após validação bem sucedida, selecione "Criar"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   O Azure começa a implantar o seu ambiente, que normalmente demora dentro de duas horas para terminar. Ocasionalmente, a implantação pode demorar até quatro horas. Para verificar o estado de implementação, na sua barra de ferramentas Azure, selecione o ícone de notificações, que abre o painel de notificações.

   ![Verificar o estado da implementação](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se a implementação terminar com sucesso, a Azure mostra esta notificação:

   ![Implementação conseguiu](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Caso contrário, siga as instruções do portal Azure para a resolução de problemas.

   > [!NOTE]
   > Se a implementação falhar ou eliminar o ISE, o Azure pode demorar até uma hora, ou possivelmente mais, em casos raros, antes de lançar as suas sub-redes. Por isso, talvez tenhas de esperar antes de reutilizares essas sub-redes noutra ISE.
   >
   > Se eliminar a sua rede virtual, o Azure geralmente demora até duas horas antes de lançar as suas sub-redes, mas esta operação pode demorar mais tempo. 
   > Ao eliminar redes virtuais, certifique-se de que ainda não há recursos ligados. 
   > Ver [Eliminar rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Para visualizar o seu ambiente, selecione **Vá para o recurso** se o Azure não for automaticamente ao seu ambiente após o fim da implementação.

1. Para verificar a saúde da rede para o seu ISE, consulte [Gerir o seu ambiente de serviço de integração.](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

   > [!CAUTION]
   > Se a rede do ISE não for saudável, o Ambiente interno de Serviço de Aplicações (ASE) que é utilizado pelo ISE também pode tornar-se insalubre. Se o ASE não estiver saudável por mais de sete dias, o ASE está suspenso. Para resolver este estado, verifique a sua configuração de rede virtual. Resolva quaisquer problemas que encontre e, em seguida, reinicie o seu ISE. Caso contrário, após 90 dias, o ASE suspenso é eliminado e o seu ISE torna-se inutilizável. Por isso, certifique-se de manter o seu ISE saudável para permitir o tráfego necessário.
   > 
   > Para obter mais informações, veja estes tópicos:
   >
   > * [Visão geral do Serviço de Aplicações Azure](../app-service/overview-diagnostics.md)
   > * [Início de mensagem para ambiente de serviço de aplicações Azure](../app-service/environment/using-an-ase.md#logging)

1. Para começar a criar aplicações lógicas e outros artefactos no seu ISE, consulte [adicionar recursos aos ambientes de serviços de integração.](../logic-apps/add-artifacts-integration-service-environment-ise.md)

   > [!IMPORTANT]
   > Depois de criar o ise, os conectores ISE geridos ficam disponíveis para você usar, mas eles não aparecem automaticamente no conector no Design de Aplicações Lógicas. Antes de poder utilizar estes conectores ISE, tem de adicionar e implantar manualmente [estes conectores no seu ISE para](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) que estes apareçam no Logic App Designer.

## <a name="next-steps"></a>Passos seguintes

* [Adicionar recursos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Gerir ambientes do serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Saiba mais sobre [a Rede Virtual Azure](../virtual-network/virtual-networks-overview.md)
* Conheça a [integração de redes virtuais para serviços Azure](../virtual-network/virtual-network-for-azure-services.md)
