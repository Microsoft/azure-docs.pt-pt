---
title: 'Tutorial: Proteja o seu hub virtual usando o Azure Firewall Manager'
description: Neste tutorial, aprende-se a proteger o seu hub virtual com o Azure Firewall Manager usando o portal Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: c44daa67b4029c73c57ca82d72ee0a9759dd4c2d
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563659"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Tutorial: Proteja o seu hub virtual usando o Azure Firewall Manager

Utilizando o Azure Firewall Manager, pode criar centros virtuais seguros para proteger o tráfego da sua rede de nuvem destinada a endereços IP privados, Azure PaaS e internet. O encaminhamento de tráfego para a firewall é automatizado, por isso não há necessidade de criar rotas definidas pelo utilizador (UDRs).

![garantir a rede de nuvens](media/secure-cloud-network/secure-cloud-network.png)

O Gestor de Firewall também suporta uma arquitetura de rede virtual hub. Para comparar os tipos de arquitetura de rede virtual e de hub, veja quais são as opções de [arquitetura do Azure Firewall Manager?](vhubs-and-vnets.md)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar a rede virtual falada
> * Criar um hub virtual seguro
> * Ligue o hub e falou VNets
> * Crie uma política de firewall e proteja o seu hub
> * Encaminhe o tráfego para o seu hub
> * Testar a firewall

## <a name="create-a-hub-and-spoke-architecture"></a>Criar um hub e falar arquitetura

Em primeiro lugar, crie uma rede virtual falada onde possa colocar os seus servidores.

### <a name="create-a-spoke-virtual-network-and-subnets"></a>Criar uma rede virtual falada e sub-redes

1. A partir da página inicial do portal Azure, **selecione Criar um recurso**.
2. Em **Rede,** selecione **Rede Virtual.**
2. Em **Subscrição**, selecione a sua subscrição.
1. Para **o grupo de recursos**, selecione Criar **novo**, e digite **FW-Manager** para o nome e selecione **OK**.
2. Para **nome**, tipo **Spoke-01**.
3. Para **a Região**, selecione **(EUA) Leste DOS EUA.**
4. Selecione **Seguinte: Endereços IP**.
1. Para **o espaço Address**, aceite o **padrão 10.0.0.0/16**.
3. No **nome da sub-rede,** selecione **predefinição**.
4. Altere o nome da sub-rede para **Workload-SN**.
5. Para **a gama de endereços da sub-rede**, tipo **10.0.1.0/24**.
6. **Selecione Guardar**..

Em seguida, crie uma sub-rede para um servidor de salto.

1. **Selecione Adicionar sub-rede**.
4. Para **o nome da sub-rede,** escreva **Jump-SN**.
5. Para **a gama de endereços sub-rede**, tipo **10.0.2.0/24**.
6. Selecione **Adicionar**.

Agora crie a rede virtual.

1. Selecione **Rever + criar**.
2. Selecione **Criar**.

### <a name="create-the-secured-virtual-hub"></a>Criar o centro virtual seguro

Crie o seu hub virtual seguro utilizando o Gestor de Firewall.

1. A partir da página inicial do portal Azure, selecione **Todos os serviços**.
2. Na caixa de pesquisa, escreva **O Gestor de Firewall** e selecione O Gestor de **Firewall**.
3. Na página **'Gestor de Firewall',** selecione **Ver centros virtuais protegidos**.
4. No Gestor de **Firewall ! Página de centros virtuais protegidos,** **selecione Criar um novo hub virtual seguro**.
5. Para **o grupo de recursos**, selecione **FW-Manager**.
7. Para **a Região**, selecione **East US**.
1. Para o **nome do hub virtual Secured**, escreva **Hub-01**.
2. Para **o espaço do endereço hub**, tipo **10.1.0.0/16**.
3. Para o novo nome vWAN, escreva **Vwan-01**.
4. Deixe a **porta de entrada Incluir VPN para ativar a** caixa de verificação Trusted Security Partners.
5. Selecione **Seguinte:Azure Firewall**.
6. Aceite a definição padrão **Azure Firewall** **Ativada** e, em seguida, selecione **Seguinte: Parceiro de Segurança Fidedigno**.
7. Aceite a definição de **desativação** **padrão do Parceiro de Segurança Fidedigna** e selecione **Seguinte: Review + create**.
8. Selecione **Criar**. Levará cerca de 30 minutos para ser acionado.

