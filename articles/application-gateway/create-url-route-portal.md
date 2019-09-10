---
title: Tutorial-criar um gateway de aplicativo com regras de roteamento com base em caminho de URL-portal do Azure
description: Neste tutorial, você aprenderá a criar regras de roteamento com base em caminho de URL para um gateway de aplicativo e um conjunto de dimensionamento de máquinas virtuais usando o portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: victorh
ms.openlocfilehash: 2cb21eb98e698ab44d73ada195fdcb7d7aac8839
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70844649"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Tutorial: Criar um gateway de aplicativo com regras de roteamento com base em caminho usando o portal do Azure

Você pode usar o portal do Azure para configurar [regras de roteamento com base no caminho de URL](application-gateway-url-route-overview.md) ao criar um [Gateway de aplicativo](application-gateway-introduction.md). Neste tutorial, você cria pools de back-end usando máquinas virtuais. Em seguida, você cria regras de roteamento que garantem que o tráfego da Web chegue aos servidores apropriados nos pools.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um gateway de aplicação
> * Criar máquinas virtuais para servidores de back-end
> * Criar pools de back-end com os servidores de back-end
> * Criar um ouvinte de back-end
> * Criar uma regra de roteamento com base em caminho

![Exemplo de encaminhamento de URL](./media/application-gateway-create-url-route-portal/scenario.png)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Neste exemplo, você cria três máquinas virtuais a serem usadas como servidores de back-end para o gateway de aplicativo. Você também instala o IIS nas máquinas virtuais para verificar se o gateway de aplicativo funciona conforme o esperado.

1. Na portal do Azure, selecione **criar um recurso**.
2. Selecione **Windows Server 2016 datacenter** na lista popular.
3. Introduza estes valores para a máquina virtual:

    - **Grupo de recursos**, selecione **criar novo**e, em seguida, digite *myResourceGroupAG*.
    - **Nome da máquina virtual**: *myVM1*
    - **Região**: *Digamos Leste dos EUA*
    - **Nome de usuário**: *azureuser*
    - **Senha**: *Azure123456!*


4. Selecione **Avançar: discos**.
5. Selecione **Avançar: rede**
6. Para **rede virtual**, selecione **criar novo** e digite esses valores para a rede virtual:

   - *myVNet* - para o nome da rede virtual.
   - *10.0.0.0/16* - para o espaço de endereços de rede virtual.
   - *myBackendSubnet* para o primeiro nome de sub-rede
   - *10.0.1.0/24* – para o espaço de endereços da sub-rede.
   - *myAGSubnet* – para o segundo nome de sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.
7. Selecione **OK**.

8. Verifique se em **interface de rede**, **myBackendSubnet** está selecionado para a sub-rede e, em **seguida, selecione Avançar: Gerenciamento**.
9. Selecione **desativado** para desabilitar o diagnóstico de inicialização.
10. Clique em **revisar + criar**, examine as configurações na página Resumo e, em seguida, selecione **criar**.
11. Crie duas máquinas virtuais, *myVM2* e *myVM3* , e coloque-as na rede virtual *MyVNet* e na sub-rede *myBackendSubnet* .

### <a name="install-iis"></a>Instalar o IIS

