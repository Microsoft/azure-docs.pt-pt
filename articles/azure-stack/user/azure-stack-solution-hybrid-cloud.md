---
title: Implementar uma cloud híbrida com o Azure e o Azure Stack | Documentos da Microsoft
description: Saiba como implementar uma cloud híbrida com o Azure e o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 336a2a3fd98f7829694eb095ff2646d9d361afd3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58097325"
---
# <a name="tutorial-deploy-a-hybrid-cloud-solution-with-azure-and-azure-stack"></a>Tutorial: Implementar uma solução de cloud híbrida com o Azure e o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Este tutorial mostra-lhe como implementar uma solução de cloud híbrida que utiliza a cloud pública do Azure e a nuvem privada do Azure Stack.

Ao utilizar uma solução de cloud híbrida, pode combinar os benefícios de conformidade de uma nuvem privada com a escalabilidade da cloud pública. Além disso, os programadores podem tirar partido do ecossistema de desenvolvedor do Microsoft e aplique suas habilidades para ambientes de cloud e no local.

## <a name="overview-and-assumptions"></a>Descrição geral e pressupostos

Pode seguir este tutorial para configurar um fluxo de trabalho que permite aos programadores implementar uma aplicação web idêntica a uma nuvem pública e uma nuvem privada. Esta aplicação será capaz de aceder a uma rede encaminhável de não-Internet alojada na cloud privada. Estas aplicações web são monitorizadas e quando existe um pico de tráfego, um programa modifica os registos DNS para redirecionar o tráfego para a cloud pública. Quando o tráfego ignora o nível antes do pico, o tráfego é encaminhado para a nuvem privada.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> - Implemente um servidor de base de dados do SQL Server ligada à híbrida.
> - Ligar uma aplicação web no global Azure a uma rede híbrida.
> - Configure o DNS para o dimensionamento entre Clouds.
> - Configure certificados SSL para dimensionamento entre Clouds.
> - Configurar e implementar a aplicação web.
> - Criar um perfil do Gestor de tráfego e configurá-la para dimensionamento entre Clouds.
> - Configure o Application Insights, monitorização e alertas para aumento no tráfego.
> - Configure o tráfego automática a alternância entre global do Azure e o Azure Stack.

### <a name="assumptions"></a>Suposições

