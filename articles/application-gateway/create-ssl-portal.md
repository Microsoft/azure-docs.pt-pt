---
title: Tutorial - configurar um gateway de aplicação com a terminação de SSL - portal do Azure
description: Neste tutorial, saiba como configurar um gateway de aplicação e adicionar um certificado para a terminação de SSL com o portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 4/17/2019
ms.author: victorh
ms.openlocfilehash: f3ba3eb12dc85a72c4e49c374e62209b83400d33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66134432"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Tutorial: Configurar um gateway de aplicação com a terminação de SSL com o portal do Azure

Pode utilizar o portal do Azure para configurar uma [gateway de aplicação](overview.md) com um certificado para a terminação de SSL, que utiliza máquinas virtuais para servidores de back-end.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um certificado autoassinado
> * Criar um gateway de aplicação com o certificado
> * Criar as máquinas virtuais utilizadas como servidores de back-end
> * Testar o gateway de aplicação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Inicie sessão no  Azure

Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Nesta secção, vai utilizar [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) para criar um certificado autoassinado. Carregue o certificado para o portal do Azure ao criar o serviço de escuta para o gateway de aplicação.

No seu computador local, abra uma janela do Windows PowerShell como administrador. Execute o seguinte comando para criar o certificado:

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

Deverá ver algo semelhante a esta resposta:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Uma rede virtual é necessária para a comunicação entre os recursos que criar. Neste exemplo, são criadas duas sub-redes: uma para o gateway de aplicação e a outra para os servidores de back-end. Pode criar uma rede virtual ao mesmo tempo que cria o gateway de aplicação.

1. Selecione **New** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e, em seguida, selecione **Gateway de Aplicação** na lista Destaques.
3. Introduza *myAppGateway* para o nome do gateway de aplicação e *myResourceGroupAG* para o novo grupo de recursos.
4. Aceite os valores predefinidos para as outras definições e, em seguida, selecione **OK**.
5. Selecione **escolher uma rede virtual**, selecione **criar nova**e, em seguida, introduza estes valores para a rede virtual:

   - *myVNet* - para o nome da rede virtual.
   - *10.0.0.0/16* - para o espaço de endereços de rede virtual.
   - *myAGSubnet* - para o nome da sub-rede.
   - *10.0.0.0/24* - para o espaço de endereço da sub-rede.

     ![Criar rede virtual](./media/create-ssl-portal/application-gateway-vnet.png)

6. Selecione **OK** para criar a rede virtual e sub-rede.
7. Selecione **escolher um endereço IP público**, selecione **criar nova**e, em seguida, introduza o nome do endereço IP público. Neste exemplo, o endereço IP público tem o nome *myAGPublicIPAddress*. Aceite os valores predefinidos para as outras definições e, em seguida, selecione **OK**.
8. Selecione **HTTPS** para o protocolo do serviço de escuta e certifique-se de que a porta é definida como **443**.
9. Selecione o ícone de pasta e navegue para o *appgwcert.pfx* certificado que criou anteriormente para carregá-lo.
10. Introduza *mycert1* para o nome do certificado e *Azure123456!* para a palavra-passe e, em seguida, selecione **OK**.

    ![Criar um novo gateway de aplicação](./media/create-ssl-portal/application-gateway-create.png)

11. Reveja as definições na página de resumo e, em seguida, selecione **OK** para criar os recursos de rede e o gateway de aplicação. Pode demorar alguns minutos até que o gateway de aplicação ser criado, aguarde até a conclusão da implementação com êxito antes de passar para a secção seguinte.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Selecione **todos os recursos** no menu do lado esquerdo e, em seguida, selecione **myVNet** na lista de recursos.
2. Selecione **sub-redes**e, em seguida, selecione **sub-rede**.

    ![Criar subrede](./media/create-ssl-portal/application-gateway-subnet.png)

3. Introduza *myBackendSubnet* para o nome da sub-rede e, em seguida, selecione **OK**.

## <a name="create-backend-servers"></a>Criar servidores de back-end

Neste exemplo, vai criar duas máquinas virtuais utilizadas como servidores de back-end para o gateway de aplicação. Também é instalar o IIS nas máquinas virtuais para verificar se que o gateway de aplicação foi criado com êxito.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Selecione **Novo**.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter** na lista Destaques.
3. Introduza estes valores para a máquina virtual:

    - *myVM* - para o nome da máquina virtual.
    - *azureuser* - no nome de utilizador do administrador.
    - *Azure123456!* para a palavra-passe.
    - Selecione **Utilizar existente** e, em seguida, selecione *myResourceGroupAG*.

4. Selecione **OK**.
5. Selecione **DS1_V2** para o tamanho da máquina virtual e selecione **selecione**.
6. Certifique-se de que **myVNet** está selecionado para a rede virtual e que a sub-rede é **myBackendSubnet**. 
7. Selecione **Desativado** para desativar o diagnóstico de arranque.
8. Selecione **OK**, reveja as definições na página de resumo e, em seguida, selecione **Criar**.

### <a name="install-iis"></a>Instalar o IIS

1. Abra a shell interativa e certifique-se de que está definido como **PowerShell**.

    ![Instalar uma extensão personalizada](./media/create-ssl-portal/application-gateway-extension.png)

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

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.
1. Selecione **conjuntos de back-end**. Um conjunto predefinido foi criado automaticamente com o gateway de aplicação. Selecione **appGatewayBackendPool**.
1. Selecione **adicionar destino** para adicionar cada máquina virtual que criou para o conjunto de back-end.

    ![Adicionar servidores back-end](./media/create-ssl-portal/application-gateway-backend.png)

1. Selecione **Guardar**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Selecione **todos os recursos**e, em seguida, selecione **myAGPublicIPAddress**.

    ![Registar o endereço IP público do gateway de aplicação](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereço do browser. Para aceitar o aviso de segurança se utilizou um certificado autoassinado, selecione os detalhes e, em seguida, avance para a página Web:

    ![Aviso de segurança](./media/create-ssl-portal/application-gateway-secure.png)

    O site IIS protegido é apresentado como no exemplo seguinte:

    ![Testar o URL base no gateway de aplicação](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o que pode fazer com o Gateway de aplicação do Azure](application-gateway-introduction.md)