1. Abra o shell interativo e verifique se ele está definido como **PowerShell**.

    ![Instalar uma extensão personalizada](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Execute o comando seguinte para instalar o IIS na máquina virtual: 

    ```azurepowershell
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

3. Crie mais duas máquinas virtuais e instale o IIS usando as etapas que você acabou de concluir. Insira os nomes de *myVM2* e *myVM3* para os nomes e para os valores de VMName em Set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

1. Selecione **criar um recurso** no menu à esquerda da portal do Azure. A **nova** janela é exibida.

2. Selecione **rede** e, em seguida, selecione **Gateway de aplicativo** na lista em **destaque** .

### <a name="basics-tab"></a>Guia básico

1. Na guia **noções básicas** , insira esses valores para as seguintes configurações do gateway de aplicativo:

   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos.
   - **Nome do gateway de aplicativo**: Digite *myAppGateway* para o nome do gateway de aplicativo.
   - **Região** – selecione **leste**dos EUA.

        ![Criar novo gateway de aplicativo: Noções Básicas](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Em **Configurar rede virtual**, selecione **myVNet** para o nome da rede virtual.
3. Selecione **myAGSubnet** para a sub-rede.
3. Aceite os valores padrão para as outras configurações e, em **seguida, selecione Avançar: Front-ends.**

### <a name="frontends-tab"></a>Guia front-ends

1. Na guia **front-ends** , verifique se **tipo de endereço IP de front-end** está definido como **público**.

   > [!NOTE]
   > Para o SKU do gateway de aplicativo v2, você só pode escolher a configuração de IP de front-end **público** . A configuração de IP de front-end privada não está habilitada no momento para este SKU v2.

2. Escolha **criar novo** para o **endereço IP público** e insira *myAGPublicIPAddress* para o nome do endereço IP público e, em seguida, selecione **OK**. 
3. Selecione **avançar: Back-** ends.

### <a name="backends-tab"></a>Guia back-ends

O pool de back-end é usado para rotear solicitações para os servidores de back-end que atendem à solicitação. Os pools de back-end podem ser compostos de NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, IPs internos, FQDN (nomes de domínio totalmente qualificados) e back-ends de vários locatários como Azure App serviço.

1. Na guia **back-ends** , selecione **+ Adicionar um pool de back-end**.

2. Na janela **Adicionar um pool de back-end** que é aberta, insira os seguintes valores para criar um pool de back-end vazio:

    - **Nome**: Digite *myBackendPool* para o nome do pool de back-end.
3. Em **destinos de back-end**, **tipo de destino**, selecione **máquina virtual** na lista suspensa.

5. Em **destino** , selecione a interface de rede para **myVM1**.
6. Selecione **Adicionar**.
7. Repita para adicionar um pool de back-end de *imagens* com *myVM2* como o destino e um pool de back-end de *vídeo* com *myVM3* como o destino.
8. Selecione **Adicionar** para salvar a configuração do pool de back-end e retornar à guia **back-ends** .

4. Na guia **back-ends** , selecione **avançar: Configuração**.

### <a name="configuration-tab"></a>Guia de configuração

Na guia **configuração** , você conectará o front-end e o pool de back-end que você criou usando uma regra de roteamento.

1. Selecione **Adicionar uma regra** na coluna **regras de roteamento** .

2. Na janela **Adicionar uma regra de roteamento** que é aberta, insira *myRoutingRule* para o **nome da regra**.

3. Uma regra de roteamento requer um ouvinte. Na guia **ouvinte** na janela **Adicionar uma regra de roteamento** , insira os seguintes valores para o ouvinte:

    - **Nome**do ouvinte: Insira *MyListener* como o nome do ouvinte.
    - **IP de front-end**: Selecione **público** para escolher o IP público que você criou para o front-end.
    - **Porta**: Digite *8080*
  
        Aceite os valores padrão para as outras configurações na guia **ouvinte** e, em seguida, selecione a guia **destinos de back-end** para configurar o restante da regra de roteamento.

4. Na guia **destinos de back-end** , selecione **myBackendPool** para o **destino de back-end**.

5. Para a **configuração de http**, selecione **criar novo** para criar uma nova configuração de http. A configuração HTTP determinará o comportamento da regra de roteamento. 

6. Na janela **Adicionar uma configuração de http** que é aberta, insira *myHTTPSetting* para o **nome da configuração http**. Aceite os valores padrão para as outras configurações na janela **Adicionar uma configuração de http** e, em seguida, selecione **Adicionar** para retornar à janela **Adicionar uma regra de roteamento** .
7. Em **roteamento baseado em caminho**, selecione **adicionar vários destinos para criar uma regra com base no caminho**.
8. Para **caminho**, digite */images/* \*.
9. Para **nome da regra de caminho**, digite *imagens*.
10. Para **configuração de http**, selecione **myHTTPSetting**
11. Para **destino de back-end**, selecione **imagens**.
12. Selecione **Adicionar** para salvar a regra de caminho e retornar para a guia **Adicionar uma regra de roteamento** .
13. Repita para adicionar outra regra para vídeo.
14. Selecione **Adicionar** para adicionar a regra de roteamento e retornar à guia **configuração** .
15. Selecione **avançar: Marcas** e, **em seguida, avançar: Examine + criar**.

> [!NOTE]
> Você não precisa adicionar uma regra de caminho */* * personalizada para manipular casos padrão. Isso é manipulado automaticamente pelo pool de back-end padrão.

### <a name="review--create-tab"></a>Revisar + criar guia

Examine as configurações na guia **revisar + criar** e, em seguida, selecione **criar** para criar a rede virtual, o endereço IP público e o gateway de aplicativo. Pode levar vários minutos para que o Azure crie o gateway de aplicativo. Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.


## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

    ![Registar o endereço IP público do gateway de aplicação](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereço do browser. Como, http:\//52.188.72.175:8080.

    ![Testar o URL base no gateway de aplicação](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   O ouvinte na porta 8080 roteia essa solicitação para o pool de back-end padrão.

3. Altere a URL para *http://&lt;IP-address&gt;: 8080/images/test.htm*, substituindo &lt;IP-&gt; address pelo seu endereço IP e você verá algo semelhante ao exemplo a seguir:

    ![Testar o URL de imagens no gateway de aplicação](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   O ouvinte na porta 8080 roteia essa solicitação para o pool de back-end de *imagens* .

4. Altere a URL para *http://&lt;IP-address&gt;: 8080/Video/Test.htm*, substituindo &lt;IP-&gt; address pelo seu endereço IP e você verá algo semelhante ao exemplo a seguir:

    ![Testar o URL de vídeo no gateway de aplicação](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   O ouvinte na porta 8080 roteia essa solicitação para o pool de back-end de *vídeo* .


## <a name="next-steps"></a>Passos Seguintes

- [Habilitando o SSL de ponta a ponta no gateway de Aplicativo Azure](application-gateway-backend-ssl.md)
