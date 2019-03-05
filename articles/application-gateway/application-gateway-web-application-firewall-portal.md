---
title: Criar um gateway de aplicação com uma firewall de aplicações web - portal do Azure | Documentos da Microsoft
description: Saiba como criar um gateway de aplicação com uma firewall de aplicações web com o portal do Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: d89e5d6fd21fdc37bffcc730fb20bae30ecc4f7c
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315032"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Criar um gateway de aplicação com uma firewall de aplicações web no portal do Azure

Pode utilizar o portal do Azure para criar uma [gateway de aplicação](application-gateway-introduction.md) com um [firewall de aplicações web](application-gateway-web-application-firewall-overview.md) (WAF). A WAF utiliza regras de [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) para proteger a sua aplicação. Estas regras incluem a proteção contra ataques, tais como injeção SQL, ataques de scripts entre sites e assunção de controlo de sessão sem autorização.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um gateway de aplicação com a WAF ativada
> * Criar as máquinas virtuais utilizadas como servidores de back-end
> * Criar uma conta de armazenamento e configurar o diagnóstico

![Exemplo de firewall de aplicações Web](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Uma rede virtual é necessária para a comunicação entre os recursos que criar. Neste exemplo, são criadas duas sub-redes: uma para o gateway de aplicação e a outra para os servidores de back-end. Pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicação.

1. Clique em **New** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e, em seguida, selecione **Gateway de Aplicação** na lista Destaques.
3. Introduza estes valores para o gateway de aplicação:

    - *myAppGateway* - para o nome do gateway de aplicação.
    - *myResourceGroupAG* - para o novo grupo de recursos.
    - Selecione *WAF* para o escalão do gateway de aplicação.

    ![Criar um novo gateway de aplicação](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

4. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
5. Clique em **escolher uma rede virtual**, clique em **criar nova**e, em seguida, introduza estes valores para a rede virtual:

    - *myVNet* - para o nome da rede virtual.
    - *10.0.0.0/16* - para o espaço de endereços de rede virtual.
    - *myAGSubnet* - para o nome da sub-rede.
    - *10.0.0.0/24* - para o espaço de endereço da sub-rede.

    ![Criar a rede virtual](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)

6. Clique em **OK** para criar a rede virtual e a sub-rede.
7. Clique em **escolher um endereço IP público**, clique em **criar nova**e, em seguida, introduza o nome do endereço IP público. Neste exemplo, o endereço IP público tem o nome *myAGPublicIPAddress*. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
8. Aceite os valores predefinidos para a configuração do serviço de escuta, deixe a firewall de aplicações Web desativada e, em seguida, clique em **OK**.
9. Reveja as definições na página de resumo e, em seguida, clique em **OK** para criar recursos de rede e o gateway de aplicação. Pode demorar alguns minutos até que o gateway de aplicação ser criado, aguarde até a conclusão da implementação com êxito antes de passar para a secção seguinte.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Clique em **Todos os recursos** no menu da esquerda e, em seguida, clique em **myVNet** na lista de recursos.
2. Clique em **sub-redes**e, em seguida, clique em **sub-rede**.

    ![Criar sub-rede](./media/application-gateway-web-application-firewall-portal/application-gateway-subnet.png)

3. Introduza *myBackendSubnet* para o nome da sub-rede e clique em **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, vai criar duas máquinas virtuais para serem utilizadas como servidores de back-end para o gateway de aplicação. Vai também instalar o IIS nas máquinas virtuais para verificar se o gateway de aplicação foi criado com êxito.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Clique em **Novo**.
2. Clique em **computação** e, em seguida, selecione **do Windows Server 2016 Datacenter** na lista em destaque.
3. Introduza estes valores para a máquina virtual:

    - *myVM* - para o nome da máquina virtual.
    - *azureuser* - no nome de utilizador do administrador.
    - *Azure123456!* para a palavra-passe.
    - Selecione **Utilizar existente** e, em seguida, selecione *myResourceGroupAG*.

4. Clique em **OK**.
5. Selecione **DS1_V2** para o tamanho da máquina virtual e clique em **Selecionar**.
6. Certifique-se de que **myVNet** está selecionado para a rede virtual e que a sub-rede é **myBackendSubnet**. 
7. Clique em **Desativado** para desativar o diagnóstico de arranque.
8. Clique em **OK**, reveja as definições na página de resumo e, em seguida, clique em **Criar**.

### <a name="install-iis"></a>Instalar o IIS

1. Abra a shell interativa e certifique-se de que está definida como **PowerShell**.

    ![Instalar uma extensão personalizada](./media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

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

3. Crie uma segunda máquina virtual e instale o IIS com os passos que acabou de concluir. Introduza *myVM2* para seu nome e para VMName no conjunto AzVMExtension.

### <a name="add-backend-servers"></a>Adicionar servidores back-end

1. Clique em **todos os recursos**e, em seguida, clique em **myAppGateway**.
2. Clique em **Conjuntos de back-end**. Um conjunto predefinido foi criado automaticamente com o gateway de aplicação. Clique em **appGatewayBackendPool**.
3. Clique em **adicionar destino** para adicionar cada máquina virtual que criou para o conjunto de back-end.

    ![Adicionar servidores back-end](./media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

4. Clique em **Guardar**.

## <a name="create-a-storage-account-and-configure-diagnostics"></a>Criar uma conta de armazenamento e configurar o diagnóstico

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Neste tutorial, o gateway de aplicação utiliza uma conta de armazenamento para armazenar dados para efeitos de deteção e prevenção. Também pode utilizar os registos do Azure Monitor ou o Hub de eventos para gravar dados.

1. Clique em **New** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Armazenamento** e selecione **Conta de Armazenamento - blob, ficheiro, tabela, fila**.
3. Introduza o nome da conta de armazenamento, selecione **utilizar existente** para o grupo de recursos e, em seguida, selecione **myResourceGroupAG**. Neste exemplo, é o nome da conta de armazenamento *myagstore1*. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **criar**.

## <a name="configure-diagnostics"></a>Configurar o diagnóstico

Configure o diagnóstico para registar dados nos registos ApplicationGatewayAccessLog, ApplicationGatewayPerformanceLog e ApplicationGatewayFirewallLog.

1. No menu da esquerda, clique em **todos os recursos**e, em seguida, selecione *myAppGateway*.
2. Em monitorização, clique em **registos de diagnóstico**.
3. Clique em **Adicionar definição de diagnóstico**.
4. Introduza *myDiagnosticsSettings* como o nome para as definições de diagnóstico.
5. Selecione **arquivo para uma conta de armazenamento**e, em seguida, clique em **configurar** para selecionar o *myagstore1* conta de armazenamento que criou anteriormente.
6. Selecione os registos do gateway de aplicação para recolher e manter.
7. Clique em **Guardar**.

    ![Configurar o diagnóstico](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Localize o endereço IP público do gateway de aplicação no ecrã Descrição geral. Clique em **todos os recursos** e, em seguida, clique em **myAGPublicIPAddress**.

    ![Registar o endereço IP público do gateway de aplicação](./media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereço do browser.

    ![Testar o gateway de aplicação](./media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Passos Seguintes

Neste artigo, aprendeu como:

> [!div class="checklist"]
> * Criar um gateway de aplicação com a WAF ativada
> * Criar as máquinas virtuais utilizadas como servidores de back-end
> * Criar uma conta de armazenamento e configurar o diagnóstico

Para saber mais sobre os gateways de aplicação e os recursos associados, avance para os artigos de procedimentos.
