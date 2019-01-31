---
title: Compreender como executar auditorias dentro de uma máquina virtual
description: Saiba como política do Azure utiliza a configuração de convidado para fazer auditoria definições dentro de uma máquina virtual do Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/29/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 77d99c90e65647a1f4a4efb07ff5520596fa54cf
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295173"
---
# <a name="understand-azure-policys-guest-configuration"></a>Compreender a configuração de convidado do Azure Policy

Para além de auditoria e [remediar](../how-to/remediate-resources.md) recursos do Azure, Azure Policy é capaz de auditoria definições dentro de uma máquina virtual. A validação é executada a extensão de configuração do convidado e o cliente. A extensão, por meio do cliente, valida as definições como a configuração do sistema operativo, configuração da aplicação ou presença, definições de ambiente e muito mais.

> [!IMPORTANT]
> Atualmente, apenas **incorporada** são suportadas políticas com a configuração de convidado.

## <a name="extension-and-client"></a>Extensão e de cliente

Para fazer auditoria definições dentro de uma máquina virtual, um [extensão da máquina virtual](../../../virtual-machines/extensions/overview.md) está ativada. A extensão transfere a atribuição de política aplicável e a definição de configuração correspondente.

### <a name="register-guest-configuration-resource-provider"></a>Registar fornecedor de recursos de configuração de convidado

Antes de poder utilizar configuração de convidado, tem de registar o fornecedor de recursos. Pode registrar através do portal ou através do PowerShell.

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

O cliente de configuração de convidado verifica a existência de novo conteúdo a cada 5 minutos.
Depois de uma atribuição de convidado é recebida, as definições são verificadas num intervalo de 15 minutos.
Os resultados são enviados para o fornecedor de recursos de configuração de convidado assim que a auditoria for concluída.
Quando uma política [acionador de avaliação](../how-to/get-compliance-data.md#evaluation-triggers) ocorre, o estado da máquina é escrito para o fornecedor de recursos de configuração de convidado.
Isso faz com que o Azure Policy avaliar as propriedades do Azure Resource Manager.
Uma edição de avaliação de política de por demanda obtém o valor mais recente do fornecedor de recursos de configuração de convidado.
No entanto, ele não aciona uma nova auditoria da configuração da máquina virtual.

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
> Configuração de convidado não é atualmente suportada nas imagens de máquina virtual personalizada.

### <a name="unsupported-client-types"></a>Tipos de cliente não suportada

A tabela seguinte lista os sistemas operativos que não são suportados:

|Sistema operativo|Notas|
|-|-|
|Cliente Windows | Não são suportados sistemas operativos de cliente (por exemplo, o Windows 7 e Windows 10).
|Servidor de Nano do Windows Server 2016 | Não suportado.|

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definição de configuração de convidado

Cada auditoria executar pela configuração de convidado requer duas definições de política, uma **DeployIfNotExists** e **auditar**. **DeployIfNotExists** serve para preparar a máquina virtual com o agente de configuração de convidados e outros componentes para oferecer suporte a [ferramentas de validação](#validation-tools).

O **DeployIfNotExists** definição de política valida e corrige os seguintes itens:

- Validar a máquina virtual foi atribuída uma configuração para avaliar. Se nenhuma atribuição está atualmente presente, obter a atribuição e preparar a máquina virtual por:
  - Autenticar para a máquina virtual utilizando um [identidade gerida](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalar a versão mais recente dos **Microsoft.GuestConfiguration** extensão
  - A instalar [ferramentas de validação](#validation-tools) e as dependências, se necessário

Uma vez a **DeployIfNotExists** está em conformidade, o **auditoria** definição de política utiliza as ferramentas de validação local para determinar se a atribuição de configuração atribuídos está em conformidade ou não compatível. A ferramenta de validação fornece os resultados para o cliente de configuração de convidado. O cliente reencaminha os resultados para a extensão de convidado, o que as torna disponíveis por meio do Provedor de recursos de configuração de convidado.

Política do Azure utiliza os fornecedores de recursos de configuração de convidado **complianceStatus** propriedade para reportar a conformidade no **conformidade** nó. Para obter mais informações, consulte [obtenção de dados de conformidade](../how-to/getting-compliance-data.md).

> [!NOTE]
> Para cada definição de configuração de convidado, tanto o **DeployIfNotExists** e **auditoria** definições de política tem de existir.

Todas as políticas incorporadas para a configuração de convidado são incluídas numa iniciativa para as definições para utilizam em atribuições de grupo. A iniciativa incorporada com o nome *[pré-visualização]: Definições de segurança de palavra-passe dentro de máquinas virtuais do Linux e Windows de auditoria* contém 18 políticas. Existem seis **DeployIfNotExists** e **auditoria** pares para Windows e três pares para Linux. Valida a apenas o destino em cada caso, a lógica dentro da definição do sistema operativo é avaliado com base no [regra de política](definition-structure.md#policy-rule) definição.

## <a name="next-steps"></a>Passos Seguintes

- Reveja exemplos em [exemplos do Azure Policy](../samples/index.md)
- Reveja o [estrutura de definição de política](definition-structure.md)
- Revisão [Noções básicas sobre os efeitos de política](effects.md)
- Compreender como [criar políticas programaticamente](../how-to/programmatically-create.md)
- Saiba como [obter dados de conformidade](../how-to/getting-compliance-data.md)
- Saiba como [remediar recursos não compatíveis](../how-to/remediate-resources.md)
- Rever o que é um grupo de gestão, com [Organizar os recursos com grupos de gestão do Azure](../../management-groups/index.md)
