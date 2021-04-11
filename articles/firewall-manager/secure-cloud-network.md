---
title: 'Tutorial: Proteja o seu hub virtual usando o Azure Firewall Manager'
description: Neste tutorial, aprende-se a proteger o seu hub virtual com o Azure Firewall Manager usando o portal Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/19/2021
ms.author: victorh
ms.openlocfilehash: 14ec6fafbbadff2ecc37b229270c269f068a666f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104670465"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Tutorial: Proteja o seu hub virtual usando o Azure Firewall Manager

Utilizando o Azure Firewall Manager, pode criar centros virtuais seguros para proteger o tráfego da sua rede de nuvem destinada a endereços IP privados, Azure PaaS e internet. O encaminhamento de tráfego para a firewall é automatizado, por isso não há necessidade de criar rotas definidas pelo utilizador (UDRs).

![garantir a rede de nuvens](media/secure-cloud-network/secure-cloud-network.png)

O Gestor de Firewall também suporta uma arquitetura de rede virtual hub. Para comparar os tipos de arquitetura de rede virtual e de hub, veja quais são as opções de [arquitetura do Azure Firewall Manager?](vhubs-and-vnets.md)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar a rede virtual falada
> * Criar um hub virtual seguro
> * Ligue o hub e falou redes virtuais
> * Encaminhe o tráfego para o seu hub
> * Implementar os servidores
> * Crie uma política de firewall e proteja o seu hub
> * Testar a firewall

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="create-a-hub-and-spoke-architecture"></a>Criar um hub e falar arquitetura

Em primeiro lugar, crie redes virtuais faladas onde possa colocar os seus servidores.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>Criar duas redes virtuais faladas e sub-redes

As duas redes virtuais terão cada um um servidor de carga de trabalho e serão protegidas pela firewall.

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Procure por **rede Virtual** e selecione **Criar**.
2. Em **Subscrição**, selecione a sua subscrição.
1. Para **o grupo de recursos**, selecione Criar **novo**, e digite **fw-manager-rg** para o nome e selecione **OK**.
2. Para **nome**, tipo **Spoke-01**.
3. Para **a Região**, selecione **(EUA) Leste DOS EUA.**
4. Selecione **Seguinte: Endereços IP**.
1. Em **Espaço de endereços**, escreva **10.0.0.0/16**.
3. No **nome da sub-rede,** selecione **predefinição**.
4. Para **o nome da sub-rede**,  **escreva Workload-01-SN**.
5. Para **a gama de endereços da sub-rede**, tipo **10.0.1.0/24**.
6. Selecione **Guardar**.
1. Selecione **Rever + criar**.
2. Selecione **Criar**.

Repita este procedimento para criar outra rede virtual semelhante:

Nome: **Spoke-02**<br>
Espaço do endereço: **10.1.0.0/16**<br>
Nome da sub-rede: **Workload-02-SN**<br>
Intervalo de endereços da sub-rede: **10.1.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>Criar o centro virtual seguro

Crie o seu hub virtual seguro utilizando o Gestor de Firewall.

1. A partir da página inicial do portal Azure, selecione **Todos os serviços**.
2. Na caixa de pesquisa, escreva **O Gestor de Firewall** e selecione O Gestor de **Firewall**.
3. Na página **'Gestor de Firewall',** selecione **Ver centros virtuais protegidos**.
4. No **| do Gestor de Firewall Página de centros virtuais protegidos,** **selecione Criar um novo hub virtual seguro**.
5. Para **o grupo de recursos**, selecione **fw-manager-rg**.
7. Para **a Região**, selecione **East US**.
1. Para o **nome do hub virtual Secured**, escreva **Hub-01**.
2. Para **o espaço do endereço hub**, tipo **10.2.0.0/16**.
3. Para o novo nome vWAN, escreva **Vwan-01**.
4. Deixe a **porta de entrada Incluir VPN para ativar a** caixa de verificação Trusted Security Partners.
5. Selecione **Seguinte: Azure Firewall**.
6. Aceite a definição padrão **Azure Firewall** **Ativada** e, em seguida, selecione **Seguinte: Parceiro de Segurança Fidedigno**.
7. Aceite a definição de **desativação** **padrão do Parceiro de Segurança Fidedigna** e selecione **Seguinte: Review + create**.
8. Selecione **Criar**. 

   Leva cerca de 30 minutos para ser acionado.

Pode obter o endereço IP público de firewall após a implementação.

