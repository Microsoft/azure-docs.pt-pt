---
title: Compreender como o conteúdo de uma máquina virtual de auditoria
description: Saiba como política do Azure utiliza a configuração de convidado para fazer auditoria definições dentro de uma máquina virtual do Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/27/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: e6621172734ea02f971bd5064b403ad4844210a3
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960771"
---
# <a name="understand-azure-policys-guest-configuration"></a>Compreender a configuração de convidado do Azure Policy

Para além de auditoria e [remediar](../how-to/remediate-resources.md) recursos do Azure, Azure Policy podem auditar as definições de dentro de uma máquina virtual. A validação é executada a extensão de configuração do convidado e o cliente. A extensão, por meio do cliente, valida as definições como a configuração do sistema operativo, configuração da aplicação ou presença, definições de ambiente e muito mais.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Extensão e de cliente

Para fazer auditoria definições dentro de uma máquina virtual, um [extensão da máquina virtual](../../../virtual-machines/extensions/overview.md) está ativada. A extensão transfere a atribuição de política aplicável e a definição de configuração correspondente.

### <a name="register-guest-configuration-resource-provider"></a>Registar fornecedor de recursos de configuração de convidado

Antes de poder utilizar configuração de convidado, tem de registar o fornecedor de recursos. Pode registrar através do portal ou através do PowerShell. O fornecedor de recursos é registado automaticamente se a atribuição de uma política de configuração de convidado é feita através do portal.

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

