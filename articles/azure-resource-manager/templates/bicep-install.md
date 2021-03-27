---
title: Criar ambientes de desenvolvimento e implantação de Bicep
description: Como configurar ambientes de desenvolvimento e implantação de Bicep
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 0e62e6a4633bee09fcbe8b783118cc95ccd5702e
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105626106"
---
# <a name="install-bicep-preview"></a>Instalar Bicep (Pré-visualização)

Saiba como configurar ambientes de desenvolvimento e implantação de Bicep.

## <a name="development-environment"></a>Ambiente de programação

Para obter a melhor experiência de autoria bicep, você precisa de dois componentes:

- **Extensão Bicep para Código de Estúdio Visual**. Para criar ficheiros Bicep, precisa de um bom editor Bicep. Recomendamos [código de estúdio visual](https://code.visualstudio.com/) com a [extensão Bicep](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep). Estas ferramentas fornecem suporte linguístico e auto-preconização de recursos. Ajudam a criar e validar ficheiros Bicep. Para obter mais informações sobre a utilização do Código do Estúdio Visual e da extensão Bicep, consulte [Quickstart: Create Bicep files with Visual Studio Code](./quickstart-create-bicep-use-visual-studio-code.md).
- **Bicep CLI**. Utilize o Bicep CLI para compilar ficheiros Bicep para modelos ARM JSON e descompiler os modelos ARM JSON para ficheiros Bicep. Para obter as instruções de instalação, consulte [instalar o Bicep CLI](#install-manually).

## <a name="deployment-environment"></a>Ambiente de implementação

Para implantar ficheiros Bicep locais, precisa de dois componentes:

- **Versão Azure CLI 2.20.0 ou posterior, ou versão Azure PowerShell 5.6.0 ou posterior**. Para as instruções de instalação, consulte:

  - [Instalar o Azure PowerShell](/powershell/azure/install-az-ps)
  - [Instalar a CLI do Azure no Windows](/cli/azure/install-azure-cli-windows)
  - [Instale o Azure CLI no Linux](/cli/azure/install-azure-cli-linux)
  - [Instalar a CLI do Azure no macOS](/cli/azure/install-azure-cli-macos)

  > [!NOTE]
  > Atualmente, tanto o Azure CLI como o Azure PowerShell só podem implementar ficheiros Bicep locais. Para obter mais informações sobre a implementação de ficheiros Bicep utilizando o Azure CLI, consulte [Implementar - CLI](./deploy-cli.md#deploy-remote-template). Para obter mais informações sobre a implementação de ficheiros Bicep utilizando a Azure PowerShell, consulte [Implementar - PowerShell]( ./deploy-powershell.md#deploy-remote-template).

- **Bicep CLI**. O Bicep CLI é necessário para compilar ficheiros Bicep para modelos JSON antes da implementação. Para obter as instruções de instalação, consulte [instalar o Bicep CLI](#install-bicep-cli).

Depois de instalados os componentes, pode implantar um ficheiro Bicep com:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateFile <path-to-template-or-bicep> `
  -storageAccountType Standard_GRS
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

---

## <a name="install-bicep-cli"></a>Instalar Bicep CLI

- Para utilizar o Bicep CLI para compilar e decompiler ficheiros Bicep, consulte [instalar manualmente](#install-manually).
- Para utilizar o CLI Azure para implantar ficheiros Bicep, consulte [utilizar com O Azure CLI](#use-with-azure-cli).
- Para utilizar o Azure PowerShell para implantar ficheiros Bicep, consulte [utilizar com a Azure PowerShell](#use-with-azure-powershell).

### <a name="use-with-azure-cli"></a>Uso com Azure CLI

Com a versão 2.20.0 do Azure CLI ou posteriormente instalada, o Bicep CLI é automaticamente instalado quando um comando que depende dele é executado. Por exemplo:

```azurecli
az deployment group create --template-file azuredeploy.bicep --resource-group myResourceGroup
```

ou

```azurecli
az bicep ...
```

Também pode instalar manualmente o CLI utilizando os comandos incorporados:

```bash
az bicep install
```

Para atualizar para a versão mais recente:

```bash
az bicep upgrade
```

Para instalar uma versão específica:

```bash
az bicep install --version v0.3.126
```

> [!IMPORTANT]
> O Azure CLI instala uma versão separada do Bicep CLI que não está em conflito com quaisquer outras instalações bicep que possa ter, e o Azure CLI não adiciona Bicep CLI ao seu PATH. Para utilizar o Bicep CLI para compilar/decompileizar ficheiros Bicep, ou para utilizar o Azure PowerShell para implantar ficheiros Bicep, consulte [instalar manualmente](#install-manually) ou [utilizar com Azure Powershell](#use-with-azure-powershell).

Para listar todas as versões disponíveis do Bicep CLI:

```bash
az bicep list-versions
```

Para mostrar as versões instaladas:

```bash
az bicep version
```

### <a name="use-with-azure-powershell"></a>Utilizar com Azure PowerShell

A Azure PowerShell ainda não tem a capacidade de instalar o Bicep CLI. A Azure PowerShell (v5.6.0 ou mais tarde) espera que o Bicep CLI já esteja instalado e disponível no PATH. Siga um dos [métodos de instalação manual](#install-manually).

Para implementar ficheiros Bicep, é necessária a versão 0.3.1 do Bicep CLI. Para verificar a versão Bicep CLI:

```cmd
bicep --version
```

> [!IMPORTANT]
> O Azure CLI instala a sua própria versão autossuficiente do Bicep CLI. A implementação do Azure PowerShell falha mesmo que tenha as versões necessárias instaladas para o Azure CLI.

Uma vez instalado o Bicep CLI, o Bicep CLI é chamado sempre que é necessário para um cmdlet de implantação. Por exemplo:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile azuredeploy.bicep
```

### <a name="install-manually"></a>Instalar manualmente

Os seguintes métodos instalam o Bicep CLI e adicionam-no ao seu PATH.

#### <a name="linux"></a>Linux

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-linux-x64
# Mark it as executable
chmod +x ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="macos"></a>macOS

##### <a name="via-homebrew"></a>via homebrew

```sh
# Add the tap for bicep
brew tap azure/bicep https://github.com/azure/bicep

# Install the tool
brew install azure/bicep/bicep
```

##### <a name="macos-manual-install"></a>instalação manual do macOS

```sh
# Fetch the latest Bicep CLI binary
curl -Lo bicep https://github.com/Azure/bicep/releases/latest/download/bicep-osx-x64
# Mark it as executable
chmod +x ./bicep
# Add Gatekeeper exception (requires admin)
sudo spctl --add ./bicep
# Add bicep to your PATH (requires admin)
sudo mv ./bicep /usr/local/bin/bicep
# Verify you can now access the 'bicep' command
bicep --help
# Done!

```

#### <a name="windows"></a>Windows

##### <a name="windows-installer"></a>Windows Installer

Descarregue e execute o [mais recente instalador do Windows](https://github.com/Azure/bicep/releases/latest/download/bicep-setup-win-x64.exe). O instalador não requer privilégios administrativos. Após a instalação, o Bicep CLI é adicionado ao seu utilizador PATH. Feche e reabra quaisquer janelas abertas do invólucro de comando para que a alteração PATH entre em vigor.

##### <a name="chocolatey"></a>Chocolate

```powershell
choco install bicep
```

##### <a name="winget"></a>Winget

```powershell
winget install -e --id Microsoft.Bicep
```

##### <a name="manual-with-powershell"></a>Manual com PowerShell

```powershell
# Create the install folder
$installPath = "$env:USERPROFILE\.bicep"
$installDir = New-Item -ItemType Directory -Path $installPath -Force
$installDir.Attributes += 'Hidden'
# Fetch the latest Bicep CLI binary
(New-Object Net.WebClient).DownloadFile("https://github.com/Azure/bicep/releases/latest/download/bicep-win-x64.exe", "$installPath\bicep.exe")
# Add bicep to your PATH
$currentPath = (Get-Item -path "HKCU:\Environment" ).GetValue('Path', '', 'DoNotExpandEnvironmentNames')
if (-not $currentPath.Contains("%USERPROFILE%\.bicep")) { setx PATH ($currentPath + ";%USERPROFILE%\.bicep") }
if (-not $env:path.Contains($installPath)) { $env:path += ";$installPath" }
# Verify you can now access the 'bicep' command.
bicep --help
# Done!
```

## <a name="install-the-nightly-builds"></a>Instale as construções noturnas

Se quiser experimentar os últimos bits de Bicep antes de serem [lançados, consulte as construções noturnas](https://github.com/Azure/bicep/blob/main/docs/installing-nightly.md)de instalação .

> [!WARNING]
> Estas construções pré-lançamento são muito mais prováveis de ter bugs conhecidos ou desconhecidos.

## <a name="next-steps"></a>Passos seguintes

Começa com o [quickstart Bicep.](./quickstart-create-bicep-use-visual-studio-code.md)