1. Open **Firewall Manager**.
2. Selecione **centros virtuais**.
3. Selecione **hub-01**.
7. Selecione **a configuração IP pública**.
8. Observe o endereço IP público para utilizar mais tarde.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>Ligue o hub e falou redes virtuais

Agora pode espreitar o centro e falar redes virtuais.

1. Selecione o grupo de recursos **fw-manager-rg** e, em seguida, selecione o **VWan-01** virtual WAN.
2. Em **Conectividade**, selecione **ligações de rede virtuais.**
3. Selecione **Adicionar ligação**.
4. Para **o nome de ligação**, tipo **hub-spoke-01**.
5. Para **Hubs**, selecione **Hub-01**.
6. Para **o grupo de recursos**, selecione **fw-manager-rg**.
7. Para **rede virtual**, selecione **Spoke-01**.
8. Selecione **Criar**.

Repita para ligar a rede virtual **Spoke-02:** nome de ligação - **hub-spoke-02**

## <a name="deploy-the-servers"></a>Implementar os servidores

1. No portal Azure, selecione **Criar um recurso**.
2. Selecione **o Centro de Dados do Windows Server 2016** na lista **Popular.**
3. Introduza estes valores para a máquina virtual:

   |Definição  |Valor  |
   |---------|---------|
   |Grupo de recursos     |**fw-manager-rg**|
   |Nome da máquina virtual     |**Srv-workload-01**|
   |Region     |**(EUA) Leste dos EUA)**|
   |Nome do utilizador do administrador     |escrever um nome de utilizador|
   |Palavra-passe     |escrever uma senha|

4. De acordo com **as regras portuárias de entrada**, para portas de entrada **pública,** selecione **Nenhum**.
6. Aceite as outras predefinições e selecione **Seguinte: Discos**.
7. Aceite as predefinições do disco e selecione **Seguinte: Networking**.
8. Selecione **o Spoke-01** para a rede virtual e selecione **Workload-01-SN** para a sub-rede.
9. Para **IP público**, selecione **Nenhum**.
11. Aceite os outros incumprimentos e selecione **Seguinte: Gestão**.
12. **Selecione Desativar** para desativar os diagnósticos de arranque. Aceite os outros predefinidos e selecione **Review + create**.
13. Reveja as definições na página do resumo e, em seguida, **selecione Criar**.

Utilize as informações na tabela seguinte para configurar outra máquina virtual chamada **Srv-Workload-02**. O resto da configuração é o mesmo que a máquina virtual **Srv-workload-01.**

|Definição  |Valor  |
|---------|---------|
|Rede virtual|**Porta-voz 02**|
|Sub-rede|**Carga de trabalho 02-SN**|

Depois de os servidores serem implantados, selecione um recurso de servidor e **note** o endereço IP privado para cada servidor.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Crie uma política de firewall e proteja o seu hub

Uma política de firewall define coleções de regras para direcionar o tráfego em um ou mais centros virtuais seguros. Vai criar a sua política de firewall e, em seguida, proteger o seu centro.

1. A partir do Gestor de Firewall, **selecione Ver as políticas de Firewall**.
2. Selecione **Criar Azure Firewall Policy**.
1. Para **o grupo de recursos**, selecione **fw-manager-rg**.
1. De acordo com **os detalhes da Política**, para a Política do tipo **Nome-01** e para a **Região** selecione **East US**. 
1. Selecione **Seguinte: Definições DNS**.
1. Selecione **Seguinte: Inspeção TLS (pré-visualização)**.
1. Selecione **Seguinte : Regras**.
1. No **separador Regras,** **selecione Adicione uma coleção de regras**.
1. Na página **de recolha de regras,** **digite App-RC-01** para o **nome**.
1. Para **o tipo de recolha de regras**, selecione **Aplicação**.
1. Para **prioridade**, tipo **100**.
1. Certifique-se de que **a ação de recolha de regras** é **permitir**.
1. Para a regra **Tipo nome** **Allow-msft**.
1. Para o **tipo Fonte**, selecione o **endereço IP**.
1. Para **fonte**, tipo **\*** .
1. Para **protocolo**, **escreva http,https**.
1. Certifique-se de que **o tipo de destino** é **FQDN.**
1. Para **destino**, **\* escreva .microsoft.com**.
1. Selecione **Adicionar**.

Adicione uma regra de DNAT para que possa ligar um ambiente de trabalho remoto à máquina virtual **Srv-Workload-01.**

