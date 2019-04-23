---
title: Tutorial - criar um gateway de aplicação com o URL com base no caminho regras de encaminhamento - portal do Azure
description: Neste tutorial, saiba como criar URL baseado no caminho regras de encaminhamento para um gateway de aplicação e o conjunto com o portal do Azure de dimensionamento de máquina virtual.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/18/2019
ms.author: victorh
ms.openlocfilehash: 5307f7674635fd33241e1faba9bb0b7c0432d10b
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001036"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Tutorial: Criar um gateway de aplicação com o caminho com base em regras de encaminhamento com o portal do Azure

Pode utilizar o portal do Azure para configurar [regras de encaminhamento baseado no caminho de URL](url-route-overview.md) quando cria um [gateway de aplicação](overview.md). Neste tutorial, vai criar conjuntos de back-end utilizando as máquinas virtuais. Em seguida, criar regras de encaminhamento que tornam-se de que o tráfego de web chega aos servidores adequados nos agrupamentos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um gateway de aplicação
> * Crie máquinas virtuais para servidores de back-end
> * Criar conjuntos de back-end com os servidores de back-end
> * Criar um serviço de escuta de back-end
> * Criar uma regra de encaminhamento baseado no caminho

![Exemplo de encaminhamento de URL](./media/create-url-route-portal/scenario.png)

