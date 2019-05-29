---
title: Gestor de tráfego do Azure tutorial - encaminhar o tráfego para pontos de extremidade ponderados-
description: Este artigo de tutorial descreve como encaminhar tráfego para pontos finais ponderados com o Gestor de Tráfego.
services: traffic-manager
author: asudbring
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: allensu
ms.openlocfilehash: f9e2b6f6a45279c52e19a63509c57fb34e739330
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258378"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>Tutorial: Controlar o encaminhamento de tráfego com pontos de extremidade ponderados utilizando o Gestor de tráfego

Este tutorial descreve como utilizar o Gestor de Tráfego do Azure para controlar o encaminhamento de tráfego do utilizador entre pontos finais com o método de encaminhamento ponderado. Com este método de encaminhamento, o utilizador atribui ponderações a cada ponto final na configuração do perfil do Gestor de Tráfego. Em seguida, o tráfego do utilizador é encaminhado com base na ponderação atribuída a cada ponto final. A ponderação é um número inteiro entre 1 e 1000. Quanto maior for o valor de ponderação atribuído a um ponto final, maior será a sua prioridade.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar duas VMs que executam um site básico no IIS.
> * Criar duas VMs de teste para ver o Gestor de Tráfego em ação.
> * Configurar um nome DNS para as VMs que executam o IIS.
> * Criar um perfil do Gestor de Tráfego.
> * Adicionar pontos finais da VM ao perfil do Gestor de Tráfego.
> * Veja o Gestor de Tráfego em ação.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para ver o Gestor de Tráfego em ação, implemente o seguinte para este tutorial:

- Duas instâncias de Web sites basic em execução em diferentes regiões do Azure: E.U.A. leste e Europa Ocidental.
- Duas VMs de teste para testar o Gestor de Tráfego: uma VM nos E.U.A. Leste e a outra VM na Europa Ocidental. As VMs de teste são utilizadas para ilustrar de que forma o Gestor de Tráfego encaminha o tráfego do utilizador para um site com uma ponderação mais alta atribuída ao respetivo ponto final.

### <a name="sign-in-to-azure"></a>Inicie sessão no  Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

### <a name="create-websites"></a>Criar sites

Nesta secção, vai criar duas instâncias do site que fornecem os dois pontos finais do serviço ao perfil do Gestor de Tráfego em duas regiões do Azure. Para criar os dois sites, execute os seguintes passos:

1. Crie duas VMs para a execução de um site básico: uma nos E.U.A. Leste e a outra na Europa Ocidental.
2. Instale um servidor de IIS em cada VM. Atualize a página Web predefinida que descreve o nome da VM à qual o utilizador está ligado quando visita o site.

#### <a name="create-vms-for-running-websites"></a>Criar VMs para a execução de sites

Nesta secção, vai criar duas VMs (*myIISVMEastUS* e *myIISVMWestEurope*) nas regiões E.U.A. leste e oeste Europa do Azure.

1. No canto superior, esquerda do portal do Azure, selecione **criar um recurso** > **computação** > **2019 do Windows Server Datacenter**.
2. Na **criar uma máquina virtual**, escreva ou selecione os seguintes valores no **Noções básicas** separador:

   - **Subscrição** > **grupo de recursos**: Selecione **criar novo** e, em seguida, escreva **myResourceGroupTM1**.
   - **Detalhes de instância** > **nome da Máquina Virtual**: Tipo *myIISVMEastUS*.
   - **Detalhes de instância** > **região**:  Selecione **E.U.A. Leste**.
   - **Conta de administrador** > **Username**:  Introduza um nome de utilizador à sua escolha.
   - **Conta de administrador** > **palavra-passe**:  Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regras de porta de entrada** > **portas de entrada públicas**: Selecione **permitir portas selecionadas**.
   - **Regras de porta de entrada** > **Selecione as portas de entrada**: Selecione **RDP** e **HTTP** na solicitação caixa pendente.

3. Selecione o **gerenciamento** separador ou selecione **seguinte: Discos**, em seguida, **seguinte: Networking**, em seguida, **seguinte: Gestão**. Sob **monitorização**, defina **diagnósticos de arranque** para **desativar**.
4. Selecione **Rever + criar**.
5. Reveja as definições e, em seguida, clique em **criar**.  
6. Siga os passos para criar uma segunda VM denominada *myIISVMWestEurope*, com um **grupo de recursos** nome do *myResourceGroupTM2*, um **localização**dos *Europa Ocidental*e todas as outras definições igual *myIISVMEastUS*.
7. A criação das VMs demora alguns minutos. Não prossiga com os restantes passos até que ambas as VMs tenham sido criadas.

