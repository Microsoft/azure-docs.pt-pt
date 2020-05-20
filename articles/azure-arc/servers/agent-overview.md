---
title: Visão geral do agente Connected Machine Windows
description: Este artigo fornece uma visão detalhada do Azure Arc para o agente de servidores disponível que suporta maquinador de monitorização de máquinas virtuais hospedadas em ambientes híbridos.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 05/18/2020
ms.topic: conceptual
ms.openlocfilehash: 4dbc559e62523a1ea17236a9e8c9666ef48bba33
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664152"
---
# <a name="overview-of-azure-arc-for-servers-agent"></a>Visão geral do Azure Arc para agente de servidores

O Agente Azure Arc para servidores Connected Machine permite-lhe gerir as suas máquinas Windows e Linux alojadas fora do Azure na sua rede corporativa ou noutro fornecedor de nuvem. Este artigo fornece uma visão geral detalhada dos requisitos do agente, do sistema e da rede, bem como dos diferentes métodos de implementação.

## <a name="download-agents"></a>Descarregar agentes

Pode descarregar o pacote de agente Azure Connected Machine para Windows e Linux a partir dos locais listados abaixo.

- Pacote de [instalação do Windows Agent Windows](https://aka.ms/AzureConnectedMachineAgent) do Microsoft Download Center.
- O pacote de agente Linux é distribuído a partir do [repositório](https://packages.microsoft.com/) de pacotes da Microsoft utilizando o formato de pacote preferido para a distribuição (. RPM ou . DEB).

>[!NOTE]
>Durante esta pré-visualização, apenas foi lançado um pacote, que é adequado para Ubuntu 16.04 ou 18.04.

O agente Azure Connected Machine para Windows e Linux pode ser atualizado para a mais recente versão manual ou automaticamente, dependendo dos seus requisitos. Para mais informações, consulte [aqui.](manage-agent.md)

## <a name="windows-agent-installation-details"></a>Detalhes da instalação do agente Windows

O agente Máquina Conectada para Windows pode ser instalado utilizando um dos três métodos seguintes:

* Clique duas vezes no ficheiro `AzureConnectedMachineAgent.msi` .
* Manualmente, executando a embalagem do Instalador do Windows `AzureConnectedMachineAgent.msi` a partir da concha command.
* A partir de uma sessão powerShell usando um método scriptd.

Depois de instalar o agente Connected Machine para windows, aplicam-se as seguintes alterações adicionais de configuração a nível do sistema.

* As seguintes pastas de instalação são criadas durante a configuração.

    |Pasta |Descrição |
    |-------|------------|
    |C:\Ficheiros do programa\AzureConnectedMachineAgent |Trajetória de instalação predefinida contendo os ficheiros de suporte do agente.|
    |%ProgramData%\AzureConnectedMachineAgent |Contém os ficheiros de configuração do agente.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Contém as fichas adquiridas.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Contém o ficheiro de configuração do agente que regista as suas informações de `agentconfig.json` registo com o serviço.|
    |%ProgramaData%\GuestConfig |Contém os ficheiros relacionados com as políticas (aplicadas) do Azure.|

* Os seguintes serviços Windows são criados na máquina-alvo durante a instalação do agente.

    |Nome do Serviço |Nome a apresentar |Nome do processo |Descrição |
    |-------------|-------------|-------------|------------|
    |himds |Serviço de Metadados de Instância Híbrida Azure |himds.exe |Este serviço implementa o serviço de metadados de instância seletiva (IMDS) para rastrear a máquina.|
    |DScService |Serviço de Configuração de Hóspedes |dsc_service.exe |Esta é a base de código de configuração do Estado Desejado (DSC v2) utilizada dentro do Azure para implementar a Política de Hóspedes.|

* As seguintes variáveis ambientais são criadas durante a instalação do agente.

    |Name |Valor predefinido |Descrição |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||
    
* Existem quatro ficheiros de registo disponíveis para resolução de problemas. São descritos na tabela seguinte.

    |Registar |Descrição |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Regista detalhes do serviço de agentes (himds) e interação com o Azure.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Contém a saída dos comandos da ferramenta azcmagent, quando o argumento verboso (-v) é usado.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |Regista detalhes da atividade de serviço da DSC,<br> em particular a conectividade entre o serviço de manadas e a Política Azure.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |Regista detalhes sobre telemetria de serviço DSC e registo de verbosa.|

* O grupo de segurança local **Hybrid aplicações** de extensão de agente híbrido é criado. 

* Durante a desinstalação do agente, os seguintes artefactos não são removidos.

    * C:\Ficheiros do programa\AzureConnectedMachineAgent\Logs
    * %ProgramData%\AzureConnectedMachineAgent e subdiretórios
    * %ProgramaData%\GuestConfig

## <a name="linux-agent-installation-details"></a>Detalhes da instalação do agente Linux

O agente Máquina Conectada para Linux é fornecido no formato de pacote preferido para a distribuição (. RPM ou . DEB) que está hospedado no [repositório](https://packages.microsoft.com/)de pacotes da Microsoft . O agente é instalado e configurado com o feixe de script [shell Install_linux_azcmagent.sh](https://aka.ms/azcmagent). 

Após a instalação do agente Máquina Conectada para o Linux, aplicam-se as seguintes alterações adicionais de configuração em todo o sistema.

* As seguintes pastas de instalação são criadas durante a configuração.

    |Pasta |Descrição |
    |-------|------------|
    |/var/opt/azcmagent/ |Trajetória de instalação predefinida contendo os ficheiros de suporte do agente.|
    |/opt/azcmagent/ |
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |Contém as fichas adquiridas.|
    |/var/lib/GuestConfig |Contém os ficheiros relacionados com as políticas (aplicadas) do Azure.|

* Os seguintes daemons são criados na máquina-alvo durante a instalação do agente.

    |Nome do Serviço |Nome a apresentar |Nome do processo |Descrição |
    |-------------|-------------|-------------|------------|
    |himdsd.serviço |Serviço de Metadados de Instância Híbrida Azure |/opt/azcmagent/bin/himds |Este serviço implementa o serviço de metadados de instância seletiva (IMDS) para rastrear a máquina.|
    |dscd.serviço |Serviço de Configuração de Hóspedes |/opt/DSC/dsc_linux_service |Esta é a base de código de configuração do Estado Desejado (DSC v2) utilizada dentro do Azure para implementar a Política de Hóspedes.|

* Existem quatro ficheiros de registo disponíveis para resolução de problemas. São descritos na tabela seguinte.

    |Registar |Descrição |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Regista detalhes do serviço de agentes (himds) e interação com o Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Contém a saída dos comandos da ferramenta azcmagent, quando o argumento verboso (-v) é usado.|
    |/opt/logs/dsc.log |Regista detalhes da atividade de serviço da DSC,<br> em particular a conectividade entre o serviço de manadas e a Política Azure.|
    |/opt/logs/dsc.telemettry.txt |Regista detalhes sobre telemetria de serviço DSC e registo de verbosa.|

* As seguintes variáveis ambientais são criadas durante a instalação do agente. Estas variáveis são definidas `/lib/systemd/system.conf.d/azcmagent.conf` em .

    |Name |Valor predefinido |Descrição |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Durante a desinstalação do agente, os seguintes artefactos não são removidos.

    * /var/opt/azcmagent
    * /opt/registos

## <a name="prerequisites"></a>Pré-requisitos

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

As seguintes versões do sistema operativo Windows e Linux são oficialmente suportadas para o agente Azure Connected Machine: 

- Windows Server 2012 R2 e superior (incluindo o Núcleo do Servidor do Windows)
- Ubuntu 16.04 e 18.04
- CentOS Linux 7
- SUSE Linux Enterprise Server (SLES) 15
- Red Hat Enterprise Linux (RHEL) 7
- Amazon Linux 2

>[!NOTE]
>Esta versão de pré-visualização do agente Connected Machine para windows apenas suporta o Windows Server configurado para utilizar o idioma inglês.
>

### <a name="required-permissions"></a>Permissões obrigatórias

- Para máquinas a bordo, você é um membro da função **de onboarding azure connected machine.**

- Para ler, modificar, reembarcar e apagar uma máquina, é membro da função de Administrador de **Recursos de Máquinas Conectadas Azure.** 

### <a name="azure-subscription-and-service-limits"></a>Limites de subscrição e serviço do Azure

Antes de configurar as suas máquinas com o Azure Arc para servidores (pré-visualização), deverá rever os [limites](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) de subscrição do Gestor de Recursos do Azure e [os limites do grupo](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) de recursos para planear o número de máquinas a ligar.

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2

Para garantir a segurança dos dados em trânsito para o Azure, encorajamo-lo vivamente a configurar a máquina para utilizar a Transport Layer Security (TLS) 1.2. As versões mais antigas da Camada de Tomadas TLS/Secure (SSL) revelaram-se vulneráveis e, embora ainda trabalhem para permitir a retrocompatibilidade, não são **recomendadas**. 

|Plataforma/Idioma | Suporte | Mais Informações |
| --- | --- | --- |
|Linux | As distribuições linux tendem a depender do [OpenSSL](https://www.openssl.org) para suporte TLS 1.2. | Verifique o [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) para confirmar que a sua versão do OpenSSL está suportada.|
| Windows Server 2012 R2 e superior | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinidas](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).|

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
|*.his.arc.azure.com|Serviço de Identidade Híbrida|

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


## <a name="installation-and-configuration"></a>Instalação e configuração

As máquinas de ligação no seu ambiente híbrido diretamente com o Azure podem ser realizadas utilizando diferentes métodos dependendo dos seus requisitos. A tabela seguinte destaca cada método para determinar qual o melhor funciona para a sua organização.

| Método | Descrição |
|--------|-------------|
| Interativamente | Instale manualmente o agente num único ou pequeno número de máquinas seguindo os passos em [Connect machines a partir do portal Azure](onboard-portal.md).<br> A partir do portal Azure, pode gerar um script e executá-lo na máquina para automatizar os passos de instalação e configuração do agente.|
| À escala | Instale e configure o agente para várias máquinas que seguem as [máquinas Connect utilizando um diretor de serviço](onboard-service-principal.md).<br> Este método cria um diretor de serviço para ligar as máquinas de forma não interativa.|
| À escala | Instale e configure o agente para várias máquinas seguindo o método Utilizando o [DSC](onboard-dsc.md)do Windows PowerShell .<br> Este método utiliza um diretor de serviço para ligar as máquinas não interactivamente ao PowerShell DSC. |

## <a name="next-steps"></a>Passos seguintes

Para começar a avaliar o Azure Arc para servidores (pré-visualização), siga o artigo [Ligar máquinas híbridas a Azure a partir do portal Azure](onboard-portal.md).
