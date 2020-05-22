---
title: 'Quickstart: Tráfego web direto usando o portal'
titleSuffix: Azure Application Gateway
description: Aprenda a usar o portal Azure para criar um Portal de Aplicações Azure que direciona o tráfego web para máquinas virtuais numa piscina de backend.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 05/21/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a6415657d68e10b63e357f3c1353bb66fc9c8262
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779435"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - portal do Azure

Neste arranque rápido, você usa o portal Azure para criar um gateway de aplicação. Depois testa-o para se certificar de que funciona corretamente. 

O gateway da aplicação direciona o tráfego web da aplicação para recursos específicos em um pool backend. Você atribui os ouvintes aos portos, cria regras e adiciona recursos a uma piscina de backend. Por uma questão de simplicidade, este artigo usa uma configuração simples com um IP frontal público, um ouvinte básico para hospedar um único site na porta de aplicação, uma regra básica de encaminhamento de pedidos, e duas máquinas virtuais na piscina de backend.

Também pode completar este quickstart utilizando [o Azure PowerShell](quick-create-powershell.md) ou [o Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]



## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuitamente.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inscreva-se no [portal Azure](https://portal.azure.com) com a sua conta Azure.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Criará o portal de aplicação utilizando os separadores na página **de gateway Criar uma aplicação.**

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**. A **nova** janela aparece.

2. Selecione **Networking** e, em seguida, selecione **Gateway de aplicação** na lista **Em destaque.**

### <a name="basics-tab"></a>Separador básico

