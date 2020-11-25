---
title: 'Quickstart: Tráfego web direto usando o portal'
titleSuffix: Azure Application Gateway
description: Neste arranque rápido, aprende-se a usar o portal Azure para criar um Gateway de aplicação Azure que direciona o tráfego web para máquinas virtuais numa piscina de backend.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: quickstart
ms.date: 11/24/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 904456de527e8d0acb1319059c18f9a3c6b0a1a3
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95992958"
---
# <a name="quickstart-direct-web-traffic-with-azure-application-gateway---azure-portal"></a>Início Rápido: Direcionar tráfego da Web com o Gateway de Aplicação do Azure - portal do Azure

Neste arranque rápido, utiliza-se o portal Azure para criar um gateway de aplicações. Em seguida, teste-o para se certificar de que funciona corretamente. 

O gateway de aplicações direciona o tráfego web da aplicação para recursos específicos numa piscina de backend. Você atribui os ouvintes aos portos, cria regras e adiciona recursos a uma piscina de backend. Por uma questão de simplicidade, este artigo usa uma configuração simples com um IP frontal público, um ouvinte básico para hospedar um único site no gateway de aplicação, uma regra de encaminhamento de pedido básico, e duas máquinas virtuais na piscina de backend.

Também pode completar este arranque rápido utilizando [a Azure PowerShell](quick-create-powershell.md) ou [a Azure CLI](quick-create-cli.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com) com a sua conta do Azure.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Criará o gateway de aplicações utilizando os separadores na página **Criar um gateway de aplicações.**

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**. A **nova** janela aparece.

2. Selecione **Networking** e, em seguida, selecione **Application Gateway** na lista **em destaque.**

### <a name="basics-tab"></a>Separador básico