### <a name="connect-the-hub-and-spoke-vnets"></a>Ligue o hub e falou VNets

Agora podes espreitar o centro e falar VNets.

1. Selecione o grupo de recursos **FW-Manager** e, em seguida, selecione o **VWan-01** virtual WAN.
2. Em **Conectividade**, selecione **ligações de rede virtuais.**
3. Selecione **Adicionar ligação**.
4. Para **o nome de ligação**, tipo **hub-spoke**.
5. Para **Hubs**, selecione **Hub-01**.
6. Para **o grupo de recursos**, selecione **FW-Manager**.
7. Para **rede virtual**, selecione **Spoke-01**.
8. Selecione **Criar**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Crie uma política de firewall e proteja o seu hub

Uma política de firewall define coleções de regras para direcionar o tráfego em um ou mais centros virtuais seguros. Vai criar a sua política de firewall e, em seguida, proteger o seu centro.

1. A partir do Gestor de Firewall, **selecione Ver as políticas de Firewall**.
2. Selecione **Criar Azure Firewall Policy**.
3. De acordo com **os detalhes da Política**, para a Política do tipo **Nome-01** e para a **Região** selecione **East US**. **Name**
4. Selecione **Seguinte:Regras**.
5. No **separador Regras,** **selecione Adicione uma coleção de regras**.
6. Na página **de recolha de regras** Adicionar uma página de recolha de regras, digite **RC-01** para o **nome**.
7. Para **o tipo de recolha de regras**, selecione **Aplicação**.
8. Para **prioridade**, tipo **100**.
9. Certifique-se de que **a ação de recolha de regras** é **permitir**.
10. Para a regra **Tipo nome** **Allow-msft**.
11. Para o **tipo Fonte**, selecione o **endereço IP**.
12. Para **fonte**, tipo **\*** .
13. Para **protocolo**, **escreva http,https**.
14. Certifique-se de que **o tipo de destino** é **FQDN.**
15. Para **destino**, ** \* escreva .microsoft.com**.
16. Selecione **Adicionar**.
17. Selecione **Seguinte : Inteligência de ameaça**.
18. Selecione **Seguinte: Hubs**.
19. No separador **Hubs,** **selecione Centros virtuais Associados**.
20. Selecione **Hub-01** e, em seguida, **selecione Adicionar**.
21. Selecione **Rever + criar**.
22. Selecione **Criar**.

Isto pode levar cerca de cinco minutos ou mais para ser completado.

## <a name="route-traffic-to-your-hub"></a>Encaminhe o tráfego para o seu hub

Agora deve certificar-se de que o tráfego de rede é encaminhado para a sua firewall.

1. A partir do Gestor de Firewall, selecione **centros virtuais seguros**.
2. Selecione **Hub-01**.
3. Em **Definições**, selecione **configuração de segurança**.
4. Sob **o tráfego de Internet**, selecione **Azure Firewall**.
5. Sob **tráfego privado**, selecione Enviar através do **Azure Firewall**.
10. Verifique se a ligação **com o hub** mostra o tráfego de **Internet** como **seguro**.
11. Selecione **Guardar**.


## <a name="test-your-firewall"></a>Teste a sua firewall

Para testar as suas regras de firewall, terá de implantar alguns servidores. Irá implementar Workload-Srv na sub-rede Workload-SN para testar as regras de firewall e Jump-Srv para que possa utilizar o Remote Desktop para ligar a partir da Internet e, em seguida, ligar-se ao Workload-Srv.

### <a name="deploy-the-servers"></a>Implementar os servidores

