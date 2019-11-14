---
title: 'Tutorial: criar usando o portal-Firewall do aplicativo Web'
description: Neste tutorial, você aprenderá a criar um gateway de aplicativo com um firewall do aplicativo Web usando o portal do Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 68a9f051bf3d59cbf32377cb503e9ded0a54d5e9
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049227"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Tutorial: criar um gateway de aplicativo com um firewall do aplicativo Web usando o portal do Azure

Este tutorial mostra como usar o portal do Azure para criar um gateway de aplicativo com um firewall do aplicativo Web (WAF). A WAF utiliza regras de [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) para proteger a sua aplicação. Estas regras incluem a proteção contra ataques, tais como injeção SQL, ataques de scripts entre sites e assunção de controlo de sessão sem autorização. Depois de criar o gateway de aplicativo, você o testará para verificar se ele está funcionando corretamente. Com o gateway de Aplicativo Azure, você direciona o tráfego da Web do aplicativo para recursos específicos atribuindo ouvintes a portas, criando regras e adicionando recursos a um pool de back-end. Para simplificar, este tutorial usa uma configuração simples com um IP de front-end público, um ouvinte básico para hospedar um único site nesse gateway de aplicativo, duas máquinas virtuais usadas para o pool de back-end e uma regra de roteamento de solicitação básica.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um gateway de aplicação com a WAF ativada
> * Criar as máquinas virtuais usadas como servidores de back-end
> * Criar uma conta de armazenamento e configurar o diagnóstico
> * Testar o gateway de aplicação

