---
title: Utilização da ferramenta MSIXMGR - Azure
description: Como utilizar a ferramenta MSIXMGR para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4b34fb0d3bb2d49255007b9722a0a636c1441b8c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101746281"
---
# <a name="using-the-msixmgr-tool"></a>Utilização da ferramenta MSIXMGR

A ferramenta MSIXMGR destina-se a expandir aplicações embaladas pela MSIX em imagens MSIX. A ferramenta requer uma aplicação embalada pela MSIX (. MSIX) e expande-o para um ficheiro VHD, VHDx ou CIM. A imagem MSIX resultante é armazenada na conta de Armazenamento Azure que a sua implementação virtual do Windows Desktop utiliza. Este artigo irá mostrar-lhe como utilizar a ferramenta MSIXMGR.

>[!NOTE]
>Para garantir a compatibilidade, certifique-se de que os CIMs que armazenam as suas imagens MSIX são gerados na versão OS que está a executar nas piscinas de anfitriões virtual do Windows Desktop. O MSIXMGR pode criar ficheiros CIM, mas só pode utilizar esses ficheiros com uma piscina hospedeira a funcionar com o Windows 10 20H2.

## <a name="requirements"></a>Requisitos

Antes de poder seguir as instruções deste artigo, terá de fazer as seguintes coisas:

- [Descarregue a ferramenta MSIXMGR](https://aka.ms/msixmgr)
- Obtenha uma aplicação embalada pela MSIX (. Ficheiro MSIX)
- Obtenha permissões administrativas na máquina onde irá criar a imagem MSIX

## <a name="create-an-msix-image"></a>Criar uma imagem MSIX

Expansão é o processo de tomada de uma aplicação embalada MSIX (. MSIX) e desapertando-o numa imagem MSIX (. VHD(x) ou . Ficheiro CIM).

Para expandir um ficheiro MSIX:

1. [Descarregue a ferramenta MSIXMGR](https://aka.ms/msixmgr) se ainda não o fez.

2. Desaperte MSIXMGR.zip numa pasta local.

3. Abra uma solicitação de comando em modo elevado.

4. Encontre a pasta local a partir do passo 2.

5. Executar o seguinte comando no pedido de comando para criar uma imagem MSIX.

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    Lembre-se de substituir os valores do espaço reservado pelos valores relevantes. Por exemplo:

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. Agora que criou a imagem, vá à pasta de destino e certifique-se de que criou com sucesso a imagem MSIX (. VHDX).

## <a name="create-an-msix-image-in-a-cim-file"></a>Criar uma imagem MSIX num ficheiro CIM

Também pode utilizar o comando no [passo 5](#create-an-msix-image) para criar ficheiros CIM e VHDX substituindo o tipo de ficheiro e a trajetória de destino.

Por exemplo, eis como usaria esse comando para fazer um ficheiro CIM:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

Eis como usarias esse comando para fazer um VHDX:

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o anexo de aplicações MSIX no [que é anexação de aplicações MSIX?](what-is-app-attach.md)

Para aprender a configurar o anexo de aplicações, confira estes artigos:

- [Configurar a anexação de aplicações MSIX com o portal do Azure](app-attach-azure-portal.md)
- [Configurar o anexo de aplicativo MSIX utilizando o PowerShell](app-attach-powershell.md)
- [Criar scripts PowerShell para anexação de aplicações MSIX](app-attach.md)
- [Prepare uma imagem MSIX para o Windows Virtual Desktop](app-attach-image-prep.md)
- [Crie uma partilha de ficheiros para o anexo de aplicações MSIX](app-attach-file-share.md)

Se tiver dúvidas sobre o anexo de aplicações MSIX, consulte a nossa [App anexar FAQ](app-attach-faq.md) e [App attach glossary](app-attach-glossary.md).
