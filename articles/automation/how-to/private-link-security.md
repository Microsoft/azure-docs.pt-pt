---
title: Utilize o Azure Private Link para ligar de forma segura as redes à Azure Automation
description: Utilize o Azure Private Link para ligar de forma segura as redes à Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 07/09/2020
ms.subservice: ''
ms.openlocfilehash: a4985784a17f2e0350a7b2c7a4f62f574862d50c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91714361"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation-preview"></a>Utilize o Azure Private Link para ligar de forma segura as redes à Azure Automation (pré-visualização)

O Ponto Final Privado do Azure é uma interface de rede que o liga a um serviço de forma privada e segura com a tecnologia Azure Private Link. O Private Endpoint utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço de Automação para o seu VNet. O tráfego de rede entre as máquinas do VNet e a conta Automation atravessa o VNet e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública.

Por exemplo, tem um VNet onde desativou o acesso à Internet de saída. No entanto, pretende aceder à sua conta de Automação em privado e utilizar funcionalidades de Automação como Webhooks, Configuração do Estado e trabalhos de runbook em Trabalhadores De Runbook Híbridos. Além disso, pretende que os utilizadores tenham acesso à conta Automation apenas através do VNET.  A implantação de ponto final privado alcança estes objetivos.

Este artigo abrange quando usar e como configurar um ponto final privado com a sua conta Automation (pré-visualização).

