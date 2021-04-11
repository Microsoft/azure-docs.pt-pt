---
title: 'Tutorial: regras de encaminhamento baseadas em caminhos URL usando portal - Azure Application Gateway'
description: Neste tutorial, você aprende a criar regras de encaminhamento baseadas em caminhos DE URL para um gateway de aplicação e conjunto de escala de máquina virtual usando o portal Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: b0ab3cbd2891ef1677c0d4ba7a00821d67714b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708956"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Tutorial: Criar uma porta de aplicação com regras de encaminhamento baseadas em caminhos usando o portal Azure

Pode utilizar o portal Azure para configurar [regras de encaminhamento baseadas em caminhos DE URL](./url-route-overview.md) quando criar um gateway de [aplicações](./overview.md). Neste tutorial, você cria piscinas de backend usando máquinas virtuais. Em seguida, cria regras de encaminhamento que garantem que o tráfego web chega aos servidores apropriados nas piscinas.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um gateway de aplicação
> * Criar máquinas virtuais para servidores backend
> * Criar piscinas de backend com os servidores backend
> * Criar um ouvinte de backend
> * Criar uma regra de encaminhamento baseada em caminhos

![Exemplo de encaminhamento de URL](./media/application-gateway-create-url-route-portal/scenario.png)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Neste exemplo, cria-se três máquinas virtuais para serem utilizadas como servidores de backend para o gateway de aplicações. Também instala iIS nas máquinas virtuais para verificar se o gateway de aplicações funciona como esperado.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. No portal Azure, selecione **Criar um recurso**.
2. Selecione **o Centro de Dados do Windows Server 2016** na lista Popular.
3. Introduza estes valores para a máquina virtual:

    - **Subscrição** - Selecione a sua subscrição.
    - **Grupo de recursos**, selecione **Criar novo** e, em seguida, digitar *myResourceGroupAG*.
    - **Nome da máquina virtual**: *myVM1*
    - **Região**: *(EUA) Leste dos EUA*
    - **Nome de utilizador**: Digite um nome de utilizador
    - **Senha**: Digite uma senha


4. Selecione **Seguinte:Discos**.
5. Selecione **Seguinte:Networking**
6. Para **a rede Virtual,** selecione **Criar novos** e, em seguida, digite estes valores para a rede virtual:

   - *myVNet* - para o nome da rede virtual.
   - *10.0.0.0/16* - para o espaço de endereços de rede virtual.
   - *myBackendSubnet* para o primeiro nome da sub-rede
   - *10.0.1.0/24* - para o espaço do endereço da sub-rede.
   - *myAGSubnet* - para o segundo nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.
7. Selecione **OK**.

8. Certifique-se de que, na **sub-rede,** **o myBackendSubnet** é selecionado para a sub-rede e, em seguida, selecione **Seguinte: Gestão**.
9. **Selecione Desativar** para desativar os diagnósticos de arranque.
10. Selecione **Rever + Criar,** rever as definições na página do resumo e, em seguida, selecione **Criar**.
11. Crie mais duas máquinas virtuais, *myVM2* e *myVM3* e coloque-as na rede virtual *MyVNet* e na sub-rede *myBackendSubnet.*

### <a name="install-iis"></a>Instalar o IIS

