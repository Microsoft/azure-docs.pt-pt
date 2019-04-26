---
title: 'Tutorial: Implementar e configurar o Azure Firewall com o portal do Azure'
description: Neste tutorial, irá aprender a implementar e configurar o Azure Firewall com o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 4/9/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: cd7797ae3b79fb874bafc89437943b084020d800
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60194229"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Tutorial: Implementar e configurar o Azure Firewall com o portal do Azure

Controlar o acesso de rede de saída é uma parte importante de um plano de segurança de rede geral. Por exemplo, pode querer limitar o acesso a sites da web. Em alternativa, pode querer limitar os endereços IP e portas que podem ser acessadas de saída.

Uma forma de controlar o acesso de rede de saída a partir de uma sub-rede do Azure é com a Azure Firewall. Com a Azure Firewall, pode configurar:

* Regras da aplicação que definem nomes de domínio completamente qualificado (FQDNs) que podem ser acedidos a partir de uma sub-rede.
* Regras de rede que definem o endereço de origem, o protocolo, a porta de destino e o endereço de destino.

O tráfego de rede está sujeito às regras de firewall configuradas quando encaminha o tráfego de rede para a firewall como o gateway padrão de sub-rede.

Neste tutorial, criou uma VNet única simplificada com três sub-redes para uma implementação simples. Para implementações de produção, uma [modelo hub- and -spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) é recomendada, em que a firewall está na sua própria VNet. Os servidores de carga de trabalho são nas VNets em modo de peering na mesma região com uma ou mais sub-redes.

* **AzureFirewallSubnet** - a firewall está nesta sub-rede.
* **Workload-SN** - o servidor de carga de trabalho está nesta sub-rede. O tráfego de rede desta sub-rede passa pela firewall.
* **Jump-SN** - o servidor “de ligação” está nesta sub-rede. O servidor de ligação tem um endereço IP público ao qual pode ligar com o Ambiente de Trabalho Remoto. A partir daí, pode ligar (com outro Ambiente de Trabalho Remoto) ao servidor de carga de trabalho.

