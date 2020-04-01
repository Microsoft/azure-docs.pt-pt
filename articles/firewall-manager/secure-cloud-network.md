---
title: 'Tutorial: Proteja o seu WAN virtual utilizando a pré-visualização do Azure Firewall Manager'
description: Neste tutorial, você aprende a proteger o seu WAN virtual com o Azure Firewall Manager usando o portal Azure.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 3dc94a8be265682fbe2128f2e5870dfdf5850a2d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "77443062"
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

Primeiro, crie um VNet falado onde possa colocar os seus servidores.

### <a name="create-a-spoke-vnet-and-subnets"></a>Criar uma VNet e subnets falados

1. A partir da página inicial do portal Azure, selecione **Criar um recurso**.
2. Em **Rede,** selecione **rede Virtual**.
4. Para **nome**, tipo **Spoke-01**.
5. Em **Espaço de endereços**, escreva **10.0.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **o grupo Recursos,** selecione **Criar novo**e digitar **FW-Manager** para o nome e selecione **OK**.
8. Para **localização**, selecione **(EUA) East US**.
9. Em **Subnet,** para o tipo **de nome** **Workload-SN**.
10. Em **Intervalo de endereços**, escreva **10.0.1.0/24**.
11. Aceite as outras definições predefinidas e, em seguida, selecione **Criar**.

Em seguida, crie uma sub-rede para um servidor de salto.

1. Na página inicial do portal Azure, selecione **Grupos** > de Recursos**FW-Manager**.
2. Selecione a rede virtual **Spoke-01.**
3. Selecione **Subnets** > **+Subnet**.
4. Para **nome**, tipo **Jump-SN**.
5. Em **Intervalo de endereços**, escreva **10.0.2.0/24**.
6. Selecione **OK**.

### <a name="create-the-secured-virtual-hub"></a>Criar o centro virtual seguro

Crie o seu hub virtual seguro usando o Firewall Manager.

1. A partir da página inicial do portal Azure, selecione **Todos os serviços.**
2. Na caixa de pesquisa, digite **O Firewall Manager** e selecione Firewall **Manager**.
3. Na página **do Firewall Manager,** selecione **Criar um Hub Virtual Seguro**.
4. Na **página Create new Secured virtual hub,** selecione a sua subscrição e o grupo de recursos **FW-Manager.**
5. Para o nome do **hub virtual seguro,** tipo **Hub-01**.
6. Para **localização,** selecione **East US**.
7. Para **o espaço de endereço**hub , tipo **10.1.0.0/16**.
8. Para o novo nome vWAN, escreva **vwan-01**.
9. Deixe a porta de **entrada VpN incluir para ativar** a caixa de verificação de parceiros de segurança fidedigna saqueada.
10. Selecione **Seguinte:Firewall Azure**.
11. Aceite a definição **ativada** por predefinição do **Firewall Do Azure** e, em seguida, selecione **Next: Trusted Security Partner**.
12. Aceite a definição de **desativação de desativação** do parceiro de **segurança fidedigno** por defeito e selecione **Seguinte: Rever + criar**.
13. Selecione **Criar**. Levará cerca de 30 minutos para ser implantado.

### <a name="connect-the-hub-and-spoke-vnets"></a>Ligue o hub e falou VNets

Agora pode espreitar o centro e falar VNets.

1. Selecione o grupo de recursos **FW-Manager** e, em seguida, selecione o **VWAN-01** virtual WAN.
2. Em **Conectividade, selecione** **ligações de rede virtual.**
3. **Selecione Adicionar ligação**.
4. Para **o nome de ligação,** digite **hub-spoke**.
5. Para **Hubs,** selecione **Hub-01**.
6. Para **a rede Virtual,** selecione **Spoke-01**.
7. Selecione **OK**.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Crie uma política de firewall e proteja o seu hub

Uma política de firewall define coleções de regras para direcionar o tráfego em um ou mais centros virtuais seguros. Vaicriar a sua política de firewall e, em seguida, proteger o seu centro.

1. A partir do Firewall Manager, selecione Criar uma Política de **Firewall Azure**.
2. Selecione a sua subscrição e, em seguida, selecione o grupo de recursos **FW-Manager.**
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
16. Selecione **Seguinte: Centros virtuais seguros**.
17. No separador **de cubos virtuais Seguros,** selecione **Hub-01**.
19. Selecione **Rever + criar**.
20. Selecione **Criar**.

Isto pode levar cerca de cinco minutos ou mais para ser concluído.

## <a name="route-traffic-to-your-hub"></a>Encaminhe o tráfego para o seu centro

Agora tem de garantir que o tráfego de rede é encaminhado para a sua firewall.

1. A partir do Firewall Manager, selecione **centros virtuais seguros**.
2. Selecione **Hub-01**.
3. Em **Definições,** selecione **definições de rota**.
4. Sob tráfego de **Internet**, Tráfego de **Redes Virtuais,** selecione **Enviar via Firewall Azure**.
5. Sob **tráfego privado Azure**, **Tráfego para Redes Virtuais,** selecione **Enviar via Firewall Azure**.
6. Selecione **Editar prefixo de endereço IP (es)**.
7. **Selecione Adicionar um prefixo**de endereço IP .
8. Tipo **10.0.1.0/24** como endereço da sub-rede de carga de trabalho e selecione **Guardar**.
9. Em **Definições,** selecione **Ligações**.
10. Selecione a ligação **com porta-hub** e, em seguida, selecione **o tráfego de internet Seguro** e, em seguida, selecione **OK**.


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
   |Nome de utilizador do administrador     |**azureuser**|
   |Palavra-passe     |escrever a sua senha|

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