1. No separador **Básicos, insira** estes valores para as seguintes definições de gateway de aplicação:

   - **Grupo de recursos**: Selecione **myResourceGroupAG** para o grupo de recursos. Se não existir, selecione **Criar novo** para criá-lo.
   - **Nome do gateway de aplicação**: Introduza *o myAppGateway* para o nome do gateway de aplicação.

     ![Criar novo gateway de aplicações: Básicos](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2. Para que o Azure comunique entre os recursos que cria, precisa de uma rede virtual. Pode criar uma nova rede virtual ou utilizar uma existente. Neste exemplo, irá criar uma nova rede virtual ao mesmo tempo que cria o gateway de aplicações. As instâncias do Gateway de Aplicação são criadas em sub-redes separadas. Cria duas sub-redes neste exemplo: uma para o gateway de aplicações e outra para os servidores backend.

    > [!NOTE]
    > [As políticas de ponto final do serviço de rede virtual](../virtual-network/virtual-network-service-endpoint-policies-overview.md) não são atualmente suportadas numa sub-rede do Gateway de Aplicação.

    No **âmbito da rede virtual Configure,** crie uma nova rede virtual selecionando Criar **novo**. Na janela **de rede virtual Create** que se abre, introduza os seguintes valores para criar a rede virtual e duas sub-redes:

    - **Nome**: Introduza *o myVNet* para o nome da rede virtual.

    - **Nome da sub-rede (sub-rede** Do Gateway de aplicação): A grelha **das sub-redes** apresentará uma sub-rede chamada *Predefinição*. Mude o nome desta sub-rede para *myAGSubnet*.<br>A sub-rede de gateway de aplicação pode conter apenas portais de aplicação. Não são permitidos outros recursos.

    - **Nome da sub-rede** (sub-rede do servidor de backend): Na segunda linha da grelha **sub-redes,** introduza o *myBackendSubnet* na coluna **nome sub-rede.**

    - **Intervalo de endereços** (sub-rede do servidor de backend): Na segunda linha da Grelha de **Sub-redes,** introduza um intervalo de endereços que não se sobreponha ao intervalo de endereços do *myAGSubnet*. Por exemplo, se o intervalo de endereços do *myAGSubnet* for de 10.0.0.0/24, insira *10.0.0.0/24* para o intervalo de endereços do *myBackendSubnet*.

    Selecione **OK** para fechar a janela **de rede virtual Create** e guardar as definições de rede virtual.

     ![Criar novo gateway de aplicações: rede virtual](./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png)
    
3. No **separador Básicos,** aceite os valores predefinidos para as outras definições e, em seguida, selecione **Seguinte: Frontends**.

### <a name="frontends-tab"></a>Separador frontends

1. No **separador Frontends,** verifique se o **tipo de endereço IP frontend** está definido para **Public**. <br>Pode configurar o Frontend IP para ser público ou privado de acordo com o seu caso de utilização. Neste exemplo, você escolherá um IP de Frontend Público.
   > [!NOTE]
   > Para o Gateway de Aplicação v2 SKU, deve haver uma configuração IP frontal **do público.** Ainda pode ter uma configuração IP de frontend pública e privada, mas a configuração IP de frontend privada (apenas o modo ILB) não está ativada para o V2 SKU. 

2. Escolha **Criar novo** para o endereço IP **público** e insira o *myAGPublicIPAddress* para o nome do endereço IP público e, em seguida, selecione **OK**. 

     ![Criar novo gateway de aplicações: frontends](./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png)

3. Selecione **Seguinte: Backends**.

### <a name="backends-tab"></a>Separador backends

O pool backend é usado para encaminhar pedidos para os servidores backend que servem o pedido. As piscinas backend podem ser compostas por NICs, conjuntos de escala de máquinas virtuais, IPs públicos, IPs internos, nomes de domínio totalmente qualificados (FQDN) e back-ends multi-inquilinos como O Azure App Service. Neste exemplo, você vai criar uma piscina de backend vazia com o seu gateway de aplicação e, em seguida, adicionar alvos de backend para a piscina de backend.

1. No **separador Backends,** selecione **+Adicione uma piscina de backend**.

2. Na janela **de piscina de backend** que se abre, introduza os seguintes valores para criar uma piscina de backend vazia:

    - **Nome**: *Insira o myBackendPool* para o nome da piscina de backend.
    - **Adicionar pool de backend sem alvos**: Selecione **Sim** para criar um pool de backend sem alvos. Irá adicionar alvos de backend depois de criar o gateway de aplicação.

3. Na janela **de reserva do pool,** selecione **Adicione** para guardar a configuração da piscina de backend e volte ao separador **Backends.**

     ![Criar novo gateway de aplicações: backends](./media/application-gateway-create-gateway-portal/application-gateway-create-backends.png)

4. No **separador Backends,** selecione **Seguinte: Configuração**.

### <a name="configuration-tab"></a>Separador de configuração

No separador **Configuração,** irá ligar o frontend e o pool de backend que criou usando uma regra de encaminhamento.

1. **Selecione Adicione uma regra** na coluna de **regras de encaminhamento.**

2. Na janela de **regra de encaminhamento Adicionar uma** janela de regra de encaminhamento que se abre, *introduza o myRoutingRule* para o **nome regra**.

3. Uma regra de encaminhamento requer um ouvinte. No **separador Ouvinte** dentro da janela De regra de encaminhamento Adicionar uma regra **de encaminhamento,** introduza os seguintes valores para o ouvinte:

    - **Nome do ouvinte**: *Insira o meu Número* para o nome do ouvinte.
    - **FRONTend IP**: Selecione **Público** para escolher o IP público que criou para o frontend.
  
      Aceite os valores predefinidos para as outras definições no **separador Listener** e, em seguida, selecione o separador **alvos de Backend** para configurar o resto da regra de encaminhamento.

   ![Criar novo gateway de aplicações: ouvinte](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-listener.png)

4. No separador **alvos de Backend,** selecione **myBackendPool** para o **alvo Backend**.

5. Para a **definição HTTP**, selecione **Criar novo** para criar uma nova definição HTTP. A definição HTTP determinará o comportamento da regra de encaminhamento. Na janela **de definição HTTP** que se abre, introduza *o myHTTPSetting* para o **nome de definição HTTP** e *80* para a **porta Backend**. Aceite os valores predefinidos para as outras definições na janela **de definição HTTP** e, em seguida, selecione **Adicionar** para voltar à janela de regra **de encaminhamento Adicionar uma.** 

     ![Criar novo gateway de aplicações: definição HTTP](./media/application-gateway-create-gateway-portal/application-gateway-create-httpsetting.png)

6. Na janela **de regra de encaminhamento,** selecione **Adicione** para guardar a regra de encaminhamento e volte ao **separador Configuração.**

     ![Criar novo gateway de aplicações: regra de encaminhamento](./media/application-gateway-create-gateway-portal/application-gateway-create-rule-backends.png)

7. Selecione **Seguinte: Tags** e depois **Seguinte: Rever + criar**.

### <a name="review--create-tab"></a>Rever + criar separador

Reveja as definições no separador **'Rever +' criar** e, em seguida, selecione **Criar** para criar a rede virtual, o endereço IP público e o gateway de aplicações. Pode levar vários minutos para o Azure criar o gateway de aplicação. Aguarde até que a colocação termine com sucesso antes de passar para a secção seguinte.

## <a name="add-backend-targets"></a>Adicionar alvos de backend

Neste exemplo, utilizará as máquinas virtuais como o alvo de backend. Pode utilizar máquinas virtuais existentes ou criar novas. Criará duas máquinas virtuais como servidores de backend para o gateway de aplicações.

Para fazer isto, vai:

1. Crie dois novos VMs, *myVM* e *myVM2,* para serem usados como servidores backend.
2. Instale o IIS nas máquinas virtuais para verificar se o gateway de aplicações foi criado com sucesso.
3. Adicione os servidores backend à piscina de backend.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**. A **nova** janela aparece.
2. Selecione **o Centro de Dados do Windows Server 2016** na lista **Popular.** Aparece a página **de máquina virtual Create a.**<br>O Application Gateway pode encaminhar o tráfego para qualquer tipo de máquina virtual utilizada na sua piscina de backend. Neste exemplo, utiliza um Datacenter Windows Server 2016.
3. Introduza estes valores no separador **Básicos** para as seguintes definições de máquina virtual:

    - **Grupo de recursos**: Selecione **myResourceGroupAG** para o nome do grupo de recursos.
    - **Nome da máquina virtual**: Introduza *o myVM* para o nome da máquina virtual.
    - **Região**: Selecione a mesma região onde criou a porta de aplicação.
    - **Nome de utilizador**: *Digite azureuser* para o nome de utilizador do administrador.
    - **Senha**: Digite uma senha.
4. Aceite as outras predefinições e, em seguida, selecione **Seguinte: Discos**.  
5. Aceite as falhas do separador **Discos** e, em seguida, selecione **Seguinte: Networking**.
6. No **separador Networking,** verifique se o **myVNet** está selecionado para a **rede Virtual** e a **sub-rede** está definida para **o myBackendSubnet**. Aceite os outros incumprimentos e, em seguida, selecione **Seguinte: Gestão**.<br>O Application Gateway pode comunicar com casos fora da rede virtual em que se encontra, mas é preciso garantir que existe conectividade IP.
7. No **separador Gestão,** desative **os diagnósticos boot** para **desativar**. Aceite os outros predefinidos e, em seguida, **selecione Review + create**.
8. No **separador 'Rever +' criar,** rever as definições, corrigir quaisquer erros de validação e, em seguida, selecionar **Criar**.
9. Aguarde que a criação da máquina virtual esteja concluída antes de continuar.

### <a name="install-iis-for-testing"></a>Instalar o IIS para testes

Neste exemplo, instala o IIS nas máquinas virtuais apenas para verificar se o Azure criou o gateway de aplicações com sucesso.

1. Abra o Azure PowerShell. Selecione **Cloud Shell** a partir da barra de navegação superior do portal Azure e, em seguida, selecione **PowerShell** da lista de drop-down. 

    ![Instalar uma extensão personalizada](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. Executar o seguinte comando para instalar o IIS na máquina virtual. Altere o parâmetro *de localização,* se necessário: 

    ```azurepowershell
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

3. Crie uma segunda máquina virtual e instale o IIS utilizando os passos que já concluiu. Utilize *o myVM2* para o nome da máquina virtual e para a definição **VMName** do **cmdlet Set-AzVMExtension.**

### <a name="add-backend-servers-to-backend-pool"></a>Adicione servidores backend para backend pool

1. No menu do portal Azure, selecione **Todos os recursos** ou procure e selecione Todos os *recursos*. Em seguida, selecione **o meuAppGateway**.

2. Selecione **piscinas backend** do menu esquerdo.

3. Selecione **myBackendPool**.

4. Em **alvos de backend**, **tipo alvo,** selecione **a máquina virtual** da lista de drop-down.

5. No **Target,** selecione as máquinas virtuais **myVM** e **myVM2** e as suas interfaces de rede associadas a partir das listas de drop-down.

   > [!div class="mx-imgBorder"]
   > ![Adicionar servidores back-end](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. Selecione **Guardar**.

7. Aguarde que a colocação esteja concluída antes de avançar para o próximo passo.

## <a name="test-the-application-gateway"></a>Testar o gateway de aplicação

Embora o IIS não seja obrigado a criar o gateway de aplicações, instalou-o neste quickstart para verificar se o Azure criou com sucesso o gateway de aplicações. Utilize o IIS para testar o gateway de aplicações:

1. Encontre o endereço IP público para a porta de aplicação na sua página **de visão geral.** ![ Registar endereço IP público de gateway de aplicações ](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png) Ou, pode selecionar **Todos os recursos,** inserir *myAGPublicIPAddress* na caixa de pesquisa e, em seguida, selecioná-lo nos resultados da pesquisa. O Azure exibe o endereço IP público na página **'Vista Geral'.**
2. Copie o endereço IP público e, em seguida, cole-o na barra de endereço do seu navegador para navegar nesse endereço IP.
3. Verifique a resposta. Uma resposta válida verifica que o gateway de aplicações foi criado com sucesso e pode conectar-se com sucesso com o backend.

   ![Testar o gateway de aplicação](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

   Refresque o navegador várias vezes e deverá ver ligações tanto para o myVM como para o myVM2.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não precisar dos recursos que criou com o gateway de aplicações, elimine o grupo de recursos. Ao eliminar o grupo de recursos, também remove o gateway de aplicação e todos os recursos relacionados.

Para eliminar o grupo de recursos:

1. No menu do portal Azure, selecione **grupos de recursos** ou procure e selecione *grupos de Recursos.*
2. Na página **de grupos de recursos,** procure **o myResourceGroupAG** na lista e, em seguida, selecione-o.
3. Na **página** do grupo Recursos , selecione **Eliminar grupo de recursos**.
4. Introduza *myResourceGroupAG* para **escrever o nome do grupo de recursos** e, em seguida, selecione **Eliminar**

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Configurar um gateway de aplicações com rescisão de TLS usando o portal Azure](create-ssl-portal.md)
