---
title: Com os serviços de balanceamento de carga no Azure | Documentos da Microsoft
description: 'Este tutorial mostra-lhe como criar um cenário com o portfólio de balanceamento de carga do Azure: O Gestor de tráfego, o Gateway de aplicação e o Balanceador de carga.'
services: traffic-manager
documentationcenter: ''
author: liumichelle
manager: dkays
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: limichel
ms.openlocfilehash: 8c4d890633a8d588b51f876a361a77e6533b5db4
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57533156"
---
# <a name="using-load-balancing-services-in-azure"></a>Com os serviços de balanceamento de carga no Azure

## <a name="introduction"></a>Introdução

O Microsoft Azure fornece vários serviços para gerir a forma como o tráfego de rede é distribuído e com balanceamento de carga. Pode utilizar estes serviços individualmente ou combinar seus métodos, consoante as suas necessidades, para criar a solução ideal.

Neste tutorial, vamos primeiro definir um caso de utilização do cliente e ver como ele pode ser mais robusta e com bom desempenho, utilizando o seguinte portfólio de balanceamento de carga do Azure: O Gestor de tráfego, o Gateway de aplicação e o Balanceador de carga. Em seguida, podemos fornecer instruções passo a passo para a criação de uma implementação que está geograficamente redundante, distribui o tráfego para VMs e ajuda-o a gerenciam diferentes tipos de pedidos.

Num nível conceitual, cada um desses serviços desempenha uma função distinta na hierarquia de balanceamento de carga.

* **O Gestor de tráfego** fornece balanceamento de carga de global DNS. Ele examina os pedidos recebidos de DNS e responde com um ponto final em bom estado, de acordo com a política de encaminhamento, que o cliente tiver selecionado. Opções de métodos de encaminhamento são:
  * Encaminhamento para enviar o requerente para o ponto final mais próximo em termos de latência do desempenho.
  * Prioridade de encaminhamento para direcionar todo o tráfego para um ponto de extremidade, com outros pontos de extremidade como cópia de segurança.
  * Round robin ponderado roteamento, que distribui o tráfego com base no peso atribuído a cada ponto de extremidade.
  * Encaminhamento para distribuir o tráfego para os pontos de extremidade do aplicativo com base na geografia com base na localização geográfica do utilizador.
  * Encaminhamento para distribuir o tráfego para os pontos de extremidade do aplicativo com base na sub-rede com base na sub-rede (intervalo de endereços IP) do utilizador.
  * Valor de múltiplas encaminhamento que permitem-lhe enviar endereços IP dos pontos finais de mais do que um aplicativo numa única resposta DNS.

  O cliente liga-se diretamente para o ponto de extremidade devolvido pelo Gestor de tráfego. O Gestor de tráfego do Azure Deteta quando um ponto final está danificado e, em seguida, redireciona os clientes para outra instância de bom estado de funcionamento. Consulte a [documentação do Gestor de tráfego do Azure](traffic-manager-overview.md) para saber mais sobre o serviço.
* **Gateway de aplicação** fornece o controlador de entrega de aplicações (ADC) como uma oferta de serviço, vários grupos de recursos de balanceamento de carga de camada 7 para a sua aplicação. Ele permite que os clientes otimizem a produtividade do web farm ao descarregar a terminação de SSL intensiva da CPU para o gateway de aplicação. Outras capacidades de encaminhamento de camada 7 incluem a distribuição round robin de tráfego de entrada, afinidade por sessões com base no cookie, encaminhamento baseado no caminho URL e a capacidade de alojar vários Web sites atrás de um gateway de aplicação único. Gateway de aplicação pode ser configurado como um gateway de acesso à Internet, um gateway só interno ou uma combinação de ambos. Gateway de aplicação é totalmente do Azure gerida, dimensionável e de elevada disponibilidade. Proporciona um conjunto avançado de capacidades de registo e diagnóstico, para uma melhor capacidade de gestão.
* **Balanceador de carga** é uma parte integral da pilha SDN do Azure, fornecendo alto desempenho e de baixa latência camada 4 balanceamento de carga serviços para todos os protocolos UDP e TCP. Gere ligações de entrada e saídas. Pode configurar pontos finais públicos e internos com balanceamento de carga e definir regras para mapear as conexões de entrada para destinos do conjunto de back-end com opções de pesquisa de estado de funcionamento TCP e HTTP para gerir a disponibilidade do serviço.

## <a name="scenario"></a>Cenário

Neste cenário de exemplo, vamos utilizar um Web site simples que serve a dois tipos de conteúdo: imagens e páginas Web composta dinamicamente. O Web site deve ser geograficamente redundante e deve atender a seus usuários do mais próximo (latência mais baixa) localização aos mesmos. O desenvolvedor de aplicativos tenha decidido que todos os URLs que corresponde ao padrão/imagens / * são servidos a partir de um conjunto dedicado de VMs que são diferentes do restante da web farm.

