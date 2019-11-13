---
title: 'Tutorial: configurar a terminação SSL no portal-Aplicativo Azure gateway'
description: Neste tutorial, você aprende a configurar um gateway de aplicativo e a adicionar um certificado para terminação SSL usando o portal do Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: b4278fc6d44f32921713681cb094b659901cc87c
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012312"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Tutorial: configurar um gateway de aplicativo com terminação SSL usando o portal do Azure

Você pode usar o portal do Azure para configurar um [Gateway de aplicativo](overview.md) com um certificado para terminação SSL que usa máquinas virtuais para servidores de back-end.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um certificado autoassinado
> * Criar um gateway de aplicação com o certificado
> * Criar as máquinas virtuais usadas como servidores de back-end
> * Testar o gateway de aplicação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em [https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Nesta seção, você usará [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) para criar um certificado autoassinado. Você carrega o certificado no portal do Azure ao criar o ouvinte para o gateway de aplicativo.

No computador local, abra uma janela do Windows PowerShell como administrador. Execute o seguinte comando para criar o certificado:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Você verá algo semelhante a esta resposta:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) com a impressão digital que foi retornada para exportar um arquivo PFX do certificado:

> [!NOTE]
> Não use nenhum caractere especial em sua senha de arquivo. pfx. Somente caracteres alfanuméricos têm suporte.

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Para criar um gateway de aplicação

1. Selecione **criar um recurso** no menu à esquerda da portal do Azure. A **nova** janela é exibida.

2. Selecione **rede** e, em seguida, selecione **Gateway de aplicativo** na lista em **destaque** .

### <a name="basics-tab"></a>Guia básico

