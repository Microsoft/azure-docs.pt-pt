---
title: Visão geral do agente Windows da máquina conectada
description: Este artigo fornece uma visão detalhada do agente de servidores ativado Azure Arc disponível, que suporta a monitorização de máquinas virtuais hospedadas em ambientes híbridos.
ms.date: 02/18/2021
ms.topic: conceptual
ms.openlocfilehash: ebd9412849b4a0b3081e892d7472e598ca6e8365
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101651098"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Visão geral do agente de servidores ativado pelo Azure Arc

O agente Azure Arc ativado por máquinas conectadas permite-lhe gerir as suas máquinas Windows e Linux hospedadas fora do Azure na sua rede corporativa ou noutro fornecedor de nuvem. Este artigo fornece uma visão geral detalhada dos requisitos do agente, do sistema e da rede, bem como dos diferentes métodos de implantação.

>[!NOTE]
>Começando com o lançamento geral dos servidores Azure Arc ativados em setembro de 2020, todas as versões pré-lançamento do agente Azure Connected Machine (agentes com versões inferiores a 1.0) estão a ser **depreciadas** até **2 de fevereiro de 2021**.  Este prazo permite-lhe fazer upgrade para a versão 1.0 ou superior antes que os agentes pré-lançados deixem de poder comunicar com o serviço de servidores ativados Azure Arc.

## <a name="agent-component-details"></a>Detalhes do componente do agente

O pacote de agente da máquina conectada Azure contém vários componentes lógicos, que estão agregados.

* O serviço de metadados de identificação de exemplo híbrido (HIMDS) gere a ligação ao Azure e à identidade Azure da máquina ligada.

* O agente de Configuração de Hóspedes fornece In-Guest funcionalidade de Política e Configuração de Hóspedes, tais como avaliar se a máquina está em conformidade com as políticas exigidas.

    Note o seguinte comportamento com a [configuração do hóspede Azure](../../governance/policy/concepts/guest-configuration.md) Policy para uma máquina desligada:

    * Uma atribuição de política de configuração de hóspedes que direcione máquinas desligadas não é afetada.
    * A atribuição de hóspedes é armazenada localmente por 14 dias. Dentro do período de 14 dias, se o agente 'Máquina Conectada' voltar a ligar-se ao serviço, as atribuições de política são reaplicadas.
    * As atribuições são eliminadas após 14 dias e não são transferidas para a máquina após o período de 14 dias.

* O agente de extensão gere extensões VM, incluindo instalação, desinstalação e atualização. As extensões são descarregadas a partir do Azure e copiadas para a `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` pasta no Windows, e para o Linux. `/opt/GC_Ext/downloads` No Windows, a extensão é instalada para o seguinte caminho `%SystemDrive%\Packages\Plugins\<extension>` , e no Linux a extensão é instalada para `/var/lib/waagent/<extension>` .

## <a name="instance-metadata"></a>Metadados de exemplo

As informações sobre os metadados sobre a máquina conectada são recolhidas após o agente 'Máquina Conectada' registar-se com servidores ativados pelo Arc. Especificamente:

* Nome, tipo e versão do sistema operativo
* Nome do computador
* Nome de domínio totalmente qualificado do computador (FQDN)
* Versão do agente da máquina conectada
* Diretório Ativo e Nome de domínio totalmente qualificado (FQDN)
* UUID (ID BIO)
* Batimento cardíaco do agente da máquina conectado
* Versão do agente da máquina conectada
* Chave pública para a identidade gerida
* Estado de conformidade com a política e detalhes (se utilizar as políticas de configuração do hóspede da Azure Policy)

As seguintes informações de metadados são solicitadas pelo agente à Azure:

* Localização de recursos (região)
* ID de máquina virtual
* Etiquetas
* Certificado de identidade gerido Azure Ative
* Atribuições de política de configuração de hóspedes
* Pedidos de extensão - instale, atualize e elimine.

## <a name="download-agents"></a>Agentes de descarregamento

Pode descarregar o pacote de agentes Azure Connected Machine para Windows e Linux a partir das localizações listadas abaixo.

