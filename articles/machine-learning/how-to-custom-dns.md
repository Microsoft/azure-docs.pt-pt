---
title: Use o servidor DNS personalizado
titleSuffix: Azure Machine Learning
description: Como configurar um servidor DNS personalizado para trabalhar com um espaço de trabalho de Aprendizagem de Máquinas Azure e ponto final privado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 10/05/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 1d215c9564d89e5bd410e68839807f5c2c752356
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91828637"
---
# <a name="how-to-use-your-workspace-with-a-custom-dns-server"></a>Como utilizar o seu espaço de trabalho com um servidor DNS personalizado

Ao utilizar o Azure Machine Learning com uma rede virtual, [existem várias formas de lidar com a resolução do nome DNS](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances). Por predefinição, o Azure lida automaticamente com a resolução de nomes para o seu espaço de trabalho e ponto final privado. No entanto, __ao utilizar o seu próprio servidor DNS personalizado,__ tem de criar manualmente entradas DNS para o espaço de trabalho.

> [!IMPORTANT]
> Este artigo abrange apenas como encontrar os endereços de domínio totalmente qualificados (FQDN) e IP para estas entradas, não fornece informações sobre a configuração dos registos DNS para estes itens. Consulte a documentação do seu software DNS para obter informações sobre como adicionar registos.

## <a name="prerequisites"></a>Pré-requisitos

- Uma Rede Virtual Azure que utiliza [o seu próprio servidor DNS](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server).

- Um espaço de trabalho de aprendizagem automática Azure com um ponto final privado. Para obter mais informações, consulte [Criar um espaço de trabalho para aprendizagem de máquinas Azure.](how-to-manage-workspace.md)

- Familiaridade com a utilização [do isolamento da rede durante o treino & inferência](how-to-enable-virtual-network.md).

- Opcionalmente, [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) ou [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="find-the-ip-addresses"></a>Encontre os endereços IP

A lista que se segue contém os nomes de domínio totalmente qualificados (FQDN) utilizados pelo seu espaço de trabalho e ponto final privado:

* `<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.studio.workspace.<region>.api.azureml.ms`
* `cert-<workspace-GUID>.workspace.<region>.api.azureml.ms`
* `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
* `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
* `<workspace-GUID>.workspace.<region>.aether.ms`
* `ml-<workspace-name>-<region>-<workspace-guid>.notebooks.azure.ml`
* Se criar uma instância computacional, também deve adicionar uma entrada para `<instance-name>.<region>.instances.azureml.ms` .

Para encontrar os endereços IP internos para as FQDNs no VNet, utilize um dos seguintes métodos:

> [!NOTE]
> Os nomes de domínio e endereços IP totalmente qualificados serão diferentes com base na sua configuração. Por exemplo, o valor GUID no nome de domínio será específico para o seu espaço de trabalho.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az network private-endpoint show --endpoint-name <endpoint> --resource-group <resource-group> --query 'customDnsConfigs[*].{FQDN: fqdn, IPAddress: ipAddresses[0]}' --output table
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
$workspaceDns=Get-AzPrivateEndpoint -Name <endpoint> -resourcegroupname <resource-group>
$workspaceDns.CustomDnsConfigs | format-table
```

# <a name="azure-portal"></a>[Portal do Azure](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com)selecione o seu __espaço de trabalho__Azure Machine Learning .
1. Na secção __Definições,__ selecione __ligações de ponto final privado .__
1. Selecione o link na coluna __de ponto final privado__ que é visualizada.
1. Uma lista dos nomes de domínio totalmente qualificados (FQDN) e endereços IP para o ponto final privado do espaço de trabalho estão na parte inferior da página.

:::image type="content" source="./media/how-to-custom-dns/private-endpoint-custom-dns.png" alt-text="Lista de FQDNs no portal":::

---

A informação devolvida de todos os métodos é a mesma; uma lista do FQDN e endereço IP privado para os recursos.

| FQDN | Endereço IP |
| ----- | ----- |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `fb7e20a0-8891-458b-b969-55ddb3382f51.studio.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `cert-fb7e20a0-8891-458b-b969-55ddb3382f51.workspace.eastus.api.azureml.ms` | `10.1.0.5` |
| `ml-myworkspace-eastus-fb7e20a0-8891-458b-b969-55ddb3382f51.notebooks.azure.net` | `10.1.0.6` |

> [!IMPORTANT]
> Algumas FQDNs não são mostradas na lista pelo ponto final privado, mas são exigidas pelo espaço de trabalho. Estes FQDNs estão listados na tabela seguinte, e também devem ser adicionados ao seu servidor DNS:
>
> * `<workspace-GUID>.workspace.<region>.experiments.azureml.net`
> * `<workspace-GUID>.workspace.<region>.modelmanagement.azureml.net`
> * `<workspace-GUID>.workspace.<region>.aether.ms`
> * Se tiver uma instância computacional, `<instance-name>.<region>.instances.azureml.ms` use, onde `<instance-name>` está o nome da sua instância de computação.
>
> Para todos estes endereços IP, utilize o mesmo endereço que as `*.api.azureml.ms` entradas devolvidas das etapas anteriores.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a utilização do Azure Machine Learning com uma rede virtual, consulte a visão geral da [rede virtual.](how-to-network-security-overview.md)