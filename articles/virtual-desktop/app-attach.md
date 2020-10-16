---
title: Anexo de aplicações MSIX do Windows Virtual Desktop - Azure
description: Como configurar o anexo de aplicações MSIX para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 06/16/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3b02be8f35ff33f758aebe03c89287c51c9ffef7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91816324"
---
# <a name="set-up-msix-app-attach"></a>Configurar a anexação da aplicação MSIX

> [!IMPORTANT]
> O anexo de aplicações MSIX encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos a sua utilização para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas.
> Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este tópico irá acompanhá-lo como configurar a aplicação MSIX anexar num ambiente de desktop virtual do Windows.

## <a name="requirements"></a>Requisitos

Antes de começar, eis o que precisa para configurar o anexo da app MSIX:

- Acesso ao portal Insider do Windows Para obter a versão do Windows 10 com suporte para a aplicação MSIX anexar APIs.
- Uma implementação de ambiente de trabalho virtual do Windows em funcionamento. Para aprender a implementar o Windows Virtual Desktop (clássico), consulte [Criar um inquilino no Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md). Para aprender a implementar o Windows Virtual Desktop com integração do Azure Resource Manager, consulte [Criar uma piscina de anfitriões com o portal Azure](./create-host-pools-azure-marketplace.md).
- A ferramenta de embalagem MSIX.
- Uma partilha de rede na sua implementação virtual do Windows Desktop onde o pacote MSIX será armazenado.

## <a name="get-the-os-image"></a>Obtenha a imagem de SO

Primeiro, tens de obter a imagem do SO. Pode obter a imagem do SO através do portal Azure. No entanto, se for membro do programa Windows Insider, tem a opção de utilizar o portal Insider do Windows.

### <a name="get-the-os-image-from-the-azure-portal"></a>Obtenha a imagem do SO do portal Azure

Para obter a imagem do SO do portal Azure:

