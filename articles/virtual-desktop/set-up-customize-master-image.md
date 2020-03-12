---
title: Prepare e personalize uma imagem Master VHD - Azure
description: Como preparar, personalizar e carregar uma imagem master do Windows Virtual Desktop para o Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 867b327ac25d51cd3955e622da9d8067ae6d9ae9
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127722"
---
# <a name="prepare-and-customize-a-master-vhd-image"></a>Preparar e personalizar uma imagem VHD principal

Este artigo diz-lhe como preparar uma imagem de disco rígido virtual (VHD) para o upload para o Azure, incluindo como criar máquinas virtuais (VMs) e instalar software neles. Estas instruções são para uma configuração específica do Windows Virtual Desktop que pode ser usada com os processos existentes da sua organização.

## <a name="create-a-vm"></a>Criar uma VM

A multi-sessão do Windows 10 Enterprise está disponível na Galeria de Imagem Azure. Existem duas opções para personalizar esta imagem.

A primeira opção é fornecer uma máquina virtual (VM) em Azure seguindo as instruções em [Create a VM a partir de uma imagem gerida,](../virtual-machines/windows/create-vm-generalized-managed.md)e depois saltar para a frente para [a preparação e instalação](set-up-customize-master-image.md#software-preparation-and-installation)do Software.

A segunda opção é criar a imagem localmente, baixando a imagem, disponibilizando um VM Hyper-V, e personalizando-a de acordo com as suas necessidades, que cobrimos na seguinte secção.

### <a name="local-image-creation"></a>Criação de imagem local

Assim que descarregar a imagem para um local local, abra o **Hyper-V Manager** para criar um VM com o VHD que copiou. As seguintes instruções são uma versão simples, mas pode encontrar instruções mais detalhadas em [Criar uma máquina virtual em Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v/).

Para criar um VM com o VHD copiado:

1. Abra o **Novo Assistente de Máquina Virtual.**

2. Na página 'Geração Especificação', selecione **Geração 1**.

    ![Uma imagem da página 'Geração Especificação'. A opção "Geração 1" é selecionada.](media/a41174fd41302a181e46385e1e701975.png)

3. No ponto de verificação, desative os pontos de verificação desligando a caixa de verificação.

    ![Uma imagem da secção Tipo checkpoint da página Checkpoints.](media/20c6dda51d7cafef33251188ae1c0c6a.png)

Também pode executar o seguinte cmdlet no PowerShell para desativar os pontos de verificação.

```powershell
Set-VM -Name <VMNAME> -CheckpointType Disabled
```

### <a name="fixed-disk"></a>Disco fixo

Se criar um VM a partir de um VHD existente, cria um disco dinâmico por padrão. Pode ser alterado para um disco fixo selecionando edit **disk...** como mostrado na imagem seguinte. Para obter instruções mais detalhadas, consulte [Prepare um VHD do Windows ou VHDX para fazer o upload para O Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md).

![Uma imagem da opção Edit Disk.](media/35772414b5a0f81f06f54065561d1414.png)

Também pode executar o seguinte cmdlet PowerShell para mudar o disco para um disco fixo.

```powershell
Convert-VHD –Path c:\\test\\MY-VM.vhdx –DestinationPath c:\\test\\MY-NEW-VM.vhd -VHDType Fixed
```

## <a name="software-preparation-and-installation"></a>Preparação e instalação de software

Esta secção abrange como preparar e instalar o FSLogix e o Windows Defender, bem como algumas opções básicas de configuração para apps e registo da sua imagem. 

Se estiver a instalar o Office 365 ProPlus e o OneDrive no seu VM, dirija-se ao [Office de instalação numa imagem Master VHD](install-office-on-wvd-master-image.md) e siga as instruções para instalar as aplicações. Depois de terminar, volte a este artigo.

Se os seus utilizadores precisarem de aceder a determinadas aplicações LOB, recomendamos que as instale depois de completar as instruções desta secção.

### <a name="set-up-user-profile-container-fslogix"></a>Configurar o recipiente de perfil do utilizador (FSLogix)

Para incluir o recipiente FSLogix como parte da imagem, siga as instruções em Criar um recipiente de perfil para uma piscina hospedeira utilizando uma partilha de [ficheiros](create-host-pools-user-profile.md#configure-the-fslogix-profile-container). Pode testar a funcionalidade do recipiente FSLogix com [este arranque rápido](/fslogix/configure-cloud-cache-tutorial/).

### <a name="configure-windows-defender"></a>Configure O Defensor do Windows

Se o Windows Defender estiver configurado no VM, certifique-se de que está configurado para não digitalizar todo o conteúdo dos ficheiros VHD e VHDX durante o anexo.

Esta configuração remove apenas a digitalização de ficheiros VHD e VHDX durante o anexo, mas não afetará a digitalização em tempo real.

Para obter instruções mais detalhadas sobre como configurar o Windows Defender no Windows Server, consulte as [exclusões do Antivírus Do Windows No Windows Server](/windows/security/threat-protection/windows-defender-antivirus/configure-server-exclusions-windows-defender-antivirus/).

Para saber mais sobre como configurar o Windows Defender para excluir certos ficheiros da digitalização, consulte [configurar e validar exclusões com base na extensão](/windows/security/threat-protection/windows-defender-antivirus/configure-extension-file-exclusions-windows-defender-antivirus/)do ficheiro e na localização da pasta .

### <a name="disable-automatic-updates"></a>Desativar atualizações automáticas

Para desativar as atualizações automáticas através da Política de Grupo local:

1. Open **Local Group Policy Editor\\Modelos Administrativos\\componentes do Windows\\Atualização do Windows**.
2. Clique na **configuração automática** e desative-a para **desativar**.

Também pode executar o seguinte comando numa solicitação de comando para desativar atualizações automáticas.

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU" /v NoAutoUpdate /t REG_DWORD /d 1 /f
```

### <a name="specify-start-layout-for-windows-10-pcs-optional"></a>Especificar o layout de início para PCs do Windows 10 (opcional)

Execute este comando para especificar um layout iniciar para computadores Windows 10.

```batch
reg add "HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer" /v SpecialRoamingOverrideAllowed /t REG_DWORD /d 1 /f
```

### <a name="set-up-time-zone-redirection"></a>Configurar a redirecção do fuso horário

A reorientação do fuso horário pode ser aplicada a nível de Política de Grupo, uma vez que todos os VMs numa piscina de acolhimento fazem parte do mesmo grupo de segurança.

Para redirecionar fusos horários:

1. No servidor De Diretório Ativo, abra a Consola de **Gestão**de Políticas do Grupo .
2. Expanda o seu domínio e objetos de política de grupo.
3. Clique no **Objeto** de Política de Grupo que criou para as definições de política do grupo e **selecione Editar**.
4. No Editor de **Gestão**de Políticas do Grupo, navegue para as **políticas** de **configuração** de computador >  > **modelos administrativos** > componentes do Windows > **serviços** de ambiente de **trabalho remotos** > dispositivo > de **sessão de ambiente** de trabalho remoto e **redirecionamento**de recursos.
5. Ativar a regulação de **redirecionamento do fuso horário.**

Também pode executar este comando na imagem principal para redirecionar fusos horários:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fEnableTimeZoneRedirection /t REG_DWORD /d 1 /f
```

### <a name="disable-storage-sense"></a>Desativar o sentido de armazenamento

Para o anfitrião da sessão de desktop virtual do Windows que utiliza a multi-sessão do Windows 10 Enterprise ou do Windows 10 Enterprise, recomendamos que seja desativado o Storage Sense. Pode desativar o Sentido de Armazenamento no menu Definições em **Armazenamento,** como mostra a seguinte imagem:

![Uma imagem do menu de armazenamento em Definições. A opção "Sentido de Armazenamento" está desligada.](media/storagesense.png)

Também pode alterar a definição com o registo executando o seguinte comando:

```batch
reg add "HKCU\Software\Microsoft\Windows\CurrentVersion\StorageSense\Parameters\StoragePolicy" /v 01 /t REG_DWORD /d 0 /f
```

### <a name="include-additional-language-support"></a>Incluir suporte linguístico adicional

Este artigo não cobre como configurar a linguagem e o apoio regional. Para obter mais informações, veja os artigos seguintes:

- [Adicione idiomas às imagens do Windows](/windows-hardware/manufacture/desktop/add-language-packs-to-windows/)
- [Características a pedido](/windows-hardware/manufacture/desktop/features-on-demand-v2--capabilities/)
- [Características linguísticas e região a pedido (FOD)](/windows-hardware/manufacture/desktop/features-on-demand-language-fod/)

### <a name="other-applications-and-registry-configuration"></a>Outras aplicações e configuração do registo

Esta secção cobre a configuração da aplicação e do sistema operativo. Toda a configuração nesta secção é feita através de entradas de registo que podem ser executadas por ferramentas de linha de comando e regedit.

>[!NOTE]
>Pode implementar as melhores práticas na configuração com objetos de política de grupo (GPOs) ou importações de registo. O administrador pode escolher qualquer opção com base nos requisitos da sua organização.

Para obter a recolha de dados de telemetria no Windows 10 Enterprise multi-sessão, execute este comando:

```batch
reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection" /v AllowTelemetry /t REG_DWORD /d 3 /f
```

Executar o seguinte comando para corrigir acidentes de Watson:

```batch
remove CorporateWerServer* from Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\Windows Error Reporting
```

Insira os seguintes comandos no editor de registo para fixar suporte de resolução de 5k. Tem de executar os comandos antes de poder ativar a pilha lado a lado.

```batch
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MaxYResolution /t REG_DWORD /d 2880 /f

reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxMonitors /t REG_DWORD /d 4 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxXResolution /t REG_DWORD /d 5120 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" /v MaxYResolution /t REG_DWORD /d 2880 /f
```

## <a name="prepare-the-image-for-upload-to-azure"></a>Prepare a imagem para o upload para Azure

Depois de terminar a configuração e instalar todas as aplicações, siga as instruções em [Preparar um Windows VHD ou VHDX para fazer o upload para o Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) para preparar a imagem.

Depois de preparar a imagem para upload, certifique-se de que o VM permanece no estado de folga ou de negócio.

## <a name="upload-master-image-to-a-storage-account-in-azure"></a>Faça upload da imagem principal para uma conta de armazenamento em Azure

Esta secção só se aplica quando a imagem principal foi criada localmente.

As seguintes instruções dirão como enviar a sua imagem principal para uma conta de armazenamento Azure. Se ainda não tem uma conta de armazenamento Azure, siga as instruções [deste artigo](/azure/javascript/tutorial-vscode-static-website-node-03) para criar uma.

1. Converta a imagem VM (VHD) para Fixed se ainda não o fez. Se não converter a imagem para Fixed, não pode criar a imagem com sucesso.

2. Faça upload do VHD para um recipiente de bolhas na sua conta de armazenamento. Pode fazer o upload rapidamente com a [ferramenta Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Para saber mais sobre a ferramenta Storage Explorer, consulte [este artigo](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

    ![Uma imagem da janela de pesquisa da Ferramenta de Armazenamento Microsoft Azure. A caixa de verificação "Upload .vhd ou vhdx as page blobs (recomendado)" é selecionada.](media/897aa9a9b6acc0aa775c31e7fd82df02.png)

3. Em seguida, vá ao portal Azure no seu navegador e procure "Imagens". A sua pesquisa deve levá-lo à página **de imagem Criar,** como mostra a seguinte imagem:

    ![Uma imagem da página de imagem Create do portal Azure, repleta de valores de exemplo para a imagem.](media/d3c840fe3e2430c8b9b1f44b27d2bf4f.png)

4. Uma vez criada a imagem, deve ver uma notificação como a da seguinte imagem:

    ![Uma imagem da notificação "imagem criada com sucesso".](media/1f41b7192824a2950718a2b7bb9e9d69.png)

## <a name="next-steps"></a>Passos seguintes

Agora que tem uma imagem, pode criar ou atualizar piscinas de anfitriões. Para saber mais sobre como criar e atualizar piscinas de anfitriões, consulte os seguintes artigos:

- [Crie uma piscina de anfitriões com um modelo de Gestor de Recursos Azure](create-host-pools-arm-template.md)
- [Tutorial: Crie uma piscina de anfitriões com o Azure Marketplace](create-host-pools-azure-marketplace.md)
- [Crie uma piscina de anfitriões com powerShell](create-host-pools-powershell.md)
- [Crie um recipiente de perfil para uma piscina anfitriã usando uma partilha de ficheiros](create-host-pools-user-profile.md)
- [Configure o método de equilíbrio de carga virtual do Windows](configure-host-pool-load-balancing.md)
