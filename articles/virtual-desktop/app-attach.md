---
title: Configure aplicativo Virtual Desktop MSIX anexar scripts PowerShell - Azure
description: Como criar scripts PowerShell para app MSIX anexar para Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/14/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 5e45c51735e0b7ab4b263d3f3047b5848c82439d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98185772"
---
# <a name="create-powershell-scripts-for-msix-app-attach-preview"></a>Criar scripts PowerShell para anexação de aplicações MSIX (pré-visualização)

> [!IMPORTANT]
> O anexo de aplicações MSIX encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tópico irá acompanhá-lo como configurar scripts PowerShell para anexação de aplicações MSIX.

>[!IMPORTANT]
>Antes de começar, certifique-se de preencher e submeter [este formulário](https://aka.ms/enablemsixappattach) para permitir que a app MSIX se anexe na sua subscrição. Se não tiver um pedido aprovado, o anexo de aplicações MSIX não funcionará. A aprovação dos pedidos pode demorar até 24 horas durante os dias úteis. Receberá um e-mail quando o seu pedido tiver sido aceite e concluído.

## <a name="install-certificates"></a>Instalar certificados

Tem de instalar certificados em todos os anfitriões da sessão na piscina anfitriã que irão acolher os aps da sua aplicação MSIX anexar pacotes.

Se a sua aplicação usa um certificado que não seja de confiança pública ou que tenha sido auto-assinado, eis como instalá-lo:

1. Clique com o botão direito no pacote e selecione **Propriedades.**
2. Na janela que aparece, selecione o separador **assinaturas Digitais.** Deve haver apenas um item na lista no separador, como mostrado na imagem seguinte. Selecione o item para realçar o item e, em seguida, selecione **Detalhes**.
3. Quando aparecer a janela de detalhes de assinatura digital, selecione o separador **Geral** e, em seguida, selecione **'Ver' Certificate** e, em seguida, selecione **o certificado de instalação**.
4. Quando o instalador abrir, selecione **a máquina local** como local de armazenamento e, em seguida, selecione **Seguinte**.
5. Se o instalador lhe perguntar se pretende permitir que a aplicação faça alterações no seu dispositivo, selecione **Sim**.
6. Selecione **Coloque todos os certificados na loja seguinte** e, em seguida, selecione **Procurar**.
7. Quando aparecer a janela da loja de certificados selecionada, **selecione Pessoas Fidedignas** e, em seguida, selecione **OK**.
8. Selecione **Seguinte** e **Termine**.

## <a name="enable-microsoft-hyper-v"></a>Ativar o Hiper-V da Microsoft

O Microsoft Hyper-V tem de ser ativado porque o `Mount-VHD` comando é necessário para o estágio e é necessário para `Dismount-VHD` desabilitado.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```

>[!NOTE]
>Esta alteração requer que reinicie a máquina virtual.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Prepare scripts PowerShell para anexação de aplicações MSIX

O anexo de aplicações MSIX tem quatro fases distintas que devem ser realizadas na seguinte ordem:

1. Fase
2. Registar
3. Desregister
4. Destage

Cada fase cria um script PowerShell. Os scripts de amostra para cada fase estão disponíveis [aqui.](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach)

### <a name="stage-powershell-script"></a>Roteiro de Fase PowerShell

Antes de atualizar os scripts PowerShell, certifique-se de que tem o volume GUID do volume no VHD. Para obter o volume GUID:

1.  Abra a partilha de rede onde o VHD está localizado dentro do VM onde irá executar o script.

2.  Clique com o botão direito no VHD e selecione **Mount**. Isto vai montar o VHD numa carta de condução.

3.  Depois de montar o VHD, a janela **do Explorador de Ficheiros** abre-se. Capture a pasta dos pais e atualize a variável **$parentFolder**

    >[!NOTE]
    >Se não vires uma pasta dos pais, isso significa que o MSIX não foi expandido corretamente. Refaça a secção anterior e tente novamente.

4.  Abra a pasta dos pais. Se for expandida corretamente, verá uma pasta com o mesmo nome que a embalagem. Atualize a variável **$packageName** para corresponder ao nome desta pasta.

    Por exemplo, `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Abra uma pressão de comando e **introduza mountvol**. Este comando apresentará uma lista de volumes e os seus GUIDs. Copie o GUID do volume onde a letra de acionamento corresponde à unidade a que montou o VHD no passo 2.

    Por exemplo, neste exemplo de saída para o comando mountvol, se montou o seu VHD para a Unidade C, deverá copiar o valor `C:\` acima:

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Atualize a variável **$volumeGuid** com o volume GUID que acabou de copiar.

7. Abra um pedido de Admin PowerShell e atualize o seguinte script PowerShell com as variáveis que se aplicam ao seu ambiente.

    ```powershell
    #MSIX app attach staging sample

    #region variables
    $vhdSrc="<path to vhd>"
    $packageName = "<package name>"
    $parentFolder = "<package parent folder>"
    $parentFolder = "\" + $parentFolder + "\"
    $volumeGuid = "<vol guid>"
    $msixJunction = "C:\temp\AppAttach\"
    #endregion

    #region mountvhd
    try
    {
          Mount-Diskimage -ImagePath $vhdSrc -NoDriveLetter -Access ReadOnly
          Write-Host ("Mounting of " + $vhdSrc + " was completed!") -BackgroundColor Green
    }
    catch
    {
          Write-Host ("Mounting of " + $vhdSrc + " has failed!") -BackgroundColor Red
    }
    #endregion

    #region makelink
    $msixDest = "\\?\Volume{" + $volumeGuid + "}\"
    if (!(Test-Path $msixJunction))
    {
         md $msixJunction
    }

    $msixJunction = $msixJunction + $packageName
    cmd.exe /c mklink /j $msixJunction $msixDest
    #endregion

    #region stage
    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime] | Out-Null
    Add-Type -AssemblyName System.Runtime.WindowsRuntime
    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where { $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult] AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]
    $asTaskAsyncOperation = $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult], [Windows.Management.Deployment.DeploymentProgress])
    $packageManager = [Windows.Management.Deployment.PackageManager]::new()
    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd
    $path = ([System.Uri]$path).AbsoluteUri
    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")
    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))
    $task
    #endregion
    ```

### <a name="register-powershell-script"></a>Registar script PowerShell

Para executar o script do registo, execute os seguintes cmdlets PowerShell com os valores do espaço reservado substituídos por valores que se aplicam ao seu ambiente.

```powershell
#MSIX app attach registration sample

