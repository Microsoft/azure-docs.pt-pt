---
title: Tutorial - Melhorar a resposta do site com o Gestor de Tráfego do Azure
description: Este artigo tutorial descreve como criar um perfil de Gestor de Tráfego para construir um site altamente responsivo.
services: traffic-manager
author: rohinkoul
Customer intent: As an IT Admin, I want to route traffic so I can improve website response by choosing the endpoint with lowest latency.
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/23/2018
ms.author: rohink
ms.openlocfilehash: f7778b60a5e84f5d8900b8e37bfa655a7915d403
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77136391"
---
# <a name="tutorial-improve-website-response-using-traffic-manager"></a>Tutorial: Melhorar a resposta do site usando o Traffic Manager

Este tutorial descreve como usar o Traffic Manager para criar um website altamente responsivo, direcionando o tráfego do utilizador para o site com a menor latência. Tipicamente, o datacenter com a latência mais baixa é o que está mais próximo na distância geográfica.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar duas VMs que executam um site básico no IIS
> * Criar duas VMs de teste para ver o Gestor de Tráfego em ação
> * Configurar o nome DNS das VMs que executam o IIS
> * Criar um perfil de Gestor de Tráfego para melhorar o desempenho do site
> * Adicionar pontos finais da VM ao perfil do Gestor de Tráfego
> * Ver o Gestor de Tráfego em ação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para ver o Gestor de Tráfego em ação, este tutorial requer que implemente o seguinte:

- Dois casos de sites básicos em execução em diferentes regiões de Azure - **Leste dos EUA** e Europa **Ocidental.**
- Dois VMs de teste para testar o Traffic Manager - um VM no **Leste dos EUA** e o segundo VM na Europa **Ocidental**. Os VMs de teste são usados para ilustrar como o Gestor de Tráfego encaminha o tráfego do utilizador para o website que está a funcionar na mesma região que proporciona a menor latência.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

### <a name="create-websites"></a>Criar sites

Nesta secção, vai criar duas instâncias do site que fornecem os dois pontos finais do serviço ao perfil do Gestor de Tráfego em duas regiões do Azure. Para criar os dois sites, siga estes passos:

1. Crie duas VMs para a execução de um site básico - uma nos **E.U.A. Leste** e outra na **Europa Ocidental**.
2. Instale o servidor do IIS em cada VM e atualize a página predefinida do site que descreve o nome da VM à qual o utilizador está ligado quando visita o site.

#### <a name="create-vms-for-running-websites"></a>Criar VMs para a execução de sites

Nesta secção, cria-se dois VMs *myIISVMEastUS* e *myIISVMWestEurope* nas regiões **leste dos EUA** e West **Europe.**

1. No canto superior esquerdo do portal Azure, selecione **Criar um recurso** > **Compute** > **Windows Server 2019 Datacenter**.
2. Em **Criar uma máquina virtual,** digite ou selecione os seguintes valores no separador **Basics:**

   - **Grupo de** **recursos** > de assinatura : Selecione **Criar novo** e, em seguida, digitar o **meu ResourceGroupTM1**.
   - **Detalhes** da > **nome virtual**da máquina : *Digite myIISVMEastUS*.
   - **Detalhes da instância** > **Região**: Selecione **East US**.
   - **Conta administradora** > **Nome de utilizador**: Introduza um nome de utilizador à sua escolha.
   - **Conta administradora** > **Palavra-passe**: Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regras do porto de entrada** > portas de entrada **públicas**: Selecione permitir **portas selecionadas**.
   - **Regras** da porta de entrada > **Selecione portas de entrada :** Selecione **RDP** e **HTTP** na caixa de puxar para baixo.

