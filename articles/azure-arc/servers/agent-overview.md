---
title: Visão geral do agente Windows da máquina conectada
description: Este artigo fornece uma visão detalhada do Arco Azure para o agente de servidores disponível que suporta a monitorização de máquinas virtuais hospedadas em ambientes híbridos.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 07/09/2020
ms.topic: conceptual
ms.openlocfilehash: ed95b902c2c0768f50a0c6dadbfc617292932c2b
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242955"
---
# <a name="overview-of-azure-arc-for-servers-agent"></a>Visão geral do Azure Arc para agente de servidores

O Azure Arc para servidores Connected Machine agent permite-lhe gerir as suas máquinas Windows e Linux hospedadas fora do Azure na sua rede corporativa ou noutro fornecedor de nuvem. Este artigo fornece uma visão geral detalhada dos requisitos do agente, do sistema e da rede, bem como dos diferentes métodos de implantação.

## <a name="agent-component-details"></a>Detalhes do componente do agente

O pacote de agente da máquina conectada Azure contém vários componentes lógicos que estão agregados.

* O serviço de metadados de identificação de exemplo híbrido (HIMDS) gere a ligação ao Azure e à identidade Azure da máquina ligada.

* O agente de Configuração de Hóspedes fornece a funcionalidade de Política de Hóspedes e Configuração de Hóspedes, tais como avaliar se a máquina está em conformidade com as políticas exigidas.

    Note o seguinte comportamento com a [configuração do hóspede Azure](../../governance/policy/concepts/guest-configuration.md) Policy para uma máquina desligada:

    * Uma atribuição de política de configuração de hóspedes que direcione máquinas desligadas não é afetada.
    * A atribuição de hóspedes é armazenada localmente por 14 dias. Dentro do período de 14 dias, se o agente 'Máquina Conectada' voltar a ligar-se ao serviço, as atribuições de política são reaplicadas.
    * As atribuições são eliminadas após 14 dias e não são transferidas para a máquina após o período de 14 dias.

* O agente de extensão gere extensões VM, incluindo instalação, desinstalação e atualização. As extensões são descarregadas a partir do Azure e copiadas para a `%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads` pasta no Windows, e para o Linux. `/opt/GC_Ext/downloads` No Windows, a extensão é instalada para o seguinte caminho `%SystemDrive%\Packages\Plugins\<extension>` , e no Linux a extensão é instalada para `/var/lib/waagent/<extension>` .

## <a name="download-agents"></a>Agentes de descarregamento

Pode descarregar o pacote de agentes Azure Connected Machine para Windows e Linux a partir das localizações listadas abaixo.

