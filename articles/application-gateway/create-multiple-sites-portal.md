---
title: 'Tutorial: Acolhe vários web sites usando o portal Azure'
titleSuffix: Azure Application Gateway
description: Neste tutorial, você aprende a criar um gateway de aplicações que hospeda vários web sites usando o portal Azure.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: cfbd5301bc2b24c4d5614e5f88c6ae18d4affc66
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721635"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>Tutorial: Criar e configurar uma porta de aplicação para hospedar vários web sites usando o portal Azure

Pode utilizar o portal Azure para [configurar o alojamento de vários web sites](multiple-site-overview.md) quando criar um [gateway de aplicações](overview.md). Neste tutorial, você define piscinas de endereços de backend usando máquinas virtuais. Em seguida, configura os ouvintes e as regras com base em dois domínios para garantir que o tráfego web chega aos servidores apropriados nas piscinas. Este tutorial utiliza exemplos de *www.contoso.com* e *www.fabrikam.com.*

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um gateway de aplicação
> * Criar máquinas virtuais para servidores backend
> * Criar piscinas de backend com os servidores backend
> * Criar serviços de escuta de back-end
> * Criar regras de encaminhamento
> * Editar ficheiro de anfitriões para resolução de nome

:::image type="content" source="./media/create-multiple-sites-portal/scenario.png" alt-text="Gateway de aplicação multi-site":::

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

1. **Selecione Criar um recurso** no menu esquerdo do portal Azure. A **nova** janela aparece.

2. Selecione **Networking** e, em seguida, selecione **Application Gateway** na lista **em destaque.**

### <a name="basics-tab"></a>Separador básico

1. No separador **Básicos, insira** estes valores para as seguintes definições de gateway de aplicação:

   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos. Se não existir, selecione **Criar novo** para criá-lo.
   - **Nome do gateway de aplicação**: Introduza *o myAppGateway* para o nome do gateway de aplicação.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png" alt-text="Criar Gateway de Aplicações":::

2.  Para que o Azure comunique entre os recursos que cria, precisa de uma rede virtual. Pode criar uma nova rede virtual ou utilizar uma existente. Neste exemplo, irá criar uma nova rede virtual ao mesmo tempo que cria o gateway de aplicações. As instâncias do Gateway de Aplicação são criadas em sub-redes separadas. Cria duas sub-redes neste exemplo: uma para o gateway de aplicações e outra para os servidores backend.

    No **âmbito da rede virtual Configurar,** selecione Criar **novos** para criar uma nova rede virtual. Na janela **de rede virtual Create** que se abre, introduza os seguintes valores para criar a rede virtual e duas sub-redes:

    - **Nome**: Introduza *o myVNet* para o nome da rede virtual.

    - **Nome da sub-rede (sub-rede** Do Gateway de aplicação): A grelha **das sub-redes** apresentará uma sub-rede chamada *Predefinição*. Mude o nome desta sub-rede para *myAGSubnet*.<br>A sub-rede de gateway de aplicação pode conter apenas portais de aplicação. Não são permitidos outros recursos.

    - **Nome da sub-rede** (sub-rede do servidor de backend): Na segunda linha da grelha **sub-redes,** introduza o *myBackendSubnet* na coluna **nome sub-rede.**

    - **Intervalo de endereços** (sub-rede do servidor de backend): Na segunda linha da Grelha de **Sub-redes,** introduza um intervalo de endereços que não se sobreponha ao intervalo de endereços do *myAGSubnet*. Por exemplo, se o intervalo de endereços do *myAGSubnet* for de 10.0.0.0/24, insira *10.0.0.0/24* para o intervalo de endereços do *myBackendSubnet*.

    Selecione **OK** para fechar a janela **de rede virtual Create** e guardar as definições de rede virtual.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png" alt-text="Criar VNet":::
    
3. No **separador Básicos,** aceite os valores predefinidos para as outras definições e, em seguida, selecione **Seguinte: Frontends**.

### <a name="frontends-tab"></a>Separador frontends