1. No portal Azure, selecione **Criar um recurso**.
2. Selecione **o Centro de Dados do Windows Server 2016** na lista **Popular.**
3. Introduza estes valores para a máquina virtual:

   |Definição  |Valor  |
   |---------|---------|
   |Grupo de recursos     |**FW-Manager**|
   |Nome da máquina virtual     |**Salto-Srv**|
   |Região     |**(EUA) Leste dos EUA)**|
   |Nome do utilizador do administrador     |escrever um nome de utilizador|
   |Palavra-passe     |escrever uma senha|

4. De acordo com **as regras portuárias de entrada**, para portas de entrada **pública,** selecione **Deixe as portas selecionadas**.
5. Para **selecionar portas de entrada**, selecione **RDP (3389)**.
6. Aceite as outras predefinições e selecione **Seguinte: Discos**.
7. Aceite as predefinições do disco e selecione **Seguinte: Networking**.
8. Certifique-se de que o **Spoke-01** está selecionado para a rede virtual e a sub-rede é **Jump-SN**.
9. Para **IP público**, aceite o nome de endereço ip público predefinido (Jump-Srv-ip).
11. Aceite os outros incumprimentos e selecione **Seguinte: Gestão**.
12. Selecione **Off** para desativar os diagnósticos de arranque. Aceite os outros predefinidos e selecione **Review + create**.
13. Reveja as definições na página do resumo e, em seguida, **selecione Criar**.

Utilize as informações na tabela seguinte para configurar outra máquina virtual chamada **Workload-Srv**. O resto da configuração é igual à da máquina virtual Srv-Jump.

|Definição  |Valor  |
|---------|---------|
|Subrede|**Workload-SN**|
|IP público|**Nenhum**|
|Portos de entrada pública|**Nenhum**|

### <a name="add-a-route-table-and-default-route"></a>Adicione uma tabela de rota e rota padrão

Para permitir uma ligação à Internet ao Jump-Srv, tem de criar uma tabela de rotas e uma rota de gateway padrão para a Internet a partir da sub-rede **Jump-SN.**

1. No portal Azure, selecione **Criar um recurso**.
2. Digite **a tabela** de rotas na caixa de pesquisa e, em seguida, selecione **tabela Rota**.
3. Selecione **Criar**.
4. Tipo **RT-01** para **nome**.
5. Selecione a sua subscrição, **FW-Manager** para o grupo de recursos e **(EUA) East US** para a região.
6. Selecione **Criar**.
7. Quando a implementação estiver concluída, selecione a tabela de rotas **RT-01.**
8. Selecione **Rotas** e, em seguida, selecione **Adicionar**.
9. Digite o nome **"Rota".** **jump-to-inet**
10. Tipo **0.0.0.0/0** para o **prefixo do endereço**.
11. Selecione **Internet** para o **tipo de lúpulo seguinte**.
12. Selecione **OK**.
13. Quando a implementação estiver concluída, selecione **Subnetas**e, em seguida, **selecione Associate**.
14. Selecione **Spoke-01** para **rede Virtual**.
15. Selecione **Jump-SN** para **sub-rede**.
16. Selecione **OK**.

### <a name="test-the-rules"></a>Testar as regras

Agora, teste as regras da firewall para confirmar que funciona como esperado.

1. A partir do portal Azure, reveja as definições de rede para a máquina virtual **Workload-Srv** e note o endereço IP privado.
2. Ligue um ambiente de trabalho remoto à máquina virtual **Jump-Srv** e inscreva-se. A partir daí, abra uma ligação de ambiente de trabalho remoto ao endereço IP privado **Workload-Srv.**

3. Abra o Internet Explorer e navegue até https://www.microsoft.com.
4. Selecione **OK**  >  **Feche** os alertas de segurança do Internet Explorer.

   Devia ver a página inicial da Microsoft.

5. Navegue para https://www.google.com.

   Deve estar bloqueado pela firewall.

Então agora verificaste que as regras da firewall estão a funcionar:

* Pode navegar para o único FQDN permitido, mas não para quaisquer outros.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça os parceiros de segurança de confiança](trusted-security-partners.md)
