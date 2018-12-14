---
title: Filtrar tráfego de rede - tutorial - Portal do Azure | Microsoft Docs
description: Neste tutorial, vai aprender a filtrar o tráfego de rede para uma sub-rede, com um grupo de segurança de rede, através do Portal do Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/13/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 68fdb158a7d4c723bffb54f33203120afb72a8ef
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385402"
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-the-azure-portal"></a>Tutorial: Filtrar o tráfego de rede com um grupo de segurança de rede através do Portal do Azure

Pode filtrar o tráfego de rede de entrada e de saída de uma sub-rede de rede virtual com um grupo de segurança de rede. Os grupos de segurança de rede contêm regras de segurança que filtram o tráfego de rede por endereço IP, porta e protocolo. As regras de segurança são aplicadas a recursos implementados numa sub-rede. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Crie um grupo de segurança de rede e regras de segurança.
> * Criar uma rede virtual e associar um grupo de segurança de rede a uma sub-rede
> * Implementar máquinas virtuais (VMs) numa sub-rede
> * Testar os filtros de tráfego

Se preferir, pode concluir este tutorial com a [CLI do Azure](tutorial-filter-network-traffic-cli.md) ou o [PowerShell](tutorial-filter-network-traffic-powershell.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. Selecione **Redes** e, em seguida, selecione **Rede virtual**.
3. Introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **Criar**:

    | Definição                 | Valor                                              |
    | ---                     | ---                                                |
    | Nome                    | myVirtualNetwork                                   |
    | Espaço de endereços           | 10.0.0.0/16                                        |
    | Subscrição            | Selecione a sua subscrição.                          |
    | Grupo de recursos          | Selecione **Criar novo** e introduza *myResourceGroup*. |
    | Localização                | Selecione **E.U.A. Leste**.                                |
    | Nome da Sub-rede            | mySubnet                                           |
    | Sub-rede - Intervalo de endereços  | 10.0.0.0/24                                        |

## <a name="create-application-security-groups"></a>Criar grupos de segurança de aplicações

Os grupos de segurança de aplicações permitem-lhe agrupar servidores com funções semelhantes, como servidores Web.

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. Na caixa **Pesquisar no Marketplace**, introduza *Grupo de segurança de aplicações*. Selecione **Grupo de segurança de aplicações** quando aparecer nos resultados da pesquisa, selecione novamente **Grupo de segurança de aplicações** em **Tudo** e, em seguida, selecione **Criar**.
3. Introduza, ou selecione as seguintes informações e, em seguida, selecione **Criar**:

    | Definição        | Valor                                                         |
    | ---            | ---                                                           |
    | Nome           | myAsgWebServers                                               |
    | Subscrição   | Selecione a sua subscrição.                                     |
    | Grupo de recursos | Selecione **Utilizar existente** e, em seguida, selecione **myResourceGroup**. |
    | Localização       | EUA Leste                                                       |

4. Conclua o passo 3 novamente, com os seguintes valores:

    | Definição        | Valor                                                         |
    | ---            | ---                                                           |
    | Nome           | myAsgMgmtServers                                              |
    | Subscrição   | Selecione a sua subscrição.                                     |
    | Grupo de recursos | Selecione **Utilizar existente** e, em seguida, selecione **myResourceGroup**. |
    | Localização       | EUA Leste                                                       |

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

1. Selecione **+ Criar um recurso**, no canto superior esquerdo do Portal do Azure.
2. Selecione **Rede** e selecione **Grupo de segurança de rede**.
3. Introduza, ou selecione as seguintes informações e, em seguida, selecione **Criar**:

    |Definição|Valor|
    |---|---|
    |Nome|myNsg|
    |Subscrição| Selecione a sua subscrição.|
    |Grupo de recursos | Selecione **Utilizar existente** e, em seguida, selecione *myResourceGroup*.|
    |Localização|EUA Leste|

## <a name="associate-network-security-group-to-subnet"></a>Associar o grupo de segurança de rede à sub-rede

1. Na caixa *Procurar recursos, serviços e documentos*, na parte superior do portal, comece a escrever *myNsg*. Quando a opção **myNsg** aparecer nos resultados de pesquisa, selecione-a.
2. Em **DEFINIÇÕES**, selecione **Sub-redes** e selecione **+ Associar**, conforme mostrado na imagem seguinte:

    ![Associar NSG à sub-rede](./media/tutorial-filter-network-traffic/associate-nsg-subnet.png)

3. Em **Associar sub-rede**, selecione **Rede Virtual** e selecione **myVirtualNetwork**. Selecione **Sub-rede**, selecione **mySubnet** e, em seguida, selecione **OK**.

## <a name="create-security-rules"></a>Criar regras de segurança

1. Em **DEFINIÇÕES** selecione **Regras de segurança de entrada** e, em seguida, selecione **+ Adicionar**, conforme mostrado na imagem seguinte:

    ![Adicionar uma regra de segurança de entrada](./media/tutorial-filter-network-traffic/add-inbound-rule.png)

2. Crie uma regra de segurança que permita que as portas 80 e 443 para o grupo de segurança de aplicações **myAsgWebServers**. Em **Adicionar regra de segurança de entrada**, introduza ou selecione os seguintes valores, aceite as restantes predefinições e, em seguida, selecione **Adicionar**:

    | Definição                 | Valor                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Destino             | Selecione **Grupo de segurança de aplicações** e, em seguida, selecione **myAsgWebServers** para **Grupo de segurança de aplicações**.  |
    | Intervalos de portas de destino | Introduza 80.443                                                                                                    |
    | Protocolo                | Selecione TCP                                                                                                      |
    | Nome                    | Allow-Web-All                                                                                                   |

3. Conclua o passo 2 novamente, com os seguintes valores:

    | Definição                 | Valor                                                                                                           |
    | ---------               | ---------                                                                                                       |
    | Destino             | Selecione **Grupo de segurança de aplicações** e, em seguida, selecione **myAsgMgmtServers** para **Grupo de segurança de aplicações**. |
    | Intervalos de portas de destino | Introduza 3389                                                                                                      |
    | Protocolo                | Selecione TCP                                                                                                      |
    | Prioridade                | Introduza 110                                                                                                       |
    | Nome                    | Allow-RDP-All                                                                                                   |

    Neste tutorial, o RDP (porta 3389) está exposto à Internet para a VM atribuída ao grupo de segurança de aplicações *myAsgMgmtServers*. Em ambientes de produção, em vez de expor a porta 3389 à Internet, recomenda-se que ligue aos recursos do Azure que quer gerir com uma VPN ou da ligação de rede privada.

Depois de concluir os passos 1 a 3, reveja as regras que criou. A lista deve ser semelhante à lista na imagem seguinte:

![Regras de segurança](./media/tutorial-filter-network-traffic/security-rules.png)

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual.

### <a name="create-the-first-vm"></a>Criar a primeira VM

1. Selecione **+ Criar um recurso**, disponível no canto superior esquerdo do Portal do Azure.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**.
3. Introduza ou selecione as seguintes informações, aceite as predefinições para as restantes definições e, em seguida, selecione **OK**:

    |Definição|Valor|
    |---|---|
    |Nome|myVmWeb|
    |Nome de utilizador| Introduza um nome de utilizador à sua escolha.|
    |Palavra-passe| Introduza uma palavra-passe à sua escolha. A palavra-passe tem de ter, pelo menos, 12 carateres e cumprir os [requisitos de complexidade definidos](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscrição| Selecione a sua subscrição.|
    |Grupo de recursos| Selecione **Utilizar existente** e selecione **myResourceGroup**.|
    |Localização| Selecione **E.U.A. Leste**|

4. Escolha um tamanho para a VM e selecione **Selecionar**.
5. Em **Definições**, selecione os seguintes valores, aceite as restantes predefinições e, em seguida, selecione **OK**:

    |Definição|Valor|
    |---|---|
    |Rede virtual |Selecione **myVirtualNetwork**|
    |Grupo de Segurança de Rede | Selecione **Avançadas**.|
    |Grupo de segurança de rede (firewall)| Selecione **(novo) myVmWeb-nsg** e, em **Escolher grupo de segurança de rede**, selecione **Nenhum**. |

6. Em **Criar** no **Resumo**, selecione **Criar** para iniciar a implementação da VM.

### <a name="create-the-second-vm"></a>Criar a segunda VM

Conclua os passos 1 a 6 novamente mas, no passo 3, dê à VM o nome *myVmMgmt*. A implementação da VM demora alguns minutos. Não avance para o passo seguinte até que a VM seja implementada.

## <a name="associate-network-interfaces-to-an-asg"></a>Associar interfaces de rede a um ASG

Quando o portal criou as VMs, criou uma interface de rede para cada VM e associou a interface de rede à VM. Adicione a interface de rede para cada VM a um dos grupos de segurança de aplicações que criou anteriormente:

1. Na caixa *Procurar recursos, serviços e documentos*, na parte superior do portal, comece a escrever *myVmWeb*. Quando a VM **myVmWeb** aparecer nos resultados da pesquisa, selecione-a.
2. Em **DEFINIÇÕES**, selecione **Redes**.  Selecione **Configurar os grupos de segurança de aplicações**, selecione **myAsgWebServers** para **Grupos de segurança de aplicações** e, em seguida, selecione **Guardar**, conforme mostrado na imagem seguinte:

    ![Associar ao ASG](./media/tutorial-filter-network-traffic/associate-to-asg.png)

3. Conclua os passos 1 e 2 novamente, procure a VM **myVmMgmt** e selecione o ASG **myAsgMgmtServers**.

## <a name="test-traffic-filters"></a>Testar os filtros de tráfego

1. Ligue à VM *myVmMgmt*. Introduza *myVmMgmt* na caixa de pesquisa, na parte superior do portal. Quando a opção **myVmMgmt** aparecer nos resultados de pesquisa, selecione-a. Selecione o botão **Ligar**.
2. Selecione **Transferir ficheiro RDP**.
3. Abra o ficheiro rdp transferido e selecione **Ligar**. Introduza o nome de utilizador e a palavra-passe que especificou ao criar a VM. Poderá ter de selecionar **Mais opções** e **Utilizar uma conta diferente** para especificar as credenciais que introduziu quando criou a VM.
4. Selecione **OK**.
5. Poderá receber um aviso de certificado durante o processo de início de sessão. Se receber o aviso, selecione **Sim** ou **Continuar** para prosseguir com a ligação.

    A ligação é bem-sucedida, porque a porta 3389 pode receber tráfego de entrada da Internet para o grupo de segurança de aplicações *myAsgMgmtServers* no qual a interface de rede ligada à VM *myVmMgmt* se encontra.

6. Ligue à VM *myVmWeb* a partir da VM *myVmMgmt* ao introduzir o seguinte comando numa sessão do PowerShell:

    ``` 
    mstsc /v:myVmWeb
    ```

    É possível ligar à VM myVmWeb a partir da VM myVmMgmt, uma vez que as VMs na mesma rede virtual podem comunicar entre si através de qualquer porta, por predefinição. No entanto, não pode criar uma ligação de ambiente de trabalho remoto para a VM *myVmWeb* a partir da Internet, porque a regra de segurança para *myAsgWebServers* não permite a porta 3389 de entrada da Internet e o tráfego de entrada da Internet é negado para todos os recursos, por predefinição.

7. Para instalar o Microsoft IIS na VM *myVmWeb*, introduza o seguinte comando a partir de uma sessão do PowerShell na VM *myVmWeb*:

    ```powershell
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    ```

8. Depois de concluída a instalação do IIS, desligue da VM *myVmWeb*, permanecendo na ligação de ambiente de trabalho remoto da VM *myVmMgmt*.
9. Desligue da VM *myVmMgmt*.
10. Na caixa *Procurar recursos, serviços e documentos*, na parte superior do portal do Azure, comece a escrever *myVmWeb* a partir do seu computador. Quando a opção **myVmWeb** aparecer nos resultados de pesquisa, selecione-a. Anote o **Endereço IP público** para a sua VM. O endereço mostrado na imagem seguinte é 137.135.84.74, mas o seu endereço é diferente:

    ![Endereço IP público](./media/tutorial-filter-network-traffic/public-ip-address.png)
  
11. Para confirmar se consegue aceder ao servidor Web *myVmWeb* a partir da Internet, abra um browser no computador e navegue para `http://<public-ip-address-from-previous-step>`. Consegue ver o ecrã de boas-vindas do IIS porque a porta 80 pode receber tráfego de entrada da Internet para o grupo de segurança de aplicações *myAsgWebServers* no qual a interface de rede ligada à VM *myVmWeb* se encontra.

## <a name="clean-up-resources"></a>Limpar recursos

Quando já não for necessário, elimine o grupo de recursos e todos os recursos contidos no mesmo:

1. Introduza *myResourceGroup* na caixa **Pesquisar** na parte superior do portal. Quando vir o **myResourceGroup** nos resultados da pesquisa, selecione-o.
2. Selecione **Eliminar grupo de recursos**.
3. Introduza *myResourceGroup* em **ESCREVER O NOME DO GRUPO DE RECURSOS:** e selecione **Eliminar**.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, criou um grupo de segurança de rede e associou-o a uma sub-rede de rede virtual. Para saber mais sobre os grupos de segurança de rede, veja [Descrição geral dos grupos de segurança de rede](security-overview.md) e [Manage a network security group](manage-network-security-group.md) (Gerir um grupo de segurança de rede).

O Azure encaminha o tráfego entre sub-redes por predefinição. Em alternativa, pode optar por encaminhar o tráfego entre sub-redes através de uma VM que funcione, por exemplo, como uma firewall. Para saber como criar uma tabela de rotas, avance para o tutorial seguinte.

> [!div class="nextstepaction"]
> [Criar uma tabela de rotas](./tutorial-create-route-table-portal.md)