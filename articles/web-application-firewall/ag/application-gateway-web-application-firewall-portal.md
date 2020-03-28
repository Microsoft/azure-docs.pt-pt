---
title: 'Tutorial: Criar usando portal - Firewall de aplicação web'
description: Neste tutorial, aprende-se a criar um portal de aplicação com uma Firewall de aplicação Web utilizando o portal Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 68a9f051bf3d59cbf32377cb503e9ded0a54d5e9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74049227"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Tutorial: Criar um portal de aplicação com uma Firewall de aplicação Web utilizando o portal Azure

Este tutorial mostra-lhe como usar o portal Azure para criar um Gateway de Aplicação com uma Firewall de Aplicação Web (WAF). A WAF utiliza regras de [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) para proteger a sua aplicação. Estas regras incluem a proteção contra ataques, tais como injeção SQL, ataques de scripts entre sites e assunção de controlo de sessão sem autorização. Depois de criar o portal de aplicação, testa-o para se certificar de que está a funcionar corretamente. Com o Portal de Aplicações Azure, direciona o tráfego web da sua aplicação para recursos específicos, atribuindo aos ouvintes aos portos, criando regras e adicionando recursos a um pool de backend. Por uma questão de simplicidade, este tutorial usa uma configuração simples com um IP frontal público, um ouvinte básico para hospedar um único site nesta porta de aplicação, duas máquinas virtuais usadas para a piscina de backend, e uma regra básica de encaminhamento de pedidos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um gateway de aplicação com a WAF ativada
> * Criar as máquinas virtuais usadas como servidores de backend
> * Criar uma conta de armazenamento e configurar o diagnóstico
> * Testar o gateway de aplicação

