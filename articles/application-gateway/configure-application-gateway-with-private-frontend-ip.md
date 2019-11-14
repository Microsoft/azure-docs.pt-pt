---
title: Configurar um ponto de extremidade do ILB (balanceador de carga interno)
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como configurar o gateway de aplicativo com um endereço IP de front-end privado
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.openlocfilehash: a9e3150a5382e4d690ddf66c43bbe51e125509d3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74075222"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Configurar um gateway de aplicativo com um ponto de extremidade de ILB (balanceador de carga interno)

Aplicativo Azure gateway pode ser configurado com um VIP voltado para a Internet ou com um ponto de extremidade interno que não é exposto à Internet (usando um IP privado para o endereço IP de front-end), também conhecido como um ponto de extremidade ILB (balanceador de carga interno). Configurar o gateway usando um endereço IP privado de front-end é útil para aplicativos de linha de negócios internos que não são expostos à Internet. Também é útil para serviços e camadas dentro de uma aplicação de várias camadas que assenta num limite de segurança que, não sendo exposto à Internet, requer a distribuição de carga round-robin, a persistência da sessão ou a terminação SSL (Secure Sockets Layer).

Este artigo orienta você pelas etapas para configurar um gateway de aplicativo com um endereço IP privado de front-end no portal do Azure.

Neste artigo, ficará a saber como:

- Criar uma configuração de IP de front-end particular para um gateway de aplicativo
- Criar um gateway de aplicativo com configuração de IP de front-end privado


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Para criar um gateway de aplicação

Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual. Você pode criar uma nova rede virtual ou usar uma existente. Neste exemplo, criaremos uma nova rede virtual. Pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicação. As instâncias do gateway de aplicativo são criadas em sub-redes separadas. Você cria duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end.

1. Clique em **novo** encontrado no canto superior esquerdo da portal do Azure.
2. Selecione **Rede** e, em seguida, selecione **Gateway de Aplicação** na lista Destaques.
3. Digite *myAppGateway* para o nome do gateway de aplicativo e *myResourceGroupAG* para o novo grupo de recursos.
4. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
5. Clique em **escolher uma rede virtual**, clique em **criar nova**e, em seguida, introduza estes valores para a rede virtual:
   - myVNet * – para o nome da rede virtual.
   - 10.0.0.0/16 *-para o espaço de endereço de rede virtual.
   - *myAGSubnet* - para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.  
     ![private-frontendip-1](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)
6. Clique em **OK** para criar a rede virtual e a sub-rede.
7. Escolha a configuração de IP de front-end como privada e, por padrão, é uma atribuição de endereço IP dinâmico. O primeiro endereço disponível da sub-rede escolhida será atribuído como o endereço IP de front-end.
8. Se você quiser escolher um IP privado do intervalo de endereços de sub-rede (alocação estática), clique na caixa **escolha um endereço IP privado específico** e especifique o endereço IP.
   > [!NOTE]
   > Uma vez alocado, o tipo de endereço IP (estático ou dinâmico) não pode ser alterado posteriormente.
9. Escolha a configuração do ouvinte para o protocolo e a porta, configuração de WAF (se necessário) e clique em OK.
    ![private-frontendip-2](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-2.png)
10. Examine as configurações na página Resumo e clique em **OK** para criar os recursos de rede e o gateway de aplicativo. Pode demorar alguns minutos até que o gateway de aplicação ser criado, aguarde até a conclusão da implementação com êxito antes de passar para a secção seguinte.

## <a name="add-backend-pool"></a>Adicionar pool de back-end

O pool de back-end é usado para rotear solicitações para os servidores de back-end que atenderão à solicitação. O back-end pode ser composto por NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, IPs internos, FQDN (nomes de domínio totalmente qualificados) e back-ends de vários locatários como Azure App serviço. Neste exemplo, usaremos máquinas virtuais como o back-end de destino. Podemos usar máquinas virtuais existentes ou criar novas. Neste exemplo, criaremos duas máquinas virtuais que o Azure usa como servidores de back-end para o gateway de aplicativo. Para fazer isso, iremos:

1. Crie duas novas VMS, *myVM* e *myVM2*, a serem usadas como servidores de back-end.
2. Instale o IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.
3. Adicione os servidores de back-end ao pool de back-end.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Clique em **Novo**.
2. Clique em **computação** e, em seguida, selecione **Windows Server 2016 datacenter** na lista em destaque.
3. Introduza estes valores para a máquina virtual:
   - *myVM* - para o nome da máquina virtual.
   - *azureuser* - no nome de utilizador do administrador.
   - *Azure123456!* para a palavra-passe.
   - Selecione **Utilizar existente** e, em seguida, selecione *myResourceGroupAG*.
4. Clique em **OK**.
5. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **selecionar**.
6. Certifique-se de que **myVNet** está selecionado para a rede virtual e que a sub-rede é **myBackendSubnet**.
7. Clique em **Desativado** para desativar o diagnóstico de arranque.
8. Clique em **OK**, reveja as definições na página de resumo e, em seguida, clique em **Criar**.

### <a name="install-iis"></a>Instalar o IIS

1. Abra a shell interativa e certifique-se de que está definida como **PowerShell**.
    ![private-frontendip-3](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-3.png)
2. Execute o comando seguinte para instalar o IIS na máquina virtual:

   ```azurepowershell
   Set-AzVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
