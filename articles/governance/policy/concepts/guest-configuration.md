---
title: Aprenda a auditar o conteúdo das máquinas virtuais
description: Saiba como a Política Azure utiliza o agente de configuração do hóspede para auditar definições dentro de máquinas virtuais.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: 128baa0df1d3e555fe9629005529710202e1bbf6
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169531"
---
# <a name="understand-azure-policys-guest-configuration"></a>Compreender a configuração de convidado do Azure Policy

Além de auditar e [remediar](../how-to/remediate-resources.md) os recursos do Azure, a Política Azure pode auditar as definições dentro de uma máquina. A validação é executada pela extensão da Configuração de Convidado e pelo cliente. A extensão, através do cliente, valida as definições, como:

- A configuração do sistema operativo
- Presença ou configuração da aplicação
- Definições do ambiente

Neste momento, a Configuração de Convidado do Azure Policy audita apenas as definições no computador. Não se aplica às configurações.

## <a name="extension-and-client"></a>Extensão e de cliente

Para auditar as definições dentro de uma máquina, é ativada uma [extensão virtual](../../../virtual-machines/extensions/overview.md) da máquina. A extensão transfere a atribuição de política aplicável e a definição de configuração correspondente.

### <a name="limits-set-on-the-extension"></a>Limites definidos na extensão

Para limitar a extensão de aplicações com impacto dentro da máquina, a Configuração de Hóspedes não está autorizada a exceder mais de 5% da utilização do CPU. Esta limitação existe tanto para definições incorporadas como personalizadas.

## <a name="register-guest-configuration-resource-provider"></a>Registar fornecedor de recursos de configuração de convidado

Antes de poder utilizar configuração de convidado, tem de registar o fornecedor de recursos. Pode registrar através do portal ou através do PowerShell. O fornecedor de recursos é registado automaticamente se a atribuição de uma política de Configuração de Hóspedes for feita através do portal.

### <a name="registration---portal"></a>Registo - Portal

Para registar o fornecedor de recursos para a configuração de convidado através do portal do Azure, siga estes passos:

1. Lance o portal Azure e clique em **Todos os serviços.** Procure e selecione **Subscrições**.

1. Localize e clique na subscrição que pretende ativar a configuração de convidado para.

1. No menu esquerdo da página **de Subscrição,** clique em fornecedores de **Recursos.**

1. Filtrar ou rolar até localizar a **Microsoft.GuestConfiguration**e, em seguida, clicar em **Registar** na mesma linha.

### <a name="registration---powershell"></a>Registo - PowerShell

Para registar o fornecedor de recursos para a configuração de convidado através do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

## <a name="validation-tools"></a>Ferramentas de validação

Dentro da máquina, o cliente de Configuração de Hóspedes utiliza ferramentas locais para executar a auditoria.

A tabela seguinte mostra uma lista das ferramentas de locais usadas em cada sistema operacional compatível:

