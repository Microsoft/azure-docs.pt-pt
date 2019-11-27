---
title: Usando serviços de balanceamento de carga no Azure | Microsoft Docs
description: 'Este tutorial mostra como criar um cenário usando o portfólio de balanceamento de carga do Azure: Gerenciador de tráfego, gateway de aplicativo e Load Balancer.'
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: kumudD
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/27/2016
ms.author: allensu
ms.openlocfilehash: 4a7f8fd45b1e496ba3f0208d523ac569a24e9e7c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227779"
---
# <a name="using-load-balancing-services-in-azure"></a>Utilizar serviços de balanceamento de carga no Azure

## <a name="introduction"></a>Introdução

O Microsoft Azure fornece vários serviços para gerenciar como o tráfego de rede é distribuído e com balanceamento de carga. Você pode usar esses serviços individualmente ou combinar seus métodos, dependendo de suas necessidades, para criar a solução ideal.

Neste tutorial, primeiro definimos um caso de uso do cliente e vemos como ele pode se tornar mais robusto e com bom desempenho usando o seguinte portfólio de balanceamento de carga do Azure: Gerenciador de tráfego, gateway de aplicativo e Load Balancer. Em seguida, fornecemos instruções passo a passo para a criação de uma implantação que é geograficamente redundante, distribui o tráfego para VMs e ajuda você a gerenciar diferentes tipos de solicitações.

Em um nível conceitual, cada um desses serviços desempenha uma função distinta na hierarquia de balanceamento de carga.

* O **Gerenciador de tráfego** fornece balanceamento de carga de DNS global. Ele examina as solicitações DNS de entrada e responde com um ponto de extremidade íntegro, de acordo com a política de roteamento que o cliente selecionou. As opções para os métodos de roteamento são:
  * Roteamento de desempenho para enviar o solicitante para o ponto de extremidade mais próximo em termos de latência.
  * Roteamento de prioridade para direcionar todo o tráfego para um ponto de extremidade, com outros pontos de extremidades como backup.
  * Roteamento de rodízio ponderado, que distribui o tráfego com base no peso atribuído a cada ponto de extremidade.
  * Roteamento baseado em Geografia para distribuir o tráfego para os pontos de extremidade do aplicativo com base na localização geográfica do usuário.
  * Roteamento baseado em sub-rede para distribuir o tráfego para os pontos de extremidade do aplicativo com base na sub-rede (intervalo de endereços IP) do usuário.
  * Roteamento de vários valores que permite enviar endereços IP de mais de um ponto de extremidade de aplicativo em uma única resposta DNS.

  O cliente se conecta diretamente ao ponto de extremidade retornado pelo Gerenciador de tráfego. O Gerenciador de tráfego do Azure detecta quando um ponto de extremidade não está íntegro e redireciona os clientes para outra instância íntegra. Consulte a [documentação do Gerenciador de tráfego do Azure](traffic-manager-overview.md) para saber mais sobre o serviço.
* O **Gateway de aplicativo** fornece o ADC (controlador de entrega de aplicativos) como um serviço, oferecendo vários recursos de balanceamento de carga de camada 7 para seu aplicativo. Ele permite que os clientes otimizem web farm produtividade descarregando a terminação SSL com uso intensivo de CPU para o gateway de aplicativo. Outros recursos de roteamento de camada 7 incluem distribuição Round Robin de tráfego de entrada, afinidade de sessão baseada em cookie, roteamento baseado em caminho de URL e a capacidade de hospedar vários sites por trás de um único gateway de aplicativo. O gateway de aplicativo pode ser configurado como um gateway voltado para a Internet, um gateway somente interno ou uma combinação de ambos. O gateway de aplicativo é totalmente gerenciado, escalonável e altamente disponível do Azure. Proporciona um conjunto avançado de capacidades de registo e diagnóstico, para uma melhor capacidade de gestão.
* **Load Balancer** é parte integrante da pilha do Sdn do Azure, fornecendo serviços de balanceamento de carga de camada 4 de alto desempenho e baixa latência para todos os protocolos UDP e TCP. Ele gerencia conexões de entrada e saída. Você pode configurar pontos de extremidade públicos e internos com balanceamento de carga e definir regras para mapear conexões de entrada para destinos de pool de back-end usando as opções de investigação de integridade TCP e HTTP para gerenciar a disponibilidade do serviço.

