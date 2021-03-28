---
title: Utilizar o Azure Firewall para inspecionar o tráfego destinado a um ponto final privado
titleSuffix: Azure Private Link
description: Saiba como pode inspecionar o tráfego destinado a um ponto final privado utilizando a Azure Firewall.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu
ms.openlocfilehash: c3218d8781377e76f05d10a8da2c954ac0b685a7
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641999"
---
# <a name="use-azure-firewall-to-inspect-traffic-destined-to-a-private-endpoint"></a>Utilizar o Azure Firewall para inspecionar o tráfego destinado a um ponto final privado

Azure Private Endpoint é o bloco de construção fundamental para a Azure Private Link. Os pontos finais privados permitem que os recursos Azure implantados numa rede virtual comuniquem privadamente com recursos de ligação privada.

Os pontos finais privados permitem o acesso dos recursos ao serviço de ligação privada implantado numa rede virtual. O acesso ao ponto final privado através de ligações de rede de observação de rede virtuais e de rede no local alarga a conectividade.

Poderá ser necessário inspecionar ou bloquear o tráfego dos clientes para os serviços expostos através de pontos finais privados. Complete esta inspeção utilizando [o Azure Firewall](../firewall/overview.md) ou um aparelho virtual de rede de terceiros.

Aplicam-se as seguintes limitações:

* Os grupos de segurança da rede (NSG) são ignorados pelo tráfego proveniente de pontos finais privados
* As rotas definidas pelo utilizador (UDR) são contornadas pelo tráfego proveniente de pontos finais privados
* Uma única tabela de rotas pode ser anexada a uma sub-rede
* Uma tabela de rotas suporta até 400 rotas

A Azure Firewall filtra o tráfego utilizando:

* [FQDN nas regras de rede](../firewall/fqdn-filtering-network-rules.md) para protocolos TCP e UDP
* [FQDN nas regras de aplicação](../firewall/features.md#application-fqdn-filtering-rules) para HTTP, HTTPS e MSSQL. 

> [!IMPORTANT] 
> Recomenda-se a utilização de regras de aplicação sobre as regras de rede na inspeção do tráfego destinado a pontos finais privados, a fim de manter a simetria do fluxo. Se forem utilizadas regras de rede ou se for utilizada uma NVA em vez de Azure Firewall, o SNAT deve ser configurado para o tráfego destinado a pontos finais privados.

> [!NOTE]
> A filtragem SQL FQDN é suportada apenas em [modo proxy](../azure-sql/database/connectivity-architecture.md#connection-policy) (porta 1433). O modo **proxy** pode resultar em mais latência em comparação com *o redirecionamento*. Se pretender continuar a utilizar o modo de redirecionamento, que é o padrão para os clientes que se ligam dentro do Azure, pode filtrar o acesso usando o FQDN nas regras da rede de firewall.

## <a name="scenario-1-hub-and-spoke-architecture---dedicated-virtual-network-for-private-endpoints"></a>Cenário 1: Hub e arquitetura falada - Rede virtual dedicada para pontos finais privados

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hub-and-spoke.png" alt-text="Rede Virtual Dedicada para Pontos Finais Privados" border="true":::

Este cenário é a arquitetura mais expansível para ligar privadamente a vários serviços Azure usando pontos finais privados. É criada uma rota que aponta para o espaço de endereço de rede onde os pontos finais privados são implantados. Esta configuração reduz a sobrecarga administrativa e impede que se desacordam no limite de 400 rotas.

As ligações de uma rede virtual do cliente ao Azure Firewall numa rede virtual de hub incorrerão em encargos se as redes virtuais forem espreitadas. As ligações do Azure Firewall numa rede virtual de hub a pontos finais privados numa rede virtual esprevada não são carregadas.

Para obter mais informações sobre os encargos relacionados com ligações com redes virtuais espreitadas, consulte a secção faq da página [de preços.](https://azure.microsoft.com/pricing/details/private-link/)

## <a name="scenario-2-hub-and-spoke-architecture---shared-virtual-network-for-private-endpoints-and-virtual-machines"></a>Cenário 2: Hub e arquitetura falada - Rede virtual partilhada para pontos finais privados e máquinas virtuais

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/shared-spoke.png" alt-text="Pontos finais privados e máquinas virtuais na mesma Rede Virtual" border="true":::

Este cenário é implementado quando:

* Não é possível ter uma rede virtual dedicada para os pontos finais privados

* Quando apenas alguns serviços são expostos na rede virtual usando pontos finais privados

As máquinas virtuais terão rotas de sistema /32 apontando para cada ponto final privado. Uma rota por ponto final privado está configurada para encaminhar o tráfego através do Azure Firewall. 

A sobrecarga administrativa da manutenção do quadro de rotas aumenta à medida que os serviços estão expostos na rede virtual. A possibilidade de atingir o limite de rota também aumenta.

Dependendo da sua arquitetura geral, é possível encontrar o limite de 400 rotas. Recomenda-se a utilização do cenário 1 sempre que possível.

As ligações de uma rede virtual do cliente ao Azure Firewall numa rede virtual de hub incorrerão em encargos se as redes virtuais forem espreitadas. As ligações do Azure Firewall numa rede virtual de hub a pontos finais privados numa rede virtual esprevada não são carregadas.

Para obter mais informações sobre os encargos relacionados com ligações com redes virtuais espreitadas, consulte a secção faq da página [de preços.](https://azure.microsoft.com/pricing/details/private-link/)

## <a name="scenario-3-single-virtual-network"></a>Cenário 3: Rede virtual única

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/single-vnet.png" alt-text="Rede virtual única" border="true":::

Use este padrão quando uma migração para um centro e arquitetura falada não é possível. Aplicam-se as mesmas considerações que no cenário 2. Neste cenário, os encargos de observação da rede virtual não se aplicam.

## <a name="scenario-4-on-premises-traffic-to-private-endpoints"></a>Cenário 4: Tráfego no local para pontos finais privados

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/on-premises.png" alt-text="Tráfego no local para pontos finais privados" border="true":::

Esta arquitetura pode ser implementada se tiver conectividade configurada com a sua rede no local usando: 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [Site para VPN site](../vpn-gateway/tutorial-site-to-site-portal.md) 

Se os seus requisitos de segurança exigirem que o tráfego do cliente para serviços expostos através de pontos finais privados seja encaminhado através de um aparelho de segurança, implemente este cenário.

Aplicam-se as mesmas considerações que no cenário 2 suplímos. Neste cenário, não há acusações de observação de redes virtuais. Para obter mais informações sobre como configurar os seus servidores DNS para permitir que as cargas de trabalho no local acedam a pontos finais privados, consulte [as cargas de trabalho no Local utilizando um reencaminhador DNS](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

## <a name="prerequisites"></a>Pré-requisitos

* Uma subscrição do Azure.
* Uma área de trabalho do Log Analytics.  

Consulte, [Crie um espaço de trabalho log Analytics no portal Azure](../azure-monitor/logs/quick-create-workspace.md) para criar um espaço de trabalho se não tiver um na sua subscrição.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-a-vm"></a>Criar uma VM

Nesta secção, irá criar uma rede virtual e uma sub-rede para hospedar o VM utilizado para aceder ao seu recurso de ligação privada. Uma base de dados Azure SQL é utilizada como serviço de exemplo.

## <a name="virtual-networks-and-parameters"></a>Redes virtuais e parâmetros

Criar três redes virtuais e as respetivas sub-redes para:

* Conter a Firewall Azure utilizada para restringir a comunicação entre o VM e o ponto final privado.
* Hospedar o VM que é usado para aceder ao seu recurso de ligação privada.
* Hospedar o ponto final privado.

Substitua os seguintes parâmetros nos passos com as informações abaixo:

### <a name="azure-firewall-network"></a>Rede Azure Firewall

| Parâmetro                   | Valor                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | E.U.A. Centro-Sul      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### <a name="virtual-machine-network"></a>Rede de máquinas virtuais

| Parâmetro                   | Valor                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | E.U.A. Centro-Sul      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | VMSubnet      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### <a name="private-endpoint-network"></a>Rede privada de pontos finais

| Parâmetro                   | Valor                 |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | E.U.A. Centro-Sul      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

10. Repita os passos 1 a 9 para criar as redes virtuais para hospedar a máquina virtual e os recursos de ponto final privado.

### <a name="create-virtual-machine"></a>Criar a máquina virtual

1. No lado superior esquerdo do ecrã no portal Azure, selecione **Criar uma** máquina Virtual  >  **Compute de** recurso  >  .

2. Na **Criar uma máquina virtual - Básicos, insira** ou selecione esta informação:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou este grupo de recursos na secção anterior.  |
    | **Detalhes da instância** |  |
    | Nome da máquina virtual | Insira **o myVM**. |
    | Region | Selecione **(EUA) South Central US**. |
    | Opções de disponibilidade | Deixar o incumprimento **Não é necessário um despedimento de infraestrutura**. |
    | Imagem | Selecione **Ubuntu Server 18.04 LTS - Gen1**. |
    | Tamanho | Selecione **Standard_B2s**. |
    | **Conta de administrador** |  |
    | Tipo de autenticação | Selecione **palavra-passe**. |
    | Nome de utilizador | Insira um nome de utilizador à sua escolha. |
    | Palavra-passe | Introduza uma palavra-passe à sua escolha. A palavra-passe deve ter pelo menos 12 caracteres de comprimento e satisfazer os [requisitos de complexidade definidos](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirmar Palavra-passe | Reentre na senha. |
    | **Regras portuárias de entrada** |  |
    | Portas de entrada públicas | Selecione **Nenhuma**. |
    |||

3. Selecione **Seguinte: Discos**.

4. Em **Criar uma máquina virtual – Discos**, mantenha as predefinições e selecione **Seguinte: Redes**.

5. Na **Criação de uma máquina virtual - Networking,** selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Selecione **myVMVNet**.  |
    | Sub-rede | Selecione **VMSubnet (10.1.0.0/24)**.|
    | IP público | Deixe o **padrão (novo) myVm-ip**. |
    | Portas de entrada públicas | Selecione **Deixe as portas selecionadas.** |
    | Selecione as portas de entrada | Selecione **SSH**.|
    ||

6. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração.

7. Quando vir a mensagem **A validação passou**, selecione **Criar**.

## <a name="deploy-the-firewall"></a>Implementar a Firewall

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.

2. Digite **firewall** na caixa de pesquisa e prima **Enter**.

3. Selecione **Firewall** e, em seguida, selecione **Criar**.

4. Na página **Criar uma firewall**, utilize a seguinte tabela para configurar a firewall:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**.  |
    | **Detalhes da instância** |  |
    | Name | Insira **myAzureFirewall**. |
    | Region | Selecione **South Central US**. |
    | Zona de disponibilidade | Deixe o padrão **Nenhum**. |
    | Escolher uma rede virtual    |    Selecione **Utilização existente**.    |
    | Rede virtual    |    Selecione **myAzFwVNet**.    |
    | Endereço IP público    |    **Selecione Adicionar novo** e em nome insira **myFirewall-ip**.    |
    | Túnel forçado    | Deixe o **desativado predefinido**.    |
    |||
5. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração.

6. Quando vir a mensagem **A validação passou**, selecione **Criar**.

## <a name="enable-firewall-logs"></a>Ativar registos de firewall

Nesta secção, ativar os registos na firewall.

1. No portal Azure, selecione **Todos os recursos** no menu à esquerda.

2. Selecione a firewall **myAzureFirewall** na lista de recursos.

3. Em **Monitorização** nas definições de firewall, selecione **definições de diagnóstico**

4. **Selecione + Adicione a definição de diagnóstico** nas definições de Diagnóstico.

5. Na **definição de Diagnóstico, insira** ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome de definição de diagnóstico | Insira **o myDiagSetting**. |
    | Detalhes da categoria | |
    | log | Selecione **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule**. |
    | Detalhes do destino | Selecione **Enviar para Registar Análises**. |
    | Subscrição | Selecione a sua subscrição. |
    | Área de trabalho do Log Analytics | Selecione o seu espaço de trabalho Log Analytics. |

6. Selecione **Guardar**.

## <a name="create-azure-sql-database"></a>Criar base de dados Azure SQL

Nesta secção, cria-se uma Base de Dados SQL privada.

1. No lado superior esquerdo do ecrã no portal Azure, **selecione Criar uma** base de dados de recursos  >    >  **SQL Databases**.

2. Na **Create SQL Database - Básicos, insira** ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou este grupo de recursos na secção anterior.|
    | **Detalhes da base de dados** |  |
    | Nome da base de dados  | Insira **a minha base de dados.**  |
    | Servidor | Selecione **Criar novo** e introduza as informações abaixo.    |
    | Nome do servidor | **Insira o mydbserver**. Se este nome for tomado, insira um nome único.   |
    | Início de sessão de administrador do servidor | Insira o nome da sua escolha. |
    | Palavra-passe    |    Introduza uma palavra-passe à sua escolha.    |
    | Confirmar Palavra-passe | Reintroduza a palavra-passe    |
    | Localização    | Selecione **(EUA) South Central US**.    |
    | Quer usar a piscina elástica SQL    | Deixe o **nº** padrão . |
    | Computação e armazenamento | Deixe o padrão **De propósito geral Gen5, 2 vCores, 32 GB de armazenamento**. |
    |||

3. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração.

4. Quando vir a mensagem **A validação passou**, selecione **Criar**.

## <a name="create-private-endpoint"></a>Criar ponto final privado

Nesta secção, cria-se um ponto final privado para a base de dados Azure SQL na secção anterior.

1. No portal Azure, selecione **Todos os recursos** no menu à esquerda.

2. Selecione o **mydbserver** do servidor Azure SQL na lista de serviços.  Se usou um nome de servidor diferente, escolha esse nome.

3. Nas definições do servidor, selecione **as ligações de ponto final privado sob** **segurança**.

4. Selecione **+ ponto final privado.**

5. Na **Criação de um ponto final privado,** insira ou selecione estas informações no **separador Básicos:**

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. |
    | **Detalhes da instância** | |
    | Name | Insira **o SQLPrivateEndpoint**. |
    | Region | Selecione **(EUA) South Central US.** |

6. Selecione o **separador Recursos** ou selecione **Seguinte: Recurso** na parte inferior da página.

7. No **separador Recursos,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Método de ligação | Selecione **Ligar a um recurso Azure no meu diretório**. |
    | Subscrição | Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft.Sql/servidores**. |
    | Recurso | Selecione **mydbserver** ou o nome do servidor que criou no passo anterior.
    | Recurso secundário de destino | Selecione **sqlServer**. |

8. Selecione o **separador Configuração** ou selecione **Seguinte: Configuração** na parte inferior da página.

9. No **separador Configuração,** introduza ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | **Rede** | |
    | Rede virtual | Selecione **myPEVnet**. |
    | Sub-rede | Selecione **PrivateEndpointSubnet**. |
    | **Integração privada de DNS** | |
    | Integrar com zona DNS privada | Selecione **Yes** (Sim). |
    | Subscrição | Selecione a sua subscrição. |
    | Zonas DNS Privadas | Deixe o **privatelink.database.windows.net** padrão . |

10. Selecione o separador **'Rever +' criar** ou selecionar **'Rever +' criar** na parte inferior da página.

11. Selecione **Criar**.

12. Após a criação do ponto final, selecione **Firewalls e redes virtuais** em **Segurança**.

13. Em **Firewalls e redes virtuais,** selecione **Sim** ao lado **de Permitir que os serviços e recursos do Azure acedam a este servidor**.

14. Selecione **Guardar**.

## <a name="connect-the-virtual-networks-using-virtual-network-peering"></a>Conecte as redes virtuais usando o espreitamento de rede virtual

Nesta secção, vamos ligar as redes virtuais **myVMVNet** e **myPEVNet** ao **myAzFwVNet** usando o peering. Não haverá conectividade direta entre **o myVMVNet** e **o myPEVNet.**

1. Na barra de pesquisa do portal, insira **o myAzFwVNet**.

2. **Selecione Peerings** no menu **Definições** e selecione **+ Adicionar**.

3. In **Add Peering** insi ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome do espreitamento do myAzFwVNet para rede virtual remota | Insira **o meuAzFwVNet-to-myVMVNet**. |
    | **Detalhes dos pares** |  |
    | Modelo de implementação de rede virtual  | Deixe o **Gestor de Recursos** predefinido .  |
    | Eu sei o meu iD de recursos | deixe esta opção desselecionada.    |
    | Subscrição | Selecione a sua subscrição.    |
    | Rede virtual | Selecione **myVMVNet**. |
    | Nome do espreitamento da rede virtual remota para o myAzFwVNet    |    Insira **myVMVNet-to-myAzFwVNet**.    |
    | **Configuração** | |
    | **Configurar definições de acesso à rede virtual** | |
    | Permitir o acesso à rede virtual do myAzFwVNet para rede virtual remota | Deixar o padrão **Ativado**.    |
    | Permitir o acesso à rede virtual a partir da rede virtual remota para o myAzFwVNet    | Deixar o padrão **Ativado**.    |
    | **Configurar as definições de tráfego reencaminhadas** | |
    | Permitir tráfego reencaminhado da rede virtual remota para o myAzFwVNet    | Selecione **Ativado**. |
    | Permitir o tráfego reencaminhado do myAzFwVNet para a rede virtual remota | Selecione **Ativado**. |
    | **Configurar as definições de trânsito de gateway** | |
    | Permitir o trânsito de gateway | Deixe esta opção desmarcada |
    |||

4. Selecione **OK**.

5. Selecione **+ Adicionar**.

6. In **Add Peering** insi ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome do espreitamento do myAzFwVNet para rede virtual remota | Insira **o meuAzFwVNet-to-myPEVNet**. |
    | **Detalhes dos pares** |  |
    | Modelo de implementação de rede virtual  | Deixe o **Gestor de Recursos** predefinido .  |
    | Eu sei o meu iD de recursos | deixe esta opção desselecionada.    |
    | Subscrição | Selecione a sua subscrição.    |
    | Rede virtual | Selecione **myPEVNet**. |
    | Nome do espreitamento da rede virtual remota para o myAzFwVNet    |    Insira **myPEVNet-to-myAzFwVNet**.    |
    | **Configuração** | |
    | **Configurar definições de acesso à rede virtual** | |
    | Permitir o acesso à rede virtual do myAzFwVNet para rede virtual remota | Deixar o padrão **Ativado**.    |
    | Permitir o acesso à rede virtual a partir da rede virtual remota para o myAzFwVNet    | Deixar o padrão **Ativado**.    |
    | **Configurar as definições de tráfego reencaminhadas** | |
    | Permitir tráfego reencaminhado da rede virtual remota para o myAzFwVNet    | Selecione **Ativado**. |
    | Permitir o tráfego reencaminhado do myAzFwVNet para a rede virtual remota | Selecione **Ativado**. |
    | **Configurar as definições de trânsito de gateway** | |
    | Permitir o trânsito de gateway | Deixe esta opção desmarcada |

7. Selecione **OK**.

## <a name="link-the-virtual-networks-to-the-private-dns-zone"></a>Ligue as redes virtuais à zona privada do DNS

Nesta secção, ligaremos as redes virtuais **myVMVNet** e **myAzFwVNet** à zona **privatelink.database.windows.net** DNS privada. Esta zona foi criada quando criámos o ponto final privado. 

A ligação é necessária para que o VM e firewall resolvam o FQDN da base de dados para o seu endereço de ponto final privado. A rede virtual **myPEVNet** foi automaticamente ligada quando o ponto final privado foi criado.

>[!NOTE]
>Se não ligar as redes virtuais VM e firewall à zona privada de DNS, tanto o VM como a firewall poderão ainda resolver o SQL Server FQDN. Resolverão o seu endereço IP público.

1. Na barra de pesquisa do portal, insira **privatelink.database**.

2. Selecione **privatelink.database.windows.net** nos resultados da pesquisa.

3. Selecione **links de rede virtual** em **Definições**.

4. Selecione **+ Adicionar**

5. In **Adicionar link de rede virtual** insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome de ligação | Insira **Link-to-myVMVNet**. |
    | **Detalhes da rede virtual** |  |
    | Conheço o ID de recursos da rede virtual  | deixe esta opção desselecionada.  |
    | Subscrição | Selecione a sua subscrição.    |
    | Rede virtual | Selecione **myVMVNet**. |
    | **CONFIGURAÇÃO** | |
    | Ativar o registo automático | deixe esta opção desselecionada.    |


6. Selecione **OK**.

## <a name="configure-an-application-rule-with-sql-fqdn-in-azure-firewall"></a>Configure uma regra de aplicação com SQL FQDN em Azure Firewall

Nesta secção, configurar uma regra de aplicação para permitir a comunicação entre **o myVM** e o ponto final privado para **o SQL** Server mydbserver.database.windows.net . 

Esta regra permite a comunicação através da firewall que criamos nos passos anteriores.

1. Na barra de pesquisa do portal, insira **myAzureFirewall**.

2. Selecione **myAzureFirewall** nos resultados da pesquisa.

3. Selecione **Regras** em **Definições** na visão geral do **myAzureFirewall.**

4. Selecione o separador **de recolha de regras de aplicação.**

5. Selecione **+ Adicionar a recolha de regras de aplicação**.

6. No **Adicionar a recolha da regra de aplicação** insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome | Insira **o SQLPrivateEndpoint**. |
    | Prioridade | Introduza **100**. |
    | Ação | Insira **Permitir**. |
    | **Regras** |  |
    | **Etiquetas FQDN** | |
    | Name  | Deixe em branco.  |
    | Tipo de origem | Deixe o **endereço IP** predefinido .    |
    | Origem | Deixe em branco. |
    | Etiquetas FQDN | Deixe o **padrão 0 selecionado**. |
    | **FQDNs alvo** | |
    | Name | Insira **o SQLPrivateEndpoint**.    |
    | Tipo de origem | Deixe o **endereço IP** predefinido . |
    | Origem | Insira **10.1.0.0/16**. |
    | Protocolo: Porto | Insira **mssql:1433**. |
    | FQDNs alvo | Introduza **mydbserver.database.windows.net.** |
    |||

7. Selecione **Adicionar**.

## <a name="route-traffic-between-the-virtual-machine-and-private-endpoint-through-azure-firewall"></a>Tráfego de rota entre a máquina virtual e o ponto final privado através do Azure Firewall

Não criámos uma rede virtual que espreita diretamente entre as redes virtuais **myVMVNet** e **myPEVNet.** A máquina virtual **myVM** não tem uma rota para o ponto final privado que criamos. 

Nesta secção, criaremos uma tabela de rotas com uma rota personalizada. 

A rota envia o tráfego da sub-rede **myVM** para o espaço de endereço da rede virtual **myPEVNet,** através do Azure Firewall.

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.

2. Digite **a tabela de rota** na caixa de pesquisa e prima **Enter**.

3. Selecione **a tabela Rota** e, em seguida, selecione **Criar**.

4. Na página do **quadro 'Criar Rota',** utilize a seguinte tabela para configurar a tabela de rotas:

    | Definição | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**.  |
    | **Detalhes da instância** |  |
    | Region | Selecione **South Central US**. |
    | Name | Insira **VMsubnet-to-AzureFirewall**. |
    | Rotas de gateway de propagação | Selecione **Não**. |

5. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração.

6. Quando vir a mensagem **A validação passou**, selecione **Criar**.

7. Assim que a implementação estiver concluída, **selecione Vá para o recurso**.

8. Selecione **Rotas** em **Definições**.

9. Selecione **+ Adicionar**.

10. Na página **'Adicionar rota',** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Nome da rota | Insira **o ponto de reunião myVMsubnet-to-privateendpoint**. |
    | Prefixo de endereço | Insira **10.2.0.0/16**.  |
    | Tipo de salto seguinte | Selecione **Aplicação virtual**. |
    | Endereço do próximo salto | Insira **10.0.0.4**. |

11. Selecione **OK**.

12. Selecione **sub-redes** em **Definições**.

13. Selecione **+ Associado**.

14. Na página de **sub-redes Associate,** insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Rede virtual | Selecione **myVMVNet**. |
    | Sub-rede | Selecione **VMSubnet**.  |

15. Selecione **OK**.

## <a name="connect-to-the-virtual-machine-from-your-client-computer"></a>Ligue-se à máquina virtual a partir do computador do seu cliente

Ligue-se ao **myVm VM** da internet da seguinte forma:

1. Na barra de pesquisa do portal, insira **o myVm-ip**.

2. Selecione **myVM-ip** nos resultados da pesquisa.

3. Copiar ou anotar o valor no **endereço IP**.

4. Se estiver a utilizar o Windows 10, executar o seguinte comando utilizando o PowerShell. Para outras versões de clientes do Windows, utilize um cliente SSH como [o Putty:](https://www.putty.org/)

* Substitua o **nome de utilizador** pelo nome de utilizador de administração que introduziu durante a criação de VM.

* Substitua o **iPaddress** pelo endereço IP do passo anterior.

    ```bash
    ssh username@IPaddress
    ```

5. Introduza a palavra-passe que definiu ao criar **o myVm**

## <a name="access-sql-server-privately-from-the-virtual-machine"></a>Acesso SQL Server em privado a partir da máquina virtual

Nesta secção, você ligará em privado à Base de Dados SQL usando o ponto final privado.

1. Introduza `nslookup mydbserver.database.windows.net`
    
    Receberá uma mensagem semelhante a abaixo:

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mydbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mydbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

2. Instale [ferramentas de linha de comando SQL Server](/sql/linux/quickstart-install-connect-ubuntu#tools).

3. Executar o seguinte comando para ligar ao SQL Server. Utilize o administrador do servidor e a palavra-passe que definiu quando criou o SQL Server nos passos anteriores.

* **\<ServerAdmin>** Substitua-o pelo nome de utilizador de administração que introduziu durante a criação do servidor SQL.

* **\<YourPassword>** Substitua-a pela palavra-passe de administração que introduziu durante a criação do servidor SQL.

    ```bash
    sqlcmd -S mydbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```
4. Um pedido de comando SQL será exibido no login bem sucedido. Introduza a **saída** para sair da ferramenta **sqlcmd.**

5. Feche a ligação ao **myVM** entrando na **saída**.

## <a name="validate-the-traffic-in-azure-firewall-logs"></a>Validar o tráfego nos registos Azure Firewall

1. No portal Azure, selecione **Todos os Recursos** e selecione o seu espaço de trabalho Log Analytics.

2. Selecione **Registos** em **Geral** na página do espaço de trabalho Log Analytics.

3. Selecione o botão azul **Get Start.**

4. Na janela **'Exemplo' das consultas,** selecione **Firewalls** em **Todas as Consultas**.

5. Selecione o botão **Executar** nos **dados de registo de regras de aplicação**.

6. Na saída de consulta de registo, verifique **se mydbserver.database.windows.net** está listado em **FQDN** e **SQLPrivateEndpoint** está listado em **RuleCollection**.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de usar os recursos, elimine o grupo de recursos e todos os recursos que contém:

1. Introduza o **myResourceGroup** na caixa **de Pesquisa** no topo do portal e selecione o **myResourceGroup** a partir dos resultados da pesquisa.

1. Selecione **Eliminar grupo de recursos**.

1. Introduza **o myResourceGroup** para **type the RESOURCE GROUP NAME** e selecione **Delete**.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você explorou diferentes cenários que você pode usar para restringir o tráfego entre uma máquina virtual e um ponto final privado usando Azure Firewall. 

Ligou-se ao VM e comunicou-se de forma segura à base de dados através do Azure Firewall utilizando um link privado.

Para saber mais sobre o ponto final privado, veja [o que é Azure Private Endpoint?](private-endpoint-overview.md)
