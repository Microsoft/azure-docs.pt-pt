---
title: Configure o encaminhamento de tráfego de subnet - Gestor de Tráfego Azure
description: Este artigo explica como configurar o Gestor de Tráfego para desviar o tráfego de subredes específicas.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: rohink
ms.openlocfilehash: 60cddce610d223433d0ffe1f6b9234625aca9881
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76938744"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Direcionar tráfego para pontos finais específicos com base na sub-rede do utilizador com o Gestor de Tráfego

Este artigo descreve como configurar o método de encaminhamento do tráfego da sub-rede. O método de encaminhamento do tráfego da **Sub-rede** permite-lhe mapear um conjunto de intervalos de endereços IP para pontos finais específicos e quando um pedido é recebido pelo Gestor de Tráfego, este inspeciona o IP de origem do pedido e devolve o ponto final associado ao mesmo.

No cenário discutido neste artigo, utilizando o encaminhamento de sub-rede, dependendo do endereço IP da consulta do utilizador, o tráfego é encaminhado para um site interno ou para um site de produção.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Para ver o Gestor de Tráfego em ação, este tutorial requer que implemente o seguinte:
- dois sites básicos em execução em diferentes regiões do Azure – **E.U.A. Leste** (funciona como site interno) e **Europa Ocidental** (funciona como site de produção).
- duas VMs de teste para testar o Gestor de Tráfego – uma VM nos **E.U.A. Leste** e a outra VM na **Europa Ocidental**.

As VMs de teste são utilizadas para ilustrar o modo como o Gestor de Tráfego encaminha o tráfego do utilizador para o site interno ou para o site de produção com base na sub-rede de onde provém a consulta do utilizador.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

### <a name="create-websites"></a>Criar sites

Nesta secção, vai criar duas instâncias do site que fornecem os dois pontos finais do serviço ao perfil do Gestor de Tráfego em duas regiões do Azure. Para criar os dois sites, siga estes passos:
1. Crie duas VMs para a execução de um site básico - uma nos **E.U.A. Leste** e outra na **Europa Ocidental**.
2. Instale o servidor do IIS em cada VM e atualize a página predefinida do site que descreve o nome da VM à qual o utilizador está ligado quando visita o site.

#### <a name="create-vms-for-running-websites"></a>Criar VMs para a execução de sites
Nesta secção, cria-se dois VMs *myEndpointVMEastUS* e *myEndpointVMWEurope* nas regiões **leste dos EUA** e West **Europe** Azure.

1. No canto superior esquerdo do portal Azure, selecione **Criar um recurso** > **Compute** > **Windows Server 2016 VM**.
2. Introduza ou selecione as seguintes informações em **Informações Básicas**, aceite as predefinições das restantes definições e, em seguida, selecione **Criar**:

    |Definição|Valor|
    |---|---|
    |Nome|myIISVMEastUS|
    |Nome de utilizador| Introduza um nome de utilizador à sua escolha.|
    |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupo de recursos| Selecione **Novo** e, em seguida, escreva *o meu ResourceGroupTM1*.|
    |Localização| Selecione **East US**.|
    |||

4. Selecione um tamanho de VM em **Escolher um tamanho**.
5. Selecione os seguintes valores para **Definições** e, em seguida, selecione **OK**:
    
    |Definição|Valor|
    |---|---|
    |Rede virtual| Selecione **Rede virtual** em **Criar rede virtual**. Para **Nome**, introduza *myVNet1*; para sub-rede, introduza *mySubnet*.|
    |Grupo de Segurança de Rede|Selecione **Básico** e, no menu pendente **Selecionar portas de entrada públicas**, selecione **HTTP** e **RDP** |
    |Diagnósticos de arranque|Selecione **Desativado**.|
    |||

6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implementação da VM.

