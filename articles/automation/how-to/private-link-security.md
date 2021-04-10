---
title: Utilize o Azure Private Link para ligar de forma segura as redes à Azure Automation
description: Utilize o Azure Private Link para ligar de forma segura as redes à Azure Automation
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/11/2020
ms.subservice: ''
ms.openlocfilehash: f3c9197faaae89e0ffb238f987ee66dafea8abdd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579800"
---
# <a name="use-azure-private-link-to-securely-connect-networks-to-azure-automation"></a>Utilize o Azure Private Link para ligar de forma segura as redes à Azure Automation

O Ponto Final Privado do Azure é uma interface de rede que o liga a um serviço de forma privada e segura com a tecnologia Azure Private Link. O Private Endpoint utiliza um endereço IP privado a partir do seu VNet, efetivamente trazendo o serviço de Automação para o seu VNet. O tráfego de rede entre as máquinas do VNet e a conta Automation atravessa o VNet e uma ligação privada na rede de espinha dorsal da Microsoft, eliminando a exposição da internet pública.

Por exemplo, tem um VNet onde desativou o acesso à Internet de saída. No entanto, pretende aceder à sua conta de Automação em privado e utilizar funcionalidades de Automação como Webhooks, Configuração do Estado e trabalhos de runbook em Trabalhadores De Runbook Híbridos. Além disso, pretende que os utilizadores tenham acesso à conta Automation apenas através do VNET.  A implantação de ponto final privado alcança estes objetivos.

Este artigo abrange quando usar e como configurar um ponto final privado com a sua conta Automation.

![Visão geral conceptual do Link Privado para Azure Automation](./media/private-link-security/private-endpoints-automation.png)

>[!NOTE]
> O suporte private Link com a Azure Automation está disponível apenas nas nuvens do Governo Azure Commercial e Azure US.

## <a name="advantages"></a>Vantagens

Com Link Privado pode:

- Ligue-se privadamente à Azure Automation sem abrir qualquer acesso público à rede.
- Ligue-se privadamente ao espaço de trabalho do Azure Monitor Log Analytics sem abrir qualquer acesso público à rede.

    >[!NOTE]
    >É necessário um ponto final privado separado para o seu espaço de trabalho Log Analytics se a sua conta Demômes estiver ligada a um espaço de trabalho do Log Analytics para encaminhar dados de trabalho, e quando tiver ativado funcionalidades como Gestão de Atualização, Rastreio de Alterações e Inventário, Configuração do Estado ou VMs de início/paragem durante o horário de folga. Para obter mais informações sobre o Private Link for Azure Monitor, consulte [use Azure Private Link para ligar de forma segura as redes ao Azure Monitor](../../azure-monitor/logs/private-link-security.md).

- Certifique-se de que os seus dados de Automação só são acedidos através de redes privadas autorizadas.
- Impedir a exfiltração de dados das suas redes privadas definindo o seu recurso Azure Automation que se conecta através do seu ponto final privado.
- Ligue de forma segura a sua rede privada no local à Azure Automation utilizando o ExpressRoute e o Private Link.
- Mantenha todo o tráfego dentro da rede de espinha dorsal microsoft Azure.

