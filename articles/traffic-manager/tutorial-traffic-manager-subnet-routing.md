---
title: Tutorial - Encaminhamento de tráfego de sub-redes de configuração com gestor de tráfego Azure
description: Este tutorial explica como configurar o Gestor de Tráfego para encaminhar o tráfego das sub-redes dos utilizadores para pontos finais específicos.
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2021
ms.author: duau
ms.openlocfilehash: 9b916f9942b0459b41d98b952fad072ae48318b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505443"
---
# <a name="tutorial-direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Tutorial: Tráfego direto para pontos finais específicos baseados na sub-rede do utilizador utilizando o Gestor de Tráfego

Este artigo descreve como configurar o método de encaminhamento do tráfego da sub-rede. O método de encaminhamento de tráfego **sub-rede** permite-lhe mapear um conjunto de intervalos de endereços IP para pontos finais específicos. Quando um pedido é recebido pelo Gestor de Tráfego, inspeciona a origem IP do pedido e devolve o ponto final associado ao mesmo.

Neste tutorial, utilizando o encaminhamento de sub-redes, dependendo do endereço IP da consulta do utilizador, o tráfego é encaminhado quer para um website interno quer para um website de produção.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar duas VMs que executam um site básico no IIS
> * Criar duas VMs de teste para ver o Gestor de Tráfego em ação
> * Configurar o nome DNS das VMs que executam o IIS
> * Criar um perfil do Gestor de Tráfego para encaminhar o tráfego como base na sub-rede do utilizador
> * Adicionar pontos finais da VM ao perfil do Gestor de Tráfego
> * Ver o Gestor de Tráfego em ação

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para ver o Gestor de Tráfego em ação, este tutorial requer que você implemente o seguinte:

- dois sites básicos em execução em diferentes regiões do Azure – **E.U.A. Leste** (funciona como site interno) e **Europa Ocidental** (funciona como site de produção).
- duas VMs de teste para testar o Gestor de Tráfego – uma VM nos **E.U.A. Leste** e a outra VM na **Europa Ocidental**.

As VMs de teste são utilizadas para ilustrar o modo como o Gestor de Tráfego encaminha o tráfego do utilizador para o site interno ou para o site de produção com base na sub-rede de onde provém a consulta do utilizador.

### <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

### <a name="create-websites"></a>Criar sites

Nesta secção, vai criar duas instâncias do site que fornecem os dois pontos finais do serviço ao perfil do Gestor de Tráfego em duas regiões do Azure. Para criar os dois sites, siga estes passos:

1. Crie duas VMs para a execução de um site básico - uma nos **E.U.A. Leste** e outra na **Europa Ocidental**.
2. Instale o servidor do IIS em cada VM e atualize a página predefinida do site que descreve o nome da VM à qual o utilizador está ligado quando visita o site.

#### <a name="create-vms-for-running-websites"></a>Criar VMs para a execução de sites

Nesta secção, você cria dois VMs *myIISVMEastUS* e *myIISVMWestEurope* nas regiões **Azure dos EUA** e Da Europa **Ocidental.**

1. No canto superior esquerdo do portal Azure, selecione **Criar um**  >  **datacenter Compute**  >  **Windows Server 2019** de recurso .
2. Na **Criação de uma máquina virtual,** escreva ou selecione os seguintes valores no **separador Básicos:**

   - **Assinatura**  >  **Grupo de Recursos**: Selecione **Criar novo** e, em seguida, digitar **myResourceGroupTM1**.
   - **Detalhes da**  >  instância **Nome da máquina virtual**: *Digite myIISVMEastUS*.
   - **Detalhes da**  >  instância **Região**: Selecione **East US**.
   - Conta de **Administrador**  >  **Nome de utilizador**: Introduza o nome de utilizador à sua escolha.
   - Conta de **Administrador**  >  **Senha**: Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regras portuárias**  >  de entrada **Portas de entrada pública**: Selecione **Deixe as portas selecionadas.**
   - **Regras portuárias**  >  de entrada **Selecione portas de entrada**: Selecione **RDP** e **HTTP** na caixa de puxar para baixo.

