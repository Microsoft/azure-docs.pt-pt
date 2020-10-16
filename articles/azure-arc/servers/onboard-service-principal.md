---
title: Ligue as máquinas híbridas ao Azure à escala
description: Neste artigo, aprende-se a ligar máquinas ao Azure utilizando servidores ativados pelo Azure Arc utilizando um principal de serviço.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: d789061c741b8a0da2edabcb56a67898ea28ec90
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108920"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Ligue as máquinas híbridas ao Azure à escala

Pode ativar servidores ativados pelo Azure Arc para várias máquinas Windows ou Linux no seu ambiente com várias opções flexíveis dependendo dos seus requisitos. Utilizando o script do modelo que fornecemos, pode automatizar cada passo da instalação, incluindo estabelecer a ligação ao Arco Azure. No entanto, é-lhe exigido que execute interativamente este script com uma conta que tenha permissões elevadas na máquina-alvo e no Azure. Para ligar as máquinas aos servidores ativados do Azure Arc, pode utilizar um diretor de [serviço](../../active-directory/develop/app-objects-and-service-principals.md) Azure Ative Directory em vez de utilizar a sua identidade privilegiada para [ligar interativamente a máquina](onboard-portal.md). Um diretor de serviço é uma identidade de gestão limitada especial que só é concedida a permissão mínima necessária para ligar máquinas a Azure usando o `azcmagent` comando. Isto é mais seguro do que usar uma conta privilegiada mais alta como um Administrador de Inquilino, e segue as nossas melhores práticas de segurança de controlo de acesso. O principal de serviço é utilizado apenas durante o embarque, não é utilizado para qualquer outro fim.  

Os métodos de instalação para instalar e configurar o agente máquina conectado requer que o método automatizado que utiliza tenha permissões de administrador nas máquinas. No Linux, utilizando a conta raiz e no Windows, como membro do grupo de Administradores Locais.

