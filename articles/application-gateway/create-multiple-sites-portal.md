---
title: Tutorial-criar um gateway de aplicativo que hospede vários sites usando o portal do Azure
description: Neste tutorial, você aprenderá a criar um gateway de aplicativo que hospeda vários sites usando o portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: victorh
ms.openlocfilehash: 73a313a6244971b65ba89fb7b676610d88acabfa
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498459"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Tutorial: Criar e configurar um gateway de aplicativo para hospedar vários sites usando o portal do Azure

Você pode usar o portal do Azure para [Configurar a hospedagem de vários sites da Web](multiple-site-overview.md) ao criar um [Gateway de aplicativo](overview.md). Neste tutorial, você define pools de endereços de back-end usando máquinas virtuais. Em seguida, vai configurar os serviços de escuta e as regras com base nos domínios que possui para assegurar que o tráfego Web chega aos servidores adequados nos conjuntos. Este tutorial parte do princípio de que possui vários domínios e utiliza exemplos de *www.contoso.com* e *www.fabrikam.com*.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um gateway de aplicação
> * Criar máquinas virtuais para servidores de back-end
> * Criar pools de back-end com os servidores de back-end
> * Criar serviços de escuta de back-end
> * Criar regras de encaminhamento
> * Criar um registo CNAME no seu domínio

![Exemplo de encaminhamento multilocal](./media/create-multiple-sites-portal/scenario.png)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

1. Selecione **criar um recurso** no menu à esquerda da portal do Azure. A **nova** janela é exibida.

2. Selecione **rede** e, em seguida, selecione **Gateway de aplicativo** na lista em **destaque** .

### <a name="basics-tab"></a>Guia básico