3. Selecione o separador **Gestão** ou selecione **Seguinte: Discos,** em **seguida, seguinte: Networking**, em **seguida, Seguinte: Gestão**. Em **Monitorização**, desemisse os **diagnósticos** **da** Bota .
4. Selecione **Rever + criar**.
5. Reveja as definições e, em seguida, **selecione Criar**.  
6. Siga os passos para criar um segundo VM chamado *myIISVMWestEurope,* com um nome de grupo de **recursos** do *myResourceGroupTM2*, uma **localização** da *Europa Ocidental*, e todas as outras configurações iguais às *myIISVMEastUS*.
7. A criação das VMs demora alguns minutos. Não continue com os passos restantes até que ambos os VMs sejam criados.

#### <a name="install-iis-and-customize-the-default-web-page"></a>Instalar o IIS e personalizar a página Web predefinida

Nesta secção, instale o servidor IIS nos dois VMs - *myIISVMEastUS*  &  *myIISVMWestEurope*- e, em seguida, atualize a página do site predefinido. A página do site personalizada mostra o nome do VM a que está a ligar quando visita o site a partir de um navegador web.

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, na lista de recursos selecione *myIISVMEastUS* que está localizado no grupo de recursos *myResourceGroupTM1.*
2. Na página **'Vista Geral',** selecione **Connect** e, em seguida, em **Ligar à máquina virtual**, selecione Download **RDP file**.
3. Abra o ficheiro rdp transferido. Se lhe for pedido, selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM. Poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM.
4. Selecione **OK**.
5. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber o aviso, selecione **Sim** ou **Continue,** para continuar com a ligação.
6. No ambiente de trabalho do servidor, navegue para o Gestor do Servidor **de Ferramentas Administrativas** do Windows > .
7. Lançar o Windows PowerShell no VM *myIISVMEastUS*, e utilizar os seguintes comandos para instalar o servidor IIS e atualizar o ficheiro htm predefinido.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

8. Feche a ligação RDP com *o myIISVMEastUS* VM.
9. Repita os passos 1-6 com a criação de uma ligação RDP com o VM *myIISVMWestEurope* dentro do grupo de recursos *myResourceGroupTM2* para instalar o IIS e personalizar a sua página web padrão.
10. Lançar o Windows PowerShell no *myIISVMWestEurope* VM e utilizar os seguintes comandos para instalar o servidor IIS e atualizar o ficheiro htm predefinido.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools

    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm

    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>Configurar os nomes DNS para as VMs que executam o IIS

O Gestor de Tráfego encaminha o tráfego do utilizador, baseando-se no nome DNS dos pontos finais do serviço. Nesta secção, configura os nomes DNS para os servidores IIS - *myIISVMEastUS* e *myIISVMWestEurope*.

1. Selecione **Todos os recursos** no menu à esquerda e, em seguida, na lista de recursos, selecione *myIISVMEastUS* que está localizado no grupo de recursos *myResourceGroupTM1.*
2. Na página **Descrição geral**, em **Nome DNS**, selecione **Configurar**.
3. Na página **Configuração**, na etiqueta do nome DNS, adicione um nome exclusivo e, em seguida, selecione **Guardar**.
4. Repita os passos 1-3, para o VM nomeado *myIISVMWestEurope* que está localizado no grupo de recursos *myResourceGroupTM2.*

### <a name="create-test-vms"></a>Criar VMs de teste

Nesta secção, você cria um VM (*myVMEastUS* e *myVMWestEurope*) em cada região de Azure **(Europa Oriental** e **Oeste).** Utilizará estes VMs para testar como o Gestor de Tráfego encaminha o tráfego do utilizador com base na sub-rede da consulta do utilizador.

