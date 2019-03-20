---
title: Criar um gateway de aplicação que aloja vários sites - portal do Azure
description: Saiba como criar um gateway de aplicação que aloja vários sites no portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 2/20/2019
ms.author: victorh
ms.openlocfilehash: 86be94404e7ab492beeebd6a467d23e68e7bce6b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58080172"
---
# <a name="create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Criar e configurar um gateway de aplicação para alojar vários web sites com o portal do Azure

Pode utilizar o portal do Azure para [configurar o alojamento de vários web sites](multiple-site-overview.md) quando cria um [gateway de aplicação](overview.md). Neste artigo, vai definir os conjuntos de endereços de back-end utilizando as máquinas virtuais. Em seguida, vai configurar os serviços de escuta e as regras com base nos domínios que possui para assegurar que o tráfego Web chega aos servidores adequados nos conjuntos. Este artigo pressupõe que tiver vários domínios e utiliza os exemplos de *www.contoso.com* e *www.fabrikam.com*.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um gateway de aplicação
> * Crie máquinas virtuais para servidores de back-end
> * Criar conjuntos de back-end com os servidores de back-end
> * Criar serviços de escuta de back-end
> * Criar regras de encaminhamento
> * Criar um registo CNAME no seu domínio

![Exemplo de encaminhamento multilocal](./media/create-multiple-sites-portal/scenario.png)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Uma rede virtual é necessária para a comunicação entre os recursos que criar. Neste exemplo, são criadas duas sub-redes: uma para o gateway de aplicação e a outra para os servidores de back-end. Pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicação.

1. Clique em **New** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e, em seguida, selecione **Gateway de Aplicação** na lista Destaques.
3. Introduza estes valores para o gateway de aplicação:

   - *myAppGateway* - para o nome do gateway de aplicação.
   - *myResourceGroupAG* - para o novo grupo de recursos.

     ![Criar um novo gateway de aplicação](./media/create-multiple-sites-portal/application-gateway-create.png)

4. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
5. Clique em **escolher uma rede virtual**, clique em **criar nova**e, em seguida, introduza estes valores para a rede virtual:

   - *myVNet* - para o nome da rede virtual.
   - *10.0.0.0/16* - para o espaço de endereços de rede virtual.
   - *myAGSubnet* - para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.

     ![Criar a rede virtual](./media/create-multiple-sites-portal/application-gateway-vnet.png)

6. Clique em **OK** para criar a rede virtual e a sub-rede.
7. Clique em **escolher um endereço IP público**, clique em **criar nova**e, em seguida, introduza o nome do endereço IP público. Neste exemplo, o endereço IP público tem o nome *myAGPublicIPAddress*. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
8. Aceite os valores predefinidos para a configuração do serviço de escuta, deixe a firewall de aplicações Web desativada e, em seguida, clique em **OK**.
9. Reveja as definições na página de resumo e, em seguida, clique em **OK** para criar os recursos de rede e o gateway de aplicação. Pode demorar alguns minutos até que o gateway de aplicação ser criado, aguarde até a conclusão da implementação com êxito antes de passar para a secção seguinte.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Clique em **Todos os recursos** no menu da esquerda e, em seguida, clique em **myVNet** na lista de recursos.
2. Clique em **sub-redes**e, em seguida, clique em **sub-rede**.

    ![Criar sub-rede](./media/create-multiple-sites-portal/application-gateway-subnet.png)

3. Introduza *myBackendSubnet* para o nome da sub-rede e clique em **OK**.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Neste exemplo, vai criar duas máquinas virtuais para serem utilizadas como servidores de back-end para o gateway de aplicação. Também é instalar o IIS nas máquinas virtuais para verificar que é o encaminhamento de tráfego corretamente.

1. Clique em **Novo**.
2. Clique em **computação** e, em seguida, selecione **do Windows Server 2016 Datacenter** na lista em destaque.
3. Introduza estes valores para a máquina virtual:

    - *contosoVM* - no nome da máquina virtual.
    - *azureuser* - no nome de utilizador do administrador.
    - *Azure123456!* para a palavra-passe.
    - Selecione **Utilizar existente** e, em seguida, selecione *myResourceGroupAG*.

4. Clique em **OK**.
5. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
6. Certifique-se de que **myVNet** está selecionado para a rede virtual e que a sub-rede é **myBackendSubnet**. 
7. Clique em **Desativado** para desativar o diagnóstico de arranque.
8. Clique em **OK**, reveja as definições na página de resumo e, em seguida, clique em **Criar**.

