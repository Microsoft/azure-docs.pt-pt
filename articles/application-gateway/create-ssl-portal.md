---
title: 'Tutorial: Configure a rescisão do SSL no portal - Gateway de aplicação Azure'
description: Neste tutorial, aprende-se a configurar um gateway de aplicação e a adicionar um certificado para a rescisão de SSL através do portal Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: b4278fc6d44f32921713681cb094b659901cc87c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74012312"
---
# <a name="tutorial-configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Tutorial: Configure um portal de aplicação com rescisão SSL utilizando o portal Azure

Pode utilizar o portal Azure para configurar um portal de [aplicação](overview.md) com um certificado de rescisão SSL que utiliza máquinas virtuais para servidores de backend.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um certificado autoassinado
> * Criar um gateway de aplicação com o certificado
> * Criar as máquinas virtuais usadas como servidores de backend
> * Testar o gateway de aplicação

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no portal Azure em[https://portal.azure.com](https://portal.azure.com)

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

Nesta secção, utiliza o [New SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) para criar um certificado auto-assinado. Faça o upload do certificado para o portal Azure quando criar o ouvinte para o gateway da aplicação.

No seu computador local, abra uma janela do Windows PowerShell como administrador. Executar o seguinte comando para criar o certificado:

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

Devia ver algo como esta resposta:

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

Utilize [exportação-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) com a impressão digital que foi devolvida para exportar um ficheiro pfx do certificado:

> [!NOTE]
> Não utilize caracteres especiais na sua palavra-passe de ficheiro .pfx. Apenas caracteres alfanuméricos são suportados.

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

1. Selecione **Criar um recurso** no menu esquerdo do portal Azure. A **nova** janela aparece.

2. Selecione **Networking** e, em seguida, selecione **Gateway de aplicação** na lista **Em destaque.**

### <a name="basics-tab"></a>Separador básico

1. No separador **Basics,** introduza estes valores para as seguintes definições de gateway de aplicação:

   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos. Se não existir, selecione **Criar novo** para criá-lo.
   - Nome de gateway da **aplicação**: Insira *myAppGateway* para o nome do gateway da aplicação.

        ![Criar novo portal de aplicações: Básicos](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  Para que o Azure se comunique entre os recursos que cria, precisa de uma rede virtual. Pode criar uma nova rede virtual ou utilizar uma existente. Neste exemplo, criará uma nova rede virtual ao mesmo tempo que cria o gateway da aplicação. As instâncias de Gateway de aplicação são criadas em subredes separadas. Cria-se duas subredes neste exemplo: uma para o portal da aplicação e outra para os servidores de backend.

    Sob **a rede virtual Configure, crie**uma nova rede virtual selecionando criar **nova**. Na janela **de rede virtual Create** que se abre, introduza os seguintes valores para criar a rede virtual e duas subredes:

    - **Nome**: Introduza *myVNet* para o nome da rede virtual.

    - **Nome da sub-rede** (sub-rede de gateway de aplicação): A grelha **de sub-redes** mostrará uma subnetchamada *Padrão*. Mude o nome desta subnet para *myAGSubnet*.<br>A sub-rede de gateway de aplicação só pode conter gateways de aplicação. Não são permitidos outros recursos.

    - **Nome da sub-rede** (sub-rede do servidor de backend): Na segunda linha da grelha **subnets,** introduza *myBackendSubnet* na coluna de **nome Subnet.**

    - Intervalo de **endereços** (sub-rede de servidor de backend): Na segunda linha da Grelha de **Subnets,** introduza uma gama de endereços que não se sobreponha à gama de endereços da *myAGSubnet*. Por exemplo, se a gama de endereços do *myAGSubnet* for de 10.0.0.0.0/24, insira *10.0.1.0/24* para a gama de endereços do *myBackendSubnet*.

    Selecione **OK** para fechar a janela de **rede virtual Criar** e guardar as definições de rede virtual.

    ![Criar novo portal de aplicações: rede virtual](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. No separador **Basics,** aceite os valores predefinidos para as outras definições e, em seguida, selecione **Seguinte: Frontends**.

### <a name="frontends-tab"></a>Separador frontends

1. No separador **Frontends,** verifique se o tipo de **endereço IP frontend** está definido para **público**. <br>Pode configurar o IP Frontend para ser público ou privado de acordo com o seu caso de utilização. Neste exemplo, você escolherá um IP Frontend Público.
   > [!NOTE]
   > Para o Gateway de aplicação v2 SKU, só pode escolher a configuração IP frontal **pública.** A configuração IP frontal privada não está ativada para este V2 SKU.

2. Escolha **Criar novo** para o endereço IP **público** e introduza *myAGPublicIPAddress* para o nome de endereço IP público e, em seguida, selecione **OK**. 

   ![Criar novo portal de aplicações: frontends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Selecione **Seguinte: Backends**.

### <a name="backends-tab"></a>Separador backends

A piscina de backend é usada para encaminhar pedidos para os servidores backend que servem o pedido. As piscinas backend podem ser compostas por NICs, conjuntos de escala de máquinas virtuais, IPs públicos, IPs internos, nomes de domínio totalmente qualificados (FQDN), e back-ends multi-inquilinos como o Azure App Service. Neste exemplo, você vai criar uma piscina de backend vazia com o seu gateway de aplicação e, em seguida, adicionar alvos de backend para a piscina de backend.

1. No **separador Backends,** **selecione +Adicione uma piscina de backend**.

2. No Add uma janela de **piscina de backend** que se abre, insira os seguintes valores para criar uma piscina de backend vazia:

    - **Nome**: Introduza *myBackendPool* para obter o nome da piscina de backend.
    - **Adicione piscina de backend sem alvos**: Selecione **Sim** para criar uma piscina de backend sem alvos. Vai adicionar alvos de backend depois de criar o portal de aplicação.

3. Na janela adicionar uma janela de **piscina de backend,** selecione **Adicionar** para salvar a configuração da piscina de backend e voltar ao **separador Backends.**

   ![Criar nova porta de aplicação: backends](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. No separador **Backends,** selecione **Seguinte: Configuração**.

### <a name="configuration-tab"></a>Separador de configuração

No separador **Configuração,** irá ligar a piscina frontal e traseira que criou utilizando uma regra de encaminhamento.

1. **Selecione Adicionar uma regra** na coluna de regras de **encaminhamento.**

2. Na janela de **regras de encaminhamento adicionar** que se abre, *introduza myRoutingRule* para o **nome da regra**.

3. Uma regra de encaminhamento requer um ouvinte. No separador **Ouvinte** dentro da janela **de regra de encaminhamento,** introduza os seguintes valores para o ouvinte:

    - **Nome do ouvinte**: Insira *o meu Ouvinte* para o nome do ouvinte.
    - **Ip Frontend**: Selecione **Público** para escolher o IP público que criou para o frontend.
    - **Protocolo**: Selecione **HTTPS**.
    - **Porta**: Verifique se o porto está inserido 443.

   Em **certificado HTTPS:**

   - Ficheiro de **certificado PFX** - Navegue e selecione o ficheiro c:\appgwcert.pfx que cria anteriormente.
   - **Nome** do certificado - Digite *mycert1* para o nome do certificado.
   - **Palavra-passe** - Tipo *Azure123456* para a palavra-passe.
  
        Aceite os valores predefinidos para as outras definições no separador **Listener** e, em seguida, selecione o separador de **alvos Backend** para configurar o resto da regra de encaminhamento.

   ![Criar novo portal de aplicações: ouvinte](./media/create-ssl-portal/application-gateway-create-rule-listener.png)

4. No separador de **alvos Backend,** selecione **myBackendPool** para o **alvo Backend**.

5. Para a **definição HTTP,** selecione **Criar novo** para criar uma nova definição HTTP. A definição http determinará o comportamento da regra de encaminhamento. Na janela **de definição http** que se abre, introduza *myHTTPSetting* para o nome de **definição HTTP**. Aceite os valores predefinidos para as outras definições na janela **de definição http** e, em seguida, selecione **Adicionar** para voltar à janela de regra **de encaminhamento Adicionar.** 

   ![Criar novo gateway de aplicação: Definição HTTP](./media/create-ssl-portal/application-gateway-create-httpsetting.png)

6. Na janela **de regras de encaminhamento,** selecione **Adicionar** para salvar a regra de encaminhamento e voltar ao separador **Configuração.**

   ![Criar nova porta de aplicação: regra de encaminhamento](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Selecione **Seguinte: Tags** e **depois Seguinte: Rever + criar**.

### <a name="review--create-tab"></a>Rever + criar separador

Reveja as definições no **separador Review + criar** e, em seguida, selecione **Criar** para criar a rede virtual, o endereço IP público e o gateway da aplicação. Pode levar vários minutos para o Azure criar o portal de aplicação. Aguarde até que a colocação termine com sucesso antes de passar para a secção seguinte.

## <a name="add-backend-targets"></a>Adicionar alvos de backend

Neste exemplo, utilizará as máquinas virtuais como o backend do alvo. Pode utilizar as máquinas virtuais existentes ou criar novas. Vai criar duas máquinas virtuais que o Azure utiliza como servidores de backend para o gateway da aplicação.

Para fazer isto, vai:

1. Crie dois novos VMs, *myVM* e *myVM2,* para serem usados como servidores de backend.
2. Instale o IIS nas máquinas virtuais para verificar se o gateway da aplicação foi criado com sucesso.
3. Adicione os servidores de backend à piscina de backend.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No portal Azure, selecione **Criar um recurso**. A **nova** janela aparece.
2. Selecione **O Datacenter do Windows Server 2016** na lista **Popular.** Aparece a página **Criar uma máquina virtual.**

   O Application Gateway pode encaminhar o tráfego para qualquer tipo de máquina virtual utilizada na sua piscina de backend. Neste exemplo, utiliza um Datacenter Do Windows Server 2016.

1. Introduza estes **valores** no separador Basics para as seguintes definições de máquina virtual:

    - **Grupo de recursos**: Selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome**da máquina virtual : Introduza *myVM* para o nome da máquina virtual.
    - **Nome de utilizador**: *Introduza azureuser* para o nome de utilizador do administrador.
    - **Palavra-passe**: Insira *o Azure123456* para obter a palavra-passe do administrador.
4. Aceite as outras predefinições e, em seguida, selecione **Seguinte: Discos**.  
5. Aceite as predefinições do separador **De Discos** e, em seguida, selecione **Seguinte: Networking**.
6. No separador **Networking,** verifique se o **myVNet** está selecionado para a **rede Virtual** e a **Subnet** está definida para **myBackendSubnet**. Aceite as outras predefinições e, em seguida, selecione **Seguinte: Gestão**.

   O Application Gateway pode comunicar com instâncias fora da rede virtual em que se encontra, mas é necessário garantir que há conectividade IP.
1. No separador **Gestão,** desloque os **diagnósticos da Bota** para **desligar**. Aceite as outras predefinições e, em seguida, selecione **Rever + criar**.
2. No **separador Review + criar,** rever as definições, corrigir quaisquer erros de validação e, em seguida, selecionar **Criar**.
3. Aguarde que a colocação esteja concluída antes de continuar.

### <a name="install-iis-for-testing"></a>Instalar o IIS para testes

Neste exemplo, instala o IIS nas máquinas virtuais apenas para verificar se o Azure criou o gateway da aplicação com sucesso.

1. Open [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell). Para isso, selecione **Cloud Shell** a partir da barra de navegação superior do portal Azure e, em seguida, selecione **PowerShell** a partir da lista de lançamentos. 

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

3. Crie uma segunda máquina virtual e instale o IIS utilizando os passos que já tinha concluído. Utilize *o myVM2* para o nome da máquina virtual e para a definição **VMName** do **cmdlet set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Adicione servidores de backend para backend pool

1. **Selecione todos os recursos**e, em seguida, selecione **myAppGateway**.

2. Selecione **piscinas backend** do menu esquerdo.

3. Selecione **myBackendPool**.

4. Under **Targets**, selecione **Virtual machine** a partir da lista de lançamentos.

5. Em INTERFACES **VIRTUAL MACHINE** e **NETWORK,** selecione as máquinas virtuais **myVM** e **myVM2** e as suas interfaces de rede associadas a partir das listas de drop-down.

    ![Adicionar servidores back-end](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Selecione **Guardar**.

7. Aguarde que a implantação esteja concluída antes de passar ao próximo passo.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. **Selecione todos os recursos**e, em seguida, selecione **myAGPublicIPAddress**.

    ![Registar o endereço IP público do gateway de aplicação](./media/create-ssl-portal/application-gateway-ag-address.png)

2. Na barra de endereços do seu navegador, escreva *https://\<o\>endereço IP da sua aplicação.*

   Para aceitar o aviso de segurança se usou um certificado auto-assinado, selecione **Detalhes** (ou **Avançado** no Chrome) e, em seguida, vá para a página web:

    ![Aviso de segurança](./media/create-ssl-portal/application-gateway-secure.png)

    O site IIS protegido é apresentado como no exemplo seguinte:

    ![Testar o URL base no gateway de aplicação](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o suporte do Application Gateway SSL](ssl-overview.md)
