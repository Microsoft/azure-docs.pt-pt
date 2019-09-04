---
title: Entenda como auditar o conteúdo de um computador
description: Saiba como o Azure Policy usa a configuração de convidado para auditar as configurações dentro de um computador do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 06a767af71f457273e0e20d1248d64c22b3563e7
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274954"
---
# <a name="understand-azure-policys-guest-configuration"></a>Compreender a configuração de convidado do Azure Policy

Além de auditar e [corrigir](../how-to/remediate-resources.md) recursos do Azure, Azure Policy pode auditar configurações dentro de um computador. A validação é executada a extensão de configuração do convidado e o cliente. A extensão, por meio do cliente, valida as definições como a configuração do sistema operativo, configuração da aplicação ou presença, definições de ambiente e muito mais.

Neste momento, Azure Policy configuração de convidado executa apenas uma auditoria de configurações dentro da máquina.
Ainda não é possível aplicar as configurações.

## <a name="extension-and-client"></a>Extensão e de cliente

Para auditar as configurações dentro de um computador, uma [extensão de máquina virtual](../../../virtual-machines/extensions/overview.md) está habilitada. A extensão transfere a atribuição de política aplicável e a definição de configuração correspondente.

### <a name="limits-set-on-the-extension"></a>Limites definidos na extensão

Para limitar a extensão de afetar os aplicativos em execução dentro da máquina, a configuração de convidado não tem permissão para exceder mais de 5% da utilização da CPU.
Isso é verdadeiro para as configurações fornecidas pela Microsoft como "internas" e para configurações personalizadas criadas por clientes.

## <a name="register-guest-configuration-resource-provider"></a>Registar fornecedor de recursos de configuração de convidado

Antes de poder utilizar configuração de convidado, tem de registar o fornecedor de recursos. Pode registrar através do portal ou através do PowerShell. O provedor de recursos será registrado automaticamente se a atribuição de uma política de configuração de convidado for feita por meio do Portal.

### <a name="registration---portal"></a>Registo - Portal

Para registar o fornecedor de recursos para a configuração de convidado através do portal do Azure, siga estes passos:

1. Iniciar o portal do Azure e clique em **todos os serviços**. Procure e selecione **subscrições**.

1. Localize e clique na subscrição que pretende ativar a configuração de convidado para.

1. No menu à esquerda do **subscrição** página, clique em **fornecedores de recursos**.

1. Filtrar por ou rolar até localizar **Microsoft.GuestConfiguration**, em seguida, clique em **registar** na mesma linha.

### <a name="registration---powershell"></a>Registo - PowerShell

Para registar o fornecedor de recursos para a configuração de convidado através do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Ferramentas de validação

Dentro do computador, o cliente de configuração de convidado usa ferramentas locais para executar a auditoria.

A tabela seguinte mostra uma lista das ferramentas de locais usadas em cada sistema operacional compatível:

|Sistema operativo|Ferramenta de validação|Notas|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby e Python é instalados pela extensão da configuração de convidado. |

### <a name="validation-frequency"></a>Frequência de validação

