---
title: Atualize o seu Azure Percept DK sobre uma ligação USB
description: Saiba como atualizar o Azure Percept DK sobre uma ligação USB
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/18/2021
ms.custom: template-how-to
ms.openlocfilehash: cd6e4e62123b4d4b927cf385aaf64a066eecc1e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104887755"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Como atualizar o Azure Percept DK sobre uma ligação USB

Embora a utilização de atualizações over-the-air (OTA) seja o melhor método para manter o sistema operativo e o firmware do seu dev kit atualizado, existem cenários em que é necessário atualizar (ou "piscar") o kit dev sobre uma ligação USB:

- Uma atualização da OTA não é possível devido à conectividade ou a outros problemas técnicos
- O dispositivo precisa de ser reposto ao seu estado de fábrica

Este guia irá mostrar-lhe como atualizar com sucesso o sistema operativo e o firmware do seu kit dev sobre uma ligação USB.

> [!WARNING]
> A atualização do seu kit dev sobre USB eliminará todos os dados existentes no dispositivo, incluindo modelos e recipientes de IA.
>
> Siga todas as instruções em ordem. Saltar passos pode colocar o seu kit dev em um estado inutilizável.

## <a name="prerequisites"></a>Pré-requisitos

- Um Azure Percept DK
- Um computador anfitrião baseado em Windows, Linux ou OS X com capacidade Wi-Fi e uma porta USB-C ou USB-A disponível
- Um cabo USB-C a USB-A (opcional, vendido separadamente)
- Um login SSH, criado durante a [experiência de configuração Azure Percept DK](./quickstart-percept-dk-set-up.md)

## <a name="download-software-tools-and-update-files"></a>Descarregue ferramentas de software e atualização de ficheiros

1. [Ferramenta NXP UUU](https://github.com/NXPmicro/mfgtools/releases). Descarregue o ficheiro de uuu.exe de **lançamento mais recente** (para Windows) ou o ficheiro uuu (para Linux) no **separador Ativos.**

1. [7-Zip](https://www.7-zip.org/). Este software será utilizado para extrair o ficheiro de imagem em bruto do seu ficheiro comprimido XZ. Descarregue e instale o ficheiro .exe apropriado.

1. [Descarregue os ficheiros de atualização](https://go.microsoft.com/fwlink/?linkid=2155734).

1. Certifique-se de que os três artefactos de construção estão presentes:
    - Número da *&lt; versão &gt;* Azure-Percept-DK .raw.xz
    - fast-hab-fw.raw
    - emmc_full.txt

## <a name="set-up-your-environment"></a>Configurar o ambiente

1. Crie uma pasta/diretório no computador anfitrião num local de fácil acesso através da linha de comando.

1. Copie a ferramenta UUU **(uuu.exe** ou **uuu)** para a nova pasta.

1. Extrair o ***&lt; &gt; número* de versão Azure-Percept-DK .raw** ficheiro do ficheiro comprimido clicando corretamente no ***&lt; &gt; número* da versão Azure-Percept-DK .raw.xz** e selecionando aqui o extrato **de 7-Zip** &gt; .

1. Mova o número  ***&lt; &gt;* de versão Azure-Percept-DK** extraído .raw ficheiro, **fast-hab-fw.raw**, eemmc_full.txtpara a pasta que contém a ferramenta UUU.

## <a name="update-your-device"></a>Atualizar o seu dispositivo

1. [SSH no seu kit dev.](./how-to-ssh-into-percept-dk.md)

1. Em seguida, abra uma posição de comando do Windows **(Iniciar**  >  **cmd)** ou um terminal Linux e navegue para a pasta onde os ficheiros de atualização e a ferramenta UUU estão armazenados. Introduza o seguinte comando na pronta ou terminal de comando para preparar o computador para receber um dispositivo intermitente:

    - Windows:

        ```console
        uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw 
        ```

    - Linux:

        ```bash
        sudo ./uuu -b emmc_full.txt fast-hab-fw.raw Azure-Percept-DK-<version number>.raw
        ```

1. Desligue o dispositivo Azure Percept Vision da porta USB-C da placa transportadora.

1. Ligue o cabo USB-C fornecido à porta USB-C da placa transportadora e à porta USB-C do computador anfitrião. Se o seu computador tiver apenas uma porta USB-A, ligue um cabo USB-C a USB-A (vendido separadamente) à placa de transporte e ao computador anfitrião.

1. No pedido de cliente SSH, insira os seguintes comandos:

    1. Coloque o dispositivo no modo de atualização USB:

        ```bash
        sudo flagutil    -wBfRequestUsbFlash    -v1
        ```

    1. Reinicie o dispositivo. A instalação da atualização começará.

        ```bash
        sudo reboot -f
        ```

1. Navegue de volta para o outro comando ou terminal. Quando a atualização estiver concluída, verá uma mensagem ```Success 1    Failure 0``` com:

    > [!NOTE]
    > Após a atualização, o seu dispositivo será reiniciado para as definições de fábrica e perderá a sua ligação Wi-Fi e o login SSH.

1. Uma vez concluída a atualização, desligue a placa do porta-aviões. Desligue o cabo USB do PC.  

## <a name="next-steps"></a>Passos seguintes

Trabalhe através da [experiência de configuração Azure Percept DK](./quickstart-percept-dk-set-up.md) para reconfigurar o seu dispositivo.