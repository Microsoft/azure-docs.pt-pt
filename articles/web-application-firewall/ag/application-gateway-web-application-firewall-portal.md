---
title: 'Tutorial: Criar usando o portal - Firewall de aplicação web'
description: Neste tutorial, aprende-se a criar um gateway de aplicações com uma Firewall de Aplicação Web utilizando o portal Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: 58002140cd6ec0cd90eefc506dc743be05e7be7e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274491"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Tutorial: Criar um gateway de aplicações com uma Firewall de Aplicação Web utilizando o portal Azure

Este tutorial mostra-lhe como usar o portal Azure para criar um Gateway de Aplicação com uma Firewall de Aplicação Web (WAF). A WAF utiliza regras de [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) para proteger a sua aplicação. Estas regras incluem a proteção contra ataques, tais como injeção SQL, ataques de scripts entre sites e assunção de controlo de sessão sem autorização. Depois de criar o gateway de aplicação, teste-o para se certificar de que está a funcionar corretamente. Com o Azure Application Gateway, direciona o tráfego web da sua aplicação para recursos específicos, atribuindo os ouvintes às portas, criando regras e adicionando recursos a um pool de backend. Por uma questão de simplicidade, este tutorial usa uma configuração simples com um IP frontal público, um ouvinte básico para hospedar um único site neste gateway de aplicações, duas máquinas virtuais usadas para a piscina de backend, e uma regra básica de encaminhamento de pedidos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um gateway de aplicação com a WAF ativada
> * Criar as máquinas virtuais utilizadas como servidores backend
> * Criar uma conta de armazenamento e configurar o diagnóstico
> * Testar o gateway de aplicação

