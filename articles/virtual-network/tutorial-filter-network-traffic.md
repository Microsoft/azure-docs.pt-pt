---
title: Tráfego de rede de filtros - tutorial - Portal Azure
titlesuffix: Azure Virtual Network
description: Neste tutorial, aprende-se a filtrar o tráfego de rede para uma sub-rede, com um grupo de segurança de rede, utilizando o portal Azure.
services: virtual-network
author: KumudD
ms.service: virtual-network
ms.topic: tutorial
ms.date: 03/06/2021
ms.author: kumud
ms.openlocfilehash: cfbb499c79761e1f2014c834e65dac35fe09ef90
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057291"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Tutorial: Filtrar o tráfego da rede com um grupo de segurança de rede utilizando o portal Azure

Pode utilizar um grupo de segurança de rede para filtrar o tráfego de rede de entrada e saída a partir de uma sub-rede de rede virtual.

Os grupos de segurança de rede contêm regras de segurança que filtram o tráfego de rede por endereço IP, porta e protocolo. As regras de segurança são aplicadas a recursos implementados numa sub-rede. 

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um grupo de segurança de rede e regras de segurança.
> * Criar uma rede virtual e associar um grupo de segurança de rede a uma sub-rede
> * Implementar máquinas virtuais (VMs) numa sub-rede
> * Testar os filtros de tráfego

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Uma subscrição do Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. **Selecione Criar um recurso** no canto superior esquerdo do portal.

2. Na caixa de pesquisa, insira **a Rede Virtual.** Selecione **Rede Virtual** nos resultados da pesquisa.

3. Na página **'Rede Virtual',** selecione **Criar**.

4. Na **Criação de rede virtual,** insira ou selecione esta informação no **separador Básicos:**

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |   |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **Criar novo**.  </br> Insira **o myResourceGroup**. </br> Selecione **OK**. |
    | **Detalhes da instância** |   |
    | Name | Introduza **myVNet**. |
    | Region | Selecione **(EUA) Leste DOS** EUA . |

5. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

6. Selecione **Criar**.

## <a name="create-application-security-groups"></a>Criar grupos de segurança de aplicações

Os grupos de segurança de aplicações permitem-lhe agrupar servidores com funções semelhantes, como servidores Web.

1. **Selecione Criar um recurso** no canto superior esquerdo do portal.

2. Na caixa de pesquisa, insira **o grupo de segurança aplicação**. Selecione **grupo de segurança de aplicação** nos resultados da pesquisa.

3. Na página do **grupo de segurança aplicação,** selecione **Criar**.

4. Na **Criação de um grupo de segurança de aplicações,** insira ou selecione estas informações no **separador Básicos:**

    | Definição | Valor |
    | ------- | ----- |
    |**Detalhes do projeto** |  |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. |
    | **Detalhes da instância** |  |
    | Name | Insira **myAsgWebServers**. |
    | Region | Selecione **(EUA) Leste DOS** EUA . | 

5. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

6. Selecione **Criar**.

7. Repita novamente o passo 4, especificando os seguintes valores:

    | Definição | Valor |
    | ------- | ----- |
    |**Detalhes do projeto** |  |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. |
    | **Detalhes da instância** |  |
    | Name | Insira **myAsgMgmtServers**. |
    | Region | Selecione **(EUA) Leste DOS** EUA . |

8. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

9. Selecione **Criar**.

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Um grupo de segurança de rede protege o tráfego de rede na sua rede virtual.

1. **Selecione Criar um recurso** no canto superior esquerdo do portal.

2. Na caixa de pesquisa, **insira o grupo de segurança da Rede.** Selecione **grupo de segurança de rede** nos resultados da pesquisa.

3. Na página do **grupo de segurança 'Rede',** selecione **Criar**.

4. No **Grupo de Segurança da Rede** Criar, insira ou selecione estas informações no **separador Básicos:**

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |   |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. |
    | **Detalhes da instância** |   |
    | Name | Insira **o myNSG**. |
    | Localização | Selecione **(EUA) Leste DOS** EUA . | 

5. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

6. Selecione **Criar**.

## <a name="associate-network-security-group-to-subnet"></a>Associar o grupo de segurança de rede à sub-rede

Nesta secção, associaremos o grupo de segurança da rede à sub-rede da rede virtual que criámos anteriormente.

1. Na caixa **Procurar recursos, serviços e documentos**, na parte superior do portal, comece a escrever **myNsg**. Quando a opção **myNsg** aparecer nos resultados de pesquisa, selecione-a.

2. Na página geral do **myNSG,** selecione **Subnetas** em **Definições**.

3. Na página **Definições,** selecione **Associate**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/associate-nsg-subnet.png" alt-text="Associar a NSG à sub-rede." border="true":::

3. Na **sub-rede Associate**, selecione **a rede Virtual** e, em seguida, selecione **myVNet**. 

4. Selecione **Sub-rede,** selecione **predefinição** e, em seguida, selecione **OK**.

## <a name="create-security-rules"></a>Criar regras de segurança

