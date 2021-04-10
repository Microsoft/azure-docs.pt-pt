---
title: Instale pacotes de idiomas no Windows 10 VMs no Windows Virtual Desktop - Azure
description: Como instalar pacotes de idiomas para VMs multi-sessão do Windows 10 no Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 12/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fc7892db2ca11ab7970835f8979360961ee01104
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463345"
---
# <a name="add-language-packs-to-a-windows-10-multi-session-image"></a>Adicione pacotes de idiomas a uma imagem multi-sessão do Windows 10

O Windows Virtual Desktop é um serviço que os seus utilizadores podem implementar a qualquer momento, em qualquer lugar. É por isso que é importante que os seus utilizadores sejam capazes de personalizar qual o idioma que as suas imagens multi-sessão do Windows 10 Enterprise exibem.

Existem duas formas de acomodar as necessidades linguísticas dos seus utilizadores:

- Construa piscinas de anfitriões dedicadas com uma imagem personalizada para cada idioma.
- Ter utilizadores com diferentes requisitos de idioma e localização na mesma piscina de anfitrião, mas personalizar as suas imagens para garantir que podem selecionar o idioma de que precisam.

Este último método é muito mais eficiente e rentável. No entanto, cabe-lhe a si decidir qual o método que melhor se adequa às suas necessidades. Este artigo irá mostrar-lhe como personalizar idiomas para as suas imagens.

## <a name="prerequisites"></a>Pré-requisitos

Precisa das seguintes coisas para personalizar as suas imagens multi-sessão do Windows 10 Enterprise para adicionar vários idiomas:

- Uma máquina virtual Azure (VM) com várias sessões do Windows 10 Enterprise, versão 1903 ou posterior

