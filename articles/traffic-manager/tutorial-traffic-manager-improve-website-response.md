---
title: Tutorial-melhorar a resposta do site com o Gerenciador de tráfego do Azure
description: Este artigo de tutorial descreve como criar um perfil do Gerenciador de tráfego para criar um site altamente responsivo.
services: traffic-manager
author: asudbring
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: allensu
ms.openlocfilehash: 3686e9a7d82f8134b44cd40468c5e430eb2e72f3
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422858"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Tutorial: melhorar a resposta do site usando o Gerenciador de tráfego

Este tutorial descreve como usar o Gerenciador de tráfego para criar um site altamente responsivo, direcionando o tráfego do usuário para o site com a menor latência. Normalmente, o datacenter com a menor latência é aquele mais próximo da distância geográfica.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar duas VMs que executam um site básico no IIS
> * Criar duas VMs de teste para ver o Gestor de Tráfego em ação
> * Configurar o nome DNS das VMs que executam o IIS
> * Criar um perfil do Gerenciador de tráfego para melhorar o desempenho do site
> * Adicionar pontos finais da VM ao perfil do Gestor de Tráfego
> * Ver o Gestor de Tráfego em ação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para ver o Gestor de Tráfego em ação, este tutorial requer que implemente o seguinte:

- Duas instâncias de sites básicos em execução em diferentes regiões do Azure – **leste dos EUA** e **Europa Ocidental**.
- Duas VMs de teste para testar o Gerenciador de tráfego – uma VM no **leste dos EUA** e a segunda VM em **Europa Ocidental**. As VMs de teste são usadas para ilustrar como o Traffic Manager roteia o tráfego do usuário para o site que está sendo executado na mesma região, pois fornece a menor latência.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

### <a name="create-websites"></a>Criar sites

Nesta secção, vai criar duas instâncias do site que fornecem os dois pontos finais do serviço ao perfil do Gestor de Tráfego em duas regiões do Azure. Para criar os dois sites, siga estes passos:

1. Crie duas VMs para a execução de um site básico - uma nos **E.U.A. Leste** e outra na **Europa Ocidental**.
2. Instale o servidor do IIS em cada VM e atualize a página predefinida do site que descreve o nome da VM à qual o utilizador está ligado quando visita o site.

#### <a name="create-vms-for-running-websites"></a>Criar VMs para a execução de sites

Nesta seção, você criará duas VMs *myIISVMEastUS* e *MyIISVMWestEurope* nas regiões **leste dos EUA** e **Europa Ocidental** Azure.

1. No canto superior esquerdo da portal do Azure, selecione **criar um recurso** > **computação** > **Windows Server 2019 datacenter**.
2. Em **criar uma máquina virtual**, digite ou selecione os seguintes valores na guia **noções básicas** :

   -  > **grupo de recursos**de **assinatura** : selecione **criar novo** e digite **myResourceGroupTM1**.
   - **Detalhes da instância** > **nome da máquina virtual**: digite *myIISVMEastUS*.
   - **Detalhes da instância** > **região**: selecione **leste dos EUA**.
   - **Conta de administrador** > **username**: Insira um nome de usuário de sua escolha.
   - **Conta de administrador** > **senha**: Insira uma senha de sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regras de porta de entrada** > **portas de entrada públicas**: selecione **permitir portas selecionadas**.
   - **Regras de porta de entrada** > **selecionar portas de entrada**: selecione **RDP** e **http** na caixa suspensa.