Se preferir, pode concluir este tutorial com [CLI do Azure](tutorial-url-route-cli.md) ou [Azure PowerShell](tutorial-url-route-powershell.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Uma rede virtual é necessária para a comunicação entre os recursos que criou. Neste exemplo, são criadas duas sub-redes: uma para o gateway de aplicação e a outra para os servidores de back-end. Pode criar uma rede virtual, ao mesmo tempo que cria o gateway de aplicação.

1. Selecione **New** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e, em seguida, selecione **Gateway de Aplicação** na lista Destaques.
3. Introduza estes valores para o gateway de aplicação:

   - *myAppGateway* - para o nome do gateway de aplicação.
   - *myResourceGroupAG* - para o novo grupo de recursos.

     ![Criar um novo gateway de aplicação](./media/create-url-route-portal/application-gateway-create.png)

4. Aceite os valores predefinidos para as outras definições e, em seguida, selecione **OK**.
5. Selecione **escolher uma rede virtual**, selecione **criar nova**e, em seguida, introduza estes valores para a rede virtual:

   - *myVNet* - para o nome da rede virtual.
   - *10.0.0.0/16* - para o espaço de endereços de rede virtual.
   - *myAGSubnet* - para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.

     ![Criar a rede virtual](./media/create-url-route-portal/application-gateway-vnet.png)

6. Selecione **OK** para criar a rede virtual e sub-rede.
7. Selecione **escolher um endereço IP público**, selecione **criar nova**e, em seguida, introduza o nome do endereço IP público. Neste exemplo, o endereço IP público tem o nome *myAGPublicIPAddress*. Aceite os valores predefinidos para as outras definições e, em seguida, selecione **OK**.
8. Aceite os valores predefinidos para a configuração do serviço de escuta, deixe a firewall de aplicações Web desativada e, em seguida, selecione **OK**.
9. Reveja as definições na página de resumo e, em seguida, selecione **OK** para criar os recursos de rede e o gateway de aplicação. Pode demorar alguns minutos até que o gateway de aplicação ser criado, aguarde até a conclusão da implementação com êxito antes de passar para a secção seguinte.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Selecione **todos os recursos** no menu do lado esquerdo e, em seguida, selecione **myVNet** na lista de recursos.
2. Selecione **sub-redes**e, em seguida, selecione **sub-rede**.

    ![Criar sub-rede](./media/create-url-route-portal/application-gateway-subnet.png)

3. Introduza *myBackendSubnet* para o nome da sub-rede e, em seguida, selecione **OK**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Neste exemplo, vai criar três máquinas virtuais para serem utilizadas como servidores de back-end para o gateway de aplicação. Também é instalar o IIS nas máquinas virtuais para verificar se que o gateway de aplicação foi criado com êxito.

1. Selecione **Novo**.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter** na lista Destaques.
3. Introduza estes valores para a máquina virtual:

    - *myVM1* - no nome da máquina virtual.
    - *azureuser* - no nome de utilizador do administrador.
    - *Azure123456!* para a palavra-passe.
    - Selecione **Utilizar existente** e, em seguida, selecione *myResourceGroupAG*.

4. Selecione **OK**.
5. Selecione **DS1_V2** para o tamanho da máquina virtual e selecione **selecione**.
6. Certifique-se de que **myVNet** está selecionado para a rede virtual e que a sub-rede é **myBackendSubnet**. 
7. Selecione **Desativado** para desativar o diagnóstico de arranque.
8. Selecione **OK**, reveja as definições na página de resumo e, em seguida, selecione **Criar**.

### <a name="install-iis"></a>Instalar o IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Abra a shell interativa e certifique-se de que está definido como **PowerShell**.

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

3. Criar duas máquinas virtuais mais e instalar o IIS com os passos que acabou de concluir. Introduza os nomes dos *myVM2* e *myVM3* para os nomes e valores de VMName no conjunto AzVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Criar conjuntos de back-end com as máquinas virtuais

1. Selecione **todos os recursos** e, em seguida, selecione **myAppGateway**.
2. Selecione **conjuntos de back-end**. Um conjunto predefinido foi criado automaticamente com o gateway de aplicação. Selecione **appGatewayBackendPool**.
3. Selecione **adicionar destino** para adicionar *myVM1* para appGatewayBackendPool.

    ![Adicionar servidores back-end](./media/create-url-route-portal/application-gateway-backend.png)

4. Selecione **Guardar**.
5. Selecione **conjuntos de back-end** e, em seguida, selecione **Add**.
6. Introduza um nome de *imagesBackendPool* e adicione *myVM2* usando **adicionar destino**.
7. Selecione **OK**.
8. Selecione **Add** novamente para adicionar outro conjunto de back-end com o nome *videoBackendPool* e adicione *myVM3* a ele.

## <a name="create-a-backend-listener"></a>Criar um serviço de escuta de back-end

1. Selecione **serviços de escuta** e selecione **básica**.
2. Introduza *myBackendListener* para o nome, *myFrontendPort* para o nome da porta de front-end e, em seguida *8080* como a porta para o serviço de escuta.
3. Selecione **OK**.

## <a name="create-a-path-based-routing-rule"></a>Criar uma regra de encaminhamento baseado no caminho

1. Selecione **regras** e, em seguida, selecione **baseado no caminho**.
2. Introduza *rule2* para o nome.
3. Introduza *imagens* para o nome do caminho primeiro. Introduza */images/* \* para o caminho. Selecione **imagesBackendPool** para o conjunto de back-end.
4. Introduza *vídeo* para o nome do segundo caminho. Introduza */video/* \* para o caminho. Selecione **videoBackendPool** para o conjunto de back-end.

    ![Criar uma regra baseada em caminho](./media/create-url-route-portal/application-gateway-route-rule.png)

5. Selecione **OK**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Selecione **todos os recursos**e, em seguida, selecione **myAGPublicIPAddress**.

    ![Registar o endereço IP público do gateway de aplicação](./media/create-url-route-portal/application-gateway-record-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereço do browser. Como, por exemplo, http://40.121.222.19.

    ![Testar o URL base no gateway de aplicação](./media/create-url-route-portal/application-gateway-iistest.png)

3. Altere o URL para http://&lt;ip-address&gt;: 8080/images/test.htm, substituindo &lt;endereço ip&gt; endereço e com o seu IP deverá ver algo semelhante ao seguinte exemplo:

    ![Testar o URL de imagens no gateway de aplicação](./media/create-url-route-portal/application-gateway-iistest-images.png)

4. Altere o URL para http://&lt;ip-address&gt;: 8080/video/test.htm, substituindo &lt;endereço ip&gt; endereço e com o seu IP deverá ver algo semelhante ao seguinte exemplo:

    ![Testar o URL de vídeo no gateway de aplicação](./media/create-url-route-portal/application-gateway-iistest-video.png)

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisar dos recursos que criou com o gateway de aplicação, remova o grupo de recursos. Ao remover o grupo de recursos, também remover o gateway de aplicação e todos os respetivos recursos relacionados. 

Para remover o grupo de recursos:

1. No menu da esquerda do portal do Azure, selecione **grupos de recursos**.
2. Sobre o **grupos de recursos** página, procure **myResourceGroupAG** na lista, em seguida, selecioná-lo.
3. Sobre o **página do grupo de recursos**, selecione **eliminar grupo de recursos**.
4. Introduza *myResourceGroupAG* para **tipo o nome de grupo de recursos** e, em seguida, selecione **eliminar**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o que pode fazer com o Gateway de aplicação do Azure](application-gateway-introduction.md)
