---
title: 'Tutorial: Proteja o seu WAN virtual utilizando a pré-visualização do Azure Firewall Manager'
description: Neste tutorial, você aprende a proteger o seu WAN virtual com o Azure Firewall Manager usando o portal Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: victorh
ms.openlocfilehash: b13f3b4eeb57c34f51152bb6d1914f6c80f31be1
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691024"
---
# <a name="tutorial-secure-your-virtual-wan-using-azure-firewall-manager-preview"></a>Tutorial: Proteja o seu WAN virtual utilizando a pré-visualização do Azure Firewall Manager 

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Utilizando a Pré-visualização do Azure Firewall Manager, pode criar centros virtuais seguros para proteger o tráfego da rede de nuvem destinado a endereços IP privados, Azure PaaS e internet. O encaminhamento de tráfego para a firewall é automatizado, por isso não há necessidade de criar rotas definidas pelo utilizador (UDRs).

![proteger a rede de nuvens](media/secure-cloud-network/secure-cloud-network.png)

O Firewall Manager também suporta uma arquitetura de rede virtual hub. Para uma comparação entre os tipos de arquitetura de rede virtual e hub seguros, veja quais são as opções de [arquitetura Azure Firewall Manager?](vhubs-and-vnets.md)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar a rede virtual falada
> * Criar um centro virtual seguro
> * Ligue o hub e falou VNets
> * Crie uma política de firewall e proteja o seu hub
> * Encaminhe o tráfego para o seu centro
> * Testar a firewall

## <a name="create-a-hub-and-spoke-architecture"></a>Criar um hub e arquitetura falada

Primeiro, crie uma rede virtual com voz onde possa colocar os seus servidores.

### <a name="create-a-spoke-virtual-network-and-subnets"></a>Criar uma rede virtual e subnets falados

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Em **Rede,** selecione **rede Virtual**.
2. Em **Subscrição**, selecione a sua subscrição.
1. Para **o grupo Recursos,** selecione **Criar novo**e digitar **FW-Manager** para o nome e selecione **OK**.
2. Para **nome**, tipo **Spoke-01**.
3. Para **a Região,** selecione **(EUA) Leste dos EUA**.
4. Selecione **Seguinte: Endereços IP**.
1. Para **o espaço 'Endereço',** aceite o padrão **10.0.0.0/16**.
3. Sob **o nome Subnet,** selecione **predefinido**.
4. Mude o nome da sub-rede para **Workload-SN**.
5. Para a gama de **endereços Subnet**, tipo **10.0.1.0/24**.
6. Selecione **Guardar**..

Em seguida, crie uma sub-rede para um servidor de salto.

1. **Selecione Adicionar sub-rede**.
4. Para **o nome Subnet,** escreva **Jump-SN**.
5. Para a gama de **endereços Subnet**, tipo **10.0.2.0/24**.
6. Selecione **Adicionar**.

Agora crie a rede virtual.

1. Selecione **Rever + criar**.
2. Selecione **Criar**.

### <a name="create-the-secured-virtual-hub"></a>Criar o centro virtual seguro

Crie o seu hub virtual seguro usando o Firewall Manager.

