---
title: Configure um ponto final do equilibrador de carga interna (ILB)
titleSuffix: Azure Application Gateway
description: Este artigo fornece informações sobre como configurar o Application Gateway com um endereço IP frontend privado
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/16/2020
ms.author: victorh
ms.openlocfilehash: df21a2c40dd532ac1ff321638099ceee8a2b3e53
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535609"
---
# <a name="configure-an-application-gateway-with-an-internal-load-balancer-ilb-endpoint"></a>Configure um gateway de aplicação com um ponto final de equilíbrio de carga interna (ILB)

O Portal de Aplicações Azure pode ser configurado com um VIP virado para a Internet ou com um ponto final interno que não esteja exposto à Internet. Um ponto final interno utiliza um endereço IP privado para a extremidade frontal, que também é conhecido como um ponto final do equilibrista de *carga interna (ILB).*

Configurar o gateway utilizando um endereço IP privado frontend é útil para aplicações internas de linha de negócio que não estão expostas à Internet. Também é útil para serviços e níveis dentro de uma aplicação de vários níveis que estão em um limite de segurança que não é exposto à Internet, mas ainda requerem distribuição de carga de robin redondo, stickiness de sessão ou Segurança da Camada de Transporte (TLS), anteriormente conhecido como Secure Sockets Layer (SSL), rescisão.

Este artigo guia-o através dos passos para configurar um gateway de aplicação com um endereço IP privado frontend usando o portal Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com>

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para que o Azure se comunique entre os recursos que cria, precisa de uma rede virtual. Pode criar uma nova rede virtual ou utilizar uma existente. Neste exemplo, cria-se uma nova rede virtual. Pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicação. As instâncias de Gateway de aplicação são criadas em subredes separadas. Cria-se duas subredes neste exemplo: uma para o portal da aplicação e outra para os servidores de backend.

1. Expanda o menu do portal e selecione **Criar um recurso.**
2. Selecione **Rede** e, em seguida, selecione **Gateway de Aplicação** na lista Destaques.
3. Insira o *myAppGateway* para o nome do gateway da aplicação e *do myResourceGroupAG* para o novo grupo de recursos.
4. Para **região,** selecione **(EUA) Centro dos EUA**.
5. Para **Tier,** selecione **Standard**.
6. Em configurar a **rede virtual, selecione** **Criar novos,** e depois introduzir estes valores para a rede virtual:
   - *myVNet* - para o nome da rede virtual.
   - *10.0.0.0/16* - para o espaço de endereços de rede virtual.
   - *myAGSubnet* - para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.
   - *myBackendSubnet* - para o nome da sub-rede de backend.
   - *10.0.1.0/24* - para o espaço de endereço de sub-rede de backend.

    ![Criar a rede virtual](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-1.png)

6. Selecione **OK** para criar a rede virtual e sub-rede.
7. Selecione **Next:Frontends**.
8. Para o tipo de **endereço IP Frontend,** selecione **Private**.

   Por padrão, é uma atribuição dinâmica de endereçoip. O primeiro endereço disponível da subnet configurada é atribuído como o endereço IP frontend.
   > [!NOTE]
   > Uma vez atribuído, o tipo de endereço IP (estático ou dinâmico) não pode ser alterado posteriormente.
9. Selecione **Next:Backends**.
10. Selecione **Adicionar uma piscina de backend**.
11. Para **nome**, digite *appGatewayBackendPool*.
12. Para **adicionar piscina de backend sem alvos,** selecione **Sim**. Vai adicionar os alvos mais tarde.
13. Selecione **Adicionar**.
14. Selecione **Next:Configuração**.
15. De acordo com as regras de **encaminhamento,** **selecione Adicionar uma regra**.
16. Para **o nome da regra,** digite *Rrule-01*.
17. Para **o nome listener**, *digite listener-01*.
18. Para **O IP Frontend,** selecione **Private**.
19. Aceite as restantes predefinições e selecione o separador de **alvos Backend.**
20. Para **o tipo target,** selecione **backend pool**, e, em seguida, selecione **appGatewayBackendPool**.
21. Para **a definição HTTP,** selecione **Criar novo**.
22. Para o nome de **definição HTTP,** escreva *http-definição-01*.
23. Para **o protocolo Backend,** selecione **HTTP**.
24. Para **a porta Backend,** tipo *80*.
25. Aceite as restantes predefinições e selecione **Adicionar**.
26. Na página de regra adicionar uma regra de **encaminhamento,** selecione **Adicionar**.
27. Selecione **Seguinte: Etiquetas**.
28. Selecione **Seguinte: Rever + criar**.
29. Reveja as definições na página sumária e, em seguida, selecione **Criar** para criar os recursos de rede e o gateway da aplicação. A criação do gateway de aplicação pode demorar vários minutos. Aguarde até que a colocação termine com sucesso antes de passar para a secção seguinte.