Este tutorial parte do princípio de que tem um conhecimento básico do global Azure e o Azure Stack. Se quiser saber mais antes de iniciar o tutorial, consulte estes artigos:

 - [Introdução ao Azure](https://azure.microsoft.com/overview/what-is-azure/)
 - [Conceitos-chave do Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

Este tutorial também pressupõe que tem uma subscrição do Azure. Se não tiver uma subscrição, pode [criar uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, certifique-se de que pode satisfazer os seguintes requisitos:

- Uma ferramenta do Azure Stack Development Kit (ASDK) ou uma assinatura num sistema integrado do Azure Stack. Para implementar um Kit de desenvolvimento do Azure Stack, siga as instruções em [implementar ASDK através do instalador](../asdk/asdk-deploy.md).
- A instalação do Azure Stack deve ter o seguinte instalado:
  - O serviço de aplicações do Azure. Trabalhar com o operador de pilha do Azure para implementar e configurar o serviço de aplicações do Azure no seu ambiente. Este tutorial requer o serviço de aplicações tem a função de trabalho dedicado disponível, pelo menos, um (1).
  - Uma imagem do Windows Server 2016
  - Um Windows Server 2016 com uma imagem do Microsoft SQL Server
  - Os planos adequados e ofertas
  - Um nome de domínio para a sua aplicação web. Se não tiver um nome de domínio pode comprar um a partir de um fornecedor de domínios como o GoDaddy, Bluehost e InMotion.
- Um certificado SSL para o seu domínio de uma autoridade de certificação fidedigna, como LetsEncrypt.
- Um aplicativo web que comunica com um banco de dados do SQL Server e suporta o Application Insights. Pode baixar o [dotnetcore-sqldb-tutorial](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial) aplicação de exemplo do GitHub.
- Uma rede híbrida entre uma rede virtual do Azure e a rede virtual do Azure Stack. Para obter instruções detalhadas, consulte [configurar a conectividade de cloud híbrida com o Azure e o Azure Stack](azure-stack-solution-hybrid-connectivity.md).

- Um híbrido integração contínua/contínua (CI/CD) pipeline de implementação com um agente de compilação privado no Azure Stack. Para obter instruções detalhadas, consulte [configurar a identidade de cloud híbrida com as aplicações do Azure e o Azure Stack](azure-stack-solution-hybrid-identity.md)

## <a name="deploy-a-hybrid-connected-sql-server-database-server"></a>Implementar um servidor de base de dados do SQL Server ligada à híbrida

1. Inicie sessão no portal de utilizador do Azure Stack.

2. Sobre o **Dashboard**, selecione **Marketplace**.

    ![Marketplace do Azure Stack](media/azure-stack-solution-hybrid-cloud/image1.png)

3. Na **Marketplace**, selecione **computação**e, em seguida, escolha **mais**. Sob **mais**, selecione o **licença gratuita do SQL Server: SQL Server 2017 Developer no Windows Server** imagem.

    ![Selecionar uma imagem de máquina virtual](media/azure-stack-solution-hybrid-cloud/image2.png)

4. No **gratuitos a licença do SQL Server: SQL Server 2017 Developer no Windows Server** selecionar **criar**.

5. No **Noções básicas > configurar as definições básicas**, forneça um **nome** para a máquina virtual (VM), uma **nome de utilizador** para o SA do SQL Server e um **depalavra-passe** para SA.  Do **subscrição** pendente, selecione a subscrição que estiver a implementar. Para **grupo de recursos**, utilize **Escolher existente** e colocar a VM no mesmo grupo de recursos que a sua aplicação web do Azure Stack.

    ![Configurar as definições básicas para a VM](media/azure-stack-solution-hybrid-cloud/image3.png)

6. Sob **tamanho**, escolha um tamanho para a sua VM. Para este tutorial, é recomendável A2_Standard ou um DS2_V2_Standard.

7. Sob **definições > configurar funcionalidades opcionais**, configure as seguintes definições:

   - **Conta de armazenamento**. Crie uma nova conta se precisar de um.
   - **Rede virtual**

     > [!Important]  
     > Certifique-se de que a sua VM do SQL Server é implementada na mesma rede virtual que os gateways de VPN.

   - **Endereço IP público**. Pode utilizar as predefinições.
   - **Grupo de segurança de rede** (NSG). Crie um novo NSG.
   - **Extensões e monitorização**. Mantenha as configurações predefinidas.
   - **Conta de armazenamento de diagnóstico**. Crie uma nova conta se precisar de um.
   - Selecione **OK** para guardar a configuração.

     ![Configurar funcionalidades opcionais](media/azure-stack-solution-hybrid-cloud/image4.png)

1. Sob **definições do SQL Server**, configure as seguintes definições:
   - Para **conectividade SQL**, selecione a **público (Internet)**.
   - Para **porta**, mantenha a predefinição **1433**.
   - Para **autenticação de SQL**, selecione **ativar**.

     > [!Note]  
     > Ao ativar a autenticação de SQL, ele deve automático-preencher com as informações de "SQLAdmin" configurada no **Noções básicas**.

   - Para o restante das definições, mantenha as predefinições. Selecione **OK**.

     ![Configurar definições do SQL Server](media/azure-stack-solution-hybrid-cloud/image5.png)

9. No **resumo**, reveja a configuração de máquina virtual e, em seguida, selecione **OK** para iniciar a implementação.

    ![Resumo da configuração](media/azure-stack-solution-hybrid-cloud/image6.png)

10. Ele irá demorar algum tempo para criar a nova VM. Pode ver o estado das suas VMs no **máquinas virtuais**.

    ![Máquinas virtuais](media/azure-stack-solution-hybrid-cloud/image7.png)

## <a name="create-web-apps-in-azure-and-azure-stack"></a>Criar aplicações web no Azure e o Azure Stack

O serviço de aplicações do Azure simplifica a executar e gerir uma aplicação web. Como o Azure Stack é consistente com o Azure, pode executar o serviço de aplicações nos dois ambientes. Irá utilizar o serviço de aplicações para alojar a sua aplicação.

### <a name="create-web-apps"></a>Criar aplicações web

1. Criar uma aplicação web no Azure ao seguir as instruções em [gerir um plano do serviço de aplicações no Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage#create-an-app-service-plan). Certifique-se de que colocar a aplicação web na mesma subscrição e grupo de recursos como a sua rede híbrida.

2. Repita o passo anterior (1) no Azure Stack.

### <a name="add-route-for-azure-stack"></a>Adicionar a rota para o Azure Stack

O serviço de aplicações no Azure Stack tem de ser encaminhável da Internet pública para permitir aos utilizadores aceder à sua aplicação. Se o Azure Stack está acessível a partir da Internet, tome nota do endereço IP destinado ao público ou URL da aplicação web do Azure Stack.

Se estiver a utilizar um ASDK, pode [configurar um mapeamento NAT estático](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-vpn-connection-one-node#configure-the-nat-virtual-machine-on-each-azure-stack-development-kit-for-gateway-traversal) para expor o serviço de aplicações fora do ambiente virtual.

### <a name="connect-a-web-app-in-azure-to-a-hybrid-network"></a>Ligar uma aplicação web no Azure a uma rede híbrida

Para fornecer conectividade entre a web front-end no Azure e a base de dados do SQL Server no Azure Stack, a aplicação web de estar ligado à rede híbrida entre o Azure e o Azure Stack. Para ativar a conectividade, terá que:

- Configurar a conectividade de ponto a site
- Configurar a aplicação Web
- Modificar o gateway de rede local no Azure Stack.

### <a name="configure-the-azure-virtual-network-for-point-to-site-connectivity"></a>Configurar a rede virtual do Azure para a conectividade de ponto a site

O gateway de rede virtual do lado do Azure da rede híbrida têm de permitir ligações de ponto a site integrar com o serviço de aplicações do Azure.

1. No Azure, navegue para a página de gateway de rede virtual. Sob **configurações**, selecione **configuraçãoponto a site**.

    ![Opção de ponto a site](media/azure-stack-solution-hybrid-cloud/image8.png)

2. Selecione **configurar agora** Configurar ponto a site.

    ![Iniciar a configuração de ponto a site](media/azure-stack-solution-hybrid-cloud/image9.png)

3. Sobre o **ponto a site** configuração página, introduza o intervalo de endereços IP privado que pretende utilizar na **conjunto de endereços**.

   > [!Note]  
   > Certifique-se de que o intervalo que especificar não se sobrepõe a qualquer um dos intervalos de endereços já utilizados por sub-redes nos componentes do Azure ou no Azure Stack global da rede híbrida.

   Sob **tipo de túnel**, desmarque a **IKEv2 VPN**. Selecione **guardar** para concluir a configuração ponto a site.

   ![Definições de ponto a site](media/azure-stack-solution-hybrid-cloud/image10.png)

### <a name="integrate-the-azure-app-service-application-with-the-hybrid-network"></a>Integrar a aplicação de serviço de aplicações do Azure com a rede híbrida

1. Para ligar a aplicação à VNet do Azure, siga as instruções em [ativar a integração de VNet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet#enabling-vnet-integration).

2. Navegue para **definições** para o plano de serviço de aplicações que alojam a aplicação web. Na **configurações**, selecione **Networking**.

    ![Configurar o funcionamento em rede](media/azure-stack-solution-hybrid-cloud/image11.png)

3. Na **integração de VNET**, selecione **clique aqui para gerir**.

    ![Gerir a integração de VNET](media/azure-stack-solution-hybrid-cloud/image12.png)

4. Selecione a VNET a que pretende configurar. Sob **IP endereços ENCAMINHADO para VNET**, introduza o intervalo de endereços IP para a VNet do Azure, a VNet do Azure Stack e os espaços de endereços de ponto a site. Selecione **guardar** para validar e guardar estas definições.

    ![Intervalos de endereços IP para encaminhar](media/azure-stack-solution-hybrid-cloud/image13.png)

Para saber mais sobre como o serviço de aplicações se integra com VNets do Azure, veja [integrar a sua aplicação com uma rede Virtual do Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet).

### <a name="configure-the-azure-stack-virtual-network"></a>Configurar a rede virtual do Azure Stack

O gateway de rede local na rede virtual do Azure Stack tem de ser configurada para encaminhar o tráfego a partir do intervalo de endereços de ponto a site do serviço de aplicações.

1. No Azure Stack, navegue até **gateway de rede Local**. Em **Definições**, selecione **Configuração**.

    ![Opção de configuração do gateway](media/azure-stack-solution-hybrid-cloud/image14.png)

2. Na **espaço de endereços**, introduza o intervalo de endereços de ponto a site para o gateway de rede virtual no selecione Azure.l **guardar** para validar e guardar esta configuração.

    ![Espaço de endereços de ponto a site](media/azure-stack-solution-hybrid-cloud/image15.png)

## <a name="configure-dns-for-cross-cloud-scaling"></a>Configurar o DNS para o dimensionamento entre Clouds

Ao configurar corretamente o DNS para aplicações entre Clouds, os utilizadores podem aceder as instâncias do Azure e o Azure Stack global da sua aplicação web. A configuração de DNS para este tutorial também permite encaminhar o tráfego de Gestor de tráfego do Azure quando a carga aumenta ou diminui.

Este tutorial utiliza o DNS do Azure para gerir o DNS. (Domínios do serviço de aplicações não funcionarão.)

### <a name="create-subdomains"></a>Criar subdomínios

Porque o Gestor de tráfego depende de CNAMEs de DNS, é necessário um subdomínio para encaminhar corretamente o tráfego para pontos de extremidade. Para obter mais informações sobre os registos DNS e o mapeamento de domínio, consulte [mapear domínios com o Gestor de tráfego](https://docs.microsoft.com/azure/app-service/web-sites-traffic-manager-custom-domain-name)

Para o Azure irá criar um subdomínio que os utilizadores de ponto final pode utilizar para aceder à sua aplicação web. Para este tutorial, pode utilizar **app.northwind.com**, mas deve personalizar este valor com base no seu próprio domínio.

Também terá de criar um subdomínio com um registo para o ponto de final do Azure Stack. Pode usar **azurestack.northwind.com**.

### <a name="configure-a-custom-domain-in-azure"></a>Configurar um domínio personalizado no Azure

1. Adicionar a **app.northwind.com** nome de anfitrião para a aplicação web do Azure por [mapeando um CNAME para o serviço de aplicações do Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

### <a name="configure-custom-domains-in-azure-stack"></a>Configurar domínios personalizados no Azure Stack

1. Adicionar a **azurestack.northwind.com** nome de anfitrião para a aplicação web do Azure Stack por [mapear um registo para o serviço de aplicações do Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-an-a-record). Utilize o endereço IP encaminháveis para a Internet para a aplicação de serviço de aplicações.

2. Adicionar a **app.northwind.com** nome de anfitrião para a aplicação web do Azure Stack por [mapeando um CNAME para o serviço de aplicações do Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record). Utilize o nome de anfitrião que configurou no passo anterior (1) como o destino para o CNAME.

## <a name="configure-ssl-certificates-for-cross-cloud-scaling"></a>Configurar certificados SSL para dimensionamento entre Clouds

Precisa garantir que os dados confidenciais recolhidos pela sua aplicação web são seguros em trânsito para e em inatividade na base de dados SQL.

Irá configurar seus aplicativos web do Azure e o Azure Stack para utilizar certificados SSL para todo o tráfego de entrada.

### <a name="add-ssl-to-azure-and-azure-stack"></a>Adicionar o SSL para o Azure e o Azure Stack

Para adicionar o SSL para o Azure:

1. Certifique-se de que o certificado SSL, que obtém é válido para o subdomínio que criou. (Há problema em utilizar certificados de caráter universal.)

2. No Azure, siga as instruções no **preparar a sua aplicação web** e **vincular o certificado SSL** seções do [vincular um certificado SSL personalizado já existente às aplicações de Web do Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) artigos. Selecione **baseado em SNI SSL** como o **tipo de SSL**.

3. Redirecione todo o tráfego para a porta HTTPS. Siga as instruções no **impor HTTPS** secção a [vincular um certificado SSL personalizado já existente às aplicações de Web do Azure](https://docs.microsoft.com/Azure/app-service/app-service-web-tutorial-custom-ssl) artigo.

Para adicionar o SSL para o Azure Stack:

- Repita os passos 1 a 3 que utilizou para o Azure.

## <a name="configure-and-deploy-the-web-application"></a>Configurar e implementar a aplicação web

Irá configurar o código do aplicativo para a telemetria de relatório para a instância correta do Application Insights e configurar as aplicações web com as cadeias de ligação correta. Para saber mais sobre o Application Insights, veja [o que é o Application Insights?](https://docs.microsoft.com/azure/application-insights/app-insights-overview)

### <a name="add-application-insights"></a>Adicionar o Application Insights

1. Abra a sua aplicação web no Microsoft Visual Studio.

2. [Adicionar o Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core#add-application-insights-telemetry) ao seu projeto para transmitir a telemetria que utiliza o Application Insights para criar alertas quando o tráfego da web aumenta ou diminui.

### <a name="configure-dynamic-connection-strings"></a>Configurar cadeias de ligação dinâmica

Cada instância da aplicação web utilizará um método diferente para ligar à base de dados SQL. A aplicação do Azure utiliza o endereço IP privado da máquina virtual do SQL Server (VM) e a aplicação no Azure Stack utiliza o endereço IP público da VM do SQL Server.

> [!Note]  
> Num sistema do Azure Stack integrada, o endereço IP público não deve ser encaminháveis para a Internet. Num Azure Stack Development Kit (ASDK), não o endereço IP público é encaminhável fora o ASDK.

Pode utilizar variáveis de ambiente de serviço de aplicações para passar uma cadeia de ligação diferente para cada instância da aplicação.

1. Abra a aplicação no Visual Studio.

2. Abra Startup.cs e localize o bloco de código seguinte:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
    ```

3. Substitua o bloco de código anterior com o código a seguir, que usa uma cadeia de ligação definida no ficheiro appSettings:

    ```C#
    services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
     // Automatically perform database migration
     services.BuildServiceProvider().GetService<MyDatabaseContext>().Database.Migrate();
    ```

### <a name="configure-app-service-application-settings"></a>Configurar definições de aplicação do serviço de aplicações

1. Crie cadeias de ligação para o Azure e o Azure Stack. As cadeias de caracteres devem ser o mesmo, exceto para os endereços IP que são utilizados.

2. No Azure e o Azure Stack, adicione a cadeia de ligação adequado [como uma definição da aplicação](https://docs.microsoft.com/azure/app-service/web-sites-configure) na aplicação web, utilizando `SQLCONNSTR\_` como um prefixo no nome.

3. **Guardar** as definições da aplicação web e reinicie a aplicação.

## <a name="enable-automatic-scaling-in-global-azure"></a>Ativar dimensionamento automático no global Azure

Quando criar a sua aplicação web num ambiente de serviço de aplicações começa com uma instância. Automaticamente pode aumentar horizontalmente para adicionar instâncias para fornecer que mais recursos para a sua aplicação de computação. Da mesma forma, pode reduzir horizontalmente e automaticamente reduzir o número de instâncias de suas necessidades de aplicação.

> [!Note]  
> Tem de ter um plano de serviço de aplicações para configurar a ampliar e reduzir horizontalmente. Se não tiver um plano, crie uma antes de iniciar os passos seguintes.

### <a name="enable-automatic-scale-out"></a>Ativar ampliação automática

1. No Azure, encontre o plano do serviço de aplicações para os sites que pretende aumentar horizontalmente e, em seguida, selecione **aumentar horizontalmente (plano do serviço de aplicações)**.

    ![Aumentar horizontalmente](media/azure-stack-solution-hybrid-cloud/image16.png)

2. Selecione **ativar o dimensionamento automático**.

    ![Ativar dimensionamento automático](media/azure-stack-solution-hybrid-cloud/image17.png)

3. Introduza um nome para **nome da definição de dimensionamento automático**. Para o **predefinido** regra de dimensionamento automático, selecione **dimensionam com base numa métrica**. Definir o **limites de instância** para **mínimo: 1**, **máximo: 10**, e **predefinido: 1**.

    ![Configurar o dimensionamento automático](media/azure-stack-solution-hybrid-cloud/image18.png)

4. Selecione **+ adicionar uma regra**.

5. Na **origem da métrica**, selecione **recurso atual**. Utilize os seguintes critérios e ações para a regra.

**Critérios**

1. Sob **agregação de tempo** selecionar **médio**.

2. Sob **nome da métrica**, selecione **percentagem de CPU**.

3. Sob **operador**, selecione **superior**.

   - Definir o **limiar** ao **50**.
   - Definir o **duração** ao **10**.

**Ação**

1. Sob **operação**, selecione **aumentar contagem em**.

2. Definir o **contagem de instâncias** ao **2**.

3. Definir o **repouso** ao **5**.

4. Selecione **Adicionar**.

5. Selecione o **+ adicionar uma regra**.

6. Na **origem da métrica**, selecione **recurso atual.**

   > [!Note]  
   > O recurso atual irá conter o nome/GUID do seu plano de serviço de aplicações e o **tipo de recurso** e **recurso** listas suspensas estarão indisponíveis.

### <a name="enable-automatic-scale-in"></a>Ativar dimensionamento automático no

Quando o tráfego diminui, a aplicação web do Azure automaticamente pode reduzir o número de instâncias ativas para reduzir os custos. Esta ação é menos agressiva do que de escalamento horizontal para minimizar o impacto nos utilizadores da aplicação.

1. Navegue para o **predefinido** aumentar horizontalmente a condição, selecione **+ adicionar uma regra**. Utilize os seguintes critérios e ações para a regra.

**Critérios**

1. Sob **agregação de tempo** selecionar **médio**.

2. Sob **nome da métrica**, selecione **percentagem de CPU**.

3. Sob **operador**, selecione **inferior a**.

   - Definir o **limiar** ao **30**.
   - Definir o **duração** ao **10**.

**Ação**

1. Sob **operação**, selecione **diminuir contagem em**.

   - Definir o **contagem de instâncias** ao **1**.
   - Definir o **repouso** ao **5**.

2. Selecione **Adicionar**.

## <a name="create-a-traffic-manager-profile-and-configure-cross-cloud-scaling"></a>Criar um perfil do Gestor de tráfego e configurar o dimensionamento entre Clouds

Irá criar um perfil do Gestor de tráfego no Azure e, em seguida, configurar pontos finais para ativar o dimensionamento entre Clouds.

### <a name="create-traffic-manager-profile"></a>Criar perfil do Gestor de Tráfego

1. Selecione **criar um recurso**
2. Selecione **de rede**
3. Selecione **perfil do Traffic Manager** e configure o seguinte:

   - Na **nome**, introduza um nome para o seu perfil. Este nome **tem** de ser exclusivo na zona trafficmanager.net e é utilizado para criar um novo nome DNS (por exemplo, northwindstore.trafficmanager.net).
   - Para **método de encaminhamento**, selecione a **ponderado**.
   - Para **subscrição**, selecione a subscrição que pretende criar este perfil em.
   - Na **grupo de recursos**, crie um novo grupo de recursos para este perfil.
   - Em **Localização do grupo de recursos**, selecione a localização do grupo de recursos. Esta definição refere-se para a localização do grupo de recursos e não tem impacto sobre o perfil do Gestor de tráfego que vai ser implementado globalmente.

4. Selecione **Criar**.

    ![Criar perfil do Gestor de Tráfego](media/azure-stack-solution-hybrid-cloud/image19.png)

   Quando a implementação global do seu perfil do Gestor de tráfego estiver concluída, é apresentada na lista de recursos para o grupo de recursos que criou-o para baixo.

### <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

1. Procure o perfil do Gestor de tráfego que criou. (Se navegar para o grupo de recursos para o perfil, selecione o perfil.)

2. Na **perfil do Gestor de tráfego**, em **definições**, selecione **pontos finais**.

3. Selecione **Adicionar**.

4. Na **adicionar ponto final**, utilize as seguintes definições para o Azure Stack:

   - Para **tipo**, selecione **ponto final externo**.
   - Introduza um **nome** para este ponto final.
   - Para **nome de domínio completamente qualificado (FQDN) ou IP** introduz o URL externo para a sua aplicação web do Azure Stack.
   - Para **peso**, mantenha a predefinição **1**. Este peso resulta em todo o tráfego vai para este ponto final, se ele está em bom estado.
   - Deixe **adicionar como desativado** desmarcada.

5. Selecione **OK** para guardar o ponto de final do Azure Stack.

Em seguida, irá configurar o ponto final do Azure.

1. No **perfil do Gestor de tráfego**, selecione **pontos de extremidade**.
2. Selecione **+ adicionar**.
3. No **adicionar ponto final**, utilize as seguintes definições para o Azure:

   - Para **tipo**, selecione **ponto final do Azure**.
   - Introduza um **nome** para este ponto final.
   - Para **tipo de recurso de destino**, selecione **serviço de aplicações**.
   - Para **recurso de destino**, selecione **escolher um serviço de aplicações** para ver uma lista de aplicações Web na mesma subscrição.
   - Em **Recurso**, escolha o Serviço de Aplicações que pretende adicionar como o primeiro ponto final.
   - Para **peso**, selecione **2**. Isso resulta em todo o tráfego vai para este ponto final, se o ponto final primário está danificado ou tem um regra/alerta que direciona o tráfego quando acionado novamente.
   - Deixe **adicionar como desativado** desmarcada.

4. Selecione **OK** para guardar o ponto final do Azure.

Depois de ambos os pontos finais estiverem configurados, estes estão listados na **perfil do Gestor de tráfego** ao selecionar **pontos de extremidade**. O exemplo na captura de ecrã seguinte mostra dois pontos de extremidade, com informações de estado e a configuração para cada um deles.

![Pontos Finais](media/azure-stack-solution-hybrid-cloud/image20.png)

## <a name="set-up-application-insights-monitoring-and-alerting"></a>Configurar o Application Insights, monitorização e alertas

O Azure Application Insights permite-lhe monitorizar a sua aplicação e enviar alertas com base nas condições que configurar. Alguns exemplos são: a aplicação não está disponível, está com falhas ou está a mostrar problemas de desempenho.

Usará as métricas do Application Insights para criar alertas. Quando estes alertas acionam, seus aplicativos Web instância será automaticamente mudar do Azure Stack para o Azure para aumentar horizontalmente e, em seguida, voltar para o Azure stack para reduzir horizontalmente.

### <a name="create-an-alert-from-metrics"></a>Criar um alerta de métricas

Navegue para o grupo de recursos para este tutorial e, em seguida, selecione a instância do Application Insights para abrir **Application Insights**.

![Application Insights](media/azure-stack-solution-hybrid-cloud/image21.png)

Usará esta vista para criar uma alerta de ampliação e um dimensionamento no alerta.

### <a name="create-the-scale-out-alert"></a>Criar a alerta de ampliação

1. Sob **configurar**, selecione **alertas (clássico)**.
2. Selecione **Adicionar alerta de métrica (clássico)**.
3. Na **Adicionar regra**, configure o seguinte:

   - Para **Name**, introduza **Estourem em nuvem do Azure**.
   - R **Descrição** é opcional.
   - Sob **origem**, **alerta sobre**, selecione **métricas**.
   - Sob **critérios**, selecione a sua subscrição, o grupo de recursos para o perfil do Traffic Manager e o nome do perfil do Gestor de tráfego para o recurso.

4. Para **métrica**, selecione **taxa de pedidos de**.
5. Para **condição**, selecione **superior**.
6. Para **limiar**, introduza **2**.
7. Para **período**, selecione **durante os últimos 5 minutos**.
8. Sob **notificar através de**:
   - Verifique a caixa de verificação **proprietários, contribuidores e leitores do E-Mail**.
   - Introduza o seu endereço de e-mail para **adicionais do administrador email(s)**.

9. Na barra de menus, selecione **guardar**.

### <a name="create-the-scale-in-alert"></a>Criar o dimensionamento de alerta

1. Sob **configurar**, selecione **alertas (clássico)**.
2. Selecione **Adicionar alerta de métrica (clássico)**.
3. Na **Adicionar regra**, configure o seguinte:

   - Para **Name**, introduza **dimensionamento no Azure Stack**.
   - R **Descrição** é opcional.
   - Sob **origem**, **alerta sobre**, selecione **métricas**.
   - Sob **critérios**, selecione a sua subscrição, o grupo de recursos para o perfil do Traffic Manager e o nome do perfil do Gestor de tráfego para o recurso.

4. Para **métrica**, selecione **taxa de pedidos de**.
5. Para **condição**, selecione **inferior a**.
6. Para **limiar**, introduza **2**.
7. Para **período**, selecione **durante os últimos 5 minutos**.
8. Sob **notificar através de**:
   - Verifique a caixa de verificação **proprietários, contribuidores e leitores do E-Mail**.
   - Introduza o seu endereço de e-mail para **adicionais do administrador email(s)**.

9. Na barra de menus, selecione **guardar**.

Captura de ecrã seguinte mostra os alertas para ampliar e reduzir horizontalmente.

   ![Alertas (clássico)](media/azure-stack-solution-hybrid-cloud/image22.png)

## <a name="redirect-traffic-between-azure-and-azure-stack"></a>Redirecionar o tráfego entre o Azure e o Azure Stack

Pode configurar manual ou automática mudar da seu tráfego da aplicação Web a alternância entre o Azure e o Azure Stack.

### <a name="configure-manual-switching-between-azure-and-azure-stack"></a>Configurar a troca manual entre o Azure e o Azure Stack

Quando o seu Web site atingir os limiares configurados, receberá um alerta. Utilize os seguintes passos para redirecionar o tráfego manualmente para o Azure.

1. No portal do Azure, selecione o perfil do Traffic Manager.

    ![Pontos finais do Gestor de tráfego](media/azure-stack-solution-hybrid-cloud/image20.png)

2. Selecione **pontos de extremidade**.
3. Selecione o **ponto final do Azure**.
4. Sob **Status** , selecione **ativado**e, em seguida, selecione **guardar**.

    ![Ativar o ponto final do Azure](media/azure-stack-solution-hybrid-cloud/image23.png)

5. No **pontos de extremidade** para o perfil do Gestor de tráfego, selecione **ponto final externo**.
6. Sob **Status** , selecione **desativada**e, em seguida, selecione **guardar**.

    ![Desativar ponto de final do Azure Stack](media/azure-stack-solution-hybrid-cloud/image24.png)

Depois dos pontos finais estiverem configurados, o tráfego de aplicativo segue-se à sua aplicação web de escalamento horizontal do Azure em vez da aplicação web do Azure Stack.

 ![Pontos finais alterados](media/azure-stack-solution-hybrid-cloud/image25.png)

Para reverter o fluxo para o Azure Stack, utilize os passos anteriores para:

- Ativar o ponto de final do Azure Stack
- Desativar o ponto final do Azure

### <a name="configure-automatic-switching-between-azure-and-azure-stack"></a>Configurar a mudança automática entre o Azure e o Azure Stack

Também pode utilizar a monitorização do Application Insights, se seu aplicativo seja executado [sem servidor](https://azure.microsoft.com/overview/serverless-computing/) ambiente fornecido pelas funções do Azure.

Neste cenário, pode configurar o Application Insights para utilizar um webhook que chama uma aplicação de funções. Esta aplicação automaticamente ativa ou desativa um ponto de extremidade em resposta a um alerta.

Utilize os seguintes passos como um guia para configurar a alternância de tráfego automática.

1. Crie uma aplicação de função do Azure.
2. Crie uma função acionada por HTTP.
3. Importe os SDKs do Azure para o Resource Manager, o Gestor de tráfego e aplicações Web.
4. Desenvolva o código para:

   - Autenticar a sua subscrição do Azure.
   - Utilize um parâmetro que alterna entre os pontos finais do Gestor de tráfego para direcionar o tráfego para o Azure ou do Azure Stack.

5. Guardar o seu código e adicione o URL da aplicação de função com os parâmetros adequados para o **Webhook** secção das definições da regra de alerta do Application Insights.
6. O tráfego será redirecionado automaticamente quando um alerta do Application Insights é acionado.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre os padrões de Cloud do Azure, veja [padrões de conceção de Cloud](https://docs.microsoft.com/azure/architecture/patterns).