7. Conclua os passos 1 a 6 novamente, com as seguintes alterações:

    |Definição|Valor|
    |---|---|
    |Grupo de recursos | Selecione **Novo** e introduza *myResourceGroupTM2*|
    |Localização|Europa ocidental|
    |Nome da VM | myIISVMWEurope|
    |Rede virtual | Selecione **Rede virtual** em **Criar rede virtual**. Para **Nome**, introduza *myVNet2*; para sub-rede, introduza *mySubnet*.|
    |||

8. A criação das VMs demora alguns minutos. Não prossiga com os restantes passos até que ambas as VMs tenham sido criadas.

   ![Criar uma VM](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalar o IIS e personalizar a página Web predefinida

Nesta secção, instala o servidor IIS nos dois VMs - *myIISVMEastUS*  & *myIISVMWEurope*- e depois atualiza a página do site predefinido. Quando visitar o site de um browser, a página personalizada do site mostra o nome da VM à qual se está a ligar.

1. Selecione **Todos os recursos** no menu do lado esquerdo e, na lista de recursos, clique na *myIISVMEastUS* que se encontra localizada no grupo de recursos *myResourceGroupTM1*.
2. Na página **Descrição geral**, clique em **Ligar** e, em seguida, em **Ligar à máquina virtual**, selecione **Transferir ficheiro RDP**.
3. Abra o ficheiro rdp transferido. Se lhe for pedido, selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM. Poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM.
4. Selecione **OK**.
5. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber o aviso, selecione **Sim** ou **Continuar** para prosseguir com a ligação.
6. No ambiente de trabalho do servidor, navegue para o**Gestor de Servidores**de **Ferramentas Administrativas**>do Windows .
7. Lance o Windows PowerShell no *myIISVMEastUS* e utilize os seguintes comandos para instalar o servidor IIS e atualizar o ficheiro htm predefinido.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. Feche a ligação RDP a *myIISVMEastUS*.
9. Repita os passos 1-6 com a criação de uma ligação RDP com o *VM myIISVMWEurope* dentro do grupo de recursos *myResourceGroupTM2* para instalar o IIS e personalizar a sua página web predefinida.
10. Lance o Windows PowerShell no *myIISVMWEurope* e utilize os seguintes comandos para instalar o servidor IIS e atualizar o ficheiro htm predefinido.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurar os nomes DNS para as VMs que executam o IIS

O Gestor de Tráfego encaminha o tráfego do utilizador, baseando-se no nome DNS dos pontos finais do serviço. Nesta secção, configura os nomes DNS para os servidores IIS - *myIISVMEastUS* e *myIISVMWEurope*.

1. Clique em **Todos os recursos** no menu do lado esquerdo e, na lista de recursos, selecione *myIISVMEastUS* que se encontra localizado no grupo de recursos *myResourceGroupTM1*.
2. Na página **Descrição geral**, em **Nome DNS**, selecione **Configurar**.
3. Na página **Configuração**, na etiqueta do nome DNS, adicione um nome exclusivo e, em seguida, selecione **Guardar**.
4. Repita os passos 1 a 3 para a VM com o nome *myIISVMWEurope* que se encontra localizada no grupo de recursos *myResourceGroupTM1*.

### <a name="create-test-vms"></a>Criar VMs de teste

Nesta secção, cria-se um VM *(mVMEastUS* e *myVMWestEurope)* em cada região de Azure **(Leste dos EUA** e **Europa Ocidental).** Irá utilizar estas VMs para testar a forma como o Gestor de Tráfego encaminha o tráfego para o servidor do IIS mais próximo quando navega até ao site.

1. No canto superior esquerdo do portal Azure, selecione **Criar um recurso** > **Compute** > **Windows Server 2016 VM**.
2. Introduza ou selecione as seguintes informações em **Informações Básicas**, aceite as predefinições das restantes definições e, em seguida, selecione **Criar**:

    |Definição|Valor|
    |---|---|
    |Nome|myVMEastUS|
    |Nome de utilizador| Introduza um nome de utilizador à sua escolha.|
    |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Grupo de recursos| Selecione **Existente** e selecione *myResourceGroupTM1*.|
    |||

4. Selecione um tamanho de VM em **Escolher um tamanho**.
5. Selecione os seguintes valores para **Definições** e, em seguida, selecione **OK**:

    |Definição|Valor|
    |---|---|
    |Rede virtual| Selecione **Rede virtual** em **Criar rede virtual**. Para **Nome**, introduza *myVNet3*; para sub-rede, introduza *mySubnet3*.|
    |Grupo de Segurança de Rede|Selecione **Básico** e, no menu pendente **Selecionar portas de entrada públicas**, selecione **HTTP** e **RDP** |
    |Diagnósticos de arranque|Selecione **Desativado**.|
    |||

6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implementação da VM.

7. Execute novamente os passos 1 a 5, com as seguintes alterações:

    |Definição|Valor|
    |---|---|
    |Nome da VM | *myVMWEurope*|
    |Grupo de recursos | Selecione **Existente** e, em seguida, digite *myResourceGroupTM2*|
    |Rede virtual | Selecione **Rede virtual** em **Criar rede virtual**. Para **Nome**, introduza *myVNet4*; para sub-rede, introduza *mySubnet4*.|
    |||

8. A criação das VMs demora alguns minutos. Não prossiga com os restantes passos até que ambas as VMs tenham sido criadas.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego
Crie um perfil para o Gestor de Tráfego que lhe permita devolver determinados pontos finais com base no IP de origem do pedido.

1. No lado superior esquerdo do ecrã, selecione **Criar um** > **perfil** > de Gestor de Tráfego de**Rede** > de recursos**Create**.
2. Em **Criar perfil do Gestor de Tráfego**, introduza ou selecione as informações seguintes, aceite as predefinições das definições restantes e selecione **Criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Nome                   | Este nome tem de ser exclusivo na zona trafficmanager.net e dá origem ao nome DNS, trafficmanager.net, que é utilizado para aceder ao perfil do seu Gestor de Tráfego.                                   |
    | Método de encaminhamento          | Selecione o método de encaminhamento da **Sub-rede**.                                       |
    | Subscrição            | Selecione a sua subscrição.                          |
    | Grupo de recursos          | Selecione **Existente** e introduza *myResourceGroupTM1*. |
    | |                              |
    |

    ![Criar um perfil do Gestor de Tráfego](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

Adicione os dois VMs que executam os servidores IIS - *myIISVMEastUS* & *myIISVMWEurope* para direcionar o tráfego do utilizador com base na subnet da consulta do utilizador.

1. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que criou na secção anterior e selecione-o nos resultados apresentados.
2. Em **Perfil do Gestor de Tráfego **, na secção **Definições**, clique em **Pontos Finais** e em **Adicionar**.
3. Introduza, ou selecione, as seguintes informações, aceite as predefinições para as definições restantes e, em seguida, selecione **OK:**

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Tipo                    | Ponto final do Azure                                   |
    | Nome           | myTestWebSiteEndpoint                                        |
    | Tipo de recurso de destino           | Endereço IP Público                          |
    | Recurso de destino          | **Escolha um endereço IP público** para mostrar a lista de recursos com endereços IP públicos na mesma subscrição. Em **Recurso**, selecione o endereço IP público com o nome *myIISVMEastUS-ip*. Este é o endereço IP público da VM do servidor do IIS na região E.U.A. Leste.|
    |  Definições de encaminhamento de sub-rede    |   Adicione o endereço IP do *myVMEastUS* test VM. Qualquer consulta de utilizador originária deste VM será direcionada para o *myTestWebSiteEndpoint*.    |

4. Repita os passos 2 e 3 para adicionar outro ponto final chamado *myProductionEndpoint* para o endereço IP público *myIISVMWEurope-ip* que está associado ao servidor IIS VM chamado *myIISVMWEurope*. Para as definições de **encaminhamento subnet,** adicione o endereço IP do vM de teste - *myVMWestEurope*. Qualquer consulta do utilizador deste teste VM será encaminhada para o ponto final - *myProductionWebsiteEndpoint*.
5. Quando a adição de ambos os pontos finais estiver concluída, estes são apresentados em **Perfil do Gestor de Tráfego**, juntamente com o respetivo estado de monitorização como **Online**.

    ![Adicionar um ponto final do Gestor de Tráfego](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)

## <a name="test-traffic-manager-profile"></a>Testar o perfil do Gestor de Tráfego
Nesta secção, irá testar a forma como o Gestor de Tráfego encaminha o tráfego de utilizador de uma determinada sub-rede para um ponto final específico. Para ver o Gestor de Tráfego em ação, execute os seguintes passos:
1. Determine o nome DNS do perfil do seu Gestor de Tráfego.
2. Veja o Gestor de Tráfego em ação da forma que se segue:
    - A partir do teste VM (*myVMEastUS)* que está localizado na região **leste dos EUA,** num navegador web, navegue até ao nome DNS do seu perfil de Traffic Manager.
    - A partir do teste VM (*myVMEastUS)* que está localizado na região da **Europa Ocidental,** num navegador web, navegue até ao nome DNS do seu perfil de Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Determinar o nome DNS do perfil do Gestor de Tráfego
Para simplificar, utilize neste tutorial o nome DNS do perfil do Gestor de Tráfego para visitar os sites.

Pode determinar o nome DNS do perfil do Gestor de Tráfego da seguinte forma:

1. Na barra de pesquisa do portal, procure o nome de perfil do Gestor de **Tráfego** que criou na secção anterior. Nos resultados que são apresentados, clique no perfil do Gestor de Tráfego.
1. Clique em **Descrição geral**.
2. O **Perfil do Gestor de Tráfego** mostra o nome DNS do perfil que acabou de criar. Nas implementações de produção, configure um nome de domínio personalizado associado ao nome de domínio do Gestor de Tráfego, utilizando um registo CNAME do DNS.

   ![Nome DNS do Gestor de Tráfego](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação
Nesta seção, pode ver o Gestor de Tráfego em ação.

1. Selecione **Todos os recursos** no menu do lado esquerdo e, na lista de recursos, clique na *myVMEastUS* que se encontra localizada no grupo de recursos *myResourceGroupTM1*.
2. Na página **Descrição geral**, clique em **Ligar** e, em seguida, em **Ligar à máquina virtual**, selecione **Transferir ficheiro RDP**.
3. Abra o ficheiro rdp transferido. Se lhe for pedido, selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM. Poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM.
4. Selecione **OK**.
5. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber o aviso, selecione **Sim** ou **Continuar** para prosseguir com a ligação.
1. Num browser, na VM *myVMEastUS*, introduza o nome DNS do perfil do Gestor de Tráfego para ver o seu site. Uma vez que o endereço IP VM *myVMEastUS* está associado ao ponto final *myIISVMEastUS*, o navegador web lança o servidor do site Test - *myIISVMEastUS*.

   ![Testar o perfil do Gestor de Tráfego](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. Em seguida, ligue-se ao VM *myVMWestEurope* localizado na **Europa Ocidental** usando os passos 1-5 e navegue para o nome de domínio do perfil do Gestor de Tráfego a partir deste VM. Uma vez que o endereço IP VM *myVMWestEurope* está associado ao ponto final *myIISVMEastUS*, o navegador web lança o servidor do site Test - *myIISVMWEurope*.

## <a name="delete-the-traffic-manager-profile"></a>Eliminar o perfil do Gestor de Tráfego
Quando já não for necessário, elimine os grupos de recursos (**ResourceGroupTM1** e **ResourceGroupTM2**). Para tal, selecione o grupo de recursos (**ResourceGroupTM1** ou **ResourceGroupTM2**) e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o [método de encaminhamento de tráfego ponderado](traffic-manager-configure-weighted-routing-method.md).
- Saiba mais sobre o [método de encaminhamento prioritário](traffic-manager-configure-priority-routing-method.md).
- Saiba mais sobre o [método de encaminhamento geográfico](traffic-manager-configure-geographic-routing-method.md).