O cliente de configuração de convidado verifica o novo conteúdo a cada 5 minutos. Depois que uma atribuição de convidado é recebida, as configurações são verificadas em um intervalo de 15 minutos. Os resultados são enviados para o provedor de recursos de configuração de convidado assim que a auditoria é concluída. Quando ocorre um [gatilho de avaliação](../how-to/get-compliance-data.md#evaluation-triggers) de política, o estado do computador é gravado no provedor de recursos de configuração do convidado. Isso faz com que Azure Policy avalie as propriedades de Azure Resource Manager. Uma avaliação de Azure Policy sob demanda recupera o valor mais recente do provedor de recursos de configuração de convidado. No entanto, ele não aciona uma nova auditoria da configuração no computador.

## <a name="supported-client-types"></a>Tipos de cliente suportados

A tabela seguinte mostra uma lista de sistemas operativos suportados nas imagens do Azure:

|Publicador|Nome|Versões|
|-|-|-|
|Canónico|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 datacenter, 2012 R2 Datacenter, 2016 Data Center, 2019 datacenter|
|Microsoft|Cliente Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> A configuração de convidado pode auditar OS nós que executam um sistema operacional com suporte. Se você quiser auditar as máquinas virtuais que usam uma imagem personalizada, será necessário duplicar a definição de **DeployIfNotExists** e modificar a seção **If** para incluir suas propriedades de imagem.

### <a name="unsupported-client-types"></a>Tipos de cliente não suportada

O Windows Server nano Server não tem suporte em nenhuma versão.

## <a name="guest-configuration-extension-network-requirements"></a>Requisitos de rede da extensão de configuração do convidado

Para se comunicar com o provedor de recursos de configuração de convidado no Azure, as máquinas exigem acesso de saída aos datacenters do Azure na porta **443**. Se você estiver usando uma rede virtual privada no Azure e não permitir o tráfego de saída, as exceções deverão ser configuradas usando regras de [grupo de segurança de rede](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . Neste momento, uma marca de serviço não existe para Azure Policy configuração de convidado.

Para listas de endereços IP, você pode baixar [Microsoft Azure intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653)do datacenter. Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP. Você só precisa permitir o acesso de saída aos IPs nas regiões em que suas VMs são implantadas.

> [!NOTE]
> O arquivo XML do endereço IP do datacenter do Azure lista os intervalos de endereços IP que são usados nos data centers Microsoft Azure. O arquivo inclui intervalos de computação, SQL e armazenamento. Um arquivo atualizado é lançado semanalmente. O arquivo reflete os intervalos atualmente implantados e as alterações futuras nos intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos data centers por pelo menos uma semana. É uma boa ideia baixar o novo arquivo XML a cada semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Azure ExpressRoute devem observar que esse arquivo é usado para atualizar o anúncio de Border Gateway Protocol (BGP) do espaço do Azure na primeira semana de cada mês.

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição de configuração de convidado

Cada execução de auditoria pela configuração de convidado requer duas definições de política, uma definição de **DeployIfNotExists** e uma definição de **AuditIfNotExists** . A definição de **DeployIfNotExists** é usada para preparar a máquina com o agente de configuração convidado e outros componentes para dar suporte às [ferramentas de validação](#validation-tools).

O **DeployIfNotExists** definição de política valida e corrige os seguintes itens:

- Valide se uma configuração foi atribuída à máquina para avaliação. Se nenhuma atribuição estiver presente no momento, obtenha a atribuição e prepare a máquina da:
  - Autenticando no computador usando uma [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalar a versão mais recente dos **Microsoft.GuestConfiguration** extensão
  - A instalar [ferramentas de validação](#validation-tools) e as dependências, se necessário

Se a atribuição de **DeployIfNotExists** não for compatível, uma [tarefa de correção](../how-to/remediate-resources.md#create-a-remediation-task) poderá ser usada.

Depois que a atribuição de **DeployIfNotExists** é compatível, a atribuição de política **AuditIfNotExists** usa as ferramentas de validação locais para determinar se a atribuição de configuração é compatível ou não compatível.
A ferramenta de validação fornece os resultados para o cliente de configuração de convidado. O cliente reencaminha os resultados para a extensão de convidado, o que as torna disponíveis por meio do Provedor de recursos de configuração de convidado.

Política do Azure utiliza os fornecedores de recursos de configuração de convidado **complianceStatus** propriedade para reportar a conformidade no **conformidade** nó. Para obter mais informações, consulte [obtenção de dados de conformidade](../how-to/getting-compliance-data.md).

> [!NOTE]
> A política **DeployIfNotExists** é necessária para que a política **AuditIfNotExists** retorne os resultados.
> Sem o **DeployIfNotExists**, a política **AuditIfNotExists** mostra "0 de 0" recursos como status.

Todas as políticas incorporadas para a configuração de convidado são incluídas numa iniciativa para as definições para utilizam em atribuições de grupo. A iniciativa interna chamada *[Preview]: As configurações de segurança de senha de auditoria dentro* de computadores Linux e Windows contêm 18 políticas. Existem seis **DeployIfNotExists** e **AuditIfNotExists** pares para Windows e três pares para Linux. Valida a apenas o destino em cada caso, a lógica dentro da definição do sistema operativo é avaliado com base no [regra de política](definition-structure.md#policy-rule) definição.

### <a name="multiple-assignments"></a>Várias atribuições

Atualmente, as políticas de configuração de convidado só dão suporte à atribuição da mesma entrada de convidado uma vez por computador, mesmo que a atribuição de política use parâmetros diferentes.

## <a name="built-in-resource-modules"></a>Módulos de recursos internos

Quando a extensão de configuração de convidado é instalada, o módulo do PowerShell "GuestConfiguration" é incluído com a versão mais recente dos módulos de recursos de DSC. Esse módulo pode ser baixado do Galeria do PowerShell usando o link ' download manual ' na página do módulo [GuestConfiguration/](https://www.powershellgallery.com/packages/GuestConfiguration/).
O formato de arquivo '. nupkg ' pode ser renomeado para '. zip ' para descompactar e examinar.

## <a name="client-log-files"></a>Arquivos de log do cliente

A extensão de configuração de convidado grava arquivos de log nos seguintes locais:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Em `<version>` que refere-se ao número de versão atual.

### <a name="collecting-logs-remotely"></a>Coletando logs remotamente

A primeira etapa na solução de problemas de configurações de convidado ou módulos deve ser usar `Test-GuestConfigurationPackage` o cmdlet seguindo as etapas em [testar um pacote de configuração de convidado](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).  Se isso não for bem-sucedido, coletar logs do cliente poderá ajudar a diagnosticar problemas.

#### <a name="windows"></a>Windows

Se você quiser usar a capacidade de comando de execução de VM do Azure para capturar informações de arquivos de log em computadores Windows, o exemplo de script do PowerShell a seguir pode ser útil. Para obter detalhes sobre como executar o script no portal do Azure ou usando Azure PowerShell, consulte [executar scripts do PowerShell em sua VM do Windows com o comando executar](../../../virtual-machines/windows/run-command.md).

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Se você quiser usar a capacidade de comando de execução de VM do Azure para capturar informações de arquivos de log em computadores Linux, o exemplo de script de bash a seguir pode ser útil. Para obter detalhes sobre como executar o script no portal do Azure ou usando CLI do Azure, consulte [executar scripts de Shell em sua VM do Linux com o comando executar](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Exemplos de configuração de convidado

Exemplos de configuração de convidado de política estão disponíveis nos seguintes locais:

- [Índice de exemplos – configuração de convidado](../samples/index.md#guest-configuration)
- [Repositório GitHub de exemplos de Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>Passos seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](definition-structure.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Entenda como [criar políticas](../how-to/programmatically-create.md)programaticamente.
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/index.md).
