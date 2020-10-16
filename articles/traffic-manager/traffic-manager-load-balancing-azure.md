---
title: Utilização de serviços de equilíbrio de carga em Azure Microsoft Docs
description: 'Este tutorial mostra-lhe como criar um cenário utilizando o portfólio de equilíbrio de carga Azure: Traffic Manager, Application Gateway e Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: duongau
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: duau
ms.openlocfilehash: 431eaff9da95063648d3e80acb54be9cc5c25bc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89393073"
---
# <a name="using-load-balancing-services-in-azure"></a>Utilizar serviços de balanceamento de carga no Azure

## <a name="introduction"></a>Introdução

O Microsoft Azure fornece vários serviços para gerir a forma como o tráfego de rede é distribuído e a carga equilibrada. Pode utilizar estes serviços individualmente ou combinar os seus métodos, dependendo das suas necessidades, para construir a solução ideal.

Neste tutorial, primeiro definimos um caso de uso do cliente e vemos como pode ser tornado mais robusto e performante utilizando o seguinte portfólio de equilíbrio de carga Azure: Traffic Manager, Application Gateway e Load Balancer. Em seguida, fornecemos instruções passo a passo para a criação de uma implantação que seja geograficamente redundante, distribui tráfego para VMs, e ajuda-o a gerir diferentes tipos de pedidos.

A nível conceptual, cada um destes serviços desempenha um papel distinto na hierarquia do equilíbrio de cargas.

* **O Traffic Manager** proporciona um equilíbrio global de carga de DNS. Olha para os pedidos de DNS que chegam e responde com um ponto final saudável, de acordo com a política de encaminhamento que o cliente selecionou. As opções para os métodos de encaminhamento são:
  * Encaminhamento de desempenho para enviar o solicitador para o ponto final mais próximo em termos de latência.
  * Encaminhamento prioritário para direcionar todo o tráfego para um ponto final, com outros pontos finais como backup.
  * Encaminhamento de robin redondo ponderado, que distribui o tráfego com base na ponderação atribuída a cada ponto final.
  * Encaminhamento baseado em geografia para distribuir o tráfego para os pontos finais da sua aplicação com base na localização geográfica do utilizador.
  * Encaminhamento baseado em sub-rede para distribuir o tráfego para os pontos finais da sua aplicação com base na sub-rede (intervalo de endereços IP) do utilizador.
  * Encaminhamento multi valor que lhe permite enviar endereços IP de mais de um ponto final de aplicação numa única resposta DNS.

  O cliente liga-se diretamente ao ponto final devolvido pelo Traffic Manager. O Azure Traffic Manager deteta quando um ponto final não é saudável e, em seguida, redireciona os clientes para outro caso saudável. Consulte a [documentação do Azure Traffic Manager](traffic-manager-overview.md) para saber mais sobre o serviço.
* **O Application Gateway** fornece o controlador de entrega de aplicações (ADC) como um serviço, oferecendo várias capacidades de equilíbrio de carga da Camada 7 para a sua aplicação. Permite que os clientes otimizem a produtividade da web farm, descarregando a rescisão intensiva de TLS da CPU para o gateway de aplicações. Outras capacidades de encaminhamento da Camada 7 incluem distribuição de rodapé de tráfego de entrada, afinidade da sessão baseada em cookies, encaminhamento baseado em caminhos de URL e a capacidade de hospedar vários websites por trás de um único gateway de aplicação. O Gateway de Aplicações pode ser configurado como um portal virado para a Internet, um portal apenas interno, ou uma combinação de ambos. Application Gateway é totalmente gerido a Azure, escalável e altamente disponível. Proporciona um conjunto avançado de capacidades de registo e diagnóstico, para uma melhor capacidade de gestão.
* **O Balancer** de Carga é parte integrante da stack Azure SDN, fornecendo serviços de equilíbrio de carga de alta performance e baixa latência Camada 4 para todos os protocolos UDP e TCP. Gere ligações de entrada e saída. Pode configurar pontos finais públicos e internos com balanceamento de carga e definir regras para mapear as ligações de entrada para destinos do conjunto de back-end, com opções de pesquisa de estado de funcionamento TCP e HTTP para gerir a disponibilidade do serviço.

## <a name="scenario"></a>Cenário