#region variables
$packageName = "<package name>"
$path = "C:\Program Files\WindowsApps\" + $packageName + "\AppxManifest.xml"
#endregion

#region register
Add-AppxPackage -Path $path -DisableDevelopmentMode -Register
#endregion
```

### <a name="deregister-powershell-script"></a>Guião PowerShell de desregista

Para este script, substitua o espaço reservado para **$packageName** pelo nome do pacote que está a testar.

```powershell
#MSIX app attach deregistration sample

#region variables
$packageName = "<package name>"
#endregion

#region deregister
Remove-AppxPackage -PreserveRoamableApplicationData $packageName
#endregion
```

### <a name="destage-powershell-script"></a>Script Destage PowerShell

Para este script, substitua o espaço reservado para **$packageName** pelo nome do pacote que está a testar. Numa produção, seria melhor executar isto no Shutdown.

```powershell
#MSIX app attach de staging sample

$vhdSrc="<path to vhd>"

#region variables
$packageName = "<package name>"
$msixJunction = "C:\temp\AppAttach"
#endregion

#region deregister
Remove-AppxPackage -AllUsers -Package $packageName
Remove-Item "$msixJunction\$packageName" -Recurse -Force -Verbose
#endregion

#region Detach VHD
Dismount-DiskImage -ImagePath $vhdSrc -Confirm:$false
#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Configurar scripts de simulação para o agente de anexação de aplicações MSIX

