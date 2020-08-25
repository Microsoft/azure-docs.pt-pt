---
title: Aprenda a auditar o conteúdo das máquinas virtuais
description: Saiba como a Azure Policy utiliza o agente de Configuração de Convidados para auditar as definições dentro de máquinas virtuais.
ms.date: 08/07/2020
ms.topic: conceptual
ms.openlocfilehash: 951960793ebda50fdb87d266c4dc8561f2fcd70f
ms.sourcegitcommit: afa1411c3fb2084cccc4262860aab4f0b5c994ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2020
ms.locfileid: "88756695"
---
# <a name="understand-azure-policys-guest-configuration"></a>Compreender a Configuração de Convidado do Azure Policy

A Azure Policy pode auditar as definições dentro de uma máquina, tanto para máquinas que executam em Azure como [arc connected Machines](../../../azure-arc/servers/overview.md). A validação é executada pela extensão da Configuração de Convidado e pelo cliente. A extensão, através do cliente, valida as definições, como:

- A configuração do sistema operativo
- Presença ou configuração da aplicação
- Definições do ambiente

Neste momento, a maioria das políticas de Configuração de Convidado do Azure Policy audita apenas as definições na máquina virtual.
Não se aplica às configurações. A exceção é uma política incorporada [referida abaixo.](#applying-configurations-using-guest-configuration)

## <a name="enable-guest-configuration"></a>Ativar a configuração do hóspede

Para auditar o estado das máquinas no seu ambiente, incluindo máquinas em Azure e Arc Connected Machines, reveja os seguintes detalhes.

## <a name="resource-provider"></a>Fornecedor de recursos

Antes de poder utilizar a Configuração do Convidado, tem de registar o fornecedor de recursos. O fornecedor de recursos é registado automaticamente se a atribuição de uma política de Configuração de Hóspedes for feita através do portal. Pode registar-se manualmente através do [portal](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) [, Azure PowerShell,](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)ou [Azure CLI](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-cli).

## <a name="deploy-requirements-for-azure-virtual-machines"></a>Implementar requisitos para máquinas virtuais Azure

Para auditar as definições dentro de uma máquina, está ativada uma [extensão de máquina virtual](../../../virtual-machines/extensions/overview.md) e a máquina deve ter uma identidade gerida pelo sistema. A extensão transfere a atribuição da política aplicável e a definição da configuração correspondente. A identidade é utilizada para autenticar a máquina à medida que lê e escreve para o serviço de Configuração de Hóspedes. A extensão não é necessária para máquinas ligadas ao arco porque está incluída no agente da Máquina Ligada ao Arco.

> [!IMPORTANT]
> A extensão de Configuração do Hóspede e uma identidade gerida são necessárias para auditar máquinas virtuais Azure. Para implementar a extensão em escala, atribua a seguinte iniciativa política:
> 
> `Deploy prerequisites to enable Guest Configuration policies on virtual machines`

### <a name="limits-set-on-the-extension"></a>Limites definidos na extensão

Para limitar a extensão de aplicações de impacto que estão a decorrer dentro da máquina, a Configuração do Hóspede não pode exceder mais de 5% do CPU. Esta limitação existe tanto para definições incorporadas como personalizadas. O mesmo se aplica ao serviço de Configuração de Hóspedes no agente Da Máquina Ligada ao Arco.

### <a name="validation-tools"></a>Ferramentas de validação

Dentro da máquina, o cliente de Configuração de Hóspedes utiliza ferramentas locais para executar a auditoria.

A tabela a seguir mostra uma lista das ferramentas locais utilizadas em cada sistema operativo suportado. Para conteúdos incorporados, a Configuração do Convidado lida com o carregamento destas ferramentas automaticamente.

|Sistema operativo|Ferramenta de validação|Notas|
|-|-|-|
|Windows|[Configuração de estado desejada powerShell](/powershell/scripting/dsc/overview/overview) v2| Carregado lateralmente numa pasta apenas utilizada pela Azure Policy. Não entrará em conflito com o Windows PowerShell DSC. PowerShell Core não é adicionado ao caminho do sistema.|
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Instala a versão 2.2.61 do Chef InSpec em localização padrão e adicionada ao caminho do sistema. Também estão instaladas dependências para o pacote InSpec, incluindo Ruby e Python. |

### <a name="validation-frequency"></a>Frequência de validação

O cliente de Configuração de Hóspedes verifica novos conteúdos a cada 5 minutos. Uma vez recebida uma atribuição de hóspedes, as definições para essa configuração são novamente verificadas num intervalo de 15 minutos. Os resultados são enviados ao fornecedor de recursos de Configuração de Convidados quando a auditoria estiver concluída. Quando ocorre um gatilho de [avaliação](../how-to/get-compliance-data.md#evaluation-triggers) de política, o estado da máquina é escrito ao fornecedor de recursos de Configuração de Convidados. Esta atualização faz com que a Azure Policy avalie as propriedades do Gestor de Recursos Azure. Uma avaliação a pedido da Azure Policy recupera o valor mais recente do fornecedor de recursos de Configuração de Convidados. No entanto, não desencadeia uma nova auditoria da configuração dentro da máquina.

## <a name="supported-client-types"></a>Tipos de clientes suportados

As políticas de configuração dos hóspedes incluem novas versões. As versões mais antigas dos sistemas operativos disponíveis no Azure Marketplace estão excluídas se o agente de Configuração de Hóspedes não for compatível.
A tabela a seguir mostra uma lista de sistemas operativos suportados em imagens Azure:

|Publisher|Nome|Versões|
|-|-|-|
|Canónico|Ubuntu Server|14.04 e mais tarde|
|Credativ|Debian|8 e mais tarde|
|Peering da Microsoft|Windows Server|2012 e mais tarde|
|Peering da Microsoft|Cliente Windows|Windows 10|
|OpenLogic|CentOS|7.3 e mais tarde|
|Red Hat|Red Hat Enterprise Linux|7.4 - 7.8|
|Suse|SLES|12 SP3-SP5|

As imagens de máquinas virtuais personalizadas são suportadas pelas políticas de Configuração do Hóspede, desde que sejam um dos sistemas operativos na tabela acima.

## <a name="network-requirements"></a>Requisitos da rede

As máquinas virtuais em Azure podem utilizar o adaptador de rede local ou um link privado para comunicar com o serviço de Configuração do Hóspede.

As máquinas Azure Arc conectam-se utilizando a infraestrutura de rede no local para chegar aos serviços Azure e reportar o estado de conformidade.

### <a name="communicate-over-virtual-networks-in-azure"></a>Comunicar através de redes virtuais em Azure

Máquinas virtuais que utilizam redes virtuais para comunicação exigirão acesso de saída aos datacenters da Azure na `443` porta. Se estiver a utilizar uma rede virtual privada em Azure que não permita tráfego de saída, configure exceções com as regras do Grupo de Segurança de Rede. A etiqueta de serviço "GuestAndHybridManagement" pode ser usada para fazer referência ao serviço de Configuração de Hóspedes.

### <a name="communicate-over-private-link-in-azure"></a>Comunicar sobre ligação privada em Azure

As máquinas virtuais podem utilizar [link privado](../../../private-link/private-link-overview.md) para comunicação ao serviço de Configuração do Hóspede. Aplique a etiqueta com o nome `EnablePrivateNeworkGC` e o valor para `TRUE` ativar esta função. A etiqueta pode ser aplicada antes ou depois das políticas de Configuração do Hóspede serem aplicadas à máquina.

O tráfego é encaminhado usando o [endereço IP público virtual](../../../virtual-network/what-is-ip-address-168-63-129-16.md) Azure para estabelecer um canal seguro e autenticado com recursos da plataforma Azure.

### <a name="azure-arc-connected-machines"></a>Máquinas ligadas a Azure Arc

Os nós localizados fora de Azure que estão ligados pelo Azure Arc requerem conectividade ao serviço de Configuração do Hóspede. Detalhes sobre os requisitos de rede e procuração fornecidos na documentação do [Arco Azure](../../../azure-arc/servers/overview.md).

Para comunicar com o fornecedor de recursos de Configuração de Hóspedes em Azure, as máquinas requerem acesso de saída aos datacenters Azure na porta **443**. Se uma rede em Azure não permitir o tráfego de saída, configure exceções com as regras [do Grupo de Segurança de Rede.](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) A [etiqueta de serviço](../../../virtual-network/service-tags-overview.md) "GuestAndHybridManagement" pode ser usada para fazer referência ao serviço de Configuração de Hóspedes.

Para servidores ligados ao Arco em centros de dados privados, permita o tráfego utilizando os seguintes padrões:

- Porto: Apenas TCP 443 necessário para acesso à Internet de saída
- URL global: `*.guestconfiguration.azure.com`

## <a name="managed-identity-requirements"></a>Requisitos de identidade geridos

Definições de política na iniciativa [Implementar pré-requisitos para permitir políticas de configuração de hóspedes em máquinas virtuais](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F12794019-7a00-42cf-95c2-882eed337cc8) permitem uma identidade gerida atribuída ao sistema, se não existir. Há duas definições políticas na iniciativa que gerem a criação de identidade. As condições IF nas definições de política garantem o comportamento correto com base no estado atual do recurso da máquina em Azure.

Se a máquina não tiver atualmente identidades geridas, a política eficaz será: [ \[ \] Pré-visualização : Adicionar identidade gerida atribuída ao sistema para permitir atribuições de Configuração de Convidados em máquinas virtuais sem identidades](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3cf2ab00-13f1-4d0c-8971-2ac904541a7e)

Se a máquina tiver atualmente uma identidade de sistema atribuída ao utilizador, a política eficaz será: [ \[ \] Pré-visualização : Adicionar identidade gerida atribuída ao sistema para permitir atribuições de Configuração de Hóspedes em máquinas virtuais com uma identidade atribuída ao utilizador](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F497dff13-db2a-4c0f-8603-28fa3b331ab6)

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição de configuração do hóspede

As políticas de configuração do hóspede utilizam o efeito **AuditIfNotExists.** Quando a definição é atribuída, um serviço back-end lida automaticamente com o ciclo de vida de todos os requisitos no fornecedor de `Microsoft.GuestConfiguration` recursos Azure.

As políticas **auditIfNotExists** não devolverão os resultados de conformidade até que todos os requisitos sejam cumpridos na máquina. As necessidades são descritas na secção [Os requisitos de implantação das máquinas virtuais Azure](#deploy-requirements-for-azure-virtual-machines)

> [!IMPORTANT]
> Num lançamento prévio da Configuração de Convidados, foi necessária uma iniciativa para combinar definições **de DeployIfNoteExists** e **AuditIfNotExists.** As definições **de DeployIfNotExists** já não são necessárias. As definições e os intiaitivos estão `[Deprecated]` rotulados, mas as atribuições existentes continuarão a funcionar.
>
> É necessário um passo manual. Se já atribuiu as iniciativas políticas na `Guest Configuration` categoria, elimine a atribuição de políticas e atribua a nova definição. As políticas de configuração do hóspede têm um padrão de nome da seguinte forma: `Audit <Windows/Linux> machines that <non-compliant condition>`

A Azure Policy utiliza a conformidade do fornecedor de recursos de configuração do **hóspedeSse** a propriedade para reportar a conformidade no nó **compliance.** Para obter mais informações, consulte [obter dados de conformidade.](../how-to/get-compliance-data.md)

#### <a name="auditing-operating-system-settings-following-industry-baselines"></a>Auditoria das definições do sistema operativo seguindo as linhas de base da indústria

Uma iniciativa da Azure Policy proporciona a capacidade de auditar as definições do sistema operativo seguindo uma "linha de base". A definição, _ \[ Preview : Audit Windows \] VMs que não correspondem às definições de base de segurança Azure_ inclui um conjunto de regras baseadas na Política de Grupo de Diretório Ativo.

A maioria das definições estão disponíveis como parâmetros. Os parâmetros permitem personalizar o que é auditado.
Alinhe a política com os seus requisitos ou mapeeia a política para informações de terceiros, tais como normas regulamentares da indústria.

Alguns parâmetros suportam uma gama de valor inteiro. Por exemplo, a definição maximum password Age poderia auditar a definição eficaz da Política de Grupo. Uma gama de "1,70" confirmaria que os utilizadores são obrigados a alterar as suas palavras-passe pelo menos a cada 70 dias, mas não menos do que um dia.

Se atribuir a política utilizando um modelo de Gestor de Recursos Azure (modelo ARM), utilize um ficheiro de parâmetros para gerir exceções. Consulte os ficheiros num sistema de controlo de versão como o Git. Comentários sobre alterações de ficheiros fornecem provas de que uma atribuição é uma exceção ao valor esperado.

#### <a name="applying-configurations-using-guest-configuration"></a>Aplicação de configurações usando a Configuração do Convidado

Apenas a definição _Configure o fuso horário nas máquinas Windows_ faz alterações na máquina configurando o fuso horário. As definições de política personalizadas para configurar configurações dentro de máquinas não são suportadas.

Ao atribuir definições que comecem com _configuração,_ também deve atribuir a definição _Implementar pré-requisitos para ativar a Política de Configuração de Hóspedes nos VMs do Windows_. Pode combinar estas definições numa iniciativa, se quiser.

> [!NOTE]
> A política de fuso horário incorporado é a única definição que suporta configurações configurantes dentro de máquinas e políticas personalizadas que configuram configurações dentro das máquinas não são suportadas.

#### <a name="assigning-policies-to-machines-outside-of-azure"></a>Atribuir políticas a máquinas fora de Azure

As políticas de auditoria disponíveis para configuração de hóspedes incluem o tipo de recurso **Microsoft.HybridCompute/machines.** Quaisquer máquinas a bordo do [Azure Arc para servidores](../../../azure-arc/servers/overview.md) que estejam no âmbito da atribuição de políticas são automaticamente incluídas.

### <a name="multiple-assignments"></a>Múltiplas atribuições

Atualmente, as políticas de configuração dos hóspedes apenas suportam a atribuição da mesma Atribuição de Hóspedes uma vez por máquina, mesmo que a atribuição de Política utilize parâmetros diferentes.

## <a name="client-log-files"></a>Ficheiros de registo de clientes

A extensão de Configuração do Hóspede escreve ficheiros de registo para os seguintes locais:

Janelas: `C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log`

Linux: `/var/lib/GuestConfig/gc_agent_logs/gc_agent.log`

Quando `<version>` se refere ao número da versão atual.

### <a name="collecting-logs-remotely"></a>Recolha de troncos remotamente

O primeiro passo para resolver configurações ou módulos de configuração de hóspedes deve ser utilizar o `Test-GuestConfigurationPackage` cmdlet seguindo os passos para [criar uma política de auditoria de configuração personalizada de hóspedes para o Windows](../how-to/guest-configuration-create.md#step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows).
Se isso não for bem sucedido, recolher registos de clientes pode ajudar a diagnosticar problemas.

#### <a name="windows"></a>Windows

Capturar informações de ficheiros de registo utilizando o [Comando de Execução Azure VM](../../../virtual-machines/windows/run-command.md), o seguinte exemplo O script PowerShell pode ser útil.

```powershell
$linesToIncludeBeforeMatch = 0
$linesToIncludeAfterMatch = 10
$logPath = 'C:\ProgramData\GuestConfig\gc_agent_logs\gc_agent.log'
Select-String -Path $logPath -pattern 'DSCEngine','DSCManagedEngine' -CaseSensitive -Context $linesToIncludeBeforeMatch,$linesToIncludeAfterMatch | Select-Object -Last 10
```

#### <a name="linux"></a>Linux

Capturar informações de ficheiros de registo usando [o Azure VM Run Command](../../../virtual-machines/linux/run-command.md), o seguinte exemplo, o script Bash pode ser útil.

```Bash
linesToIncludeBeforeMatch=0
linesToIncludeAfterMatch=10
logPath=/var/lib/GuestConfig/gc_agent_logs/gc_agent.log
egrep -B $linesToIncludeBeforeMatch -A $linesToIncludeAfterMatch 'DSCEngine|DSCManagedEngine' $logPath | tail
```

## <a name="guest-configuration-samples"></a>Amostras de configuração de hóspedes

As amostras de política incorporadas da Configuração do Hóspede estão disponíveis nos seguintes locais:

- [Definições de política incorporadas - Configuração de hóspedes](../samples/built-in-policies.md#guest-configuration)
- [Iniciativas incorporadas - Configuração de Hóspedes](../samples/built-in-initiatives.md#guest-configuration)
- [Azure Policy amostras GitHub repo](https://github.com/Azure/azure-policy/tree/master/built-in-policies/policySetDefinitions/Guest%20Configuration)

## <a name="next-steps"></a>Passos seguintes

- Saiba como ver os detalhes de cada definição a partir da [vista de conformidade da Configuração do Convidado](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)
- Rever exemplos nas [amostras da Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](./definition-structure.md).
- Veja [Compreender os efeitos do Policy](./effects.md).
- Entenda como [criar políticas programáticas.](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade.](../how-to/get-compliance-data.md)
- Saiba como [remediar recursos não conformes.](../how-to/remediate-resources.md)
- Reveja o que é um grupo de gestão com [Organizar os seus recursos com grupos de gestão Azure.](../../management-groups/overview.md)
