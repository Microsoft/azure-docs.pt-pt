---
title: 'Tutorial: Implementar e configurar o Azure Firewall com o portal do Azure'
description: Neste tutorial, irá aprender a implementar e configurar o Azure Firewall com o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0892bde09891d2edbd7f8cc8715ccc0d2f047ed4
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70113479"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Tutorial: Implementar e configurar o Azure Firewall com o portal do Azure

Controlar o acesso de rede de saída é uma parte importante de um plano de segurança de rede geral. Por exemplo, talvez você queira limitar o acesso a sites da Web. Ou, talvez você queira limitar os endereços IP de saída e as portas que podem ser acessadas.

Uma forma de controlar o acesso de rede de saída a partir de uma sub-rede do Azure é com a Azure Firewall. Com a Azure Firewall, pode configurar:

* Regras da aplicação que definem nomes de domínio completamente qualificado (FQDNs) que podem ser acedidos a partir de uma sub-rede.
* Regras de rede que definem o endereço de origem, o protocolo, a porta de destino e o endereço de destino.

O tráfego de rede está sujeito às regras de firewall configuradas quando encaminha o tráfego de rede para a firewall como o gateway padrão de sub-rede.

Neste tutorial, criou uma VNet única simplificada com três sub-redes para uma implementação simples. Para implantações de produção, um [modelo de Hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) é recomendado, onde o firewall está em sua própria VNet. Os servidores de carga de trabalho estão em VNets emparelhados na mesma região com uma ou mais sub-redes.

* **AzureFirewallSubnet** - a firewall está nesta sub-rede.
* **Workload-SN** - o servidor de carga de trabalho está nesta sub-rede. O tráfego de rede desta sub-rede passa pela firewall.
* **Jump-SN** - o servidor “de ligação” está nesta sub-rede. O servidor de ligação tem um endereço IP público ao qual pode ligar com o Ambiente de Trabalho Remoto. A partir daí, pode ligar (com outro Ambiente de Trabalho Remoto) ao servidor de carga de trabalho.

![Tutorial de infraestrutura de rede](media/tutorial-firewall-rules-portal/Tutorial_network.png)

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Configurar um ambiente de rede de teste
> * Implementar uma firewall
> * Criar uma rota predefinida
> * Configurar uma regra de aplicativo para permitir o acesso ao www.google.com
> * Configurar uma regra de rede para permitir o acesso aos servidores DNS externos
> * Testar a firewall

Se preferir, pode executar este tutorial com o [Azure PowerShell](deploy-ps.md).

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="set-up-the-network"></a>Configurar a rede

Em primeiro lugar, crie um grupo de recursos para conter os recursos necessários para implementar a firewall. Em seguida, crie uma VNet, sub-redes e servidores de teste.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

O grupo de recursos contém todos os recursos para o tutorial.

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Na home page portal do Azure, selecione **grupos** > de recursos**Adicionar**.
3. Em **Nome do grupo de recursos**, escreva **Test-FW-RG**.
4. Em **Subscrição**, selecione a sua subscrição.
5. Em **Localização do grupo de recursos**, selecione uma localização. Todos os recursos subsequentes que criar têm de estar na mesma localização.
6. Selecione **Criar**.

### <a name="create-a-vnet"></a>Criar uma VNet

Esta VNet irá conter três sub-redes.