![Exemplo de firewall de aplicações Web](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para o Azure comunicar entre recursos, precisa de uma rede virtual. Pode criar uma nova rede virtual ou utilizar uma existente. Neste exemplo, cria-se uma nova rede virtual. Pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicação. As instâncias do Gateway de Aplicação são criadas em sub-redes separadas. Cria duas sub-redes neste exemplo: uma para o gateway de aplicações e outra para os servidores backend.

**Selecione Criar um recurso** no menu esquerdo do portal Azure. A **nova** janela aparece.

Selecione **Networking** e, em seguida, selecione **Application Gateway** na lista **em destaque.**

### <a name="basics-tab"></a>Separador básico

1. No separador **Básicos, insira** estes valores para as seguintes definições de gateway de aplicação:

   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos. Se não existir, selecione **Criar novo** para criá-lo.
   - **Nome do gateway de aplicação**: Introduza *o myAppGateway* para o nome do gateway de aplicação.
   - **Nível**: selecione **WAF V2**.

     ![Criar novo gateway de aplicações: Básicos](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  Para que o Azure comunique entre os recursos que cria, precisa de uma rede virtual. Pode criar uma nova rede virtual ou utilizar uma existente. Neste exemplo, irá criar uma nova rede virtual ao mesmo tempo que cria o gateway de aplicações. As instâncias do Gateway de Aplicação são criadas em sub-redes separadas. Cria duas sub-redes neste exemplo: uma para o gateway de aplicações e outra para os servidores backend.

    No **âmbito da rede virtual Configure,** crie uma nova rede virtual selecionando Criar **novo**. Na janela **de rede virtual Create** que se abre, introduza os seguintes valores para criar a rede virtual e duas sub-redes:

    - **Nome**: Introduza *o myVNet* para o nome da rede virtual.

    - **Nome da sub-rede (sub-rede** Do Gateway de aplicação): A grelha **das sub-redes** apresentará uma sub-rede chamada *Predefinição*. Mude o nome desta sub-rede para *myAGSubnet*.<br>A sub-rede de gateway de aplicação pode conter apenas portais de aplicação. Não são permitidos outros recursos.

    - **Nome da sub-rede** (sub-rede do servidor de backend): Na segunda linha da grelha **sub-redes,** introduza o *myBackendSubnet* na coluna **nome sub-rede.**

    - **Intervalo de endereços** (sub-rede do servidor de backend): Na segunda linha da Grelha de **Sub-redes,** introduza um intervalo de endereços que não se sobreponha ao intervalo de endereços do *myAGSubnet*. Por exemplo, se o intervalo de endereços do *myAGSubnet* for de 10.0.0.0/24, insira *10.0.0.0/24* para o intervalo de endereços do *myBackendSubnet*.

    Selecione **OK** para fechar a janela **de rede virtual Create** e guardar as definições de rede virtual.

     ![Criar novo gateway de aplicações: rede virtual](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. No **separador Básicos,** aceite os valores predefinidos para as outras definições e, em seguida, selecione **Seguinte: Frontends**.

### <a name="frontends-tab"></a>Separador frontends

1. No **separador Frontends,** verifique se o **tipo de endereço IP frontend** está definido para **Public**. <br>Pode configurar o Frontend IP para ser público ou privado de acordo com o seu caso de utilização. Neste exemplo, você escolherá um IP de Frontend Público.
   > [!NOTE]
   > Para o Gateway de Aplicação v2 SKU, só pode escolher a configuração IP frontal **do público.** A configuração IP do frontend privado não está ativada para este V2 SKU.

2. Escolha **Criar novo** para o endereço IP **público** e insira o *myAGPublicIPAddress* para o nome do endereço IP público e, em seguida, selecione **OK**. 

     ![Criar novo gateway de aplicações: frontends](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. Selecione **Seguinte: Backends**.

### <a name="backends-tab"></a>Separador backends

O pool backend é usado para encaminhar pedidos para os servidores backend que servem o pedido. As piscinas backend podem ser compostas por NICs, conjuntos de escala de máquinas virtuais, IPs públicos, IPs internos, nomes de domínio totalmente qualificados (FQDN) e back-ends multi-inquilinos como O Azure App Service. Neste exemplo, você vai criar uma piscina de backend vazia com o seu gateway de aplicação e, em seguida, adicionar alvos de backend para a piscina de backend.

1. No **separador Backends,** selecione **+Adicione uma piscina de backend**.

2. Na janela **de piscina de backend** que se abre, introduza os seguintes valores para criar uma piscina de backend vazia:

    - **Nome**: *Insira o myBackendPool* para o nome da piscina de backend.
    - **Adicionar pool de backend sem alvos**: Selecione **Sim** para criar um pool de backend sem alvos. Irá adicionar alvos de backend depois de criar o gateway de aplicação.

3. Na janela **de reserva do pool,** selecione **Adicione** para guardar a configuração da piscina de backend e volte ao separador **Backends.**

     ![Criar novo gateway de aplicações: backends](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. No **separador Backends,** selecione **Seguinte: Configuração**.

### <a name="configuration-tab"></a>Separador de configuração

No separador **Configuração,** irá ligar o frontend e o pool de backend que criou usando uma regra de encaminhamento.

1. **Selecione Adicione uma regra** na coluna de **regras de encaminhamento.**

2. Na janela de **regra de encaminhamento Adicionar uma** janela de regra de encaminhamento que se abre, *introduza o myRoutingRule* para o **nome regra**.

3. Uma regra de encaminhamento requer um ouvinte. No **separador Ouvinte** dentro da janela De regra de encaminhamento Adicionar uma regra **de encaminhamento,** introduza os seguintes valores para o ouvinte:

    - **Nome do ouvinte**: *Insira o meu Número* para o nome do ouvinte.
    - **FRONTend IP**: Selecione **Público** para escolher o IP público que criou para o frontend.
  
      Aceite os valores predefinidos para as outras definições no **separador Listener** e, em seguida, selecione o separador **alvos de Backend** para configurar o resto da regra de encaminhamento.

   ![Criar novo gateway de aplicações: ouvinte](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. No separador **alvos de Backend,** selecione **myBackendPool** para o **alvo Backend**.

5. Para a **definição HTTP**, selecione **Criar novo** para criar uma nova definição HTTP. A definição HTTP determinará o comportamento da regra de encaminhamento. Na janela **de definição HTTP** que se abre, introduza *o myHTTPSetting* para o **nome de definição HTTP**. Aceite os valores predefinidos para as outras definições na janela **de definição HTTP** e, em seguida, selecione **Adicionar** para voltar à janela de regra **de encaminhamento Adicionar uma.** 

     ![Criar novo gateway de aplicações: definição HTTP](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. Na janela **de regra de encaminhamento,** selecione **Adicione** para guardar a regra de encaminhamento e volte ao **separador Configuração.**

     ![Criar novo gateway de aplicações: regra de encaminhamento](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. Selecione **Seguinte: Tags** e depois **Seguinte: Rever + criar**.

### <a name="review--create-tab"></a>Rever + criar separador

Reveja as definições no separador **'Rever +' criar** e, em seguida, selecione **Criar** para criar a rede virtual, o endereço IP público e o gateway de aplicações. Pode levar vários minutos para o Azure criar o gateway de aplicação. 

Aguarde até que a colocação termine com sucesso antes de passar para a secção seguinte.

## <a name="add-backend-targets"></a>Adicionar alvos de backend

Neste exemplo, utilizará as máquinas virtuais como o alvo de backend. Pode utilizar máquinas virtuais existentes ou criar novas. Irá criar duas máquinas virtuais que o Azure utiliza como servidores de backend para o gateway de aplicações.

Para fazer isto, vai:

1. Crie dois novos VMs, *myVM* e *myVM2,* para serem usados como servidores backend.
2. Instale o IIS nas máquinas virtuais para verificar se o gateway de aplicações foi criado com sucesso.
3. Adicione os servidores backend à piscina de backend.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No portal Azure, selecione **Criar um recurso**. A **nova** janela aparece.
2. Selecione **o Centro de Dados do Windows Server 2016** na lista **Popular.** Aparece a página **de máquina virtual Create a.**<br>O Application Gateway pode encaminhar o tráfego para qualquer tipo de máquina virtual utilizada na sua piscina de backend. Neste exemplo, utiliza um Datacenter Windows Server 2016.
3. Introduza estes valores no separador **Básicos** para as seguintes definições de máquina virtual:

    - **Grupo de recursos**: Selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome da máquina virtual**: Introduza *o myVM* para o nome da máquina virtual.
    - **Nome de utilizador**: *Introduza o nome de* utilizador do administrador.
    - **Senha**: Insira *Azure123456!* para a senha do administrador.
4. Aceite as outras predefinições e, em seguida, selecione **Seguinte: Discos**.  
5. Aceite as falhas do separador **Discos** e, em seguida, selecione **Seguinte: Networking**.
6. No **separador Networking,** verifique se o **myVNet** está selecionado para a **rede Virtual** e a **sub-rede** está definida para **o myBackendSubnet**. Aceite os outros incumprimentos e, em seguida, selecione **Seguinte: Gestão**.<br>O Application Gateway pode comunicar com casos fora da rede virtual em que se encontra, mas é preciso garantir que existe conectividade IP.
7. No **separador Gestão,** desata **o diagnóstico de Boot** para **desligar**. Aceite os outros predefinidos e, em seguida, **selecione Review + create**.
8. No **separador 'Rever +' criar,** rever as definições, corrigir quaisquer erros de validação e, em seguida, selecionar **Criar**.
9. Aguarde que a criação da máquina virtual esteja concluída antes de continuar.

### <a name="install-iis-for-testing"></a>Instalar o IIS para testes

Neste exemplo, instala o IIS nas máquinas virtuais apenas para verificar se o Azure criou o gateway de aplicações com sucesso.

1. Abrir [a azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Para tal, selecione **Cloud Shell** a partir da barra de navegação superior do portal Azure e, em seguida, selecione **PowerShell** da lista de drop-down. 

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

3. Crie uma segunda máquina virtual e instale o IIS utilizando os passos que já concluiu. Utilize *o myVM2* para o nome da máquina virtual e para a definição **VMName** do **cmdlet Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Adicione servidores backend para backend pool

1. Selecione **Todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **piscinas backend** do menu esquerdo.

3. Selecione **myBackendPool**.

4. Em **'Alvos'**, selecione **a máquina virtual** da lista de drop-down.

5. Em INTERFACES **DE MÁQUINA VIRTUAL** e **REDE,** selecione as máquinas virtuais **myVM** e **myVM2** e as suas interfaces de rede associadas a partir das listas de drop-down.

    ![Adicionar servidores back-end](../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

6. Selecione **Guardar**.

7. Aguarde que a colocação esteja concluída antes de avançar para o próximo passo.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Criar uma conta de armazenamento e configurar o diagnóstico

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para este artigo, o gateway de aplicações utiliza uma conta de armazenamento para armazenar dados para fins de deteção e prevenção. Também pode utilizar registos do Azure Monitor ou do Event Hub para gravar dados.

1. **Selecione Criar um recurso** no canto superior esquerdo do portal Azure.
1. Selecione **Armazenamento**e, em seguida, selecione **a conta de Armazenamento**.
1. Para *o grupo de recursos,* selecione **myResourceGroupAG** para o grupo de recursos.
1. Digite *a myagstore1* para o nome da conta de armazenamento.
1. Aceite os valores predefinidos para as outras definições e, em seguida, selecione **Review + Create**.
1. Reveja as definições e, em seguida, **selecione Criar**.

### <a name="configure-diagnostics"></a>Configurar o diagnóstico

Configure o diagnóstico para registar dados nos registos ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog e ApplicationGatewayFirewallLog.

1. No menu da esquerda, selecione **Todos os recursos**e, em seguida, selecione *myAppGateway*.
2. Em Monitorização, selecione **Definições de Diagnóstico**.
3. **selecione Adicionar a definição de diagnóstico**.
4. *Insira as minhas Definições de Diagnóstico* como o nome para as definições de diagnóstico.
5. Selecione **Archive para uma conta de armazenamento**e, em seguida, selecione **Configure** para selecionar a conta de armazenamento *myagstore1* que criou anteriormente e, em seguida, selecione **OK**.
6. Selecione os registos de gateway de aplicação para recolher e manter.
7. Selecione **Guardar**.

    ![Configurar o diagnóstico](../media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="create-and-link-a-web-application-firewall-policy"></a>Criar e ligar uma política de Firewall de aplicação web

Todas as personalizações e configurações da WAF estão num objeto separado, chamado waf policy. A apólice deve estar associada ao seu Gateway de Aplicação. Para criar uma política da WAF, consulte [Criar uma Política WAF](create-waf-policy-ag.md). Uma vez criada, pode então associar a política ao seu WAF (ou a um ouvinte individual) a partir da Política WAF no separador **Gateways de Aplicação Associado.** 

![Gateways de aplicações associados](../media/application-gateway-web-application-firewall-portal/associated-application-gateways.png)

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Embora o IIS não seja obrigado a criar o gateway de aplicações, instalou-o para verificar se o Azure criou com sucesso o gateway da aplicação. Utilize o IIS para testar o gateway de aplicações:

1. Encontre o endereço IP público para a porta de aplicação na sua página **de visão geral.** ![ Endereço IP de gateway de aplicação de registo](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   Ou, pode selecionar **Todos os recursos,** inserir *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecioná-lo nos resultados da pesquisa. O Azure exibe o endereço IP público na página **'Vista Geral'.**
1. Copie o endereço IP público e cole-o na barra de endereço do browser.
1. Verifique a resposta. Uma resposta válida verifica que o gateway de aplicações foi criado com sucesso e pode conectar-se com sucesso com o backend.

   ![Testar o gateway de aplicação](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com o gateway de aplicação, remova o grupo de recursos. Ao remover o grupo de recursos, também remove o gateway de aplicação e todos os seus recursos relacionados. 

Para remover o grupo de recursos:

1. No menu esquerdo do portal Azure, selecione **Grupos de Recursos**.
2. Na página **de grupos de recursos,** procure **o myResourceGroupAG** na lista e, em seguida, selecione-o.
3. Na **página**do grupo Recursos , selecione **Eliminar grupo de recursos**.
4. Introduza *o myResourceGroupAG* para **escrever o nome do grupo de recursos** e, em seguida, selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre firewall de aplicações web](../overview.md)
