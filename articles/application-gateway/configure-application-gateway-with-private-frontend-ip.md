---
title: Configurar o Gateway de aplicação do Azure com um endereço IP de front-end privado
description: Este artigo fornece informações sobre como configurar o Gateway de aplicação com um endereço IP de front-end privado
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/26/2019
ms.author: absha
ms.openlocfilehash: 599372bccc7465bfea0387dedd8dfd63b8a61060
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555099"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Configurar um gateway de aplicação com um ponto final (ILB) do Balanceador de carga interno

O Gateway de aplicação do Azure pode ser configurado com um VIP de acesso à Internet ou com um ponto final interno não exposto à Internet (ao utilizar um IP privado para o endereço IP de front-end), também conhecido como um balanceador de carga interno ponto final (ILB). Configurar o gateway com um endereço IP privado de front-end é útil para aplicações de linha de negócio internas não expostas à Internet. Também é útil para serviços e camadas dentro de uma aplicação de várias camadas que assenta num limite de segurança que, não sendo exposto à Internet, requer a distribuição de carga round-robin, a persistência da sessão ou a terminação SSL (Secure Sockets Layer).

Este artigo orienta-o pelos passos para configurar um gateway de aplicação com um endereço IP privado de front-end do Portal do Azure.

Neste artigo, ficará a saber como:

- Criar uma configuração de IP de front-end privado para um Gateway de aplicação
- Criar um gateway de aplicação com a configuração de IP de front-end privado

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para o Azure comunicar entre os recursos que criar, ele precisa de uma rede virtual. Pode criar uma nova rede virtual ou utilize um já existente. Neste exemplo, iremos criar uma nova rede virtual. Pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicação. Instâncias de Gateway de aplicação são criadas em sub-redes separadas. Criar duas sub-redes neste exemplo: um para o gateway de aplicação e outro para os servidores de back-end.

1. Clique em **New** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e, em seguida, selecione **Gateway de Aplicação** na lista Destaques.
3. Introduza *myAppGateway* para o nome do gateway de aplicação e *myResourceGroupAG* para o novo grupo de recursos.
4. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
5. Clique em **escolher uma rede virtual**, clique em **criar nova**e, em seguida, introduza estes valores para a rede virtual:
   - myVNet * - para o nome da rede virtual.
   - 10.0.0.0/16* - para o espaço de endereços de rede virtual.
   - *myAGSubnet* - para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.  
   ![private-frontendip-1](.\media\configure-application-gateway-with-private-frontend-ip\private-frontendip-1.png)
6. Clique em **OK** para criar a rede virtual e a sub-rede.
7. Escolha a configuração de IP de front-end como privadas e por predefinição, é uma atribuição de endereços IP dinâmica. O primeiro endereço disponível do escolhido sub-rede será atribuída como o endereço IP de front-end.
8. Se gostaria de escolher um IP privado a partir do intervalo de endereços da sub-rede (alocação estático), clique na caixa **escolher um endereço IP privado específico** e especifique o endereço IP.
   > [!NOTE]
   > Depois de atribuído, o tipo de endereço IP (dinâmico ou estático) não é possível alterar mais tarde.
9. Escolha a configuração do serviço de escuta para o protocolo e a porta, a configuração do WAF (se necessário) e clique em OK.
    ![private-frontendip-2](.\media\configure-application-gateway-with-private-frontend-ip\private-frontendip-2.png)
10. Reveja as definições na página de resumo e, em seguida, clique em **OK** para criar os recursos de rede e o gateway de aplicação. Pode demorar alguns minutos até que o gateway de aplicação ser criado, aguarde até a conclusão da implementação com êxito antes de passar para a secção seguinte.

## <a name="add-backend-pool"></a>Adicionar conjunto back-end

O conjunto de back-end é utilizado para encaminhar pedidos para os servidores de back-end que irão ser que atendeu à solicitação. Back-end pode ser composto de NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, nomes de IPs interno, de domínio completamente qualificado (FQDN) e back-ends de multi-inquilino, como o serviço de aplicações do Azure. Neste exemplo, utilizaremos as máquinas virtuais como o back-end de destino. Pode utilizar máquinas virtuais existentes ou criar novos. Neste exemplo, iremos criar duas máquinas virtuais que utiliza o Azure como servidores de back-end para o gateway de aplicação. Para tal, iremos:

1. Criar VMS novas 2, a *myVM* e *myVM2*, para ser utilizada como servidores de back-end.
2. Instale o IIS nas máquinas virtuais para verificar se o gateway de aplicação foi criado com êxito.
3. Adicione os servidores de back-end para o conjunto de back-end.

### <a name="create-a-virtual-machine"></a>Crie uma máquina virtual

1. Clique em **Novo**.
2. Clique em **computação** e, em seguida, selecione **do Windows Server 2016 Datacenter** na lista em destaque.
3. Introduza estes valores para a máquina virtual:
   - *myVM* - para o nome da máquina virtual.
   - *azureuser* - no nome de utilizador do administrador.
   - *Azure123456!* para a palavra-passe.
   - Selecione **Utilizar existente** e, em seguida, selecione *myResourceGroupAG*.
4. Clique em **OK**.
5. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **selecione**.
6. Certifique-se de que **myVNet** está selecionado para a rede virtual e que a sub-rede é **myBackendSubnet**.
7. Clique em **Desativado** para desativar o diagnóstico de arranque.
8. Clique em **OK**, reveja as definições na página de resumo e, em seguida, clique em **Criar**.

### <a name="install-iis"></a>Instalar o IIS

1. Abra a shell interativa e certifique-se de que está definida como **PowerShell**.
    ![private-frontendip-3](.\media\configure-application-gateway-with-private-frontend-ip\private-frontendip-3.png)
2. Execute o comando seguinte para instalar o IIS na máquina virtual:

   ```azurepowershell
   Set-AzureRmVMExtension `
   
     -ResourceGroupName myResourceGroupAG `
   
     -ExtensionName IIS `
   
     -VMName myVM `
   
     -Publisher Microsoft.Compute `
   
     -ExtensionType CustomScriptExtension `
   
     -TypeHandlerVersion 1.4 `
   
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' -Location EastUS  ```



3. Create a second virtual machine and install IIS using the steps that you just finished. Enter myVM2 for its name and for VMName in Set-AzureRmVMExtension.

### Add backend servers to backend pool

1. Click **All resources**, and then click **myAppGateway**.
2. Click **Backend pools**. A default pool was automatically created with the application gateway. Click **appGatewayBackendPool**.
3. Click **Add target** to add each virtual machine that you created to the backend pool.
   ![private-frontendip-4](.\media\configure-application-gateway-with-private-frontend-ip\private-frontendip-4.png)
4. Click **Save.**

## Test the application gateway

1. Check your frontend IP that got assigned by clicking the **Frontend IP Configurations** blade in the portal.
    ![private-frontendip-5](.\media\configure-application-gateway-with-private-frontend-ip\private-frontendip-5.png)
2. Copy the private IP address, and then paste it into the address bar of your browser of a VM in the same VNet or on-premises which has connectivity to this VNet and try to access the Application Gateway.

## Next steps

In this tutorial, you learned how to:

- Create a private frontend IP configuration for an Application Gateway
- Create an application gateway with private frontend IP configuration

If you want to monitor the health of your backend, see [Application Gateway Diagnostics](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).
