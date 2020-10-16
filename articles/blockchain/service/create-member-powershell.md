---
title: Criar um membro do Serviço Azure Blockchain - Azure PowerShell
description: Crie um membro do Serviço Azure Blockchain para um consórcio blockchain usando a Azure PowerShell.
ms.date: 9/22/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: b57c44e79d599ab41b2c3356ee337811acdf639d
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948362"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-powershell"></a>Quickstart: Criar um membro blockchain do Azure Blockchain Service usando a Azure PowerShell

Neste quickstart, você implementa um novo membro blockchain e consórcio no Azure Blockchain Service usando Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se conecte à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Enquanto o módulo **Az.Blockchain** PowerShell estiver em pré-visualização, deve instalá-lo separadamente do módulo Az PowerShell utilizando o `Install-Module` cmdlet. Uma vez que este módulo PowerShell fica geralmente disponível, torna-se parte de futuros lançamentos de módulos Az PowerShell e disponível de forma nativa a partir de Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Blockchain
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

## <a name="register-resource-provider"></a>Registar o fornecedor de recursos

Se esta for a sua primeira utilização do serviço Azure Blockchain, tem de registar o fornecedor de recursos **Microsoft.Blockchain.**

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Blockchain
```

## <a name="choose-a-specific-azure-subscription"></a>Escolha uma subscrição específica do Azure

Se tiver várias subscrições do Azure, escolha a subscrição adequada na qual os recursos devem ser faturados. Selecione uma subscrição específica utilizando o [cmdlet Set-AzContext.](/powershell/module/az.accounts/set-azcontext)

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definir variáveis

Vai usar várias informações repetidamente. Criar variáveis para armazenar a informação.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'eastus'
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Criar um [grupo de recursos Azure](../../azure-resource-manager/management/overview.md) utilizando o cmdlet [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos como um grupo.

O exemplo a seguir cria um grupo de recursos com base no nome da `$resourceGroupName` variável na região especificada na `$location` variável.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-blockchain-member"></a>Criar um membro blockchain

Um membro do Azure Blockchain Service é um nó blockchain numa rede de blockchain de consórcio privado.
Ao providenciar um membro, pode criar ou aderir a uma rede de consórcios. Precisa de pelo menos um membro para uma rede de consórcios. O número de membros blockchain necessários pelos participantes depende do seu cenário. Os participantes do consórcio podem ter um ou mais membros blockchain ou podem partilhar membros com outros participantes. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).

Existem vários parâmetros e propriedades que precisa passar. Substitua os parâmetros de exemplo pelos seus valores.

```azurepowershell-interactive
$passwd = Read-Host -Prompt 'Enter the members default transaction node password' -AsSecureString
$csPasswd = Read-Host -Prompt 'Enter  the consortium account password' -AsSecureString

$memberParams = @{
  Name = 'myblockchainmember'
  ResourceGroupName = $resourceGroupName
  Consortium = 'myconsortium'
  ConsortiumManagementAccountPassword = $csPasswd
  Location = $location
  Password = $passwd
  Protocol = 'Quorum'
  Sku = 'S0'
}
New-AzBlockchainMember @memberParams
```

| Parâmetro | Descrição |
|---------|-------------|
| **ResourceGroupName** | Nome do grupo de recursos onde são criados os recursos do Serviço Azure Blockchain. Utilize o grupo de recursos que criou na secção anterior.
| **Nome** | Um nome único que identifica o seu membro blockchain do Azure Blockchain Service. O nome é usado para o endereço de ponto final público. Por exemplo, `myblockchainmember.blockchain.azure.com`.
| **Localização** | Região de Azure onde o membro blockchain é criado. Por exemplo, `westus2`. Escolha a localização que estiver mais próxima dos seus utilizadores ou das suas outras aplicações do Azure. As funcionalidades podem não estar disponíveis em algumas regiões. O Azure Blockchain Data Manager está disponível nas seguintes regiões de Azure: Leste dos EUA e Europa Ocidental.
| **Palavra-passe** | A palavra-passe para o nó de transação padrão do membro. Utilize a palavra-passe para autenticação básica ao ligar-se ao ponto final público do nó de transação padrão do membro blockchain.
| **Protocolo** | Protocolo blockchain. Atualmente, o protocolo _Qurum_ é suportado.
| **Consórcio** | Nome do consórcio para aderir ou criar. Para obter mais informações sobre consórcios, consulte [o consórcio Azure Blockchain Service](consortium.md).
| **ConsórcioManagementApalhopassa** | A senha da conta do consórcio também é conhecida como a palavra-passe da conta do membro. A palavra-passe da conta do membro é usada para encriptar a chave privada da conta Ethereum que é criada para o seu membro. Utilize a conta de membro e a palavra-passe da conta do membro para a gestão do consórcio.
| **Sku** | Tipo de nível. **S0** para standard ou **B0** para básico. Utilize o nível _básico_ para desenvolvimento, teste e prova de conceitos. Utilize o nível _Standard_ para implantações de nível de produção. Também deve utilizar o nível _Standard_ se estiver a utilizar o Blockchain Data Manager ou a enviar um grande volume de transações privadas. A alteração do nível de preços entre o básico e o padrão após a criação dos membros não é apoiada.

Leva cerca de 10 minutos para criar o membro blockchain e recursos de apoio.

## <a name="clean-up-resources"></a>Limpar recursos

Pode utilizar o membro blockchain que criou para o próximo quickstart ou tutorial. Quando já não é necessário, pode eliminar os recursos eliminando o `myResourceGroup` grupo de recursos que criou para o arranque rápido.

> [!CAUTION]
> O exemplo a seguir elimina o grupo de recursos especificado e todos os recursos contidos no mesmo.
> Se existirem recursos fora do âmbito deste artigo no grupo de recursos especificados, também serão eliminados.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Passos seguintes

Neste quickstart, você lançou um membro do Azure Blockchain Service e um novo consórcio. Experimente o próximo quickstart para usar o Kit de Desenvolvimento Azure Blockchain para o Ethereum para se ligar a um membro do Serviço Azure Blockchain.

> [!div class="nextstepaction"]
> [Use código de estúdio visual para ligar ao Serviço Azure Blockchain](connect-vscode.md)