---
title: Conectar-se a redes virtuais do Azure com um ISE-aplicativos lógicos do Azure
description: Criar um ISE (ambiente do serviço de integração) que possa acessar redes virtuais do Azure (VNETs) de aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: 9adc8b3f96847c346a59905d1a5ec145fadd2f5b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888710"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Conectar-se a redes virtuais do Azure de aplicativos lógicos do Azure usando um ISE (ambiente do serviço de integração)

Para cenários em que seus aplicativos lógicos e contas de integração precisam de acesso a uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md), crie um [ISE ( *ambiente do serviço de integração* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Um ISE é um ambiente privado e isolado que usa armazenamento dedicado e outros recursos que são mantidos separados do serviço de aplicativos lógicos públicos ou "globais". Essa separação também reduz qualquer impacto que outros locatários do Azure possam ter no desempenho de seus aplicativos.

Quando você cria um ISE, o Azure *injeta* esse ISE em sua rede virtual do Azure, que, em seguida, implanta o serviço de aplicativos lógicos em sua rede virtual. Ao criar um aplicativo lógico ou uma conta de integração, selecione o ISE como seu local. Seu aplicativo lógico ou conta de integração pode acessar diretamente os recursos, como VMs (máquinas virtuais), servidores, sistemas e serviços, em sua rede virtual.

![Selecione o ambiente do serviço de integração](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Para que os aplicativos lógicos e as contas de integração trabalhem juntos em um ISE, ambos devem usar o *mesmo ISE* como seu local.

Um ISE aumentou os limites de duração da execução, retenção de armazenamento, taxa de transferência, tempos limite de solicitação e resposta HTTP, tamanhos de mensagem e solicitações de conector personalizado. Para obter mais informações, consulte [limites e configuração para aplicativos lógicos do Azure](logic-apps-limits-and-config.md). Para saber mais sobre o ISEs, confira [acesso aos recursos de rede virtual do Azure de aplicativos lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

Este artigo mostra como concluir essas tarefas:

* Certifique-se de que todas as portas necessárias em sua rede virtual estejam abertas para que o tráfego possa percorrer o ISE entre as sub-redes nessa rede virtual.

* Crie seu ISE.

* Adicione capacidade extra ao ISE.

> [!IMPORTANT]
> Os aplicativos lógicos, os gatilhos internos, as ações internas e os conectores executados no ISE usam um plano de preços diferente do plano de preços baseado em consumo. Para saber como o preço e a cobrança funcionam para o ISEs, consulte o [modelo de preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md#fixed-pricing). Para obter taxas de preços, consulte [preços dos aplicativos lógicos](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure. Se não tiver uma subscrição do Azure, [inscreva-se para obter uma conta do Azure gratuita](https://azure.microsoft.com/free/).

* Uma [rede virtual do Azure](../virtual-network/virtual-networks-overview.md). Se você não tiver uma rede virtual, saiba como [criar uma rede virtual do Azure](../virtual-network/quick-create-portal.md). 

  * Sua rede virtual precisa ter quatro sub-redes *vazias* para criar e implantar recursos no ISE. Você pode criar essas sub-redes com antecedência, ou pode esperar até criar seu ISE, em que você pode criar sub-redes ao mesmo tempo. Saiba mais sobre [os requisitos de sub-rede](#create-subnet).

  * Os nomes de sub-rede precisam começar com um caractere alfabético ou um sublinhado e não podem usar estes caracteres: `<`, `>`, `%`, `&`, `\\`, `?`, `/`. 
  
  * Se você quiser implantar o ISE por meio de um modelo de Azure Resource Manager, primeiro certifique-se de delegar uma sub-rede vazia a Microsoft. Logic/integrationServiceEnvironment. Você não precisa fazer essa delegação ao implantar por meio do portal do Azure.

  * Certifique-se de que sua rede virtual disponibilize [essas portas](#ports) para que o ISE funcione corretamente e permaneça acessível.

  * Se você usar o [ExpressRoute](../expressroute/expressroute-introduction.md), que fornece uma conexão privada com os serviços de nuvem da Microsoft, deverá [criar uma tabela de rotas](../virtual-network/manage-route-table.md) que tenha a seguinte rota e vincular essa tabela a cada sub-rede usada pelo ISE:

    **Nome**: <*nome da rota*><br>
    **Prefixo de endereço**: 0.0.0.0/0<br>
    **Próximo salto**: Internet

* Se você quiser usar servidores DNS personalizados para sua rede virtual do Azure, [Configure esses servidores seguindo estas etapas](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) antes de implantar o ISE em sua rede virtual. Caso contrário, sempre que alterar o servidor DNS, você também precisará reiniciar o ISE.

  > [!IMPORTANT]
  > Se você alterar as configurações do servidor DNS depois de criar um ISE, certifique-se de reiniciar o ISE. Para obter mais informações sobre como gerenciar configurações do servidor DNS, consulte [criar, alterar ou excluir uma rede virtual](../virtual-network/manage-virtual-network.md#change-dns-servers).

<a name="ports"></a>

## <a name="check-network-ports"></a>Verificar portas de rede

Quando você usa um ISE com uma rede virtual do Azure, um problema de configuração comum é ter uma ou mais portas bloqueadas. Os conectores que você usa para criar conexões entre o ISE e o sistema de destino também podem ter seus próprios requisitos de porta. Por exemplo, se você se comunicar com um sistema FTP usando o conector de FTP, verifique se a porta que você usa no seu sistema FTP está disponível, por exemplo, a porta 21 para enviar comandos. Para garantir que o ISE permaneça acessível e possa funcionar corretamente, abra as portas especificadas pela tabela abaixo. Caso contrário, se qualquer porta necessária estiver indisponível, o ISE para de funcionar.

> [!IMPORTANT]
> As portas de origem são efêmeras, portanto, certifique-se de defini-las como `*` para todas as regras.
> Para a comunicação interna dentro de suas sub-redes, seu ISE exige que você abra todas as portas dentro dessas sub-redes.

* Se você tiver criado uma nova rede virtual e sub-redes sem nenhuma restrição, não será necessário configurar [NSGs (grupos de segurança de rede)](../virtual-network/security-overview.md#network-security-groups) em sua rede virtual para controlar o tráfego entre sub-redes.

* Em uma rede virtual existente, *opcionalmente* , você pode configurar o NSGs [filtrando o tráfego de rede entre sub-redes](../virtual-network/tutorial-filter-network-traffic.md). Se você escolher essa rota, na rede virtual em que deseja configurar o NSGs, abra as portas especificadas pela tabela a seguir. Se você usar [as regras de segurança do NSG](../virtual-network/security-overview.md#security-rules), precisará dos protocolos TCP e UDP.

* Se você já tiver NSGs ou firewalls existentes em sua rede virtual, certifique-se de abrir as portas especificadas pela tabela abaixo. Se você usar [as regras de segurança do NSG](../virtual-network/security-overview.md#security-rules), precisará dos protocolos TCP e UDP.

Aqui está a tabela que descreve as portas em sua rede virtual que o ISE usa e onde essas portas são usadas. As [marcas de serviço do Gerenciador de recursos](../virtual-network/security-overview.md#service-tags) representam um grupo de prefixos de endereço IP que ajudam a minimizar a complexidade ao criar regras de segurança.

| Objetivo | Direção | Portas de destino | Marca de serviço de origem | Etiqueta do serviço de destino | Notas |
|---------|-----------|-------------------|--------------------|-------------------------|-------|
| Comunicação de aplicativos lógicos do Azure | Saída | 80, 443 | VirtualNetwork | Internet | A porta depende do serviço externo com o qual o serviço de aplicativos lógicos se comunica |
| Azure Active Directory | Saída | 80, 443 | VirtualNetwork | AzureActiveDirectory | |
| Dependência de armazenamento do Azure | Saída | 80, 443 | VirtualNetwork | Storage | |
| Comunicação entre sub-redes | Saída de & de entrada | 80, 443 | VirtualNetwork | VirtualNetwork | Para comunicação entre sub-redes |
| Comunicação com o aplicativo lógico do Azure | Entrada | 443 | Pontos de extremidade de acesso interno: <br>VirtualNetwork <p><p>Pontos de extremidade de acesso externo: <br>Internet <p><p>**Observação**: esses pontos de extremidade referem-se à configuração de EndPoint que foi [selecionada na criação do ISE](#create-environment). Para obter mais informações, consulte [Endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | O endereço IP do computador ou serviço que chama qualquer gatilho de solicitação ou webhook que existe em seu aplicativo lógico. Fechar ou bloquear essa porta impede chamadas HTTP para aplicativos lógicos com gatilhos de solicitação. |
| Histórico de execução do aplicativo lógico | Entrada | 443 | Pontos de extremidade de acesso interno: <br>VirtualNetwork <p><p>Pontos de extremidade de acesso externo: <br>Internet <p><p>**Observação**: esses pontos de extremidade referem-se à configuração de EndPoint que foi [selecionada na criação do ISE](#create-environment). Para obter mais informações, consulte [Endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). | VirtualNetwork | O endereço IP do computador do qual você exibe o histórico de execução do aplicativo lógico. Embora o fechamento ou o bloqueio dessa porta não impeça que você exiba o histórico de execução, não é possível exibir as entradas e saídas de cada etapa nesse histórico de execução. |
| Gerenciamento de conexão | Saída | 443 | VirtualNetwork  | AppService | |
| Publicar logs de diagnóstico & métricas | Saída | 443 | VirtualNetwork  | AzureMonitor | |
| Comunicação do Gerenciador de tráfego do Azure | Entrada | 443 | AzureTrafficManager | VirtualNetwork | |
| Designer de aplicativos lógicos – propriedades dinâmicas | Entrada | 454 | Consulte a coluna observações para obter os endereços IP a serem permitidos | VirtualNetwork | As solicitações são provenientes dos endereços IP de [entrada](../logic-apps/logic-apps-limits-and-config.md#inbound) do ponto de extremidade de acesso dos aplicativos lógicos para essa região. |
| Verificação de integridade da rede | Saída de & de entrada | 454 | Consulte a coluna observações para obter os endereços IP a serem permitidos | VirtualNetwork | As solicitações são provenientes do ponto de extremidade de acesso dos aplicativos lógicos para endereços IP de [entrada](../logic-apps/logic-apps-limits-and-config.md#inbound) e [saída](../logic-apps/logic-apps-limits-and-config.md#outbound) para essa região. |
| Dependência de gerenciamento do serviço de aplicativo | Entrada | 454, 455 | AppServiceManagement | VirtualNetwork | |
| Implantação de conector | Entrada | 454 | AzureConnectors | VirtualNetwork | Necessário para implantar e atualizar conectores. Fechar ou bloquear essa porta faz com que implantações do ISE falhem e impeçam atualizações ou correções do conector. |
| Implantação de política de conector | Entrada | 3443 | Internet | VirtualNetwork | Necessário para implantar e atualizar conectores. Fechar ou bloquear essa porta faz com que implantações do ISE falhem e impeçam atualizações ou correções do conector. |
| Dependência do SQL do Azure | Saída | 1433 | VirtualNetwork | SQL | |
| Azure Resource Health | Saída | 1886 | VirtualNetwork | AzureMonitor | Para a publicação do status de integridade para Resource Health |
| Gerenciamento de API-ponto de extremidade de gerenciamento | Entrada | 3443 | APIManagement | VirtualNetwork | |
| Dependência de log para política do hub de eventos e agente de monitoramento | Saída | 5672 | VirtualNetwork | EventHub | |
| Acessar o cache do Azure para instâncias de Redis entre instâncias de função | Entrada <br>Saída | 6379-6383 | VirtualNetwork | VirtualNetwork | Além disso, para que o ISE funcione com o cache do Azure para Redis, você deve abrir essas [portas de saída e de entrada descritas no cache do Azure para perguntas frequentes do Redis](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| Azure Load Balancer | Entrada | * | AzureLoadBalancer | VirtualNetwork | |
||||||

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Criar seu ISE

Para criar seu ISE (ambiente do serviço de integração), siga estas etapas:

1. No [portal do Azure](https://portal.azure.com), no menu principal do Azure, selecione **criar um recurso**.
Na caixa de pesquisa, digite "ambiente do serviço de integração" como seu filtro.

   ![Criar novo recurso](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. No painel criação de Ambiente de Serviço de Integração, escolha **criar**.

   ![Escolha "criar"](./media/connect-virtual-network-vnet-isolated-environment/create-integration-service-environment.png)

1. Forneça esses detalhes para o seu ambiente e escolha **revisar + criar**, por exemplo:

   ![Fornecer detalhes do ambiente](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Propriedade | Necessário | Valor | Descrição |
   |----------|----------|-------|-------------|
   | **Subscrição** | Sim | <*Azure-subscription-name*> | A assinatura do Azure a ser usada para seu ambiente |
   | **Grupo de recursos** | Sim | <*Azure-Resource-Group-name*> | O grupo de recursos do Azure em que você deseja criar seu ambiente |
   | **Nome do ambiente do serviço de integração** | Sim | <*nome do ambiente*> | O nome do ISE, que pode conter apenas letras, números, hifens (`-`), sublinhados (`_`) e pontos (`.`). |
   | **Localização** | Sim | <*Azure-datacenter-região*> | A região do datacenter do Azure onde implantar seu ambiente |
   | **SKU** | Sim | **Premium** ou **desenvolvedor (sem SLA)** | A SKU do ISE a ser criada e usada. Para diferenças entre essas SKUs, consulte [SKUs do ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level). <p><p>**Importante**: essa opção está disponível somente na criação do ISE e não pode ser alterada posteriormente. |
   | **Capacidade adicional** | Especiais <br>Sim <p><p>Programador: <br>Não aplicável | Especiais <br>0 a 10 <p><p>Programador: <br>Não aplicável | O número de unidades de processamento adicionais a serem usadas para este recurso do ISE. Para adicionar capacidade após a criação, consulte [adicionar capacidade do ISE](#add-capacity). |
   | **Ponto de extremidade de acesso** | Sim | **Interno** ou **externo** | O tipo de pontos de extremidade de acesso a serem usados para o ISE, que determinam se os gatilhos de solicitação ou webhook em aplicativos lógicos no ISE podem receber chamadas de fora de sua rede virtual. O tipo de ponto de extremidade também afeta o acesso a entradas e saídas no histórico de execuções do aplicativo lógico. Para obter mais informações, consulte [Endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access). <p><p>**Importante**: essa opção está disponível somente na criação do ISE e não pode ser alterada posteriormente. |
   | **Rede virtual** | Sim | <*Azure-Virtual-Network-name*> | A rede virtual do Azure onde você deseja injetar seu ambiente para que os aplicativos lógicos nesse ambiente possam acessar sua rede virtual. Se você não tiver uma rede, [primeiro crie uma rede virtual do Azure](../virtual-network/quick-create-portal.md). <p>**Importante**: você *só* pode executar essa injeção ao criar o ISE. |
   | **Sub-redes** | Sim | <de *sub-rede – lista de recursos*> | Um ISE requer quatro sub-redes *vazias* para criar e implantar recursos em seu ambiente. Para criar cada sub-rede, [siga as etapas descritas nesta tabela](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Criar sub-rede**

   Para criar e implantar recursos em seu ambiente, o ISE precisa de quatro sub-redes *vazias* que não são delegadas a nenhum serviço. Você *não pode* alterar esses endereços de sub-rede depois de criar seu ambiente.
   
   > [!IMPORTANT]
   > 
   > Os nomes de sub-rede devem começar com um caractere alfabético ou um sublinhado (sem números) e não usam esses caracteres: `<`, `>`, `%`, `&`, `\\`, `?`, `/`.
   
   Além disso, cada sub-rede deve atender a esses requisitos:

   * Usa o [formato CIDR (roteamento entre domínios sem classificação)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) e um espaço de endereço da classe B.

   * Usa pelo menos um `/27` no espaço de endereço porque cada sub-rede deve ter *pelo menos* 32 endereços como um *mínimo*. Por exemplo:

     * `10.0.0.0/27` tem 32 endereços porque 2<sup>(32-27)</sup> é 2<sup>5</sup> ou 32.

     * `10.0.0.0/24` tem 256 endereços porque 2<sup>(32-24)</sup> é 2<sup>8</sup> ou 256.

     * `10.0.0.0/28` tem apenas 16 endereços e é muito pequeno porque 2<sup>(32-28)</sup> é 2<sup>4</sup> ou 16.

     Para saber mais sobre como calcular endereços, consulte [blocos CIDR de IPv4](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Se você usar o [ExpressRoute](../expressroute/expressroute-introduction.md), precisará [criar uma tabela de rotas](../virtual-network/manage-route-table.md) que tenha a rota a seguir e vincular essa tabela a cada sub-rede usada pelo ISE:

     **Nome**: <*nome da rota*><br>
     **Prefixo de endereço**: 0.0.0.0/0<br>
     **Próximo salto**: Internet

   1. Na lista **sub-redes** , escolha **gerenciar configuração de sub-rede**.

      ![Gerenciar configuração de sub-rede](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet.png)

   1. No painel **sub-redes** , escolha **sub-rede**.

      ![Adicionar sub-rede](./media/connect-virtual-network-vnet-isolated-environment/add-subnet.png)

   1. No painel **Adicionar sub-rede** , forneça essas informações.

      * **Nome**: o nome da sua sub-rede
      * **Intervalo de endereços (bloco CIDR)** : o intervalo da sua sub-rede em sua rede virtual e no formato CIDR

      ![Adicionar detalhes da sub-rede](./media/connect-virtual-network-vnet-isolated-environment/subnet-details.png)

   1. Quando terminar, escolha **OK**.

   1. Repita essas etapas para mais três sub-redes.

      > [!NOTE]
      > Se as sub-redes que você tentar criar não forem válidas, o portal do Azure mostrará uma mensagem, mas não bloqueará seu progresso.

   Para obter mais informações sobre como criar sub-redes, consulte [Adicionar uma sub-rede de rede virtual](../virtual-network/virtual-network-manage-subnet.md).

1. Depois que o Azure valida com êxito as informações do ISE, escolha **criar**, por exemplo:

   ![Após a validação bem-sucedida, escolha "criar"](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   O Azure inicia a implantação do seu ambiente, mas esse processo *pode* levar até duas horas antes de concluir. Para verificar o status da implantação, na barra de ferramentas do Azure, escolha o ícone notificações, que abre o painel notificações.

   ![Verificar status da implantação](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Se a implantação for concluída com êxito, o Azure mostrará esta notificação:

   ![Implantação bem-sucedida](./media/connect-virtual-network-vnet-isolated-environment/deployment-success.png)

   Caso contrário, siga as instruções de portal do Azure para solucionar problemas de implantação.

   > [!NOTE]
   > Se a implantação falhar ou você excluir o ISE, o Azure poderá levar até uma hora antes de liberar suas sub-redes. Esse atraso significa que você pode precisar esperar antes de reutilizar essas sub-redes em outro ISE.
   >
   > Se você excluir sua rede virtual, o Azure geralmente levará até duas horas antes de lançar suas sub-redes, mas essa operação poderá levar mais tempo. 
   > Ao excluir redes virtuais, certifique-se de que nenhum recurso ainda esteja conectado. 
   > Consulte [excluir rede virtual](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Para exibir seu ambiente, escolha **ir para o recurso** se o Azure não acessar automaticamente seu ambiente após a conclusão da implantação.

1. Para verificar a integridade da rede para o ISE, consulte [gerenciar o ambiente do serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health).

1. Para começar a criar aplicativos lógicos e outros artefatos em seu ISE, consulte [Adicionar artefatos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md).

<a name="add-capacity"></a>

## <a name="add-ise-capacity"></a>Adicionar capacidade do ISE

A unidade base do ISE Premium tem capacidade fixa, portanto, se você precisar de mais taxa de transferência, poderá adicionar mais unidades de escala, seja durante a criação ou depois. Você pode dimensionar automaticamente com base nas métricas de desempenho ou com base em várias unidades de processamento adicionais. Se você escolher dimensionamento automático com base nas métricas, poderá escolher entre vários critérios e especificar as condições de limite para atender a esses critérios. A SKU do desenvolvedor não inclui a capacidade de adicionar unidades de escala.

1. No portal do Azure, encontre o ISE.

1. Para examinar o uso e as métricas de desempenho do ISE, no menu principal do ISE, selecione **visão geral**.

   ![Exibir o uso do ISE](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-usage.png)

1. Para configurar o dimensionamento automático, em **configurações**, selecione **escalar horizontalmente**. Na guia **Configurar** , escolha **Habilitar dimensionamento automático**.

   ![Ativar dimensionamento automático](./media/connect-virtual-network-vnet-isolated-environment/scale-out.png)

1. Para **nome da configuração de dimensionamento automático**, forneça um nome para sua configuração.

1. Na seção **padrão** , escolha **escala com base em uma métrica** ou **escala para uma contagem de instâncias específica**.

   * Se você escolher baseado em instância, insira o número de unidades de processamento entre 0 e 10, inclusive.

   * Se você escolher baseado em métrica, siga estas etapas:

     1. Na seção **regras** , escolha **Adicionar uma regra**.

     1. No painel **regra de dimensionamento** , configure os critérios e a ação a serem tomadas quando a regra for disparada.

     1. Quando terminar, escolha **Adicionar**.

1. Quando tiver concluído as configurações de dimensionamento automático, salve as alterações.

## <a name="next-steps"></a>Passos seguintes

* [Adicionar artefatos a ambientes de serviço de integração](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Verificar a integridade da rede para ambientes do serviço de integração](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Saiba mais sobre a [rede virtual do Azure](../virtual-network/virtual-networks-overview.md)
* Saiba mais sobre [a integração de rede virtual para serviços do Azure](../virtual-network/virtual-network-for-azure-services.md)
