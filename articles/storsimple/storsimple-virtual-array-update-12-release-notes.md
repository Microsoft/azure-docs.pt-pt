---
title: Microsoft Azure StorSimple Virtual Array Update 1.2 notas de lançamento| Microsoft Docs
description: Descreve problemas e resoluções abertos críticos para o StorSimple Virtual Array que executa a Atualização 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: 1118dfcec67a49365f1f6e5e522e98b97694d052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94960198"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>StorSimple Virtual Array Update 1.2 notas de lançamento

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de lançamento são continuamente atualizadas. À medida que são descobertos problemas críticos que requerem uma solução, os mesmos são adicionados a este documento. Antes de implementar o seu StorSimple Virtual Array, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 1.2 corresponde à versão do software **10.0.10311.0**.

> [!IMPORTANT]
> - As atualizações são disruptivas e reiniciam o seu dispositivo. Se a I/O estiver em andamento, o dispositivo incorre em tempo de inatividade. Para instruções detalhadas sobre os pacotes utilizados para aplicar esta atualização, aceda ao [Download Update 1.2](#download-update-12).
> - A atualização 1.2 só está disponível através do portal Azure se o seu dispositivo estiver a executar a atualização 1.0 ou 1.1.

## <a name="whats-new-in-update-12"></a>Novidades na Atualização 1.2

Esta atualização contém as seguintes correções de erro:

- Melhor resiliência ao processar ficheiros eliminados.
- Exceções de manuseamento melhoradas na trajetória de arranque de códigos que conduzem a falhas reduzidas em backups, restauros, leituras de nuvens e recuperação automática de espaço.

## <a name="download-update-12"></a>Baixar atualização 1.2

Para descarregar esta atualização, aceda ao servidor [do Catálogo microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) e descarregue o pacote KB4502035. Este pacote contém os seguintes pacotes:

 - **KB4493446** que contém atualizações cumulativas do Windows para 2012 R2 até abril de 2019. Para obter mais informações sobre o que está incluído neste rollup, vá ao rollup mensal de [segurança de abril.](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)
 - **KB3011067,** que é um ficheiro de pacote autónomo microsoft update WindowsTH-KB3011067-x64. Este ficheiro é utilizado para atualizar o software do dispositivo.

Baixe o KB4502035 e siga estas instruções para [aplicar a atualização através da web UI local](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problemas fixados na Atualização 1.2

O quadro seguinte fornece um resumo das questões corrigidas nesta versão.

| N.º | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Eliminação| Nas versões anteriores do software, houve um problema quando o uso do dispositivo não mudou mesmo quando os ficheiros foram eliminados. Esta questão é corrigida nesta versão. O caminho do código de tiering tornou-se mais resistente no processamento de ficheiros eliminados.|
| 2 |Processamento de exceções| Nas versões anteriores do software, havia um problema na sequência do reboot do sistema que poderia potencialmente levar a falhas nas cópias de segurança, restaurar, ler a partir da nuvem e recuperação automática de espaço. Esta versão contém alterações na forma como as exceções foram tratadas na trajetória de arranque.|

## <a name="known-issues-in-update-12"></a>Problemas conhecidos na Atualização 1.2

Não foram divulgados novos problemas no Update 1.2. Todos os problemas notados pelo lançamento são transitados de lançamentos anteriores. Para ver o resumo das questões conhecidas incluídas nas versões anteriores, vá a [edições conhecidas na Atualização 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Passos seguintes

Baixe o KB4502035 e [aplique a atualização através da web UI local.](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)

## <a name="references"></a>Referências

À procura de uma nota de lançamento mais antiga? Vá para:
* [StorSimple Virtual Array Update 1.1 Notas de lançamento](storsimple-virtual-array-update-11-release-notes.md)
* [StorSimple Virtual Array Update 1.0 Notas de lançamento](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 Notas de lançamento](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 Notas de lançamento](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 Notas de lançamento](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Notas de lançamento](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 e 0.2 Notas de lançamento](storsimple-ova-update-01-release-notes.md)
* [StorSimple Notas de lançamento de disponibilidade geral de matriz virtual](./storsimple-virtual-array-update-06-release-notes.md)