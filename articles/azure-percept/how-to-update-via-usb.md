---
title: Atualize o seu Azure Percept DK sobre uma ligação USB
description: Saiba como atualizar o Azure Percept DK sobre uma ligação USB
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 7f5e5e4da9fea671fc85a55fc8cc191fa14b720f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663007"
---
# <a name="how-to-update-azure-percept-dk-over-a-usb-connection"></a>Como atualizar o Azure Percept DK sobre uma ligação USB

Siga este guia para saber como executar uma atualização USB para o conselho de transporte do seu Azure Percept DK.

## <a name="prerequisites"></a>Pré-requisitos
- Computador anfitrião com uma porta USB-C ou USB-A disponível.
- Placa de transporte Azure Percept DK (dev kit) e fornecido USB-C para cabo USB-C. Se o seu computador anfitrião tiver uma porta USB-A mas não uma porta USB-C, poderá utilizar um cabo USB-C para USB-A (vendido separadamente).
- Instale [o PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) (acesso administrativo necessário).
- Instale a ferramenta UUU NXP. [Descarregue o](https://github.com/NXPmicro/mfgtools/releases) ficheiro de uuu.exe de lançamento mais recente (para Windows) ou o ficheiro uuu (para Linux) no separador Ativos.
- [Instale 7-Zip](https://www.7-zip.org/). Este software será utilizado para extrair o ficheiro de imagem em bruto do seu ficheiro comprimido XZ. Descarregue e instale o ficheiro .exe apropriado.

## <a name="steps"></a>Passos
1.  Descarregue os [seguintes três ficheiros USB Update](https://go.microsoft.com/fwlink/?linkid=2155734):
    - pe101-uefi-***&lt; número &gt; de versão***.raw.xz
    - emmc_full.txt
    - fast-hab-fw.raw
 
1. Extrai para **&lt; o &gt; número** da versão pe101-uefi-* .raw do número * _&lt; &gt;_ da versão pe101-uefi comprimido **.raw.xz. Não sabe como extrair? Faça o download e instale o 7-Zip, clique com o botão direito no ficheiro de imagem **.xz** e selecione extrato de 7-Zip &gt; Aqui.

1. Copie os seguintes três ficheiros para a pasta que contém a ferramenta UUU:
    - Pe101-uefi-***&lt; número &gt;*** de versão extraído .raw ficheiro (do passo 2).
    - emmc_full.txt (do passo 1).
    - fast-hab-fw.raw (a partir do passo 1).
 
1. Ligue o dev kit.
1. [Ligue ao dev kit sobre SSH](./how-to-ssh-into-percept-dk.md)
1. Abra uma solicitação de comando do Windows (Iniciar &gt; cmd) ou um terminal Linux e navegue para a pasta onde os ficheiros de atualização estão armazenados. Executar o seguinte comando para iniciar a atualização:
    - Windows: ```uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    - Linux: ```sudo ./uuu -b emmc_full.txt fast-hab-fw.raw pe101-uefi-<version number>.raw```
    
Depois de executar estes comandos, pode ver uma mensagem indicando "À espera do dispositivo..." no pedido de comando. Isto é esperado e deve seguir para o próximo passo.
    
1. Ligue a placa de porta-kits dev ao computador anfitrião através de um cabo USB. Ligue-se sempre das placas de porta-transportes porta USB-C à porta USB-C ou USB-A do computador anfitrião (cabo USB-C a USB-A vendido separadamente), dependendo das portas disponíveis. 
 
1. No terminal SSH/PuTTY, insira os seguintes comandos para definir o kit dev no modo USB e, em seguida, reiniciar o kit dev.
    - ```flagutil    -wBfRequestUsbFlash    -v1```
    - ```reboot -f```
 
1. Pode obter uma indicação de que o computador anfitrião reconhece o dispositivo e que o processo de atualização será iniciado automaticamente. Volte ao comando para ver o estado. O processo levará até dez minutos e quando a atualização for bem sucedida, verá uma mensagem indicando "Sucesso 1 Falha 0"
 
1. Uma vez concluída a atualização, desligue a placa do porta-aviões. Desligue o cabo USB do PC.  Ligue o módulo Azure Percept Vision de volta à placa de transporte utilizando o cabo USB.

1. Ligue o porta-aviões de volta.

## <a name="next-steps"></a>Passos seguintes

O seu kit dev está agora atualizado com sucesso. Pode continuar o desenvolvimento e a operação com o seu devkit.