---
title: Prepare e personalize uma imagem master VHD - Azure
description: Como preparar, personalizar e carregar uma imagem principal do Windows Virtual Desktop para o Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 01/19/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: ca92e6e888ead6bbd79c7a8d524516ee95e8c45d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092931"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Preparar e personalizar uma imagem VHD principal

Este artigo diz-lhe como preparar uma imagem de disco rígido virtual (VHD) para o upload para a Azure, incluindo como criar máquinas virtuais (VMs) e instalar software nelas. Estas instruções são para uma configuração virtual do Windows que pode ser usada com os processos existentes da sua organização.

>[!IMPORTANT]
>Recomendamos que utilize uma imagem da Galeria de Imagens Azure. No entanto, se precisar de utilizar uma imagem personalizada, certifique-se de que já não tem o Windows Virtual Desktop Agent instalado no seu VM. A utilização de uma imagem personalizada com o Windows Virtual Desktop Agent pode causar problemas com a imagem, como bloquear o registo e impedir ligações de sessão do utilizador.  

## <a name="create-a-vm"></a>Criar uma VM

O Windows 10 Enterprise está disponível na Galeria de Imagens Azure. Existem duas opções para personalizar esta imagem.

A primeira opção é a disponibilização de uma máquina virtual (VM) em Azure, seguindo as instruções na [Create a VM a partir de uma imagem gerida](../virtual-machines/windows/create-vm-generalized-managed.md), e depois avançar para a [preparação e instalação do Software](set-up-customize-master-image.md#software-preparation-and-installation).

A segunda opção é criar a imagem localmente, descarregando a imagem, provisionando um Hiper-VM, e personalizando-a de acordo com as suas necessidades, que cobrimos na secção seguinte.

### <a name="local-image-creation"></a>Criação de imagem local

Depois de ter descarregado a imagem para um local local, abra o **Hyper-V Manager** para criar um VM com o VHD que copiou. As seguintes instruções são uma versão simples, mas pode encontrar instruções mais detalhadas na [Criação de uma máquina virtual em Hiper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Para criar um VM com o VHD copiado:

1. Abra o **novo assistente de máquinas virtuais**.

2. Na página Especificar Geração, selecione **Geração 1**.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem da página Especifica geração. A opção "Geração 1" é selecionada.](media/a41174fd41302a181e46385e1e701975.png)

3. No tipo checkpoint, desative os pontos de verificação desmarcando a caixa de verificação.

    > [!div class="mx-imgBorder"]
    > ![Uma imagem da secção do tipo checkpoint da página Checkpoints.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Também pode executar o seguinte cmdlet em PowerShell para desativar os pontos de verificação.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Disco fixo

Se criar um VM a partir de um VHD existente, cria um disco dinâmico por padrão. Pode ser alterado para um disco fixo selecionando **EditAr Disco...** como mostrado na imagem seguinte. Para obter instruções mais detalhadas, consulte [Prepare um VHD ou VHDX do Windows para fazer o upload para Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

> [!div class="mx-imgBorder"]
> ![Uma imagem da opção Edit Disco.](media/35772414b5a0f81f06f54065561d1414.png)

Também pode executar o cmdlet PowerShell seguinte para alterar o disco para um disco fixo.

```powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Preparação e instalação de software

Esta secção abrange como preparar e instalar o FSLogix e o Windows Defender, bem como algumas opções básicas de configuração para apps e o registo da sua imagem.

Se estiver a instalar aplicações microsoft 365 para empresa e OneDrive no seu VM, vá ao [Install Office numa imagem master VHD](install-office-on-wvd-master-image.md) e siga as instruções para instalar as aplicações. Depois de terminar, volte a este artigo.

Se os seus utilizadores precisarem de aceder a certas aplicações LOB, recomendamos que as instale depois de completar as instruções desta secção.

### <a name="set-up-user-profile-container-fslogix"></a>Configurar o recipiente de perfil do utilizador (FSLogix)

Para incluir o recipiente FSLogix como parte da imagem, siga as instruções na [Criar um recipiente de perfil para uma piscina hospedeira utilizando uma partilha de ficheiros](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Pode testar a funcionalidade do recipiente FSLogix com [este arranque rápido](/fslogix/configure-cloud-cache-tutorial/).

### <a name="configure-windows-defender"></a>Configure Windows Defender

Se o Windows Defender estiver configurado no VM, certifique-se de que está configurado para não digitalizar todo o conteúdo dos ficheiros VHD e VHDX durante o anexo.

Esta configuração apenas remove a digitalização dos ficheiros VHD e VHDX durante o anexo, mas não afetará a digitalização em tempo real.

Para obter instruções mais detalhadas sobre como configurar o Windows Defender no Windows Server, consulte [as exclusões antivírus do Windows Defender no Windows Defender No Windows Server](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Para saber mais sobre como configurar o Windows Defender para excluir certos ficheiros da digitalização, consulte [Configure e valide exclusões com base na extensão de ficheiros e localização da pasta](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/).

### <a name="disable-automatic-updates"></a>Desativar atualizações automáticas

Para desativar as atualizações automáticas através da política de grupo local:

1. Abra **os modelos administrativos do editor de política do grupo \\ \\ local, windows components \\ Windows Update**.
2. Clique com o botão direito **Configurar a Atualização Automática** e defini-la para **Desativado**.

Também pode executar o seguinte comando numa solicitação de comando para desativar atualizações automáticas.

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Especificar o layout iniciar para computadores windows 10 (opcional)

Executar este comando para especificar um layout Iniciar para computadores Windows 10.

```cmd
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Configurar a reorientação do fuso horário

A reorientação do fuso horário pode ser aplicada ao nível da Política de Grupo, uma vez que todos os VMs de uma piscina de acolhimento fazem parte do mesmo grupo de segurança.

Para redirecionar os fusos horários:

1. No servidor Ative Directory, abra a **Consola de Gestão de Políticas de Grupo**.
2. Expanda o seu domínio e objetos de política de grupo.
3. Clique com o botão direito no **Objeto de Política de Grupo** que criou para as definições de política de grupo e selecione **Editar**.
4. No Editor de **Gestão de Políticas** de Grupo , navegue para políticas **de configuração de computador**  >    >  **Modelos administrativos** Windows Componentes  >  **Componentes**  >  **Remotos Desktop** Session  >  **Host** Device e  >  **Reorientação de Recursos**.
5. Ativar a **definição de reorientação do fuso horário** Desemitamento.

Também pode executar este comando na imagem principal para redirecionar fusos horários:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Desativar o sentido de armazenamento

Para o anfitrião de sessão virtual do Windows Desktop que utilize o Windows 10 Enterprise ou o Windows 10 Enterprise multi-sessão, recomendamos desativar o Storage Sense. Pode desativar o Sentido de Armazenamento no menu Definições em **Armazenamento**, como mostra a seguinte imagem:

> [!div class="mx-imgBorder"]
> ![Uma imagem do menu de armazenamento em Definições. A opção "Sentido de armazenamento" está desligada.](media/storagesense.png)

Também pode alterar a definição com o registo executando o seguinte comando:

```cmd
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Incluir suporte linguístico adicional

Este artigo não abrange como configurar a linguagem e o apoio regional. Para obter mais informações, veja os seguintes artigos:

- [Adicionar idiomas às imagens do Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Características a pedido](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Características linguísticas e regionis a pedido (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Outras aplicações e configuração de registo

Esta secção abrange a configuração da aplicação e do sistema operativo. Toda a configuração desta secção é feita através de entradas de registo que podem ser executadas por ferramentas de linha de comando e regedit.

>[!NOTE]
>Pode implementar as melhores práticas na configuração com os Objetos de Política de Grupo (GPOs) ou com as importações de registo. O administrador pode escolher qualquer uma das opções com base nos requisitos da sua organização.

Para a recolha de dados de telemetria do Hub de feedback na multi-sessão do Windows 10 Enterprise, execute este comando:

```cmd
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Executar o seguinte comando para corrigir acidentes watson:

```cmd
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Introduza os seguintes comandos no editor de registo para corrigir o suporte de resolução 5k. Tem de executar os comandos antes de poder ativar a pilha lado a lado.

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Prepare a imagem para o upload para Azure

Depois de terminar a configuração e instalar todas as aplicações, siga as instruções em [Prepare um VHD ou VHDX do Windows para fazer o upload para Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) para preparar a imagem.

Depois de preparar a imagem para o upload, certifique-se de que o VM permanece no estado off ou deallocated.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Faça upload da imagem principal para uma conta de armazenamento em Azure

Esta secção só se aplica quando a imagem principal foi criada localmente.

As seguintes instruções dir-lhe-ão como enviar a sua imagem principal para uma conta de armazenamento Azure. Se ainda não tiver uma conta de armazenamento Azure, siga as instruções [deste artigo](/azure/developer/javascript/tutorial-vscode-static-website-node-03) para criar uma.

1. Converta a imagem VM (VHD) em Fixed se ainda não o fez. Se não converter a imagem em Fixed, não poderá criar a imagem com sucesso.

2. Faça o upload do VHD para um recipiente de bolhas na sua conta de armazenamento. Pode fazer o upload rapidamente com a [ferramenta Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Para saber mais sobre a ferramenta Storage Explorer, consulte [este artigo.](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)

    > [!div class="mx-imgBorder"]
    > ![Uma imagem da janela de pesquisa da Ferramenta do Explorador de Armazenamento microsoft Azure. É selecionada a caixa de verificação "Upload .vhd ou vhdx como bolhas de página (recomendadas)".](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Em seguida, vá ao portal Azure no seu navegador e procure por "Imagens". A sua pesquisa deve levá-lo à página **de imagem Criar,** como mostrado na seguinte imagem:

    > [!div class="mx-imgBorder"]
    > ![Uma imagem da página de imagem Create do portal Azure, preenchida com valores de exemplo para a imagem.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Uma vez criada a imagem, deverá ver uma notificação como a da seguinte imagem:

    > [!div class="mx-imgBorder"]
    > ![Uma imagem da notificação "imagem criada com sucesso".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma imagem, pode criar ou atualizar piscinas de anfitriões. Para saber mais sobre como criar e atualizar piscinas de anfitriões, consulte os seguintes artigos:

- [Criar um conjunto de anfitriões com um modelo do Azure Resource Manager](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)
- [Tutorial: Criar uma piscina de anfitriões com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Criar um conjunto de anfitriões com o PowerShell](create-host-pools-powershell.md)
- [Criar um contentor de perfis para um conjunto de anfitriões através de uma partilha de ficheiros](create-host-pools-user-profile.md)
- [Configurar o método de balanceamento de carga do Windows Virtual Desktop](configure-host-pool-load-balancing.md)

Se encontrou um problema de conectividade depois de preparar ou personalizar a sua imagem VHD, consulte o [guia de resolução de problemas](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) para obter ajuda.
