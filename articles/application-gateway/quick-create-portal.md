---
title: Início Rápido - Direcionar tráfego da Web com o Gateway de Aplicação do Azure - portal do Azure | Microsoft Docs
description: Saiba como utilizar o portal do Azure para criar um Gateway de Aplicação do Azure que direciona o tráfego da Web para máquinas virtuais num conjunto de back-end.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 1/8/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 325e06cadd2593684bc610a0387829541bea30b2
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57311802"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Início rápido: Tráfego da web direto com o Gateway de aplicação do Azure - portal do Azure

Este início rápido mostra como utilizar o portal do Azure para criar rapidamente o gateway de aplicação com duas máquinas virtuais no respetivo conjunto de back-end. Em seguida, vai testá-lo para se certificar de que está a funcionar corretamente. Com o Gateway de aplicação do Azure, direcionar o tráfego de web de aplicação a recursos específicos por: serviços de escuta a atribuir às portas, criação de regras e adicionar recursos a um agrupamento de back-end.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta do Azure.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para o Azure comunicar entre os recursos que criar, ele precisa de uma rede virtual. Criar duas sub-redes neste exemplo: um para o gateway de aplicação e outro para os servidores de back-end. Pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicação.

1. Selecione **criar um recurso** no menu da esquerda do portal do Azure. O **New** é apresentada a janela.

2. Selecione **Networking** e, em seguida, selecione **Gateway de aplicação** no **em destaque** lista.

### <a name="basics-page"></a>Página de noções básicas

1. Sobre o **Noções básicas** página, introduza estes valores para as seguintes definições do gateway de aplicação:

    - **Nome**: Introduza *myAppGateway* para o nome do gateway de aplicação.
    - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos. Se não existir, selecione **criar novo** para criá-lo.

    ![Criar um novo gateway de aplicação](./media/application-gateway-create-gateway-portal/application-gateway-create.png)

2. Aceite os valores predefinidos para as outras definições e, em seguida, selecione **OK**.

### <a name="settings-page"></a>Página de definições

1. Sobre o **configurações** página, em **configuração de sub-rede**, selecione **escolher uma rede virtual**.

2. Sobre o **escolher rede virtual** página, selecione **criar nova**e, em seguida, introduza valores para as seguintes definições de rede virtual:

    - **Nome**: Introduza *myVNet* para o nome da rede virtual.

    - **Espaço de endereços**: Introduza *10.0.0.0/16* para o espaço de endereços de rede virtual.

    - **Nome da sub-rede**: Introduza *myAGSubnet* para o nome da sub-rede.<br>A sub-rede do gateway de aplicação pode conter apenas os gateways de aplicação. Não existem outros recursos são permitidos.

    - **Intervalo de endereços da sub-rede**: Introduza *10.0.0.0/24* para o intervalo de endereços da sub-rede.

    ![Criar a rede virtual](./media/application-gateway-create-gateway-portal/application-gateway-vnet.png)

3. Selecione **OK** para voltar para o **definições** página.

4. Sob **a configuração de IP de front-end**, certifique-se **tipo de endereço IP** está definida como **pública**. Sob **endereço IP público**, certifique-se **criar nova** está selecionada. 

5. Introduza *myAGPublicIPAddress* para o nome do endereço IP público. 

6. Aceite os valores predefinidos para as outras definições e, em seguida, selecione **OK**.

### <a name="summary-page"></a>Página de resumo

Reveja as definições na **resumo** página e, em seguida, selecione **OK** para criar a rede virtual, o endereço IP público e o gateway de aplicação. Pode demorar vários minutos para o Azure criar o gateway de aplicação. Aguarde até a conclusão da implementação com êxito antes de passar para a secção seguinte.

## <a name="add-a-subnet"></a>Adicionar uma sub-rede

Adicione uma sub-rede para a rede virtual que criou ao seguir estes passos:

1. Selecione **todos os recursos** no menu da esquerda do portal do Azure, introduza *myVNet* na caixa de pesquisa e, em seguida, selecione **myVNet** resultados da pesquisa.