1. No **separador Frontends,** verifique se o **tipo de endereço IP frontend** está definido para **Public**. <br>Pode configurar o Frontend IP para ser público ou privado de acordo com o seu caso de utilização. Neste exemplo, você escolherá um IP de Frontend Público.
   > [!NOTE]
   > Para o Gateway de Aplicação v2 SKU, só pode escolher a configuração IP frontal **do público.** A configuração IP do frontend privado não está ativada para este V2 SKU.

2. **Selecione Adicionar novo** para o endereço IP **público** e insira *o myAGPublicIPAddress* para o nome do endereço IP público e, em seguida, selecione **OK**. 

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png" alt-text="Criar outro VNet":::

3. Selecione **Seguinte: Backends**.

### <a name="backends-tab"></a>Separador backends

O pool backend é usado para encaminhar pedidos para os servidores backend que servem o pedido. As piscinas backend podem ser NICs, conjuntos de escala de máquinas virtuais, IPs públicos, IPs internos, nomes de domínio totalmente qualificados (FQDN) e back-ends multi-inquilinos como O Azure App Service. Neste exemplo, você vai criar uma piscina de backend vazia com o seu gateway de aplicação e, em seguida, adicionar alvos de backend para a piscina de backend.

1. No **separador Backends,** **selecione Adicione uma piscina de backend**.

2. Na janela **de piscina de backend** que se abre, introduza os seguintes valores para criar uma piscina de backend vazia:

    - **Nome**: *Insira contosoPool* para o nome da piscina de backend.
    - **Adicionar pool de backend sem alvos**: Selecione **Sim** para criar um pool de backend sem alvos. Irá adicionar alvos de backend depois de criar o gateway de aplicação.

3. Na janela **de reserva do pool,** selecione **Adicione** para guardar a configuração da piscina de backend e volte ao separador **Backends.**
4. Agora adicione outra piscina de backend chamada *fabrikamPool* da mesma forma que você acrescentou a piscina anterior.
1. Selecione **Adicionar**.

    :::image type="content" source="./media/create-multiple-sites-portal/backend-pools.png" alt-text="Criar backends":::

4. No **separador Backends,** selecione **Seguinte: Configuração**.

### <a name="configuration-tab"></a>Separador de configuração

No separador **Configuração,** irá ligar as piscinas fronte e backend criadas utilizando uma regra de encaminhamento.

1. **Selecione Adicione uma regra de encaminhamento** na coluna de **regras de encaminhamento.**

2. Na janela **de regra de encaminhamento Adicionar uma** janela de regra de encaminhamento que se abre, *introduza contosoRule* para o **nome regra**.

3. Uma regra de encaminhamento requer um ouvinte. No **separador Ouvinte** dentro da janela De regra de encaminhamento Adicionar uma regra **de encaminhamento,** introduza os seguintes valores para o ouvinte:

    - **Nome da regra**: *contosoRule*.
    - **Nome do ouvinte**: *contosoListener*.
    - **FRONTend IP**: Selecione **Público** para escolher o IP público que criou para o frontend.

   Em **definições adicionais:**
   - **Tipo de ouvinte**: Vários sites
   - **Nome do anfitrião**: **www.contoso.com**

   Aceite os valores predefinidos para as outras definições no **separador Listener** e, em seguida, selecione o separador **alvos de Backend** para configurar o resto da regra de encaminhamento.

   :::image type="content" source="./media/create-multiple-sites-portal/routing-rule.png" alt-text="Criar regra de encaminhamento":::

4. No separador **alvos de Backend,** selecione **contosoPool** para o **alvo Backend**.

5. Para a **definição HTTP**, selecione **Adicione novo** para criar uma nova definição HTTP. A definição HTTP determinará o comportamento da regra de encaminhamento. Na janela **de definição HTTP** que se abre, *introduza o contosoHTTPSetting* para o **nome de definição HTTP**. Aceite os valores predefinidos para as outras definições na janela **de definição HTTP** e, em seguida, selecione **Adicionar** para voltar à janela de regra **de encaminhamento Adicionar uma.** 