1. Na guia **noções básicas** , insira esses valores para as seguintes configurações do gateway de aplicativo:

   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos. Se ele não existir, selecione **criar novo** para criá-lo.
   - **Nome do gateway de aplicativo**: Digite *myAppGateway* para o nome do gateway de aplicativo.

     ![Criar novo gateway de aplicativo: Noções Básicas](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual. Você pode criar uma nova rede virtual ou usar uma existente. Neste exemplo, você criará uma nova rede virtual ao mesmo tempo em que criar o gateway de aplicativo. As instâncias do gateway de aplicativo são criadas em sub-redes separadas. Você cria duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end.

    Em **Configurar rede virtual**, selecione **criar novo** para criar uma nova rede virtual. Na janela **criar rede virtual** que é aberta, insira os seguintes valores para criar a rede virtual e duas sub-redes:

    - **Nome**: Digite *myVNet* para o nome da rede virtual.

    - **Nome da sub-rede** (Sub-rede do gateway de aplicativo): A grade **sub-redes** mostrará uma sub-rede denominada *Default*. Altere o nome dessa sub-rede para *myAGSubnet*.<br>A sub-rede do gateway de aplicativo pode conter somente gateways de aplicativo. Nenhum outro recurso é permitido.

    - **Nome da sub-rede** (sub-rede do servidor de back-end): Na segunda linha da grade **sub-redes** , insira *myBackendSubnet* na coluna **nome da sub-rede** .

    - **Intervalo de endereços** (sub-rede do servidor de back-end): Na segunda linha da grade **sub-redes** , insira um intervalo de endereços que não se sobreponha ao intervalo de endereços de *myAGSubnet*. Por exemplo, se o intervalo de endereços de *myAGSubnet* for 10.0.0.0/24, digite *10.0.1.0/24* para o intervalo de endereços de *myBackendSubnet*.

    Selecione **OK** para fechar a janela **criar rede virtual** e salvar as configurações de rede virtual.

     ![Criar novo gateway de aplicativo: rede virtual](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na guia **noções básicas** , aceite os valores padrão para as outras configurações e, em **seguida, selecione Avançar: Front-ends.**

### <a name="frontends-tab"></a>Guia front-ends

1. Na guia **front-ends** , verifique se **tipo de endereço IP de front-end** está definido como **público**. <br>Você pode configurar o IP de front-end para ser público ou privado de acordo com seu caso de uso. Neste exemplo, você escolherá um IP de front-end público.
   > [!NOTE]
   > Para o SKU do gateway de aplicativo v2, você só pode escolher a configuração de IP de front-end **público** . A configuração de IP de front-end privada não está habilitada no momento para este SKU v2.

2. Escolha **criar novo** para o **endereço IP público** e insira *myAGPublicIPAddress* para o nome do endereço IP público e, em seguida, selecione **OK**. 

     ![Criar novo gateway de aplicativo: front-ends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Selecione **avançar: Back-** ends.

### <a name="backends-tab"></a>Guia back-ends

O pool de back-end é usado para rotear solicitações para os servidores de back-end que atendem à solicitação. Os pools de back-end podem ser NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, IPs internos, FQDN (nomes de domínio totalmente qualificados) e back-ends de vários locatários como Azure App serviço. Neste exemplo, você criará um pool de back-end vazio com o gateway de aplicativo e, em seguida, adicionará destinos de back-end ao pool de back-end.

1. Na guia **back-ends** , selecione **+ Adicionar um pool de back-end**.

2. Na janela **Adicionar um pool de back-end** que é aberta, insira os seguintes valores para criar um pool de back-end vazio:

    - **Nome**: Digite *contosoPool* para o nome do pool de back-end.
    - **Adicionar pool de back-end sem destinos**: Selecione **Sim** para criar um pool de back-end sem destinos. Você adicionará destinos de back-end depois de criar o gateway de aplicativo.

3. Na janela **Adicionar um pool de back-end** , selecione **Adicionar** para salvar a configuração do pool de back-end e retornar à guia **back-ends** .
4. Agora, adicione outro pool de back-end chamado *fabrikamPool*.

     ![Criar novo gateway de aplicativo: back-ends](./media/create-multiple-sites-portal/backend-pools.png)

4. Na guia **back-ends** , selecione **avançar: Configuração**.

### <a name="configuration-tab"></a>Guia de configuração

Na guia **configuração** , você conectará os pools de front-end e backend criados usando uma regra de roteamento.

1. Selecione **Adicionar uma regra** na coluna **regras de roteamento** .

2. Na janela **Adicionar uma regra de roteamento** que é aberta, insira *contosoRule* para o **nome da regra**.

3. Uma regra de roteamento requer um ouvinte. Na guia **ouvinte** na janela **Adicionar uma regra de roteamento** , insira os seguintes valores para o ouvinte:

    - **Nome**do ouvinte: Digite *contosoListener* para o nome do ouvinte.
    - **IP de front-end**: Selecione **público** para escolher o IP público que você criou para o front-end.

   Em **configurações adicionais**:
   - **Tipo**de ouvinte: Vários sites
   - **Nome do host**: **www.contoso.com**

   Aceite os valores padrão para as outras configurações na guia **ouvinte** e, em seguida, selecione a guia **destinos de back-end** para configurar o restante da regra de roteamento.

   ![Criar novo gateway de aplicativo: ouvinte](./media/create-multiple-sites-portal/routing-rule.png)

4. Na guia **destinos de back-end** , selecione **contosoPool** para o **destino de back-end**.

5. Para a **configuração de http**, selecione **criar novo** para criar uma nova configuração de http. A configuração HTTP determinará o comportamento da regra de roteamento. Na janela **Adicionar uma configuração de http** que é aberta, insira *contosoHTTPSetting* para o **nome da configuração http**. Aceite os valores padrão para as outras configurações na janela **Adicionar uma configuração de http** e, em seguida, selecione **Adicionar** para retornar à janela **Adicionar uma regra de roteamento** . 

6. Na janela **Adicionar uma regra de roteamento** , selecione **Adicionar** para salvar a regra de roteamento e retornar para a guia **configuração** .
7. Selecione **Adicionar uma regra** e adicione uma regra semelhante, ouvinte, destino de back-end e configuração de http para a Fabrikam.

     ![Criar novo gateway de aplicativo: regra de roteamento](./media/create-multiple-sites-portal/fabrikamRule.png)

7. Selecione **avançar: Marcas** e, **em seguida, avançar: Examine + criar**.

### <a name="review--create-tab"></a>Revisar + criar guia

Examine as configurações na guia **revisar + criar** e, em seguida, selecione **criar** para criar a rede virtual, o endereço IP público e o gateway de aplicativo. Pode levar vários minutos para que o Azure crie o gateway de aplicativo.

Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

## <a name="add-backend-targets"></a>Adicionar destinos de back-end

Neste exemplo, você usará máquinas virtuais como o back-end de destino. Você pode usar máquinas virtuais existentes ou criar novas. Você criará duas máquinas virtuais que o Azure usa como servidores de back-end para o gateway de aplicativo.

Para adicionar destinos de back-end, você vai:

1. Crie duas novas VMs, *contosoVM* e *fabrikamVM*, a serem usadas como servidores de back-end.
2. Instale o IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.
3. Adicione os servidores de back-end aos pools de back-end.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Na portal do Azure, selecione **criar um recurso**. A **nova** janela é exibida.
2. Selecione **computação** e, em seguida, selecione **Windows Server 2016 datacenter** na lista **popular** . A página **criar uma máquina virtual** é exibida.<br>O gateway de aplicativo pode rotear o tráfego para qualquer tipo de máquina virtual usada em seu pool de back-end. Neste exemplo, você usa um datacenter do Windows Server 2016.
3. Insira esses valores na guia **noções básicas** para as seguintes configurações de máquina virtual:

    - **Grupo de recursos**: Selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome da máquina virtual**: Digite *contosoVM* para o nome da máquina virtual.
    - **Nome de usuário**: Insira *azureuser* para o nome de usuário do administrador.
    - **Senha**: Insira *Azure123456!* para a senha de administrador.
4. Aceite os outros padrões e, em seguida **, selecione Avançar: Discos**.  
5. Aceite os padrões da guia **discos** e, em **seguida, selecione Avançar: Rede**.
6. Na guia **rede** , verifique se **myVNet** está selecionado para a **rede virtual** e se a **sub-rede** está definida como **myBackendSubnet**. Aceite os outros padrões e, em seguida **, selecione Avançar: Gerenciamento**.<br>O gateway de aplicativo pode se comunicar com instâncias fora da rede virtual em que ela está, mas você precisa garantir que haja conectividade IP.
7. Na guia **Gerenciamento** , defina **diagnóstico de inicialização** como **desativado**. Aceite os outros padrões e, em seguida, selecione revisar **+ criar**.
8. Na guia **revisar + criar** , examine as configurações, corrija os erros de validação e, em seguida, selecione **criar**.
9. Aguarde a conclusão da criação da máquina virtual antes de continuar.

### <a name="install-iis-for-testing"></a>Instalar o IIS para teste

Neste exemplo, você instala o IIS nas máquinas virtuais somente para verificar se o Azure criou o gateway de aplicativo com êxito.

1. Abra [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Para fazer isso, selecione **Cloud Shell** na barra de navegação superior do portal do Azure e, em seguida, selecione **PowerShell** na lista suspensa. 

    ![Instalar uma extensão personalizada](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Execute o comando seguinte para instalar o IIS na máquina virtual: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. Crie uma segunda máquina virtual e instale o IIS usando as etapas que você concluiu anteriormente. Use *fabrikamVM* para o nome da máquina virtual e para a configuração **VMName** do cmdlet **set-AzVMExtension** .

### <a name="add-backend-servers-to-backend-pools"></a>Adicionar servidores back-end a pools de back-end

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **pools de back-end** no menu à esquerda.

3. Selecione **contosoPool**.

4. Em **destinos**, selecione **máquina virtual** na lista suspensa.

5. Em adaptadores de **rede**e **máquina virtual** , selecione a máquina virtual **contosoVM** e a interface de rede associada nas listas suspensas.

    ![Adicionar servidores back-end](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Selecione **Guardar**.
7. Repita para adicionar o *fabrikamVM* e a interface ao *fabrikamPool*.

Aguarde a conclusão da implantação antes de prosseguir para a próxima etapa.

## <a name="create-a-www-a-record-in-your-domains"></a>Criar um registro da www A em seus domínios

Depois que o gateway de aplicativo for criado com seu endereço IP público, você poderá obter o endereço IP e usá-lo para criar um registro A em seus domínios. 

1. Clique em **todos os recursos**e, em seguida, clique em **myAGPublicIPAddress**.

    ![Registrar endereço DNS do gateway de aplicativo](./media/create-multiple-sites-portal/public-ip.png)

2. Copie o endereço IP e use-o como o valor para um novo registro *www* a em seus domínios.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Introduza o nome de domínio na barra de endereço do seu browser. Como, por exemplo, http://www.contoso.com.

    ![Testar o site contoso no gateway de aplicação](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Altere o endereço para o outro domínio, e deverá ver algo semelhante ao seguinte exemplo:

    ![Testar o site fabrikam no gateway de aplicação](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais dos recursos que criou com o gateway de aplicativo, remova o grupo de recursos. Ao remover o grupo de recursos, você também remove o gateway de aplicativo e todos os seus recursos relacionados.

Para remover o grupo de recursos:

1. No menu à esquerda da portal do Azure, selecione **grupos de recursos**.
2. Na página **grupos de recursos** , procure **myResourceGroupAG** na lista e, em seguida, selecione-o.
3. Na **página grupo de recursos**, selecione **excluir grupo de recursos**.
4. Digite *myResourceGroupAG* para **digitar o nome do grupo de recursos** e, em seguida, selecione **excluir**

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o que você pode fazer com Aplicativo Azure gateway](application-gateway-introduction.md)