1. A partir da página inicial do portal Azure, selecione **Todos os serviços.**
2. Na caixa de pesquisa, digite **O Firewall Manager** e selecione Firewall **Manager**.
3. Na página **do Firewall Manager,** selecione **'Ver polos virtuais' protegidos**.
4. No **Gestor de Firewall [ Página de hubs virtuais seguros,** **selecione Criar novo hub virtual seguro**.
5. Para **o grupo Derecursos,** selecione **FW-Manager**.
7. Para **a Região,** selecione **East US**.
1. Para o nome do **hub virtual seguro,** tipo **Hub-01**.
2. Para **o espaço de endereço**hub , tipo **10.1.0.0/16**.
3. Para o novo nome vWAN, escreva **Vwan-01**.
4. Deixe a porta de **entrada VpN incluir para ativar** a caixa de verificação de parceiros de segurança fidedigna saqueada.
5. Selecione **Seguinte:Firewall Azure**.
6. Aceite a definição **ativada** por predefinição do **Firewall Do Azure** e, em seguida, selecione **Next: Trusted Security Partner**.
7. Aceite a definição de **desativação de desativação** do parceiro de **segurança fidedigno** por defeito e selecione **Seguinte: Rever + criar**.
8. Selecione **Criar**. Levará cerca de 30 minutos para ser implantado.

### <a name="connect-the-hub-and-spoke-vnets"></a>Ligue o hub e falou VNets

Agora pode espreitar o centro e falar VNets.

1. Selecione o grupo de recursos **FW-Manager** e, em seguida, selecione o **VWAN-01** virtual WAN.
2. Em **Conectividade, selecione** **ligações de rede virtual.**
3. **Selecione Adicionar ligação**.
4. Para **o nome de ligação,** digite **hub-spoke**.
5. Para **Hubs,** selecione **Hub-01**.
6. Para **o grupo Derecursos,** selecione **FW-Manager**.
7. Para **a rede Virtual,** selecione **Spoke-01**.
8. Selecione **OK**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Crie uma política de firewall e proteja o seu hub

Uma política de firewall define coleções de regras para direcionar o tráfego em um ou mais centros virtuais seguros. Vaicriar a sua política de firewall e, em seguida, proteger o seu centro.

1. A partir do Firewall Manager, selecione **as políticas de Firewall Do View Azure**.
2. Selecione **Criar a Política de Firewall Azure**.
3. Em **detalhes de política**, para o tipo de **nome** **Política-01** e para **região** selecione **Leste DOS EUA**.
4. Selecione **Next:Rules**.
5. No separador **Regras,** selecione **Adicionar uma coleção de regras**.
6. Na página adicionar uma página de recolha de **regras,** digite **RC-01** para o **nome**.
7. Para o tipo de **recolha de regras,** selecione **Application**.
8. Para **Prioridade**, tipo **100**.
9. Certifique-se de que a **ação de recolha** de regras é **permitida.**
10. Para a regra **Nome** tipo **Desmame**.
11. Para **endereço Fonte,** escreva **\***.
12. Para **protocolo**, escreva **http,https**.
13. Certifique-se de que o tipo de destino é **FQDN**.
14. Para **destino**, escreva ** \*.microsoft.com**.
15. Selecione **Adicionar**.
16. Selecione **Seguinte: Hubs**.
17. No separador **Hubs,** selecione **Centros virtuais Associados**.
18. Selecione **Hub-01** e, em seguida, **selecione Adicionar**.
1. Selecione **Rever + criar**.
2. Selecione **Criar**.

Isto pode levar cerca de cinco minutos ou mais para ser concluído.

## <a name="route-traffic-to-your-hub"></a>Encaminhe o tráfego para o seu centro

Agora tem de garantir que o tráfego de rede é encaminhado para a sua firewall.

1. A partir do Firewall Manager, selecione **centros virtuais seguros**.
2. Selecione **Hub-01**.
3. Em **Definições,** selecione **definições de rota**.
4. Sob tráfego de **Internet**, Tráfego de **Redes Virtuais,** selecione **Enviar via Firewall Azure**.
5. Sob **tráfego privado Azure**, **Tráfego para Redes Virtuais,** selecione **Enviar via Firewall Azure**.
6. Selecione **Editar prefixo de endereço IP (es)**.
8. Tipo **10.0.1.0/24** como endereço da sub-rede de carga de trabalho e selecione **Guardar**.
9. Em **Definições,** selecione **Ligações**.
10. Verifique se a ligação **com porta-hub** mostra o Tráfego de **Internet** como **Protegido**.


## <a name="test-your-firewall"></a>Teste a sua firewall

Para testar as suas regras de firewall, terá de implantar alguns servidores. Você vai implementar Workload-Srv na subnet Workload-SN para testar as regras de firewall, e Jump-Srv para que você possa usar Remote Desktop para ligar a partir da Internet e, em seguida, ligar-se a Workload-Srv.

### <a name="deploy-the-servers"></a>Implementar os servidores

1. No portal Azure, selecione **Criar um recurso**.
2. Selecione **O Datacenter do Windows Server 2016** na lista **Popular.**
3. Introduza estes valores para a máquina virtual:

   |Definição  |Valor  |
   |---------|---------|
   |Grupo de recursos     |**FW-Manager**|
   |Nome da máquina virtual     |**Salta-Srv**|
   |Região     |**(EUA) Leste dos EUA)**|
   |Nome de utilizador do administrador     |escrever um nome de utilizador|
   |Palavra-passe     |escrever uma senha|