> [!NOTE]
> O tamanho da sub-rede AzureFirewallSubnet é/26. Para obter mais informações sobre o tamanho da sub-rede, consulte perguntas frequentes sobre o [Firewall do Azure](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. No home page de portal do Azure, selecione **criar um recurso**.
2. Em **rede**, selecione **rede virtual**.
4. Em **Nome**, escreva **Test-FW-VN**.
5. Em **Espaço de endereços**, escreva **10.0.0.0/16**.
6. Em **Subscrição**, selecione a sua subscrição.
7. Para **grupo de recursos**, selecione **Test-FW-RG**.
8. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
9. Em **Sub-rede**, em **Nome**, escreva **AzureFirewallSubnet**. A firewall estará nesta sub-rede, e o nome da sub-rede **tem** de ser AzureFirewallSubnet.
10. Para **intervalo de endereços**, digite **10.0.1.0/26**.
11. Aceite as outras configurações padrão e, em seguida, selecione **criar**.

### <a name="create-additional-subnets"></a>Criar sub-redes adicionais

Em seguida, crie sub-redes para o servidor de ligação e uma sub-rede para os servidores de carga de trabalho.

1. Na home page portal do Azure, selecione **grupos** > **de recursos Test-FW-RG**.
2. Selecione a rede virtual **Test-FW-vn** .
3. Selecione sub- **redes** >  **+ sub-rede**.
4. Em **Nome**, escreva **Workload-SN**.
5. Em **Intervalo de endereços**, escreva **10.0.2.0/24**.
6. Selecione **OK**.

Crie outra sub-rede com o nome **Jump-SN** e com o intervalo de endereços **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora, crie as máquinas virtuais de ligação e de carga de trabalho, e coloque-as nas sub-redes adequadas.

1. Na portal do Azure, selecione **criar um recurso**.
2. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter** na lista Destaques.
3. Introduza estes valores para a máquina virtual:

   |Definição  |Value  |
   |---------|---------|
   |Resource group     |**Test-FW-RG**|
   |Nome da máquina virtual     |**SRV de salto**|
   |Região     |Mesmo que o anterior|
   |Nome de usuário do administrador     |**azureuser**|
   |Palavra-passe     |**Azure123456!**|

4. Em **regras de porta de entrada**, para **portas de entrada públicas**, selecione **permitir portas selecionadas**.
5. Para **selecionar portas de entrada**, selecione **RDP (3389)** .

6. Aceite os outros padrões e selecione **avançar: Discos**.
7. Aceite os padrões de disco e selecione **avançar: Rede**.
8. Verifique se **Test-FW-vn** está selecionado para a rede virtual e a sub-rede é **Jump-SN**.
9. Para **IP público**, aceite o novo nome de endereço IP público padrão (SRV-Jump-IP).
11. Aceite os outros padrões e selecione **avançar: Gerenciamento**.
12. Selecione **desativado** para desabilitar o diagnóstico de inicialização. Aceite os outros padrões e selecione revisar **+ criar**.
13. Examine as configurações na página Resumo e, em seguida, selecione **criar**.

Use as informações na tabela a seguir para configurar outra máquina virtual chamada **SRV-Work**. O resto da configuração é igual à da máquina virtual Srv-Jump.

|Definição  |Value  |
|---------|---------|
|Subnet|**Carga de trabalho-SN**|
|IP Público|**Nenhum**|
|Portas de entrada públicas|**Nenhum**|

## <a name="deploy-the-firewall"></a>Implementar a firewall

Implemente a firewall na VNet.

1. No portal home page, selecione **criar um recurso**.
2. Digite **Firewall** na caixa de pesquisa e pressione **Enter**.
3. Selecione **Firewall** e, em seguida, selecione **criar**.
4. Na página **Criar uma firewall**, utilize a seguinte tabela para configurar a firewall:

   |Definição  |Valor  |
   |---------|---------|
   |Subscription     |\<a sua subscrição\>|
   |Resource group     |**Test-FW-RG** |
   |Name     |**Test-FW01**|
   |Location     |Selecionar a mesma localização que utilizou anteriormente|
   |Escolher uma rede virtual     |**Usar existente**: **Test-FW-VN**|
   |Endereço IP público     |**Crie um novo**. O endereço IP público tem de ser do tipo SKU Standard.|

5. Selecione **Rever + criar**.
6. Examine o resumo e, em seguida, selecione **criar** para criar o firewall.

   Este processo irá demorar alguns minutos a implementar.
7. Após a conclusão da implantação, vá para o grupo de recursos **Test-FW-RG** e selecione o firewall **Test-FW01** .
8. Anote o endereço IP privado. Vai utilizá-lo mais tarde quando criar a rota predefinida.

## <a name="create-a-default-route"></a>Criar uma rota predefinida

Na sub-rede **Workload-SN**, vai configurar a rota de saída predefinida para passar pela firewall.

1. No home page de portal do Azure, selecione **todos os serviços**.
2. Em **rede**, selecione **tabelas de rotas**.
3. Selecione **Adicionar**.
4. Em **Nome**, escreva **Firewall-route**.
5. Em **Subscrição**, selecione a sua subscrição.
6. Para **grupo de recursos**, selecione **Test-FW-RG**.
7. Em **Localização**, selecione a mesma localização que utilizou anteriormente.
8. Selecione **Criar**.
9. Selecione **Atualizar**e, em seguida, selecione a tabela **Firewall-rota** de rota.
10. Selecione **sub-redes** e, em seguida, selecione **associar**.
11. Selecione **rede** > virtual**Test-FW-vn**.
12. Para **sub-rede**, selecione **carga de trabalho-SN**. Certifique-se de selecionar somente a sub-rede **carga de trabalho-SN** para essa rota, caso contrário, o firewall não funcionará corretamente.

13. Selecione **OK**.
14. Selecione **rotas** e, em seguida, selecione **Adicionar**.
15. Para **nome da rota**, digite **FW-DG**.
16. Em **Prefixo de endereço**, escreva **0.0.0.0/0**.
17. Em **Tipo de salto seguinte**, selecione **Aplicação virtual**.

    O Azure Firewall é, de facto, um serviço gerido, mas a aplicação virtual funciona nesta situação.
18. Em **Endereço do próximo salto**, escreva o endereço IP privado para a firewall, que anotou anteriormente.
19. Selecione **OK**.

## <a name="configure-an-application-rule"></a>Configurar uma regra de aplicação

Essa é a regra de aplicativo que permite o acesso de saída ao www.google.com.

1. Abra o **Test-FW-RG**e selecione o firewall **Test-FW01** .
2. Na página **Test-FW01** , em **configurações**, selecione **regras**.
3. Selecione a guia **coleção de regras de aplicativo** .
4. Selecione **Adicionar coleção de regras de aplicativo**.
5. Em **Nome**, escreva **App-Coll01**.
6. Em **Prioridade**, escreva **200**.
7. Em **Ação**, selecione **Permitir**.
8. Em **regras**, **FQDNs de destino**, para **nome**, digite **Allow-Google**.
9. Em **Endereços de Origem**, escreva **10.0.2.0/24**.
10. Em **Protocolo:porta**, escreva **http, https**.
11. Para **FQDNS de destino**, digite **www.google.com**
12. Selecione **Adicionar**.

O Azure Firewall inclui uma coleção de regras incorporadas para os FQDNs de infraestrutura que são permitidos por predefinição. Estes FQDNs são específicos da plataforma e não podem ser utilizados para outros fins. Para obter mais informações, veja [FQDNs de Infraestrutura](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurar uma regra de rede

Esta é a regra de rede que permite acesso de saída aos dois endereços IP na porta 53 (DNS).

1. Selecione a guia **coleção de regras de rede** .
2. Selecione **Adicionar coleção de regras de rede**.
3. Em **Nome**, escreva **Net-Coll01**.
4. Em **Prioridade**, escreva **200**.
5. Em **Ação**, selecione **Permitir**.

6. Em **regras**, para **nome**, digite **Allow-DNS**.
7. Em **Protocolo**, selecione **UDP**.
8. Em **Endereços de Origem**, escreva **10.0.2.0/24**.
9. Em Endereço de destino, escreva **209.244.0.3,209.244.0.4**
10. Em **Portas de Destino**, escreva **53**.
11. Selecione **Adicionar**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Alterar o endereço DNS primário e secundário para a interface de rede **Srv-Work**

Para fins de teste neste tutorial, configure os endereços DNS primários e secundários do servidor. Esse não é um requisito geral de firewall do Azure.

1. No portal do Azure, abra o grupo de recursos **Test-FW-RG**.
2. Selecione a interface de rede para a máquina virtual **SRV-Work** .
3. Em **configurações**, selecione **servidores DNS**.
4. Em **servidores DNS**, selecione **personalizado**.
5. Escreva **209.244.0.3** na caixa de texto **Adicionar servidor DNS** e **209.244.0.4** na caixa de texto seguinte.
6. Selecione **Guardar**.
7. Reinicie a máquina virtual **Srv-Work**.

## <a name="test-the-firewall"></a>Testar a firewall

Agora, teste o firewall para confirmar se ele funciona conforme o esperado.

1. No portal do Azure, reveja as definições de rede para a máquina virtual **Srv-Work** e anote o endereço IP privado.
2. Conecte uma área de trabalho remota à máquina virtual **SRV-salto** e entre. A partir daí, abra uma conexão de área de trabalho remota para o endereço IP privado **SRV de trabalho** .

3. Abra o Internet Explorer e navegue até https://www.google.com.
4. Selecione **OK** > **fechar** nos alertas de segurança do Internet Explorer.

   Você deve ver o Google home page.

5. Navegue para https://www.microsoft.com.

   Deve estar bloqueado pela firewall.

Agora você verificou que as regras de firewall estão funcionando:

* Pode navegar para o único FQDN permitido, mas não para quaisquer outros.
* Pode resolver nomes DNS com o servidor DNS externo configurado.

## <a name="clean-up-resources"></a>Limpar recursos

Pode manter os recursos da firewall para o próximo tutorial. Se já não precisar dos mesmos elimine o grupo de recursos **Test-FW-RG** para eliminar todos os recursos relacionados com a firewall.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Monitorar logs de firewall do Azure](./tutorial-diagnostics.md)