6. Na janela **de regra de encaminhamento,** selecione **Adicione** para guardar a regra de encaminhamento e volte ao **separador Configuração.**
7. **Selecione Adicione uma regra de encaminhamento** e adicione uma regra semelhante, ouvinte, alvo de backend e definição HTTP para Fabrikam.

     :::image type="content" source="./media/create-multiple-sites-portal/fabrikam-rule.png" alt-text="Regra de Fabrikam":::

7. Selecione **Seguinte: Tags** e depois **Seguinte: Rever + criar**.

### <a name="review--create-tab"></a>Rever + criar separador

Reveja as definições no separador **'Rever +' criar** e, em seguida, selecione **Criar** para criar a rede virtual, o endereço IP público e o gateway de aplicações. Pode levar vários minutos para o Azure criar o gateway de aplicação.

Aguarde até que a colocação termine com sucesso antes de passar para a secção seguinte.

## <a name="add-backend-targets"></a>Adicionar alvos de backend

Neste exemplo, utilizará as máquinas virtuais como o alvo de backend. Pode utilizar máquinas virtuais existentes ou criar novas. Irá criar duas máquinas virtuais que o Azure utiliza como servidores de backend para o gateway de aplicações.

Para adicionar alvos de backend, você:

1. Crie dois novos VMs, *contosoVM* e *fabrikamVM,* para serem usados como servidores backend.
2. Instale o IIS nas máquinas virtuais para verificar se o gateway de aplicações foi criado com sucesso.
3. Adicione os servidores backend às piscinas de backend.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No portal Azure, selecione **Criar um recurso**. A **nova** janela aparece.
2. Selecione **o Centro de Dados do Windows Server 2016** na lista **Popular.** Aparece a página **de máquina virtual Create a.**<br>O Application Gateway pode encaminhar o tráfego para qualquer tipo de máquina virtual utilizada na sua piscina de backend. Neste exemplo, utiliza um Datacenter Windows Server 2016.
3. Introduza estes valores no separador **Básicos** para as seguintes definições de máquina virtual:

    - **Subscrição**: selecione a sua subscrição.
    - **Grupo de recursos**: Selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome da máquina virtual**: *Introduza contosoVM* para o nome da máquina virtual.
    - **Região**: Selecione a mesma região que usou antes.
    - **Nome de utilizador**: Introduza um nome para o nome de utilizador do administrador.
    - **Senha**: Introduza uma palavra-passe para o administrador.
1. Aceite as outras predefinições e, em seguida, selecione **Seguinte: Discos**.  
2. Aceite as falhas do separador **Discos** e, em seguida, selecione **Seguinte: Networking**.
3. No **separador Networking,** verifique se o **myVNet** está selecionado para a **rede Virtual** e a **sub-rede** está definida para **o myBackendSubnet**. Aceite os outros incumprimentos e, em seguida, selecione **Seguinte: Gestão**.<br>O Application Gateway pode comunicar com casos fora da rede virtual em que se encontra, mas é preciso garantir que existe conectividade IP.
4. No **separador Gestão,** desative **os diagnósticos boot** para **desativar**. Aceite os outros predefinidos e, em seguida, **selecione Review + create**.
5. No **separador 'Rever +' criar,** rever as definições, corrigir quaisquer erros de validação e, em seguida, selecionar **Criar**.
6. Aguarde que a criação da máquina virtual esteja concluída antes de continuar.

### <a name="install-iis-for-testing"></a>Instalar o IIS para testes

Neste exemplo, instala o IIS nas máquinas virtuais apenas para verificar se o Azure criou o gateway de aplicações com sucesso.

