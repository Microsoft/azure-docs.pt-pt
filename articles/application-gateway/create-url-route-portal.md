---
title: 'Tutorial: Regras de encaminhamento baseadas em percursos de URL usando portal - Gateway de aplicação Azure'
description: Neste tutorial, você aprende a criar regras de encaminhamento baseadas em url para um gateway de aplicação e conjunto de escala de máquina virtual usando o portal Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 8d48ea133aaabbe9fd44bda545d672e68c93c08d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312204"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Tutorial: Criar um portal de aplicação com regras de encaminhamento baseadas em caminhos usando o portal Azure

Pode utilizar o portal Azure para configurar [regras de encaminhamento baseadas em url](application-gateway-url-route-overview.md) quando criar um gateway de [aplicação](application-gateway-introduction.md). Neste tutorial, você cria piscinas de backend usando máquinas virtuais. Em seguida, cria regras de encaminhamento que garantem que o tráfego web chegue aos servidores apropriados nas piscinas.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um gateway de aplicação
> * Criar máquinas virtuais para servidores de backend
> * Crie piscinas de backend com os servidores de backend
> * Criar um ouvinte de backend
> * Criar uma regra de encaminhamento baseada em caminhos

![Exemplo de encaminhamento de URL](./media/application-gateway-create-url-route-portal/scenario.png)

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no portal Azure em[https://portal.azure.com](https://portal.azure.com)

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Neste exemplo, cria-se três máquinas virtuais para serem usadas como servidores de backend para o gateway da aplicação. Também instala o IIS nas máquinas virtuais para verificar se o gateway da aplicação funciona como esperado.

1. No portal Azure, selecione **Criar um recurso**.
2. Selecione **O Datacenter do Windows Server 2016** na lista Popular.
3. Introduza estes valores para a máquina virtual:

    - **Grupo de recursos**, selecione **Criar novo,** e, em seguida, digitar *o myResourceGroupAG*.
    - **Nome da máquina virtual**: *myVM1*
    - **Região**: *(EUA) Leste dos EUA*
    - **Nome de utilizador**: *azureuser*
    - **Palavra-passe**: *Azure123456!*


4. Selecione **Next:Disks**.
5. Selecione **Next:Networking**
6. Para **a rede Virtual,** selecione **Criar novos** e, em seguida, digitar estes valores para a rede virtual:

   - *myVNet* - para o nome da rede virtual.
   - *10.0.0.0/16* - para o espaço de endereços de rede virtual.
   - *myBackendSubnet* para o primeiro nome da sub-rede
   - *10.0.1.0/24* - para o espaço de endereço da sub-rede.
   - *myAGSubnet* - para o segundo nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.
7. Selecione **OK**.

8. Certifique-se de que, no âmbito da Interface de **Rede,** o **myBackendSubnet** é selecionado para a sub-rede e, em seguida, selecione **Next: Management**.
9. Selecione **Off** para desativar diagnósticos de arranque.
10. Clique em **Rever + Criar,** reveja as definições na página de resumo e, em seguida, selecione **Criar**.
11. Crie mais duas máquinas virtuais, *myVM2* e *myVM3* e coloque-as na rede virtual *MyVNet* e na subnet *myBackendSubnet.*

### <a name="install-iis"></a>Instalar o IIS

1. Abra a concha interativa e certifique-se de que está definida para **powerShell**.

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

3. Crie mais duas máquinas virtuais e instale o IIS utilizando os passos que acabou de terminar. Introduza os nomes do *myVM2* e *myVM3* para os nomes e para os valores de VMName em Set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

1. Selecione **Criar um recurso** no menu esquerdo do portal Azure. A **nova** janela aparece.

2. Selecione **Networking** e, em seguida, selecione **Gateway de aplicação** na lista **Em destaque.**

### <a name="basics-tab"></a>Separador básico

1. No separador **Basics,** introduza estes valores para as seguintes definições de gateway de aplicação:

   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos.
   - Nome de gateway da **aplicação**: Insira *myAppGateway* para o nome do gateway da aplicação.
   - **Região** - Selecione **(EUA) Leste dos EUA**.

        ![Criar novo portal de aplicações: Básicos](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Em **configurar a rede virtual, selecione** **myVNet** para o nome da rede virtual.
3. Selecione **myAGSubnet** para a sub-rede.
3. Aceite os valores predefinidos para as outras definições e, em seguida, selecione **Seguinte: Frontends**.

### <a name="frontends-tab"></a>Separador frontends

1. No separador **Frontends,** verifique se o tipo de **endereço IP frontend** está definido para **público**.

   > [!NOTE]
   > Para o Gateway de aplicação v2 SKU, só pode escolher a configuração IP frontal **pública.** A configuração IP frontal privada não está ativada para este V2 SKU.

2. Escolha **Criar novo** para o endereço IP **público** e introduza *myAGPublicIPAddress* para o nome de endereço IP público e, em seguida, selecione **OK**. 
3. Selecione **Seguinte: Backends**.

### <a name="backends-tab"></a>Separador backends

A piscina de backend é usada para encaminhar pedidos para os servidores backend que servem o pedido. As piscinas backend podem ser compostas por NICs, conjuntos de escala de máquinas virtuais, IPs públicos, IPs internos, nomes de domínio totalmente qualificados (FQDN), e back-ends multi-inquilinos como o Azure App Service.

1. No **separador Backends,** **selecione +Adicione uma piscina de backend**.

2. No Add uma janela de **piscina de backend** que se abre, insira os seguintes valores para criar uma piscina de backend vazia:

    - **Nome**: Introduza *myBackendPool* para obter o nome da piscina de backend.
3. Em **'Backend Targets'**, **Tipo de destino,** selecione a máquina **Virtual** da lista de drop-down.

5. Em **'Target'** selecione a interface de rede para **myVM1**.
6. Selecione **Adicionar**.
7. Repita para adicionar uma piscina de backend *Images* com *myVM2* como alvo, e uma piscina de *vídeo* backend com *myVM3* como alvo.
8. **Selecione Adicionar** para guardar a configuração da piscina de backend e voltar ao **separador Backends.**

4. No separador **Backends,** selecione **Seguinte: Configuração**.

### <a name="configuration-tab"></a>Separador de configuração

No separador **Configuração,** irá ligar a piscina frontal e traseira que criou utilizando uma regra de encaminhamento.

1. **Selecione Adicionar uma regra** na coluna de regras de **encaminhamento.**

2. Na janela de **regras de encaminhamento adicionar** que se abre, *introduza myRoutingRule* para o **nome da regra**.

3. Uma regra de encaminhamento requer um ouvinte. No separador **Ouvinte** dentro da janela **de regra de encaminhamento,** introduza os seguintes valores para o ouvinte:

    - **Nome do ouvinte**: Insira *o meu Ouvinte* para o nome do ouvinte.
    - **Ip Frontend**: Selecione **Público** para escolher o IP público que criou para o frontend.
    - **Porta**: Tipo *8080*
  
        Aceite os valores predefinidos para as outras definições no separador **Listener** e, em seguida, selecione o separador de **alvos Backend** para configurar o resto da regra de encaminhamento.

4. No separador de **alvos Backend,** selecione **myBackendPool** para o **alvo Backend**.

5. Para a **definição HTTP,** selecione **Criar novo** para criar uma nova definição HTTP. A definição http determinará o comportamento da regra de encaminhamento. 

6. Na janela **de definição http** que se abre, introduza *myHTTPSetting* para o nome de **definição HTTP**. Aceite os valores predefinidos para as outras definições na janela **de definição http** e, em seguida, selecione **Adicionar** para voltar à janela de regra **de encaminhamento Adicionar.**
7. Sob **o encaminhamento baseado em Path,** selecione **Adicionar vários alvos para criar uma regra baseada no caminho**.
8. Para **Caminho,** tipo */imagens/*\*.
9. Para o nome da **regra do caminho,** escreva *Imagens*.
10. Para **a definição HTTP,** selecione **myHTTPSetting**
11. Para o **alvo Backend,** selecione **Imagens**.
12. **Selecione Adicionar** para salvar a regra do caminho e voltar ao separador de **regra de encaminhamento.**
13. Repita para adicionar outra regra para Vídeo.
14. **Selecione Adicionar** para adicionar a regra de encaminhamento e voltar ao separador **Configuração.**
15. Selecione **Seguinte: Tags** e **depois Seguinte: Rever + criar**.

> [!NOTE]
> Não é necessário adicionar */* uma regra de caminho personalizado para lidar com casos predefinidos. Isto é tratado automaticamente pela reserva de backend padrão.

### <a name="review--create-tab"></a>Rever + criar separador

Reveja as definições no **separador Review + criar** e, em seguida, selecione **Criar** para criar a rede virtual, o endereço IP público e o gateway da aplicação. Pode levar vários minutos para o Azure criar o portal de aplicação. Aguarde até que a colocação termine com sucesso antes de passar para a secção seguinte.


## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. **Selecione todos os recursos**e, em seguida, selecione **myAppGateway**.

    ![Registar o endereço IP público do gateway de aplicação](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereço do browser. Tais como,\/http: /52.188.72.175:8080.

    ![Testar o URL base no gateway de aplicação](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   O ouvinte da porta 8080 encaminha este pedido para a piscina de backend padrão.

3. Mude o URL para *http://&lt;endereço&gt;ip :8080/images/test.htm,* substituindo &lt;o endereço&gt; IP pelo seu endereço IP, e deverá ver algo como o seguinte exemplo:

    ![Testar o URL de imagens no gateway de aplicação](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   O ouvinte do porto 8080 encaminha este pedido para a piscina de backend *Images.*

4. Mude o URL para *http://&lt;endereço&gt;ip :8080/video/test.htm,* substituindo &lt;o endereço&gt; IP pelo seu endereço IP, e deverá ver algo como o seguinte exemplo:

    ![Testar o URL de vídeo no gateway de aplicação](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   O ouvinte do porto 8080 encaminha este pedido para a piscina de *vídeo* backend.


## <a name="next-steps"></a>Passos seguintes

- [Habilitar fim ao TLS no Portal de Aplicação Azure](application-gateway-backend-ssl.md)