1. Abra o [portal Azure](https://portal.azure.com) e inscreva-se.

2. Ir para **criar uma máquina virtual.**

3. No separador **Basic,** selecione **o Windows 10 multi-sessão empresarial, versão 2004**.

4. Siga o resto das instruções para terminar a criação da máquina virtual.

     >[!NOTE]
     >Pode utilizar este VM para testar diretamente o anexo de aplicações MSIX. Para saber mais, avance para [gerar um pacote VHD ou VHDX para MSIX](#generate-a-vhd-or-vhdx-package-for-msix). Caso contrário, continue a ler esta secção.

### <a name="get-the-os-image-from-the-windows-insider-portal"></a>Obtenha a imagem de SO a partir do portal Windows Insider

Para obter a imagem de SO a partir do Portal Do Windows Insider:

1. Abra o [portal Windows Insider](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) e inscreva-se.

     >[!NOTE]
     >Tem de ser membro do programa Windows Insider para aceder ao portal Windows Insider. Para saber mais sobre o programa Windows Insider, consulte a nossa [documentação do Windows Insider.](/windows-insider/at-home/)

2. Desloque-se até à secção **de edição Select** e selecione **o Windows 10 Insider Preview Enterprise (FAST) – Construa 19041** ou mais tarde.

3. **Selecione Confirmar,** em seguida, selecione o idioma que pretende utilizar e, em seguida, selecione **Confirmar** novamente.

     >[!NOTE]
     >De momento, o inglês é a única língua que foi testada com a funcionalidade. Pode selecionar outros idiomas, mas podem não ser apresentados como pretendido.

4. Quando o link de descarregamento for gerado, selecione o **Download de 64 bits** e guarde-o para o disco rígido local.

## <a name="prepare-the-vhd-image-for-azure"></a>Prepare a imagem VHD para O Azure

Em seguida, terá de criar uma imagem de VHD mestre. Se ainda não criou a sua imagem master VHD, vá a [Preparar e personalize uma imagem master VHD](set-up-customize-master-image.md) e siga as instruções lá.

Depois de criar a sua imagem principal em VHD, tem de desativar as atualizações automáticas para aplicações de anexação de aplicações MSIX. Para desativar as atualizações automáticas, terá de executar os seguintes comandos numa solicitação de comando elevada:

```cmd
rem Disable Store auto update:

reg add HKLM\Software\Policies\Microsoft\WindowsStore /v AutoDownload /t REG_DWORD /d 0 /f
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Automatic app update" /Disable
Schtasks /Change /Tn "\Microsoft\Windows\WindowsUpdate\Scheduled Start" /Disable

rem Disable Content Delivery auto download apps that they want to promote to users:

reg add HKCU\Software\Microsoft\Windows\CurrentVersion\ContentDeliveryManager /v PreInstalledAppsEnabled /t REG_DWORD /d 0 /f

reg add HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\ContentDeliveryManager\Debug /v ContentDeliveryAllowedOverride /t REG_DWORD /d 0x2 /f

rem Disable Windows Update:

sc config wuauserv start=disabled
```

Depois de desativar as atualizações automáticas, tem de ativar o Hyper-V porque vai utilizar o comando Mount-VHD para encenar e desmontar vHD para desarmagem.

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Hyper-V -All
```
>[!NOTE]
>Esta alteração requer que reinicie a máquina virtual.

Em seguida, prepare o VHD VM para Azure e carreque o disco VHD resultante para Azure. Para saber mais, consulte [Prepare e personalize uma imagem master VHD](set-up-customize-master-image.md).

Depois de ter enviado o VHD para Azure, crie uma piscina hospedeira baseada nesta nova imagem seguindo as instruções na [piscina de anfitriões, utilizando o tutorial do Azure Marketplace.](create-host-pools-azure-marketplace.md)

## <a name="prepare-the-application-for-msix-app-attach"></a>Prepare a aplicação para o anexo de aplicações MSIX

Se já tem um pacote MSIX, avance para a [infraestrutura virtual de ambiente de trabalho do Windows.](#configure-windows-virtual-desktop-infrastructure) Se pretender testar aplicações antigas, siga as instruções na [Criar um pacote MSIX a partir de um instalador de ambiente de trabalho num VM](/windows/msix/packaging-tool/create-app-package-msi-vm/) para converter a aplicação legacy num pacote MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Gere um pacote VHD ou VHDX para MSIX

Os pacotes estão em formato VHD ou VHDX para otimizar o desempenho. A MSIX exige que os pacotes VHD ou VHDX funcionem corretamente.

Para gerar um pacote VHD ou VHDX para MSIX:

1. [Descarregue a ferramenta msixmgr](https://aka.ms/msixmgr) e guarde a pasta .zip para uma pasta dentro de uma Sessão de VM do anfitrião.

2. Desaperte a ferramenta msixmgr .zip.

3. Coloque a embalagem MSIX de origem na mesma pasta onde desapertou a ferramenta msixmgr.

4. Executar o seguinte cmdlet em PowerShell para criar um VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Certifique-se de que o tamanho do VHD é grande o suficiente para manter o MSIX expandido.*

5. Executar o seguinte cmdlet para montar o recém-criado VHD:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Executar este cmdlet para rubricar o VHD:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Executar este cmdlet para criar uma nova divisória:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Executar este cmdlet para formatar a partição:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Crie uma pasta dos pais no VHD montado. Este passo é obrigatório, uma vez que o anexo da aplicação MSIX requer uma pasta dos pais. Podes nomear a pasta dos pais o que quiseres.

### <a name="expand-msix"></a>Expandir MSIX

Depois disso, terá de "expandir" a imagem DOMix desempacotando-a. Para desempacotar a imagem DOM:

1. Abra um pedido de comando como Administrador e navegue para a pasta onde descarregou e desapertou a ferramenta msixmgr.

2. Executar o cmdlet seguinte para desempacotar o MSIX no VHD que criou e montou na secção anterior.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    A seguinte mensagem deve aparecer uma vez que a desembalagem é feita:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Se utilizar pacotes da Microsoft Store para negócios (ou educação) dentro da sua rede, ou em dispositivos que não estejam ligados à internet, terá de obter as licenças de pacote da Loja e instalá-las para executar a aplicação com sucesso. Ver [Utilizar as embalagens offline](#use-packages-offline).

3. Navegue para o VHD montado e abra a pasta da aplicação e confirme que o conteúdo do pacote está presente.

4. Desmonte o VHD.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Configure a infraestrutura virtual de desktop do Windows

Por design, um único pacote expandido MSIX (o VHD que criou na secção anterior) pode ser partilhado entre VMs de anfitrião de várias sessões, uma vez que os VHDs são ligados apenas no modo de leitura.

Antes de começar, certifique-se de que a sua partilha de rede satisfaz estes requisitos:

- A parte é compatível com SMB.
- Os VMs que fazem parte do pool de anfitriões da sessão têm permissões NTFS para a partilha.

### <a name="set-up-an-msix-app-attach-share"></a>Crie uma partilha de anexação de aplicativo MSIX

No seu ambiente de ambiente de trabalho virtual do Windows, crie uma partilha de rede e mova o pacote para lá.

>[!NOTE]
> A melhor prática para criar partilhas de rede MSIX é configurar a partilha de rede com permissões apenas de leitura NTFS.

## <a name="install-certificates"></a>Instalar certificados

Se a sua aplicação usa um certificado que não seja de confiança pública ou que tenha sido auto-assinado, eis como instalá-lo:

1. Clique com o botão direito no pacote e selecione **Propriedades.**
2. Na janela que aparece, selecione o separador **assinaturas Digitais.** Deve haver apenas um item na lista no separador, como mostrado na imagem seguinte. Selecione o item para realçar o item e, em seguida, selecione **Detalhes**.
3. Quando aparecer a janela de detalhes de assinatura digital, selecione o separador **Geral** e, em seguida, selecione **'Ver' Certificate**e, em seguida, selecione **o certificado de instalação**.
4. Quando o instalador abrir, selecione **a máquina local** como local de armazenamento e, em seguida, selecione **Seguinte**.
5. Se o instalador lhe perguntar se pretende permitir que a aplicação faça alterações no seu dispositivo, selecione **Sim**.
6. Selecione **Coloque todos os certificados na loja seguinte**e, em seguida, selecione **Procurar**.
7. Quando aparecer a janela da loja de certificados selecionada, **selecione Pessoas Fidedignas**e, em seguida, selecione **OK**.
8. Selecione **Seguinte** e **Termine**.

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
      1. `$contentID` é o valor ContentID do ficheiro de licença não codificado (.xml). Pode abrir o ficheiro de licença num editor de texto à sua escolha.
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
