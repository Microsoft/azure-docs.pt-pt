---
title: Ligar a redes virtuais do Azure a partir do Azure Logic Apps, por meio de um ambiente de serviço de integração (ISE)
description: Criar um ambiente de serviço de integração (ISE) para que o logic apps e contas de integração podem aceder a redes virtuais do Azure (VNETs), enquanto se mantém isolado do Azure "global" ou público e privadas
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: b452485ccf235d1f245989e40840f2f0b3b2ae45
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544510"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Ligar a redes virtuais do Azure do Azure Logic Apps com um ambiente de serviço de integração (ISE)

Para cenários em que o logic apps e as contas de integração precisam de acesso a uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md), criar um [ *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Um ISE é um ambiente isolado e privado que utiliza armazenamento dedicado e outros recursos que sejam mantidos separados do serviço de aplicações lógicas "global" ou público. Essa separação também reduz a qualquer impacto que outros inquilinos do Azure podem ter sobre o desempenho das suas aplicações. O ISE encontra-se *injetado* para a sua rede virtual do Azure, que, em seguida, implementa o serviço de aplicações lógicas na sua rede virtual. Quando cria uma conta de integração ou da aplicação lógica, selecione esta ISE como a respetiva localização. Sua conta de integração ou da aplicação lógica, em seguida, pode aceder diretamente a recursos, como máquinas virtuais (VMs), servidores, sistemas e serviços, na sua rede virtual.

![Selecione o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

Este artigo mostra como concluir estas tarefas:

* Defina as portas de rede virtual do Azure para que o tráfego pode ser transferidos através de seu ambiente de serviço de integração (ISE) em sub-redes na rede virtual.

* Crie o seu ambiente de serviço de integração (ISE).

* Crie uma aplicação lógica que pode ser executados no seu ISE.

* Crie uma conta de integração para as aplicações lógicas no seu ISE.

Para obter mais informações sobre os ambientes de serviço de integração, consulte [acesso a recursos de rede Virtual do Azure a partir do Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">inscreva-se para obter uma conta do Azure gratuita</a>.

  > [!IMPORTANT]
  > Aplicações lógicas, acionadores incorporados, ações incorporadas e conectores que executam no seu uso ISE um plano de preços diferente do plano de preços baseado no consumo. Para obter mais informações, consulte [preços de aplicações lógicas](../logic-apps/logic-apps-pricing.md).

* Uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Se não tiver uma rede virtual, saiba como [criar uma Azure virtual network](../virtual-network/quick-create-portal.md). 

  * A rede virtual tem de ter quatro *vazio* sub-redes para a implantação e criação de recursos no seu ISE. Pode criar estas sub-redes com antecedência, ou pode aguardar até que criar seu ISE onde pode criar sub-redes, ao mesmo tempo. Saiba mais sobre [requisitos de sub-rede](#create-subnet). 
  
    > [!NOTE]
    > Se usar [ExpressRoute](../expressroute/expressroute-introduction.md), que fornece uma ligação privada com serviços cloud da Microsoft, deve [criar uma tabela de rotas](../virtual-network/manage-route-table.md) que tem o seguinte, encaminhar e ligar essa tabela com cada sub-rede utilizada pelo seu ISE:
    > 
    > **Nome**: <*nome da rota*><br>
    > **Prefixo de endereço**: 0.0.0.0/0<br>
    > **Do próximo salto**: Internet

  * Certifique-se de que a rede virtual [disponibiliza estas portas](#ports) para que seu ISE funciona corretamente e permanece acessível.

* Se pretender utilizar servidores DNS personalizados para a sua rede virtual do Azure, [configurar esses servidores ao seguir estes passos](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) antes de implementar seu ISE à sua rede virtual. Caso contrário, sempre que alterar o seu servidor DNS, também tem de reiniciar o ISE, que é um recurso que está disponível em pré-visualização pública do ISE.

* Conhecimento básico sobre [como criar aplicações lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="ports"></a>

## <a name="set-up-network-ports"></a>Configurar portas de rede

Para funcionar corretamente e mantenha-se acessível, o ambiente de serviço de integração (ISE) tem de ter as portas específicas disponíveis na sua rede virtual. Caso contrário, se qualquer uma destas portas não estão disponíveis, poderá perder o acesso ao seu ISE, que poderá parar de funcionar. Quando utiliza um ISE numa rede virtual, um problema de configuração comum é ter um ou mais portas bloqueadas. Para as ligações entre seu ISE e o sistema de destino, o conector que utiliza também pode ter seus próprios requisitos de porta. Por exemplo, se comunicar com um sistema FTP utilizando o conector FTP, certifique-se a porta a que utilizar em que o sistema FTP, como a porta 21 para o envio de comandos, está disponível.

Para controlar o tráfego entre sub-redes da rede virtual onde implementar seu ISE, pode configurar [grupos de segurança de rede](../virtual-network/security-overview.md) para as sub-redes por [filtragem de tráfego de rede em sub-redes](../virtual-network/tutorial-filter-network-traffic.md). Estas tabelas descrevem as portas na sua rede virtual que utiliza o ISE e onde se acostumar essas portas. O [etiquetas de serviço do Gestor de recursos](../virtual-network/security-overview.md#service-tags) representa um grupo de prefixos de endereços IP que o ajudam a minimizar a complexidade ao criar regras de segurança.

> [!IMPORTANT]
> Para a comunicação interna dentro as sub-redes, a ISE requer que abrir todas as portas dentro dessas sub-redes.

| Objetivo | Direction | Portas | Etiqueta de serviço de origem | Etiqueta do serviço de destino | Notas |
|---------|-----------|-------|--------------------|-------------------------|-------|
| Comunicação a partir do Azure Logic Apps | Saída | 80 & 443 | VirtualNetwork | Internet | A porta depende do serviço externo com o qual comunica o serviço de aplicações lógicas |
| Azure Active Directory | Saída | 80 & 443 | VirtualNetwork | AzureActiveDirectory | |
| Dependência de armazenamento do Azure | Saída | 80 & 443 | VirtualNetwork | Armazenamento | |
| Comunicação intersubnet | Entrada e saída | 80 & 443 | VirtualNetwork | VirtualNetwork | Para a comunicação entre sub-redes |
| Comunicação com o Azure Logic Apps | Entrada | 443 | Internet  | VirtualNetwork | O endereço IP para o computador ou serviço que chama qualquer acionador de pedido ou o webhook que existe na sua aplicação lógica. Fechar ou bloquear essa porta impede que as chamadas HTTP para aplicações lógicas com acionadores de pedido.  |
| Histórico de execução da aplicação lógica | Entrada | 443 | Internet  | VirtualNetwork | O endereço IP para o computador a partir da qual ver a aplicação lógica do histórico de execuções. Embora a fechar ou bloquear essa porta não impede que visualizar o histórico de execuções, não é possível ver as entradas e saídas de cada passo em que o histórico de execuções. |
| Gerenciamento de conexões | Saída | 443 | VirtualNetwork  | Internet | |
| Publicar os registos de diagnóstico e métricas | Saída | 443 | VirtualNetwork  | AzureMonitor | |
| Comunicação do Gestor de tráfego do Azure | Entrada | 443 | AzureTrafficManager | VirtualNetwork | |
| Estruturador de aplicações lógicas - propriedades dinâmicas | Entrada | 454 | Internet  | VirtualNetwork | Pedidos são provenientes do Logic Apps [aceder ao ponto final de entrada a endereços IP nessa região](../logic-apps/logic-apps-limits-and-config.md#inbound). |
| Dependência de aplicação do serviço de gestão | Entrada | 454 & 455 | AppServiceManagement | VirtualNetwork | |
| Implementação do conector | Entrada | 454 & 3443 | Internet  | VirtualNetwork | Necessário para implantar e atualizar os conectores. Fechar ou bloquear essa porta faz com que as implementações de ISE efetuar a ativação e impede que o conector atualizações ou correções. |
| Dependência SQL do Azure | Saída | 1433 | VirtualNetwork | SQL |
| Azure Resource Health | Saída | 1886 | VirtualNetwork | Internet | Para a publicação de estado de funcionamento para Estado de funcionamento do recurso |
| Gestão de API - ponto final de gestão | Entrada | 3443 | APIManagement  | VirtualNetwork | |
| Dependência do registo para a política do Hub de eventos e o agente de monitorização | Saída | 5672 | VirtualNetwork  | EventHub | |
| Aceder a Cache do Azure para instâncias de Redis entre instâncias de função | Entrada <br>Saída | 6379-6383 | VirtualNetwork  | VirtualNetwork | Além disso, para ISE trabalhar com o Azure Cache de Redis, tem de abrir estes [descritas no Redis perguntas frequentes sobre a Cache do Azure a portas de saída e entradas](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Balanceador de Carga do Azure | Entrada | * | AzureLoadBalancer | VirtualNetwork |  |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Criar seu ISE

Para criar o ambiente de serviço de integração (ISE), siga estes passos:

1. Na [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **criar um recurso**.
Na caixa de pesquisa, introduza "ambiente de serviço de integração" como o filtro.

   ![Criar novo recurso](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. No painel de criação de ambiente de serviço de integração, escolha **criar**.

   ![Escolha "Criar"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Forneça estes detalhes para o seu ambiente e, em seguida, escolha **rever + criar**, por exemplo:

   ![Fornecer detalhes do ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriedade | Necessário | Value | Descrição |
   |----------|----------|-------|-------------|
   | **Subscrição** | Sim | <*Azure-subscription-name*> | A subscrição do Azure a utilizar para o seu ambiente |
   | **Grupo de recursos** | Sim | <*Azure-resource-group-name*> | O grupo de recursos do Azure onde pretende criar o seu ambiente |
   | **Nome do ambiente de serviço de integração** | Sim | <*environment-name*> | O nome para dar o seu ambiente |
   | **Localização** | Sim | <*Azure-datacenter-region*> | A região do datacenter do Azure onde pretende implementar o seu ambiente |
   | **Capacidade adicional** | Sim | 0 a 10 | O número de unidades de processamento adicional para utilizar para este recurso ISE. Para adicionar capacidade após a criação, consulte [capacidade de adicionar ISE](#add-capacity). |
   | **Rede virtual** | Sim | <*Azure-virtual-network-name*> | A rede virtual do Azure em que deseja injetar o seu ambiente para que aplicações lógicas nesse ambiente podem acessar a rede virtual. Se não tiver uma rede, [primeiro, criar uma Azure virtual network](../virtual-network/quick-create-portal.md). <p>**Importante**: Pode *apenas* realizar este injeção quando cria seu ISE. |
   | **Sub-redes** | Sim | <*subnet-resource-list*> | Um ISE requer quatro *vazio* sub-redes para a criação de recursos no seu ambiente. Para criar cada sub-rede [siga os passos nesta tabela](#create-subnet).  |
   |||||

   <a name="create-subnet"></a>

   **Criar sub-rede**

   Para criar recursos no seu ambiente, a ISE tem quatro *vazio* sub-redes que não são delegados a qualquer serviço. 
   *Não é possível* alterar estes endereços de sub-rede depois de criar o seu ambiente. Cada sub-rede tem de cumprir estes critérios:

   * Tem um nome que começa com um caráter alfabético ou um caráter de sublinhado e não tem estes carateres: `<`, `>`, `%`, `&`, `\\`, `?`, `/`

   * Utiliza a [formato de encaminhamento de entre domínios Classless (CIDR)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e um espaço de endereços de classe B.

   * Utiliza a, pelo menos, um `/27` o endereço de espaço porque cada sub-rede tem de ter 32 endereços como o *mínimo*. Por exemplo:

     * `10.0.0.0/27` tem 32 endereços porque 2<sup>(32-27)</sup> é 2<sup>5</sup> ou 32.

     * `10.0.0.0/24` tem de 256 endereços porque 2<sup>(32-24)</sup> é 2<sup>8</sup> ou 256.

     * `10.0.0.0/28` tem apenas 16 endereços e é muito pequeno porque 2<sup>(32-28)</sup> é 2<sup>4</sup> ou 16.

     Para saber mais sobre o cálculo de endereços, veja [blocos CIDR de IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se usar [ExpressRoute](../expressroute/expressroute-introduction.md), não se esqueça [criar uma tabela de rotas](../virtual-network/manage-route-table.md) que tem o seguinte, encaminhar e ligar essa tabela com cada sub-rede utilizada pelo seu ISE:

     **Nome**: <*nome da rota*><br>
     **Prefixo de endereço**: 0.0.0.0/0<br>
     **Do próximo salto**: Internet

   1. Sob o **sub-redes** lista, escolha **configuração de sub-rede de gerir**.

      ![Gerir a configuração de sub-rede](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. Sobre o **sub-redes** painel, escolha **sub-rede**.

      ![Adicionar sub-rede ](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. Sobre o **adicionar sub-rede** painel, indique estas informações.

      * **Nome**: O nome para a sua sub-rede
      * **Intervalo de endereços (bloco CIDR)**: Intervalo da sua sub-rede na sua rede virtual e no formato CIDR

      ![Adicionar detalhes da sub-rede](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Quando tiver terminado, escolha **OK**.

   1. Repita estes passos para três sub-redes mais.

      > [!NOTE]
      > Se as sub-redes que tenta criar não são válidas, o portal do Azure mostra uma mensagem, mas não bloqueie o seu progresso.

1. Depois do Azure com êxito valida suas informações de ISE, escolha **criar**, por exemplo:

   ![Após a validação com êxito, escolha "Criar"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure começa a implantação de seu ambiente, mas esse processo *poderá* demorar até duas horas antes de terminar. 
   Para verificar o estado de implementação, na sua barra de ferramentas do Azure, selecione o ícone de notificações, que abre o painel de notificações.

   ![Verificar o estado de implementação](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se a conclusão da implementação, o Azure mostra esta notificação:

   ![Implementação efetuada com êxito](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Caso contrário, siga as instruções do portal do Azure para resolução de problemas de implementação.

   > [!NOTE]
   > Se falhar a implementação ou eliminar seu ISE, Azure, pode demorar até uma hora antes de lançar as sub-redes. Este atraso significa significa que pode ter de aguardar antes de reutilizar essas sub-redes no ISE do outro. 
   >
   > Se eliminar a rede virtual, o Azure normalmente demora até duas horas antes de liberar se as sub-redes, mas esta operação pode demorar mais tempo. 
   > Ao eliminar redes virtuais, certifique-se de que não existem recursos ainda estão ligados. Ver [Delete de rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Para ver o seu ambiente, escolha **Ir para recurso** se o Azure não passar automaticamente para o seu ambiente após a conclusão da implementação.  

Para obter mais informações sobre como criar sub-redes, veja [adicionar uma sub-rede de rede virtual](../virtual-network/virtual-network-manage-subnet.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-app---ise"></a>Criar aplicação lógica - ISE

Para criar aplicações lógicas que executar no seu ambiente de serviço de integração (ISE), [crie as aplicações lógicas como habitualmente](../logic-apps/quickstart-create-first-logic-app-workflow.md) , exceto quando definido o **localização** propriedade, selecione o ISE do  **Ambientes de serviço de integração** secção, por exemplo:

  ![Selecione o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/create-logic-app-with-integration-service-environment.png)

As diferenças no como acionadores e ações de trabalho e como eles estão rotulado como quando utiliza um ISE em comparação com o serviço de aplicações lógicas global, ver [isolado global na descrição geral do ISE](connect-virtual-network-vnet-isolated-environment-overview.md#difference).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-account---ise"></a>Criar conta de integração - ISE

Se pretender utilizar uma conta de integração com logic apps num ambiente de serviço de integração (ISE), essa conta de integração tem de utilizar o *mesmo ambiente* como as aplicações lógicas. Aplicações lógicas num ISE podem referenciar apenas as contas de integração no ISE do mesmo.

Para criar uma conta de integração que utiliza um ISE [criar a sua conta de integração da forma habitual](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , exceto quando definido o **localização** propriedade, selecione seu ISE do **integração ambientes de serviço** secção, por exemplo:

![Selecione o ambiente de serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/create-integration-account-with-integration-service-environment.png)

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Adicione a capacidade ISE

A unidade de base do ISE corrigiu capacidade, portanto, se precisar de mais débito, pode adicionar mais unidades de escala. Pode dimensionar automaticamente com base em métricas de desempenho ou com base num número de unidades de processamento adicional. Se escolher o dimensionamento automático com base em métricas, pode escolher de entre vários critérios e especifique as condições de limiar para atender esse critério.

1. No portal do Azure, encontre o ISE.

1. Para rever as métricas de utilização e desempenho para seu ISE, no menu principal do seu ISE, selecione **descrição geral**.

   ![Ver a utilização de ISE](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Para configurar o dimensionamento automático, em **configurações**, selecione **aumentar horizontalmente**. Sobre o **configurar** separador, escolha **ativar o dimensionamento automático**.

   ![Ativar o dimensionamento automático](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Para **nome da definição de dimensionamento automático**, forneça um nome para sua configuração.

1. Na **predefinido** secção, escolha o **dimensionam com base numa métrica** ou **Dimensionar para uma contagem de instâncias específica**.

   * Se escolher com base na instância, introduza o número de unidades de processamento entre 0 e 10, inclusivamente.

   * Se escolher com base na métrica, siga estes passos:

     1. Na **regras** secção, escolha **adicionar uma regra**.

     1. Sobre o **regra de dimensionamento** painel, configurar os critérios e a ação a tomar quando a regra for acionada.

     1. Quando tiver terminado, escolha **adicionar**.

1. Quando tiver terminado com as suas definições de dimensionamento automático, guarde as alterações.

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [rede Virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre [integração da rede virtual para serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