|Sistema operativo|Ferramenta de validação|Notas|
|-|-|-|
|Windows|[Windows PowerShell Desejado Configuração do Estado](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby e Python é instalados pela extensão da configuração de convidado. |

### <a name="validation-frequency"></a>Frequência de validação

O cliente de Configuração de Hóspedes verifica novos conteúdos a cada 5 minutos. Uma vez recebida uma atribuição de hóspedes, as definições são verificadas num intervalo de 15 minutos. Os resultados são enviados para o fornecedor de recursos de Configuração de Hóspedes assim que a auditoria estiver concluída. Quando ocorre um gatilho de [avaliação](../how-to/get-compliance-data.md#evaluation-triggers) de política, o estado da máquina é escrito ao fornecedor de recursos de Configuração de Hóspedes. Esta atualização faz com que a Política Azure avalie as propriedades do Gestor de Recursos Do Azure. Uma avaliação de Política Azure a pedido recupera o valor mais recente do fornecedor de recursos de Configuração de Hóspedes. No entanto, não desencadeia uma nova auditoria da configuração dentro da máquina.

## <a name="supported-client-types"></a>Tipos de cliente suportados

A tabela seguinte mostra uma lista de sistemas operativos suportados nas imagens do Azure:

|Publicador|Nome|Versões|
|-|-|-|
|Canónico|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|Datacenter 2012, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Cliente Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> A Configuração do Hóspede pode auditar os nódosos que executam um SISTEMA suportado. Se quiser auditar máquinas virtuais que utilizem uma imagem personalizada, precisa duplicar a definição **DeployIfNotExists** e modificar a secção **Se** para incluir as suas propriedades de imagem.

### <a name="unsupported-client-types"></a>Tipos de cliente não suportada

O Windows Server Nano Server não é suportado em nenhuma versão.

## <a name="guest-configuration-extension-network-requirements"></a>Requisitos da rede de extensão de configuração do hóspede

Para comunicar com o fornecedor de recursos de Configuração de Hóspedes em Azure, as máquinas requerem acesso de saída aos centros de dados Azure na porta **443**. Se estiver a utilizar uma rede virtual privada em Azure que não permita o tráfego de saída, configure exceções com as regras [do Network Security Group.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)
A etiqueta de [serviço](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" pode ser utilizada para fazer referência ao serviço de Configuração de Hóspedes.

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição de configuração de convidado

Cada auditoria realizada pela Configuração do Hóspede requer duas definições de política, uma definição **DeployIfNotExists** e uma definição **AuditIfNotExists.** A definição **DeployIfNotExists** é utilizada para preparar a máquina com o agente de configuração do hóspede e outros componentes para suportar as ferramentas de [validação](#validation-tools).

A definição de política **DeployIfNotExists** valida e corrige os seguintes itens:

- Validar a máquina foi atribuída uma configuração para avaliar. Se não houver nenhuma atribuição atualmente presente, obtenha a atribuição e prepare a máquina por:
  - Autenticação à máquina utilizando uma [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalação da versão mais recente da extensão **Microsoft.GuestConfiguration**
  - Instalação de ferramentas e dependências de [validação,](#validation-tools) se necessário

Se a atribuição **DeployIfNotExists** não for conforme, pode ser utilizada uma tarefa de [reparação.](../how-to/remediate-resources.md#create-a-remediation-task)

Uma vez que a atribuição **Do DesdobrifNotExists** esteja em conformidade, a atribuição de políticas **AuditIfNotExists** utiliza as ferramentas de validação locais para determinar se a atribuição de configuração é conforme ou não conforme. A ferramenta de validação fornece os resultados para o cliente de configuração de convidado. O cliente reencaminha os resultados para a extensão de convidado, o que as torna disponíveis por meio do Provedor de recursos de configuração de convidado.

A Política Azure utiliza a propriedade **compliancede** os fornecedores de recursos de configuração de hóspedes para reportar a conformidade no nó **de conformidade.** Para mais informações, consulte [a obtenção](../how-to/get-compliance-data.md)de dados de conformidade .

> [!NOTE]
> A política **DeployIfNotExists** é necessária para que a política **AuditIfNotExist** a devolução dos resultados. Sem o **DeployIfNotExists,** a política **AuditIfNotExists** mostra os recursos "0 de 0" como estatuto.

Todas as políticas incorporadas para a configuração de convidado são incluídas numa iniciativa para as definições para utilizam em atribuições de grupo. A iniciativa incorporada denominada _\[Preview\]: Audit Password security settings inside Linux and Windows machines_ contém 18 políticas. Existem seis pares **ImplementIfNotExists** e **AuditIfNotExists** para Windows e três pares para o Linux. A lógica de [definição](definition-structure.md#policy-rule) de política valida que apenas o sistema operativo-alvo é avaliado.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditoria das definições do sistema operativo seguindo as linhas de base da indústria

Uma das iniciativas disponíveis na Política do Azure fornece a capacidade de auditar as definições do sistema operativo dentro de máquinas virtuais seguindo uma "linha de base" da Microsoft. A definição, _\[Preview\]: Audit Windows VMs que não correspondem_ às definições de base de segurança do Azure inclui um conjunto completo de regras de auditoria baseadas em definições da Política do Grupo de Diretórios Ativos.

A maioria das definições estão disponíveis como parâmetros. Esta funcionalidade permite-lhe personalizar o que é auditado para alinhar a política com os seus requisitos organizacionais ou mapear a política para informações de terceiros, como normas regulamentares do setor.

Alguns parâmetros suportam uma gama de valor inteiro. Por exemplo, o parâmetro idade máxima da senha pode ser definido utilizando um operador de gama para dar flexibilidade aos proprietários das máquinas. Pode auditar que a definição eficaz da Política do Grupo que exige que os utilizadores mudem as suas palavras-passe não deve ser superior a 70 dias, mas não deve ser inferior a um dia. Tal como descrito na bolha de informação para o parâmetro, para tornar esta política comercial o valor de auditoria eficaz, fixou o valor em "1,70".

Se atribuir a apólice utilizando um modelo de implementação do Gestor de Recursos Azure, pode utilizar um ficheiro de parâmetros para gerir estas definições a partir do controlo de fonte. Utilizando uma ferramenta como a Git para gerir alterações às políticas de Auditoria com comentários em cada documento de check-in, prova do porquê de uma atribuição ser uma exceção ao valor esperado.

#### <a name="applying-configurations-using-guest-configuration"></a>Aplicação de configurações usando a configuração do hóspede

A mais recente funcionalidade da Política Azure configura as configurações dentro das máquinas. A definição _Configurar o fuso horário nas máquinas Windows_ altera a máquina configurando o fuso horário.

Ao atribuir definições que comecem com _configure,_ deve também atribuir a definição _De si de implementação de pré-requisitos para ativar a Política_de Configuração do Hóspede nos VMs do Windows . Pode combinar estas definições numa iniciativa, se quiser.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Atribuindo políticas a máquinas fora de Azure

As políticas de Auditoria disponíveis para configuração de hóspedes incluem o tipo de recursos **Microsoft.HybridCompute/machines.** Quaisquer máquinas a bordo do [Azure Arc para Servidores](../../../azure-arc/servers/overview.md) que estejam no âmbito da atribuição de políticas estão automaticamente incluídas.

### <a name="multiple-assignments"></a>Múltiplas atribuições

As políticas de configuração do hóspede atualmente apenas suportam a atribuição da mesma Atribuição de Hóspedes uma vez por máquina, mesmo que a atribuição de Política utilize diferentes parâmetros.

## <a name="built-in-resource-modules"></a>Módulos de recursos incorporados

Ao instalar a extensão de Configuração de Hóspedes, o módulo PowerShell 'GuestConfiguration' está incluído com a versão mais recente dos módulos de recursos DSC. Este módulo pode ser descarregado da PowerShell Gallery utilizando o link 'Manual Download' da página do módulo [GuestConfiguration](https://www.powershellgallery.com/packages/GuestConfiguration/). O formato de ficheiro '.nupkg' pode ser renomeado para '.zip' para descomprimir e rever.

## <a name="client-log-files"></a>Ficheiros de registo do cliente

A extensão de Configuração do Hóspede escreve ficheiros de registo para os seguintes locais:

Janelas: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Sempre que `<version>` se refere ao número da versão atual.

### <a name="collecting-logs-remotely"></a>Recolha de registos remotamente

O primeiro passo para resolver as configurações ou módulos de configuração do hóspede deve ser utilizar o `Test-GuestConfigurationPackage` cmdlet seguindo os passos em Testar um pacote de [configuração de hóspedes](../how-to/guest-configuration-create.md#test-a-guest-configuration-package).
Se isso não for bem sucedido, recolher registos de clientes pode ajudar a diagnosticar problemas.

#### <a name="windows"></a>Windows

Para utilizar a capacidade de Comando de Execução Azure VM para capturar informações de ficheiros de registo nas máquinas Windows, o seguinte exemplo do script PowerShell pode ser útil. Para mais informações, consulte [scripts Run PowerShell no seu Windows VM com Comando](../../../virtual-machines/windows/run-command.md)de Execução .

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$latestVersion = Get-ChildItem -Path 'C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\' | ForEach-Object {$_.FullName} | Sort-Object -Descending | Select-Object -First 1
Select-String -Path "$latestVersion\dsc\logs\dsc.log" -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Para utilizar a capacidade de Comando de Execução Azure VM para capturar informações de ficheiros de registo nas máquinas Linux, o seguinte exemplo de script Bash pode ser útil. Para mais informações, consulte [os scripts run shell no seu VM Linux com Comando de Execução](../../../virtual-machines/linux/run-command.md)

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
latestVersion=$(find /var/lib/waagent/ -type d -name "Microsoft.GuestConfiguration.ConfigurationforLinux-*" -maxdepth 1 -print | sort -z | sed -n 1p)
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' "$latestVersion/GCAgent/logs/dsc.log" | tail
```

## <a name="guest-configuration-samples"></a>Amostras de configuração de convidados

Fonte para as iniciativas de configuração de hóspedes de política estão disponíveis nos seguintes locais:

- [Iniciativas incorporadas - Configuração de Hóspedes](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy amostras GitHub repo](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest Configuration)

## <a name="next-steps"></a>Passos seguintes

- Reveja exemplos nas [amostras da Política Azure.](../samples/index.md)
- Reveja a [estrutura de definição do Azure Policy](definition-structure.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Compreender como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como obter dados de [conformidade.](../how-to/get-compliance-data.md)
- Aprenda a [remediar recursos não conformes](../how-to/remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)
