---
title: Aplicativo MSIX de desktop virtual do Windows anexam PowerShell - Azure
description: Como configurar o anexo de aplicações MSIX para o Windows Virtual Desktop utilizando o PowerShell.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f44cbf3764063c511c896f11bb7ebfaae2973f0c
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365404"
---
# <a name="set-up-msix-app-attach-using-powershell"></a>Configurar o anexo de aplicativo MSIX utilizando o PowerShell

Além do portal Azure, também pode configurar a aplicação MSIX anexar manualmente com o PowerShell. Este artigo irá acompanhá-lo como usar o PowerShell para configurar o anexo de aplicações MSIX.

## <a name="requirements"></a>Requisitos

>[!IMPORTANT]
>Antes de começar, certifique-se de preencher e submeter [este formulário](https://aka.ms/enablemsixappattach) para permitir que a app MSIX se anexe na sua subscrição. Se não tiver um pedido aprovado, o anexo de aplicações MSIX não funcionará. A aprovação dos pedidos pode demorar até 24 horas durante os dias úteis. Receberá um e-mail quando o seu pedido tiver sido aceite e concluído.

Aqui está o que precisa para configurar o anexo da app MSIX:

- Uma implementação de ambiente de trabalho virtual do Windows em funcionamento. Para aprender a implementar o Windows Virtual Desktop (clássico), consulte [Criar um inquilino no Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Para aprender a implementar o Windows Virtual Desktop com integração do Azure Resource Manager, consulte [Criar uma piscina de anfitriões com o portal Azure](./create-host-pools-azure-marketplace.md).
- Uma piscina de anfitriões virtual do Windows Desktop com pelo menos um anfitrião de sessão ativa.
- Esta piscina de anfitriões deve estar no ambiente de validação.
- Um grupo de aplicações remotas desktop.
- A ferramenta de embalagem MSIX.
- Uma aplicação embalada pela MSIX expandiu-se para uma imagem MSIX que é enviada para uma partilha de ficheiros.
- Uma partilha de ficheiros na sua implementação virtual do Windows Desktop onde o pacote MSIX será armazenado.
- A partilha de ficheiros onde fez o upload da imagem MSIX também deve estar acessível a todas as máquinas virtuais (VMs) na piscina anfitriã. Os utilizadores precisarão de permissões apenas de leitura para aceder à imagem.
- Descarregue e instale o PowerShell Core.
- Descarregue o módulo Azure PowerShell de pré-visualização pública e expanda-o para uma pasta local.
- Instale o módulo Azure executando o seguinte cmdlet:

    ```powershell
    Install-Module -Name Az -Force
    ```

## <a name="sign-in-to-azure-and-import-the-module"></a>Inscreva-se no Azure e importe o módulo

Assim que tiver todos os requisitos prontos, abra o núcleo PowerShell num pedido de comando elevado e execute este cmdlet:

```powershell
Connect-AzAccount
```

Depois de executá-la, autente a sua conta usando as suas credenciais. Neste caso, pode ser-lhe pedido um URL do dispositivo ou um token.

## <a name="import-the-azwindowsvirtualdesktop-module"></a>Importar o módulo Az.WindowsVirtualDesktop

Você precisará do módulo Az.DesktopVirtualization para seguir as instruções deste artigo.

>[!NOTE]
>Para a pré-visualização pública, forneceremos o módulo como ficheiros ZIP separados que deve importar manualmente.

Antes de começar, pode executar o seguinte cmdlet para ver se o módulo Az.DesktopVirtualization já está instalado na sua sessão ou VM:

```powershell
Get-Module | Where-Object { $_.Name -Like "desktopvirtualization" }
```

Se quiser desinstalar uma cópia existente do módulo e começar de novo, execute este cmdlet:

```powershell
Uninstall-Module Az.DesktopVirtualization
```

Se o módulo estiver bloqueado no seu VM, erte este cmdlet para desbloqueá-lo:

```powershell
Unblock-File "<path>\Az.DesktopVirtualization.psm1"
```

Com a limpeza fora do caminho, é hora de importar o módulo.

1. Executar o cmdlet seguinte e, em seguida, prima a tecla **R** quando solicitado para concordar em executar o código personalizado.

   ```powershell
   Import-Module -Name "<path>\Az.DesktopVirtualization.psm1" -Verbose
   ```

2. Depois de ter executado o cmdlet de importação, verifique se tem os cmdlets para o MSIX executando o seguinte cmdlet:

   ```powershell
   Get-Command -Module Az.DesktopVirtualization | Where-Object { $_.Name -match "MSIX" }
   ```

   Se os cmdlets estiverem lá, a saída deve ser assim:

   ```powershell
   CommandType     Name                                               Version    Source

   -----------     ----                                               -------    ------

   Function        Expand-AzWvdMsixImage                              0.0        Az.DesktopVirtualization

   Function        Get-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        New-AzWvdMsixPackage                               0.0        Az.DesktopVirtualization

   Function        Remove-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization

   Function        Update-AzWvdMsixPackage                            0.0        Az.DesktopVirtualization
   ```

   Se não vir esta saída, feche todas as sessões powerShell e PowerShell Core e tente novamente.

## <a name="set-up-helper-variables"></a>Configurar variáveis de ajuda

Depois de importar o módulo, terá de configurar as variáveis de ajuda. Os seguintes exemplos irão mostrar-lhe como fazer cada um.

Para obter o seu ID de subscrição:

```powershell
Get-AzContext -ListAvailable | fl
```

Para selecionar o contexto de um inquilino Azure e subscrição com um nome:

```powershell
$obj = Select-AzContext -Name "<Name>"
```

Para definir a variável de subscrição:

```powershell
$subId = $obj.Subscription.Id
```

Para definir o nome do espaço de trabalho:

```powershell
$ws = "<WorksSpaceName>"
```

Para definir o nome da piscina hospedeira:

```powershell
$hp = "<HostPoolName>"
```

Para configurar o grupo de recursos onde os VMs do anfitrião da sessão estão configurados:

```powershell
$rg = "<ResourceGroupName>"
```

E, finalmente, para confirmar que definiu corretamente todas as variáveis:

```powershell
Get-AzWvdWorkspace -Name $ws -ResourceGroupName $rg -SubscriptionId $subID
```

## <a name="add-an-msix-package-to-a-host-pool"></a>Adicione um pacote MSIX a uma piscina de anfitriões

Uma vez configurado tudo, é hora de adicionar o pacote MSIX a uma piscina de anfitriões. Para isso, primeiro terá de seguir o caminho da UNC para a imagem do MSIX.

Utilizando o caminho do UNC, executar este cmdlet para expandir a imagem MSIX:

```powershell
$obj = Expand-AzWvdMsixImage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subID -Uri <UNCPath>
```

Executar este cmdlet para adicionar o pacote MSIX à piscina de anfitrião desejada:

```powershell
New-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId -PackageAlias $obj.PackageAlias -DisplayName <DisplayName> -ImagePath <UNCPath> -IsActive:$true
```

Assim que terminar, confirme que o pacote foi criado com este cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object {$_.PackageFamilyName -eq $obj.PackageFamilyName}
```

## <a name="remove-an-msix-package-from-a-host-pool"></a>Remova um pacote MSIX de uma piscina de anfitriões

Para remover um pacote de uma piscina hospedeira:

Obtenha uma lista de todos os pacotes associados a uma piscina de anfitrião com este cmdlet e, em seguida, encontre o nome do pacote que deseja remover na saída:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId 
```

Em alternativa, também pode obter um pacote específico com base no seu nome de exibição com este cmdlet:

```powershell
Get-AzWvdMsixPackage -HostPoolName $hp -ResourceGroupName $rg -SubscriptionId $subId | Where-Object { $_.Name -like "Power" }
```

Para remover a embalagem, executar este cmdlet:

```powershell
Remove-AzWvdMsixPackage -FullName $obj.PackageFullName -HostPoolName $hp -ResourceGroupName $rg
```

## <a name="publish-msix-apps-to-an-app-group"></a>Publicar aplicativos MSIX para um grupo de aplicações

Só pode seguir as instruções desta secção se tiver terminado de seguir as instruções nas secções anteriores. Se você tem uma piscina de anfitrião com um anfitrião de sessão ativa, pelo menos um grupo de aplicações desktop, e adicionou um pacote MSIX à piscina anfitriã, você está pronto para ir.

Para publicar uma aplicação do pacote MSIX para um grupo de aplicações, terá de encontrar o seu nome e, em seguida, use esse nome no cmdlet editorial.

Para publicar uma aplicação:

Executar este cmdlet para listar todos os grupos de aplicações disponíveis:

```powershell
Get-AzWvdApplicationGroup -ResourceGroupName $rg -SubscriptionId $subId
```

Quando encontrar o nome do grupo de aplicações para publicar aplicações para, use o seu nome neste cmdlet:

```powershell
$grName = "<AppGroupName>"
```

Finalmente, terá de publicar a aplicação.

- Para publicar a aplicação MSIX num grupo de aplicações para desktop, execute este cmdlet:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0
   ```

- Para publicar a aplicação a um grupo de aplicações remotas, execute este cmdlet em vez disso:

   ```powershell
   New-AzWvdApplication -ResourceGroupName $rg -SubscriptionId $subId -Name PowerBi -ApplicationType MsixApplication -ApplicationGroupName $grName -MsixPackageFamilyName $obj.PackageFamilyName -CommandLineSetting 0 -MsixPackageApplicationId $obj.PackageApplication.AppId
   ```

>[!NOTE]
>Se um utilizador for designado para um grupo de aplicações remotas e um grupo de aplicações de desktop no mesmo pool de anfitriões, quando o utilizador se ligar ao seu ambiente de trabalho remoto, verá aplicações MSIX de ambos os grupos.

## <a name="next-steps"></a>Passos seguintes

Faça perguntas à nossa comunidade sobre esta funcionalidade no [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Também pode deixar feedback para o Windows Virtual Desktop no [centro de feedback virtual do Windows Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Aqui estão outros artigos que pode achar útil:

- [App MSIX anexa glossário](app-attach-glossary.md)
- [App MSIX anexa FAQ](app-attach-faq.md)
