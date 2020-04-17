---
title: Aprenda a auditar o conteúdo das máquinas virtuais
description: Saiba como a Política Azure utiliza o agente de configuração do hóspede para auditar definições dentro de máquinas virtuais.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: e4899f6b3108cabb4e9cdd36e4b2bc5cd2f1cbd4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538040"
---
# <a name="understand-azure-policys-guest-configuration"></a>Compreender a configuração de hóspedes da Política Azure

Além de auditar e [remediar](../how-to/remediate-resources.md) os recursos do Azure, a Política Azure pode auditar as definições dentro de uma máquina. A validação é executada pela extensão da Configuração de Convidado e pelo cliente. A extensão, através do cliente, valida as definições, como:

- A configuração do sistema operativo
- Presença ou configuração da aplicação
- Definições do ambiente

Neste momento, a maioria das políticas de configuração de hóspedes da Política Azure apenas auditam definições dentro da máquina. Não aplicam configurações. A exceção é uma política incorporada [abaixo referida](#applying-configurations-using-guest-configuration).

## <a name="extension-and-client"></a>Extensão e cliente

Para auditar as definições dentro de uma máquina, é ativada uma [extensão virtual](../../../virtual-machines/extensions/overview.md) da máquina. A extensão descarrega a atribuição de política aplicável e a definição de configuração correspondente.

### <a name="limits-set-on-the-extension"></a>Limites definidos na extensão

Para limitar a extensão de aplicações com impacto dentro da máquina, a Configuração de Hóspedes não está autorizada a exceder mais de 5% do CPU. Esta limitação existe tanto para definições incorporadas como personalizadas.

## <a name="register-guest-configuration-resource-provider"></a>Registe o fornecedor de recursos de configuração de hóspedes

Antes de poder utilizar a Configuração do Hóspede, tem de registar o fornecedor de recursos. Pode registar-se através do [portal](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), [Azure PowerShell](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell), ou [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli). O fornecedor de recursos é registado automaticamente se a atribuição de uma política de Configuração de Hóspedes for feita através do portal.

## <a name="validation-tools"></a>Ferramentas de validação

Dentro da máquina, o cliente de Configuração de Hóspedes utiliza ferramentas locais para executar a auditoria.

A tabela que se segue apresenta uma lista das ferramentas locais utilizadas em cada sistema operativo suportado:

|Sistema operativo|Ferramenta de validação|Notas|
|-|-|-|
|Windows|[Windows PowerShell Desejado Configuração do Estado](/powershell/scripting/dsc/overview/overview) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Se a Ruby e a Python não estiverem na máquina, são instaladas pela extensão de Configuração de Hóspedes. |

### <a name="validation-frequency"></a>Frequência de validação

O cliente de Configuração de Hóspedes verifica novos conteúdos a cada 5 minutos. Uma vez recebida uma atribuição de hóspedes, as definições para essa configuração são novamente verificadas num intervalo de 15 minutos.
Os resultados são enviados ao fornecedor de recursos de Configuração de Hóspedes quando a auditoria estiver concluída. Quando ocorre um gatilho de [avaliação](../how-to/get-compliance-data.md#evaluation-triggers) de política, o estado da máquina é escrito ao fornecedor de recursos de Configuração de Hóspedes. Esta atualização faz com que a Política Azure avalie as propriedades do Gestor de Recursos Do Azure. Uma avaliação de Política Azure a pedido recupera o valor mais recente do fornecedor de recursos de Configuração de Hóspedes. No entanto, não desencadeia uma nova auditoria da configuração dentro da máquina.

## <a name="supported-client-types"></a>Tipos de clientes suportados

A tabela seguinte mostra uma lista de sistemaoperativo suportado nas imagens do Azure:

|Publicador|Nome|Versões|
|-|-|-|
|Canónico|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|Datacenter 2012, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Cliente Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5, 7.6, 7.7|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5, 7.6, 7.7, 7.8|
|Suse|SLES|12 SP3|

### <a name="unsupported-client-types"></a>Tipos de clientes não suportados

O Windows Server Nano Server não é suportado em nenhuma versão.

## <a name="guest-configuration-extension-network-requirements"></a>Requisitos da rede de extensão de configuração do hóspede

Para comunicar com o fornecedor de recursos de Configuração de Hóspedes em Azure, as máquinas requerem acesso de saída aos centros de dados Azure na porta **443**. Se uma rede em Azure não permitir o tráfego de saída, configure exceções com as regras do Grupo de Segurança da [Rede.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule)
A etiqueta de [serviço](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" pode ser utilizada para fazer referência ao serviço de Configuração de Hóspedes.

## <a name="azure-managed-identity-requirements"></a>Requisitos de identidade geridos pelo Azure

As políticas **DeployIfNotExists** que adicionam a extensão a máquinas virtuais também permitem um sistema de identidade gerida, se não existir.

> [!WARNING]
> Evite permitir que o utilizador atribuísse identidade gerida a máquinas virtuais no âmbito de políticas que permitam o sistema de identidade gerida atribuída. A identidade atribuída ao utilizador será substituída e poderá a máquina ficar sem resposta.

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição de configuração de hóspedes

Cada auditoria realizada pela Configuração do Hóspede requer duas definições de política, uma definição **DeployIfNotExists** e uma definição **AuditIfNotExists.** 

A definição de política **DeployIfNotExists** valida e corrige os seguintes itens:

- Validar a máquina foi atribuída uma configuração para avaliar. Se não houver nenhuma atribuição atualmente presente, obtenha a atribuição e prepare a máquina por:
  - Autenticação à máquina utilizando uma [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalação da versão mais recente da extensão **Microsoft.GuestConfiguration**
  - Instalação de ferramentas e dependências de [validação,](#validation-tools) se necessário

Se a atribuição **DeployIfNotExists** não for conforme, pode ser utilizada uma tarefa de [reparação.](../how-to/remediate-resources.md#create-a-remediation-task)

Uma vez que a atribuição **DeployIfNotExists** esteja em conformidade, a atribuição de política **AuditIfNotExists** determina se a atribuição do hóspede é conforme ou não conforme. A ferramenta de validação fornece os resultados ao cliente de Configuração de Hóspedes. O cliente reencaminha os resultados para a Extensão do Hóspede, que os disponibiliza através do fornecedor de recursos de Configuração de Hóspedes.

A Política Azure utiliza a propriedade **compliancede** os fornecedores de recursos de configuração de hóspedes para reportar a conformidade no nó **de conformidade.** Para mais informações, consulte [a obtenção](../how-to/get-compliance-data.md)de dados de conformidade .

> [!NOTE]
> A política **DeployIfNotExists** é necessária para que a política **AuditIfNotExist** a devolução dos resultados. Sem o **DeployIfNotExists,** a política **AuditIfNotExists** mostra os recursos "0 de 0" como estatuto.

Todas as políticas incorporadas para configuração de hóspedes estão incluídas numa iniciativa para agrupar as definições para uso em atribuições. A iniciativa incorporada denominada _ \[Pré-visualização\]: Audite a segurança da palavra-passe dentro das máquinas Linux e Windows_ contém 18 políticas. Existem seis pares **ImplementIfNotExists** e **AuditIfNotExists** para Windows e três pares para o Linux. A lógica de [definição](definition-structure.md#policy-rule) de política valida que apenas o sistema operativo-alvo é avaliado.

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditoria das definições do sistema operativo seguindo as linhas de base da indústria

Uma iniciativa da Política Azure oferece a capacidade de auditar as definições do sistema operativo seguindo uma "linha de base". A definição, _ \[Pré-visualização\]: Auditar VMs windows que não correspondam_ às definições de base de segurança do Azure inclui um conjunto de regras baseadas na Política de Grupo de Diretório Ativo.

A maioria das definições estão disponíveis como parâmetros. Os parâmetros permitem personalizar o que é auditado. Alinhe a política com os seus requisitos ou mapeie a política para informações de terceiros, como normas regulamentares do setor.

Alguns parâmetros suportam uma gama de valor inteiro. Por exemplo, a definição da Idade máxima da palavra-passe poderia auditar a definição efetiva da Política do Grupo. Uma gama de "1,70" confirmaria que os utilizadores são obrigados a alterar as suas palavras-passe pelo menos a cada 70 dias, mas não menos do que um dia.

Se atribuir a apólice utilizando um modelo de implementação do Gestor de Recursos Azure, utilize um ficheiro de parâmetros para gerir exceções. Verifique os ficheiros num sistema de controlo de versão, como git. Comentários sobre alterações de ficheiros fornecem provas de que uma atribuição é uma exceção ao valor esperado.

#### <a name="applying-configurations-using-guest-configuration"></a>Aplicação de configurações usando a configuração do hóspede

A mais recente funcionalidade da Política Azure configura as configurações dentro das máquinas. A definição _Configurar o fuso horário nas máquinas Windows_ altera a máquina configurando o fuso horário.

Ao atribuir definições que comecem com _configure,_ deve também atribuir a definição _De si de implementação de pré-requisitos para ativar a Política_de Configuração do Hóspede nos VMs do Windows . Pode combinar estas definições numa iniciativa, se quiser.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Atribuindo políticas a máquinas fora de Azure

As políticas de Auditoria disponíveis para configuração de hóspedes incluem o tipo de recursos **Microsoft.HybridCompute/machines.** Quaisquer máquinas a bordo do [Azure Arc para Servidores](../../../azure-arc/servers/overview.md) que estejam no âmbito da atribuição de políticas estão automaticamente incluídas.

### <a name="multiple-assignments"></a>Múltiplas atribuições

As políticas de configuração do hóspede atualmente apenas suportam a atribuição da mesma Atribuição de Hóspedes uma vez por máquina, mesmo que a atribuição de Política utilize diferentes parâmetros.

## <a name="client-log-files"></a>Ficheiros de registo do cliente

A extensão de Configuração do Hóspede escreve ficheiros de registo para os seguintes locais:

Janelas:`C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux:`/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Onde `<version>` se refere ao número da versão atual.

### <a name="collecting-logs-remotely"></a>Recolha de registos remotamente

O primeiro passo para resolver as configurações ou módulos de configuração do hóspede deve ser utilizar o `Test-GuestConfigurationPackage` cmdlet seguindo os passos de como criar uma política de auditoria personalizada de configuração de [hóspedes para windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Se isso não for bem sucedido, recolher registos de clientes pode ajudar a diagnosticar problemas.

#### <a name="windows"></a>Windows

Capture informações de ficheiros de registo utilizando [o Comando de Execução Azure VM,](../../../virtual-machines/windows/run-command.md)o seguinte exemplo o script PowerShell pode ser útil.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Capture informações de ficheiros de registo utilizando [o Comando de Execução Azure VM](../../../virtual-machines/linux/run-command.md), o seguinte exemplo de que o script Bash pode ser útil.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Amostras de configuração de convidados

As amostras de política incorporadas da Configuração do Hóspede estão disponíveis nos seguintes locais:

- [Definições políticas incorporadas - Configuração de Hóspedes](../samples/built-in-policies.md#guest-configuration)
- [Iniciativas incorporadas - Configuração de Hóspedes](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy amostras GitHub repo](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Passos seguintes

- Saiba como ver os detalhes de cada definição a partir da vista de conformidade de [configuração](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration) do hóspede
- Reveja exemplos nas [amostras da Política Azure.](../samples/index.md)
- Reveja a [estrutura de definição do Azure Policy](definition-structure.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Compreender como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como obter dados de [conformidade.](../how-to/get-compliance-data.md)
- Aprenda a [remediar recursos não conformes](../how-to/remediate-resources.md).
- Reveja o que é um grupo de gestão com organizar os seus recursos com grupos de [gestão Azure.](../../management-groups/overview.md)
