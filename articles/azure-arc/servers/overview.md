---
title: Visão geral do arco do Azure para servidores
description: Saiba como usar o Azure ARC para servidores para automatizar o ciclo de vida da infraestrutura e dos aplicativos.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: automação do Azure, DSC, PowerShell, configuração de estado desejado, gerenciamento de atualizações, controle de alterações, inventário, runbooks, Python, gráfico, híbrido
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 7a2e9d39629e4fdb349652c9c48d0084d051f9f8
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122834"
---
# <a name="what-is-azure-arc-for-servers"></a>O que é o arco do Azure para servidores

O arco do Azure para servidores permite que você gerencie computadores que estão fora do Azure.
Quando um computador não Azure é conectado ao Azure, ele se torna um **computador conectado** e é tratado como um recurso no Azure. Cada **computador conectado** tem uma ID de recurso, é gerenciado como parte de um grupo de recursos dentro de uma assinatura e se beneficia de construções padrão do Azure, como Azure Policy e marcação.

Um pacote de agente precisa ser instalado em cada computador para conectá-lo ao Azure. O restante deste documento explica o processo mais detalhadamente.

Os computadores terão um status de **conectado** ou **desconectado** com base no tempo de check-in do agente. Cada check-in é chamado de pulsação. Se um computador não tiver feito check-in nos últimos 5 minutos, ele será exibido como offline até que a conectividade seja restaurada.  <!-- For more information on troubleshooting agent connectivity, see [Troubleshooting Azure Arc for servers](troubleshoot/arc-for-servers.md). -->

![Servidores conectados](./media/overview/arc-for-servers-onboarded-servers.png)

## <a name="clients"></a>Clientes

### <a name="supported-operating-systems"></a>Sistemas Operativos Suportados

Na visualização pública, damos suporte a:

- Windows Server 2012 R2 e mais recente
- Ubuntu 16, 4 e 18, 4

A versão de visualização pública foi projetada para fins de avaliação e não deve ser usada para gerenciar recursos de produção críticos.

## <a name="azure-subscription-and-service-limits"></a>Limites de serviço e assinatura do Azure