## <a name="add-backend-pool"></a>Adicione piscina de backend

A piscina de backend é usada para encaminhar pedidos para os servidores backend que servem o pedido. O backend pode ser composto por NICs, conjuntos de escala de máquinas virtuais, endereços IP públicos, endereços IP internos, nomes de domínio totalmente qualificados (FQDN), e back-ends multi-inquilinos como o Azure App Service. Neste exemplo, utiliza-se máquinas virtuais como o backend do alvo. Pode utilizar as máquinas virtuais existentes ou criar novas. Neste exemplo, cria-se duas máquinas virtuais que o Azure utiliza como servidores de backend para o gateway da aplicação.

Para fazer isto, tu:

1. Crie duas novas máquinas virtuais, *myVM* e *myVM2,* usadas como servidores de backend.
2. Instale o IIS nas máquinas virtuais para verificar se o gateway da aplicação foi criado com sucesso.
3. Adicione os servidores de backend à piscina de backend.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **Criar um recurso**.
2. **Selecione Compute** e, em seguida, selecione **a máquina Virtual**.
4. Introduza estes valores para a máquina virtual:
   - selecione *myResourceGroupAG* para **o grupo de recursos**.
   - *myVM* - para nome de **máquina virtual**.
   - Selecione **Windows Server 2019 Datacenter** para **Imagem**.
   - um **nome de utilizador**válido.
   - uma **palavra-passe**válida.
5. Aceite as restantes predefinições e selecione **Seguinte: Discos**.
6. Aceite as predefinições e selecione **Seguinte : Networking**.
7. Certifique-se de que **myVNet** está selecionado para a rede virtual e que a sub-rede é **myBackendSubnet**.
8. Aceite as restantes predefinições e selecione **Seguinte: Gestão**.
9. Selecione **Off** para desativar diagnósticos de arranque.
10. Aceite as restantes predefinições e selecione **Seguinte: Avançado**.
11. Selecione **Seguinte : Etiquetas**.
12. Selecione **Seguinte : Rever + criar**.
13. Reveja as definições na página resumo e, em seguida, selecione **Criar**. Pode levar vários minutos para criar o VM. Aguarde até que a colocação termine com sucesso antes de passar para a secção seguinte.

### <a name="install-iis"></a>Instalar o IIS

1. Abra a Cloud Shell e certifique-se de que está definido para **powerShell**.
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

     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `

     -Location CentralUS `

   ```



3. Crie uma segunda máquina virtual e instale o IIS com os passos que acabou de concluir. Introduza myVM2 para o seu nome e para VMName em Set-AzVMExtension.

### <a name="add-backend-servers-to-backend-pool"></a>Adicione servidores de backend para backend pool

1. **Selecione todos os recursos**e, em seguida, selecione **myAppGateway**.
2. Selecione **piscinas backend**. Selecione **appGatewayBackendPool**.
3. No **tipo Target** selecione Máquina **Virtual** e sob **destino**, selecione o vNIC associado ao myVM.
4. Repita para adicionar MyVM2.
   ![private-frontendip-4](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-4.png)
5. selecione **Salvar.**

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Verifique o ip frontend que foi atribuído clicando na página de **Configurações IP Frontend** no portal.
    ![private-frontendip-5](./media/configure-application-gateway-with-private-frontend-ip/private-frontendip-5.png)
2. Copie o endereço IP privado e, em seguida, cole-o na barra de endereços do navegador num VM no mesmo VNet ou no local que tenha conectividade com este VNet e tente aceder ao Gateway da Aplicação.

## <a name="next-steps"></a>Passos seguintes

Se quiser monitorizar a saúde do seu backend, consulte os registos de saúde e diagnóstico de [back-end para gateway](application-gateway-diagnostics.md)de aplicação .