Antes de começar, certifique-se de rever os [pré-requisitos](agent-overview.md#prerequisites) e verificar se a sua subscrição e recursos cumprem os requisitos.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

No final deste processo, terá ligado com sucesso as suas máquinas híbridas aos servidores ativados do Azure Arc.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Criar um diretor de serviço para o embarque à escala

Pode utilizar [o Azure PowerShell](/powershell/azure/install-az-ps) para criar um diretor de serviço com o [cmdlet New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal) Ou pode seguir os passos listados no [Create a Service Principal utilizando o portal Azure](../../active-directory/develop/howto-create-service-principal-portal.md) para completar esta tarefa.

> [!NOTE]
> Quando cria um titular de serviço, a sua conta deve ser um Proprietário ou Administrador de Acesso ao Utilizador na subscrição que pretende utilizar para o embarque. Se não tiver permissões suficientes para criar atribuições de funções, o diretor de serviço pode ser criado, mas não será capaz de embarcar em máquinas.
>

Para criar o principal de serviço utilizando o PowerShell, execute o seguinte.

1. Execute o seguinte comando. Deve armazenar a saída do [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet numa variável, ou não conseguirá recuperar a palavra-passe necessária num passo posterior.

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

2. Para recuperar a palavra-passe armazenada na `$sp` variável, execute o seguinte comando:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Na saída, encontre o valor da palavra-passe na **palavra-passe** de campo e copie-a. Encontre também o valor no campo **ApplicationId** e copie-o também. Guarde-os para mais tarde num lugar seguro. Se esquecer ou perder a palavra-passe principal do serviço, pode reiniciá-la utilizando o [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet.

Os valores das seguintes propriedades são utilizados com parâmetros passados `azcmagent` para:

* O valor da propriedade **ApplicationId** é usado para o valor do `--service-principal-id` parâmetro
* O valor da propriedade da **palavra-passe** é usado para o  `--service-principal-secret` parâmetro utilizado para ligar o agente.

> [!NOTE]
> Certifique-se de usar a propriedade principal do **serviço ApplicationId,** não a propriedade **ID.**
>

A **função de azure connected machine onboarding** contém apenas as permissões necessárias para embarcar numa máquina. Pode atribuir a permissão principal do serviço para permitir que o seu âmbito inclua um grupo de recursos ou uma subscrição. Para adicionar atribuição de funções, consulte [adicionar ou remover atribuições de funções Azure utilizando o portal Azure](../../role-based-access-control/role-assignments-portal.md) ou [adicionar ou remover atribuições de funções Azure usando Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Instale o agente e ligue-se ao Azure

Os passos seguintes instalam e configuram o agente da Máquina Conectada nas suas máquinas híbridas utilizando o modelo de script, que executa passos semelhantes descritos nas [máquinas híbridas Connect para Azure a partir do artigo do portal Azure.](onboard-portal.md) A diferença está no passo final onde se estabelece a ligação ao Arco Azure utilizando o `azcmagent` comando utilizando o principal de serviço.

Seguem-se as definições que configura o `azcmagent` comando a utilizar para o principal de serviço.

* `tenant-id` : O identificador único (GUID) que representa o seu exemplo dedicado de Azure AD.
* `subscription-id` : O ID de subscrição (GUID) da sua assinatura Azure em que pretende que as máquinas entrem.
* `resource-group` : O nome do grupo de recursos onde pretende que as suas máquinas ligadas pertençam.
* `location`: Ver [regiões de Azure apoiadas.](overview.md#supported-regions) Esta localização pode ser a mesma ou diferente, como a localização do grupo de recursos.
* `resource-name` : (*Opcional*) Utilizado para a representação de recursos Azure da sua máquina no local. Se não especificar este valor, utiliza-se o nome de anfitrião da máquina.

Pode saber mais sobre a `azcmagent` ferramenta da linha de comando, revendo a Referência [Azcmagent](./manage-agent.md).

### <a name="windows-installation-script"></a>Script de instalação do Windows

Segue-se um exemplo do agente de máquinas conectadas para o script de instalação do Windows que foi modificado para utilizar o principal de serviço para suportar uma instalação totalmente automatizada e não interativa do agente.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

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

>[!NOTE]
>O script suporta apenas a execução a partir de uma versão de 64 bits do Windows PowerShell.
>

### <a name="linux-installation-script"></a>Script de instalação linux

Segue-se um exemplo do agente de máquinas conectadas para o script de instalação Do Linux que foi modificado para utilizar o principal de serviço para suportar uma instalação totalmente automatizada e não interativa do agente.

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

>[!NOTE]
>Você deve ter permissões de acesso à *raiz* em máquinas Linux para executar **azcmagent**.

Depois de instalar o agente e configurá-lo para ligar aos servidores ativados do Azure Arc, vá ao portal Azure para verificar se o servidor se ligou com sucesso. Veja as suas máquinas no [portal do Azure](https://aka.ms/hybridmachineportal).

![Uma ligação de servidor bem sucedida](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Passos seguintes

* As informações relativas à resolução de problemas podem ser encontradas no guia do [agente da máquina conectado de resolução de problemas.](troubleshoot-agent-onboard.md)

- Aprenda a gerir a sua máquina utilizando [a Azure Policy](../../governance/policy/overview.md), para coisas como [a configuração do hóspede](../../governance/policy/concepts/guest-configuration.md)VM , verificando se a máquina está a reportar ao espaço de trabalho esperado do Log Analytics, permitir a monitorização com o [Azure Monitor com VMs](../../azure-monitor/insights/vminsights-enable-policy.md), e muito mais.

- Saiba mais sobre o [agente Log Analytics.](../../azure-monitor/platform/log-analytics-agent.md) O agente Log Analytics para Windows e Linux é necessário quando pretende recolher dados de monitorização do sistema operativo e da carga de trabalho, geri-lo utilizando livros de aplicação da Automação ou funcionalidades como Update Management, ou utilizar outros serviços Azure como [o Azure Security Center](../../security-center/security-center-introduction.md).