2. Selecione **sub-redes** no menu à esquerda e em seguida, selecione **+ sub-rede**. 

    ![Criar sub-rede](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. Do **adicionar sub-rede** página, introduza *myBackendSubnet* para o **nome** da sub-rede e, em seguida, selecione **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, vai criar duas máquinas virtuais que utiliza o Azure como servidores de back-end para o gateway de aplicação. Também é instalar o IIS nas máquinas virtuais para verificar o que Azure foi criado o gateway de aplicação com êxito.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No portal do Azure, selecione **criar um recurso**. O **New** é apresentada a janela.

2. Selecione **computação** e, em seguida, selecione **do Windows Server 2016 Datacenter** no **em destaque** lista. O **criar uma máquina virtual** é apresentada a página.

3. Introduza estes valores no **Noções básicas** separador para as seguintes definições de máquina virtual:

    - **Grupo de recursos**: Selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome da máquina virtual**: Introduza *myVM* para o nome da máquina virtual.
    - **Nome de utilizador**: Introduza *azureuser* para o nome de utilizador de administrador.
    - **palavra-passe**: Introduza *Azure123456!* a palavra-passe de administrador.

4. Aceite as outras predefinições e, em seguida, selecione **seguinte: Discos**.  

5. Aceite o **discos** separador predefinições e, em seguida, selecione **seguinte: Funcionamento em rede**.

6. No **Networking** separador, certifique-se de que **myVNet** está selecionada para o **rede Virtual** e a **sub-rede** está definido como  **myBackendSubnet**. Aceite as outras predefinições e, em seguida, selecione **seguinte: Management**.

8. Sobre o **gestão** separador, defina **diagnósticos de arranque** para **desativar**. Aceite as outras predefinições e, em seguida, selecione **rever + criar**.

9. Sobre o **rever + criar** separador, reveja as definições, corrija os erros de validação e, em seguida, selecione **criar**.

10. Aguarde até a criação da máquina virtual concluir antes de continuar.

### <a name="install-iis"></a>Instalar o IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Open [do Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Para tal, selecione **Cloud Shell** na barra de navegação superior do portal do Azure e, em seguida, selecione **PowerShell** na lista pendente. 

    ![Instalar uma extensão personalizada](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. Crie uma segunda máquina virtual e instalar o IIS, utilizando os passos que concluiu anteriormente. Uso *myVM2* para o nome da máquina virtual e para o **VMName** definição do **Set-AzVMExtension** cmdlet.

### <a name="add-backend-servers"></a>Adicionar servidores back-end

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **conjuntos de back-end** no menu à esquerda. Azure criado automaticamente um conjunto predefinido, **appGatewayBackendPool**, ao criar o gateway de aplicação. 

3. Selecione **appGatewayBackendPool**.

4. Sob **destinos**, selecione **Máquina Virtual** na lista pendente.

5. Sob **máquina VIRTUAL** e **INTERFACES de rede**, selecione o **myVM** e **myVM2** máquinas virtuais e de suas redes associadas interfaces de listas pendentes.

    ![Adicionar servidores back-end](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Selecione **Guardar**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Encontrar o endereço IP público para o gateway de aplicação na respetiva **descrição geral** página.

    ![Registar o endereço IP público do gateway de aplicação](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)

    Em alternativa, pode selecionar **todos os recursos**, introduza *myAGPublicIPAddress* na pesquisa caixa e, em seguida, selecione-o nos resultados da pesquisa. Azure mostra o endereço IP público na **descrição geral** página.

2. Copie o endereço IP público e cole-o na barra de endereço do browser.

    ![Testar o gateway de aplicação](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o gateway de aplicação, remova o grupo de recursos. Ao remover o grupo de recursos, também remover o gateway de aplicação e todos os respetivos recursos relacionados. 

Para remover o grupo de recursos:
1. No menu da esquerda do portal do Azure, selecione **grupos de recursos**.
2. Sobre o **grupos de recursos** página, procure **myResourceGroupAG** na lista, em seguida, selecioná-lo.
3. Sobre o **página do grupo de recursos**, selecione **eliminar grupo de recursos**.
4. Introduza *myResourceGroupAG* para **tipo o nome de grupo de recursos** e, em seguida, selecione **eliminar**

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Gerir o tráfego da Web com um gateway de aplicação com a CLI do Azure](./tutorial-manage-web-traffic-cli.md)
