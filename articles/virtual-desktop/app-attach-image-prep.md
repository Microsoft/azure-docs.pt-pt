---
title: Windows Virtual Desktop preparar app MSIX anexar imagem - Azure
description: Como criar uma aplicação MSIX anexar imagem para um conjunto de anfitriões virtual do Windows Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 04/13/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 443f117907381862639564dfbf9752562f4a3564
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363670"
---
# <a name="prepare-an-msix-image-for-windows-virtual-desktop"></a>Prepare uma imagem MSIX para o Windows Virtual Desktop

O anexo de aplicações MSIX é uma solução de camadas de aplicação que permite anexar dinamicamente as aplicações de um pacote MSIX a uma sessão de utilizador. O sistema de pacotes MSIX separa as aplicações do sistema operativo, facilitando a construção de imagens para máquinas virtuais. Os pacotes MSIX também lhe dão um maior controlo sobre quais as aplicações que os seus utilizadores podem aceder nas suas máquinas virtuais. Pode até separar as aplicações da imagem principal e dá-las mais tarde aos utilizadores.

## <a name="create-a-vhd-or-vhdx-package-for-msix"></a>Crie um pacote VHD ou VHDX para MSIX

Os pacotes MSIX precisam de estar num formato VHD ou VHDX para funcionarem corretamente. Isto significa que, para começar, terá de criar um pacote VHD ou VHDX.

>[!NOTE]
>Se ainda não o fez, certifique-se de que ativa o Hyper-V seguindo as instruções [no Instalar Hyper-V no Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v).

Para criar um pacote VHD ou VHDX para MSIX:

1. Primeiro, abra o PowerShell.
2. Em seguida, executar o seguinte cmdlet para criar um VHD:

    ```powershell
    New-VHD -SizeBytes <size>MB -Path c:\temp\<name>.vhd -Dynamic -Confirm:$false
    ```

    >[!NOTE]
    > Certifique-se de que o VHD é grande o suficiente para manter o pacote MSIX expandido.

3. Executar o seguinte cmdlet para montar o VHD que acabou de criar:

    ```powershell
    $vhdObject = Mount-VHD c:\temp\<name>.vhd -Passthru
    ```

4. Em seguida, executar este cmdlet para rubricar o VHD montado:

    ```powershell
    $disk = Initialize-Disk -Passthru -Number $vhdObject.Number
    ```

5. Depois disso, executar este cmdlet para criar uma nova divisória para o VHD inicializado:

    ```powershell
    $partition = New-Partition -AssignDriveLetter -UseMaximumSize -DiskNumber $disk.Number
    ```

6. Executar este cmdlet para formatar a partição:

    ```powershell
    Format-Volume -FileSystem NTFS -Confirm:$false -DriveLetter $partition.DriveLetter -Force
    ```

7. Finalmente, crie uma pasta dos pais no VHD montado. Este passo é necessário porque o pacote MSIX deve ter uma pasta principal para funcionar corretamente. Não importa o nome da pasta dos pais, desde que exista a pasta dos pais.

## <a name="expand-msix"></a>Expandir MSIX

Depois disso, terá de expandir a imagem MSIX "desempacotando" os seus ficheiros para o VHD.

Para expandir a imagem MSIX:

1. [Descarregue a ferramenta msixmgr](https://aka.ms/msixmgr) e guarde a pasta .zip para uma pasta dentro de uma Sessão de VM do anfitrião.

2. Desaperte a ferramenta msixmgr .zip pasta.

3. Coloque a embalagem MSIX de origem na mesma pasta onde desapertou a ferramenta msixmgr.

4. Abra um pedido de comando como Administrador e navegue para a pasta onde descarregou e desapertou a ferramenta msixmgr.

5. Executar o cmdlet seguinte para desempacotar o MSIX no VHD que criou na secção anterior.

    ```powershell
    msixmgr.exe -Unpack -packagePath <package>.msix -destination "f:\<name of folder you created earlier>" -applyacls
    ```

    A seguinte mensagem deve aparecer depois de terminar de desempacotar:

    > AcLs desembalados e aplicados com sucesso para pacote: <package name> .msix

    >[!NOTE]
    > Se estiver a utilizar pacotes da Microsoft Store para negócios ou educação na sua rede ou em dispositivos não ligados à internet, terá de descarregar e instalar licenças de pacotes a partir da Microsoft Store para executar as aplicações. Para obter as licenças, consulte [use pacotes offline](app-attach.md#use-packages-offline).

6. Vá ao VHD montado e abra a pasta da aplicação para se certificar de que o conteúdo da embalagem está lá.

7. Desmonte o VHD.

## <a name="upload-msix-image-to-share"></a>Faça upload da imagem da MSIX para partilhar

Depois de ter criado o pacote MSIX, terá de carregar o ficheiro VHD, VHDX ou CIM resultante para uma partilha onde as máquinas virtuais dos seus utilizadores podem aceder ao mesmo.

## <a name="next-steps"></a>Passos seguintes

Faça perguntas à nossa comunidade sobre esta funcionalidade no [Windows Virtual Desktop TechCommunity](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop).

Também pode deixar feedback para o Windows Virtual Desktop no [centro de feedback virtual do Windows Desktop](https://support.microsoft.com/help/4021566/windows-10-send-feedback-to-microsoft-with-feedback-hub-app).

Aqui estão outros artigos que pode achar útil:

- [App MSIX anexa glossário](app-attach-glossary.md)
- [App MSIX anexa FAQ](app-attach-faq.md)