---
title: Entenda como auditar o conteúdo de uma máquina virtual
description: Saiba como política do Azure utiliza a configuração de convidado para fazer auditoria definições dentro de uma máquina virtual do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 6f51d2907738f49ace559f1b127458eda71de287
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624091"
---
# <a name="understand-azure-policys-guest-configuration"></a>Compreender a configuração de convidado do Azure Policy

Além de auditar e [corrigir](../how-to/remediate-resources.md) recursos do Azure, Azure Policy pode auditar configurações dentro de uma máquina virtual. A validação é executada a extensão de configuração do convidado e o cliente. A extensão, por meio do cliente, valida as definições como a configuração do sistema operativo, configuração da aplicação ou presença, definições de ambiente e muito mais.

Neste momento, Azure Policy configuração de convidado executa apenas uma auditoria de configurações dentro da máquina.
Ainda não é possível aplicar as configurações.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Extensão e de cliente

Para fazer auditoria definições dentro de uma máquina virtual, um [extensão da máquina virtual](../../../virtual-machines/extensions/overview.md) está ativada. A extensão transfere a atribuição de política aplicável e a definição de configuração correspondente.

### <a name="register-guest-configuration-resource-provider"></a>Registar fornecedor de recursos de configuração de convidado

Antes de poder utilizar configuração de convidado, tem de registar o fornecedor de recursos. Pode registrar através do portal ou através do PowerShell. O provedor de recursos será registrado automaticamente se a atribuição de uma política de configuração de convidado for feita por meio do Portal.

#### <a name="registration---portal"></a>Registo - Portal

Para registar o fornecedor de recursos para a configuração de convidado através do portal do Azure, siga estes passos:

1. Iniciar o portal do Azure e clique em **todos os serviços**. Procure e selecione **subscrições**.

1. Localize e clique na subscrição que pretende ativar a configuração de convidado para.

1. No menu à esquerda do **subscrição** página, clique em **fornecedores de recursos**.

1. Filtrar por ou rolar até localizar **Microsoft.GuestConfiguration**, em seguida, clique em **registar** na mesma linha.

#### <a name="registration---powershell"></a>Registo - PowerShell

Para registar o fornecedor de recursos para a configuração de convidado através do PowerShell, execute o seguinte comando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Ferramentas de validação

Dentro da máquina virtual, o cliente de configuração de convidado utiliza ferramentas locais para executar a auditoria.

A tabela seguinte mostra uma lista das ferramentas de locais usadas em cada sistema operacional compatível:

|Sistema operativo|Ferramenta de validação|Notas|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby e Python é instalados pela extensão da configuração de convidado. |

### <a name="validation-frequency"></a>Frequência de validação