### <a name="install-iis"></a>Instalar o IIS

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Abra a shell interativa e certifique-se de que está definida como **PowerShell**.

    ![Instalar uma extensão personalizada](./media/create-multiple-sites-portal/application-gateway-extension.png)

2. Execute o comando seguinte para instalar o IIS na máquina virtual: 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. Criar a segunda máquina virtual e instalar o IIS com os passos que acabou de concluir. Introduza os nomes dos *fabrikamVM* para o nome e o valor de VMName no conjunto AzVMExtension.

## <a name="create-backend-pools-with-the-virtual-machines"></a>Criar conjuntos de back-end com as máquinas virtuais

1. Clique em **todos os recursos** e, em seguida, clique em **myAppGateway**.
2. Clique em **conjuntos de back-end**e, em seguida, clique em **Add**.
3. Introduza um nome de *contosoPool* e adicione *contosoVM* usando **adicionar destino**.

    ![Adicionar servidores back-end](./media/create-multiple-sites-portal/application-gateway-multisite-backendpool.png)

4. Clique em **OK**.
5. Clique em **conjuntos de back-end** e, em seguida, clique em **Add**.
6. Criar a *fabrikamPool* com o *fabrikamVM* com os passos que acabou de concluir.

## <a name="create-backend-listeners"></a>Criar serviços de escuta de back-end

1. Clique em **serviços de escuta** e, em seguida, clique em **multilocal**.
2. Introduza estes valores para o serviço de escuta:
    
   - *contosoListener* - no nome do serviço de escuta.
   - *www.contoso.com* -substitua este exemplo de nome de anfitrião com o seu nome de domínio.

3. Clique em **OK**.
4. Criar um segundo ouvinte usando o nome da *fabrikamListener* e utilizar o seu nome de domínio de segundo. Neste exemplo, *www.fabrikam.com* é utilizado.

![Serviços de escuta MULT-sites](media/create-multiple-sites-portal/be-listeners.png)

## <a name="create-routing-rules"></a>Criar regras de encaminhamento

As regras são processadas pela ordem em que são apresentadas, e o tráfego é direcionado com a primeira regra que corresponder, independentemente da especificidade. Por exemplo, se tiver uma regra com um serviço de escuta básico e uma regra com uma escuta de vários sites, ambas na mesma porta, a regra com o serviço de escuta de vários sites tem de estar listada antes da regra com o serviço de escuta básico, para que a regra de vários sites funcione conforme esperado. 

Neste exemplo, vai criar duas novas regras e eliminar a regra predefinida que foi criada quando criou o gateway de aplicação. 

1. Clique em **regras** e, em seguida, clique em **básica**.
2. Introduza *contosoRule* para o nome.
3. Selecione *contosoListener* para o serviço de escuta.
4. Selecione *contosoPool* para o conjunto de back-end.

    ![Criar uma regra baseada em caminho](./media/create-multiple-sites-portal/application-gateway-multisite-rule.png)

5. Clique em **OK**.
6. Crie uma segunda regra usando os nomes dos *fabrikamRule*, *fabrikamListener*, e *fabrikamPool*.
7. Eliminar a regra predefinida com o nome *rule1* clicar nele e, em seguida, clicando em **eliminar**.

## <a name="create-a-cname-record-in-your-domain"></a>Criar um registo CNAME no seu domínio

Depois de criar o gateway de aplicação com o respetivo endereço IP público, pode obter o endereço DNS e utilizá-lo para criar um registo CNAME no seu domínio. Não é recomendada a utilização de registos A, uma vez que o VIP pode ser alterado no reinício do gateway de aplicação.

1. Clique em **todos os recursos**e, em seguida, clique em **myAGPublicIPAddress**.

    ![Gateway de aplicação de registo endereço DNS](./media/create-multiple-sites-portal/application-gateway-multisite-dns.png)

2. Copie o endereço DNS e utilizá-lo como o valor para um novo registo CNAME no seu domínio.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Introduza o nome de domínio na barra de endereço do seu browser. Como, por exemplo, http://www.contoso.com.

    ![Testar o site contoso no gateway de aplicação](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Altere o endereço para o outro domínio, e deverá ver algo semelhante ao seguinte exemplo:

    ![Testar o site fabrikam no gateway de aplicação](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>Passos Seguintes

[Configurar o serviço de aplicações com o Gateway de aplicação](create-web-app.md)