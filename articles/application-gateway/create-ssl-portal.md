---
title: Configurar um gateway de aplicação com a terminação de SSL - portal do Azure | Documentos da Microsoft
description: Saiba como configurar um gateway de aplicação e adicionar um certificado para a terminação de SSL com o portal do Azure.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.date: 5/15/2018
ms.author: victorh
ms.openlocfilehash: 33e24a8d8715dd6f2b37ed566a1479dffd93c466
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57308096"
---
# <a name="configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Configurar um gateway de aplicação com a terminação de SSL com o portal do Azure

Pode utilizar o portal do Azure para configurar uma [gateway de aplicação](overview.md) com um certificado para a terminação de SSL, que utiliza máquinas virtuais para servidores de back-end.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Criar um certificado autoassinado
> * Criar um gateway de aplicação com o certificado
> * Criar as máquinas virtuais utilizadas como servidores de back-end

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Nesta secção, vai utilizar [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) para criar um certificado autoassinado que é carregado para o portal do Azure ao criar o serviço de escuta para o gateway de aplicação.

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

1. Clique em **New** encontrado no canto superior esquerdo do portal do Azure.
2. Selecione **Rede** e, em seguida, selecione **Gateway de Aplicação** na lista Destaques.
3. Introduza *myAppGateway* para o nome do gateway de aplicação e *myResourceGroupAG* para o novo grupo de recursos.
4. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
5. Clique em **escolher uma rede virtual**, clique em **criar nova**e, em seguida, introduza estes valores para a rede virtual:

    - *myVNet* - para o nome da rede virtual.
    - *10.0.0.0/16* - para o espaço de endereços de rede virtual.
    - *myAGSubnet* - para o nome da sub-rede.
    - *10.0.0.0/24* - para o espaço de endereço da sub-rede.

    ![Criar a rede virtual](./media/create-ssl-portal/application-gateway-vnet.png)

6. Clique em **OK** para criar a rede virtual e a sub-rede.
7. Clique em **escolher um endereço IP público**, clique em **criar nova**e, em seguida, introduza o nome do endereço IP público. Neste exemplo, o endereço IP público tem o nome *myAGPublicIPAddress*. Aceite os valores predefinidos para as outras definições e, em seguida, clique em **OK**.
8. Clique em **HTTPS** para o protocolo do serviço de escuta e certifique-se de que a porta é definida como **443**.
9. Clique no ícone de pasta e navegue para o *appgwcert.pfx* certificado que criou anteriormente para carregá-lo.
10. Introduza *mycert1* para o nome do certificado e *Azure123456!* a palavra-passe e clique em **OK**.

    ![Criar um novo gateway de aplicação](./media/create-ssl-portal/application-gateway-create.png)

11. Reveja as definições na página de resumo e, em seguida, clique em **OK** para criar os recursos de rede e o gateway de aplicação. Pode demorar alguns minutos até que o gateway de aplicação ser criado, aguarde até a conclusão da implementação com êxito antes de passar para a secção seguinte.

### <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Clique em **Todos os recursos** no menu da esquerda e, em seguida, clique em **myVNet** na lista de recursos.
2. Clique em **sub-redes**e, em seguida, clique em **sub-rede**.

    ![Criar sub-rede](./media/create-ssl-portal/application-gateway-subnet.png)

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

3. Clique em **todos os recursos**e, em seguida, clique em **myAppGateway**.
4. Clique em **Conjuntos de back-end**. Um conjunto predefinido foi criado automaticamente com o gateway de aplicação. Clique em **appGatewayBackendPool**.
5. Clique em **adicionar destino** para adicionar cada máquina virtual que criou para o conjunto de back-end.

    ![Adicionar servidores back-end](./media/create-ssl-portal/application-gateway-backend.png)

6. Clique em **Guardar**.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Clique em **todos os recursos**e, em seguida, clique em **myAGPublicIPAddress**.

    ![Registar o endereço IP público do gateway de aplicação](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Copie o endereço IP público e cole-o na barra de endereço do browser. Para aceitar o aviso de segurança se utilizou um certificado autoassinado, selecione os detalhes e, em seguida, avance para a página Web:

    ![Aviso de segurança](./media/create-ssl-portal/application-gateway-secure.png)

    O site IIS protegido é apresentado como no exemplo seguinte:

    ![Testar o URL base no gateway de aplicação](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um certificado autoassinado
> * Criar um gateway de aplicação com o certificado
> * Criar as máquinas virtuais utilizadas como servidores de back-end

Para saber mais sobre os gateways de aplicação e os recursos associados, avance para os artigos de procedimentos.