Certifique-se de ler os limites de Azure Resource Manager e planejar o número de computadores a serem conectados de acordo com a diretriz listada para a [assinatura](../../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)e para os [grupos de recursos](../../azure-subscription-service-limits.md#resource-group-limits). Em particular, por padrão, há um limite de 800 servidores por grupo de recursos.

## <a name="networking-configuration"></a>Configuração de rede

Durante a instalação e o tempo de execução, o agente requer conectividade com **pontos de extremidade de serviço de arco do Azure**. Se a conectividade de saída estiver bloqueada por firewalls, verifique se as URLs a seguir não estão bloqueadas por padrão. Todas as conexões são de saída do agente para o Azure e são protegidas com **SSL**. Todo o tráfego pode ser roteado por meio de um proxy **https** . Se você permitir os intervalos de IP ou nomes de domínio aos quais os servidores têm permissão para se conectar, deverá permitir o acesso à porta 443 para as seguintes marcas de serviço e nomes DNS.

Marcas de serviço:

* AzureActiveDirectory
* AzureTrafficManager

Para obter uma lista de endereços IP para cada marca de serviço/região, consulte o arquivo JSON – [intervalos de IP do Azure e marcas de serviço – nuvem pública](https://www.microsoft.com/download/details.aspx?id=56519). A Microsoft publica atualizações semanais que contêm cada serviço do Azure e os intervalos de IP que ele usa. Consulte [marcas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags)para obter mais detalhes.

Esses nomes DNS são fornecidos além das informações de intervalo de IP de marca de serviço, pois a maioria dos serviços atualmente não tem um registro de marca de serviço e, como tal, os IPs estão sujeitos a alterações. Se os intervalos de IP forem necessários para sua configuração de firewall, a marca de serviço **AzureCloud** deverá ser usada para permitir o acesso a todos os serviços do Azure. Não desabilite o monitoramento de segurança ou a inspeção dessas URLs, mas permita que você tenha outro tráfego de Internet.

| Ambiente de Domínio | Pontos finais do serviço do Azure necessários |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Configuração de Convidado|
|*-agentservice-prod-1.azure-automation.net|Configuração de Convidado|
|*. his.hybridcompute.azure-automation.net|Serviço de identidade híbrida|

### <a name="installation-network-requirements"></a>Requisitos de rede de instalação

Baixe o [pacote do agente do computador conectado do Azure](https://aka.ms/AzureConnectedMachineAgent) de nossos servidores de distribuição oficiais os sites abaixo devem estar acessíveis em seu ambiente. Você pode optar por baixar o pacote em um compartilhamento de arquivos e fazer com que o agente seja instalado a partir daí. Nesse caso, o script de integração gerado no portal do Azure pode precisar ser modificado.

Windows:

* `aka.ms`
* `download.microsoft.com`

Linux:

* `aka.ms`
* `packages.microsoft.com`

Consulte a seção [configuração do servidor proxy](quickstart-onboard-powershell.md#proxy-server-configuration)para obter informações sobre como configurar o agente para usar o proxy.

## <a name="register-the-required-resource-providers"></a>Registrar os provedores de recursos necessários

Para usar o arco do Azure para servidores, você deve registrar os provedores de recursos necessários.

* **Microsoft.HybridCompute**
* **Microsoft. GuestConfiguration**

Você pode registrar os provedores de recursos com os seguintes comandos:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

CLI do Azure:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Você também pode registrar os provedores de recursos usando o portal seguindo as etapas em [portal do Azure](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="supported-scenarios"></a>Cenários com suporte

Depois de registrar um nó, você pode começar a gerenciar seus nós usando outros serviços do Azure.

Na visualização pública, os cenários a seguir têm suporte para **computadores conectados**.

## <a name="guest-configuration"></a>Configuração de Convidado

Depois de conectar o computador ao Azure, você pode atribuir políticas do Azure a **computadores conectados** usando a mesma experiência que a atribuição de política para máquinas virtuais do Azure.

Para obter mais informações, consulte [entender a configuração de convidado de Azure Policy](../../governance/policy/concepts/guest-configuration.md).

Os logs do agente de configuração do convidado para um **computador conectado** estão nos seguintes locais:

* Windows - `%ProgramFiles%\AzureConnectedMachineAgent\logs\dsc.log`
* Linux:-`/opt/logs/dsc.log`

## <a name="log-analytics"></a>Log Analytics

Os dados de log coletados pelo [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) e armazenados no espaço de trabalho do log Analytics agora conterão propriedades específicas para o computador, como **ResourceId**, que pode ser usado para o acesso ao log centrado no recurso.

- Os computadores que já têm o agente MMA instalado terão a funcionalidade de **arco do Azure** habilitada por pacotes de gerenciamento atualizados.
- O [agente MMA versão 10.20.18011 ou superior](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows#agent-and-vm-extension-version) é necessário para a integração do Azure ARC para servidores.
- Ao consultar dados de log em [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview), o esquema de dados retornado conterá o **ResourceId** híbrido no formato `/subscriptions/<SubscriptionId/resourceGroups/<ResourceGroup>/providers/Microsoft.HybridCompute/machines/<MachineName>`.

Para obter mais informações, consulte Introdução [ao log Analytics no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

<!-- MMA agent version 10.20.18011 and later -->

## <a name="next-steps"></a>Passos Seguintes

Há dois métodos para conectar computadores usando o Azure ARC para servidores.

* **Interativamente** -siga o [início rápido do portal](quickstart-onboard-portal.md) para gerar um script do portal e executá-lo no computador. Essa é a melhor opção se você estiver conectando uma máquina por vez.
* **Em escala** – siga o [início rápido do PowerShell](quickstart-onboard-powershell.md) para criar uma entidade de serviço para conectar computadores de forma não interativa.