4. De acordo com as regras da **porta de entrada**, para as portas de entrada **pública,** selecione **Permitir portas selecionadas**.
5. Para **selecionar as portas de entrada,** selecione **RDP (3389)**.
6. Aceite as outras predefinições e selecione **Seguinte: Discos**.
7. Aceite as predefinições do disco e selecione **Seguinte: Networking**.
8. Certifique-se de que o **Spoke-01** está selecionado para a rede virtual e a sub-rede é **Jump-SN**.
9. Para **IP Público,** aceite o novo nome de endereço ip público (Jump-Srv-ip).
11. Aceite os outros predefinições e selecione **Seguinte: Gestão**.
12. Selecione **Off** para desativar diagnósticos de arranque. Aceite as outras predefinições e selecione **Rever + criar**.
13. Reveja as definições na página resumo e, em seguida, selecione **Criar**.

Utilize as informações na tabela seguinte para configurar outra máquina virtual chamada **Workload-Srv**. O resto da configuração é igual à da máquina virtual Srv-Jump.

|Definição  |Valor  |
|---------|---------|
|Subrede|**Workload-SN**|
|IP público|**Nenhum**|
|Portos de entrada pública|**Nenhum**|

### <a name="add-a-route-table-and-default-route"></a>Adicione uma tabela de rotas e uma rota padrão

Para permitir uma ligação à Internet com o Jump-Srv, tem de criar uma tabela de rotas e uma rota de gateway padrão para a Internet a partir da subnet **Jump-SN.**

1. No portal Azure, selecione **Criar um recurso**.
2. Digite **a tabela** de rota na caixa de pesquisa e, em seguida, selecione a **tabela Rota**.
3. Selecione **Criar**.
4. Tipo **RT-01** para **Nome**.
5. Selecione a sua subscrição, **FW-Manager** para o grupo de recursos e **(EUA) Leste dos EUA** para a região.
6. Selecione **Criar**.
7. Quando a implantação estiver concluída, selecione a tabela de rota **RT-01.**
8. Selecione **Rotas** e, em seguida, selecione **Adicionar**.
9. Digite **saltar para inet** para o **nome Rota**.
10. Tipo **0.0.0.0/0** para o **prefixo**do endereço .
11. Selecione **Internet** para o **próximo tipo de lúpulo**.
12. Selecione **OK**.
13. Quando a implementação estiver concluída, selecione **Sub-redes,** então selecione **Associate**.
14. Selecione **Spoke-01** para **rede Virtual**.
15. Selecione **Jump-SN** para **Subnet**.
16. Selecione **OK**.

### <a name="test-the-rules"></a>Testar as regras

Agora, teste as regras da firewall para confirmar que funciona como esperado.

1. A partir do portal Azure, reveja as definições de rede para a máquina virtual **Workload-Srv** e note o endereço IP privado.
2. Ligue um ambiente de trabalho remoto à máquina virtual **Jump-Srv** e inscreva-se. A partir daí, abra uma ligação remota de ambiente de trabalho ao endereço IP privado **Workload-Srv.**

3. Abra o Internet Explorer e navegue até https://www.microsoft.com.
4. Selecione **OK** > **Close** nos alertas de segurança do Internet Explorer.

   Devia ver a página inicial da Microsoft.

5. Navegue para https://www.google.com.

   Deve estar bloqueado pela firewall.

Então agora verificou que as regras da firewall estão funcionando:

* Pode navegar para o único FQDN permitido, mas não para quaisquer outros.



## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Conheça parceiros de segurança fidedignos](trusted-security-partners.md)