## <a name="scenario"></a>Cenário

Neste cenário de exemplo, usamos um site simples que serve para dois tipos de conteúdo: imagens e páginas da Web renderizadas dinamicamente. O site deve ser geograficamente redundante e deve servir a seus usuários do local mais próximo (menor latência) para eles. O desenvolvedor do aplicativo decidiu que todas as URLs que correspondem ao padrão/images/* são servidas de um pool de VMs dedicado que são diferentes do restante do web farm.

Além disso, o pool de VMs padrão que atende ao conteúdo dinâmico precisa se comunicar com um banco de dados back-end hospedado em um cluster de alta disponibilidade. Toda a implantação é configurada por meio de Azure Resource Manager.

Usar o Gerenciador de tráfego, o gateway de aplicativo e o Load Balancer permite que este site alcance essas metas de design:

* **Redundância de várias regiões geográficas**: se uma região ficar inativa, o Traffic Manager roteará o tráfego diretamente para a região mais próxima sem nenhuma intervenção do proprietário do aplicativo.
* **Latência reduzida**: como o Gerenciador de tráfego direciona automaticamente o cliente para a região mais próxima, o cliente enfrenta uma latência mais baixa ao solicitar o conteúdo da página da Web.
* **Escalabilidade independente**: como a carga de trabalho do aplicativo Web é separada por tipo de conteúdo, o proprietário do aplicativo pode dimensionar as cargas de trabalho de solicitação independentemente umas das outras. O gateway de aplicativo garante que o tráfego seja roteado para os pools corretos com base nas regras especificadas e na integridade do aplicativo.
* **Balanceamento de carga interno**: como Load Balancer está na frente do cluster de alta disponibilidade, somente o ponto de extremidade ativo e íntegro para um banco de dados é exposto ao aplicativo. Além disso, um administrador de banco de dados pode otimizar a carga de trabalho distribuindo réplicas ativas e passivas em todo o cluster, independentemente do aplicativo de front-end. Load Balancer fornece conexões com o cluster de alta disponibilidade e garante que somente bancos de dados íntegros recebam solicitações de conexão.

O diagrama a seguir mostra a arquitetura desse cenário:

![Diagrama de arquitetura de balanceamento de carga](./media/traffic-manager-load-balancing-azure/scenario-diagram.png)

> [!NOTE]
> Este exemplo é apenas uma das muitas configurações possíveis dos serviços de balanceamento de carga que o Azure oferece. O Gerenciador de tráfego, o gateway de aplicativo e o Load Balancer podem ser combinados e combinados para melhor atender às suas necessidades de balanceamento de carga. Por exemplo, se o descarregamento SSL ou o processamento de camada 7 não for necessário, Load Balancer poderá ser usado no lugar do gateway de aplicativo.

## <a name="setting-up-the-load-balancing-stack"></a>Configurando a pilha de balanceamento de carga

### <a name="step-1-create-a-traffic-manager-profile"></a>Etapa 1: criar um perfil do Gerenciador de tráfego

1. No portal do Azure, clique em **criar um recurso** > **rede** > **perfil do Gerenciador de tráfego** > **criar**.
2. Insira as seguintes informações básicas:

   * **Nome**: Dê ao seu perfil do Gerenciador de tráfego um nome de prefixo DNS.
   * **Método de roteamento**: selecione a política de método de roteamento de tráfego. Para obter mais informações sobre os métodos, consulte [sobre métodos de roteamento de tráfego do Traffic Manager](traffic-manager-routing-methods.md).
   * **Assinatura**: selecione a assinatura que contém o perfil.
   * **Grupo de recursos**: selecione o grupo de recursos que contém o perfil. Pode ser um grupo de recursos novo ou existente.
   * **Local do grupo de recursos**: o serviço do Gerenciador de tráfego é global e não está associado a um local. No entanto, você deve especificar uma região para o grupo onde os metadados associados ao perfil do Gerenciador de tráfego residem. Esse local não tem impacto sobre a disponibilidade do tempo de execução do perfil.

3. Clique em **criar** para gerar o perfil do Gerenciador de tráfego.

   ![Folha "criar Gerenciador de tráfego"](./media/traffic-manager-load-balancing-azure/s1-create-tm-blade.png)

### <a name="step-2-create-the-application-gateways"></a>Etapa 2: criar os gateways de aplicativo

1. No portal do Azure, no painel esquerdo, clique em **criar um recurso** > **rede** > **Gateway de aplicativo**.
2. Insira as seguintes informações básicas sobre o gateway de aplicativo:

   * **Nome**: o nome do gateway de aplicativo.
   * **Tamanho do SKU**: o tamanho do gateway de aplicativo, disponível como pequeno, médio ou grande.
   * **Contagem**de instâncias: o número de instâncias, um valor de 2 a 10.
   * **Grupo de recursos**: o grupo de recursos que contém o gateway de aplicativo. Pode ser um grupo de recursos existente ou um novo.
   * **Local**: a região do gateway de aplicativo, que é o mesmo local que o grupo de recursos. O local é importante, pois a rede virtual e o IP público devem estar no mesmo local que o gateway.
3. Clique em **OK**.
4. Defina a rede virtual, a sub-rede, o IP de front-end e as configurações de ouvinte para o gateway de aplicativo. Nesse cenário, o endereço IP de front-end é **público**, o que permite que ele seja adicionado como um ponto de extremidade ao perfil do Gerenciador de tráfego posteriormente.
5. Configure o ouvinte com uma das seguintes opções:
    * Se você usar HTTP, não há nada a ser configurado. Clique em **OK**.
    * Se você usar HTTPS, será necessária uma configuração adicional. Consulte [criar um gateway de aplicativo](../application-gateway/application-gateway-create-gateway-portal.md), começando na etapa 9. Quando você tiver concluído a configuração, clique em **OK**.

#### <a name="configure-url-routing-for-application-gateways"></a>Configurar o roteamento de URL para gateways de aplicativo

Quando você escolhe um pool de back-end, um gateway de aplicativo configurado com uma regra baseada em caminho usa um padrão de caminho da URL de solicitação, além da distribuição Round Robin. Nesse cenário, estamos adicionando uma regra com base no caminho para direcionar qualquer URL com "/images/\*" para o pool de servidores de imagem. Para obter mais informações sobre como configurar o roteamento baseado em caminho de URL para um gateway de aplicativo, consulte [criar uma regra com base no caminho para um gateway de aplicativo](../application-gateway/application-gateway-create-url-route-portal.md).

![Diagrama da camada da Web do gateway de aplicativo](./media/traffic-manager-load-balancing-azure/web-tier-diagram.png)

1. Em seu grupo de recursos, vá para a instância do gateway de aplicativo que você criou na seção anterior.
2. Em **configurações**, selecione **pools de back-end**e, em seguida, selecione **Adicionar** para adicionar as VMs que você deseja associar aos pools de back-end da camada da Web.
3. Insira o nome do pool de back-end e todos os endereços IP dos computadores que residem no pool. Nesse cenário, estamos conectando dois pools de servidores back-end de máquinas virtuais.

   ![Gateway de aplicativo "Adicionar pool de back-end"](./media/traffic-manager-load-balancing-azure/s2-appgw-add-bepool.png)

4. Em **configurações** do gateway de aplicativo, selecione **regras**e, em seguida, clique no botão **baseado em caminho** para adicionar uma regra.

   ![Botão "baseado em caminho" das regras do gateway de aplicativo](./media/traffic-manager-load-balancing-azure/s2-appgw-add-pathrule.png)

5. Configure a regra fornecendo as informações a seguir.

   Configurações básicas:

   + **Nome**: o nome amigável da regra que é acessível no Portal.
   + **Listener**: o ouvinte que é usado para a regra.
   + **Pool de back**-end padrão: o pool de back-ends a ser usado com a regra padrão.
   + **Configurações http padrão**: as configurações de http a serem usadas com a regra padrão.

   Regras baseadas em caminho:

   + **Nome**: o nome amigável da regra com base no caminho.
   + **Paths**: a regra de caminho que é usada para encaminhar o tráfego.
   + **Pool de back**-end: o pool de back-ends a ser usado com esta regra.
   + **Configuração de http**: as configurações de http a serem usadas com esta regra.

   > [!IMPORTANT]
   > Caminhos: os caminhos válidos devem começar com "/". O curinga "\*" é permitido apenas no final. Os exemplos válidos são/XYZ,/XYZ\*ou/XYZ/\*.

   ![Folha "Adicionar regra com base no caminho" do gateway de aplicativo](./media/traffic-manager-load-balancing-azure/s2-appgw-pathrule-blade.png)

### <a name="step-3-add-application-gateways-to-the-traffic-manager-endpoints"></a>Etapa 3: Adicionar gateways de aplicativo aos pontos de extremidade do Gerenciador de tráfego

Nesse cenário, o Gerenciador de tráfego está conectado aos gateways de aplicativo (conforme configurado nas etapas anteriores) que residem em regiões diferentes. Agora que os gateways de aplicativo estão configurados, a próxima etapa é conectá-los ao seu perfil do Gerenciador de tráfego.

1. Abra seu perfil do Gerenciador de tráfego. Para fazer isso, examine seu grupo de recursos ou procure o nome do perfil do Gerenciador de tráfego de **todos os recursos**.
2. No painel esquerdo, selecione **pontos**de extremidade e, em seguida, clique em **Adicionar** para adicionar um EndPoint.

   ![Botão "Adicionar" dos pontos de extremidade do Gerenciador de tráfego](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint.png)

3. Crie um ponto de extremidade inserindo as seguintes informações:

   * **Tipo**: selecione o tipo de ponto de extremidade para balancear a carga. Nesse cenário, selecione **ponto de extremidade do Azure** porque estamos conectando-o às instâncias do gateway de aplicativo que foram configuradas anteriormente.
   * **Nome**: Insira o nome do ponto de extremidade.
   * **Tipo de recurso de destino**: selecione **endereço IP público** e, em **recurso de destino**, selecione o IP público do gateway de aplicativo que foi configurado anteriormente.

   !["Adicionar ponto de extremidade" do Gerenciador de tráfego](./media/traffic-manager-load-balancing-azure/s3-tm-add-endpoint-blade.png)

4. Agora você pode testar sua configuração acessando-a com o DNS do seu perfil do Gerenciador de tráfego (neste exemplo: TrafficManagerScenario.trafficmanager.net). Você pode reenviar solicitações, ativar ou desativar VMs e servidores Web que foram criados em regiões diferentes e alterar as configurações de perfil do Gerenciador de tráfego para testar a configuração.

### <a name="step-4-create-a-load-balancer"></a>Etapa 4: criar um balanceador de carga

Nesse cenário, Load Balancer distribui conexões da camada da Web para os bancos de dados em um cluster de alta disponibilidade.

Se o seu cluster de banco de dados de alta disponibilidade estiver usando o SQL Server AlwaysOn, confira [configurar um ou mais Always on ouvintes do grupo de disponibilidade](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) para obter instruções passo a passo.

Para obter mais informações sobre como configurar um balanceador de carga interno, consulte [criar um balanceador de carga interno no portal do Azure](../load-balancer/load-balancer-get-started-ilb-arm-portal.md).

1. No portal do Azure, no painel esquerdo, clique em **criar um recurso** > **rede** > **balanceador de carga**.
2. Escolha um nome para o balanceador de carga.
3. Defina o **tipo** como **interno**e escolha a rede virtual e a sub-rede apropriadas para o balanceador de carga residir.
4. Em **atribuição de endereço IP**, selecione **dinâmico** ou **estático**.
5. Em **grupo de recursos**, escolha o grupo de recursos para o balanceador de carga.
6. Em **local**, escolha a região apropriada para o balanceador de carga.
7. Clique em **criar** para gerar o balanceador de carga.

#### <a name="connect-a-back-end-database-tier-to-the-load-balancer"></a>Conectar uma camada de banco de dados back-end ao balanceador de carga

1. Em seu grupo de recursos, localize o balanceador de carga que foi criado nas etapas anteriores.
2. Em **configurações**, clique em **pools de back-end**e clique em **Adicionar** para adicionar um pool de back-ends.

   ![Load Balancer "Adicionar pool de back-end"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-bepool.png)

3. Insira o nome do pool de back-end.
4. Adicione máquinas individuais ou um conjunto de disponibilidade ao pool de back-ends.

#### <a name="configure-a-probe"></a>Configurar uma investigação

1. No balanceador de carga, em **configurações**, selecione **investigações**e clique em **Adicionar** para adicionar uma investigação.

   ![Load Balancer "Adicionar investigação"](./media/traffic-manager-load-balancing-azure/s4-ilb-add-probe.png)

2. Insira o nome para a investigação.
3. Selecione o **protocolo** para a investigação. Para um banco de dados, você pode querer uma investigação TCP em vez de uma investigação HTTP. Para saber mais sobre as investigações do balanceador de carga, consulte [entender as investigações do balanceador de carga](../load-balancer/load-balancer-custom-probe-overview.md).
4. Insira a **porta** do banco de dados a ser usada para acessar a investigação.
5. Em **intervalo**, especifique a frequência de investigação do aplicativo.
6. Em **limite não íntegro**, especifique o número de falhas de investigação contínua que devem ocorrer para que a VM de back-end seja considerada não íntegra.
7. Clique em **OK** para criar a investigação.

#### <a name="configure-the-load-balancing-rules"></a>Configurar as regras de balanceamento de carga

1. Em **configurações** do balanceador de carga, selecione **regras de balanceamento de carga**e clique em **Adicionar** para criar uma regra.
2. Insira o **nome** para a regra de balanceamento de carga.
3. Escolha o **endereço IP de front-end** do balanceador de carga, o **protocolo**e a **porta**.
4. Em **porta de back-end**, especifique a porta a ser usada no pool de back-ends.
5. Selecione o **pool de back-end** e a **investigação** que foram criados nas etapas anteriores para aplicar a regra a.
6. Em **persistência da sessão**, escolha como você deseja que as sessões persistam.
7. Em **tempos limite de ociosidade**, especifique o número de minutos antes de um tempo limite de ociosidade.
8. Em **IP flutuante**, selecione **desabilitado** ou **habilitado**.
9. Clique em **OK** para criar a regra.

### <a name="step-5-connect-web-tier-vms-to-the-load-balancer"></a>Etapa 5: conectar VMs da camada da Web ao balanceador de carga

Agora, configuramos o endereço IP e a porta de front-end do balanceador de carga nos aplicativos em execução nas VMs da camada da Web para qualquer conexão de banco de dados. Essa configuração é específica para os aplicativos que são executados nessas VMs. Para configurar o endereço IP de destino e a porta, consulte a documentação do aplicativo. Para localizar o endereço IP do front-end, na portal do Azure, vá para o pool de IPS de front-end nas **configurações do balanceador de carga**.

![Painel de navegação do Load Balancer "pool de IPS de front-end"](./media/traffic-manager-load-balancing-azure/s5-ilb-frontend-ippool.png)

## <a name="next-steps"></a>Passos seguintes

* [Descrição Geral do Gestor de Tráfego](traffic-manager-overview.md)
* [Visão geral do gateway de aplicativo](../application-gateway/application-gateway-introduction.md)
* [Descrição Geral do Balanceador de Carga do Azure (Azure Load Balancer overview)](../load-balancer/load-balancer-overview.md)
