---
title: Aplicativo MSIX do Windows Virtual Desktop - Azure
description: Como configurar a aplicação MSIX anexapara o Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: ec69a9906eabb4ce56f79b1b88c2b5f2440f84b1
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612474"
---
# <a name="set-up-msix-app-attach"></a>Configurar a anexação da aplicação MSIX

> [!IMPORTANT]
> A anexação da aplicação MSIX encontra-se atualmente em pré-visualização pública.
> Esta versão de pré-visualização é fornecida sem um acordo de nível de serviço, e não recomendamos usá-la para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para mais informações, consulte [os Termos Suplementares de Utilização para pré-visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)do Microsoft Azure .

Este tópico irá acompanhá-lo sobre como configurar a aplicação MSIX anexar-se num ambiente de ambiente de trabalho virtual do Windows.

## <a name="requirements"></a>Requisitos

Antes de começar, eis o que precisa para configurar a anexação da aplicação MSIX:

- O acesso ao portal Insider do Windows para obter a versão do Windows 10 com suporte para a aplicação MSIX anexa mAP.
- Uma implementação funcional do Windows Virtual Desktop. Para obter informações, consulte [Criar um inquilino no Windows Virtual Desktop](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
- A ferramenta de embalagem MSIX
- Uma partilha de rede na sua implementação do Windows Virtual Desktop onde o pacote MSIX será armazenado

## <a name="get-the-os-image"></a>Obtenha a imagem de OS

Primeiro, precisa de obter a imagem de OS que utilizará para a aplicação MSIX. Para obter a imagem de OS:

1. Abra o [portal Windows Insider](https://www.microsoft.com/software-download/windowsinsiderpreviewadvanced?wa=wsignin1.0) e inscreva-se.

     >[!NOTE]
     >Deve ser membro do programa Insider do Windows para aceder ao portal Insider do Windows. Para saber mais sobre o programa Windows Insider, consulte a [documentação do Windows Insider](/windows-insider/at-home/).

2. Desloque-se até à secção **de edição Select** e selecione **Windows 10 Insider Preview Enterprise (FAST) – Build 19035** ou mais tarde.

3. Selecione **Confirmar,** então selecione o idioma que deseja utilizar e, em seguida, selecione **Confirmar** novamente.
    
     >[!NOTE]
     >Neste momento, o inglês é a única língua que foi testada com a funcionalidade. Pode selecionar outras línguas, mas podem não exibir como pretendido.
    
4. Quando o link de descarregamento for gerado, selecione o **Download de 64 bits** e guarde-o para o disco rígido local.

## <a name="prepare-the-vhd-image-for-azure"></a>Prepare a imagem VHD para Azure 

Antes de começar, terá de criar uma imagem vHD mestre. Se ainda não criou a sua imagem De Mestre VHD, vá à [Prepare e personalize uma imagem VHD mestre](set-up-customize-master-image.md) e siga as instruções lá. 

Depois de ter criado a sua imagem Master VHD, deve desativar as atualizações automáticas para aplicações msix anexar aplicações. Para desativar as atualizações automáticas, terá de executar os seguintes comandos num pedido de comando elevado:

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

Em seguida, prepare o VM VHD para Azure e carregue o disco VHD resultante para O Azure. Para saber mais, consulte [Preparar e personalizar uma imagem VHD mestre](set-up-customize-master-image.md).

Depois de ter enviado o VHD para o Azure, crie uma piscina de anfitriões baseada nesta nova imagem seguindo as instruções na [piscina de anfitriões Create a host, utilizando o tutorial Azure Marketplace.](create-host-pools-azure-marketplace.md)

## <a name="prepare-the-application-for-msix-app-attach"></a>Preparar a aplicação para a anexação da aplicação MSIX 

Se já tem um pacote MSIX, salte para a configuração da [infraestrutura virtual do Ambiente](#configure-windows-virtual-desktop-infrastructure)de trabalho do Windows . Se pretender testar aplicações antigas, siga as instruções em [Criar um pacote MSIX a partir de um instalador de ambiente](/windows/msix/packaging-tool/create-app-package-msi-vm/) de trabalho num VM para converter a aplicação legacy num pacote MSIX.

## <a name="generate-a-vhd-or-vhdx-package-for-msix"></a>Gere um pacote VHD ou VHDX para MSIX

Os pacotes estão em formato VHD ou VHDX para otimizar o desempenho. O MSIX requer que os pacotes VHD ou VHDX funcionem corretamente.

Para gerar um pacote VHD ou VHDX para MSIX:

1. [Descarregue a ferramenta msixmgr](https://aka.ms/msixmgr) e guarde a pasta .zip para uma pasta dentro de um VM anfitrião de sessão.

2. Desinsera a pasta msixmgr .zip.

3. Coloque a embalagem MSIX de origem na mesma pasta onde desaperte a ferramenta msixmgr.

4. Executar o seguinte cmdlet no PowerShell para criar um VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    >Certifique-se de que o tamanho de VHD é grande o suficiente para segurar o MSIX expandido.*

5. Executar o seguinte cmdlet para montar o VHD recém-criado:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

6. Executar este cmdlet para inicializar o VHD:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

7. Execute este cmdlet para criar uma nova partição:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

8. Execute este cmdlet para formatar a partição:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

9. Crie uma pasta dos pais no VHD montado. Este passo é obrigatório, uma vez que a aplicação MSIX anexa requer uma pasta-mãe. Pode nomear a pasta dos pais o que quiser.

### <a name="expand-msix"></a>Expandir MSIX

Depois disso, terá de "expandir" a imagem MSIX desempacotando-a. Para desembalar a imagem MSIX:

1. Abra um pedido de comando como Administrador e navegue para a pasta onde descarregou e desaperte a ferramenta msixmgr.

2. Passe o seguinte cmdlet para desembalar o MSIX no VHD que criou e montou na secção anterior.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    A seguinte mensagem deve aparecer uma vez feita a desembalagem:

    `Successfully unpacked and applied ACLs for package: <package name>.msix`

    >[!NOTE]
    > Se utilizar pacotes da Microsoft Store for Business (ou Education) dentro da sua rede, ou em dispositivos que não estejam ligados à internet, terá de obter as licenças de pacote da Loja e instalá-las para executar a app com sucesso. Consulte [as embalagens de utilização offline](#use-packages-offline).

3. Navegue para o VHD montado e abra a pasta da aplicação e confirme que o conteúdo do pacote está presente.

4. Desmonte o VHD.

## <a name="configure-windows-virtual-desktop-infrastructure"></a>Configure a infraestrutura virtual do ambiente de trabalho do Windows

Por design, um único pacote expandido MSIX (o VHD que criou na secção anterior) pode ser partilhado entre VMs hospedeiros de várias sessões, uma vez que os VHDs estão ligados no modo de leitura.

Antes de começar, certifique-se de que a sua quota de rede satisfaz estes requisitos:

- A parte é compatível com SMB.
- Os VMs que fazem parte da piscina anfitriã da sessão têm permissões NTFS para a parte.

### <a name="set-up-an-msix-app-attach-share"></a>Configurar uma parte anexa da aplicação MSIX 

No ambiente do Windows Virtual Desktop, crie uma partilha de rede e mova o pacote para lá.

>[!NOTE]
> A melhor prática para criar ações de rede MSIX é criar a partilha de rede com permissões de leitura NTFS.

## <a name="install-certificates"></a>Instalar certificados

Se a sua aplicação usa um certificado que não é de confiança pública ou foi auto-assinado, eis como instalá-lo:

1. Clique na embalagem e selecione **Propriedades**.
2. Na janela que aparece, selecione o separador **de assinaturas Digitais.** Só deve haver um item na lista no separador, como mostra a seguinte imagem. Selecione este item para realçar o item e, em seguida, selecione **Detalhes**.
3. Quando aparecer a janela de detalhes de assinatura digital, selecione o separador **Geral** e, em seguida, selecione **o certificado De instalação**.
4. Quando o instalador abrir, selecione a **máquina local** como local de armazenamento e, em seguida, selecione **Next**.
5. Se o instalador lhe perguntar se pretende permitir que a aplicação faça alterações no seu dispositivo, selecione **Sim**.
6. Selecione **Coloque todos os certificados na seguinte loja**e, em seguida, selecione **'Navegar**' .
7. Quando aparecer a janela do depósito de certificados, selecione **pessoas fidedignas**e, em seguida, selecione **OK**.
8. Selecione **Concluir**.

## <a name="prepare-powershell-scripts-for-msix-app-attach"></a>Prepare scripts PowerShell para anexação de aplicativos MSIX

A anexação da aplicação MSIX tem quatro fases distintas que devem ser executadas na seguinte ordem:

1. Fase
2. Registar
3. Desregistre
4. Palco

Cada fase cria um script PowerShell. Os scripts de amostra para cada fase estão disponíveis [aqui](https://github.com/Azure/RDS-Templates/tree/master/msix-app-attach).

### <a name="stage-the-powershell-script"></a>Encenar o script PowerShell

Antes de atualizar os scripts PowerShell, certifique-se de que tem o volume GUID do volume no VHD. Para obter o volume GUID:

1.  Abra a partilha de rede onde o VHD está localizado dentro do VM onde você executará o script.

2.  Clique no VHD e selecione **Mount**. Isto vai montar o VHD numa carta de condução.

3.  Depois de montar o VHD, a janela do Explorador de **Ficheiros** abrir-se-á. Capture a pasta-mãe e atualize a variável **$parentFolder**

    >[!NOTE]
    >Se não vires uma pasta dos pais, isso significa que o MSIX não foi devidamente expandido. Refaça a secção anterior e tente novamente.

4.  Abra a pasta dos pais. Se for corretamente expandido, verá uma pasta com o mesmo nome que a embalagem. Atualize a **variável $packageName** para combinar com o nome desta pasta.

    Por exemplo, `VSCodeUserSetup-x64-1.38.1_1.38.1.0_x64__8wekyb3d8bbwe`.

5.  Abra um pedido de comando e entre em **mountvol**. Este comando apresentará uma lista de volumes e seus GUIDs. Copie o GUID do volume onde a letra de unidade corresponde à unidade a que montou o seu VHD no passo 2.

    Por exemplo, neste exemplo, a saída para o comando mountvol, se montou o seu VHD para conduzir C, vai querer copiar o valor acima: `C:\`

    ```cmd
    Possible values for VolumeName along with current mount points are:

    \\?\Volume{a12b3456-0000-0000-0000-10000000000}\
    *** NO MOUNT POINTS ***

    \\?\Volume{c78d9012-0000-0000-0000-20000000000}\
        E:\

    \\?\Volume{d34e5678-0000-0000-0000-30000000000}\
        C:\

    ```


6.  Atualize a **variável $volumeGuid** com o volume GUID que acabou de copiar.

7. Abra um aviso de PowerShell da Admin e atualize o seguinte script PowerShell com as variáveis que se aplicam ao seu ambiente.

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

    [Windows.Management.Deployment.PackageManager,Windows.Management.Deployment,ContentType=WindowsRuntime]
    | Out-Null

    Add-Type -AssemblyName System.Runtime.WindowsRuntime

    $asTask = ([System.WindowsRuntimeSystemExtensions].GetMethods() | Where {
    $_.ToString() -eq 'System.Threading.Tasks.Task`1[TResult]
    AsTask[TResult,TProgress](Windows.Foundation.IAsyncOperationWithProgress`2[TResult,TProgress])'})[0]

    $asTaskAsyncOperation =
    $asTask.MakeGenericMethod([Windows.Management.Deployment.DeploymentResult],
    [Windows.Management.Deployment.DeploymentProgress])

    $packageManager = [Windows.Management.Deployment.PackageManager]::new()

    $path = $msixJunction + $parentFolder + $packageName # needed if we do the pbisigned.vhd

    $path = ([System.Uri]$path).AbsoluteUri

    $asyncOperation = $packageManager.StagePackageAsync($path, $null, "StageInPlace")

    $task = $asTaskAsyncOperation.Invoke($null, @($asyncOperation))

    $task

    #endregion
    ```

### <a name="register-powershell-script"></a>Registar o script PowerShell

Para executar o script de registo, execute os seguintes cmdlets PowerShell com os valores do espaço reservado substituídos por valores que se aplicam ao seu ambiente.

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

### <a name="deregister-powershell-script"></a>Desregistre o script PowerShell

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

### <a name="destage-powershell-script"></a>Destage PowerShell script

Para este script, substitua o espaço reservado para **$packageName** pelo nome do pacote que está a testar.

```powershell
#MSIX app attach de staging sample

#region variables

$packageName = "<package name>"

$msixJunction = "C:\temp\AppAttach\"

#endregion

#region deregister

Remove-AppxPackage -AllUsers -Package $packageName

cd $msixJunction

rmdir $packageName -Force -Verbose

#endregion
```

## <a name="set-up-simulation-scripts-for-the-msix-app-attach-agent"></a>Configurar scripts de simulação para o agente anexado da aplicação MSIX

Depois de criar os scripts, os utilizadores podem executá-los manualmente ou instalá-los para funcionar automaticamente como scripts de arranque, logon, logoff e encerramento. Para saber mais sobre este tipo de scripts, consulte [Utilizar scripts de arranque, encerramento, logon e logoff na Política](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn789196(v=ws.11)/)de Grupo .

Cada um destes scripts automáticos executa uma fase da aplicação anexando scripts:

- O guião de arranque executa o guião do palco.
- O script de logon executa o script de registo.
- O script de logoff executa o script de desregistro.
- O guião de encerramento executa o guião de desencenação.

## <a name="use-packages-offline"></a>Use pacotes offline

Se estiver a utilizar pacotes da [Microsoft Store for Business](https://businessstore.microsoft.com/) ou da Microsoft Store for [Education](https://educationstore.microsoft.com/) dentro da sua rede ou em dispositivos que não estejam ligados à internet, precisa de obter as licenças de pacote da Microsoft Store e instalá-las no seu dispositivo para executar com sucesso a aplicação. Se o seu dispositivo estiver on-line e puder ligar-se à Microsoft Store for Business, as licenças necessárias devem ser descarregadas automaticamente, mas se estiver offline, terá de configurar as licenças manualmente. 

Para instalar os ficheiros de licença, terá de utilizar um script PowerShell que chame a classe MDM_EnterpriseModernAppManagement_StoreLicenses02_01 no Fornecedor de Pontes WMI.  

Aqui está como configurar as licenças para uso offline: 

1. Descarregue o pacote de aplicações, licenças e quadros necessários da Microsoft Store for Business. Precisa dos ficheiros de licença codificados e não codificados. Instruções detalhadas de descarregamento podem ser encontradas [aqui](/microsoft-store/distribute-offline-apps#download-an-offline-licensed-app).
2. Atualize as seguintes variáveis no script para o passo 3:
      1. `$contentID`é o valor ContentID do ficheiro de licença não codificado (.xml). Pode abrir o ficheiro da licença num editor de texto à sua escolha.
      2. `$licenseBlob`é a cadeia inteira para a bolha de licença no ficheiro de licença codificada (.bin). Pode abrir o ficheiro de licença codificado num editor de texto à sua escolha. 
3. Execute o seguinte script a partir de um aviso de Admin PowerShell. Um bom lugar para realizar a instalação de licença é no final do roteiro de [encenação](#stage-the-powershell-script) que também precisa ser executado a partir de um pedido de administrador.

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

Também pode deixar feedback para o Windows Virtual Desktop no centro de feedback do [Windows Virtual Desktop](https://aka.ms/MRSFeedbackHub), ou deixar feedback para a aplicação MSIX e ferramenta de embalagem na [aplicação MSIX anexar o centro](https://aka.ms/msixappattachfeedback) de feedback e o centro de feedback da ferramenta de embalagem [MSIX](https://aka.ms/msixtoolfeedback).