1. No separador **Basics,** introduza estes valores para as seguintes definições de gateway de aplicação:

   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos. Se não existir, selecione **Criar novo** para criá-lo.
   - Nome de gateway da **aplicação**: Insira *myAppGateway* para o nome do gateway da aplicação.

     ![Criar novo portal de aplicações: Básicos](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Para que o Azure se comunique entre os recursos que cria, precisa de uma rede virtual. Pode criar uma nova rede virtual ou utilizar uma existente. Neste exemplo, criará uma nova rede virtual ao mesmo tempo que cria o gateway da aplicação. As instâncias de Gateway de aplicação são criadas em subredes separadas. Cria-se duas subredes neste exemplo: uma para o portal da aplicação e outra para os servidores de backend.

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
   > Para o Gateway de aplicação v2 SKU, deve haver uma configuração IP frontal **pública.** Você ainda pode ter uma configuração IP frontend privada, mas a configuração IP frontal privada apenas (apenas o modo ILB) não está ativada para o v2 SKU. 

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
  
      Aceite os valores predefinidos para as outras definições no separador **Listener** e, em seguida, selecione o separador de **alvos Backend** para configurar o resto da regra de encaminhamento.

   ![Criar novo portal de aplicações: ouvinte](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. No separador de **alvos Backend,** selecione **myBackendPool** para o **alvo Backend**.

5. Para a **definição HTTP,** selecione **Criar novo** para criar uma nova definição HTTP. A definição http determinará o comportamento da regra de encaminhamento. Na janela **de definição http** que se abre, introduza *myHTTPSetting* para o nome de **definição HTTP** e *80* para a **porta Backend**. Aceite os valores predefinidos para as outras definições na janela **de definição http** e, em seguida, selecione **Adicionar** para voltar à janela de regra **de encaminhamento Adicionar.** 

     ![Criar novo gateway de aplicação: Definição HTTP](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. Na janela **de regras de encaminhamento,** selecione **Adicionar** para salvar a regra de encaminhamento e voltar ao separador **Configuração.**

     ![Criar nova porta de aplicação: regra de encaminhamento](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Selecione **Seguinte: Tags** e **depois Seguinte: Rever + criar**.

### <a name="review--create-tab"></a>Rever + criar separador

Reveja as definições no **separador Review + criar** e, em seguida, selecione **Criar** para criar a rede virtual, o endereço IP público e o gateway da aplicação. Pode levar vários minutos para o Azure criar o portal de aplicação. Aguarde até que a colocação termine com sucesso antes de passar para a secção seguinte.

## <a name="add-backend-targets"></a>Adicionar alvos de backend

Neste exemplo, utilizará as máquinas virtuais como o backend do alvo. Pode utilizar as máquinas virtuais existentes ou criar novas. Criará duas máquinas virtuais como servidores de backend para o gateway da aplicação.

Para fazer isto, vai:

1. Crie dois novos VMs, *myVM* e *myVM2,* para serem usados como servidores de backend.
2. Instale o IIS nas máquinas virtuais para verificar se o gateway da aplicação foi criado com sucesso.
3. Adicione os servidores de backend à piscina de backend.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**. A **nova** janela aparece.
2. Selecione **O Datacenter do Windows Server 2016** na lista **Popular.** Aparece a página **Criar uma máquina virtual.**<br>O Application Gateway pode encaminhar o tráfego para qualquer tipo de máquina virtual utilizada na sua piscina de backend. Neste exemplo, utiliza um Datacenter Do Windows Server 2016.
3. Introduza estes **valores** no separador Basics para as seguintes definições de máquina virtual:

    - **Grupo de recursos**: Selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome**da máquina virtual : Introduza *myVM* para o nome da máquina virtual.
    - **Região**: Selecione a mesma região onde criou o portal de aplicação.
    - **Nome de utilizador**: *Digite azureuser* para o nome de utilizador do administrador.
    - **Palavra-passe**: Digite uma palavra-passe.
4. Aceite as outras predefinições e, em seguida, selecione **Seguinte: Discos**.  
5. Aceite as predefinições do separador **De Discos** e, em seguida, selecione **Seguinte: Networking**.
6. No separador **Networking,** verifique se o **myVNet** está selecionado para a **rede Virtual** e a **Subnet** está definida para **myBackendSubnet**. Aceite as outras predefinições e, em seguida, selecione **Seguinte: Gestão**.<br>O Application Gateway pode comunicar com instâncias fora da rede virtual em que se encontra, mas é necessário garantir que há conectividade IP.
7. No separador **Gestão,** desloque os **diagnósticos da Bota** para **desligar**. Aceite as outras predefinições e, em seguida, selecione **Rever + criar**.
8. No **separador Review + criar,** rever as definições, corrigir quaisquer erros de validação e, em seguida, selecionar **Criar**.
9. Aguarde que a criação da máquina virtual esteja concluída antes de continuar.

### <a name="install-iis-for-testing"></a>Instalar o IIS para testes

Neste exemplo, instala o IIS nas máquinas virtuais apenas para verificar se o Azure criou o gateway da aplicação com sucesso.

1. Open Azure PowerShell. Selecione **Cloud Shell** a partir da barra de navegação superior do portal Azure e, em seguida, selecione **PowerShell** a partir da lista de drop-down. 

    ![Instalar uma extensão personalizada](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Executar o seguinte comando para instalar o IIS na máquina virtual. Mude o parâmetro *de localização,* se necessário: 

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

1. No menu do portal Azure, selecione **Todos os recursos** ou procure e selecione Todos os *recursos.* Em seguida, selecione **myAppGateway**.

2. Selecione **piscinas backend** do menu esquerdo.

3. Selecione **myBackendPool**.

4. Sob **os alvos backend**, **Tipo alvo**, selecione a **máquina Virtual** da lista de drop-down.

5. No **Target**Target , selecione as máquinas virtuais **myVM** e **myVM2** e as suas interfaces de rede associadas a partir das listas de drop-down.


   > [!div class="mx-imgBorder"]
   > ![Adicionar servidores back-end](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Selecione **Guardar**.

7. Aguarde que a implantação esteja concluída antes de passar ao próximo passo.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Embora o IIS não seja necessário para criar o gateway da aplicação, instalou-o neste arranque rápido para verificar se o Azure criou com sucesso o portal de aplicações. Utilize o IIS para testar o gateway de aplicação:

1. Encontre o endereço IP público para o gateway da aplicação na sua página **'Visão Geral'.** ![ Registre o endereço IP público da aplicação ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) Ou, pode selecionar **Todos os recursos,** introduzir *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecioná-lo nos resultados da pesquisa. O Azure exibe o endereço IP público na página **'Overview'.**
2. Copie o endereço IP público e, em seguida, cole-o na barra de endereços do seu navegador para navegar nesse endereço IP.
3. Verifique a resposta. Uma resposta válida verifica que o gateway da aplicação foi criado com sucesso e pode ligar-se com sucesso ao backend.

   ![Testar o gateway de aplicação](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Refresque o navegador várias vezes e deverá ver ligações tanto para myVM como myVM2.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não precisa dos recursos que criou com o portal de aplicação, elimine o grupo de recursos. Ao eliminar o grupo de recursos, também remove o portal de aplicação e todos os recursos relacionados.

Para eliminar o grupo de recursos:

1. No menu do portal Azure, selecione **grupos de recursos** ou procure e selecione *grupos de recursos*.
2. Na página dos **grupos de Recursos,** procure o **myResourceGroupAG** na lista e, em seguida, selecione-o.
3. Na página do **grupo Recursos,** selecione **Eliminar o grupo de recursos**.
4. Introduza *o myResourceGroupAG* para **ESCREVER O NOME DE GRUPO DE RECURSOS** e, em seguida, selecione **Eliminar**

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Configure um portal de aplicação com rescisão de TLS utilizando o portal Azure](create-ssl-portal.md)