![Exemplo de firewall de aplicações Web](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para que o Azure se comunique entre recursos, precisa de uma rede virtual. Pode criar uma nova rede virtual ou utilizar uma existente. Neste exemplo, cria-se uma nova rede virtual. Pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicação. As instâncias de Gateway de aplicação são criadas em subredes separadas. Cria-se duas subredes neste exemplo: uma para o portal da aplicação e outra para os servidores de backend.

Selecione **Criar um recurso** no menu esquerdo do portal Azure. A **nova** janela aparece.

Selecione **Networking** e, em seguida, selecione **Gateway de aplicação** na lista **Em destaque.**

### <a name="basics-tab"></a>Separador básico

1. No separador **Basics,** introduza estes valores para as seguintes definições de gateway de aplicação:

   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos. Se não existir, selecione **Criar novo** para criá-lo.
   - Nome de gateway da **aplicação**: Insira *myAppGateway* para o nome do gateway da aplicação.
   - **Nível**: selecione **WAF V2**.

     ![Criar novo portal de aplicações: Básicos](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  Para que o Azure se comunique entre os recursos que cria, precisa de uma rede virtual. Pode criar uma nova rede virtual ou utilizar uma existente. Neste exemplo, criará uma nova rede virtual ao mesmo tempo que cria o gateway da aplicação. As instâncias de Gateway de aplicação são criadas em subredes separadas. Cria-se duas subredes neste exemplo: uma para o portal da aplicação e outra para os servidores de backend.

    Sob **a rede virtual Configure, crie**uma nova rede virtual selecionando criar **nova**. Na janela **de rede virtual Create** que se abre, introduza os seguintes valores para criar a rede virtual e duas subredes:

    - **Nome**: Introduza *myVNet* para o nome da rede virtual.

    - **Nome da sub-rede** (sub-rede de gateway de aplicação): A grelha **de sub-redes** mostrará uma subnetchamada *Padrão*. Mude o nome desta subnet para *myAGSubnet*.<br>A sub-rede de gateway de aplicação só pode conter gateways de aplicação. Não são permitidos outros recursos.

    - **Nome da sub-rede** (sub-rede do servidor de backend): Na segunda linha da grelha **subnets,** introduza *myBackendSubnet* na coluna de **nome Subnet.**

    - Intervalo de **endereços** (sub-rede de servidor de backend): Na segunda linha da Grelha de **Subnets,** introduza uma gama de endereços que não se sobreponha à gama de endereços da *myAGSubnet*. Por exemplo, se a gama de endereços do *myAGSubnet* for de 10.0.0.0.0/24, insira *10.0.1.0/24* para a gama de endereços do *myBackendSubnet*.

    Selecione **OK** para fechar a janela de **rede virtual Criar** e guardar as definições de rede virtual.

     ![Criar novo portal de aplicações: rede virtual](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. No separador **Basics,** aceite os valores predefinidos para as outras definições e, em seguida, selecione **Seguinte: Frontends**.

### <a name="frontends-tab"></a>Separador frontends

1. No separador **Frontends,** verifique se o tipo de **endereço IP frontend** está definido para **público**. <br>Pode configurar o IP Frontend para ser público ou privado de acordo com o seu caso de utilização. Neste exemplo, você escolherá um IP Frontend Público.
   > [!NOTE]
   > Para o Gateway de aplicação v2 SKU, só pode escolher a configuração IP frontal **pública.** A configuração IP frontal privada não está ativada para este V2 SKU.

2. Escolha **Criar novo** para o endereço IP **público** e introduza *myAGPublicIPAddress* para o nome de endereço IP público e, em seguida, selecione **OK**. 

     ![Criar novo portal de aplicações: frontends](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. Selecione **Seguinte: Backends**.

### <a name="backends-tab"></a>Separador backends

A piscina de backend é usada para encaminhar pedidos para os servidores backend que servem o pedido. As piscinas backend podem ser compostas por NICs, conjuntos de escala de máquinas virtuais, IPs públicos, IPs internos, nomes de domínio totalmente qualificados (FQDN), e back-ends multi-inquilinos como o Azure App Service. Neste exemplo, você vai criar uma piscina de backend vazia com o seu gateway de aplicação e, em seguida, adicionar alvos de backend para a piscina de backend.

1. No **separador Backends,** **selecione +Adicione uma piscina de backend**.

2. No Add uma janela de **piscina de backend** que se abre, insira os seguintes valores para criar uma piscina de backend vazia:

    - **Nome**: Introduza *myBackendPool* para obter o nome da piscina de backend.
    - **Adicione piscina de backend sem alvos**: Selecione **Sim** para criar uma piscina de backend sem alvos. Vai adicionar alvos de backend depois de criar o portal de aplicação.

3. Na janela adicionar uma janela de **piscina de backend,** selecione **Adicionar** para salvar a configuração da piscina de backend e voltar ao **separador Backends.**

     ![Criar nova porta de aplicação: backends](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. No separador **Backends,** selecione **Seguinte: Configuração**.

### <a name="configuration-tab"></a>Separador de configuração

No separador **Configuração,** irá ligar a piscina frontal e traseira que criou utilizando uma regra de encaminhamento.

1. **Selecione Adicionar uma regra** na coluna de regras de **encaminhamento.**

2. Na janela de **regras de encaminhamento adicionar** que se abre, *introduza myRoutingRule* para o **nome da regra**.

3. Uma regra de encaminhamento requer um ouvinte. No separador **Ouvinte** dentro da janela **de regra de encaminhamento,** introduza os seguintes valores para o ouvinte:

    - **Nome do ouvinte**: Insira *o meu Ouvinte* para o nome do ouvinte.
    - **Ip Frontend**: Selecione **Público** para escolher o IP público que criou para o frontend.
  
      Aceite os valores predefinidos para as outras definições no separador **Listener** e, em seguida, selecione o separador de **alvos Backend** para configurar o resto da regra de encaminhamento.

   ![Criar novo portal de aplicações: ouvinte](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. No separador de **alvos Backend,** selecione **myBackendPool** para o **alvo Backend**.

5. Para a **definição HTTP,** selecione **Criar novo** para criar uma nova definição HTTP. A definição http determinará o comportamento da regra de encaminhamento. Na janela **de definição http** que se abre, introduza *myHTTPSetting* para o nome de **definição HTTP**. Aceite os valores predefinidos para as outras definições na janela **de definição http** e, em seguida, selecione **Adicionar** para voltar à janela de regra **de encaminhamento Adicionar.** 

     ![Criar novo gateway de aplicação: Definição HTTP](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. Na janela **de regras de encaminhamento,** selecione **Adicionar** para salvar a regra de encaminhamento e voltar ao separador **Configuração.**

     ![Criar nova porta de aplicação: regra de encaminhamento](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. Selecione **Seguinte: Tags** e **depois Seguinte: Rever + criar**.

### <a name="review--create-tab"></a>Rever + criar separador

Reveja as definições no **separador Review + criar** e, em seguida, selecione **Criar** para criar a rede virtual, o endereço IP público e o gateway da aplicação. Pode levar vários minutos para o Azure criar o portal de aplicação. 

Aguarde até que a colocação termine com sucesso antes de passar para a secção seguinte.

## <a name="add-backend-targets"></a>Adicionar alvos de backend

Neste exemplo, utilizará as máquinas virtuais como o backend do alvo. Pode utilizar as máquinas virtuais existentes ou criar novas. Vai criar duas máquinas virtuais que o Azure utiliza como servidores de backend para o gateway da aplicação.

Para fazer isto, vai:

1. Crie dois novos VMs, *myVM* e *myVM2,* para serem usados como servidores de backend.
2. Instale o IIS nas máquinas virtuais para verificar se o gateway da aplicação foi criado com sucesso.
3. Adicione os servidores de backend à piscina de backend.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No portal Azure, selecione **Criar um recurso**. A **nova** janela aparece.
2. Selecione **O Datacenter do Windows Server 2016** na lista **Popular.** Aparece a página **Criar uma máquina virtual.**<br>O Application Gateway pode encaminhar o tráfego para qualquer tipo de máquina virtual utilizada na sua piscina de backend. Neste exemplo, utiliza um Datacenter Do Windows Server 2016.
3. Introduza estes **valores** no separador Basics para as seguintes definições de máquina virtual:

    - **Grupo de recursos**: Selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome**da máquina virtual : Introduza *myVM* para o nome da máquina virtual.
    - **Nome de utilizador**: *Introduza azureuser* para o nome de utilizador do administrador.
    - **Palavra-passe**: Insira *O Azure123456!* para a senha do administrador.
4. Aceite as outras predefinições e, em seguida, selecione **Seguinte: Discos**.  
5. Aceite as predefinições do separador **De Discos** e, em seguida, selecione **Seguinte: Networking**.
6. No separador **Networking,** verifique se o **myVNet** está selecionado para a **rede Virtual** e a **Subnet** está definida para **myBackendSubnet**. Aceite as outras predefinições e, em seguida, selecione **Seguinte: Gestão**.<br>O Application Gateway pode comunicar com instâncias fora da rede virtual em que se encontra, mas é necessário garantir que há conectividade IP.
7. No separador **Gestão,** desloque os **diagnósticos da Bota** para **desligar**. Aceite as outras predefinições e, em seguida, selecione **Rever + criar**.
8. No **separador Review + criar,** rever as definições, corrigir quaisquer erros de validação e, em seguida, selecionar **Criar**.
9. Aguarde que a criação da máquina virtual esteja concluída antes de continuar.

### <a name="install-iis-for-testing"></a>Instalar o IIS para testes

Neste exemplo, instala o IIS nas máquinas virtuais apenas para verificar se o Azure criou o gateway da aplicação com sucesso.

1. Open [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Para isso, selecione **Cloud Shell** a partir da barra de navegação superior do portal Azure e, em seguida, selecione **PowerShell** a partir da lista de lançamentos. 

    ![Instalar uma extensão personalizada](../media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

2. Execute o comando seguinte para instalar o IIS na máquina virtual: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Crie uma segunda máquina virtual e instale o IIS utilizando os passos que já tinha concluído. Utilize *o myVM2* para o nome da máquina virtual e para a definição **VMName** do **cmdlet set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Adicione servidores de backend para backend pool

1. **Selecione todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **piscinas backend** do menu esquerdo.

3. Selecione **myBackendPool**.

4. Under **Targets**, selecione **Virtual machine** a partir da lista de lançamentos.

5. Em INTERFACES **VIRTUAL MACHINE** e **NETWORK,** selecione as máquinas virtuais **myVM** e **myVM2** e as suas interfaces de rede associadas a partir das listas de drop-down.

    ![Adicionar servidores back-end](../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

6. Selecione **Guardar**.

7. Aguarde que a implantação esteja concluída antes de passar ao próximo passo.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Criar uma conta de armazenamento e configurar o diagnóstico

### <a name="create-a-storage-account"></a>Criar uma conta do Storage

Para este artigo, o gateway da aplicação utiliza uma conta de armazenamento para armazenar dados para fins de deteção e prevenção. Também pode utilizar registos do Monitor Azure ou do Event Hub para registar dados.

1. Selecione **Criar um recurso** no canto superior esquerdo do portal Azure.
1. Selecione **Armazenamento,** e, em seguida, **selecione a conta de armazenamento**.
1. Para *o grupo Derecursos,* selecione **myResourceGroupAG** para o grupo de recursos.
1. Digite *myagstore1* para o nome da conta de armazenamento.
1. Aceite os valores predefinidos para as outras definições e, em seguida, selecione **Rever + Criar**.
1. Reveja as definições e, em seguida, selecione **Criar**.

### <a name="configure-diagnostics"></a>Configurar o diagnóstico

Configure o diagnóstico para registar dados nos registos ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog e ApplicationGatewayFirewallLog.

1. No menu à esquerda, selecione **Todos os recursos**e, em seguida, selecione *myAppGateway*.
2. Sob monitorização, selecione **definições de Diagnóstico**.
3. **selecione Adicionar definição**de diagnóstico .
4. Introduza as definições de diagnóstico em *myDiagnosticsComo* nome para as definições de diagnóstico.
5. Selecione **Archive para uma conta**de armazenamento , e, em seguida, selecione **Configure** para selecionar a conta de armazenamento *myagstore1* que criou anteriormente e, em seguida, selecione **OK**.
6. Selecione os registos de gateway da aplicação para recolher e guardar.
7. Selecione **Guardar**.

    ![Configurar o diagnóstico](../media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="create-and-link-a-web-application-firewall-policy"></a>Criar e ligar uma política de firewall de aplicação web

Todas as personalizações e configurações da WAF estão num objeto separado, chamado de Política WAF. A política deve ser associada ao seu Gateway de Aplicação. Para criar uma política waf, consulte [Criar uma Política WAF](create-waf-policy-ag.md). Uma vez criado, pode então associar a política ao seu WAF (ou ouvinte individual) a partir da Política WAF no separador Gateways de **Aplicação Associado.** 

![Gateways de aplicação associados](../media/application-gateway-web-application-firewall-portal/associated-application-gateways.png)

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Embora o IIS não seja necessário para criar o gateway da aplicação, instalou-o para verificar se o Azure criou com sucesso o portal de aplicações. Utilize o IIS para testar o gateway de aplicação:

1. Encontre o endereço IP público para o gateway da aplicação na sua página **'Visão Geral'.** ![Endereço IP público de porta de aplicação recorde](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   Ou, pode selecionar **Todos os recursos**, insira o *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecione-o nos resultados da pesquisa. O Azure exibe o endereço IP público na página **'Overview'.**
1. Copie o endereço IP público e cole-o na barra de endereço do browser.
1. Verifique a resposta. Uma resposta válida verifica que o gateway da aplicação foi criado com sucesso e pode ligar-se com sucesso ao backend.

   ![Testar o gateway de aplicação](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o portal de aplicação, remova o grupo de recursos. Ao remover o grupo de recursos, também remove a porta de aplicação e todos os seus recursos relacionados. 

Para remover o grupo de recursos:

1. No menu esquerdo do portal Azure, selecione **Grupos de Recursos**.
2. Na página dos **grupos de Recursos,** procure o **myResourceGroupAG** na lista e, em seguida, selecione-o.
3. Na página do **grupo Recursos,** selecione **Eliminar o grupo de recursos**.
4. Introduza *o myResourceGroupAG* para **ESCREVER O NOME DE GRUPO DE RECURSOS** e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre firewall de aplicação web](../overview.md)
