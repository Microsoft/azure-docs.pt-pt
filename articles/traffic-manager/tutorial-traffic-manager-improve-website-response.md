---
title: Tutorial - encaminhar o tráfego para melhorar a resposta do Web site utilizando o Gestor de tráfego do Azure
description: Este artigo de tutorial descreve como criar um perfil do Gestor de tráfego para criar um Web site de elevada capacidade de resposta.
services: traffic-manager
author: asudbring
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: allensu
ms.openlocfilehash: 304beeae02da5836ba88a56d7166fc681e263501
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258363"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Tutorial: Melhorar a resposta do Web site utilizando o Gestor de tráfego

Este tutorial descreve como utilizar o Gestor de tráfego para criar um site de elevada capacidade de resposta ao direcionar o tráfego de utilizador para o Web site com a menor latência. Normalmente, o Centro de dados com a latência mais baixa é aquele que está mais próximo na distância geográfica.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar duas VMs que executam um site básico no IIS
> * Criar duas VMs de teste para ver o Gestor de Tráfego em ação
> * Configurar o nome DNS das VMs que executam o IIS
> * Criar um perfil do Gestor de tráfego para um desempenho melhorado de site
> * Adicionar pontos finais da VM ao perfil do Gestor de Tráfego
> * Ver o Gestor de Tráfego em ação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para ver o Gestor de Tráfego em ação, este tutorial requer que implemente o seguinte:

- duas instâncias do Web sites basic em execução em diferentes regiões do Azure - **E.U.A. Leste** e **Europa Ocidental**.
- dois VMs de teste para testar o Gestor de tráfego - uma VM na **E.U.A. Leste** e a segunda VM na **Europa Ocidental**. O teste de VMs são usados para ilustrar como o Gestor de tráfego encaminha o tráfego de utilizador para o site no qual está em execução na mesma região pois ele fornece a latência mais baixa.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

### <a name="create-websites"></a>Criar sites

Nesta secção, vai criar duas instâncias do site que fornecem os dois pontos finais do serviço ao perfil do Gestor de Tráfego em duas regiões do Azure. Para criar os dois sites, siga estes passos:

1. Crie duas VMs para a execução de um site básico - uma nos **E.U.A. Leste** e outra na **Europa Ocidental**.
2. Instale o servidor do IIS em cada VM e atualize a página predefinida do site que descreve o nome da VM à qual o utilizador está ligado quando visita o site.

#### <a name="create-vms-for-running-websites"></a>Criar VMs para a execução de sites

Nesta secção, vai criar duas VMs *myIISVMEastUS* e *myIISVMWestEurope* no **E.U.A. Leste** e **Europa Ocidental** regiões do Azure.

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

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalar o IIS e personalizar a página Web predefinida

Nesta secção, instalar o servidor IIS em duas VMs *myIISVMEastUS* e *myIISVMWestEurope*e, em seguida, atualize a página de Web site predefinido. Quando visitar o site de um browser, a página personalizada do site mostra o nome da VM à qual se está a ligar.

1. Selecione **Todos os recursos** no menu do lado esquerdo e, na lista de recursos, clique na *myIISVMEastUS* que se encontra localizada no grupo de recursos *myResourceGroupTM1*.
2. Na página **Descrição geral**, clique em **Ligar** e, em seguida, em **Ligar à máquina virtual**, selecione **Transferir ficheiro RDP**.
3. Abra o ficheiro rdp transferido. Se lhe for pedido, selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM. Poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM.
4. Selecione **OK**.
5. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber o aviso, selecione **Sim** ou **Continuar** para prosseguir com a ligação.
6. No ambiente de trabalho do servidor, navegue para **Ferramentas Administrativas do Windows**>**Gestor de Servidor**.
7. Inicie o Windows PowerShell na VM1 e utilize os seguintes comandos para instalar o servidor de ISS e atualizar o ficheiro html predefinido.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

     ![Instalar o IIS e personalizar a página Web](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)
8. Feche a ligação RDP a *myIISVMEastUS*.
9. Repita os passos 1 a 8 com através da criação de uma ligação RDP com a VM *myIISVMWestEurope* dentro do *myResourceGroupTM2* grupo de recursos para instalar o IIS e personalizar a sua página da web padrão.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurar os nomes DNS para as VMs que executam o IIS

O Gestor de Tráfego encaminha o tráfego do utilizador, baseando-se no nome DNS dos pontos finais do serviço. Nesta secção, vai configurar os nomes DNS para os servidores IIS - *myIISVMEastUS* e *myIISVMWestEurope*.

1. Clique em **Todos os recursos** no menu do lado esquerdo e, na lista de recursos, selecione *myIISVMEastUS* que se encontra localizado no grupo de recursos *myResourceGroupTM1*.
2. Na página **Descrição geral**, em **Nome DNS**, selecione **Configurar**.
3. Na página **Configuração**, na etiqueta do nome DNS, adicione um nome exclusivo e, em seguida, selecione **Guardar**.
4. Repita os passos 1 a 3, para a VM com o nome *myIISVMWestEurope* que se encontra no *myResourceGroupTM2* grupo de recursos.

### <a name="create-test-vms"></a>Criar VMs de teste

Nesta secção, vai criar uma VM (*myVMEastUS* e *myVMWestEurope*) em cada região do Azure (**E.U.A. Leste** e **Europa Ocidental**). Irá utilizar estas VMs para testar a forma como o Gestor de Tráfego encaminha o tráfego para o servidor do IIS mais próximo quando navega até ao site.

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

