---
title: Utilização de serviços de equilíbrio de carga em Azure Microsoft Docs
description: 'Este tutorial mostra-lhe como criar um cenário utilizando o portfólio de equilíbrio de carga seletiva Azure: Traffic Manager, Application Gateway e Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: rohink
ms.openlocfilehash: 786b4141ed83121dce069b45ce7ddcd91bb3b00e
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84020800"
---
# <a name="using-load-balancing-services-in-azure"></a>Utilizar serviços de balanceamento de carga no Azure

## <a name="introduction"></a>Introdução

O Microsoft Azure fornece vários serviços para gerir a forma como o tráfego de rede é distribuído e a carregar equilibrada. Pode utilizar estes serviços individualmente ou combinar os seus métodos, dependendo das suas necessidades, para construir a solução ideal.

Neste tutorial, definimos primeiro um caso de uso do cliente e vemos como pode ser feito mais robusto e performante utilizando o seguinte portfólio de equilíbrio de carga seletiva Azure: Traffic Manager, Application Gateway e Load Balancer. Em seguida, fornecemos instruções passo a passo para criar uma implementação que é geograficamente redundante, distribui tráfego para VMs, e ajuda-o a gerir diferentes tipos de pedidos.

A nível conceptual, cada um destes serviços desempenha um papel distinto na hierarquia de equilíbrio de carga.

* **O Traffic Manager** fornece um equilíbrio global de carga dNS. Analisa os pedidos de DNS e responde com um ponto final saudável, de acordo com a política de encaminhamento que o cliente selecionou. As opções para métodos de encaminhamento são:
  * Encaminhamento de desempenho para enviar o solicitador para o ponto final mais próximo em termos de latência.
  * Encaminhamento prioritário para direcionar todo o tráfego para um ponto final, com outros pontos finais como backup.
  * Encaminhamento de robin redondo ponderado, que distribui o tráfego com base na ponderação atribuída a cada ponto final.
  * Encaminhamento baseado em geografia para distribuir o tráfego para os pontos finais da sua aplicação com base na localização geográfica do utilizador.
  * Encaminhamento baseado em subnet para distribuir o tráfego para os pontos finais da sua aplicação com base na subnet (intervalo de endereço IP) do utilizador.
  * Encaminhamento multi valor que lhe permite enviar endereços IP de mais de um ponto final de aplicação numa única resposta DNS.

  O cliente liga-se diretamente ao ponto final devolvido pelo Traffic Manager. O Azure Traffic Manager deteta quando um ponto final não é saudável e depois redireciona os clientes para outra instância saudável. Consulte a documentação do Gestor de [Tráfego do Azure](traffic-manager-overview.md) para saber mais sobre o serviço.
* **O Application Gateway** fornece o controlador de entrega de aplicações (ADC) como um serviço, oferecendo várias capacidades de equilíbrio de carga da Camada 7 para a sua aplicação. Permite que os clientes otimizem a produtividade da quinta web descarregando a rescisão intensiva de TLS por CPU para o gateway da aplicação. Outras capacidades de encaminhamento da Camada 7 incluem distribuição de rodada-robin do tráfego de entrada, afinidade de sessão baseada em cookies, encaminhamento baseado em caminhos de URL, e a capacidade de hospedar vários websites atrás de um único gateway de aplicação. O Gateway de aplicações pode ser configurado como um gateway virado para a Internet, um gateway apenas interno, ou uma combinação de ambos. Application Gateway é totalmente gerido, escalável e altamente disponível. Proporciona um conjunto avançado de capacidades de registo e diagnóstico, para uma melhor capacidade de gestão.
* **Load Balancer** é parte integrante da pilha Azure SDN, fornecendo serviços de equilíbrio de carga de baixa latência e de baixa latência para todos os protocolos UDP e TCP. Gere as ligações de entrada e saída. Pode configurar pontos finais públicos e internos com balanceamento de carga e definir regras para mapear as ligações de entrada para destinos do conjunto de back-end, com opções de pesquisa de estado de funcionamento TCP e HTTP para gerir a disponibilidade do serviço.

## <a name="scenario"></a>Cenário