![Exemplo de firewall de aplicações Web](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Para criar um gateway de aplicação

Para que o Azure se comunique entre os recursos, ele precisa de uma rede virtual. Você pode criar uma nova rede virtual ou usar uma existente. Neste exemplo, você cria uma nova rede virtual. Pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicação. As instâncias do gateway de aplicativo são criadas em sub-redes separadas. Você cria duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end.

Selecione **criar um recurso** no menu à esquerda da portal do Azure. A **nova** janela é exibida.

Selecione **rede** e, em seguida, selecione **Gateway de aplicativo** na lista em **destaque** .

### <a name="basics-tab"></a>Guia básico

1. Na guia **noções básicas** , insira esses valores para as seguintes configurações do gateway de aplicativo:

   - **Grupo de recursos**: selecione **myResourceGroupAG** para o grupo de recursos. Se ele não existir, selecione **criar novo** para criá-lo.
   - **Nome do gateway de aplicativo**: insira *myAppGateway* para o nome do gateway de aplicativo.
   - **Camada**: selecione **WAF v2**.

     ![Criar novo gateway de aplicativo: Noções básicas](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual. Você pode criar uma nova rede virtual ou usar uma existente. Neste exemplo, você criará uma nova rede virtual ao mesmo tempo em que criar o gateway de aplicativo. As instâncias do gateway de aplicativo são criadas em sub-redes separadas. Você cria duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end.

    Em **Configurar rede virtual**, crie uma nova rede virtual selecionando **criar nova**. Na janela **criar rede virtual** que é aberta, insira os seguintes valores para criar a rede virtual e duas sub-redes:

    - **Nome**: insira *myVNet* para o nome da rede virtual.

    - **Nome da sub-rede** (sub-rede do gateway de aplicativo): a grade de **sub-redes** mostrará uma sub-rede denominada *padrão*. Altere o nome dessa sub-rede para *myAGSubnet*.<br>A sub-rede do gateway de aplicativo pode conter somente gateways de aplicativo. Nenhum outro recurso é permitido.

    - **Nome da sub-rede** (sub-rede do servidor de back-end): na segunda linha da grade **sub-redes** , insira *MyBackendSubnet* na coluna **nome da sub-rede** .

    - **Intervalo de endereços** (sub-rede do servidor de back-end): na segunda linha da grade **sub-redes** , insira um intervalo de endereços que não se sobreponha ao intervalo de endereços de *myAGSubnet*. Por exemplo, se o intervalo de endereços de *myAGSubnet* for 10.0.0.0/24, digite *10.0.1.0/24* para o intervalo de endereços de *myBackendSubnet*.

    Selecione **OK** para fechar a janela **criar rede virtual** e salvar as configurações de rede virtual.

     ![Criar novo gateway de aplicativo: rede virtual](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. Na guia **noções básicas** , aceite os valores padrão para as outras configurações e, em seguida, selecione **Avançar: front-ends**.

### <a name="frontends-tab"></a>Guia front-ends

1. Na guia **front-ends** , verifique se **tipo de endereço IP de front-end** está definido como **público**. <br>Você pode configurar o IP de front-end para ser público ou privado de acordo com seu caso de uso. Neste exemplo, você escolherá um IP de front-end público.
   > [!NOTE]
   > Para o SKU do gateway de aplicativo v2, você só pode escolher a configuração de IP de front-end **público** . A configuração de IP de front-end privada não está habilitada no momento para este SKU v2.

2. Escolha **criar novo** para o **endereço IP público** e insira *myAGPublicIPAddress* para o nome do endereço IP público e, em seguida, selecione **OK**. 

     ![Criar novo gateway de aplicativo: front-ends](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. Selecione **Avançar: back-ends**.

### <a name="backends-tab"></a>Guia back-ends

O pool de back-end é usado para rotear solicitações para os servidores de back-end que atendem à solicitação. Os pools de back-end podem ser compostos de NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, IPs internos, FQDN (nomes de domínio totalmente qualificados) e back-ends de vários locatários como Azure App serviço. Neste exemplo, você criará um pool de back-end vazio com o gateway de aplicativo e, em seguida, adicionará destinos de back-end ao pool de back-end.

1. Na guia **back-ends** , selecione **+ Adicionar um pool de back-end**.

2. Na janela **Adicionar um pool de back-end** que é aberta, insira os seguintes valores para criar um pool de back-end vazio:

    - **Nome**: insira *myBackendPool* para o nome do pool de back-end.
    - **Adicionar pool de back-end sem destinos**: selecione **Sim** para criar um pool de back-end com nenhum destino. Você adicionará destinos de back-end depois de criar o gateway de aplicativo.

3. Na janela **Adicionar um pool de back-end** , selecione **Adicionar** para salvar a configuração do pool de back-end e retornar à guia **back-ends** .

     ![Criar novo gateway de aplicativo: back-ends](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. Na guia **back-ends** , selecione **Avançar: configuração**.

### <a name="configuration-tab"></a>Guia de configuração

Na guia **configuração** , você conectará o front-end e o pool de back-end que você criou usando uma regra de roteamento.

1. Selecione **Adicionar uma regra** na coluna **regras de roteamento** .

2. Na janela **Adicionar uma regra de roteamento** que é aberta, insira *myRoutingRule* para o **nome da regra**.

3. Uma regra de roteamento requer um ouvinte. Na guia **ouvinte** na janela **Adicionar uma regra de roteamento** , insira os seguintes valores para o ouvinte:

    - **Nome do ouvinte**: insira *MyListener* para o nome do ouvinte.
    - **IP de front-end**: selecione **público** para escolher o IP público que você criou para o front-end.
  
      Aceite os valores padrão para as outras configurações na guia **ouvinte** e, em seguida, selecione a guia **destinos de back-end** para configurar o restante da regra de roteamento.

   ![Criar novo gateway de aplicativo: ouvinte](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. Na guia **destinos de back-end** , selecione **myBackendPool** para o **destino de back-end**.

5. Para a **configuração de http**, selecione **criar novo** para criar uma nova configuração de http. A configuração HTTP determinará o comportamento da regra de roteamento. Na janela **Adicionar uma configuração de http** que é aberta, insira *myHTTPSetting* para o **nome da configuração http**. Aceite os valores padrão para as outras configurações na janela **Adicionar uma configuração de http** e, em seguida, selecione **Adicionar** para retornar à janela **Adicionar uma regra de roteamento** . 

     ![Criar novo gateway de aplicativo: configuração de HTTP](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. Na janela **Adicionar uma regra de roteamento** , selecione **Adicionar** para salvar a regra de roteamento e retornar para a guia **configuração** .

     ![Criar novo gateway de aplicativo: regra de roteamento](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. Selecione **Avançar: marcas** e **Avançar: revisar + criar**.

### <a name="review--create-tab"></a>Revisar + criar guia

Examine as configurações na guia **revisar + criar** e, em seguida, selecione **criar** para criar a rede virtual, o endereço IP público e o gateway de aplicativo. Pode levar vários minutos para que o Azure crie o gateway de aplicativo. 

Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

## <a name="add-backend-targets"></a>Adicionar destinos de back-end

Neste exemplo, você usará máquinas virtuais como o back-end de destino. Você pode usar máquinas virtuais existentes ou criar novas. Você criará duas máquinas virtuais que o Azure usa como servidores de back-end para o gateway de aplicativo.

Para fazer isso, você vai:

1. Crie duas novas VMs, *myVM* e *myVM2*, a serem usadas como servidores de back-end.
2. Instale o IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.
3. Adicione os servidores de back-end ao pool de back-end.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Na portal do Azure, selecione **criar um recurso**. A **nova** janela é exibida.
2. Selecione **Windows Server 2016 datacenter** na lista **popular** . A página **criar uma máquina virtual** é exibida.<br>O gateway de aplicativo pode rotear o tráfego para qualquer tipo de máquina virtual usada em seu pool de back-end. Neste exemplo, você usa um datacenter do Windows Server 2016.
3. Insira esses valores na guia **noções básicas** para as seguintes configurações de máquina virtual:

    - **Grupo de recursos**: selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome da máquina virtual**: insira *myVM* para o nome da máquina virtual.
    - **Username**: insira *azureuser* para o nome de usuário do administrador.
    - **Senha**: insira *Azure123456!* para a senha de administrador.
4. Aceite os outros padrões e, em seguida, selecione **Avançar: discos**.  
5. Aceite os padrões da guia **discos** e, em seguida, selecione **Avançar: rede**.
6. Na guia **rede** , verifique se **myVNet** está selecionado para a **rede virtual** e se a **sub-rede** está definida como **myBackendSubnet**. Aceite os outros padrões e, em seguida, selecione **Avançar: gerenciamento**.<br>O gateway de aplicativo pode se comunicar com instâncias fora da rede virtual em que ela está, mas você precisa garantir que haja conectividade IP.
7. Na guia **Gerenciamento** , defina **diagnóstico de inicialização** como **desativado**. Aceite os outros padrões e, em seguida, selecione **revisar + criar**.
8. Na guia **revisar + criar** , examine as configurações, corrija os erros de validação e, em seguida, selecione **criar**.
9. Aguarde a conclusão da criação da máquina virtual antes de continuar.

### <a name="install-iis-for-testing"></a>Instalar o IIS para teste

Neste exemplo, você instala o IIS nas máquinas virtuais somente para verificar se o Azure criou o gateway de aplicativo com êxito.

1. Abra [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Para fazer isso, selecione **Cloud Shell** na barra de navegação superior do portal do Azure e, em seguida, selecione **PowerShell** na lista suspensa. 

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

3. Crie uma segunda máquina virtual e instale o IIS usando as etapas que você concluiu anteriormente. Use *myVM2* para o nome da máquina virtual e para a configuração **VMName** do cmdlet **set-AzVMExtension** .

### <a name="add-backend-servers-to-backend-pool"></a>Adicionar servidores de back-end ao pool de back-end

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **pools de back-end** no menu à esquerda.

3. Selecione **myBackendPool**.

4. Em **destinos**, selecione **máquina virtual** na lista suspensa.

5. Em **adaptadores de rede**e **máquina virtual** , selecione as máquinas virtuais **myVM** e **myVM2** e suas interfaces de rede associadas nas listas suspensas.

    ![Adicionar servidores back-end](../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

6. Selecione **Guardar**.

7. Aguarde a conclusão da implantação antes de prosseguir para a próxima etapa.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Criar uma conta de armazenamento e configurar o diagnóstico

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para este artigo, o gateway de aplicativo usa uma conta de armazenamento para armazenar dados para fins de detecção e prevenção. Você também pode usar os logs de Azure Monitor ou o Hub de eventos para registrar dados.

1. Selecione **criar um recurso** no canto superior esquerdo do portal do Azure.
1. Selecione **armazenamento**e, em seguida, selecione **conta de armazenamento**.
1. Para *grupo de recursos*, selecione **myResourceGroupAG** para o grupo de recursos.
1. Digite *myagstore1* para o nome da conta de armazenamento.
1. Aceite os valores padrão para as outras configurações e, em seguida, selecione **revisar + criar**.
1. Reveja as definições e, em seguida, selecione **criar**.

### <a name="configure-diagnostics"></a>Configurar o diagnóstico

Configure o diagnóstico para registar dados nos registos ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog e ApplicationGatewayFirewallLog.

1. No menu à esquerda, selecione **todos os recursos**e, em seguida, selecione *myAppGateway*.
2. Em monitoramento, selecione **configurações de diagnóstico**.
3. Selecione **Adicionar configuração de diagnóstico**.
4. Insira *myDiagnosticsSettings* como o nome das configurações de diagnóstico.
5. Selecione **arquivar em uma conta de armazenamento**e, em seguida, selecione **Configurar** para selecionar a conta de armazenamento *myagstore1* que você criou anteriormente e, em seguida, selecione **OK**.
6. Selecione os logs do gateway de aplicativo a serem coletados e mantidos.
7. Selecione **Guardar**.

    ![Configurar o diagnóstico](../media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="create-and-link-a-web-application-firewall-policy"></a>Criar e vincular uma política de firewall do aplicativo Web

Todas as personalizações e configurações do WAF estão em um objeto separado, chamado de política WAF. A política deve ser associada ao seu gateway de aplicativo. Para criar uma política de WAF, consulte [criar uma política de WAF](create-waf-policy-ag.md). Depois de ser criado, você pode associar a política ao seu WAF (ou um ouvinte individual) da política WAF na guia **gateways de aplicativo associados** . 

![Gateways de aplicativo associados](../media/application-gateway-web-application-firewall-portal/associated-application-gateways.png)

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Embora o IIS não seja necessário para criar o gateway de aplicativo, você o instalou para verificar se o Azure criou o gateway de aplicativo com êxito. Use o IIS para testar o gateway de aplicativo:

1. Localize o endereço IP público para o gateway de aplicativo em sua página de **visão geral** .![registrar o endereço IP público do gateway de aplicativo](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   Ou, você pode selecionar **todos os recursos**, inserir *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecioná-los nos resultados da pesquisa. O Azure exibe o endereço IP público na página **visão geral** .
1. Copie o endereço IP público e cole-o na barra de endereço do browser.
1. Verifique a resposta. Uma resposta válida verifica se o gateway de aplicativo foi criado com êxito e se pode se conectar com êxito com o back-end.

   ![Testar o gateway de aplicação](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais dos recursos que criou com o gateway de aplicativo, remova o grupo de recursos. Removendo o grupo de recursos, você também remove o gateway de aplicativo e todos os seus recursos relacionados. 

Para remover o grupo de recursos:

1. No menu à esquerda da portal do Azure, selecione **grupos de recursos**.
2. Na página **grupos de recursos** , procure **myResourceGroupAG** na lista e, em seguida, selecione-o.
3. Na **página grupo de recursos**, selecione **excluir grupo de recursos**.
4. Insira *myResourceGroupAG* para **digite o nome do grupo de recursos** e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o Firewall do aplicativo Web](../overview.md)