Para mais informações, consulte [os Principais Benefícios do Link Privado.](../../private-link/private-link-overview.md#key-benefits)

## <a name="limitations"></a>Limitações

- Na implementação atual do Private Link, os trabalhos na nuvem de conta de automação não podem aceder aos recursos da Azure que são garantidos usando o ponto final privado. Por exemplo, Azure Key Vault, Azure SQL, Conta de Armazenamento Azure, etc. Para resolver isto, use um [Trabalhador De Runbook Híbrido.](../automation-hybrid-runbook-worker.md)
- Tem de utilizar a versão mais recente do [agente Log Analytics](../../azure-monitor/agents/log-analytics-agent.md) para Windows ou Linux.
- O [Log Analytics Gateway](../../azure-monitor/agents/gateway.md) não suporta o Link Privado.

## <a name="how-it-works"></a>Como funciona

A Azure Automation Private Link liga um ou mais pontos finais privados (e, portanto, as redes virtuais em que estão contidas) ao seu recurso conta de automação. Estes pontos finais são máquinas que usam webhooks para iniciar um livro de execução, máquinas que hospedam o papel de Trabalhador de Runbook Híbrido e nós de Configuração do Estado Desejado (DSC).

Depois de criar pontos finais privados para automação, cada um dos URLs de Automação virados para o público está mapeado para um ponto final privado no seu VNet. Você ou uma máquina podem contactar diretamente os URLs automation.

### <a name="webhook-scenario"></a>Cenário Webhook

Você pode começar os runbooks fazendo um POST no URL webhook. Por exemplo, o URL parece: `https://<automationAccountId>.webhooks.<region>.azure-automation.net/webhooks?token=gzGMz4SMpqNo8gidqPxAJ3E%3d`

### <a name="hybrid-runbook-worker-scenario"></a>Cenário híbrido do trabalhador do runbook

A funcionalidade de Trabalhador de Runbook Híbrido do utilizador da Azure Automation permite-lhe executar livros de execução diretamente na máquina Azure ou não-Azure, incluindo servidores registados com servidores ativados Azure Arc. A partir da máquina ou servidor que está hospedando o papel, você pode executar runbooks diretamente sobre ele e contra recursos no ambiente para gerir esses recursos locais.

Um ponto final JRDS é utilizado pelo trabalhador híbrido para iniciar/parar os livros, transferir os livros para o trabalhador e enviar o fluxo de registo de trabalho de volta para o serviço de Automação.Depois de permitir o ponto final do JRDS, o URL seria assim: `https://<automationaccountID>.jobruntimedata.<region>.azure-automation.net` . Isto garantiria a execução de runbook no trabalhador híbrido ligado à Rede Virtual Azure é capaz de executar empregos sem a necessidade de abrir uma ligação de saída à Internet.  

> [!NOTE]
>Com a implementação atual de Private Links for Azure Automation, apenas suporta trabalhos de funcionamento no Trabalhador De Runbook Híbrido ligado a uma rede virtual Azure e não suporta trabalhos na nuvem.

## <a name="hybrid-worker-scenario-for-update-management"></a>Cenário do trabalhador híbrido para a gestão de atualização  

O sistema Hybrid Runbook Worker suporta um conjunto de runbooks ocultos utilizados pela função De Gestão de Atualização que são projetados para instalar atualizações especificadas pelo utilizador em máquinas Windows e Linux. Quando a Azure Automation Update Management estiver ativada, qualquer máquina ligada ao seu espaço de trabalho Log Analytics é automaticamente configurada como um trabalhador de runbook híbrido do sistema.

Para compreender & configurar a avaliação de Gestão de Atualização [sobre a Gestão de Atualizações](../update-management/overview.md). A funcionalidade de Gestão de Atualização tem uma dependência de um espaço de trabalho Log Analytics, pelo que requer a ligação do espaço de trabalho com uma conta Automation. Um espaço de trabalho Log Analytics armazena dados recolhidos pela solução e acolhe as suas pesquisas e vistas de registo.

Se pretender que as suas máquinas configuradas para a gestão de Atualização se conectem ao espaço de trabalho do & Do Registo de Adoção de Forma segura através do canal Private Link, tem de ativar o Link Privado para o espaço de trabalho Log Analytics ligado à Conta de Automação configurada com Ligação Privada.

Pode controlar como um espaço de trabalho Log Analytics pode ser alcançado a partir de fora dos âmbitos de ligação privada, seguindo os passos descritos no [Configure Log Analytics](../../azure-monitor/logs/private-link-security.md#configure-log-analytics). Se definir **Permita o acesso à rede pública para ingestão** a **Nº,** então as máquinas fora dos âmbitos ligados não podem enviar dados para este espaço de trabalho. Se definir Permita o acesso à **rede pública para consultas** a **No,** então as máquinas fora dos âmbitos não podem aceder aos dados neste espaço de trabalho.

Utilize o sub-recurso alvo **DSCAndHybridWorker** para permitir o Private Link para os trabalhadores híbridos do sistema & do utilizador.

> [!NOTE]
> As máquinas hospedadas fora do Azure que são geridas pela Update Management e estão ligadas ao Azure VNet sobre o estojo privado ExpressRoute, túneis VPN e redes virtuais espreitadas usando pontos finais privados de suporte private Link.

### <a name="state-configuration-agentsvc-scenario"></a>Cenário de configuração do estado (agentesvc)

A Configuração do Estado fornece-lhe um serviço de gestão de configuração Azure que lhe permite escrever, gerir e compilar configurações de Configuração do Estado (DSC) de PowerShell Desired (DSC) para nós em qualquer datacenter de nuvem ou no local.

O agente na máquina regista-se com o serviço DSC e, em seguida, utiliza o ponto final de serviço para puxar a configuração do DSC. O ponto final do serviço de agente parece: `https://<automationAccountId>.agentsvc.<region>.azure-automation.net` .

O URL para o ponto final & privado público seria o mesmo, no entanto, seria mapeado para um endereço IP privado quando o link privado está ativado.

## <a name="planning-based-on-your-network"></a>Planeamento com base na sua rede

Antes de configurar o seu recurso de conta Automation, considere os requisitos de isolamento da sua rede. Avalie o acesso das suas redes virtuais à internet pública e as restrições de acesso à sua conta de Automação (incluindo a criação de um Âmbito do Grupo de Ligação Privada para Registos de Monitores Azure se estiver integrado na sua conta Demôm automação). Inclua também uma revisão dos [registos DNS](./automation-region-dns-records.md) do serviço de Automação como parte do seu plano para garantir que as funcionalidades suportadas funcionem sem problemas.

### <a name="connect-to-a-private-endpoint"></a>Ligar-se a um ponto final privado

Crie um ponto final privado para ligar a nossa rede. Pode criá-lo no [portal Azure Private Link center](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints). Uma vez aplicadas as alterações ao publicNetworkAccess e Private Link, pode demorar até 35 minutos para que produzam efeitos.

Nesta secção, irá criar um ponto final privado para a sua conta Demôm automação.

1. No lado superior esquerdo do ecrã, **selecione Criar um > de rede de recursos > Private Link Center**.

2. No **Private Link Center - Overview,** sobre a opção de construir uma **ligação privada a um serviço**, selecione **Start**.

3. Na **Criação de uma máquina virtual - Básicos, insira** ou selecione as seguintes informações:

    | Definição | Valor |
    | ------- | ----- |
    | **DETALHES DO PROJETO** | |
    | Subscrição | Selecione a sua subscrição. |
    | Grupo de recursos | Selecione **myResourceGroup**. Criou isto na secção anterior.  |
    | **DETALHES DE INSTÂNCIA** |  |
    | Name | Insira o seu *PrivateEndpoint*. |
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
    |Integrar com zona DNS privada |Selecione **Yes** (Sim). |
    |Zona DNS Privada |Selecione *(Novo)privatelink.azure-automation.net* |
    |||

8. Selecione **Rever + criar**. Acedeu à página **Rever + criar**, onde o Azure valida a sua configuração.

9. Quando vir a mensagem **A validação passou**, selecione **Criar**.

No **Private Link Center,** selecione **Pontos finais privados** para ver o seu recurso de ligação privada.

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

Também pode controlar a propriedade de acesso à rede pública a partir do portal Azure. A partir da sua Conta de Automação, selecione o isolamento da **rede** a partir do painel esquerdo sob a secção **Definições de Conta.** Quando a definição de Acesso à Rede Pública está definida para **Nº,** apenas são permitidas ligações sobre pontos finais privados e todas as ligações sobre os pontos finais públicos são negadas.

![Definição de acesso à rede pública](./media/private-link-security/allow-public-network-access.png)

## <a name="dns-configuration"></a>Configuração do DNS

Ao ligar-se a um recurso de ligação privada utilizando um nome de domínio totalmente qualificado (FQDN) como parte da cadeia de ligação, é importante configurar corretamente as definições de DNS para resolver o endereço IP privado atribuído. Os serviços Azure existentes podem já ter uma configuração DNS para utilizar ao ligar em cima de um ponto final público. A sua configuração DNS deve ser revista e atualizada para se ligar utilizando o seu ponto final privado.

A interface de rede associada ao ponto final privado contém o conjunto completo de informações necessárias para configurar o seu DNS, incluindo endereços IP FQDN e privados atribuídos para um determinado recurso de ligação privada.

Pode utilizar as seguintes opções para configurar as definições de DNS para pontos finais privados:

* Utilize o ficheiro hospedeiro (apenas recomendado para testes). Pode utilizar o ficheiro anfitrião numa máquina virtual para substituir o DNS para obter primeiro a resolução do nome. A sua entrada em DNS deve parecer o seguinte exemplo: `privatelinkFQDN.jrds.sea.azure-automation.net` .

* Utilize uma [zona privada de DNS](../../dns/private-dns-privatednszone.md). Você pode usar zonas de DNS privadas para anular a resolução DNS para um determinado ponto final privado. Uma zona privada de DNS pode ser ligada à sua rede virtual para resolver domínios específicos. Para permitir que o agente da sua máquina virtual comunique através do ponto final privado, crie um registo privado de DNS como `privatelink.azure-automation.net` . Adicione um novo DNS *Um* mapeamento de registo ao IP do ponto final privado.

* Utilize o reencaminhador DNS (opcional). Pode utilizar o seu remetente DNS para anular a resolução DNS para um determinado recurso de ligação privada. Se o seu [servidor DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) estiver hospedado numa rede virtual, pode criar uma regra de encaminhamento DENS para utilizar uma zona de DNS privada para simplificar a configuração de todos os recursos de ligação privada.

Para obter mais informações, veja [Configuração do DNS do Ponto Final Privado do Azure](../../private-link/private-endpoint-dns.md).

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o Private Endpoint, veja [o que é Azure Private Endpoint?](../../private-link/private-endpoint-overview.md)