---
title: 'Quickstart: Criar um espaço de trabalho synapse usando Azure PowerShell'
description: Crie um espaço de trabalho Azure Synapse utilizando o Azure PowerShell seguindo os passos deste guia.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: overview
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a19d1f26616697e15ae4c39a63c44bdaf83f78f9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675765"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>Quickstart: Criar um espaço de trabalho sinapse Azure com Azure PowerShell

Azure PowerShell é um conjunto de cmdlets para gerir os recursos da Azure diretamente da PowerShell. Pode utilizá-la no seu browser com o Azure Cloud Shell. Também pode instalá-lo em macOS, Linux ou Windows.

Neste arranque rápido, aprende-se a criar um espaço de trabalho synapse utilizando a Azure PowerShell.

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- [Conta de armazenamento de Azure Data Lake Gen2](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > O espaço de trabalho Azure Synapse precisa de ser capaz de ler e escrever para a conta ADLS Gen2 selecionada. Para qualquer conta de armazenamento que ligue como conta de armazenamento primário, deve ativar o **espaço hierárquico** na criação da conta de armazenamento, tal como descrito na [Criar uma conta de armazenamento](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account).

Se optar por utilizar a Cloud Shell, consulte [a visão geral da Azure Cloud Shell](../cloud-shell/overview.md) para obter mais informações.

### <a name="install-the-azure-powershell-module-locally"></a>Instale o módulo Azure PowerShell localmente

Se optar por utilizar o PowerShell localmente, este artigo requer que instale o módulo Az PowerShell e se conecte à sua conta Azure utilizando o cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Para obter mais informações sobre a instalação do módulo Az PowerShell, consulte [instalar a Azure PowerShell](/powershell/azure/install-az-ps).

Para obter mais informações sobre a autenticação com a Azure PowerShell, consulte [Iniciar s-se-in com a Azure PowerShell](/powershell/azure/authenticate-azureps).

### <a name="install-the-azure-synapse-powershell-module"></a>Instale o módulo Azure Synapse PowerShell

> [!IMPORTANT]
> Enquanto o módulo **Az.Synapse** PowerShell estiver em pré-visualização, deve instalá-lo separadamente utilizando o `Install-Module` cmdlet. Quando este módulo do PowerShell entrar em disponibilidade geral, fará parte das versões futuras do módulo Az PowerShell e estará disponível por predefinição a partir do Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Criar um espaço de trabalho Azure Synapse usando Azure PowerShell

1. Definir variáveis ambientais necessárias para criar recursos para o espaço de trabalho Azure Synapse.

   |        Nome da variável        |                                                 Descrição                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | Nome da sua conta de armazenamento ADLS Gen2 existente.                                                           |
   | StorageAccountResourceGroup | Nome do seu grupo de recursos de conta de armazenamento ADLS Gen2 existente.                                             |
   | Nome de FileShare               | Nome do seu sistema de ficheiros de armazenamento existente.                                                                  |
   | Grupo SynapseResource        | Escolha um novo nome para o seu grupo de recursos Azure Synapse.                                                    |
   | Region                      | Escolha uma das [regiões de Azure.](https://azure.microsoft.com/global-infrastructure/geographies/#overview) |
   | SinapseWorkspaceName        | Escolha um nome único para o seu novo espaço de trabalho Azure Synapse.                                                  |
   | SqlUser                     | Escolha um valor para um novo nome de utilizador.                                                                          |
   | SqlPassword                 | Escolha uma senha segura.                                                                                   |
   | ClientIP                    | Endereço IP público do sistema de onde está a executar o PowerShell.                                             |
   |                             |                                                                                                             |

1. Crie um grupo de recursos como recipiente para o seu espaço de trabalho Azure Synapse:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Criar um espaço de trabalho Azure Synapse:

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. Obtenha Web e Dev URL para O espaço de trabalho Azure Synapse:

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. Crie uma Regra de Firewall para permitir o seu acesso ao Espaço de Trabalho Azure Synapse a partir da sua máquina:

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. Abra o endereço WEB URL do Espaço de Trabalho Azure Synapse armazenado em variável ambiente `WorkspaceWeb` para aceder ao seu espaço de trabalho:

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Azure Synapse workspace web](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Siga os passos abaixo para eliminar o espaço de trabalho Azure Synapse.

> [!WARNING]
> A eliminação de um espaço de trabalho Azure Synapse removerá os motores de análise e os dados armazenados na base de dados dos pools SQL contidos e metadados do espaço de trabalho. Deixará de ser possível ligar-se aos pontos finais SQL ou Apache Spark. Todos os artefactos de código serão eliminados (consultas, cadernos, definições de emprego e oleodutos). A eliminação do espaço de trabalho **não** afetará os dados da Data Lake Store Gen2 ligada ao espaço de trabalho.

Se o espaço de trabalho Azure Synapse criado neste artigo não for necessário, pode eliminá-lo executando o seguinte exemplo.

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Em seguida, você pode [criar piscinas SQL](quickstart-create-sql-pool-studio.md) ou [criar piscinas Apache Spark](quickstart-create-apache-spark-pool-studio.md) para começar a analisar e explorar seus dados.