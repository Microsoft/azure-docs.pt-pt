---
title: 'Tutorial: Implementar & configurar firewall Azure utilizando o portal Azure'
description: Neste tutorial, irá aprender a implementar e configurar o Azure Firewall com o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 07/15/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: e73f11ec178c067941ee33e02f37c96605460ee0
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658592"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Tutorial: implementar e configurar o Azure Firewall com o portal do Azure

Controlar o acesso de rede de saída é uma parte importante de um plano de segurança de rede geral. Por exemplo, pode querer limitar o acesso a sites web. Ou, pode querer limitar os endereços IP de saída e portas que podem ser acedidos.

Uma forma de controlar o acesso de rede de saída a partir de uma sub-rede do Azure é com a Azure Firewall. Com a Azure Firewall, pode configurar:

* Regras da aplicação que definem nomes de domínio completamente qualificado (FQDNs) que podem ser acedidos a partir de uma sub-rede.
* Regras de rede que definem o endereço de origem, o protocolo, a porta de destino e o endereço de destino.

O tráfego de rede está sujeito às regras de firewall configuradas quando encaminha o tráfego de rede para a firewall como o gateway padrão de sub-rede.

Para este tutorial, cria-se um VNet único simplificado com duas sub-redes para fácil implementação.

Para implantações de produção, [recomenda-se](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) um modelo de hub e spoke, onde a firewall está no seu próprio VNet. Os servidores de carga de trabalho estão em VNets espreitados na mesma região com uma ou mais sub-redes.

* **AzureFirewallSubnet** - a firewall está nesta sub-rede.
* **Workload-SN** - o servidor de carga de trabalho está nesta sub-rede. O tráfego de rede desta sub-rede passa pela firewall.

![Tutorial de infraestrutura de rede](media/tutorial-firewall-deploy-portal/tutorial-network.png)

Neste tutorial, vai aprender a:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implementar uma firewall
> * Criar uma rota predefinida
> * Configure uma regra de aplicação para permitir o acesso a www.google.com
> * Configurar uma regra de rede para permitir o acesso aos servidores DNS externos
> * Configurar uma regra NAT para permitir um ambiente de trabalho remoto para o servidor de teste
> * Testar a firewall

Se preferir, pode executar este tutorial com o [Azure PowerShell](deploy-ps.md).

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="set-up-the-network"></a>Configurar a rede

Em primeiro lugar, crie um grupo de recursos para conter os recursos necessários para implementar a firewall. Em seguida, crie um VNet, sub-redes e um servidor de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos contém todos os recursos para o tutorial.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. No menu do portal Azure, selecione **grupos de recursos** ou procure e selecione *grupos* de Recursos a partir de qualquer página. Em seguida, selecione **Adicionar**.
3. Para **o nome do grupo de recursos**, insira *Test-FW-RG*.
4. Em **Subscrição**, selecione a sua subscrição.
5. Em **Localização do grupo de recursos**, selecione uma localização. Todos os outros recursos que cria devem estar no mesmo local.
6. Selecione **Criar**.

### <a name="create-a-vnet"></a>Criar uma VNet

