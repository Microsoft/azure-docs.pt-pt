---
title: Tutorial-criar um gateway de aplicativo com regras de roteamento com base em caminho de URL-portal do Azure
description: Neste tutorial, você aprenderá a criar regras de roteamento com base em caminho de URL para um gateway de aplicativo e um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 07/27/2019
ms.author: victorh
ms.openlocfilehash: 63a1faa79374e72eabfbee4ece454728c3b4cc05
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597583"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Tutorial: Criar um gateway de aplicativo com regras de roteamento com base em caminho usando o portal do Azure

Você pode usar o portal do Azure para configurar [regras de roteamento com base no caminho de URL](url-route-overview.md) ao criar um [Gateway de aplicativo](overview.md). Neste tutorial, você cria pools de back-end usando máquinas virtuais. Em seguida, você cria regras de roteamento que garantem que o tráfego da Web chegue aos servidores apropriados nos pools.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um gateway de aplicação
> * Criar máquinas virtuais para servidores de back-end
> * Criar pools de back-end com os servidores de back-end
> * Criar um ouvinte de back-end
> * Criar uma regra de roteamento com base em caminho

![Exemplo de encaminhamento de URL](./media/create-url-route-portal/scenario.png)

Se preferir, você pode concluir este tutorial usando [CLI do Azure](tutorial-url-route-cli.md) ou [Azure PowerShell](tutorial-url-route-powershell.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

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

    - **Nome da sub-rede** (Sub-rede do gateway de aplicativo): A grade **sub-redes** mostrará uma sub-rede denominada *Default*. Altere o nome dessa sub-rede para *myAGSubnet*.

      A sub-rede do gateway de aplicativo pode conter somente gateways de aplicativo. Nenhum outro recurso é permitido.

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

    - **Nome**: Digite *appGatewayBackendPool* para o nome do pool de back-end.
    - **Adicionar pool de back-end sem destinos**: Selecione **Sim** para criar um pool de back-end sem destinos. Você adicionará destinos de back-end depois de criar o gateway de aplicativo.

3. Na janela **Adicionar um pool de back-end** , selecione **Adicionar** para salvar a configuração do pool de back-end e retornar à guia **back-ends** .
4. Agora, adicione mais dois pools de back-end chamados *imagesBackendPool*e *videoBackendPool*.

     ![Criar novo gateway de aplicativo: back-ends](./media/create-url-route-portal/backends.png)

4. Na guia **back-ends** , selecione **avançar: Configuração**.

### <a name="configuration-tab"></a>Guia de configuração

Na guia **configuração** , você conectará os pools de front-end e backend criados usando regras de roteamento.

1. Selecione **Adicionar uma regra** na coluna **regras de roteamento** .
2. Na janela **Adicionar uma regra de roteamento** que é aberta, insira *Rule1* para o **nome da regra**.
3. Uma regra de roteamento requer um ouvinte. Na guia **ouvinte** na janela **Adicionar uma regra de roteamento** , insira os seguintes valores para o ouvinte:

    - **Nome**do ouvinte: Digite *defaultlistener* para o nome do ouvinte.
    - **IP de front-end**: Selecione **público** para escolher o IP público que você criou para o front-end.

   Aceite os valores padrão para as outras configurações na guia **ouvinte** e, em seguida, selecione a guia **destinos de back-end** para configurar o restante da regra de roteamento.
4. Na guia **destinos de back-end** , selecione **appGatewayBackendPool** para o **destino de back-end**.

5. Para a **configuração de http**, selecione **criar novo** para criar uma nova configuração de http. A configuração HTTP determinará o comportamento da regra de roteamento. Na janela **Adicionar uma configuração de http** que é aberta, insira *myHTTPSetting* para o **nome da configuração http**. Aceite os valores padrão para as outras configurações na janela **Adicionar uma configuração de http** e, em seguida, selecione **Adicionar** para retornar à janela **Adicionar uma regra de roteamento** . 

6. Na janela **Adicionar uma regra de roteamento** , selecione **Adicionar** para salvar a regra de roteamento e retornar para a guia **configuração** .



1. Selecione **Adicionar uma regra** na coluna **regras de roteamento** .

2. Na janela **Adicionar uma regra de roteamento** que é aberta, insira *Rule2* para o **nome da regra**.

3. Uma regra de roteamento requer um ouvinte. Na guia **ouvinte** na janela **Adicionar uma regra de roteamento** , insira os seguintes valores para o ouvinte:

    - **Nome**do ouvinte: Digite *myBackendListener* para o nome do ouvinte.
    - **IP de front-end**: Selecione **público** para escolher o IP público que você criou para o front-end.
    - **Porta**: 8080

   Em **configurações adicionais**:
   - **Tipo**de ouvinte: Básica

   Aceite os valores padrão para as outras configurações na guia **ouvinte** e, em seguida, selecione a guia **destinos de back-end** para configurar o restante da regra de roteamento.

4. Na guia **destinos de back-end** , selecione **appGatewayBackendPool** para o **destino de back-end**.

5. Para a **configuração de http**, selecione *myHTTPSetting*. Aceite os valores padrão para as outras configurações na janela **Adicionar uma configuração de http** e, em seguida, selecione **Adicionar** para retornar à janela **Adicionar uma regra de roteamento** . 

1. Em **roteamento baseado em caminho**, selecione **adicionar vários destinos para criar uma regra com base no caminho**.
2. Na janela **Adicionar uma regra de caminho** , insira os seguintes valores para a regra de caminho:

   - **Caminho**: */images/\**
   - **Nome da regra de caminho**: *Imagens*
   - **Configuração de http**: selecione *myHTTPSetting*
   - **Destino de back-end**: *imagesBackendPool*
9. Selecione **Adicionar**.
10. Adicione outra regra de caminho chamada *vídeo*, com um caminho */Video/\**  e *videoBackendPool*.
11. Selecione **salvar alterações e voltar para regras de roteamento**.

    ![Adicionar uma regra de encaminhamento](media/create-url-route-portal/add-routing-rule.png)

12. Selecione **Adicionar**.

7. Selecione **avançar: Marcas** e, **em seguida, avançar: Examine + criar**.

### <a name="review--create-tab"></a>Revisar + criar guia

Examine as configurações na guia **revisar + criar** e, em seguida, selecione **criar** para criar a rede virtual, o endereço IP público e o gateway de aplicativo. Pode levar vários minutos para que o Azure crie o gateway de aplicativo.

Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.


## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Neste exemplo, você cria três máquinas virtuais a serem usadas como servidores de back-end para o gateway de aplicativo. Você também instala o IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.

1. Na portal do Azure, selecione **criar um recurso**. A **nova** janela é exibida.
2. Selecione **computação** e, em seguida, selecione **Windows Server 2016 datacenter** na lista **popular** . A página **criar uma máquina virtual** é exibida.

   O gateway de aplicativo pode rotear o tráfego para qualquer tipo de máquina virtual usada em seu pool de back-end. Neste exemplo, você usa um datacenter do Windows Server 2016.
1. Insira esses valores na guia **noções básicas** para as seguintes configurações de máquina virtual:

    - **Grupo de recursos**: Selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome da máquina virtual**: Digite *myVM1* para o nome da máquina virtual.
    - **Nome de usuário**: Insira *azureuser* para o nome de usuário do administrador.
    - **Senha**: Insira *Azure123456!* para a senha de administrador.
4. Aceite os outros padrões e, em seguida **, selecione Avançar: Discos**.  
5. Aceite os padrões da guia **discos** e, em **seguida, selecione Avançar: Rede**.
6. Na guia **rede** , verifique se **myVNet** está selecionado para a **rede virtual** e se a **sub-rede** está definida como **myBackendSubnet**. Aceite os outros padrões e, em seguida **, selecione Avançar: Gerenciamento**.

   O gateway de aplicativo pode se comunicar com instâncias fora da rede virtual em que ela está, mas você precisa garantir que haja conectividade IP.
1. Na guia **Gerenciamento** , defina **diagnóstico de inicialização** como **desativado**. Aceite os outros padrões e, em seguida, selecione revisar **+ criar**.
2. Na guia **revisar + criar** , examine as configurações, corrija os erros de validação e, em seguida, selecione **criar**.
3. Aguarde a conclusão da criação da máquina virtual antes de continuar.

### <a name="install-iis"></a>Instalar o IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Abra o shell interativo e verifique se ele está definido como **PowerShell**.

    ![Instalar uma extensão personalizada](./media/create-url-route-portal/application-gateway-extension.png)

2. Execute o comando seguinte para instalar o IIS na máquina virtual: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName myVM1 `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Crie mais duas máquinas virtuais e instale o IIS usando as etapas que você acabou de concluir. Use *myVM2* e *myVM3* para os nomes de máquina virtual e para os valores **VMName** em Set-AzVMExtension.

## <a name="add-backend-servers-to-backend-pools"></a>Adicionar servidores back-end a pools de back-end

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **pools de back-end** no menu à esquerda.

3. Selecione **appGatewayBackendPool**.

4. Em **destinos**, selecione **máquina virtual** na lista suspensa.

5. Em adaptadores de **rede**e **máquina virtual** , selecione a máquina virtual **myVM1** e a interface de rede associada nas listas suspensas.

    ![Adicionar servidores back-end](./media/create-url-route-portal/backend-pool.png)

6. Selecione **Guardar**.
7. Repita para adicionar *myVM2* e interface ao *imagesBackendPool*e, em seguida, *myVM3* e interface ao *videoBackendPool*.

Aguarde a conclusão da implantação antes de prosseguir para a próxima etapa.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Selecione **todos os recursos**e, em seguida, selecione **myAGPublicIPAddress**.

    ![Registar o endereço IP público do gateway de aplicação](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereço do browser. Como, por exemplo, http://40.121.222.19.

    ![Testar o URL base no gateway de aplicação](./media/create-url-route-portal/application-gateway-iistest.png)

3. Altere a URL para http://&lt;IP-address&gt;: 8080/images/test.htm, substituindo &lt;IP-&gt; address pelo seu endereço IP e você verá algo semelhante ao exemplo a seguir:

    ![Testar o URL de imagens no gateway de aplicação](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Altere a URL para http://&lt;IP-address&gt;: 8080/Video/Test.htm, substituindo &lt;IP-&gt; address pelo seu endereço IP e você verá algo semelhante ao exemplo a seguir:

    ![Testar o URL de vídeo no gateway de aplicação](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisar mais dos recursos que criou com o gateway de aplicativo, remova o grupo de recursos. Removendo o grupo de recursos, você também remove o gateway de aplicativo e todos os seus recursos relacionados. 

Para remover o grupo de recursos:

1. No menu à esquerda da portal do Azure, selecione **grupos de recursos**.
2. Na página **grupos de recursos** , procure **myResourceGroupAG** na lista e, em seguida, selecione-o.
3. Na **página grupo de recursos**, selecione **excluir grupo de recursos**.
4. Insira *myResourceGroupAG* para **digite o nome do grupo de recursos** e, em seguida, selecione **excluir**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o que você pode fazer com Aplicativo Azure gateway](application-gateway-introduction.md)