* [Pacote do Windows Agent Windows Installer](https://aka.ms/AzureConnectedMachineAgent) do Microsoft Download Center.

* O pacote de agente Linux é distribuído a partir do [repositório](https://packages.microsoft.com/) de pacotes da Microsoft utilizando o formato de pacote preferido para a distribuição (. RPM ou . DEB).

O agente Azure Connected Machine para Windows e Linux pode ser atualizado para a versão mais recente manualmente ou automaticamente, dependendo dos seus requisitos. Para mais informações, consulte [aqui.](manage-agent.md)

## <a name="prerequisites"></a>Pré-requisitos

### <a name="supported-environments"></a>Ambientes apoiados

Os servidores ativados pelo Arco suportam a instalação do agente 'Máquina Conectada' em qualquer servidor físico e máquina virtual hospedada *fora* do Azure. Isto inclui máquinas virtuais em plataformas como VMware, Azure Stack HCI e outros ambientes em nuvem. Os servidores ativados pelo Arco não suportam a instalação do agente em máquinas virtuais em funcionamento em Azure, ou máquinas virtuais que executam no Azure Stack Hub ou no Azure Stack Edge, uma vez que já estão modelados como VMs Azure.

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

As seguintes versões do sistema operativo Windows e Linux são oficialmente suportadas para o agente Azure Connected Machine:

- Windows Server 2008 R2, Windows Server 2012 R2 e superior (incluindo Server Core)
- Ubuntu 16.04 e 18.04 LTS (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)
- Oráculo Linux 7

> [!WARNING]
> O nome de hospedeiro Linux ou nome do computador Windows não pode utilizar uma das palavras ou marcas registadas reservadas no nome, caso contrário, tentar registar a máquina conectada com a Azure falhará. Consulte [os erros de nome de recursos reservados](../../azure-resource-manager/templates/error-reserved-resource-name.md) resolvem uma lista das palavras reservadas.

### <a name="required-permissions"></a>Permissões obrigatórias

* Para máquinas a bordo, você é membro da **função de azure connected machine onboarding.**

* Para ler, modificar e eliminar uma máquina, é membro da função de Administrador de **Recursos da Máquina Azure.** 

### <a name="azure-subscription-and-service-limits"></a>Subscrição do Azure e limites do serviço

Antes de configurar as suas máquinas com servidores ativados pelo Azure Arc, reveja os [limites de subscrição](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) do Gestor de Recursos Azure e [limites](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) de grupo de recursos para planear o número de máquinas a serem ligadas.

Os servidores ativados Azure Arc suportam até 5.000 casos de máquinas num grupo de recursos.

### <a name="transport-layer-security-12-protocol"></a>Protocolo de Segurança da Camada de Transporte 1.2

Para garantir a segurança dos dados em trânsito para Azure, encorajamo-lo a configurar a máquina para utilizar a Segurança da Camada de Transporte (TLS) 1.2. Versões mais antigas da camada de tomadas TLS/Secure Sockets (SSL) foram consideradas vulneráveis e, embora ainda atualmente trabalhem para permitir retrocompatibilidade, não são **recomendadas**.

|Plataforma/Idioma | Suporte | Mais Informações |
| --- | --- | --- |
|Linux | As distribuições linux tendem a contar com [o suporte OpenSSL](https://www.openssl.org) para suporte TLS 1.2. | Verifique o [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) para confirmar que a sua versão do OpenSSL está suportada.|
| Windows Server 2012 R2 e superior | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinições](/windows-server/security/tls/tls-registry-settings).|

### <a name="networking-configuration"></a>Configuração da rede

O agente de máquinas conectadas para Linux e Windows comunica de saída de forma segura ao Arco Azure sobre a porta TCP 443. Se a máquina ligar através de uma firewall ou servidor proxy para comunicar através da Internet, reveja o seguinte para entender os requisitos de configuração da rede.

> [!NOTE]
> Os servidores ativados pelo Arco não suportam a utilização de um [gateway Log Analytics](../../azure-monitor/agents/gateway.md) como procuração para o agente 'Máquina Conectada'.
>

Se a conectividade de saída for restringida pela sua firewall ou servidor proxy, certifique-se de que os URLs listados abaixo não estão bloqueados. Quando apenas permite os intervalos IP ou nomes de domínio necessários para que o agente comunique com o serviço, tem de permitir o acesso às seguintes Tags de Serviço e URLs.

Etiquetas de serviço:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* Estrutura AzureArcInfra

URLs:

| Recursos do agente | Descrição |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |Configuração de Convidado|
|`*.his.arc.azure.com`|Serviço de Identidade Híbrida|
|`www.office.com`|Office 365|

Os agentes de pré-visualização (versão 0.11 e inferior) também requerem acesso aos seguintes URLs:

| Recursos do agente | Descrição |
|---------|---------|
|`agentserviceapi.azure-automation.net`|Configuração de Convidado|
|`*-agentservice-prod-1.azure-automation.net`|Configuração de Convidado|

Para obter uma lista de endereços IP para cada tag/região de serviço, consulte o ficheiro JSON - [Gamas IP Azure e Tags de Serviço – Nuvem Pública.](https://www.microsoft.com/download/details.aspx?id=56519) A Microsoft publica atualizações semanais contendo cada Serviço Azure e as gamas IP que utiliza. Esta informação no ficheiro JSON é a lista atual de pontos a tempo das gamas IP que correspondem a cada tag de serviço. Os endereços IP estão sujeitos a alterações. Se forem necessários intervalos de endereços IP para a sua configuração de firewall, então a Tag de Serviço **AzureCloud** deve ser utilizada para permitir o acesso a todos os serviços Azure. Não desative a monitorização de segurança ou a inspeção destes URLs, permitindo-os como se fosse outro tráfego de Internet.

Para mais informações, [reveja as etiquetas de serviço.](../../virtual-network/service-tags-overview.md)

### <a name="register-azure-resource-providers"></a>Registar fornecedores de recursos Azure

Os servidores ativados Azure Arc dependem dos seguintes fornecedores de recursos Azure na sua subscrição para utilizar este serviço:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Se não estiverem registados, pode registá-los utilizando os seguintes comandos:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Também pode registar os fornecedores de recursos no portal Azure seguindo os passos no [portal Azure](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="installation-and-configuration"></a>Instalação e configuração

As máquinas de ligação no seu ambiente híbrido diretamente com o Azure podem ser realizadas utilizando métodos diferentes, dependendo dos seus requisitos. A tabela a seguir destaca cada método para determinar qual o melhor funciona para a sua organização.

> [!IMPORTANT]
> O agente 'Máquina Conectada' não pode ser instalado numa máquina virtual Azure Windows. Se tentar, a instalação deteta-o e recua.

| Método | Descrição |
|--------|-------------|
| Interativamente | Instale manualmente o agente num único ou pequeno número de máquinas seguindo os passos das [máquinas Connect a partir do portal Azure](onboard-portal.md).<br> A partir do portal Azure, pode gerar um script e executá-lo na máquina para automatizar os passos de instalação e configuração do agente.|
| Em escala | Instale e configuure o agente para várias máquinas que seguem as [máquinas Connect utilizando um Diretor de Serviço](onboard-service-principal.md).<br> Este método cria um principal de serviço para ligar máquinas não interativamente.|
| Em escala | Instale e configuure o agente para várias máquinas seguindo o método Utilizando o [Windows PowerShell DSC](onboard-dsc.md).<br> Este método utiliza um principal de serviço para ligar máquinas não interativamente com o PowerShell DSC. |

## <a name="connected-machine-agent-technical-overview"></a>Visão geral técnica do agente da máquina conectada

### <a name="windows-agent-installation-details"></a>Detalhes da instalação do agente do Windows

O agente da Máquina Conectada para Windows pode ser instalado utilizando um dos três métodos seguintes:

* Clique duas vezes no `AzureConnectedMachineAgent.msi` ficheiro.
* Manualmente, executando o pacote do Instalador do Windows `AzureConnectedMachineAgent.msi` a partir da concha do Comando.
* A partir de uma sessão PowerShell usando um método scripted.

Após a instalação do agente 'Máquina Conectada' para o Windows, aplicam-se as seguintes alterações de configuração a nível do sistema.

* As seguintes pastas de instalação são criadas durante a instalação.

    |Pasta |Descrição |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Caminho de instalação predefinido que contenha os ficheiros de suporte do agente.|
    |%ProgramData%\AzureConnectedMachineAgent |Contém os ficheiros de configuração do agente.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Contém os tokens adquiridos.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Contém o ficheiro de configuração do agente `agentconfig.json` que regista as suas informações de registo com o serviço.|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | Caminho de instalação que contém os ficheiros do agente de configuração do hóspede. |
    |%ProgramData%\GuestConfig |Contém as políticas (aplicadas) do Azure.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | As extensões são descarregadas a partir de Azure e copiadas aqui.|

* Os seguintes serviços Windows são criados na máquina-alvo durante a instalação do agente.

    |Nome do serviço |Nome a apresentar |Nome do processo |Descrição |
    |-------------|-------------|-------------|------------|
    |osds |Serviço de Metadados de Caso Híbrido Azure |osds |Este serviço implementa o serviço de metadados Azure Instance (IMDS) para gerir a ligação ao Azure e a identidade Azure da máquina conectada.|
    |GCArcService |Serviço de Arco de Configuração de Hóspedes |gc_service |Monitoriza a configuração estatal desejada da máquina.|
    |Serviço de Extensão |Serviço de extensão de configuração do hóspede | gc_service |Instala as extensões necessárias para a máquina.|

* As seguintes variáveis ambientais são criadas durante a instalação do agente.

    |Name |Valor predefinido |Descrição |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Existem vários ficheiros de registo disponíveis para resolução de problemas. São descritos na tabela seguinte.

    |Registo |Descrição |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Regista detalhes do serviço e interação dos agentes (HIMDS) com o Azure.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Contém a saída dos comandos da ferramenta azcmagent, quando o argumento verboso (v) é utilizado.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |Regista detalhes da atividade de serviço da DSC,<br> em particular, a conectividade entre o serviço HIMDS e a Política Azure.|
    |%ProgramaData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |Grava detalhes sobre telemetria de serviço DSC e registo verboso.|
    |%ProgramData%\GuestConfig\ext_mgr_logs|Grava detalhes sobre o componente do agente de extensão.|
    |%ProgramData%\GuestConfig\extension_logs\<Extension>|Grava detalhes da extensão instalada.|

* O grupo de segurança local **Aplicações de extensão de agente híbrido são criadas.**

* Durante a desinstalação do agente, os seguintes artefactos não são removidos.

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent e subdiretórios
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Detalhes da instalação do agente Linux

O agente da Máquina Conectada para o Linux é fornecido no formato de pacote preferido para a distribuição (. RPM ou . DEB) que está hospedado no [repositório](https://packages.microsoft.com/)de pacotes da Microsoft. O agente está instalado e configurado com o pacote de scripts da concha [Install_linux_azcmagent.sh](https://aka.ms/azcmagent).

Após a instalação do agente 'Máquina Conectada' para o Linux, aplicam-se as seguintes alterações de configuração a nível do sistema.

* As seguintes pastas de instalação são criadas durante a instalação.

    |Pasta |Descrição |
    |-------|------------|
    |/var/opt/azcmagent/ |Caminho de instalação predefinido que contenha os ficheiros de suporte do agente.|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | Caminho de instalação que contém os ficheiros do agente de configuração do hóspede.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |Contém os tokens adquiridos.|
    |/var/lib/GuestConfig |Contém as políticas (aplicadas) do Azure.|
    |/opt/GC_Ext/downloads|As extensões são descarregadas a partir de Azure e copiadas aqui.|

* Os seguintes daemons são criados na máquina-alvo durante a instalação do agente.

    |Nome do serviço |Nome a apresentar |Nome do processo |Descrição |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Serviço de agente de máquinas conectado Azure |osds |Este serviço implementa o serviço de metadados Azure Instance (IMDS) para gerir a ligação ao Azure e a identidade Azure da máquina conectada.|
    |gcad.servce |Serviço GC Arc |gc_linux_service |Monitoriza a configuração estatal desejada da máquina. |
    |extd.service |Serviço de Extensão |gc_linux_service | Instala as extensões necessárias para a máquina.|

* Existem vários ficheiros de registo disponíveis para resolução de problemas. São descritos na tabela seguinte.

    |Registo |Descrição |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Regista detalhes do serviço e interação dos agentes (HIMDS) com o Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Contém a saída dos comandos da ferramenta azcmagent, quando o argumento verboso (v) é utilizado.|
    |/opt/logs/dsc.log |Regista detalhes da atividade de serviço da DSC,<br> em particular a conectividade entre o serviço de himas e a Política Azure.|
    |/opt/logs/dsc.telemetry.txt |Grava detalhes sobre telemetria de serviço DSC e registo verboso.|
    |/var/lib/GuestConfig/ext_mgr_logs |Grava detalhes sobre o componente do agente de extensão.|
    |/var/lib/GuestConfig/extension_logs|Grava detalhes da extensão instalada.|

* As seguintes variáveis ambientais são criadas durante a instalação do agente. Estas variáveis estão definidas em `/lib/systemd/system.conf.d/azcmagent.conf` .

    |Name |Valor predefinido |Descrição |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Durante a desinstalação do agente, os seguintes artefactos não são removidos.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>Passos seguintes

* Para começar a avaliar os servidores ativados do Azure Arc, siga o artigo [Ligue as máquinas híbridas ao Azure a partir do portal Azure](onboard-portal.md).

* As informações relativas à resolução de problemas podem ser encontradas no guia do [agente da máquina conectado de resolução de problemas.](troubleshoot-agent-onboard.md)