![Visão geral conceptual do Link Privado para Azure Automation](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> O suporte private Link com a Azure Automation (pré-visualização) está disponível apenas nas nuvens do Governo Azure Commercial e Azure US.

## <a name="advantages"></a>Vantagens

Com Link Privado pode:

- Ligue-se privadamente à Azure Automation sem abrir qualquer acesso público à rede.
- Ligue-se privadamente ao espaço de trabalho do Azure Monitor Log Analytics sem abrir qualquer acesso público à rede.

    >[!NOTE]
    >Isto é necessário se a sua conta Demômes estiver ligada a um espaço de trabalho do Log Analytics para encaminhar dados de trabalho, e quando tiver ativado funcionalidades como Gestão de Atualização, Rastreio de Alterações e Inventário, Configuração do Estado ou VMs de início/paragem durante o horário de expediente. Para obter mais informações sobre o Private Link for Azure Monitor, consulte [use Azure Private Link para ligar de forma segura as redes ao Azure Monitor](../../azure-monitor/platform/private-link-security.md).

- Certifique-se de que os seus dados de Automação só são acedidos através de redes privadas autorizadas.
- Impedir a exfiltração de dados das suas redes privadas definindo o seu recurso Azure Automation que se conecta através do seu ponto final privado.
- Ligue de forma segura a sua rede privada no local à Azure Automation utilizando o ExpressRoute e o Private Link.
- Mantenha todo o tráfego dentro da rede de espinha dorsal microsoft Azure.

Para mais informações, consulte [os Principais Benefícios do Link Privado.](../../private-link/private-link-overview.md#key-benefits)

## <a name="how-it-works"></a>Como funciona

A Azure Automation Private Link liga um ou mais pontos finais privados (e, portanto, as redes virtuais em que estão contidas) ao seu recurso conta de automação. Estes pontos finais são máquinas que usam webhooks para iniciar um livro de aplicação, máquinas que hospedam o papel de Trabalhador de Runbook Híbrido e nós DSC.

Depois de criar pontos finais privados para automação, cada um dos URLs de Automação virados para o público, que você ou uma máquina pode contactar diretamente, está mapeado para um ponto final privado no seu VNet.

Como parte do lançamento de pré-visualização, uma conta Automation não pode aceder aos recursos da Azure que são protegidos usando o ponto final privado. Por exemplo, Azure Key Vault, Azure SQL, Conta de Armazenamento Azure, etc.

### <a name="webhook-scenario"></a>Cenário Webhook

Você pode começar os runbooks fazendo um POST no URL webhook. Por exemplo, o URL parece: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="state-configuration-agentsvc-scenario"></a>Cenário de configuração do estado (agentesvc)

A Configuração do Estado fornece-lhe um serviço de gestão de configuração Azure que lhe permite escrever, gerir e compilar configurações de Configuração do Estado (DSC) de PowerShell Desired (DSC) para nós em qualquer datacenter de nuvem ou no local.

O agente na máquina regista-se com o serviço DSC e, em seguida, utiliza o ponto final de serviço para puxar a configuração do DSC. O ponto final do serviço de agente parece: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

O URL para o ponto final & privado público seria o mesmo, no entanto, seria mapeado para um endereço IP privado quando o link privado está ativado.

## <a name="planning-based-on-your-network"></a>Planeamento com base na sua rede

Antes de configurar o seu recurso de conta Automation, considere os requisitos de isolamento da sua rede. Avalie o acesso das suas redes virtuais à internet pública e as restrições de acesso à sua conta de Automação (incluindo a criação de um Âmbito do Grupo de Ligação Privada para Registos de Monitores Azure se estiver integrado na sua conta Demôm automação). Inclua também uma revisão dos [registos DNS](./automation-region-dns-records.md) do serviço de Automação como parte do seu plano para garantir que as funcionalidades suportadas funcionem sem problemas.

### <a name="connect-to-a-private-endpoint"></a>Ligar-se a um ponto final privado

Crie um ponto final privado para ligar a nossa rede. Pode criá-lo no [portal Azure Private Link center](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). Uma vez aplicadas as alterações ao publicNetaccess e ao link privado, pode demorar até 35 minutos para que produzam efeitos.

Nesta secção, irá criar um ponto final privado para a sua conta Demôm automação.

1. No lado superior esquerdo do ecrã, **selecione Criar um recurso > Networking > Private Link Center (Preview)**.

2. No **Private Link Center - Overview,** sobre a opção de construir uma **ligação privada a um serviço**, selecione **Start**.

3. Na **Criação de uma máquina virtual - Básicos, insira**ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.  |
    | **DETALHES DE INSTÂNCIA** |  |
    | Nome | Insira o seu *PrivateEndpoint*. |
    | Region | Selecione **A Sua Região**. |
    |||

4. Selecione **Seguinte: Recurso**.

5. Na **Criação de um ponto final privado - Recurso,** insira ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    |Método de ligação  | Selecione ligar a um recurso Azure no meu diretório.|
    | Subscrição| Selecione a sua subscrição. |
    | Tipo de recurso | Selecione **Microsoft.Automation/automationS**. |
    | Recurso |Selecione *myAutomationAAcount*|
    |Subresource-alvo |Selecione *Webhook* ou *DSCAndHybridWorker* dependendo do seu cenário.|
    |||

6. Selecione **Seguinte: Configuração**.

7. Em **Criar um ponto final privado - Configuração,** introduzir ou selecionar as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    |**REDES**| |
    | Rede virtual| Selecione *MyVirtualNetwork*. |
    | Sub-rede | Selecione *mySubnet*. |
    |**INTEGRAÇÃO DE DNS PRIVADO**||
    |Integrar com zona DNS privada |Selecione **Sim**. |
    |Zona DNS Privada |Selecione *(Novo)privatelink.azure-automation.net* |
    |||

8. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração.

9. Quando vir a mensagem **A validação passou**, selecione **Criar**.

No **Centro de Ligação Privada (Pré-visualização)**, selecione **pontos finais privados** para visualizar o seu recurso de ligação privada.

![Ligação privada de recursos de automação](./media/private-link-security/private-link-automation-resource.png)

Selecione o recurso para ver todos os detalhes. Isto cria um novo ponto final privado para a sua conta Demôm automação e atribui-lhe um IP privado a partir da sua rede virtual. O **estado de Ligação** mostra como **aprovado.**

Da mesma forma, um nome de domínio totalmente qualificado (FQDN) único é criado para a Configuração do Estado (agentsvc) e para o tempo de trabalho do Trabalhador Runbook Híbrido (jrds). Cada um deles é atribuído um IP separado do seu VNet e o estado de **Ligação** aparece conforme **aprovado.**

Se o consumidor de serviço tiver permissões Azure RBAC no recurso Automation, pode escolher o método de aprovação automática. Neste caso, quando o pedido chega ao recurso do fornecedor de Automação, não é necessária qualquer ação do prestador de serviços e a ligação é automaticamente aprovada.

## <a name="set-public-network-access-flags"></a>Definir bandeiras de acesso à rede pública

Pode configurar uma conta Automation para negar todas as configurações públicas e permitir que apenas as ligações através de pontos finais privados melhorem ainda mais a segurança da rede. Se pretender restringir o acesso à conta Automation apenas dentro do VNet e não permitir o acesso a partir da internet pública, pode definir `publicNetworkAccess` a propriedade para `$false` .

Quando a definição de Acesso à **Rede Pública** está definida para , `$false` apenas são permitidas ligações através de pontos finais privados e todas as ligações através de pontos finais públicos são negadas com uma mensagem de erro não autorizada e o estado HTTP de 401. 

O seguinte script PowerShell mostra como `Get` e a propriedade de Acesso à Rede `Set` **Pública** ao nível da conta Dem automação:

```powershell
$account = Get-AzResource -ResourceType Microsoft.Automation/automationAccounts -ResourceGroupName "<resourceGroupName>" -Name "<automationAccountName>" -ApiVersion "2020-01-13-preview"
$account.Properties | Add-Member -Name 'publicNetworkAccess' -Type NoteProperty -Value $false
$account | Set-AzResource -Force -ApiVersion "2020-01-13-preview"
```

## <a name="dns-configuration"></a>Configuração do DNS

Ao ligar-se a um recurso de ligação privada utilizando um nome de domínio totalmente qualificado (FQDN) como parte da cadeia de ligação, é importante configurar corretamente as definições de DNS para resolver o endereço IP privado atribuído. Os serviços Azure existentes podem já ter uma configuração DNS para utilizar ao ligar em cima de um ponto final público. A sua configuração DNS deve ser revista e atualizada para se ligar utilizando o seu ponto final privado.

A interface de rede associada ao ponto final privado contém o conjunto completo de informações necessárias para configurar o seu DNS, incluindo endereços IP FQDN e privados atribuídos para um determinado recurso de ligação privada.

Pode utilizar as seguintes opções para configurar as definições de DNS para pontos finais privados:

* Utilize o ficheiro hospedeiro (apenas recomendado para testes). Pode utilizar o ficheiro anfitrião numa máquina virtual para substituir o DNS para obter primeiro a resolução do nome.

* Utilize uma [zona privada de DNS](../../dns/private-dns-privatednszone.md). Você pode usar zonas de DNS privadas para anular a resolução DNS para um determinado ponto final privado. Uma zona privada de DNS pode ser ligada à sua rede virtual para resolver domínios específicos. Para permitir que o agente da sua máquina virtual comunique através do ponto final privado, crie um registo privado de DNS como `privatelink.azure-automation.net` . Adicione um novo DNS *Um* mapeamento de registo ao IP do ponto final privado.

* Utilize o reencaminhador DNS (opcional). Pode utilizar o seu remetente DNS para anular a resolução DNS para um determinado recurso de ligação privada. Se o seu [servidor DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) estiver hospedado numa rede virtual, pode criar uma regra de encaminhamento DENS para utilizar uma zona de DNS privada para simplificar a configuração de todos os recursos de ligação privada.

Para obter mais informações, consulte [a configuração do DNS do Ponto Final Privado Azure](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Private Endpoint, veja [o que é Azure Private Endpoint?](../../private-link/private-endpoint-overview.md)