Esta VNet irá conter três sub-redes.

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é /26. Para obter mais informações sobre o tamanho da sub-rede, consulte [a Azure Firewall FAQ](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. No menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.
1. Selecione rede virtual **de rede**  >  **Virtual network**.
2. Em **Subscrição**, selecione a sua subscrição.
3. Para **o grupo de recursos**, selecione **Test-FW-RG**.
4. Em **Nome**, escreva **Test-FW-VN**.
5. Para **a Região**, selecione o mesmo local que usou anteriormente.
6. Selecione **Seguinte: endereços IP**.
7. Para **o espaço IPv4 Address,** tipo **10.0.0.0/16**.
8. Na **sub-rede,** selecione **o padrão**.
9. Para o nome **da sub-rede,** tipo **AzureFirewallSubnet**. A firewall estará nesta sub-rede, e o nome da sub-rede **tem** de ser AzureFirewallSubnet.
10. Para **a gama Address**, tipo **10.0.1.0/26**.
11. Selecione **Guardar**.

   Em seguida, crie uma sub-rede para o servidor de carga de trabalho.

1. **Selecione Adicionar sub-rede**.
4. Para **o nome da sub-rede,** **escreva Workload-SN**.
5. Para **a gama de endereços sub-rede**, tipo **10.0.2.0/24**.
6. Selecione **Adicionar**.
7. Selecione **Rever + criar**.
8. Selecione **Criar**.

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Agora crie a máquina virtual de carga de trabalho e coloque-a na sub-rede **Workload-SN.**

1. No menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.
2. Selecione **Compute** e, em seguida, selecione **máquina Virtual**.
3. **Centro de dados do Windows Server 2016** na lista em destaque.
4. Introduza estes valores para a máquina virtual:

   |Definição  |Valor  |
   |---------|---------|
   |Grupo de recursos     |**Teste-FW-RG**|
   |Nome da máquina virtual     |**Srv-Work**|
   |Região     |O mesmo que anterior|
   |Imagem|Windows Server 2019 Datacenter|
   |Nome do utilizador do administrador     |Digite um nome de utilizador|
   |Palavra-passe     |Digite uma senha|

4. De acordo com **as regras portuárias de entrada**, portas de entrada **pública,** selecione **Nenhum**.
6. Aceite as outras predefinições e selecione **Seguinte: Discos**.
7. Aceite as predefinições do disco e selecione **Seguinte: Networking**.
8. Certifique-se de que **o Test-FW-VN** está selecionado para a rede virtual e a sub-rede é **Workload-SN**.
9. Para **IP público**, selecione **Nenhum**.
11. Aceite os outros incumprimentos e selecione **Seguinte: Gestão**.
12. Selecione **Off** para desativar os diagnósticos de arranque. Aceite os outros predefinidos e selecione **Review + create**.
13. Reveja as definições na página do resumo e, em seguida, **selecione Criar**.

## <a name="deploy-the-firewall"></a>Implementar a firewall

Implemente a firewall na VNet.

1. No menu do portal do Azure ou a partir da **Home page**, selecione **Criar um recurso**.
2. Digite **firewall** na caixa de pesquisa e prima **Enter**.
3. Selecione **Firewall** e, em seguida, selecione **Criar**.
4. Na página **Criar uma firewall**, utilize a seguinte tabela para configurar a firewall:

   |Definição  |Valor  |
   |---------|---------|
   |Subscrição     |\<your subscription\>|
   |Grupo de recursos     |**Teste-FW-RG** |
   |Name     |**Test-FW01**|
   |Localização     |Selecionar a mesma localização que utilizou anteriormente|
   |Escolher uma rede virtual     |**Utilização existente**: **Test-FW-VN**|
   |Endereço IP público     |**Adicionar novo**<br>**Nome**:  **fw-pip**|

5. Selecione **Rever + criar**.
6. Reveja o resumo e, em seguida, **selecione Criar** para criar a firewall.

   Este processo irá demorar alguns minutos a implementar.
7. Após a implementação concluída, vá ao grupo de recursos **Test-FW-RG** e selecione a firewall **Test-FW01.**
8. Observe os endereços IP privados e públicos da firewall. Usará estes endereços mais tarde.

## <a name="create-a-default-route"></a>Criar uma rota predefinida

Na sub-rede **Workload-SN**, vai configurar a rota de saída predefinida para passar pela firewall.

1. No menu do portal Azure, selecione **Todos os serviços** ou procure e selecione *Todos os serviços* de qualquer página.
2. Em **Rede,** selecione **tabelas de rota**.
3. Selecione **Adicionar**.
4. Em **Nome**, escreva **Firewall-route**.
5. Em **Subscrição**, selecione a sua subscrição.
6. Para **o grupo de recursos**, selecione **Test-FW-RG**.
7. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
8. Selecione **Criar**.
9. Selecione **Refresh** e, em seguida, selecione a tabela **de rota de rota firewall.**
10. Selecione **Sub-redes** e, em seguida, **selecione Associate**.
11. Selecione **Rede Virtual**  >  **Test-FW-VN**.
12. Para **a sub-rede**, selecione **Workload-SN**. Certifique-se de que seleciona apenas a **sub-rede Workload-SN** para esta rota, caso contrário a sua firewall não funcionará corretamente.

13. Selecione **OK**.
14. Selecione **Rotas** e, em seguida, selecione **Adicionar**.
15. Para **o nome da rota**, tipo **fw-dg**.
16. Em **Prefixo de endereço**, escreva **0.0.0.0/0**.
17. Em **Tipo de salto seguinte**, selecione **Aplicação virtual**.

    O Azure Firewall é, de facto, um serviço gerido, mas a aplicação virtual funciona nesta situação.
18. Em **Endereço do próximo salto**, escreva o endereço IP privado para a firewall, que anotou anteriormente.
19. Selecione **OK**.

## <a name="configure-an-application-rule"></a>Configurar uma regra de aplicação

Esta é a regra da aplicação que permite o acesso de saída a `www.google.com` .

1. Abra o **Test-FW-RG** e selecione a firewall **Test-FW01.**
2. Na página **Teste-FW01,** em **Definições,** selecione **Regras**.
3. Selecione o separador **de recolha de regras de aplicação.**
4. **Selecione Adicionar a recolha de regras de aplicação**.
5. Em **Nome**, escreva **App-Coll01**.
6. Em **Prioridade**, escreva **200**.
7. Em **Ação**, selecione **Permitir**.
8. De **acordo com as regras**, Target **FQDNs**, for **Name**, type **Allow-Google**.
9. Para **o tipo de fonte**, selecione endereço **IP**.
10. Para **fonte**, tipo **10.0.2.0/24**.
11. Em **Protocolo:porta**, escreva **http, https**.
12. Para **O Alvo FQDNS,** tipo **`www.google.com`**
13. Selecione **Adicionar**.

O Azure Firewall inclui uma coleção de regras incorporadas para os FQDNs de infraestrutura que são permitidos por predefinição. Estes FQDNs são específicos da plataforma e não podem ser utilizados para outros fins. Para obter mais informações, veja [FQDNs de Infraestrutura](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurar uma regra de rede

Esta é a regra de rede que permite acesso de saída aos dois endereços IP na porta 53 (DNS).

1. Selecione o separador **de recolha de regras da Rede.**
2. **Selecione Adicionar a recolha de regras de rede**.
3. Em **Nome**, escreva **Net-Coll01**.
4. Em **Prioridade**, escreva **200**.
5. Em **Ação**, selecione **Permitir**.
6. De **acordo com as regras**, **endereços IP**, para **nome,** tipo **Allow-DNS**.
7. Em **Protocolo**, selecione **UDP**.
9. Para **o tipo de fonte**, selecione endereço **IP**.
1. Para **fonte**, tipo **10.0.2.0/24**.
2. Para **o tipo destino,** selecione **o endereço IP**.
3. Endereço **de destino**, tipo **209.244.0.3.209.244.0.4**

   Estes são servidores DNS públicos operados pela CenturyLink.
1. Em **Portas de Destino**, escreva **53**.
2. Selecione **Adicionar**.

## <a name="configure-a-dnat-rule"></a>Configurar uma regra DNAT

Esta regra permite-lhe ligar um ambiente de trabalho remoto à Srv-Work máquina virtual através da firewall.

1. Selecione o **separador de recolha de regras NAT.**
2. Selecione **Adicionar a coleção de regras NAT**.
3. Para **nome,** escreva **rdp**.
4. Em **Prioridade**, escreva **200**.
5. De **acordo com as regras**, para **nome**, tipo **rdp-nat**.
6. Em **Protocolo**, selecione **TCP**.
7. Para **o tipo de fonte**, selecione endereço **IP**.
8. Para **fonte,** escreva * *\** _.
9. Para _*Endereço destino**, digite o endereço IP público de firewall.
10. Para **portos de destino**, tipo **3389**.
11. Para **endereço traduzido,** digite o endereço IP privado **Srv-work.**
12. Em **Porta traduzida**, escreva **3389**.
13. Selecione **Adicionar**.


### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Alterar o endereço DNS primário e secundário para a interface de rede **Srv-Work**

Para efeitos de teste neste tutorial, configuure os endereços DNS primários e secundários do servidor. Isto não é um requisito geral da Azure Firewall.

1. No menu do portal Azure, selecione **grupos de recursos** ou procure e selecione *grupos* de Recursos a partir de qualquer página. Selecione o grupo de recursos **Test-FW-RG.**
2. Selecione a interface de rede para a máquina virtual **Srv-Work.**
3. Em **Definições**, selecione **servidores DNS**.
4. Nos **servidores DNS**, selecione **Custom**.
5. Escreva **209.244.0.3** na caixa de texto **Adicionar servidor DNS** e **209.244.0.4** na caixa de texto seguinte.
6. Selecione **Guardar**.
7. Reinicie a máquina virtual **Srv-Work**.

## <a name="test-the-firewall"></a>Testar a firewall

Agora, teste a firewall para confirmar que funciona como esperado.

1. Conecte um ambiente de trabalho remoto para firewall endereço IP público e inscreva-se na máquina virtual **Srv-Work.** 
3. Abra o Internet Explorer e navegue até `https://www.google.com`.
4. Selecione **OK**  >  **Feche** os alertas de segurança do Internet Explorer.

   Devia ver a página inicial do Google.

5. Navegue para `https://www.microsoft.com`.

   Deve estar bloqueado pela firewall.

Então agora verificaste que as regras da firewall estão a funcionar:

* Pode navegar para o único FQDN permitido, mas não para quaisquer outros.
* Pode resolver nomes DNS com o servidor DNS externo configurado.

## <a name="clean-up-resources"></a>Limpar os recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos elimine o grupo de recursos **Test-FW-RG** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Tutorial: monitorizar registos do Azure Firewall](./firewall-diagnostics.md)