O cliente de configuração de convidado verifica o novo conteúdo a cada 5 minutos. Depois que uma atribuição de convidado é recebida, as configurações são verificadas em um intervalo de 15 minutos. Os resultados são enviados para o provedor de recursos de configuração de convidado assim que a auditoria é concluída. Quando ocorre um [gatilho de avaliação](../how-to/get-compliance-data.md#evaluation-triggers) de política, o estado do computador é gravado no provedor de recursos de configuração do convidado. Isso faz com que Azure Policy avalie as propriedades de Azure Resource Manager. Uma avaliação de Azure Policy sob demanda recupera o valor mais recente do provedor de recursos de configuração de convidado. No entanto, ele não aciona uma nova auditoria da configuração na máquina virtual.

### <a name="supported-client-types"></a>Tipos de cliente suportados

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

### <a name="guest-configuration-extension-network-requirements"></a>Requisitos de rede da extensão de configuração do convidado

Para se comunicar com o provedor de recursos de configuração de convidado no Azure, as máquinas virtuais exigem acesso de saída aos datacenters do Azure na porta **443**. Se você estiver usando uma rede virtual privada no Azure e não permitir o tráfego de saída, as exceções deverão ser configuradas usando regras de [grupo de segurança de rede](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) . Neste momento, uma marca de serviço não existe para Azure Policy configuração de convidado.

Para listas de endereços IP, você pode baixar [Microsoft Azure intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653)do datacenter. Esse arquivo é atualizado semanalmente e tem os intervalos atualmente implantados e as alterações futuras nos intervalos de IP. Você só precisa permitir o acesso de saída aos IPs nas regiões em que suas VMs são implantadas.

> [!NOTE]
> O arquivo XML do endereço IP do datacenter do Azure lista os intervalos de endereços IP que são usados nos data centers Microsoft Azure. O arquivo inclui intervalos de computação, SQL e armazenamento. Um arquivo atualizado é lançado semanalmente. O arquivo reflete os intervalos atualmente implantados e as alterações futuras nos intervalos de IP. Novos intervalos que aparecem no arquivo não são usados nos data centers por pelo menos uma semana. É uma boa ideia baixar o novo arquivo XML a cada semana. Em seguida, atualize seu site para identificar corretamente os serviços em execução no Azure. Os usuários do Azure ExpressRoute devem observar que esse arquivo é usado para atualizar o anúncio de Border Gateway Protocol (BGP) do espaço do Azure na primeira semana de cada mês.

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição de configuração de convidado

Cada execução de auditoria pela configuração de convidado requer duas definições de política, uma definição de **DeployIfNotExists** e uma definição de **auditoria** . A definição **DeployIfNotExists** é usada para preparar a máquina virtual com o agente de configuração convidado e outros componentes para dar suporte às [ferramentas de validação](#validation-tools).

O **DeployIfNotExists** definição de política valida e corrige os seguintes itens:

- Validar a máquina virtual foi atribuída uma configuração para avaliar. Se nenhuma atribuição está atualmente presente, obter a atribuição e preparar a máquina virtual por:
  - Autenticar para a máquina virtual utilizando um [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalar a versão mais recente dos **Microsoft.GuestConfiguration** extensão
  - A instalar [ferramentas de validação](#validation-tools) e as dependências, se necessário

Se a atribuição de **DeployIfNotExists** não for compatível, uma [tarefa de correção](../how-to/remediate-resources.md#create-a-remediation-task) poderá ser usada.

Depois que a atribuição de **DeployIfNotExists** for compatível, a atribuição de política de **auditoria** usará as ferramentas de validação locais para determinar se a atribuição de configuração está em conformidade ou sem conformidade.
A ferramenta de validação fornece os resultados para o cliente de configuração de convidado. O cliente reencaminha os resultados para a extensão de convidado, o que as torna disponíveis por meio do Provedor de recursos de configuração de convidado.

Política do Azure utiliza os fornecedores de recursos de configuração de convidado **complianceStatus** propriedade para reportar a conformidade no **conformidade** nó. Para obter mais informações, consulte [obtenção de dados de conformidade](../how-to/getting-compliance-data.md).

> [!NOTE]
> A política **DeployIfNotExists** é necessária para que a política de **auditoria** retorne os resultados.
> Sem o **DeployIfNotExists**, a política de **auditoria** mostra "0 de 0" recursos como status.

Todas as políticas incorporadas para a configuração de convidado são incluídas numa iniciativa para as definições para utilizam em atribuições de grupo. A iniciativa interna chamada *[Preview]: As configurações de segurança de senha de auditoria nas máquinas* virtuais Linux e Windows contêm 18 políticas. Há seis **DeployIfNotExists** e pares de **auditoria** para o Windows e três pares para Linux. Valida a apenas o destino em cada caso, a lógica dentro da definição do sistema operativo é avaliado com base no [regra de política](definition-structure.md#policy-rule) definição.

## <a name="multiple-assignments"></a>Várias atribuições

Atualmente, as políticas de configuração de convidado só dão suporte à atribuição da mesma entrada de convidado uma vez por máquina virtual, mesmo que a atribuição de política use parâmetros diferentes.

## <a name="client-log-files"></a>Arquivos de log do cliente

A extensão de configuração de convidado grava arquivos de log nos seguintes locais:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Em `<version>` que refere-se ao número de versão atual.

## <a name="guest-configuration-samples"></a>Exemplos de configuração de convidado

Exemplos de configuração de convidado de política estão disponíveis nos seguintes locais:

- [Índice de exemplos – configuração de convidado](../samples/index.md#guest-configuration)
- [Repositório GitHub de exemplos de Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration).

## <a name="next-steps"></a>Passos Seguintes

- Examine exemplos em [exemplos de Azure Policy](../samples/index.md).
- Reveja a [estrutura de definição do Azure Policy](definition-structure.md).
- Veja [Compreender os efeitos do Policy](effects.md).
- Entenda como [criar políticas](../how-to/programmatically-create.md)programaticamente.
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md).
- Saiba como [corrigir recursos sem conformidade](../how-to/remediate-resources.md).
- Examine o que é um grupo de gerenciamento e [Organize seus recursos com grupos de gerenciamento do Azure](../../management-groups/index.md).