1. Selecione **a recolha de Adicionar/Regra**.
1. Para **nome**, escreva **dnat-rdp**.
1. Para **o tipo de recolha de regras**, selecione **DNAT**.
1. Para **prioridade**, tipo **100**.
1. Para a regra **Tipo de Nome** **Allow-rdp**.
1. Para o **tipo Fonte**, selecione o **endereço IP**.
1. Para **fonte**, tipo **\*** .
1. Em **Protocolo**, selecione **TCP**.
1. Para **portos de destino**, tipo **3389**.
1. Para **o tipo de destino**, selecione endereço **IP**.
1. Para **destino**, digite o endereço IP público de firewall que anotado anteriormente.
1. Para **endereço traduzido**, digite o endereço IP privado para **Srv-Workload-01** que anotou anteriormente.
1. Em **Porta traduzida**, escreva **3389**.
1. Selecione **Adicionar**.

Adicione uma regra de rede para que possa ligar um ambiente de trabalho remoto de **Srv-Workload-01** a **Srv-Workload-02**.

1. **Selecione Adicione uma coleção de regras.**
2. Para **nome**, tipo **vnet-rdp**.
3. Para **o tipo de recolha de regras**, selecione **Rede**.
4. Para **prioridade**, tipo **100**.
1. Para **ação de recolha de regras**, selecione **Permitir**.
1. Para a regra **Tipo de Nome** **Allow-vnet**.
1. Para o **tipo Fonte**, selecione o **endereço IP**.
1. Para **fonte**, tipo **\*** .
1. Em **Protocolo**, selecione **TCP**.
1. Para **portos de destino**, tipo **3389**.
1. Para **o tipo de destino**, selecione endereço **IP**.
1. Para **destino**, digite o endereço IP privado **Srv-Workload-02** que observou anteriormente.
1. Selecione **Adicionar**.
1. Selecione **Rever + criar**.
1. Selecione **Criar**.

## <a name="associate-policy"></a>Política associada

Associe a política de firewall ao centro.

1. A partir do Gestor de Firewall, selecione **Azure Firewall Policies**.
1. Selecione a caixa de verificação para **a Política-01**.
1. **Selecione Gerir associações/Centros associados.**
1. Selecione **hub-01**.
1. Selecione **Adicionar**.

## <a name="route-traffic-to-your-hub"></a>Encaminhe o tráfego para o seu hub

Agora deve certificar-se de que o tráfego de rede é encaminhado através da sua firewall.

1. A partir do Gestor de Firewall, selecione **Centros Virtuais.**
2. Selecione **Hub-01**.
3. Em **Definições**, selecione **configuração de segurança**.
4. Sob **o tráfego de Internet**, selecione **Azure Firewall**.
5. Sob **tráfego privado**, selecione Enviar através do **Azure Firewall**.
1. Selecione **Guardar**.

   Leva alguns minutos para atualizar as tabelas de rotas.
1. Verifique se as duas ligações mostram que o Azure Firewall protege tanto a Internet como o tráfego privado.

## <a name="test-the-firewall"></a>Testar a firewall

Para testar as regras de firewall, irá ligar um ambiente de trabalho remoto utilizando o endereço IP público de firewall, que é NATed a **Srv-Workload-01**. A partir daí, utilizará um navegador para testar a regra da aplicação e ligar um ambiente de trabalho remoto ao **Srv-Workload-02** para testar a regra da rede.

### <a name="test-the-application-rule"></a>Testar a regra da aplicação

Agora, teste as regras da firewall para confirmar que funciona como esperado.

1. Conecte um ambiente de trabalho remoto para firewall endereço IP público e inscreva-se.

3. Abra o Internet Explorer e navegue até `https://www.microsoft.com`.
4. Selecione **OK**  >  **Feche** os alertas de segurança do Internet Explorer.

   Devia ver a página inicial da Microsoft.

5. Navegue para `https://www.google.com`.

   Deve estar bloqueado pela firewall.

Então agora verificou que a regra da aplicação de firewall está a funcionar:

* Pode navegar para o único FQDN permitido, mas não para quaisquer outros.

### <a name="test-the-network-rule"></a>Testar a regra da rede

Agora teste a regra da rede.

- A partir do Srv-Workload-01, abra um ambiente de trabalho remoto para o endereço IP privado Srv-Workload-02.

   Um ambiente de trabalho remoto deve ligar-se ao Srv-Workload-02.

Então agora verificou que a regra da rede de firewall está a funcionar:
* Pode ligar um ambiente de trabalho remoto a um servidor localizado noutra rede virtual.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar de testar os seus recursos de firewall, elimine o grupo de recursos **fw-manager-rg** para eliminar todos os recursos relacionados com firewall.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça os parceiros de segurança de confiança](trusted-security-partners.md)