- O Iso linguístico, o disco de procura (FOD) 1 e o Inbox Apps ISO da versão OS que a imagem utiliza. Pode descarregá-las aqui:
     
     - Idioma ISO:
        - [Windows 10, versão 1903 ou 1909 Language Pack ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)
        - [Windows 10, versão 2004 ou 20H2 Language Pack ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_CLIENTLANGPACKDVD_OEM_MULTI.iso)

     - DISCO FOD 1 ISO:
        - [Windows 10, versão 1903 ou 1909 FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        - [Windows 10, versão 2004 ou 20H2 FOD Disk 1 ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_FOD-PACKAGES_OEM_PT1_amd64fre_MULTI.iso)
        
     - Aplicativos inbox ISO:
        - [Windows 10, versão 1903 ou 1909 Aplicações inbox ISO](https://software-download.microsoft.com/download/pr/18362.1.190318-1202.19h1_release_amd64fre_InboxApps.iso)
        - [Windows 10, versão 2004 Inbox Apps ISO](https://software-download.microsoft.com/download/pr/19041.1.191206-1406.vb_release_amd64fre_InboxApps.iso)
        - [Windows 10, versão 20H2 Aplicações inbox ISO](https://software-download.microsoft.com/download/pr/19041.508.200905-1327.vb_release_svc_prod1_amd64fre_InboxApps.iso)
     
     - Se utilizar ficheiros ISO (LXP) locais para localizar as suas imagens, também terá de descarregar o LXP ISO apropriado para obter a melhor experiência linguística
        - Se estiver a utilizar o Windows 10, versão 1903 ou 1909:
          - [Windows 10, versão 1903 ou LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_1903_32_64_ARM64_MultiLng_LngPkAll_LXP_ONLY.iso)
        - Se estiver a utilizar o Windows 10, versão 2004 ou 20H2, utilize a informação em [Adicionar idiomas no Windows 10: Problemas conhecidos](/windows-hardware/manufacture/desktop/language-packs-known-issue) para descobrir qual dos seguintes ISOs LXP é o certo para si:
          - [Windows 10, versão 2004 ou 20H2 **9B** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_64_ARM64_MultiLang_LangPckAll_LIP_LXP_ONLY)
          - [Windows 10, versão 2004 ou 20H2 **9C** LXP ISO](https://software-download.microsoft.com/download/pr/Win_10_2004_32_64_ARM64_MultiLng_LngPkAll_LIP_9C_LXP_ONLY)
          - [Windows 10, versão 2004 ou 20H2 **10C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2010C.iso)
          - [Windows 10, versão 2004 ou 20H2 **11C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2011C.iso)
          - [Windows 10, versão 2004 ou 20H2 **1C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2101C.iso)
          - [Windows 10, versão 2004 ou 20H2 **2C** LXP ISO](https://software-download.microsoft.com/download/pr/LanguageExperiencePack.2102C.iso)

- Partilha de ficheiros Azure ou partilha de ficheiros numa máquina virtual do servidor de ficheiros do Windows

>[!NOTE]
>A partilha de ficheiros (repositório) deve estar acessível a partir do Azure VM que pretende utilizar para criar a imagem personalizada.

## <a name="create-a-content-repository-for-language-packages-and-features-on-demand"></a>Criar um repositório de conteúdos para pacotes linguísticos e funcionalidades a pedido

Para criar o repositório de conteúdos para pacotes linguísticos e FODs e um repositório para os pacotes de Aplicações inbox:

1. Numa VM Azure, descarregue as aplicações multi-linguísticos do Windows 10 ISO, FODs e Inbox para várias sessões empresariais do Windows 10, versão 1903/1909 e imagens de 2004 a partir dos links em [Pré-requisitos.](#prerequisites)

2. Abra e monte os ficheiros ISO no VM.

3. Vá ao pacote de idiomas ISO e copie o conteúdo das **pastas LocalExperiencePacks** e **x64 \\ langpacks** e, em seguida, cole o conteúdo na partilha de ficheiros.

4. Vá ao **ficheiro FOD ISO,** copie todo o seu conteúdo e, em seguida, cole-o na partilha de ficheiros.
5. Vá à pasta **amd64fre** na Caixa de Entrada ISO e copie o conteúdo do repositório para as aplicações da caixa de entrada que preparou.

     >[!NOTE]
     > Se estiver a trabalhar com armazenamento limitado, apenas copie os ficheiros para os idiomas de que os seus utilizadores precisam. Pode distinguir os ficheiros olhando para os códigos de linguagem nos seus nomes de ficheiros. Por exemplo, o ficheiro francês tem o código "fr-FR" em seu nome. Para obter uma lista completa de códigos linguísticos para todos os [idiomas disponíveis, consulte pacotes de idiomas disponíveis para windows](/windows-hardware/manufacture/desktop/available-language-packs-for-windows).

     >[!IMPORTANT]
     > Algumas línguas requerem fontes adicionais incluídas em pacotes de satélite que seguem diferentes convenções de nomeação. Por exemplo, os nomes dos ficheiros de fonte japonesa incluem "Jpan".
     >
     > [!div class="mx-imgBorder"]
     > ![Um exemplo dos pacotes de língua japonesa com a etiqueta linguística "Jpan" nos seus nomes de ficheiros.](media/language-pack-example.png)

6. Desajuste as permissões na partilha do repositório de conteúdos linguísticos para que tenha lido o acesso a partir do VM que utilizará para construir a imagem personalizada.

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-manually"></a>Crie manualmente uma imagem multi-sessão personalizada do Windows 10 Enterprise

Para criar manualmente uma imagem multi-sessão personalizada do Windows 10 Enterprise:

1. Implemente um Azure VM, em seguida, vá à Galeria Azure e selecione a versão atual da multi-sessão do Windows 10 Enterprise que está a utilizar.
2. Depois de ter implantado o VM, ligue-o usando RDP como administrador local.
3. Certifique-se de que o seu VM tem todas as atualizações mais recentes do Windows. Faça o download das atualizações e reinicie o VM, se necessário.
4. Conecte-se ao pacote de idiomas, FOD e Inbox Apps compartilhar repositório e montá-lo para uma unidade de letra (por exemplo, unidade E).

## <a name="create-a-custom-windows-10-enterprise-multi-session-image-automatically"></a>Crie automaticamente uma imagem multi-sessão personalizada do Windows 10 Enterprise

Se preferir instalar idiomas através de um processo automatizado, pode configurar um script no PowerShell. Pode utilizar a seguinte amostra de script para instalar os pacotes de línguas espanhol (Espanha), Francês (França) e chinês (PRC) e pacotes de satélite para o Windows 10 Enterprise multi-sessão, versão 2004. O script integra o pacote de interface de linguagem e todos os pacotes de satélite necessários na imagem. No entanto, também pode modificar este script para instalar outros idiomas. Apenas certifique-se de executar o script de uma sessão elevada de PowerShell, ou então não funcionará.

```powershell
########################################################
## Add Languages to running Windows Image for Capture##
########################################################

##Disable Language Pack Cleanup##
Disable-ScheduledTask -TaskPath "\Microsoft\Windows\AppxDeploymentClient\" -TaskName "Pre-staged app cleanup"

##Set Language Pack Content Stores##
[string]$LIPContent = "E:"

##Spanish##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\es-es\LanguageExperiencePack.es-es.Neutral.appx -LicensePath $LIPContent\es-es\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_es-es.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-es-es-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~es-es~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~es-es~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
Set-WinUserLanguageList $LanguageList -force

##French##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\fr-fr\LanguageExperiencePack.fr-fr.Neutral.appx -LicensePath $LIPContent\fr-fr\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_fr-fr.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-fr-fr-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~fr-fr~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~fr-FR~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~fr-FR~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("fr-fr")
Set-WinUserLanguageList $LanguageList -force

##Chinese(PRC)##
Add-AppProvisionedPackage -Online -PackagePath $LIPContent\zh-cn\LanguageExperiencePack.zh-cn.Neutral.appx -LicensePath $LIPContent\zh-cn\License.xml
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Client-Language-Pack_x64_zh-cn.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Basic-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Fonts-Hans-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Handwriting-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-OCR-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-Speech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-LanguageFeatures-TextToSpeech-zh-cn-Package~31bf3856ad364e35~amd64~~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-NetFx3-OnDemand-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-MSPaint-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Notepad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-PowerShell-ISE-FOD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-Printing-WFS-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-StepsRecorder-Package~31bf3856ad364e35~amd64~zh-cn~.cab
Add-WindowsPackage -Online -PackagePath $LIPContent\Microsoft-Windows-WordPad-FoD-Package~31bf3856ad364e35~amd64~zh-cn~.cab
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

O script pode demorar algum tempo, dependendo do número de idiomas que precisa de instalar.

Uma vez que o script esteja terminado, verifique se os pacotes de idiomas instalados corretamente indo para **iniciar**  >  **definições** tempo &  >    >  **idioma**. Se os ficheiros linguísticos estiverem lá, está tudo preparado.

Depois de adicionar idiomas adicionais à imagem do Windows, as aplicações da caixa de entrada também são necessárias para serem atualizadas para suportar os idiomas adicionados. Isto pode ser feito refrescando as aplicações pré-instaladas com o conteúdo das aplicações de caixa de entrada ISO. Para realizar esta atualização num ambiente desligado (sem acesso à Internet a partir do VM possível), pode utilizar a seguinte amostra de script PowerShell para automatizar o processo.

```powershell
#########################################
## Update Inbox Apps for Multi Language##
#########################################
##Set Inbox App Package Content Stores##
[string]$InboxApps = "F:\"
##Update Inbox Store Apps##
$AllAppx = Get-Item $inboxapps\*.appx | Select-Object name
$AllAppxBundles = Get-Item $inboxapps\*.appxbundle | Select-Object name
$allAppxXML = Get-Item $inboxapps\*.xml | Select-Object name
foreach ($Appx in $AllAppx) {
    $appname = $appx.name.substring(0,$Appx.name.length-5)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    
    Write-Host "Handeling with xml $appname"  
  
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
      
      Write-Host "Handeling without xml $appname"
      
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
foreach ($Appx in $AllAppxBundles) {
    $appname = $appx.name.substring(0,$Appx.name.length-11)
    $appnamexml = $appname + ".xml"
    $pathappx = $InboxApps + "\" + $appx.Name
    $pathxml = $InboxApps + "\" + $appnamexml
    
    if($allAppxXML.name.Contains($appnamexml)){
    Write-Host "Handeling with xml $appname"
    
    Add-AppxProvisionedPackage -Online -PackagePath $pathappx -LicensePath $pathxml
    } else {
       Write-Host "Handeling without xml $appname"
      Add-AppxProvisionedPackage -Online -PackagePath $pathappx -skiplicense
    }
}
```

>[!IMPORTANT]
>As aplicações de caixa de entrada incluídas na ISO não são as versões mais recentes das aplicações pré-instaladas do Windows. Para obter a versão mais recente de todas as aplicações, é necessário atualizar as aplicações utilizando a App Windows Store e realizar uma pesquisa manual de atualizações depois de ter instalado os idiomas adicionais.

Quando terminar, certifique-se de desligar a parte.

## <a name="finish-customizing-your-image"></a>Termine de personalizar a sua imagem

Depois de instalar os pacotes de idiomas, pode instalar qualquer outro software que queira adicionar à sua imagem personalizada.

Uma vez terminada a personalização da sua imagem, terá de executar a ferramenta de preparação do sistema (sysprep).

Para executar sysprep:

1. Abra uma solicitação de comando elevada e executar o seguinte comando para generalizar a imagem:  
   
     ```cmd
     C:\Windows\System32\Sysprep\sysprep.exe /oobe /generalize /shutdown
     ```

2. Pare o VM e, em seguida, capture-o numa imagem gerida seguindo as instruções em [Criar uma imagem gerida de um VM generalizado em Azure](../virtual-machines/windows/capture-image-resource.md).

3. Agora pode utilizar a imagem personalizada para implementar um conjunto de anfitriões virtual do Windows Desktop. Para aprender a implantar uma piscina de anfitriões, consulte [Tutorial: Crie uma piscina de anfitriões com o portal Azure.](create-host-pools-azure-marketplace.md)

## <a name="enable-languages-in-windows-settings-app"></a>Ativar idiomas na aplicação de definições do Windows

Finalmente, depois de implementar o pool anfitrião, terá de adicionar o idioma à lista de idiomas de cada utilizador para que possam selecionar o seu idioma preferido no menu Definições.

Para garantir que os seus utilizadores podem selecionar os idiomas instalados, iniciar sômposições como utilizador e, em seguida, executar o cmdlet PowerShell seguinte para adicionar os pacotes de idiomas instalados ao menu idiomas. Também pode configurar este script como uma tarefa automatizada ou script de início de sessão que se ativa quando o utilizador se inscreve na sua sessão.

```powershell
$LanguageList = Get-WinUserLanguageList
$LanguageList.Add("es-es")
$LanguageList.Add("fr-fr")
$LanguageList.Add("zh-cn")
Set-WinUserLanguageList $LanguageList -force
```

Depois de um utilizador alterar as suas definições de idioma, terá de iniciar sessão de trabalho virtual do Windows e iniciar novamente o seu trabalho para que as alterações produzam efeitos. 

## <a name="next-steps"></a>Passos seguintes

Se está curioso sobre problemas conhecidos para pacotes de idiomas, consulte [adicionar pacotes de idiomas no Windows 10, versão 1803 e versões posteriores: Problemas conhecidos](/windows-hardware/manufacture/desktop/language-packs-known-issue).

Se tiver mais alguma dúvida sobre o Windows 10 Enterprise multi-sessão, consulte as nossas [FAQ](windows-10-multisession-faq.md).