1. Em **Definições** do **myNSG,** selecione **regras de segurança de entrada**.

2. Nas **regras de segurança de entrada,** selecione **+ Adicionar**:

    :::image type="content" source="./media/tutorial-filter-network-traffic/add-inbound-rule.png" alt-text="Adicione a regra de segurança de entrada." border="true":::

3. Crie uma regra de segurança que permita que as portas 80 e 443 para o grupo de segurança de aplicações **myAsgWebServers**. In **Adicionar a regra de segurança de entrada,** insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Origem | Deixe o padrão de **Qualquer**. |
    | Intervalo de portas de origem | Deixe o padrão de **(*)** |
    | Destino | Selecione **grupo de segurança de aplicação**. |
    | Grupo de segurança de aplicação de destino | Selecione **myAsgWebServers**. |
    | Serviço | Deixe o padrão de **Costume**. |
    | Intervalos de portas de destino | Insira **80.443**. |
    | Protocolo | Selecione **TCP**. |
    | Ação | Deixe o padrão de **Permitir**. |
    | Prioridade | Deixe o padrão de **100**. |
    | Name | Insira **o Allow-Web-All**. |

    :::image type="content" source="./media/tutorial-filter-network-traffic/inbound-security-rule.png" alt-text="Regra de segurança de entrada." border="true":::

3. Conclua o passo 2 novamente, com os seguintes valores:

    | Definição | Valor |
    | ------- | ----- |
    | Origem | Deixe o padrão de **Qualquer**. |
    | Intervalo de portas de origem | Deixe o padrão de **(*)** |
    | Destino | Selecione **grupo de segurança de aplicação**. |
    | Grupo de segurança de aplicação de destino | Selecione **myAsgMgmtServers**. |
    | Serviço | Deixe o padrão de **Costume**. |
    | Intervalos de portas de destino | Insira **o 3389**. |
    | Protocolo | Selecione **TCP**. |
    | Ação | Deixe o padrão de **Permitir**. |
    | Prioridade | Deixe o padrão de **110**. |
    | Name | Insira **Allow-RDP-All**. |

    > [!CAUTION]
    > Neste artigo, o RDP (porta 3389) está exposto à internet para o VM que é atribuído ao grupo de segurança de aplicações **myAsgMgmtServers.** 
    >
    > Para ambientes de produção, em vez de expor a porta 3389 à internet, recomenda-se que se conecte aos recursos da Azure que pretende gerir utilizando uma VPN, ligação de rede privada ou Azure Bastion.
    >
    > Para mais informações sobre Azure Bastion, veja [o que é Azure Bastion?](../bastion/bastion-overview.md)

Depois de concluir os passos 1 a 3, reveja as regras que criou. A sua lista deve parecer-se com a lista no seguinte exemplo:

:::image type="content" source="./media/tutorial-filter-network-traffic/security-rules.png" alt-text="Regras de segurança." border="true":::

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. **Selecione Criar um recurso** no canto superior esquerdo do portal.

2. **Selecione Compute** e, em seguida, selecione **máquina Virtual**.

3. Na **Criação de uma máquina virtual,** insira ou selecione esta informação no **separador Básicos:**

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** |  |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. |
    | **Detalhes da instância** |   |
    | Nome da máquina virtual | Insira **myVMWeb**. |
    | Region | Selecione **(EUA) Leste DOS** EUA . |
    | Opções de disponibilidade | Não deixe o incumprimento de não ser necessário despedimento. |
    | Imagem | Selecione **o Centro de Dados 2019 do Windows Server - Gen1**. |
    | Instância do Azure Spot | Deixe o padrão de não verificado. |
    | Tamanho | Selecione **Standard_D2s_V3**. |
    | **Conta de administrador** |   |
    | Nome de utilizador | Introduza um nome de utilizador. |
    | Palavra-passe | Introduza uma senha. |
    | Confirmar palavra-passe | Reentre na senha. |
    | **Regras portuárias de entrada** |   |
    | Portas de entrada públicas | Selecione **Nenhuma**. |

4. Selecione o separador **Rede**.

5. No **separador Networking,** introduza ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Interface de rede** |   |
    | Rede virtual | Selecione **myVNet**. |
    | Sub-rede | Selecione **predefinição (10.0.0.0/24)**. |
    | IP público | Deixe o padrão de um novo IP público. |
    | Grupo de segurança de rede NIC | Selecione **Nenhuma**. | 

6. Selecione o **separador 'Rever +' ou** selecione o botão **'Avaliação azul+'** na parte inferior da página.

7. Selecione **Criar**.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Complete os passos 1-7 novamente, mas no passo 3, nomeie o VM **myVMMgmt**. A implementação da VM demora alguns minutos. 

Não continue para o próximo passo até que o VM seja implantado.

## <a name="associate-network-interfaces-to-an-asg"></a>Associar interfaces de rede a um ASG

Quando o portal criou as VMs, criou uma interface de rede para cada VM e associou a interface de rede à VM. 

Adicione a interface de rede para cada VM a um dos grupos de segurança de aplicações que criou anteriormente:

1. Na **caixa de recursos de pesquisa, serviços e docs** no topo do portal, comece a digitar **o myVMWeb**. Quando a máquina virtual **myVMWeb** aparecer nos resultados da pesquisa, selecione-a.

2. Em **Definições**, selecione **Networking**.  

3. Selecione o separador **grupos de segurança aplicação** e, em seguida, selecione **Configurar os grupos de segurança da aplicação**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/configure-app-sec-groups.png" alt-text="Configurar grupos de segurança de aplicações." border="true":::

4. Em **Configurar os grupos de segurança da aplicação,** selecione **myAsgWebServers**. Selecione **Guardar**.

    :::image type="content" source="./media/tutorial-filter-network-traffic/select-asgs.png" alt-text="Selecione grupos de segurança de aplicações." border="true":::

5. Complete os passos 1 e 2 novamente, procurando a máquina virtual **myVMMgmt** e selecionando o  **myAsgMgmtServers** ASG.

## <a name="test-traffic-filters"></a>Testar os filtros de tráfego

1. Ligue-se ao **myVMMgmt** VM. Introduza **o myVMMgmt** na caixa de pesquisa no topo do portal. Quando **o myVMMgmt** aparecer nos resultados da pesquisa, selecione-o. Selecione o botão **Ligar**.

2. Selecione **Transferir ficheiro RDP**.

3. Abra o ficheiro rdp transferido e selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM.

4. Selecione **OK**.

5. Pode receber um aviso de certificado durante o processo de ligação. Se receber o aviso, selecione **Sim** ou **Continue,** para continuar com a ligação.

    A ligação tem sucesso, porque a porta 3389 é permitida a entrada da internet para o grupo de segurança de aplicações **myAsgMgmtServers.** 
    
    A interface de rede para **o myVMMgmt** está associada ao grupo de segurança da aplicação **myAsgMgmtServers** e permite a ligação.

6. Abra uma sessão PowerShell no **myVMMgmt**. Ligue ao **myVMWeb** utilizando o seguinte exemplo: 

    ```powershell
    mstsc /v:myVmWeb
    ```

    A ligação RDP do **myVMMgmt** ao **myVMWeb** tem sucesso porque as máquinas virtuais na mesma rede podem comunicar com cada uma em qualquer porta por defeito.
    
    Não é possível criar uma ligação RDP à máquina virtual **myVMWeb** a partir da internet. A regra de segurança para os **myAsgWebServers** impede ligações à porta 3389 de entrada da internet. O tráfego de entrada da Internet é negado a todos os recursos por defeito.

7. Para instalar o Microsoft IIS na máquina virtual **myVMWeb,** insira o seguinte comando a partir de uma sessão PowerShell na máquina virtual **myVMWeb:**

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Depois de concluída a instalação do IIS, desligue-se da máquina virtual **myVMWeb,** o que o deixa na ligação remota de ambiente de trabalho da máquina virtual **myVMMgmt.**

9. Desligue do **myVMMgmt** VM.

10. Na caixa **de recursos, serviços e docs de pesquisa** no topo do portal Azure, comece a digitar o **myVMWeb** a partir do seu computador. Quando **o myVMWeb** aparecer nos resultados da pesquisa, selecione-o. Anote o **Endereço IP público** para a sua VM. O endereço mostrado no exemplo seguinte é 23.96.39.113, mas o seu endereço é diferente:

    :::image type="content" source="./media/tutorial-filter-network-traffic/public-ip-address.png" alt-text="Endereço IP público." border="true":::
    
11. Para confirmar que pode aceder ao servidor web **myVMWeb** a partir da internet, abra um navegador de Internet no seu computador e navegue para `http://<public-ip-address-from-previous-step>` . 

Você vê o ecrã de boas-vindas IIS, porque a porta 80 é permitida a entrada da internet para o grupo de segurança da aplicação **myAsgWebServers.** 

A interface de rede anexada ao **myVMWeb** está associada ao grupo de segurança da aplicação **myAsgWebServers** e permite a ligação. 

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo:

1. Introduza **myResourceGroup** na caixa **Pesquisar**, na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza **myResourceGroup** em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial:

* Criou um grupo de segurança de rede e associou-o a uma sub-rede de rede virtual. 
* Criei grupos de segurança de aplicações para web e gestão.
* Criei duas máquinas virtuais.
* Testei a filtragem da rede de grupos de segurança de aplicações.

Para saber mais sobre os grupos de segurança de rede, veja [Descrição geral dos grupos de segurança de rede](./network-security-groups-overview.md) e [Manage a network security group](manage-network-security-group.md) (Gerir um grupo de segurança de rede).

O Azure encaminha o tráfego entre sub-redes por predefinição. Em alternativa, pode optar por encaminhar o tráfego entre sub-redes através de uma VM que funcione, por exemplo, como uma firewall. 

Para saber como criar uma tabela de rotas, avance para o tutorial seguinte.
> [!div class="nextstepaction"]
> [Criar uma tabela de rotas](./tutorial-create-route-table-portal.md)