1. Na guia **noções básicas** , insira esses valores para as seguintes configurações do gateway de aplicativo:

   - **Grupo de recursos**: selecione **myResourceGroupAG** para o grupo de recursos. Se ele não existir, selecione **criar novo** para criá-lo.
   - **Nome do gateway de aplicativo**: insira *myAppGateway* para o nome do gateway de aplicativo.

        ![Criar novo gateway de aplicativo: Noções básicas](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Para que o Azure se comunique entre os recursos que você cria, ele precisa de uma rede virtual. Você pode criar uma nova rede virtual ou usar uma existente. Neste exemplo, você criará uma nova rede virtual ao mesmo tempo em que criar o gateway de aplicativo. As instâncias do gateway de aplicativo são criadas em sub-redes separadas. Você cria duas sub-redes neste exemplo: uma para o gateway de aplicativo e outra para os servidores de back-end.

    Em **Configurar rede virtual**, crie uma nova rede virtual selecionando **criar nova**. Na janela **criar rede virtual** que é aberta, insira os seguintes valores para criar a rede virtual e duas sub-redes:

    - **Nome**: insira *myVNet* para o nome da rede virtual.

    - **Nome da sub-rede** (sub-rede do gateway de aplicativo): a grade de **sub-redes** mostrará uma sub-rede denominada *padrão*. Altere o nome dessa sub-rede para *myAGSubnet*.<br>A sub-rede do gateway de aplicativo pode conter somente gateways de aplicativo. Nenhum outro recurso é permitido.

    - **Nome da sub-rede** (sub-rede do servidor de back-end): na segunda linha da grade **sub-redes** , insira *MyBackendSubnet* na coluna **nome da sub-rede** .

    - **Intervalo de endereços** (sub-rede do servidor de back-end): na segunda linha da grade **sub-redes** , insira um intervalo de endereços que não se sobreponha ao intervalo de endereços de *myAGSubnet*. Por exemplo, se o intervalo de endereços de *myAGSubnet* for 10.0.0.0/24, digite *10.0.1.0/24* para o intervalo de endereços de *myBackendSubnet*.

    Selecione **OK** para fechar a janela **criar rede virtual** e salvar as configurações de rede virtual.

    ![Criar novo gateway de aplicativo: rede virtual](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. Na guia **noções básicas** , aceite os valores padrão para as outras configurações e, em seguida, selecione **Avançar: front-ends**.

### <a name="frontends-tab"></a>Guia front-ends

1. Na guia **front-ends** , verifique se **tipo de endereço IP de front-end** está definido como **público**. <br>Você pode configurar o IP de front-end para ser público ou privado de acordo com seu caso de uso. Neste exemplo, você escolherá um IP de front-end público.
   > [!NOTE]
   > Para o SKU do gateway de aplicativo v2, você só pode escolher a configuração de IP de front-end **público** . A configuração de IP de front-end privada não está habilitada no momento para este SKU v2.

2. Escolha **criar novo** para o **endereço IP público** e insira *myAGPublicIPAddress* para o nome do endereço IP público e, em seguida, selecione **OK**. 

   ![Criar novo gateway de aplicativo: front-ends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Selecione **Avançar: back-ends**.

### <a name="backends-tab"></a>Guia back-ends

O pool de back-end é usado para rotear solicitações para os servidores de back-end que atendem à solicitação. Os pools de back-end podem ser compostos de NICs, conjuntos de dimensionamento de máquinas virtuais, IPs públicos, IPs internos, FQDN (nomes de domínio totalmente qualificados) e back-ends de vários locatários como Azure App serviço. Neste exemplo, você criará um pool de back-end vazio com o gateway de aplicativo e, em seguida, adicionará destinos de back-end ao pool de back-end.

1. Na guia **back-ends** , selecione **+ Adicionar um pool de back-end**.

2. Na janela **Adicionar um pool de back-end** que é aberta, insira os seguintes valores para criar um pool de back-end vazio:

    - **Nome**: insira *myBackendPool* para o nome do pool de back-end.
    - **Adicionar pool de back-end sem destinos**: selecione **Sim** para criar um pool de back-end com nenhum destino. Você adicionará destinos de back-end depois de criar o gateway de aplicativo.

3. Na janela **Adicionar um pool de back-end** , selecione **Adicionar** para salvar a configuração do pool de back-end e retornar à guia **back-ends** .

   ![Criar novo gateway de aplicativo: back-ends](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. Na guia **back-ends** , selecione **Avançar: configuração**.

### <a name="configuration-tab"></a>Guia de configuração

Na guia **configuração** , você conectará o front-end e o pool de back-end que você criou usando uma regra de roteamento.

1. Selecione **Adicionar uma regra** na coluna **regras de roteamento** .

2. Na janela **Adicionar uma regra de roteamento** que é aberta, insira *myRoutingRule* para o **nome da regra**.

3. Uma regra de roteamento requer um ouvinte. Na guia **ouvinte** na janela **Adicionar uma regra de roteamento** , insira os seguintes valores para o ouvinte:

    - **Nome do ouvinte**: insira *MyListener* para o nome do ouvinte.
    - **IP de front-end**: selecione **público** para escolher o IP público que você criou para o front-end.
    - **Protocolo**: selecione **https**.
    - **Porta**: verifique se 443 foi inserido para a porta.

   Em **certificado HTTPS**:

   - **Arquivo de certificado pfx** – navegue até e selecione o arquivo c:\appgwcert.pfx que você criou anteriormente.
   - **Nome do certificado** – digite *mycert1* para o nome do certificado.
   - **Senha** -digite *Azure123456* para a senha.
  
        Aceite os valores padrão para as outras configurações na guia **ouvinte** e, em seguida, selecione a guia **destinos de back-end** para configurar o restante da regra de roteamento.

   ![Criar novo gateway de aplicativo: ouvinte](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. Na guia **destinos de back-end** , selecione **myBackendPool** para o **destino de back-end**.

5. Para a **configuração de http**, selecione **criar novo** para criar uma nova configuração de http. A configuração HTTP determinará o comportamento da regra de roteamento. Na janela **Adicionar uma configuração de http** que é aberta, insira *myHTTPSetting* para o **nome da configuração http**. Aceite os valores padrão para as outras configurações na janela **Adicionar uma configuração de http** e, em seguida, selecione **Adicionar** para retornar à janela **Adicionar uma regra de roteamento** . 

   ![Criar novo gateway de aplicativo: configuração de HTTP](./media/create-ssl-portal/application-gateway-create-httpsetting.png)

6. Na janela **Adicionar uma regra de roteamento** , selecione **Adicionar** para salvar a regra de roteamento e retornar para a guia **configuração** .

   ![Criar novo gateway de aplicativo: regra de roteamento](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Selecione **Avançar: marcas** e **Avançar: revisar + criar**.

### <a name="review--create-tab"></a>Revisar + criar guia

Examine as configurações na guia **revisar + criar** e, em seguida, selecione **criar** para criar a rede virtual, o endereço IP público e o gateway de aplicativo. Pode levar vários minutos para que o Azure crie o gateway de aplicativo. Aguarde até que a implantação seja concluída com êxito antes de passar para a próxima seção.

## <a name="add-backend-targets"></a>Adicionar destinos de back-end

Neste exemplo, você usará máquinas virtuais como o back-end de destino. Você pode usar máquinas virtuais existentes ou criar novas. Você criará duas máquinas virtuais que o Azure usa como servidores de back-end para o gateway de aplicativo.

Para fazer isso, você vai:

1. Crie duas novas VMs, *myVM* e *myVM2*, a serem usadas como servidores de back-end.
2. Instale o IIS nas máquinas virtuais para verificar se o gateway de aplicativo foi criado com êxito.
3. Adicione os servidores de back-end ao pool de back-end.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. Na portal do Azure, selecione **criar um recurso**. A **nova** janela é exibida.
2. Selecione **Windows Server 2016 datacenter** na lista **popular** . A página **criar uma máquina virtual** é exibida.

   O gateway de aplicativo pode rotear o tráfego para qualquer tipo de máquina virtual usada em seu pool de back-end. Neste exemplo, você usa um datacenter do Windows Server 2016.

1. Insira esses valores na guia **noções básicas** para as seguintes configurações de máquina virtual:

    - **Grupo de recursos**: selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome da máquina virtual**: insira *myVM* para o nome da máquina virtual.
    - **Username**: insira *azureuser* para o nome de usuário do administrador.
    - **Senha**: insira *Azure123456* para a senha de administrador.
4. Aceite os outros padrões e, em seguida, selecione **Avançar: discos**.  
5. Aceite os padrões da guia **discos** e, em seguida, selecione **Avançar: rede**.
6. Na guia **rede** , verifique se **myVNet** está selecionado para a **rede virtual** e se a **sub-rede** está definida como **myBackendSubnet**. Aceite os outros padrões e, em seguida, selecione **Avançar: gerenciamento**.

   O gateway de aplicativo pode se comunicar com instâncias fora da rede virtual em que ela está, mas você precisa garantir que haja conectividade IP.
1. Na guia **Gerenciamento** , defina **diagnóstico de inicialização** como **desativado**. Aceite os outros padrões e, em seguida, selecione **revisar + criar**.
2. Na guia **revisar + criar** , examine as configurações, corrija os erros de validação e, em seguida, selecione **criar**.
3. Aguarde a conclusão da implantação antes de continuar.

### <a name="install-iis-for-testing"></a>Instalar o IIS para teste

Neste exemplo, você instala o IIS nas máquinas virtuais somente para verificar se o Azure criou o gateway de aplicativo com êxito.

1. Abra [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Para fazer isso, selecione **Cloud Shell** na barra de navegação superior do portal do Azure e, em seguida, selecione **PowerShell** na lista suspensa. 

    ![Instalar uma extensão personalizada](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

3. Crie uma segunda máquina virtual e instale o IIS usando as etapas que você concluiu anteriormente. Use *myVM2* para o nome da máquina virtual e para a configuração **VMName** do cmdlet **set-AzVMExtension** .

### <a name="add-backend-servers-to-backend-pool"></a>Adicionar servidores de back-end ao pool de back-end

1. Selecione **todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **pools de back-end** no menu à esquerda.

3. Selecione **myBackendPool**.

4. Em **destinos**, selecione **máquina virtual** na lista suspensa.

5. Em **adaptadores de rede**e **máquina virtual** , selecione as máquinas virtuais **myVM** e **myVM2** e suas interfaces de rede associadas nas listas suspensas.

    ![Adicionar servidores back-end](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Selecione **Guardar**.

7. Aguarde a conclusão da implantação antes de prosseguir para a próxima etapa.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Selecione **todos os recursos**e, em seguida, selecione **myAGPublicIPAddress**.

    ![Registar o endereço IP público do gateway de aplicação](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Na barra de endereços do seu navegador, digite *https://\<seu endereço IP do gateway de aplicativo\>* .

   Para aceitar o aviso de segurança se você usou um certificado autoassinado, selecione **detalhes** (ou **avançado** no Chrome) e vá para a página da Web:

    ![Aviso de segurança](./media/create-ssl-portal/application-gateway-secure.png)

    O site IIS protegido é apresentado como no exemplo seguinte:

    ![Testar o URL base no gateway de aplicação](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o suporte a SSL do gateway de aplicativo](ssl-overview.md)