1. No canto superior esquerdo do portal Azure, selecione **Criar um**  >  **datacenter Compute**  >  **Windows Server 2019** de recurso .
2. Na **Criação de uma máquina virtual,** escreva ou selecione os seguintes valores no **separador Básicos:**

   - **Assinatura**  >  **Grupo de Recursos**: Selecione **myResourceGroupTM1**.
   - **Detalhes da**  >  instância **Nome da máquina virtual**: *Digite myVMEastUS*.
   - **Detalhes da**  >  instância **Região**: Selecione **East US**.
   - Conta de **Administrador**  >  **Nome de utilizador**: Introduza o nome de utilizador à sua escolha.
   - Conta de **Administrador**  >  **Senha**: Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).
   - **Regras portuárias**  >  de entrada **Portas de entrada pública**: Selecione **Deixe as portas selecionadas.**
   - **Regras portuárias**  >  de entrada **Selecione portas de entrada**: Selecione **RDP** na caixa de puxar para baixo.

3. Selecione o separador **Gestão** ou selecione **Seguinte: Discos,** em **seguida, seguinte: Networking**, em **seguida, Seguinte: Gestão**. Em **Monitorização**, desemisse os **diagnósticos** **da** Bota .
4. Selecione **Rever + criar**.
5. Reveja as definições e, em seguida, **selecione Criar**.  
6. Siga os passos para criar um segundo VM chamado *myVMWestEurope,* com um nome de grupo de **recursos** do *myResourceGroupTM2*, uma **localização** da *Europa Ocidental*, e todas as outras configurações iguais às *myVMEastUS*.
7. A criação das VMs demora alguns minutos. Não prossiga com os restantes passos até que ambas as VMs tenham sido criadas.

## <a name="create-a-traffic-manager-profile"></a>Criar um perfil do Gestor de Tráfego

Crie um perfil para o Gestor de Tráfego que lhe permita devolver determinados pontos finais com base no IP de origem do pedido.

1. No lado superior esquerdo do ecrã, selecione **Criar um recurso**. Procure o *perfil de Gestor de Tráfego* e selecione **Criar**.
2. No **perfil 'Criar Gestor de Tráfego',** insira ou selecione as seguintes informações. Aceite as predefinições para as definições restantes e, em seguida, **selecione Criar**.

    ![Criar um perfil do Gestor de Tráfego](./media/tutorial-traffic-manager-subnet-routing/create-traffic-manager-profile.png)

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Nome                   | Este nome tem de ser exclusivo na zona trafficmanager.net e dá origem ao nome DNS, trafficmanager.net, que é utilizado para aceder ao perfil do seu Gestor de Tráfego.                                   |
    | Método de encaminhamento          | Selecione o método de encaminhamento da **Sub-rede**.                                       |
    | Subscrição            | Selecione a sua subscrição.                          |
    | Grupo de recursos          | Selecione **Existente** e introduza *myResourceGroupTM1*. |

## <a name="add-traffic-manager-endpoints"></a>Adicionar pontos finais do Gestor de Tráfego

Adicione os dois VMs que executam os servidores IIS - *myIISVMEastUS*  &  *myIISVMWestEurope* para encaminhar o tráfego do utilizador com base na sub-rede da consulta do utilizador.

1. Na barra de pesquisa do portal, procure o nome do perfil do Gestor de Tráfego que criou na secção anterior e selecione-o nos resultados apresentados.
2. No **perfil de Gestor de Tráfego**, na secção **Definições,** selecione **Pontos de Final** e, em seguida, selecione **Adicionar**.
3. Insira ou selecione as seguintes informações. Aceite as predefinições para as definições restantes e, em seguida, selecione **OK**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Tipo                    | Ponto final do Azure                                   |
    | Name           | myInternalWebSiteEndpoint                                        |
    | Tipo de recurso de destino           | Endereço IP Público                          |
    | Recurso de destino          | **Escolha um endereço IP público** para mostrar a lista de recursos com endereços IP públicos na mesma subscrição. Em **Recurso**, selecione o endereço IP público com o nome *myIISVMEastUS-ip*. Este é o endereço IP público da VM do servidor do IIS na região E.U.A. Leste.|
    |  Definições de encaminhamento de sub-rede    |   Adicione o endereço IP do VM de teste *myVMEastUS.* Qualquer consulta do utilizador originária deste VM será direcionada para o *myInternalWebSiteEndpoint*.    |