![Tutorial de infraestrutura de rede](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implementar uma firewall
> * Criar uma rota predefinida
> * Configurar uma regra de aplicação para permitir o acesso ao www.google.com
> * Configurar uma regra de rede para permitir o acesso aos servidores DNS externos
> * Testar a firewall

Se preferir, pode executar este tutorial com o [Azure PowerShell](deploy-ps.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="set-up-the-network"></a>Configurar a rede

Em primeiro lugar, crie um grupo de recursos para conter os recursos necessários para implementar a firewall. Em seguida, crie uma VNet, sub-redes e servidores de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos contém todos os recursos para o tutorial.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na página inicial de portal do Azure, selecione **grupos de recursos** > **Add**.
3. Em **Nome do grupo de recursos**, escreva **Test-FW-RG**.
4. Em **Subscrição**, selecione a sua subscrição.
5. Em **Localização do grupo de recursos**, selecione uma localização. Todos os recursos subsequentes que criar têm de estar na mesma localização.
6. Selecione **Criar**.

### <a name="create-a-vnet"></a>Criar uma VNet

Esta VNet irá conter três sub-redes.

1. A partir do Azure home page do portal, selecione **criar um recurso**.
2. Sob **Networking**, selecione **rede Virtual**.
4. Em **Nome**, escreva **Test-FW-VN**.
5. Em **Espaço de endereços**, escreva **10.0.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **grupo de recursos**, selecione **teste-FW-RG**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Em **Sub-rede**, em **Nome**, escreva **AzureFirewallSubnet**. A firewall estará nesta sub-rede, e o nome da sub-rede **tem** de ser AzureFirewallSubnet.
10. Em **Intervalo de endereços**, escreva **10.0.1.0/24**.
11. Aceite as outras predefinições e, em seguida, selecione **criar**.

> [!NOTE]
> O tamanho mínimo da sub-rede AzureFirewallSubnet é /26.

### <a name="create-additional-subnets"></a>Criar sub-redes adicionais

Em seguida, crie sub-redes para o servidor de ligação e uma sub-rede para os servidores de carga de trabalho.

1. Na página inicial de portal do Azure, selecione **grupos de recursos** > **teste-FW-RG**.
2. Selecione o **FW-teste-VN** rede virtual.
3. Selecione **sub-redes** > **+ sub-rede**.
4. Em **Nome**, escreva **Workload-SN**.
5. Em **Intervalo de endereços**, escreva **10.0.2.0/24**.
6. Selecione **OK**.

Crie outra sub-rede com o nome **Jump-SN** e com o intervalo de endereços **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora, crie as máquinas virtuais de ligação e de carga de trabalho, e coloque-as nas sub-redes adequadas.

1. No portal do Azure, selecione **criar um recurso**.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter** na lista Destaques.
3. Introduza estes valores para a máquina virtual:

   |Definição  |Value  |
   |---------|---------|
   |Grupo de recursos     |**Test-FW-RG**|
   |Nome da máquina virtual     |**Srv-Jump**|
   |Região     |Mesmo que a anterior|
   |Nome de utilizador de administrador     |**azureuser**|
   |Palavra-passe     |**Azure123456!**|

4. Sob **regras de porta de entrada**, para **portas de entrada públicas**, selecione **permitir portas selecionadas**.
5. Para **Selecione as portas de entrada**, selecione **RDP (. 3389)**.

6. Aceite as predefinições e selecione **seguinte: Discos**.
7. Aceite as predefinições de disco e selecione **seguinte: Funcionamento em rede**.
8. Certifique-se de que **FW-teste-VN** está selecionada para a rede virtual e a sub-rede é **atalhos SN**.
9. Para **IP público**, aceite o nome de endereço de ip público novo predefinido (ip de salto de Srv).
11. Aceite as predefinições e selecione **seguinte: Management**.
12. Selecione **desativar** para desativar o diagnóstico de arranque. Aceite as predefinições e selecione **rever + criar**.
13. Reveja as definições na página de resumo e, em seguida, selecione **criar**.

Utilize as informações na tabela seguinte para configurar outra máquina virtual com o nome **Srv trabalho**. O resto da configuração é igual à da máquina virtual Srv-Jump.

|Definição  |Value  |
|---------|---------|
|Subrede|**SN da carga de trabalho**|
|IP público|**Nenhum**|
|Portas de entrada públicas|**Nenhum**|

## <a name="deploy-the-firewall"></a>Implementar a firewall

Implemente a firewall na VNet.

1. Na home page do portal, selecione **criar um recurso**.
2. Tipo **firewall** na caixa de pesquisa e prima **Enter**.
3. Selecione **Firewall** e, em seguida, selecione **criar**.
4. Na página **Criar uma firewall**, utilize a seguinte tabela para configurar a firewall:

   |Definição  |Valor  |
   |---------|---------|
   |Subscrição     |\<a sua subscrição\>|
   |Grupo de recursos     |**Test-FW-RG** |
   |Name     |**Test-FW01**|
   |Location     |Selecionar a mesma localização que utilizou anteriormente|
   |Escolher uma rede virtual     |**Utilizar existente**: **Test-FW-VN**|
   |Endereço IP público     |**Crie um novo**. O endereço IP público tem de ser do tipo SKU Standard.|

5. Selecione **Rever + criar**.
6. Reveja o resumo e, em seguida, selecione **criar** para criar a firewall.

   Este processo irá demorar alguns minutos a implementar.
7. Depois de concluída a implementação, vá para o **teste-FW-RG** recursos de grupo e selecione o **teste FW01** firewall.
8. Anote o endereço IP privado. Vai utilizá-lo mais tarde quando criar a rota predefinida.

## <a name="create-a-default-route"></a>Criar uma rota predefinida

Na sub-rede **Workload-SN**, vai configurar a rota de saída predefinida para passar pela firewall.

1. A partir do Azure home page do portal, selecione **todos os serviços**.
2. Sob **Networking**, selecione **tabelas de rotas**.
3. Selecione **Adicionar**.
4. Em **Nome**, escreva **Firewall-route**.
5. Em **Subscrição**, selecione a sua subscrição.
6. Para **grupo de recursos**, selecione **teste-FW-RG**.
7. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
8. Selecione **Criar**.
9. Selecione **Atualize**e, em seguida, selecione a **Firewall rota** tabela de rotas.
10. Selecione **sub-redes** e, em seguida, selecione **associar**.
11. Selecione **rede Virtual** > **FW-teste-VN**.
12. Para **sub-rede**, selecione **carga de trabalho-SN**. Certifique-se de que seleciona apenas as **SN da carga de trabalho** sub-rede para esta rota, caso contrário, a firewall não funcionará corretamente.

13. Selecione **OK**.
14. Selecione **rotas** e, em seguida, selecione **Add**.
15. Para **nome da rota**, tipo **fw-grupo de distribuição**.
16. Em **Prefixo de endereço**, escreva **0.0.0.0/0**.
17. Em **Tipo de salto seguinte**, selecione **Aplicação virtual**.

    O Azure Firewall é, de facto, um serviço gerido, mas a aplicação virtual funciona nesta situação.
18. Em **Endereço do próximo salto**, escreva o endereço IP privado para a firewall, que anotou anteriormente.
19. Selecione **OK**.

## <a name="configure-an-application-rule"></a>Configurar uma regra de aplicação

Esta é a regra de aplicação que permite acesso de saída www.google.com.

1. Abra o **teste-FW-RG**e selecione o **teste FW01** firewall.
2. Sobre o **FW01 de teste** página, em **definições**, selecione **regras**.
3. Selecione o **coleção de regras de aplicação** separador.
4. Selecione **adicionar a coleção de regras de aplicação**.
5. Em **Nome**, escreva **App-Coll01**.
6. Em **Prioridade**, escreva **200**.
7. Em **Ação**, selecione **Permitir**.
8. Sob **regras**, **FQDNs de destino**, para **nome**, tipo **permitir Google**.
9. Em **Endereços de Origem**, escreva **10.0.2.0/24**.
10. Em **Protocolo:porta**, escreva **http, https**.
11. Para **destino FQDNS**, tipo **www.google.com**
12. Selecione **Adicionar**.

O Azure Firewall inclui uma coleção de regras incorporadas para os FQDNs de infraestrutura que são permitidos por predefinição. Estes FQDNs são específicos da plataforma e não podem ser utilizados para outros fins. Para obter mais informações, veja [FQDNs de Infraestrutura](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurar uma regra de rede

Esta é a regra de rede que permite acesso de saída aos dois endereços IP na porta 53 (DNS).

1. Selecione o **coleção de regras de rede** separador.
2. Selecione **adicionar a coleção de regras de rede**.
3. Em **Nome**, escreva **Net-Coll01**.
4. Em **Prioridade**, escreva **200**.
5. Em **Ação**, selecione **Permitir**.

6. Sob **regras**, para **nome**, tipo **permitir DNS**.
7. Em **Protocolo**, selecione **UDP**.
8. Em **Endereços de Origem**, escreva **10.0.2.0/24**.
9. Em Endereço de destino, escreva **209.244.0.3,209.244.0.4**
10. Em **Portas de Destino**, escreva **53**.
11. Selecione **Adicionar**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Alterar o endereço DNS primário e secundário para a interface de rede **Srv-Work**

Para fins de teste neste tutorial, configure os endereços DNS do servidor primários e secundários. Isso não é um requisito geral do Firewall do Azure.

1. No portal do Azure, abra o grupo de recursos **Test-FW-RG**.
2. Selecione a interface de rede para o **Srv trabalho** máquina virtual.
3. Sob **configurações**, selecione **servidores DNS**.
4. Sob **servidores DNS**, selecione **personalizado**.
5. Escreva **209.244.0.3** na caixa de texto **Adicionar servidor DNS** e **209.244.0.4** na caixa de texto seguinte.
6. Selecione **Guardar**.
7. Reinicie a máquina virtual **Srv-Work**.

## <a name="test-the-firewall"></a>Testar a firewall

Agora, teste a firewall para confirmar que funciona conforme esperado.

1. No portal do Azure, reveja as definições de rede para a máquina virtual **Srv-Work** e anote o endereço IP privado.
2. Ligar um ambiente de trabalho remoto **Srv atalhos** máquina virtual e o início de sessão. A partir daí, abra uma ligação de ambiente de trabalho remoto para o **Srv trabalho** endereço IP privado.

3. Abra o Internet Explorer e navegue até http://www.google.com.
4. Selecione **OK** > **fechar** sobre os alertas de segurança do Internet Explorer.

   Deverá ver a home page do Google.

5. Navegue para http://www.microsoft.com.

   Deve estar bloqueado pela firewall.

Portanto, agora verificar que as regras de firewall estão a funcionar:

* Pode navegar para o único FQDN permitido, mas não para quaisquer outros.
* Pode resolver nomes DNS com o servidor DNS externo configurado.

## <a name="clean-up-resources"></a>Limpar recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos elimine o grupo de recursos **Test-FW-RG** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Tutorial: Monitorize registos de Firewall do Azure](./tutorial-diagnostics.md)
