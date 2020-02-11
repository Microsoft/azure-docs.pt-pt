---
title: Ligue as máquinas híbridas ao Azure em escala
description: Neste artigo, aprende-se a ligar máquinas ao Azure utilizando o Azure Arc para servidores (pré-visualização) utilizando um diretor de serviço.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 0b0e37f8967c23c1be7dcf4ac987a2fc998e9c6a
ms.sourcegitcommit: d12880206cf9926af6aaf3bfafda1bc5b0ec7151
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114270"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Ligue as máquinas híbridas ao Azure em escala

Pode ativar o Azure Arc para servidores (pré-visualização) para várias máquinas Windows ou Linux no seu ambiente com várias opções flexíveis, dependendo das suas necessidades. Utilizando o script de modelo que fornecemos, pode automatizar cada passo da instalação, incluindo estabelecer a ligação ao Arco Azure. No entanto, é-lhe exigido executar interativamente este script com uma conta que tenha permissões elevadas na máquina-alvo e em Azure. Para ligar as máquinas ao Azure Arc para servidores, pode utilizar um diretor de [serviço](../../active-directory/develop/app-objects-and-service-principals.md) de Diretório Ativo Azure em vez de utilizar a sua identidade privilegiada para [ligar interativamente a máquina](quickstart-onboard-portal.md). Um diretor de serviço é uma identidade de gestão limitada especial que só é concedida a permissão mínima necessária para ligar máquinas a Azure usando o comando `azcmagent`. Isto é mais seguro do que usar uma conta privilegiada mais alta como um Administrador de Inquilino, e segue as nossas melhores práticas de segurança de controlo de acesso. O diretor de serviço é utilizado apenas durante o embarque, não é utilizado para qualquer outro fim.  

Os métodos de instalação para instalar e configurar o agente Máquina Conectada requerem que o método automatizado que utiliza tem permissões de administrador nas máquinas. No Linux, utilizando a conta raiz e no Windows, como membro do grupo de Administradores Locais.

Antes de começar, certifique-se de rever os [pré-requisitos](overview.md#prerequisites) e verificar se a sua subscrição e recursos cumprem os requisitos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

No final deste processo, terá ligado com sucesso as suas máquinas híbridas ao Azure Arc para servidores.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Criar um Diretor de Serviço para embarque em escala

Pode utilizar o [Azure PowerShell](/powershell/azure/install-az-ps) para criar um diretor de serviço com o [cmdlet New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal) Ou pode seguir os passos listados no [âmbito create a Service Principal utilizando o portal Azure](../../active-directory/develop/howto-create-service-principal-portal.md) para completar esta tarefa.

> [!NOTE]
> Quando cria um diretor de serviço, a sua conta deve ser um Proprietário ou Administrador de Acesso ao Utilizador na subscrição que pretende utilizar para o embarque. Se não tiver permissões suficientes para criar atribuições de funções, o diretor de serviço pode ser criado, mas não será capaz de embarcar em máquinas.
>

Para criar o diretor de serviço utilizando o PowerShell, execute o seguinte.

1. Executar o seguinte comando. Deve armazenar a saída do [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet numa variável, ou não poderá recuperar a palavra-passe necessária num passo posterior.

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. Para recuperar a palavra-passe armazenada na variável `$sp`, execute o seguinte comando:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Na saída, encontre o valor da palavra-passe sob a **palavra-passe** do campo e copie-o. Encontre também o valor no campo **ApplicationId** e copie-o também. Guarde-os para mais tarde num lugar seguro. Se esquecer ou perder a sua senha principal de serviço, pode resetá-la utilizando o [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet.

Os valores das seguintes propriedades são utilizados com parâmetros passados para o `azcmagent`:

* O valor da propriedade **ApplicationId** é usado para o valor do parâmetro `--service-principal-id`
* O valor da propriedade da **palavra-passe** é utilizado para o parâmetro `--service-principal-secret` utilizado para ligar o agente.

> [!NOTE]
> Certifique-se de utilizar a propriedade principal do serviço **ApplicationId,** não a propriedade **Id.**
>

A função **de embarque da máquina conectada Azure** contém apenas as permissões necessárias para embarcar numa máquina. Pode atribuir a permissão principal do serviço para permitir que o seu âmbito inclua um grupo de recursos ou uma subscrição. Para adicionar a atribuição de funções, consulte Adicionar ou remover atribuições de [funções utilizando o Azure RBAC e o portal Azure](../../role-based-access-control/role-assignments-portal.md) ou adicionar ou remover atribuições de [funções utilizando o Azure RBAC e o Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Instale o agente e ligue-se ao Azure

Os seguintes passos instalam e configuram o agente Máquina Conectada nas suas máquinas híbridas utilizando o modelo de script, que executa passos semelhantes descritos nas [máquinas híbridas Connect para Azure a partir do artigo do portal Azure.](quickstart-onboard-portal.md) A diferença está no passo final em que estabelece a ligação ao Azure Arc utilizando o comando `azcmagent` utilizando o diretor de serviço. 

Seguem-se as definições que configura o comando `azcmagent` a utilizar para o diretor de serviço.

* `tenant-id`: O identificador único (GUID) que representa a sua instância dedicada de Azure AD.
* `subscription-id` : O ID de subscrição (GUID) da sua assinatura Azure em que deseja as máquinas.
* `resource-group` : O nome do grupo de recursos onde pretende que as suas máquinas ligadas pertençam.
* `location` : Ver [regiões azure apoiadas.](overview.md#supported-regions) Esta localização pode ser a mesma ou diferente, como a localização do grupo de recursos.
* `resource-name` : (*Opcional)* Utilizado para a representação de recursos Azure da sua máquina no local. Se não especificar este valor, o nome de anfitrião da máquina é utilizado.

Pode saber mais sobre a `azcmagent` ferramenta de linha de comando através da revisão da [Referência Azcmagent](azcmagent-reference.md).

### <a name="windows-installation-script"></a>Script de instalação do Windows

Segue-se um exemplo do agente Connected Machine para o script de instalação windows que foi modificado para utilizar o diretor de serviço para suportar uma instalação totalmente automatizada e não interativa do agente.

```
 # Download the package
Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

### <a name="linux-installation-script"></a>Script de instalação linux

Segue-se um exemplo do agente Connected Machine para o script de instalação Linux que foi modificado para utilizar o diretor de serviço para suportar uma instalação totalmente automatizada e não interativa do agente.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

Depois de instalar o agente e configurá-lo para ligar ao Azure Arc para servidores (pré-visualização), vá ao portal Azure para verificar se o servidor foi ligado com sucesso. Veja as suas máquinas no [portal Azure.](https://aka.ms/hybridmachineportal)

![Uma ligação bem-sucedida do servidor](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passos seguintes

- Saiba como gerir a sua máquina utilizando a [Política Azure](../../governance/policy/overview.md), para coisas como a configuração de [hóspedes](../../governance/policy/concepts/guest-configuration.md)VM , verificando que a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com o [Monitor Azure com VMs](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md), e muito mais.

- Saiba mais sobre o [agente Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). O agente Log Analytics para Windows e Linux é necessário quando pretende monitorizar proativamente o SISTEMA e as cargas de trabalho em funcionamento na máquina, geri-la utilizando livros de execução automation ou soluções como a Update Management, ou utilizar outros serviços Azure como o [Azure Security Center.](../../security-center/security-center-intro.md)