1. Abra a concha interativa e certifique-se de que está definida para **PowerShell**.

    ![Instalar uma extensão personalizada](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. Execute o comando seguinte para instalar o IIS na máquina virtual: 

    ```azurepowershell
         $publicSettings = @{ "fileUris&quot; = (,&quot;https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

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

3. Instale o IIS nas outras máquinas virtuais utilizando os passos que acabou de terminar. Utilize *o myVM2* e *o myVM3* para valores VMName em Set-AzVMExtension.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

1. **Selecione Criar um recurso** no menu esquerdo do portal Azure. A **nova** janela aparece.

2. Selecione **Networking** e, em seguida, selecione **Application Gateway** na lista **em destaque.**

### <a name="basics-tab"></a>Separador básico

1. No separador **Básicos, insira** estes valores para as seguintes definições de gateway de aplicação:

   - **Subscrição**: selecione a sua subscrição.
   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos.
   - **Nome do gateway de aplicação**: *Digite myAppGateway* para o nome do gateway de aplicação.
   - **Região** - Selecione **(EUA) Leste DOS EUA**.

        ![Criar novo gateway de aplicações: Básicos](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Na **rede virtual Configurar,** selecione **myVNet** para o nome da rede virtual.
3. Selecione **myAGSubnet** para a sub-rede.
3. Aceite os valores predefinidos para as outras definições e, em seguida, selecione **Seguinte: Frontends**.

### <a name="frontends-tab"></a>Separador frontends

1. No **separador Frontends,** verifique se o **tipo de endereço IP frontend** está definido para **Public**.

   > [!NOTE]
   > Para o Gateway de Aplicação v2 SKU, só pode escolher a configuração IP frontal **do público.** A configuração IP do frontend privado não está ativada para este V2 SKU.

2. **Selecione Adicionar novo** para o endereço IP **público** e insira *o myAGPublicIPAddress* para o nome do endereço IP público e, em seguida, selecione **OK**. 
3. Selecione **Seguinte: Backends**.

### <a name="backends-tab"></a>Separador backends

O pool backend é usado para encaminhar pedidos para os servidores backend que servem o pedido. As piscinas backend podem ser compostas por NICs, conjuntos de escala de máquinas virtuais, IPs públicos, IPs internos, nomes de domínio totalmente qualificados (FQDN) e back-ends multi-inquilinos como O Azure App Service.

1. No **separador Backends,** **selecione Adicione uma piscina de backend**.

2. Na janela **de piscina de backend** que se abre, introduza os seguintes valores para criar uma piscina de backend vazia:

    - **Nome**: *Insira o myBackendPool* para o nome da piscina de backend.
3. No **tipo Target,** selecione **a máquina virtual** da lista de drop-down.

5. No **Target** selecione a interface de rede para **o myVM1**.
6. Selecione **Adicionar**.
7. Repita para adicionar uma piscina de backend *Images* com *o myVM2* como alvo, e uma piscina de backend *vídeo* com *o myVM3* como alvo.
8. **Selecione Adicionar** para guardar a configuração da piscina de backend e voltar ao **separador Backends.**

4. No **separador Backends,** selecione **Seguinte: Configuração**.

### <a name="configuration-tab"></a>Separador de configuração

No separador **Configuração,** irá ligar o frontend e o pool de backend que criou usando uma regra de encaminhamento.

1. **Selecione Adicione uma regra de encaminhamento** na coluna de **regras de encaminhamento.**

2. Na janela de **regra de encaminhamento Adicionar uma** janela de regra de encaminhamento que se abre, *introduza o myRoutingRule* para o **nome regra**.

3. Uma regra de encaminhamento requer um ouvinte. No **separador Ouvinte** dentro da janela De regra de encaminhamento Adicionar uma regra **de encaminhamento,** digite os seguintes valores para o ouvinte:

    - **Nome do ouvinte**: *Insira o meu Número* para o nome do ouvinte.
    - **FRONTend IP**: Selecione **Público** para escolher o IP público que criou para o frontend.
    - **Porta**: Tipo *8080*
  
        Aceite os valores predefinidos para as outras definições no **separador Listener** e, em seguida, selecione o separador **alvos de Backend** para configurar o resto da regra de encaminhamento.

4. No separador **alvos de Backend,** selecione **myBackendPool** para o **alvo Backend**.

5. Para a **definição HTTP**, selecione **Adicione novo** para criar uma nova definição HTTP. A definição HTTP determinará o comportamento da regra de encaminhamento. 

6. Na janela **de definição HTTP** que se abre, introduza *o myHTTPSetting* para o **nome de definição HTTP**. Aceite os valores predefinidos para as outras definições na janela **de definição HTTP** e, em seguida, selecione **Adicionar** para voltar à janela de regra **de encaminhamento Adicionar uma.**
7. No **roteamento baseado em Path**, selecione Adicione **vários alvos para criar uma regra baseada em caminhos**.
8. Para **Caminho,** tipo */imagens/* \* .
9. Para **o nome alvo,** escreva *Imagens*.
10. Para **a definição HTTP**, selecione **myHTTPSetting**
11. Para **o alvo backend**, selecione **Images**.
12. **Selecione Adicionar** para guardar a regra do caminho e voltar ao **separador Adicionar uma regra de encaminhamento.**
13. Repita para adicionar outra regra para Vídeo.
14. **Selecione Adicionar** para adicionar a regra de encaminhamento e voltar ao **separador Configuração.**
15. Selecione **Seguinte: Tags** e depois **Seguinte: Rever + criar**.

> [!NOTE]
> Não é necessário adicionar uma regra de caminho personalizada */* * para lidar com casos predefinidos. Isto é automaticamente manuseado pelo pool de backend predefinido.

### <a name="review--create-tab"></a>Rever + criar separador

Reveja as definições no separador **'Rever +' criar** e, em seguida, selecione **Criar** para criar a rede virtual, o endereço IP público e o gateway de aplicações. Pode levar vários minutos para o Azure criar o gateway de aplicação. Aguarde até que a colocação termine com sucesso antes de passar para a secção seguinte.


## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Selecione **Todos os recursos** e, em seguida, selecione **myAppGateway**.

    ![Registar o endereço IP público do gateway de aplicação](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereço do browser. Tal como, http: \/ /52.188.72.175:8080.

    ![Testar o URL base no gateway de aplicação](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   O ouvinte na porta 8080 encaminha este pedido para a piscina de backend predefinido.

3. Altere o URL para *http:// &lt; endereço &gt; ip:8080/images/test.htm,* substituindo &lt; o endereço IP pelo seu endereço &gt; IP, e deverá ver algo como o seguinte exemplo:

    ![Testar o URL de imagens no gateway de aplicação](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   O ouvinte no porto 8080 encaminha este pedido para a piscina de backend *Images.*

4. Altere o URL para *http:// &lt; endereço ip &gt; :8080/video/test.htm,* substituindo o &lt; endereço IP pelo seu endereço &gt; IP, e deverá ver algo como o seguinte exemplo:

    ![Testar o URL de vídeo no gateway de aplicação](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   O ouvinte no porto 8080 encaminha este pedido para a piscina de backend *vídeo.*

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos conexões. Para tal, selecione o grupo de recursos e selecione **Eliminar o grupo de recursos**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ativar o fim do TLS no Gateway de Aplicações Azure](./ssl-overview.md)