3. Selecione a guia **Gerenciamento** ou selecione **Avançar: discos**e **Avançar: rede**e **próximo: gerenciamento**. Em **monitoramento**, defina **diagnóstico de inicialização** como **desativado**.
4. Selecione **Rever + criar**.
5. Examine as configurações e clique em **criar**.  
6. Siga as etapas para criar uma segunda VM denominada *myIISVMWestEurope*, com um nome de **grupo de recursos** de *myResourceGroupTM2*, um **local** de *Europa Ocidental*e todas as outras configurações iguais a *myIISVMEastUS*.
7. A criação das VMs demora alguns minutos. Não prossiga com os restantes passos até que ambas as VMs tenham sido criadas.

   ![Criar uma VM](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalar o IIS e personalizar a página Web predefinida

Nesta seção, você instala o servidor IIS nas duas VMs *myIISVMEastUS* e *myIISVMWestEurope*e, em seguida, atualiza a página padrão do site. Quando visitar o site de um browser, a página personalizada do site mostra o nome da VM à qual se está a ligar.

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
9. Repita as etapas 1-8 com criando uma conexão RDP com a VM *myIISVMWestEurope* dentro do grupo de recursos *myResourceGroupTM2* para instalar o IIS e personalizar sua página da Web padrão.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurar os nomes DNS para as VMs que executam o IIS

O Gestor de Tráfego encaminha o tráfego do utilizador, baseando-se no nome DNS dos pontos finais do serviço. Nesta seção, você configurará os nomes DNS para os servidores IIS- *myIISVMEastUS* e *myIISVMWestEurope*.

1. Clique em **Todos os recursos** no menu do lado esquerdo e, na lista de recursos, selecione *myIISVMEastUS* que se encontra localizado no grupo de recursos *myResourceGroupTM1*.
2. Na página **Descrição geral**, em **Nome DNS**, selecione **Configurar**.
3. Na página **Configuração**, na etiqueta do nome DNS, adicione um nome exclusivo e, em seguida, selecione **Guardar**.
4. Repita as etapas de 1-3 para a VM chamada *myIISVMWestEurope* que está localizada no grupo de recursos *myResourceGroupTM2* .

### <a name="create-test-vms"></a>Criar VMs de teste

Nesta seção, você criará uma VM (*myVMEastUS* e *myVMWestEurope*) em cada região do Azure (**leste dos EUA** e **Europa Ocidental**). Irá utilizar estas VMs para testar a forma como o Gestor de Tráfego encaminha o tráfego para o servidor do IIS mais próximo quando navega até ao site.

1. No canto superior esquerdo da portal do Azure, selecione **criar um recurso** > **computação** > **Windows Server 2019 datacenter**.
2. Em **criar uma máquina virtual**, digite ou selecione os seguintes valores na guia **noções básicas** :

   - Grupo de **recursos**de > de **assinatura** : selecione **myResourceGroupTM1**.
   - **Detalhes da instância** > **nome da máquina virtual**: digite *myVMEastUS*.
   - **Detalhes da instância** > **região**: selecione **leste dos EUA**.
   - **Conta de administrador** > **username**: Insira um nome de usuário de sua escolha.
   - **Conta de administrador** > **senha**: Insira uma senha de sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regras de porta de entrada** > **portas de entrada públicas**: selecione **permitir portas selecionadas**.
   - **Regras de porta de entrada** > **selecionar portas de entrada**: selecione **RDP** na caixa suspensa.

3. Selecione a guia **Gerenciamento** ou selecione **Avançar: discos**e **Avançar: rede**e **próximo: gerenciamento**. Em **monitoramento**, defina **diagnóstico de inicialização** como **desativado**.
4. Selecione **Rever + criar**.
5. Examine as configurações e clique em **criar**.  
6. Siga as etapas para criar uma segunda VM denominada *myVMWestEurope*, com um nome de **grupo de recursos** de *myResourceGroupTM2*, um **local** de *Europa Ocidental*e todas as outras configurações iguais a *myVMEastUS*.
7. A criação das VMs demora alguns minutos. Não prossiga com os restantes passos até que ambas as VMs tenham sido criadas.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil do Gerenciador de tráfego que direcione o tráfego do usuário enviando-o para o ponto de extremidade com menor latência.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso** > **Rede** > **Perfil do Gestor de Tráfego** > **Criar**.
2. Em **Criar perfil do Gestor de Tráfego**, introduza ou selecione as informações seguintes, aceite as predefinições das definições restantes e selecione **Criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Nome                   | Este nome tem de ser exclusivo na zona trafficmanager.net e dá origem ao nome DNS, trafficmanager.net, que é utilizado para aceder ao perfil do seu Gestor de Tráfego.                                   |
    | Método de encaminhamento          | Selecione o método de roteamento de **desempenho** .                                       |
    | Subscrição            | Selecione a sua subscrição.                          |
    | Grupo de recursos          | Selecione o grupo de recursos *myResourceGroupTM1*. |
    | Localização                | Selecione **E.U.A. Leste**. Esta definição refere-se à localização do grupo de recursos e não tem qualquer impacto no perfil do Gestor de Tráfego que vai ser implementado globalmente.                              |
    |

    ![Criar um perfil do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

Adicione as duas VMs que executam os servidores IIS- *myIISVMEastUS* & *myIISVMWestEurope* para rotear o tráfego do usuário para o ponto de extremidade mais próximo ao usuário.

1. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que criou na secção anterior e selecione-o nos resultados apresentados.
2. Em **Perfil do Gestor de Tráfego** , na secção **Definições**, clique em **Pontos Finais** e em **Adicionar**.
3. Introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **OK**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Tipo                    | Ponto final do Azure                                   |
    | Nome           | myEastUSEndpoint                                        |
    | Tipo de recurso de destino           | Endereço IP Público                          |
    | Recurso de destino          | **Escolha um endereço IP público** para mostrar a lista de recursos com endereços IP públicos na mesma subscrição. Em **Recurso**, selecione o endereço IP público com o nome *myIISVMEastUS-ip*. Este é o endereço IP público da VM do servidor do IIS na região E.U.A. Leste.|
    |        |           |

4. Repita as etapas 2 e 3 para adicionar outro ponto de extremidade chamado *myWestEuropeEndpoint* para o endereço IP público *myIISVMWestEurope-IP* que está associado à VM do servidor IIS denominada *myIISVMWestEurope*.
5. Quando a adição de ambos os pontos finais estiver concluída, estes são apresentados em **Perfil do Gestor de Tráfego**, juntamente com o respetivo estado de monitorização como **Online**.

    ![Adicionar um ponto final do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Testar o perfil do Gestor de Tráfego

Nesta seção, você testará como o Traffic Manager roteia o tráfego do usuário para as VMs mais próximas que executam o site para fornecer latência mínima. Para ver o Gestor de Tráfego em ação, execute os seguintes passos:

1. Determine o nome DNS do perfil do seu Gestor de Tráfego.
2. Veja o Gestor de Tráfego em ação da forma que se segue:
    - Na VM de teste (*myVMEastUS*) que está localizada na região **leste dos EUA** , em um navegador da Web, navegue até o nome DNS do seu perfil do Gerenciador de tráfego.
    - Na VM de teste (*myVMWestEurope*) que está localizada na região **Europa Ocidental** , em um navegador da Web, navegue até o nome DNS do seu perfil do Gerenciador de tráfego.

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
1. Num browser, na VM *myVMEastUS*, introduza o nome DNS do perfil do Gestor de Tráfego para ver o seu site. Desde a VM localizada no **leste dos EUA**, você será encaminhado para o site mais próximo hospedado no servidor IIS mais próximo *myIISVMEastUS* que está localizado no **leste dos EUA**.

   ![Testar o perfil do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Em seguida, conecte-se à VM *myVMWestEurope* localizada em **Europa Ocidental** usando as etapas 1-5 e navegue até o nome de domínio do perfil do Gerenciador de tráfego dessa VM. Como a VM localizada em **Europa Ocidental**, agora você será roteada para o site hospedado em mais próximo do servidor IIS *myIISVMWestEurope* que está localizado em **Europa Ocidental**.

   ![Testar o perfil do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Eliminar o perfil do Gestor de Tráfego

Quando já não for necessário, elimine os grupos de recursos (**ResourceGroupTM1** e **ResourceGroupTM2**). Para tal, selecione o grupo de recursos (**ResourceGroupTM1** ou **ResourceGroupTM2**) e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Distribuir o tráfego para um conjunto de pontos finais](traffic-manager-configure-weighted-routing-method.md)