4. Repita os passos 2 e 3 para adicionar outro ponto final chamado *myProdWebsiteEndpoint* para o endereço IP público *myIISVMWestEurope-ip* que está associado ao servidor IIS VM chamado *myIISVMWestEurope*. Para **as definições de encaminhamento da sub-rede**, adicione o endereço IP do VM de teste - *myVMWestEurope*. Qualquer consulta de utilizador desta VM de teste será encaminhada para o ponto final - *myProdWebsiteEndpoint*.
5. Quando a adição de ambos os pontos finais está completa, são exibidas no **perfil de Gestor de Tráfego,** juntamente com o seu estado de monitorização como **Online**.

## <a name="test-traffic-manager-profile"></a>Testar o perfil do Gestor de Tráfego

Nesta secção, irá testar a forma como o Gestor de Tráfego encaminha o tráfego de utilizador de uma determinada sub-rede para um ponto final específico. Para ver o Gestor de Tráfego em ação, execute os seguintes passos:

1. Determine o nome DNS do perfil do seu Gestor de Tráfego.
2. Veja o Gestor de Tráfego em ação da forma que se segue:
    - A partir do teste VM (*myVMEastUS)* que está localizado na região **leste dos EUA,** num navegador web, navegue até ao nome DNS do seu perfil de Gestor de Tráfego.
    - A partir do teste VM (*myVMWestEurope)* que está localizado na região da **Europa Ocidental,** num navegador web, navegue até ao nome DNS do seu perfil de Gestor de Tráfego.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Determinar o nome DNS do perfil do Gestor de Tráfego

Para simplificar, utilize neste tutorial o nome DNS do perfil do Gestor de Tráfego para visitar os sites.

Pode determinar o nome DNS do perfil do Gestor de Tráfego da seguinte forma:

1. Na barra de pesquisa do portal, procure o nome do **perfil do Gestor de Tráfego** que criou na secção anterior. Nos resultados apresentados, selecione o perfil do gestor de tráfego.
2. Selecione **Descrição geral**.
3. O **Perfil do Gestor de Tráfego** mostra o nome DNS do perfil que acabou de criar. Nas implementações de produção, configure um nome de domínio personalizado associado ao nome de domínio do Gestor de Tráfego, utilizando um registo CNAME do DNS.

### <a name="view-traffic-manager-in-action"></a>Ver o Gestor de Tráfego em ação

Nesta seção, pode ver o Gestor de Tráfego em ação.

1. Selecione **todos os recursos** no menu à esquerda e, em seguida, na lista de recursos selecione *myVMEastUS* que está localizado no grupo de recursos *myResourceGroupTM1.*
2. Na página **'Vista Geral',** selecione **Connect** e, em seguida, em **Ligar à máquina virtual**, selecione Download **RDP file**.
3. Abra o ficheiro rdp transferido. Se lhe for pedido, selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM. Poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM.
4. Selecione **OK**.
5. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber o aviso, selecione **Sim** ou **Continue,** para continuar com a ligação.
6. Num browser, na VM *myVMEastUS*, introduza o nome DNS do perfil do Gestor de Tráfego para ver o seu site. Uma vez que o endereço IP *do myVMEastUS* VM está associado ao ponto final *myInternalWebsiteEndpoint,* o navegador web lança o servidor do site test - *myIISVMEastUS*.

7. Em seguida, ligue-se ao *myVMWestEurope* VM localizado na **Europa Ocidental** utilizando os passos 1-5 e navegue pelo nome de domínio do gestor de tráfego a partir deste VM. Uma vez que o endereço IP *do myVMWestEurope* VM está associado ao ponto final *myProductionWebsiteEndpoint,* o navegador web lança o servidor do site test - *myIISVMWestEurope*.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine os grupos de recursos (**ResourceGroupTM1** e **ResourceGroupTM2**). Para tal, selecione o grupo de recursos (**ResourceGroupTM1** ou **ResourceGroupTM2**) e, em seguida, selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o método de encaminhamento de sub-redes, consulte:

> [!div class="nextstepaction"]
> [Método de encaminhamento de tráfego de sub-rede](traffic-manager-routing-methods.md#subnet)