O cliente de configuração de convidado verifica a existência de novo conteúdo a cada 5 minutos. Depois de uma atribuição de convidado é recebida, as definições são verificadas num intervalo de 15 minutos. Os resultados são enviados para o fornecedor de recursos de configuração de convidado assim que a auditoria for concluída. Quando uma política [acionador de avaliação](../how-to/get-compliance-data.md#evaluation-triggers) ocorre, o estado da máquina é escrito para o fornecedor de recursos de configuração de convidado. Este evento faz com que o Azure Policy avaliar as propriedades do Azure Resource Manager. Uma edição de avaliação de política de por demanda obtém o valor mais recente do fornecedor de recursos de configuração de convidado. No entanto, ele não aciona uma nova auditoria da configuração da máquina virtual.

### <a name="supported-client-types"></a>Tipos de cliente suportados

A tabela seguinte mostra uma lista de sistemas operativos suportados nas imagens do Azure:

|Publicador|Nome|Versões|
|-|-|-|
|Canónico|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|Centro de dados de 2012, 2012 R2 Datacenter, 2016 Datacenter|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SUSE|SLES|12 SP3|

> [!IMPORTANT]
> Configuração do convidado pode auditar qualquer servidor com um SO suportado.  Se gostaria de servidores que utilizam uma imagem personalizada de auditoria, precisa duplicar a **DeployIfNotExists** definição e modificar a **se** secção para incluir as propriedades da imagem.

### <a name="unsupported-client-types"></a>Tipos de cliente não suportada

A tabela seguinte lista os sistemas operativos que não são suportados:

|Sistema operativo|Notas|
|-|-|
|Cliente Windows | Não são suportados sistemas operativos de cliente (por exemplo, o Windows 7 e Windows 10).
|Servidor de Nano do Windows Server 2016 | Não suportado.|

### <a name="guest-configuration-extension-network-requirements"></a>Requisitos de rede de extensão da configuração de convidado

Para comunicar com o fornecedor de recursos de configuração de convidado no Azure, as máquinas virtuais exigem acesso de saída para os datacenters do Azure na porta **443**. Se estiver a utilizar uma rede virtual privada no Azure e não permitir o tráfego de saída, exceções devem ser configuradas com [grupo de segurança de rede](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) regras. Neste momento, a etiqueta de serviço não existe para a configuração de convidado de política do Azure.

Para listas de endereços IP, pode baixar [intervalos de IP do Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Este ficheiro é atualizado semanalmente e tem os intervalos implementados atualmente e as alterações futuras para os intervalos de IP. Apenas terá de permitir o acesso de saída para os IPs nas regiões onde as suas VMs são implementadas.

> [!NOTE]
> O ficheiro XML de endereço IP do Datacenter do Azure apresenta uma lista de intervalos de endereços IP que são utilizados nos datacenters do Microsoft Azure. O arquivo inclui os intervalos de computação, SQL e armazenamento.
> Um ficheiro atualizado é publicado semanalmente. O ficheiro reflete os intervalos implementados atualmente e as alterações futuras para os intervalos de IP. Os novo intervalos que aparecem no ficheiro não são utilizados nos centros de dados para, pelo menos, uma semana.
> É uma boa idéia para transferir o ficheiro XML novo todas as semanas. Em seguida, atualize o seu site para identificar corretamente os serviços em execução no Azure. Os utilizadores do Azure ExpressRoute devem observar que este ficheiro é utilizado para atualizar o anúncio de protocolo BGP (Border Gateway) do espaço Azure na primeira semana de cada mês.

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição de configuração de convidado

Cada auditoria executar pela configuração de convidado requer duas definições de política, uma **DeployIfNotExists** definição e uma **auditar** definição. O **DeployIfNotExists** definição é utilizada para preparar a máquina virtual com o agente de configuração de convidados e outros componentes para suportar a [ferramentas de validação](#validation-tools).

O **DeployIfNotExists** definição de política valida e corrige os seguintes itens:

- Validar a máquina virtual foi atribuída uma configuração para avaliar. Se nenhuma atribuição está atualmente presente, obter a atribuição e preparar a máquina virtual por:
  - Autenticar para a máquina virtual utilizando um [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalar a versão mais recente dos **Microsoft.GuestConfiguration** extensão
  - A instalar [ferramentas de validação](#validation-tools) e as dependências, se necessário

Se o **DeployIfNotExists** atribuição está em não conformidade, uma [as tarefas de remediação](../how-to/remediate-resources.md#create-a-remediation-task) pode ser utilizado.

Uma vez a **DeployIfNotExists** atribuição está em conformidade, o **auditoria** atribuição de política utiliza as ferramentas de validação local para determinar se a atribuição de configuração está em conformidade ou não compatível.
A ferramenta de validação fornece os resultados para o cliente de configuração de convidado. O cliente reencaminha os resultados para a extensão de convidado, o que as torna disponíveis por meio do Provedor de recursos de configuração de convidado.

Política do Azure utiliza os fornecedores de recursos de configuração de convidado **complianceStatus** propriedade para reportar a conformidade no **conformidade** nó. Para obter mais informações, consulte [obtenção de dados de conformidade](../how-to/getting-compliance-data.md).

> [!NOTE]
> Para cada definição de configuração de convidado, tanto o **DeployIfNotExists** e **auditoria** definições de política tem de existir.

Todas as políticas incorporadas para a configuração de convidado são incluídas numa iniciativa para as definições para utilizam em atribuições de grupo. O incorporado *[pré-visualização]: Definições de segurança de palavra-passe dentro de máquinas virtuais do Linux e Windows de auditoria* iniciativa contém 18 políticas. Existem seis **DeployIfNotExists** e **auditoria** pares de definição de política para Windows e três pares para Linux.
Para cada um, o **DeployIfNotExists** [regra de definição de política](definition-structure.md#policy-rule) limita os sistemas avaliados.

## <a name="next-steps"></a>Passos Seguintes

- Reveja exemplos em [exemplos do Azure Policy](../samples/index.md)
- Reveja o [estrutura de definição de política](definition-structure.md)
- Revisão [Noções básicas sobre os efeitos de política](effects.md)
- Compreender como [criar políticas programaticamente](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md)
- Saiba como [remediar recursos não compatíveis](../how-to/remediate-resources.md)
- Rever o que é um grupo de gestão, com [Organizar os recursos com grupos de gestão do Azure](../../management-groups/index.md)