* [Pacote do Windows Agent Windows Installer](https://aka.ms/AzureConnectedMachineAgent) do Microsoft Download Center.

* O pacote de agente Linux é distribuído a partir do [repositório](https://packages.microsoft.com/) de pacotes da Microsoft utilizando o formato de pacote preferido para a distribuição (. RPM ou . DEB).

>[!NOTE]
>Durante esta pré-visualização, apenas um pacote foi lançado, que é adequado para Ubuntu 16.04 ou 18.04.

O agente Azure Connected Machine para Windows e Linux pode ser atualizado para a versão mais recente manualmente ou automaticamente, dependendo dos seus requisitos. Para mais informações, consulte [aqui.](manage-agent.md)

## <a name="windows-agent-installation-details"></a>Detalhes da instalação do agente do Windows

O agente da Máquina Conectada para Windows pode ser instalado utilizando um dos três métodos seguintes:

* Clique duas vezes no `AzureConnectedMachineAgent.msi` ficheiro.
* Manualmente, executando o pacote do Instalador do Windows `AzureConnectedMachineAgent.msi` a partir da concha do Comando.
* A partir de uma sessão PowerShell usando um método scripted.

Após a instalação do agente 'Máquina Conectada' para o Windows, aplicam-se as seguintes alterações adicionais de configuração a nível do sistema.

* As seguintes pastas de instalação são criadas durante a instalação.

    |Pasta |Descrição |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineagent |Caminho de instalação predefinido que contenha os ficheiros de suporte do agente.|
    |%ProgramaData%\AzureConnectedMachineagent |Contém os ficheiros de configuração do agente.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Contém os tokens adquiridos.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Contém o ficheiro de configuração do agente `agentconfig.json` que regista as suas informações de registo com o serviço.|
    |%SystemDrive%\Program Files\ArcConnectedMachineAgent\ExtensionService\GC | Caminho de instalação que contém os ficheiros do agente de configuração do hóspede. |
    |%ProgramData%\GuestConfig |Contém as políticas (aplicadas) do Azure.|
    |%SystemDrive%\AzureConnectedMachineAgent\ExtensionService\downloads | As extensões são descarregadas a partir de Azure e copiadas aqui.|

* Os seguintes serviços Windows são criados na máquina-alvo durante a instalação do agente.

    |Nome do serviço |Nome a apresentar |Nome do processo |Descrição |
    |-------------|-------------|-------------|------------|
    |osds |Serviço de Metadados de Caso Híbrido Azure |himds.exe |Este serviço implementa o serviço de metadados Azure Instance (IMDS) para gerir a ligação ao Azure e a identidade Azure da máquina conectada.|
    |DscService |Serviço de Configuração de Hóspedes |dsc_service.exe |Esta é a base de código de configuração do estado desejada (DSC v2) usada dentro do Azure para implementar a Política de Hóspedes.|

* As seguintes variáveis ambientais são criadas durante a instalação do agente.

    |Nome |Valor predefinido |Descrição |
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
    |%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs|Grava detalhes sobre o componente do agente de extensão.|
    |%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>|Grava detalhes da extensão instalada.|

* O grupo de segurança local **Aplicações de extensão de agente híbrido são criadas.**

* Durante a desinstalação do agente, os seguintes artefactos não são removidos.

    * %ProgramFiles%\AzureConnectedMachineAgent\Logs
    * %ProgramData%\AzureConnectedMachineAgent e subdiretórios
    * %ProgramData%\GuestConfig

## <a name="linux-agent-installation-details"></a>Detalhes da instalação do agente Linux

O agente da Máquina Conectada para o Linux é fornecido no formato de pacote preferido para a distribuição (. RPM ou . DEB) que está hospedado no [repositório](https://packages.microsoft.com/)de pacotes da Microsoft. O agente está instalado e configurado com o pacote de scripts de concha [Install_linux_azcmagent.sh](https://aka.ms/azcmagent).

Após a instalação do agente 'Máquina Conectada' para o Linux, aplicam-se as seguintes alterações adicionais de configuração a nível do sistema.

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
    |himdsd.service |Serviço de Metadados de Caso Híbrido Azure |/opt/azcmagent/bin/himds |Este serviço implementa o serviço de metadados Azure Instance (IMDS) para gerir a ligação ao Azure e a identidade Azure da máquina conectada.|
    |dscd.service |Serviço de Configuração de Hóspedes |/opt/DSC/dsc_linux_service |Esta é a base de código de configuração do estado desejada (DSC v2) usada dentro do Azure para implementar a Política de Hóspedes.|

* Existem vários ficheiros de registo disponíveis para resolução de problemas. São descritos na tabela seguinte.

    |Registo |Descrição |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Regista detalhes do serviço e interação dos agentes (HIMDS) com o Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Contém a saída dos comandos da ferramenta azcmagent, quando o argumento verboso (v) é utilizado.|
    |/opt/logs/dsc.log |Regista detalhes da atividade de serviço da DSC,<br> em particular a conectividade entre o serviço de himas e a Política Azure.|
    |/opt/logs/dsc.telemetry.txt |Grava detalhes sobre telemetria de serviço DSC e registo verboso.|
    |/var/lib/GuestConfig/ext_mgr_logs |Grava detalhes sobre o componente do agente de extensão.|
    |/var/log/GuestConfig/extension_logs|Grava detalhes da extensão instalada.|

* As seguintes variáveis ambientais são criadas durante a instalação do agente. Estas variáveis estão definidas em `/lib/systemd/system.conf.d/azcmagent.conf` .

    |Nome |Valor predefinido |Descrição |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Durante a desinstalação do agente, os seguintes artefactos não são removidos.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="prerequisites"></a>Pré-requisitos

### <a name="supported-operating-systems"></a>Sistemas operativos suportados

As seguintes versões do sistema operativo Windows e Linux são oficialmente suportadas para o agente Azure Connected Machine: 

- Windows Server 2012 R2 e superior (incluindo o Núcleo do Servidor do Windows)
- Ubuntu 16.04 e 18.04 (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)

>[!NOTE]
>Esta versão de pré-visualização do agente 'Máquina Conectada para Windows' suporta apenas o Windows Server configurado para utilizar o idioma inglês.
>

### <a name="required-permissions"></a>Permissões obrigatórias

* Para máquinas a bordo, você é membro da **função de azure connected machine onboarding.**

* Para ler, modificar, voltar a bordo e eliminar uma máquina, é membro da função de Administrador de **Recursos da Máquina Azure.** 

### <a name="azure-subscription-and-service-limits"></a>Limites de subscrição e serviço azure

Antes de configurar as suas máquinas com O Arco Azure para servidores (pré-visualização), deverá rever os [limites](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) de subscrição do Gestor de Recursos Azure e [os limites](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) do grupo de recursos para planear o número de máquinas a serem ligadas.

## <a name="tls-12-protocol"></a>Protocolo TLS 1.2

Para garantir a segurança dos dados em trânsito para Azure, encorajamo-lo a configurar a máquina para utilizar a Segurança da Camada de Transporte (TLS) 1.2. Versões mais antigas da camada de tomadas TLS/Secure Sockets (SSL) foram consideradas vulneráveis e, embora ainda atualmente trabalhem para permitir retrocompatibilidade, não são **recomendadas**.

|Plataforma/Idioma | Suporte | Mais Informações |
| --- | --- | --- |
|Linux | As distribuições linux tendem a contar com [o suporte OpenSSL](https://www.openssl.org) para suporte TLS 1.2. | Verifique o [OpenSSL Changelog](https://www.openssl.org/news/changelog.html) para confirmar que a sua versão do OpenSSL está suportada.|
| Windows Server 2012 R2 e superior | Suportado e ativado por defeito. | Para confirmar que ainda está a utilizar as [definições predefinições](/windows-server/security/tls/tls-registry-settings).|

### <a name="networking-configuration"></a>Configuração de rede

O agente de máquinas conectadas para Linux e Windows comunica de saída de forma segura ao Arco Azure sobre a porta TCP 443. Se a máquina ligar através de uma firewall ou servidor proxy para comunicar através da Internet, rever os requisitos abaixo para entender os requisitos de configuração da rede.

Se a conectividade de saída for restringida pela sua firewall ou servidor proxy, certifique-se de que os URLs listados abaixo não estão bloqueados. Se permitir apenas os intervalos IP ou os nomes de domínio necessários para que o agente comunique com o serviço, também deve permitir o acesso às seguintes Tags de Serviço e URLs.

Etiquetas de serviço:

* AzureActiveDirectory
* AzureTrafficManager

URLs:

| Recursos do agente | Descrição |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`agentserviceapi.azure-automation.net`|Configuração de Convidado|
|`*-agentservice-prod-1.azure-automation.net`|Configuração de Convidado|
|`*.guestconfiguration.azure.com` |Configuração de Convidado|
|`*.his.arc.azure.com`|Serviço de Identidade Híbrida|

Para obter uma lista de endereços IP para cada tag/região de serviço, consulte o ficheiro JSON - [Gamas IP Azure e Tags de Serviço – Nuvem Pública.](https://www.microsoft.com/download/details.aspx?id=56519) A Microsoft publica atualizações semanais contendo cada Serviço Azure e as gamas IP que utiliza. Para mais informações, [reveja as etiquetas de serviço.](../../virtual-network/security-overview.md#service-tags)

Os URLs na tabela anterior são necessários para além das informações do intervalo de endereços IP da Marca de Serviço, uma vez que a maioria dos serviços não tem atualmente um registo de Marca de Serviço. Como tal, os endereços IP estão sujeitos a alterações. Se forem necessários intervalos de endereços IP para a sua configuração de firewall, então a Tag de Serviço **AzureCloud** deve ser utilizada para permitir o acesso a todos os serviços Azure. Não desative a monitorização de segurança ou a inspeção destes URLs, permitindo-os como se fosse outro tráfego de Internet.

### <a name="register-azure-resource-providers"></a>Registar fornecedores de recursos Azure

O Azure Arc para servidores (pré-visualização) depende dos seguintes fornecedores de recursos Azure na sua subscrição para utilizar este serviço:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

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

Também pode registar os fornecedores de recursos no portal Azure seguindo os passos no [portal Azure](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="installation-and-configuration"></a>Instalação e configuração

As máquinas de ligação no seu ambiente híbrido diretamente com o Azure podem ser realizadas utilizando métodos diferentes, dependendo dos seus requisitos. A tabela a seguir destaca cada método para determinar qual o melhor funciona para a sua organização.

| Método | Descrição |
|--------|-------------|
| Interativamente | Instale manualmente o agente num único ou pequeno número de máquinas seguindo os passos das [máquinas Connect a partir do portal Azure](onboard-portal.md).<br> A partir do portal Azure, pode gerar um script e executá-lo na máquina para automatizar os passos de instalação e configuração do agente.|
| Em escala | Instale e configuure o agente para várias máquinas que seguem as [máquinas Connect utilizando um Diretor de Serviço](onboard-service-principal.md).<br> Este método cria um principal de serviço para ligar máquinas não interativamente.|
| Em escala | Instale e configuure o agente para várias máquinas seguindo o método Utilizando o [Windows PowerShell DSC](onboard-dsc.md).<br> Este método utiliza um principal de serviço para ligar máquinas não interativamente com o PowerShell DSC. |

## <a name="next-steps"></a>Passos seguintes

Para começar a avaliar o Arco azul para servidores (pré-visualização), siga o artigo [Ligue as máquinas híbridas ao Azure a partir do portal Azure](onboard-portal.md).
