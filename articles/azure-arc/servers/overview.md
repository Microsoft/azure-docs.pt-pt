---
title: Azure Arc para servidores (pré-visualização) Visão geral
description: Aprenda a usar o Azure Arc para servidores para gerir máquinas que estão hospedadas fora do Azure como se fosse um recurso Azure.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: automação azul, DSC, powershell, configuração de estado desejada, gestão de atualizações, rastreio de alterações, inventário, livros de execução, pitão, gráfico, híbrido
ms.date: 02/03/2020
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: c38bc2d5d9a7e23c0bb3041a5cb3b5cd1da9adb1
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114234"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>O que é O Arco Azure para servidores (pré-visualização)

O Azure Arc para servidores (pré-visualização) permite-lhe gerir as suas máquinas Windows e Linux alojadas fora do Azure na sua rede corporativa ou outro fornecedor de nuvem, à semelhança da forma como gere as máquinas virtuais nativas do Azure. Quando uma máquina híbrida está ligada ao Azure, torna-se uma máquina conectada e é tratada como um recurso em Azure. Cada máquina conectada tem um ID de recursos, é gerida como parte de um grupo de recursos dentro de uma subscrição, e beneficia de construções padrão do Azure, como a Política Azure e etiquetas de aplicação.

Para oferecer esta experiência com as suas máquinas híbridas alojadas fora do Azure, o agente Azure Connected Machine precisa de ser instalado em cada máquina que planeia ligar ao Azure. Este agente não fornece qualquer outra funcionalidade e não substitui o agente Azure [Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando pretende monitorizar proativamente o SISTEMA e as cargas de trabalho em funcionamento na máquina, geri-la utilizando livros de execução automation ou soluções como a Update Management, ou utilizar outros serviços Azure como o [Azure Security Center.](../../security-center/security-center-intro.md)

>[!NOTE]
>Este lançamento de pré-visualização destina-se a fins de avaliação e recomendamos que não gere máquinas de produção críticas.
>

## <a name="supported-scenarios"></a>Cenários suportados

O Azure Arc para servidores (pré-visualização) suporta os seguintes cenários com máquinas conectadas:

- Atribuir [configurações de hóspedes da Política Azure](../../governance/policy/concepts/guest-configuration.md) utilizando a mesma experiência que a atribuição de políticas para máquinas virtuais Azure.
- Os dados de registo recolhidos pelo agente Log Analytics e armazenados no espaço de trabalho do Log Analytics com os quais a máquina está registada agora contêm propriedades específicas da máquina, como o ID de recursos, que podem ser usados para apoiar o acesso ao registo [de contexto de recursos.](../../azure-monitor/platform/design-logs-deployment.md#access-mode)

## <a name="supported-regions"></a>Regiões suportadas

Com o Azure Arc para servidores (pré-visualização), apenas algumas regiões são suportadas:

- WestUS2
- Europa Ocidental
- Ásia Ocidental

## <a name="prerequisites"></a>Pré-requisitos

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

As seguintes versões do sistema operativo Windows e Linux são oficialmente suportadas para o agente Azure Connected Machine: 

- Windows Server 2012 R2 e superior
- Ubuntu 16.04 e 18.04

>[!NOTE]
>Esta versão de pré-visualização do agente Connected Machine para windows apenas suporta o Windows Server configurado para utilizar o idioma inglês.
>

### <a name="required-permissions"></a>Permissões obrigatórias

- Para máquinas a bordo, você é um membro da função **de onboarding azure connected machine.**

- Para ler, modificar, reembarcar e apagar uma máquina, é membro da função de Administrador de **Recursos de Máquinas Conectadas Azure.** 

### <a name="azure-subscription-and-service-limits"></a>Limites de subscrição e serviço do Azure

Antes de configurar as suas máquinas com o Azure Arc para servidores (pré-visualização), deverá rever os [limites](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits---azure-resource-manager) de subscrição do Gestor de Recursos do Azure e [os limites do grupo](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) de recursos para planear o número de máquinas a ligar.

### <a name="networking-configuration"></a>Configuração de rede

O agente Connected Machine para Linux e Windows comunica de forma segura a Azure Arc sobre a porta TCP 443. Se a máquina ligar através de uma firewall ou servidor proxy para comunicar através da Internet, reveja os requisitos abaixo para entender os requisitos de configuração da rede.

Se a conectividade de saída for restringida pela sua firewall ou servidor proxy, certifique-se de que os URLs listados abaixo não estão bloqueados. Se permitir apenas os intervalos IP ou nomes de domínio necessários para que o agente se comunique com o serviço, também deve permitir o acesso às seguintes Etiquetas de Serviço e URLs.

Etiquetas de serviço:

- AzureActiveDirectory
- AzureTrafficManager

URLs:

| Recursos do agente | Descrição |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Configuração de Convidado|
|*-agentservice-prod-1.azure-automation.net|Configuração de Convidado|
|*.his.hybridcompute.azure-automation.net|Serviço de Identidade Híbrida|

Para obter uma lista de endereços IP para cada etiqueta/região de serviço, consulte o ficheiro JSON - [Gamas Ip Azure e etiquetas](https://www.microsoft.com/download/details.aspx?id=56519)de serviço – Nuvem Pública . A Microsoft publica atualizações semanais contendo cada Serviço Azure e as gamas IP que utiliza. Para mais informações, reveja [as etiquetas de serviço](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Os URLs na tabela anterior são necessários para além da informação de endereço IP do etiqueta de serviço, uma vez que a maioria dos serviços não tem atualmente um registo de etiqueta de serviço. Como tal, os endereços IP estão sujeitos a alterações. Se forem necessárias gamas de endereços IP para a configuração da sua firewall, a etiqueta de serviço **AzureCloud** deve ser utilizada para permitir o acesso a todos os serviços Azure. Não desative a monitorização ou inspeção de segurança destes URLs, permita-os como faria com outros tráfegos de Internet.

### <a name="register-azure-resource-providers"></a>Registe os fornecedores de recursos da Azure

O Azure Arc para servidores (pré-visualização) depende dos seguintes fornecedores de recursos Azure na sua subscrição, a fim de utilizar este serviço:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Se não estiverem registados, pode registá-los utilizando os seguintes comandos:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Também pode registar os fornecedores de recursos no portal Azure seguindo os passos sob o [portal Azure.](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)

## <a name="connected-machine-agent"></a>Agente máquina conectada

Pode descarregar o pacote De agente de máquinas conectadas Azure para Windows e Linux a partir dos locais listados abaixo.

- Pacote de [instalação do Windows Agent Windows](https://aka.ms/AzureConnectedMachineAgent) do Microsoft Download Center.
- O pacote de agente Linux é distribuído a partir do [repositório](https://packages.microsoft.com/) de pacotes da Microsoft utilizando o formato de pacote preferido para a distribuição (. RPM ou . DEB).

>[!NOTE]
>Durante esta pré-visualização, apenas foi lançado um pacote, que é adequado para Ubuntu 16.04 ou 18.04.

## <a name="install-and-configure-agent"></a>Instalar e configurar o agente

As máquinas de ligação no seu ambiente híbrido diretamente com o Azure podem ser realizadas utilizando diferentes métodos dependendo dos seus requisitos. A tabela seguinte destaca cada método para determinar qual o melhor funciona para a sua organização.

| Método | Descrição |
|--------|-------------|
| Interativamente | Instale manualmente o agente num único ou pequeno número de máquinas seguindo os passos em [Connect machines a partir do portal Azure](quickstart-onboard-portal.md).<br> A partir do portal Azure, pode gerar um script e executá-lo na máquina para automatizar os passos de instalação e configuração do agente.|
| À escala | Instale e configure o agente para várias máquinas que seguem as [máquinas Connect utilizando um diretor de serviço](quickstart-onboard-powershell.md).<br> Este método cria um diretor de serviço para ligar as máquinas de forma não interativa.|


## <a name="next-steps"></a>Passos seguintes

- Para começar a avaliar o Azure Arc para servidores (pré-visualização), siga o artigo [Ligar máquinas híbridas a Azure a partir do portal Azure](quickstart-onboard-portal.md). 