Crie um perfil do Gestor de tráfego que direciona o tráfego de utilizadores, enviando-as para o ponto final com latência mais baixa.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso** > **Rede** > **Perfil do Gestor de Tráfego** > **Criar**.
2. Em **Criar perfil do Gestor de Tráfego**, introduza ou selecione as informações seguintes, aceite as predefinições das definições restantes e selecione **Criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Name                   | Este nome tem de ser exclusivo na zona trafficmanager.net e dá origem ao nome DNS, trafficmanager.net, que é utilizado para aceder ao perfil do seu Gestor de Tráfego.                                   |
    | Método de encaminhamento          | Selecione o **desempenho** método de encaminhamento.                                       |
    | Subscrição            | Selecione a sua subscrição.                          |
    | Grupo de recursos          | Selecione o grupo de recursos *myResourceGroupTM1*. |
    | Location                | Selecione **E.U.A. Leste**. Esta definição refere-se à localização do grupo de recursos e não tem qualquer impacto no perfil do Gestor de Tráfego que vai ser implementado globalmente.                              |
    |

    ![Criar um perfil do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

Adicione as duas VMs com o IIS servidores – *myIISVMEastUS* & *myIISVMWestEurope* para encaminhar o tráfego de utilizador para o ponto final mais próximo ao usuário.

1. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que criou na secção anterior e selecione-o nos resultados apresentados.
2. Em **Perfil do Gestor de Tráfego** , na secção **Definições**, clique em **Pontos Finais** e em **Adicionar**.
3. Introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **OK**:

    | Definição                 | Value                                              |
    | ---                     | ---                                                |
    | Type                    | Ponto final do Azure                                   |
    | Name           | myEastUSEndpoint                                        |
    | Tipo de recurso de destino           | Endereço IP Público                          |
    | Recurso de destino          | **Escolha um endereço IP público** para mostrar a lista de recursos com endereços IP públicos na mesma subscrição. Em **Recurso**, selecione o endereço IP público com o nome *myIISVMEastUS-ip*. Este é o endereço IP público da VM do servidor do IIS na região E.U.A. Leste.|
    |        |           |

4. Repita os passos 2 e 3 para adicionar outro ponto final com o nome *myWestEuropeEndpoint* para o endereço IP público *myIISVMWestEurope-ip* que está associado a VM com o nome do servidor IIS  *myIISVMWestEurope*.
5. Quando a adição de ambos os pontos finais estiver concluída, estes são apresentados em **Perfil do Gestor de Tráfego**, juntamente com o respetivo estado de monitorização como **Online**.

    ![Adicionar um ponto final do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Testar o perfil do Gestor de Tráfego

Nesta secção, vai testar a forma como o Gestor de tráfego encaminha o tráfego de utilizador para as VMs mais próximos em execução o Web site para fornecer uma latência mínima. Para ver o Gestor de Tráfego em ação, execute os seguintes passos:

1. Determine o nome DNS do perfil do seu Gestor de Tráfego.
2. Veja o Gestor de Tráfego em ação da forma que se segue:
    - Da VM de teste (*myVMEastUS*) que se encontra no **E.U.A. Leste** região, num browser, navegue para o nome DNS do perfil do Traffic Manager.
    - Da VM de teste (*myVMWestEurope*) que se encontra no **Europa Ocidental** região, num browser, navegue para o nome DNS do perfil do Traffic Manager.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Determinar o nome DNS do perfil do Gestor de Tráfego

Para simplificar, utilize neste tutorial o nome DNS do perfil do Gestor de Tráfego para visitar os sites.

Pode determinar o nome DNS do perfil do Gestor de Tráfego da seguinte forma:

1. Na barra de pesquisa do portal, procure o nome do **perfil do Gestor de Tráfego** que criou na secção anterior. Nos resultados que são apresentados, clique no perfil do Gestor de Tráfego.
2. Clique em **Descrição geral**.
3. O **Perfil do Gestor de Tráfego** mostra o nome DNS do perfil que acabou de criar. Nas implementações de produção, configure um nome de domínio personalizado associado ao nome de domínio do Gestor de Tráfego, utilizando um registo CNAME do DNS.

   ![Nome DNS do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação

Nesta seção, pode ver o Gestor de Tráfego em ação.

1. Selecione **Todos os recursos** no menu do lado esquerdo e, na lista de recursos, clique na *myVMEastUS* que se encontra localizada no grupo de recursos *myResourceGroupTM1*.
2. Na página **Descrição geral**, clique em **Ligar** e, em seguida, em **Ligar à máquina virtual**, selecione **Transferir ficheiro RDP**.
3. Abra o ficheiro rdp transferido. Se lhe for pedido, selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM. Poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM.
4. Selecione **OK**.
5. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber o aviso, selecione **Sim** ou **Continuar** para prosseguir com a ligação.
1. Num browser, na VM *myVMEastUS*, introduza o nome DNS do perfil do Gestor de Tráfego para ver o seu site. Uma vez que a VM localizado em **E.U.A. Leste**, são encaminhados para o site mais próximo hospedado no servidor do IIS mais próximo *myIISVMEastUS* que se encontra no **E.U.A. Leste**.

   ![Testar o perfil do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Em seguida, ligar à VM *myVMWestEurope* localizado na **Europa Ocidental** utilize os passos 1 a 5 e navegue para o nome de domínio de perfil do Gestor de tráfego desta VM. Uma vez que a VM localizado em **Europa Ocidental**, agora é encaminhados para o Web site alojado no mais próximo do servidor do IIS *myIISVMWestEurope* que se encontra no **Europa Ocidental**.

   ![Testar o perfil do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Eliminar o perfil do Gestor de Tráfego

Quando já não for necessário, elimine os grupos de recursos (**ResourceGroupTM1** e **ResourceGroupTM2**). Para tal, selecione o grupo de recursos (**ResourceGroupTM1** ou **ResourceGroupTM2**) e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Distribuir o tráfego para um conjunto de pontos finais](traffic-manager-configure-weighted-routing-method.md)