Além disso, o conjunto VM predefinido, que serve o conteúdo dinâmico tem de comunicar com um banco de dados de back-end que está alojado num cluster de elevada disponibilidade. Toda a implantação é configurada através do Gestor de recursos do Azure.

Com o Gestor de tráfego, o Gateway de aplicação e o Balanceador de carga permite que este Web site alcançar essas metas de design:

* **Redundância geográfica várias**: Se uma região ficar inativo, o Gestor de tráfego encaminha o tráfego diretamente para a região mais próxima sem qualquer intervenção do proprietário do aplicativo.
* **Latência reduzida**: Uma vez que o Gestor de tráfego automaticamente direciona o cliente para a região mais próxima, o cliente experiências latência mais baixa quando solicitar o conteúdo da página da Web.
* **Escalabilidade independente**: Uma vez que a carga de trabalho de aplicação web é separada por tipo de conteúdo, o proprietário da aplicação pode dimensionar as cargas de trabalho de pedido independentes umas das outras. Gateway de aplicação garante que o tráfego é encaminhado para os conjuntos de certos com base em regras especificadas e o estado de funcionamento da aplicação.
* **Balanceamento de carga interno**: Como é Balanceador de carga à frente do cluster de elevada disponibilidade, apenas o Active Directory e bom estado de funcionamento ponto final de uma base de dados é exposto à aplicação. Além disso, um administrador de banco de dados pode otimizar a carga de trabalho ao distribuir as réplicas ativas e passivas no cluster independentemente da aplicação de front-end. Balanceador de carga fornece ligações para o cluster de elevada disponibilidade e garante que apenas bom estado de funcionamento bases de dados recebem pedidos de ligação.

O diagrama seguinte mostra a arquitetura de neste cenário:

![Diagrama de balanceamento de carga de arquitetura](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Neste exemplo é apenas uma das muitas configurações possíveis dos serviços de balanceamento de carga que o Azure oferece. O Gestor de tráfego, o Gateway de aplicação e o Balanceador de carga podem ser misturadas e correspondentes para se adequar melhor às suas necessidades de balanceamento de carga. Por exemplo, se a descarga de SSL ou não seja necessário processamento de camada 7, Balanceador de carga pode ser utilizado em vez do Gateway de aplicação.

## <a name="setting-up-the-load-balancing-stack"></a>Como configurar a pilha de balanceamento de carga

### <a name="step-1-create-a-traffic-manager-profile"></a>Passo 1: Criar um perfil do Gestor de Tráfego

1. No portal do Azure, clique em **criar um recurso** > **rede** > **perfil do Traffic Manager**  >   **Criar**.
2. Introduza as seguintes informações básicas:

  * **Nome**: Dar um DNS de perfil do Traffic Manager nome de prefixo.
  * **Método de encaminhamento**: Selecione a política de método de encaminhamento de tráfego. Para obter mais informações sobre os métodos, consulte [sobre o Gestor de tráfego de métodos de encaminhamento de tráfego](traffic-manager-routing-methods.md).
  * **Subscrição**: Selecione a subscrição que contém o perfil.
  * **Grupo de recursos**: Selecione o grupo de recursos que contém o perfil. Pode ser um grupo de recursos novo ou existente.
  * **Localização do grupo de recursos**: Serviço de Gestor de tráfego é global e não está vinculado a uma localização. No entanto, tem de especificar uma região para o grupo de onde os metadados associados ao perfil do Gestor de tráfego residem. Esta localização não tem impacto sobre a disponibilidade de tempo de execução do perfil.

3. Clique em **criar** para gerar o perfil do Gestor de tráfego.

  ![Painel "Criar o Gestor de tráfego"](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Passo 2: Criar gateways de aplicação

1. No portal do Azure, no painel esquerdo, clique em **criar um recurso** > **rede** > **Gateway de aplicação**.
2. Introduza as seguintes informações básicas sobre o gateway de aplicação:

  * **Nome**: O nome do gateway de aplicação.
  * **Tamanho SKU**: O tamanho do gateway de aplicação, disponível como pequena, média ou grande.
  * **Contagem de instâncias**: O número de instâncias, um valor de 2 a 10.
  * **Grupo de recursos**: O grupo de recursos que contém o gateway de aplicação. Pode ser um grupo de recursos existente ou um novo.
  * **Localização**: A região para o gateway de aplicação, o que é a mesma localização que o grupo de recursos. A localização é importante, porque a rede virtual e o IP público tem de ser na mesma localização que o gateway.
3. Clique em **OK**.
4. Defina a rede virtual, sub-rede, IP de front-end e configurações de serviço de escuta para o gateway de aplicação. Neste cenário, é o endereço IP Front-end **público**, permitindo-lhe ser adicionado como um ponto de extremidade mais tarde para o perfil do Gestor de tráfego.
5. Configure o serviço de escuta com uma das seguintes opções:
    * Se utilizar HTTP, não há nada para configurar. Clique em **OK**.
    * Se utilizar HTTPS, ainda mais configuração é necessária. Consulte a [criar um gateway de aplicação](../application-gateway/application-gateway-create-gateway-portal.md), a partir passo 9. Quando tiver concluído a configuração, clique em **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Configurar o encaminhamento do URL para gateways de aplicação

Ao escolher um conjunto de back-end, um gateway de aplicação que está configurado com uma regra baseada em caminho demora um padrão de caminho do URL do pedido, além de distribuição round-robin. Neste cenário, estamos a adicionar uma regra baseada em caminho para direcionar qualquer URL com "/images/\*" ao agrupamento de servidores de imagem. Para obter mais informações sobre como configurar o URL com base no caminho de encaminhamento para um gateway de aplicação, consulte [criar uma regra baseada em caminho para um gateway de aplicação](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagrama de camada de web de Gateway de aplicação](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. A partir do seu grupo de recursos, aceda à instância do gateway de aplicação que criou na secção anterior.
2. Sob **configurações**, selecione **conjuntos back-end**e, em seguida, selecione **Add** para adicionar as VMs que pretende associar os conjuntos de back-end de camada web.
3. Introduza o nome do conjunto de back-end e todos os endereços IP das máquinas que residem no conjunto. Neste cenário, estamos a ligar dois agrupamentos de servidores de back-end de máquinas virtuais.

  ![Gateway de aplicação "Adicionar conjunto de back-end"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Sob **definições** do gateway de aplicação, selecione **regras**e, em seguida, clique nas **baseado no caminho** botão para adicionar uma regra.

  ![Botão de "Baseado no caminho" regras de Gateway de aplicação](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Configure a regra, fornecendo as seguintes informações.

   Definições básicas:

   + **Nome**: O nome amigável da regra que está acessível no portal.
   + **Serviço de escuta**: O serviço de escuta é utilizado para a regra.
   + **Conjunto back-end de predefinido**: O conjunto de back-end para ser utilizado com a regra predefinida.
   + **Predefinições de HTTP**: As definições de HTTP a ser utilizado com a regra predefinida.

   Regras com base no caminho:

   + **Nome**: O nome amigável da regra com base no caminho.
   + **Caminhos**: A regra de caminho que é usada para encaminhamento de tráfego.
   + **Conjunto back-end**: O conjunto de back-end a utilizar com esta regra.
   + **Definição de HTTP**: As definições de HTTP a ser utilizada com esta regra.

   > [!IMPORTANT]
   > Caminhos: Caminhos válidos tem de começar com "/". O caráter universal "\*" é permitido apenas no final. Exemplos válidos são /xyz, /xyz\*, ou /xyz/\*.

   ![Painel de "Adicionar regra baseado no caminho" do Gateway de aplicação](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Passo 3: Adicionar os gateways de aplicação para os pontos finais do Gestor de tráfego

Neste cenário, o Gestor de tráfego está ligado aos gateways de aplicação (conforme configurado nos passos anteriores) que residem em diferentes regiões. Agora que os gateways de aplicação estão configurados, o próximo passo é ligá-los ao seu perfil do Gestor de tráfego.

1. Abra o perfil do Traffic Manager. Para tal, dar uma olhada no seu grupo de recursos ou procure o nome do perfil do Gestor de tráfego **todos os recursos**.
2. No painel esquerdo, selecione **pontos de extremidade**e, em seguida, clique em **Add** para adicionar um ponto final.

  ![Botão de Gestor de tráfego pontos finais "Adicionar"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Crie um ponto de extremidade ao introduzir as seguintes informações:

  * **Tipo de**: Selecione o tipo de ponto final de balanceamento de carga. Neste cenário, selecione **ponto final do Azure** porque estamos nos conectando-lo para as instâncias de gateway de aplicação que foram configuradas anteriormente.
  * **Nome**: Introduza o nome do ponto de extremidade.
  * **Tipo de recurso de destino**: Selecione **endereço IP público** e, em **recurso de destino**, selecione o IP público do gateway de aplicação que foi configurado anteriormente.

   ![Gestor de tráfego "Adicionar ponto final"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Agora pode testar a configuração acessando-o com o DNS do seu perfil do Gestor de tráfego (neste exemplo: TrafficManagerScenario.trafficmanager.net). Pode reenviar pedidos, abrir ou prejudicar a VMs e servidores web que foram criados em regiões diferentes e alterar as definições de perfil do Gestor de tráfego para testar a configuração.

### <a name="step-4-create-a-load-balancer"></a>Passo 4: Criar um balanceador de carga

Neste cenário, o Balanceador de carga distribui ligações a partir da camada web para as bases de dados dentro de um cluster de elevada disponibilidade.

Se o cluster de base de dados de elevada disponibilidade está a utilizar o AlwaysOn do SQL Server, consulte [configurar uma ou mais sempre no grupo de serviços de escuta disponibilidade](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) para obter instruções passo a passo.

Para obter mais informações sobre como configurar um balanceador de carga interno, consulte [criar um balanceador de carga interno no portal do Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. No portal do Azure, no painel esquerdo, clique em **criar um recurso** > **rede** > **Balanceador de carga**.
2. Escolha um nome para o Balanceador de carga.
3. Definir o **tipo** ao **interno**e escolha a rede virtual adequado e a sub-rede para o Balanceador de carga estar situada em.
4. Sob **atribuição de endereços IP**, selecione **dinâmico** ou **estático**.
5. Sob **grupo de recursos**, escolha o grupo de recursos para o Balanceador de carga.
6. Sob **localização**, escolha a região adequada para o Balanceador de carga.
7. Clique em **criar** para gerar o Balanceador de carga.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Ligar uma camada de banco de dados back-end para o Balanceador de carga

1. Do grupo de recursos, encontre o Balanceador de carga criado nos passos anteriores.
2. Sob **configurações**, clique em **conjuntos back-end**e, em seguida, clique em **Add** para adicionar um conjunto de back-end.

  ![Balanceador de carga "Adicionar conjunto de back-end"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Introduza o nome do conjunto de back-end.
4. Adicione máquinas individuais ou um conjunto de disponibilidade para o conjunto de back-end.

#### <a name="configure-a-probe"></a>Configurar uma sonda

1. No seu Balanceador de carga, sob **configurações**, selecione **sondas**e, em seguida, clique em **Add** para adicionar uma pesquisa.

 ![Balanceador de carga "Adicionar sonda"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Introduza o nome para a sonda.
3. Selecione o **protocolo** para a sonda. Para uma base de dados, poderá uma sonda TCP em vez de uma sonda HTTP. Para saber mais sobre as sondas do Balanceador de carga, veja [as sondas do Balanceador de carga compreender](../load-balancer/load-balancer-custom-probe-overview.md).
4. Introduza o **porta** da base de dados a ser utilizado para aceder à sonda.
5. Sob **intervalo**, especifique a frequência de sonda à aplicação.
6. Sob **limiar de mau estado de funcionamento**, especifique o número de falhas de sonda contínua que deve ocorrer para a VM de back-end ser considerado em mau estado de funcionamento.
7. Clique em **OK** para criar a sonda.

#### <a name="configure-the-load-balancing-rules"></a>Configurar as regras de balanceamento de carga

1. Sob **configurações** do seu Balanceador de carga, selecione **regras de balanceamento de carga**e, em seguida, clique em **Add** para criar uma regra.
2. Introduza o **nome** para a regra de balanceamento de carga.
3. Escolha o **endereço de IP de front-end** do Balanceador de carga **protocolo**, e **porta**.
4. Sob **porta de back-end**, especifique a porta a ser utilizada no conjunto de back-end.
5. Selecione o **conjunto back-end** e o **sonda** que foram criados nos passos anteriores para aplicar a regra.
6. Sob **persistência da sessão**, escolha como pretende que as sessões persistam.
7. Sob **tempos limite de inatividade**, especifique o número de minutos antes de um tempo limite de inatividade.
8. Sob **IP flutuante**, selecione **desativada** ou **ativado**.
9. Clique em **OK** para criar a regra.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Passo 5: Ligar VMs da camada web para o Balanceador de carga

Agora, podemos configurar a porta de front-end Balanceador de carga e endereços IP nas aplicações que estão em execução nas suas VMs de camada web para quaisquer ligações de base de dados. Esta configuração é específica para as aplicações que são executados nestas VMS. Para configurar o endereço IP de destino e a porta, consulte a documentação do aplicativo. Para localizar o endereço IP do front-end, no portal do Azure, vá para o conjunto IP Front-end no **definições do Balanceador de carga**.

![Painel de navegação de "Conjunto de IP de front-end" de Balanceador de carga](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Passos Seguintes

* [Descrição Geral do Gestor de Tráfego](traffic-manager-overview.md)
* [Descrição geral do Gateway de aplicação](../application-gateway/application-gateway-introduction.md)
* [Descrição Geral do Balanceador de Carga do Azure (Azure Load Balancer overview)](../load-balancer/load-balancer-overview.md)