Neste cenário de exemplo, usamos um site simples que serve dois tipos de conteúdo: imagens e páginas web dinamicamente renderizadas. O website deve ser geograficamente redundante, e deve servir os seus utilizadores a partir do local mais próximo (de menor latência) para eles. O desenvolvedor de aplicações decidiu que quaisquer URLs que correspondam ao padrão /imagens/* são servidos a partir de um conjunto dedicado de VMs que são diferentes do resto da fazenda web.

Além disso, o pool VM padrão que serve o conteúdo dinâmico precisa de falar com uma base de dados back-end que está hospedada num cluster de alta disponibilidade. Toda a implementação é configurada através do Azure Resource Manager.

A utilização de Gestor de Tráfego, Gateway de Aplicações e Balancer de Carga permite que este website atinja estes objetivos de design:

* **Redundância multi-geo:** Se uma região se abater, o Gestor de Tráfego encaminha o tráfego perfeitamente para a região mais próxima sem qualquer intervenção do proprietário da aplicação.
* **Latência reduzida**: Como o Gestor de Tráfego direciona automaticamente o cliente para a região mais próxima, o cliente experimenta uma menor latência ao solicitar o conteúdo da página web.
* **Escalabilidade independente**: Como a carga de trabalho da aplicação web é separada por tipo de conteúdo, o proprietário da aplicação pode escalar as cargas de trabalho do pedido independentemente umas das outras. A Application Gateway garante que o tráfego é encaminhado para as piscinas certas com base nas regras especificadas e na saúde da aplicação.
* **Equilíbrio interno da carga**: Como o Balanceador de Carga está em frente ao cluster de alta disponibilidade, apenas o ponto final ativo e saudável para uma base de dados está exposto à aplicação. Além disso, um administrador de base de dados pode otimizar a carga de trabalho distribuindo réplicas ativas e passivas através do cluster independente da aplicação frontal. O Balancer de Carga fornece ligações ao cluster de alta disponibilidade e garante que apenas bases de dados saudáveis recebem pedidos de ligação.

O seguinte diagrama mostra a arquitetura deste cenário:

![Diagrama de arquitetura de equilíbrio de carga](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Este exemplo é apenas uma das muitas configurações possíveis dos serviços de equilíbrio de carga que a Azure oferece. O Gestor de Tráfego, o Gateway de Aplicações e o Balancer de Carga podem ser misturados e combinados de forma melhor adequado às suas necessidades de equilíbrio de carga. Por exemplo, se o descarregamento de TLS ou o processamento da Camada 7 não forem necessários, o Balanceador de Carga pode ser utilizado no lugar do Gateway de Aplicação.

## <a name="setting-up-the-load-balancing-stack"></a>Configurar a pilha de equilíbrio de carga

### <a name="step-1-create-a-traffic-manager-profile"></a>Passo 1: Criar um perfil de Gestor de Tráfego

1. No portal Azure, clique em Criar um perfil de Gestor de Tráfego de Rede de **Recursos**  >  **Networking**  >  **Traffic Manager profile**  >  **Criar**.
2. Introduza as seguintes informações básicas:

   * **Nome**: Dê ao seu perfil de Gestor de Tráfego um nome de prefixo DNS.
   * **Método de encaminhamento**: Selecione a política do método de encaminhamento de tráfego. Para obter mais informações sobre os métodos, consulte sobre os [métodos de encaminhamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md).
   * **Assinatura**: Selecione a subscrição que contém o perfil.
   * **Grupo de recursos**: Selecione o grupo de recursos que contém o perfil. Pode ser um grupo de recursos novo ou existente.
   * **Localização do grupo de recursos**: O serviço de Gestor de Tráfego é global e não está ligado a uma localização. No entanto, deve especificar uma região para o grupo onde residem os metadados associados ao perfil de Gestor de Tráfego. Esta localização não tem impacto na disponibilidade de tempo de execução do perfil.

3. Clique em **Criar** para gerar o perfil de Gestor de Tráfego.

   ![Lâmina "Create Traffic Manager"](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Passo 2: Criar os gateways de aplicação

1. No portal Azure, no painel esquerdo, clique em **Criar um Gateway**  >  **de aplicação de rede de**recursos  >  **Application Gateway**.
2. Introduza as seguintes informações básicas sobre o gateway de aplicação:

   * **Nome**: O nome do gateway de aplicação.
   * **Tamanho SKU**: O tamanho do gateway de aplicação, disponível como Pequeno, Médio ou Grande.
   * **Contagem de exemplos**: O número de ocorrências, um valor de 2 a 10.
   * **Grupo de recursos**: O grupo de recursos que detém o gateway de aplicação. Pode ser um grupo de recursos existente ou um novo.
   * **Localização**: A região para o gateway de aplicação, que é o mesmo local que o grupo de recursos. A localização é importante, porque a rede virtual e o IP público devem estar no mesmo local que o gateway.
3. Clique em **OK**.
4. Defina as configurações de rede virtual, sub-rede, IP frontal e ouvinte para o gateway de aplicações. Neste cenário, o endereço IP frontal é **público**, o que permite a sua adição como ponto final ao perfil de Gestor de Tráfego mais tarde.
5. Configure o ouvinte com uma das seguintes opções:
    * Se utilizar HTTP, não há nada a configurar. Clique em **OK**.
    * Se utilizar HTTPS, é necessária uma configuração adicional. Consulte para [Criar um gateway de aplicação,](../application-gateway/application-gateway-create-gateway-portal.md)a partir do passo 9. Quando tiver concluído a configuração, clique em **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Configurar o encaminhamento de URL para gateways de aplicações

Quando você escolhe uma piscina de back-end, um gateway de aplicação configurado com uma regra baseada em caminho toma um padrão de caminho do URL de pedido, além da distribuição de robin redondo. Neste cenário, estamos adicionando uma regra baseada em caminho para direcionar qualquer URL com "/images/" \* para o conjunto do servidor de imagem. Para obter mais informações sobre a configuração do encaminhamento baseado em caminhos de URL para um gateway de aplicação, consulte [criar uma regra baseada em caminhos para um gateway de aplicações](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagrama de nível web do Gateway de aplicação](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Do seu grupo de recursos, vá ao caso do gateway de aplicação que criou na secção anterior.
2. Em **Definições**, selecione **pools backend**e, em seguida, selecione **Adicionar** para adicionar os VMs que pretende associar com as piscinas traseiras de nível web.
3. Insira o nome da piscina traseira e todos os endereços IP das máquinas que residem na piscina. Neste cenário, estamos a ligar duas piscinas de servidores de back-end de máquinas virtuais.

   ![Gateway de aplicação "Adicionar piscina de backend"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Em **Definições** do gateway de aplicações, selecione **Regras**e, em seguida, clique no botão **'Path' para** adicionar uma regra.

   ![Botão "Path based" das Regras do Gateway de Aplicação](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Configure a regra fornecendo as seguintes informações.

   Definições básicas:

   + **Nome**: O nome amigável da regra acessível no portal.
   + **Ouvinte**: O ouvinte que é utilizado para a regra.
   + **Piscina de backend predefinido**: A piscina traseira a ser utilizada com a regra padrão.
   + **Definições HTTP predefinidos**: As definições HTTP a utilizar com a regra predefinida.

   Regras baseadas no caminho:

   + **Nome**: O nome amigável da regra baseada no caminho.
   + **Caminhos**: A regra do caminho que é utilizada para encaminhar o tráfego.
   + **Backend Pool**: A piscina traseira a ser utilizada com esta regra.
   + **DEFINIÇÃO HTTP**: As definições HTTP a utilizar com esta regra.

   > [!IMPORTANT]
   > Caminhos: Os caminhos válidos devem começar com "/". O wildcard " \* " só é permitido no final. Exemplos válidos são /xyz, /xyz, \* ou /xyz/ \* .

   ![Lâmina de gateway de aplicação "Adicionar regra baseada em caminho"](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Passo 3: Adicionar gateways de aplicação aos pontos finais do Gestor de Tráfego

Neste cenário, o Traffic Manager está ligado aos gateways de aplicações (configurados nas etapas anteriores) que residem em diferentes regiões. Agora que os gateways de aplicação estão configurados, o próximo passo é ligá-los ao seu perfil de Gestor de Tráfego.

1. Abra o seu perfil de Gestor de Tráfego. Para tal, procure no seu grupo de recursos ou procure o nome do perfil de Gestor de Tráfego de **Todos os Recursos.**
2. No painel esquerdo, selecione **Endpoints**e, em seguida, clique **em Adicionar** para adicionar um ponto final.

   ![Botão de ponto final "Adicionar" do Gestor de Tráfego](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Criar um ponto final introduzindo as seguintes informações:

   * **Tipo**: Selecione o tipo de ponto final para o equilíbrio de carga. Neste cenário, selecione **O ponto final do Azure** porque estamos a ligá-lo às instâncias de gateway de aplicação que foram configuradas anteriormente.
   * **Nome**: Introduza o nome do ponto final.
   * **Tipo de recurso-alvo**: Selecione **o endereço IP público** e, em seguida, no recurso **Target**, selecione o IP público do gateway de aplicações que foi configurado anteriormente.

   ![Gestor de tráfego "Adicionar ponto final"](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Agora pode testar a sua configuração acedendo-a com o DNS do seu perfil de Gestor de Tráfego (neste exemplo: TrafficManagerScenario.trafficmanager.net). Pode reencotar pedidos, apresentar ou retirar VMs e servidores web que foram criados em diferentes regiões e alterar as definições de perfil do Gestor de Tráfego para testar a sua configuração.

### <a name="step-4-create-a-load-balancer"></a>Passo 4: Criar um equilibrador de carga

Neste cenário, o Balanceador de Carga distribui ligações do nível web para as bases de dados dentro de um cluster de alta disponibilidade.

Se o seu cluster de base de dados de alta disponibilidade estiver a utilizar o SQL Server AlwaysOn, consulte [um ou mais ouvintes do Grupo Always On Availability](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) para obter instruções passo a passo.

Para obter mais informações sobre a configuração de um equilibrador de carga interno, consulte [Criar um equilibrador de carga interno no portal Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. No portal Azure, no painel esquerdo, clique em **Criar um equilibrador**  >  **de carga de rede de**recursos  >  **Load balancer**.
2. Escolha um nome para o seu equilibrador de carga.
3. Desabrague o **Tipo** para **Interno**, e escolha a rede virtual adequada e a sub-rede para o equilibrador de carga residir.
4. Na **atribuição de endereços IP**, selecione **Dynamic** ou **Static**.
5. No **grupo De recursos,** escolha o grupo de recursos para o equilibrador de carga.
6. Em **Localização**, escolha a região apropriada para o equilibrador de carga.
7. Clique em **Criar** para gerar o equilibrador de carga.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Ligue um nível de base de dados de back-end ao equilibrador de carga

1. Do seu grupo de recursos, encontre o equilibrador de carga que foi criado nos passos anteriores.
2. Em **Definições,** clique em **pools backend**e, em seguida, clique **em Adicionar** para adicionar uma piscina traseira.

   ![Balanceador de Carga "Adicionar piscina de backend"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Insira o nome da piscina traseira.
4. Adicione máquinas individuais ou um conjunto de disponibilidade na piscina traseira.

#### <a name="configure-a-probe"></a>Configure uma sonda

1. No seu equilibrador de carga, em **Definições,** selecione **Sondas**e, em seguida, clique em **Adicionar** uma sonda.

   ![Balanceador de Carga "Adicionar sonda"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Insira o nome da sonda.
3. Selecione o **Protocolo** para a sonda. Para uma base de dados, pode querer uma sonda TCP em vez de uma sonda HTTP. Para saber mais sobre as sondas de balanceadores de carga, consulte [as sondas de equilíbrio](../load-balancer/load-balancer-custom-probe-overview.md)de carga .
4. Introduza o **Porto** da sua base de dados para aceder à sonda.
5. Em **Intervalo**, especifique com que frequência sondar a aplicação.
6. No **limiar pouco saudável,** especifique o número de falhas contínuas da sonda que devem ocorrer para que o VM de back-end seja considerado insalubre.
7. Clique **em OK** para criar a sonda.

#### <a name="configure-the-load-balancing-rules"></a>Configure as regras de equilíbrio de carga

1. Em **Definições** do seu equilibrador de carga, selecione **regras de equilíbrio de carga**e, em seguida, clique em **Adicionar** para criar uma regra.
2. Introduza o **Nome** para a regra de equilíbrio de carga.
3. Escolha o **endereço IP frontend** do esquilibrador de carga, **protocolo**e **porta**.
4. No **porto backend**, especifique a porta a utilizar na piscina traseira.
5. Selecione o **pool Backend** e o **Sonda** que foram criados nos passos anteriores para aplicar a regra.
6. Em **'Under Session persistência'**, escolha como quer que as sessões persistam.
7. Nos **intervalos de tempo inativos, especifique**o número de minutos antes de um intervalo de tempo inativo.
8. Em **IP flutuante**, selecione **desativado** ou **ativado**.
9. Clique em **OK** para criar a regra.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Passo 5: Ligue os VMs de nível web ao balançador de carga

Agora configuramos o endereço IP e a porta frontal do balanceador de carga nas aplicações que estão a ser executadas nos seus VMs de nível web para quaisquer ligações de base de dados. Esta configuração é específica para as aplicações que funcionam nestes VMs. Para configurar o endereço IP de destino e a porta, consulte a documentação da aplicação. Para encontrar o endereço IP da extremidade frontal, no portal Azure, vá ao pool IP frontal nas definições do **balançador de carga**.

![Painel de navegação do balanceador de carga "Frontend IP pool"](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Passos seguintes

* [Descrição Geral do Gestor de Tráfego](traffic-manager-overview.md)
* [Descrição geral do Gateway de Aplicação](../application-gateway/application-gateway-introduction.md)
* [Descrição Geral do Balanceador de Carga do Azure (Azure Load Balancer overview)](../load-balancer/load-balancer-overview.md)