3. Selecione o separador **Gestão** ou selecione **Seguinte: Discos,** em seguida **Seguinte: Networking**, em seguida **Seguinte: Gestão**. Sob **monitorização,** descoloque os **diagnósticos da bota** para **desligar**.
4. Selecione **Rever + criar**.
5. Reveja as definições e, em seguida, clique em **Criar**.  
6. Siga os passos para criar um segundo VM chamado *myIISVMWestEurope*, com um nome de grupo de **recursos** do *myResourceGroupTM2*, uma **localização** da *Europa Ocidental*, e todas as outras configurações as mesmas que o *myIISVMEastUS*.
7. A criação das VMs demora alguns minutos. Não prossiga com os restantes passos até que ambas as VMs tenham sido criadas.

   ![Criar uma VM](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalar o IIS e personalizar a página Web predefinida

Nesta secção, instala o servidor IIS nos dois VMs *myIISVMEastUS* e *myIISVMWestEurope*e, em seguida, atualiza a página do site predefinido. Quando visitar o site de um browser, a página personalizada do site mostra o nome da VM à qual se está a ligar.

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
9. Repita os passos 1-8 com a criação de uma ligação RDP com o *VM myIISVMWestEurope* dentro do grupo de recursos *myResourceGroupTM2* para instalar o IIS e personalizar a sua página web predefinida.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurar os nomes DNS para as VMs que executam o IIS

O Gestor de Tráfego encaminha o tráfego do utilizador, baseando-se no nome DNS dos pontos finais do serviço. Nesta secção, configura os nomes DNS para os servidores IIS - *myIISVMEastUS* e *myIISVMWestEurope*.

1. Clique em **Todos os recursos** no menu do lado esquerdo e, na lista de recursos, selecione *myIISVMEastUS* que se encontra localizado no grupo de recursos *myResourceGroupTM1*.
2. Na página **Descrição geral**, em **Nome DNS**, selecione **Configurar**.
3. Na página **Configuração**, na etiqueta do nome DNS, adicione um nome exclusivo e, em seguida, selecione **Guardar**.
4. Repita os passos 1-3, para o VM denominado *myIISVMWestEurope* que está localizado no grupo de recursos *myResourceGroupTM2.*

### <a name="create-test-vms"></a>Criar VMs de teste

Nesta secção, cria-se um VM (*myVMEastUS* e *myVMWestEurope*) em cada região de Azure **(Leste dos EUA** e **Europa Ocidental).** Irá utilizar estas VMs para testar a forma como o Gestor de Tráfego encaminha o tráfego para o servidor do IIS mais próximo quando navega até ao site.

1. No canto superior esquerdo do portal Azure, selecione **Criar um recurso** > **Compute** > **Windows Server 2019 Datacenter**.
2. Em **Criar uma máquina virtual,** digite ou selecione os seguintes valores no separador **Basics:**

   - **Grupo de** **recursos** > de assinatura : Selecione **myResourceGroupTM1**.
   - **Detalhes da > ** **nome virtual**da máquina : *Digite myVMEastUS*.
   - **Detalhes da instância** > **Região**: Selecione **East US**.
   - **Conta administradora** > **Nome de utilizador**: Introduza um nome de utilizador à sua escolha.
   - **Conta administradora** > **Palavra-passe**: Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regras do porto de entrada** > portas de entrada **públicas**: Selecione permitir **portas selecionadas**.
   - **Regras** da porta de entrada > **Selecione as portas de entrada**: Selecione **RDP** na caixa de puxar para baixo.

3. Selecione o separador **Gestão** ou selecione **Seguinte: Discos,** em seguida **Seguinte: Networking**, em seguida **Seguinte: Gestão**. Sob **monitorização,** descoloque os **diagnósticos da bota** para **desligar**.
4. Selecione **Rever + criar**.
5. Reveja as definições e, em seguida, clique em **Criar**.  
6. Siga os passos para criar um segundo VM chamado *myVMWestEurope*, com um nome de **grupo** de recursos do *myResourceGroupTM2*, uma **localização** da *Europa Ocidental*, e todas as outras configurações como *myVMEastUS*.
7. A criação das VMs demora alguns minutos. Não prossiga com os restantes passos até que ambas as VMs tenham sido criadas.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil de Gestor de Tráfego que direcione o tráfego do utilizador enviando-os para o ponto final com menor latência.

1. No canto superior esquerdo do ecrã, selecione **Criar um recurso** > **Rede** > **Perfil do Gestor de Tráfego** > **Criar**.
2. Em **Criar perfil do Gestor de Tráfego**, introduza ou selecione as informações seguintes, aceite as predefinições das definições restantes e selecione **Criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Nome                   | Este nome tem de ser exclusivo na zona trafficmanager.net e dá origem ao nome DNS, trafficmanager.net, que é utilizado para aceder ao perfil do seu Gestor de Tráfego.                                   |
    | Método de encaminhamento          | Selecione o método de encaminhamento **do Desempenho.**                                       |
    | Subscrição            | Selecione a sua subscrição.                          |
    | Grupo de recursos          | Selecione o grupo de recursos *myResourceGroupTM1*. |
    | Localização                | Selecione **E.U.A. Leste**. Esta definição refere-se à localização do grupo de recursos e não tem qualquer impacto no perfil do Gestor de Tráfego que vai ser implementado globalmente.                              |
    |

    ![Criar um perfil do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

Adicione os dois VMs que executam os servidores IIS - *myIISVMEastUS* & *myIISVMWestEurope* para direcionar o tráfego dos utilizadores para o ponto final mais próximo do utilizador.

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

4. Repita os passos 2 e 3 para adicionar outro ponto final chamado *myWestEuropeEndpoint* para o endereço IP público *myIISVMWestEurope-ip* que está associado ao servidor IIS VM chamado *myIISVMWestEurope*.
5. Quando a adição de ambos os pontos finais estiver concluída, estes são apresentados em **Perfil do Gestor de Tráfego**, juntamente com o respetivo estado de monitorização como **Online**.

    ![Adicionar um ponto final do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-endpoint.png)

## <a name="test-traffic-manager-profile"></a>Testar o perfil do Gestor de Tráfego

Nesta secção, testa como o Gestor de Tráfego encaminha o tráfego do utilizador para os VMs mais próximos que executam o website para fornecer a latência mínima. Para ver o Gestor de Tráfego em ação, execute os seguintes passos:

1. Determine o nome DNS do perfil do seu Gestor de Tráfego.
2. Veja o Gestor de Tráfego em ação da forma que se segue:
    - A partir do teste VM (*myVMEastUS)* que está localizado na região **leste dos EUA,** num navegador web, navegue até ao nome DNS do seu perfil de Traffic Manager.
    - A partir do teste VM (*myVMWestEurope)* que está localizado na região da **Europa Ocidental,** num navegador web, navegue até ao nome DNS do seu perfil de Traffic Manager.

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
1. Num browser, na VM *myVMEastUS*, introduza o nome DNS do perfil do Gestor de Tráfego para ver o seu site. Uma vez que o VM localizado no **Leste dos EUA,** você é encaminhado para o site mais próximo hospedado no servidor IIS mais próximo *myIISVMEastUS* que está localizado no **Leste dos EUA**.

   ![Testar o perfil do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

2. Em seguida, ligue-se ao VM *myVMWestEurope* localizado na **Europa Ocidental** usando os passos 1-5 e navegue para o nome de domínio do perfil do Gestor de Tráfego a partir deste VM. Uma vez que o VM localizado na **Europa Ocidental,** está agora encaminhado para o site hospedado no servidor IIS *myIISVMWestEurope* que está localizado na **Europa Ocidental.**

   ![Testar o perfil do Gestor de Tráfego](./media/tutorial-traffic-manager-improve-website-response/westeurope-traffic-manager-test.png)

## <a name="delete-the-traffic-manager-profile"></a>Eliminar o perfil do Gestor de Tráfego

Quando já não for necessário, elimine os grupos de recursos (**ResourceGroupTM1** e **ResourceGroupTM2**). Para tal, selecione o grupo de recursos (**ResourceGroupTM1** ou **ResourceGroupTM2**) e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Distribuir o tráfego para um conjunto de pontos finais](traffic-manager-configure-weighted-routing-method.md)