![Criar uma VM](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-webpage"></a>Instalar o IIS e personalizar a página Web predefinida

Nesta secção, instale o servidor IIS nas duas VMs myIISVMEastUS e myIISVMWestEurope e, em seguida, atualize a página Web predefinida. Quando visitar o site a partir de um browser, a página Web personalizada mostra o nome da VM à qual se está a ligar.

1. Selecione **Todos os recursos** no menu da esquerda. Na lista de recursos, selecione **myIISVMEastUS** no grupo de recursos **myResourceGroupTM1**.
2. Na página **Descrição geral**, selecione **Ligar**. Em **Ligar à máquina virtual**, selecione **Transferir Ficheiro RDP**.
3. Abra o ficheiro .rdp transferido. Se lhe for pedido, selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou quando criou a VM. Poderá ter de selecionar **Mais opções** > **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM.
4. Selecione **OK**.
5. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber o aviso, selecione **Sim** ou **Continuar** para prosseguir com a ligação.
6. No ambiente de trabalho do servidor, navegue para **Ferramentas Administrativas do Windows** > **Gestor de Servidor**.
7. Abra o Windows PowerShell na VM1. Utilize os seguintes comandos para instalar o servidor de ISS e atualizar o ficheiro .htm predefinido.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![Instalar o IIS e personalizar a página Web](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. Feche a ligação RDP a **myIISVMEastUS**.
9. Repita os passos 1 a 8. Crie uma ligação de RDP com a VM **myIISVMWestEurope** dentro do **myResourceGroupTM2** grupo de recursos, para instalar o IIS e personalizar a sua página de Web padrão.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurar os nomes DNS para as VMs que executam o IIS

O Gestor de Tráfego encaminha o tráfego do utilizador com base no nome DNS dos pontos finais de serviço. Nesta secção, vai configurar os nomes DNS para o IIS servidores myIISVMEastUS e myIISVMWestEurope.

1. Selecione **Todos os recursos** no menu da esquerda. Na lista de recursos, selecione **myIISVMEastUS** no grupo de recursos **myResourceGroupTM1**.
2. Na página **Descrição geral**, em **Nome DNS**, selecione **Configurar**.
3. Na página **Configuração**, abaixo da etiqueta de nome DNS, adicione um nome exclusivo. Em seguida, selecione **Guardar**.
4. Repita os passos 1 a 3 para a VM com o nome **myIISVMWestEurope** no **myResourceGroupTM2** grupo de recursos.

### <a name="create-a-test-vm"></a>Criar uma VM de teste

Nesta secção, vai criar uma VM (*myVMEastUS* e *myVMWestEurope*) em cada região do Azure (**E.U.A. Leste** e **Europa Ocidental**). Irá utilizar estas VMs para testar como o Gestor de tráfego encaminha o tráfego para o ponto de extremidade do Web site que tem o valor de peso mais alto.

1. No canto superior, esquerda do portal do Azure, selecione **criar um recurso** > **computação** > **2019 do Windows Server Datacenter**.
2. Na **criar uma máquina virtual**, escreva ou selecione os seguintes valores no **Noções básicas** separador:

   - **Subscrição** > **grupo de recursos**: Select **myResourceGroupTM1**.
   - **Detalhes de instância** > **nome da Máquina Virtual**: Tipo *myVMEastUS*.
   - **Detalhes de instância** > **região**:  Selecione **E.U.A. Leste**.
   - **Conta de administrador** > **Username**:  Introduza um nome de utilizador à sua escolha.
   - **Conta de administrador** > **palavra-passe**:  Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regras de porta de entrada** > **portas de entrada públicas**: Selecione **permitir portas selecionadas**.
   - **Regras de porta de entrada** > **Selecione as portas de entrada**: Selecione **RDP** na solicitação caixa pendente.

3. Selecione o **gerenciamento** separador ou selecione **seguinte: Discos**, em seguida, **seguinte: Networking**, em seguida, **seguinte: Gestão**. Sob **monitorização**, defina **diagnósticos de arranque** para **desativar**.
4. Selecione **Rever + criar**.
5. Reveja as definições e, em seguida, clique em **criar**.  
6. Siga os passos para criar uma segunda VM denominada *myVMWestEurope*, com um **grupo de recursos** nome do *myResourceGroupTM2*, um **localização** dos *Europa Ocidental*e todas as outras definições igual *myVMEastUS*.
7. A criação das VMs demora alguns minutos. Não prossiga com os restantes passos até que ambas as VMs tenham sido criadas.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil do Gestor de Tráfego com base no método de encaminhamento **Ponderado**.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso** > **Rede** > **Perfil do Gestor de Tráfego** > **Criar**.
2. Em **Criar perfil do Gestor de Tráfego**, introduza ou selecione as seguintes informações. Aceite as predefinições das outras definições e selecione **Criar**.

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Name                   | Introduza um nome exclusivo na zona trafficmanager.net. Resulta no nome DNS trafficmanager.net, que serve para aceder ao seu perfil do Gestor de Tráfego.                                   |
    | Método de encaminhamento          | Selecione o método de encaminhamento **Ponderado**.                                       |
    | Subscrição            | Selecione a sua subscrição.                          |
    | Grupo de recursos          | Selecione **Utilizar existente** e, em seguida, selecione **myResourceGroupTM1**. |
    |        |   |

    ![Criar um perfil do Gestor de Tráfego](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

Adicione as duas VMs em execução o myIISVMEastUS de servidores IIS e myIISVMWestEurope, para encaminhar o tráfego de utilizador aos mesmos.

1. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que criou na secção anterior. Selecione o perfil nos resultados que são apresentados.
2. Em **Perfil do Gestor de Tráfego**, na secção **Definições**, selecione **Pontos finais** > **Adicionar**.
3. Introduza ou selecione as seguintes informações. Aceite as predefinições das outras definições e selecione **OK**.

    | Definição                 | Value                                              |
    | ---                     | ---                                                |
    | Type                    | Introduza o ponto final do Azure.                                   |
    | Name           | Introduza **myEastUSEndpoint**.                                        |
    | Tipo de recurso de destino           | Selecione **Endereço IP público**.                          |
    | Recurso de destino          | Escolha um endereço IP público para mostrar a lista de recursos com endereços IP públicos na mesma subscrição. Em **Recurso**, selecione o endereço IP público com o nome **myIISVMEastUS-ip**. Este é o endereço IP público da VM do servidor do IIS na região E.U.A. Leste.|
    |  Peso      | Introduza **100**.        |
    |        |           |

4. Repita os passos 2 e 3 para adicionar outro ponto final com o nome **myWestEuropeEndpoint** para o endereço IP público **myIISVMWestEurope-ip**. Este endereço está associado a VM myIISVMWestEurope com o nome do servidor de IIS. Para **Peso**, introduza **25**.
5. Quando a adição de ambos os pontos finais estiver concluída, estes são apresentados no perfil do Gestor de Tráfego, juntamente com o respetivo estado de monitorização como **Online**.

## <a name="test-the-traffic-manager-profile"></a>Testar o perfil do Gestor de Tráfego

Para ver o Gestor de Tráfego em ação, execute os seguintes passos:

1. Determine o nome DNS do perfil do seu Gestor de Tráfego.
2. Veja o Gestor de Tráfego em ação.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Determinar o nome DNS do perfil do Gestor de Tráfego

Para simplificar, utilize neste tutorial o nome DNS do perfil do Gestor de Tráfego para visitar os sites.

Pode determinar o nome DNS do perfil do Gestor de Tráfego da seguinte forma:

1. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que criou na secção anterior. Nos resultados que são apresentados, selecione o perfil do Gestor de Tráfego.
2. Selecione **Descrição geral**.
3. O perfil do Gestor de Tráfego apresenta o respetivo nome DNS. Nas implementações de produção, vai configurar um nome de domínio personalizado para associar ao nome de domínio do Gestor de Tráfego, utilizando um registo CNAME do DNS.

   ![Nome DNS do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação

Nesta seção, pode ver o Gestor de Tráfego em ação.

1. Selecione **Todos os recursos** no menu da esquerda. Na lista de recursos, selecione **myVMEastUS** no grupo de recursos **myResourceGroupTM1**.
2. Na página **Descrição geral**, selecione **Ligar**. Em **Ligar à máquina virtual**, selecione **Transferir Ficheiro RDP**.
3. Abra o ficheiro .rdp transferido. Se lhe for pedido, selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou quando criou a VM. Poderá ter de selecionar **Mais opções** > **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM.
4. Selecione **OK**.
5. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber o aviso, selecione **Sim** ou **Continuar** para prosseguir com a ligação.
6. Num browser, na VM myVMEastUS, introduza o nome DNS do perfil do Gestor de Tráfego para ver o seu site. É encaminhado para o site alojado no servidor de IIS myIISVMEastUS, porque lhe foi atribuído uma ponderação mais elevada de **100**. MyIISVMWestEurope de servidor IIS é atribuído um valor de peso de ponto de extremidade inferior da **25**.

   ![Testar o perfil do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. Repita os passos 1 a 6 no myVMWestEurope a VM para ver a resposta do Web site ponderada.

## <a name="delete-the-traffic-manager-profile"></a>Eliminar o perfil do Gestor de Tráfego

Quando já não precisar dos grupos de recursos que criou neste tutorial, pode eliminá-los. Para tal, selecione o grupo de recursos (**ResourceGroupTM1** ou **ResourceGroupTM2**) e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Encaminhar tráfego para pontos finais específicos com base na localização geográfica do utilizador](traffic-manager-configure-geographic-routing-method.md)