1. Abrir [a azure PowerShell](../cloud-shell/quickstart-powershell.md). Para tal, selecione **Cloud Shell** a partir da barra de navegação superior do portal Azure e, em seguida, selecione **PowerShell** da lista de drop-down. 

    ![Instalar uma extensão personalizada](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Executar o seguinte comando para instalar o IIS na máquina virtual, substituindo a região do grupo de recursos por <\> localização: 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location <location>
    ```

3. Crie uma segunda máquina virtual e instale o IIS utilizando os passos que já concluiu. Utilize *fabrikamVM* para o nome da máquina virtual e para a definição **VMName** do **cmdlet Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pools"></a>Adicione servidores de backend para backend pools

1. Selecione **Todos os recursos** e, em seguida, selecione **myAppGateway**.

2. Selecione **piscinas backend** do menu esquerdo.

3. **Selecione contosoPool**.

4. No **tipo Target,** selecione **a máquina virtual** da lista de drop-down.

5. No **Target**, selecione a interface de rede da máquina virtual **contosoVM** a partir da lista de drop-down.

    ![Adicionar servidores back-end](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. Selecione **Guardar**.
7. Repita para adicionar o *fabrikamVM* e interface à *fabrikamPool*.

Aguarde que a colocação esteja concluída antes de avançar para o próximo passo.

## <a name="edit-your-hosts-file-for-name-resolution"></a>Edite o ficheiro dos anfitriões para resolução de nomes

Após a criação do gateway de aplicações com o seu endereço IP público, pode obter o endereço IP e usá-lo para editar o ficheiro dos anfitriões para resolver `www.contoso.com` e `www.fabrikam.com` . Num ambiente de produção, pode criar um `CNAME` DNS para resolução de nomes.

1. Clique **em Todos os recursos** e, em seguida, clique no **myAGPublicIPAddress**.

    ![Endereço DNS do gateway de aplicação de registo](./media/create-multiple-sites-portal/public-ip.png)

2. Copie o endereço IP e use-o como valor para novas entradas no seu `hosts` ficheiro.
1. Na sua máquina local, abra um pedido de comando administrativo e navegue para `c:\Windows\System32\drivers\etc` .
1. Abra o `hosts` ficheiro e adicione as seguintes entradas, onde está o endereço IP público do gateway de `x.x.x.x` aplicação:
   ```dos
   # Copyright (c) 1993-2009 Microsoft Corp.
   #
   # This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
   #
   # This file contains the mappings of IP addresses to host names. Each
   # entry should be kept on an individual line. The IP address should
   # be placed in the first column followed by the corresponding host name.
   # The IP address and the host name should be separated by at least one
   # space.
   #
   # Additionally, comments (such as these) may be inserted on individual
   # lines or following the machine name denoted by a '#' symbol.
   #
   # For example:
   #
   #      102.54.94.97     rhino.acme.com          # source server
   #       38.25.63.10     x.acme.com              # x client host
   
   # localhost name resolution is handled within DNS itself.
   #    127.0.0.1       localhost
   #    ::1             localhost
   x.x.x.x www.contoso.com
   x.x.x.x www.fabrikam.com

   ```
1. Guarde o ficheiro.
1. Executar os seguintes comandos para carregar e exibir as alterações no ficheiro dos anfitriões:
   ```dos
    ipconfig/registerdns
    ipconfig/displaydns
   ```
   
## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

1. Digite um nome de domínio na barra de endereço do seu navegador. Por exemplo, `http://www.contoso.com`.

    ![Testar o site contoso no gateway de aplicação](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. Altere o endereço para outro domínio e deverá ver algo como o seguinte exemplo:

    ![Testar o site fabrikam no gateway de aplicação](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com o gateway de aplicação, remova o grupo de recursos. Quando remove o grupo de recursos, também remove o gateway de aplicação e todos os seus recursos relacionados.

Para remover o grupo de recursos:

1. No menu esquerdo do portal Azure, selecione **Grupos de Recursos**.
2. Na página **de grupos de recursos,** procure **o myResourceGroupAG** na lista e, em seguida, selecione-o.
3. Na **página** do grupo Recursos , selecione **Eliminar grupo de recursos**.
4. Introduza *o myResourceGroupAG* para **escrever o nome do grupo de recursos** e, em seguida, selecione **Delete**.

Para restaurar o ficheiro dos anfitriões:
1. Elimine as `www.contoso.com` linhas e `www.fabrikam.com` linhas do ficheiro dos anfitriões e corra `ipconfig/registerdns` e a partir do pedido de `ipconfig/flushdns` comando.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Saiba mais sobre o que pode fazer com o Azure Application Gateway](./overview.md)