Neste cenário de exemplo, utilizamos um website simples que serve dois tipos de conteúdo: imagens e páginas web dinamicamente renderizadas. O website deve ser geograficamente redundante, devendo servir os seus utilizadores a partir da localização mais próxima (mais baixa) dos mesmos. O desenvolvedor de aplicações decidiu que quaisquer URLs que correspondam ao padrão /imagens/* são servidos a partir de um conjunto dedicado de VMs que são diferentes do resto da quinta web.

Além disso, o pool de VM padrão que serve o conteúdo dinâmico precisa de falar com uma base de dados de back-end que está alojada num cluster de alta disponibilidade. Toda a implantação é criada através do Gestor de Recursos Azure.

A utilização do Traffic Manager, Application Gateway e Load Balancer permite que este website atinja estes objetivos de design:

* **Redundância multi-geo:** Se uma região cair, o Gestor de Tráfego encaminha o tráfego para a região mais próxima sem qualquer intervenção do proprietário da aplicação.
* **Latência reduzida**: Como o Traffic Manager direciona automaticamente o cliente para a região mais próxima, o cliente experimenta menor latência ao solicitar o conteúdo da página web.
* **Escalabilidade independente**: Como a carga de trabalho da aplicação web é separada por tipo de conteúdo, o proprietário da aplicação pode escalar as cargas de trabalho de pedido independentes umas das outras. Application Gateway garante que o tráfego é encaminhado para as piscinas certas com base nas regras especificadas e na saúde da aplicação.
* **Equilíbrio interno da carga**: Como o Balancer de carga está em frente ao cluster de alta disponibilidade, apenas o ponto final ativo e saudável para uma base de dados está exposto à aplicação. Além disso, um administrador de base de dados pode otimizar a carga de trabalho distribuindo réplicas ativas e passivas através do cluster independenteda mente frontal. O Load Balancer fornece ligações ao cluster de alta disponibilidade e garante que apenas bases de dados saudáveis recebem pedidos de ligação.

O diagrama seguinte mostra a arquitetura deste cenário:

![Diagrama da arquitetura de equilíbrio de carga](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Este exemplo é apenas uma das muitas configurações possíveis dos serviços de equilíbrio de carga que o Azure oferece. O Gestor de Tráfego, o Application Gateway e o Load Balancer podem ser misturados e combinados de melhor forma às suas necessidades de equilíbrio de carga. Por exemplo, se não for necessário descarregar TLS ou processar a Camada 7, o Balancer de carga pode ser utilizado no lugar do Gateway da Aplicação.

## <a name="setting-up-the-load-balancing-stack"></a>Configuração da pilha de equilíbrio de carga

### <a name="step-1-create-a-traffic-manager-profile"></a>Passo 1: Criar um perfil de Gestor de Tráfego

1. No portal Azure, clique em **Criar um**perfil de Gestor de Tráfego em Rede de recursos  >  **Networking**  >  **Traffic Manager profile**  >  **Criar**.
2. Introduza as seguintes informações básicas:

   * **Nome**: Dê ao seu perfil de Gestor de Tráfego um nome de prefixo DNS.
   * **Método de encaminhamento**: Selecione a política do método de encaminhamento de tráfego. Para obter mais informações sobre os métodos, consulte os métodos de [encaminhamento de tráfego do Gestor de Tráfego](traffic-manager-routing-methods.md).
   * **Subscrição**: Selecione a subscrição que contém o perfil.
   * **Grupo de recursos**: Selecione o grupo de recursos que contém o perfil. Pode ser um novo ou já existente grupo de recursos.
   * **Localização**do grupo de recursos : O serviço Traffic Manager é global e não está ligado a uma localização. No entanto, deve especificar uma região para o grupo onde residem os metadados associados ao perfil do Gestor de Tráfego. Esta localização não tem impacto na disponibilidade de tempo de execução do perfil.

3. Clique em **Criar** para gerar o perfil do Gestor de Tráfego.

   ![Lâmina "Criar Gestor de Tráfego"](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Passo 2: Criar os gateways de aplicação

1. No portal Azure, no painel esquerdo, clique em **Criar um**Gateway de  >  **Networking**  >  **Aplicação**de Rede de Recursos .
2. Introduza as seguintes informações básicas sobre o gateway da aplicação:

   * **Nome**: O nome do gateway da aplicação.
   * **Tamanho SKU**: O tamanho do gateway de aplicação, disponível como Pequeno, Médio ou Grande.
   * **Contagem de exemplos**: O número de ocorrências, um valor de 2 a 10.
   * **Grupo de recursos**: O grupo de recursos que detém a porta de entrada de aplicação. Pode ser um grupo de recursos existente ou um novo.
   * **Localização**: A região para o gateway de aplicação, que é a mesma localização que o grupo de recursos. A localização é importante, porque a rede virtual e o IP público devem estar no mesmo local que o portal.
3. Clique em **OK**.
4. Defina as configurações de rede virtual, subnet, IP frontal e ouvinte para o gateway da aplicação. Neste cenário, o endereço IP frontal é **Público**, que permite que seja adicionado como ponto final para o perfil do Gestor de Tráfego mais tarde.
5. Configure o ouvinte com uma das seguintes opções:
    * Se utilizar http, não há nada para configurar. Clique em **OK**.
    * Se utilizar HTTPS, é necessária uma configuração adicional. Consulte a Criação de um gateway de [aplicação,](../application-gateway/application-gateway-create-gateway-portal.md)a partir do passo 9. Quando tiver concluído a configuração, clique em **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Configure o encaminhamento de URL para gateways de aplicação

Quando você escolhe um pool de back-end, um gateway de aplicação configurado com uma regra baseada em caminhos toma um padrão de caminho do URL de pedido, além da distribuição de robin redondo. Neste cenário, estamos adicionando uma regra baseada no caminho para direcionar qualquer URL com "/imagens/ \* " para o conjunto de servidores de imagem. Para obter mais informações sobre a configuração do encaminhamento baseado em caminhos de URL para um gateway de aplicação, consulte criar [uma regra baseada em caminhos para um gateway de aplicação](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagrama de nível web de gateway de aplicação](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Do seu grupo de recursos, vá ao caso do portal de aplicação que criou na secção anterior.
2. Em **Definições,** selecione **backend pools**e, em seguida, selecione **Adicionar** para adicionar os VMs que pretende associar com as piscinas de back-end de nível web.
3. Insira o nome da piscina traseira e todos os endereços IP das máquinas que residem na piscina. Neste cenário, estamos a ligar duas piscinas de servidores de back-end de máquinas virtuais.

   ![Gateway de aplicação "Adicionar piscina de backend"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Em **Definições** do gateway da aplicação, selecione **Regras**, e, em seguida, clique no botão baseado **em Path** para adicionar uma regra.

   ![Botão de regras de gateway de aplicação "Path based"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Configure a regra fornecendo as seguintes informações.

   Definições básicas:

   + **Nome**: O nome amigável da regra que é acessível no portal.
   + **Ouvinte**: O ouvinte que é usado para a regra.
   + **Reserva de backend padrão**: A piscina traseira a utilizar com a regra padrão.
   + **Definições de PREdefinição HTTP**: As definições HTTP a utilizar com a regra predefinida.

   Regras baseadas em caminhos:

   + **Nome**: O nome amigável da regra baseada no caminho.
   + **Caminhos**: A regra do caminho que é utilizada para encaminhar o tráfego.
   + **Backend Pool**: A piscina traseira a ser usada com esta regra.
   + **DEFINIÇÃO HTTP**: As definições http a utilizar com esta regra.

   > [!IMPORTANT]
   > Caminhos: Caminhos válidos devem começar com "/". O wildcard \* " é permitido apenas no final. Exemplos válidos são /xyz, /xyz, \* ou /xyz/ \* .

   ![Lâmina de gateway de aplicação "Adicionar regra baseada no caminho"](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Passo 3: Adicionar gateways de aplicação aos pontos finais do Gestor de Tráfego

Neste cenário, o Gestor de Tráfego está ligado a gateways de aplicações (conforme configurado nas etapas anteriores) que residem em diferentes regiões. Agora que os gateways da aplicação estão configurados, o próximo passo é ligá-los ao perfil do Gestor de Tráfego.

1. Abra o seu perfil de Gestor de Tráfego. Para isso, procure no seu grupo de recursos ou procure o nome do perfil do Gestor de Tráfego de **Todos os Recursos**.
2. No painel esquerdo, selecione **Pontos Finais**, e, em seguida, clique em **Adicionar** um ponto final.

   ![O gestor de tráfego endpoints botão "Adicionar"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Criar um ponto final ao introduzir as seguintes informações:

   * **Tipo:** Selecione o tipo de ponto final para o equilíbrio de carga. Neste cenário, selecione ponto final do **Azure** porque estamos a ligá-lo às instâncias de gateway da aplicação que foram configuradas anteriormente.
   * **Nome**: Introduza o nome do ponto final.
   * Tipo de **recurso-alvo**: Selecione **o endereço IP público** e, em seguida, sob o recurso **Target,** selecione o IP público do gateway de aplicação que foi configurado anteriormente.

   ![Gestor de Tráfego "Adicionar ponto final"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Agora pode testar a sua configuração acedendo-a com o perfil DNS do seu Gestor de Tráfego (neste exemplo: TrafficManagerScenario.trafficmanager.net). Pode reenviar pedidos, trazer ou derrubar VMs e servidores web que foram criados em diferentes regiões e alterar as definições de perfil do Gestor de Tráfego para testar a sua configuração.

### <a name="step-4-create-a-load-balancer"></a>Passo 4: Criar um equilibrador de carga

Neste cenário, o Load Balancer distribui ligações do nível web para as bases de dados dentro de um cluster de alta disponibilidade.

Se o seu cluster de base de dados de alta disponibilidade estiver a utilizar o SQL Server AlwaysOn, consulte o [Configure um ou mais ouvintes](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) do Grupo Always On Availability para obter instruções passo a passo.

Para obter mais informações sobre a configuração de um equilibrador de carga interna, consulte [Criar um equilibrador](../load-balancer/load-balancer-get-started-ilb-arm-portal.md)de carga interno no portal Azure .

1. No portal Azure, no painel esquerdo, clique em **Criar um**equilíbrio de carga de rede de  >  **Networking**  >  **Load balancer**recursos.
2. Escolha um nome para o seu equilibrador de carga.
3. Desloque o **Tipo** para **Interno**e escolha a rede virtual e a sub-rede apropriadas para que o equilibrador de carga resida.
4. No âmbito **da atribuição de endereçoIP,** selecione **Dynamic** ou **Static**.
5. No **grupo Recursos,** escolha o grupo de recursos para o equilibrador de carga.
6. Em **Localização,** escolha a região adequada para o equilibrador de carga.
7. Clique em **Criar** para gerar o equilibrador de carga.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Ligue um nível de base de dados back-end ao equilibrador de carga

1. Do seu grupo de recursos, encontre o equilibrador de carga que foi criado nos passos anteriores.
2. Em **Definições,** clique em **piscinas backend**e, em seguida, clique em **Adicionar** para adicionar uma piscina de back-end.

   ![Balancer de carga "Adicione piscina de backend"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Insira o nome da piscina traseira.
4. Adicione máquinas individuais ou um conjunto de disponibilidade para a piscina traseira.

#### <a name="configure-a-probe"></a>Configure uma sonda

1. No seu equilibrador de carga, em **Definições,** selecione **Sondas**e, em seguida, clique em **Adicionar** uma sonda.

   ![Balancer "Adicionar sonda"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Insira o nome da sonda.
3. Selecione o **Protocolo** para a sonda. Para uma base de dados, pode querer uma sonda TCP em vez de uma sonda HTTP. Para saber mais sobre sondas de equilíbrio de carga, consulte as [sondas de equilíbrio de carga .](../load-balancer/load-balancer-custom-probe-overview.md)
4. Insira o **Porto** da sua base de dados para ser utilizado para aceder à sonda.
5. Em **intervalos,** especifique com que frequência sondar a aplicação.
6. Sob o **limiar não saudável,** especifique o número de falhas contínuas de sonda que devem ocorrer para que o VM traseiro seja considerado insalubre.
7. Clique **em OK** para criar a sonda.

#### <a name="configure-the-load-balancing-rules"></a>Configure as regras de equilíbrio de carga

1. Em **Definições** do seu equilibrador de carga, selecione regras de equilíbrio de **carga**e, em seguida, clique em **Adicionar** para criar uma regra.
2. Introduza o **nome** para a regra de equilíbrio de carga.
3. Escolha o **Endereço IP frontend** do equilibrador de carga, **Protocolo,** e **Porta**.
4. Sob **a porta Backend,** especifique a porta a utilizar na piscina traseira.
5. Selecione a **piscina Backend** e a **Sonda** que foram criadas nos passos anteriores para aplicar a regra.
6. Sob a persistência da **Sessão,** escolha como pretende que as sessões permaneçam.
7. Em **intervalos inativos,** especifique o número de minutos antes de um intervalo de tempo inativo.
8. Em **IP flutuante,** selecione **desativado** ou **ativado**.
9. Clique em **OK** para criar a regra.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Passo 5: Ligar VMs de nível web ao equilibrador de carga

Agora configuramos o endereço IP e a porta frontal do equilibrador de carga nas aplicações que estão a ser recorridas nos seus VMs de nível web para quaisquer ligações de base de dados. Esta configuração é específica das aplicações que funcionam nestes VMs. Para configurar o endereço IP de destino e a porta, consulte a documentação da aplicação. Para encontrar o endereço IP da extremidade frontal, no portal Azure, vá para o pool IP frontal nas **definições**do equilíbrio de carga .

![Painel de navegação "Frontend IP pool" do Balancer](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Próximos passos

* [Descrição Geral do Gestor de Tráfego](traffic-manager-overview.md)
* [Descrição geral do Gateway de Aplicação](../application-gateway/application-gateway-introduction.md)
* [Descrição Geral do Balanceador de Carga do Azure (Azure Load Balancer overview)](../load-balancer/load-balancer-overview.md)