Depois de criar os scripts, os utilizadores podem executá-los manualmente ou configurar-os para funcionar automaticamente como scripts de arranque, início de sessão, logor o logote e de encerramento. Para saber mais sobre este tipo de scripts, consulte [Usando scripts de arranque, encerramento, início de símis e logoff na Política de Grupo.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)

Cada um destes scripts automáticos executa uma fase dos scripts anexados da aplicação:

- O guião da startup executa o guião do palco.
- O script do início de são executa o script do registo.
- O script logoff executa o script do desregister.
- O guião de encerramento executa o guião de desaparar-se.

## <a name="use-packages-offline"></a>Use pacotes offline

Se estiver a utilizar pacotes da [Microsoft Store para negócios](https://businessstore.microsoft.com/) ou da [Microsoft Store for Education](https://educationstore.microsoft.com/) dentro da sua rede ou em dispositivos que não estejam ligados à internet, precisa de obter as licenças de pacote da Microsoft Store e instalá-las no seu dispositivo para executar com sucesso a aplicação. Se o seu dispositivo estiver online e puder ligar-se à Microsoft Store for Business, as licenças necessárias devem ser descarregadas automaticamente, mas se estiver offline, terá de configurar as licenças manualmente.

Para instalar os ficheiros de licença, terá de utilizar um script PowerShell que ligue para a classe MDM_EnterpriseModernAppManagement_StoreLicenses02_01 no Provedor de Ponte WMI.

Eis como configurar as licenças para uso offline:

1. Descarregue o pacote de aplicações, licenças e quadros necessários da Microsoft Store para negócios. Precisa dos ficheiros de licença codificados e não codificados. As instruções de descarregamento detalhadas podem ser [encontradas aqui.](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app)
2. Atualize as seguintes variáveis no roteiro para o passo 3:
      1. `$contentID` é o valor ContentID do ficheiro de licença descodificada (.xml). Pode abrir o ficheiro de licença num editor de texto à sua escolha.
      2. `$licenseBlob` é a cadeia inteira para a bolha de licença no ficheiro de licença codificado (.bin). Pode abrir o ficheiro de licença codificado num editor de texto à sua escolha.
3. Execute o seguinte script a partir de um pedido Admin PowerShell. Um bom lugar para executar a instalação de licença é no final do script de [encenação](#stage-powershell-script) que também precisa ser executado a partir de um pedido de administrador.

```powershell
$namespaceName = "root\cimv2\mdm\dmmap"
$className = "MDM_EnterpriseModernAppManagement_StoreLicenses02_01"
$methodName = "AddLicenseMethod"
$parentID = "./Vendor/MSFT/EnterpriseModernAppManagement/AppLicenses/StoreLicenses"

#TODO - Update $contentID with the ContentID value from the unencoded license file (.xml)
$contentID = "{'ContentID'_in_unencoded_license_file}"

#TODO - Update $licenseBlob with the entire String in the encoded license file (.bin)
$licenseBlob = "{Entire_String_in_encoded_license_file}"

$session = New-CimSession

#The final string passed into the AddLicenseMethod should be of the form <License Content="encoded license blob" />
$licenseString = '<License Content='+ '"' + $licenseBlob +'"' + ' />'

$params = New-Object Microsoft.Management.Infrastructure.CimMethodParametersCollection
$param = [Microsoft.Management.Infrastructure.CimMethodParameter]::Create("param",$licenseString ,"String", "In")
$params.Add($param)


try
{
   $instance = New-CimInstance -Namespace $namespaceName -ClassName $className -Property @{ParentID=$parentID;InstanceID=$contentID}
   $session.InvokeMethod($namespaceName, $instance, $methodName, $params)

}
catch [Exception]
{
     write-host $_ | out-string
}
```

## <a name="next-steps"></a>Passos seguintes

Esta funcionalidade não é suportada atualmente, mas pode fazer perguntas à comunidade no [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Também pode deixar feedback para o Windows Virtual Desktop no [centro de feedback virtual do Windows Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).
