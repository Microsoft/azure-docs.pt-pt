---
title: Microsoft Azure StorSimple Virtual Array Update 1.2 nota de lançamento Microsoft Docs
description: Descreve questões e resoluções críticas abertas para o StorSimple Virtual Array executando update 1.2.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "66420607"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Notas de lançamento storSimple Virtual Array Update 1.2

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de lançamento são continuamente atualizadas. À medida que são descobertos problemas críticos que requerem uma solução, os mesmos são adicionados a este documento. Antes de implementar o seu StorSimple Virtual Array, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 1.2 corresponde à versão do software **10.0.10311.0**.

> [!IMPORTANT]
> - As atualizações são disruptivas e reiniciam o seu dispositivo. Se o I/O estiver em curso, o dispositivo incorre no tempo de inatividade. Para obter instruções detalhadas sobre as embalagens utilizadas para aplicar esta atualização, vá ao [Download Update 1.2](#download-update-12).
> - A atualização 1.2 só está disponível através do portal Azure se o seu dispositivo estiver a executar o Update 1.0 ou 1.1.

## <a name="whats-new-in-update-12"></a>Novidades na Atualização 1.2

Esta atualização contém as seguintes correções de bugs:

- Melhoria da resiliência ao processar ficheiros apagados.
- Melhoria das exceções de manuseamento no caminho do arranque de código, levando a falhas reduzidas em backups, restauro, leituras na nuvem e recuperação automática de espaço.

## <a name="download-update-12"></a>Baixar Atualização 1.2

Para descarregar esta atualização, vá ao servidor do [Microsoft Update Catalog](https://www.catalog.update.microsoft.com/Home.aspx) e descarregue o pacote KB4502035. Este pacote contém os seguintes pacotes:

 - **KB4493446** que contém atualizações cumulativas do Windows para 2012 R2 até abril de 2019. Para mais informações sobre o que está incluído neste rollup, vá ao rollup mensal de [segurança de abril.](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446)
 - **KB3011067,** que é um ficheiro de ficheiro autónomo da Microsoft Update, WindowsTH-KB3011067-x64. Este ficheiro é utilizado para atualizar o software do dispositivo.

Baixe kB4502035 e siga estas instruções para [aplicar a atualização através](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)da Web UI local .

## <a name="issues-fixed-in-update-12"></a>Problemas corrigidos na Atualização 1.2

A tabela que se segue apresenta um resumo das questões fixadas nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Eliminação| Nas versões anteriores do software, houve um problema quando o uso do dispositivo não mudou mesmo quando os ficheiros foram apagados. Esta questão é corrigida nesta versão. O caminho do código de tiering tornou-se mais resistente no processamento de ficheiros apagados.|
| 2 |Processamento de exceções| Nas versões anteriores do software, houve um problema na sequência do reboot do sistema que poderia potencialmente levar a falhas nas cópias de segurança, restaurar, ler a partir da nuvem e recuperação automática do espaço. Esta versão contém alterações sobre a forma como as exceções foram tratadas no caminho do arranque.|

## <a name="known-issues-in-update-12"></a>Questões conhecidas na Atualização 1.2

Não foram lançados novos problemas no Update 1.2. Todas as questões de lançamento são transmitidas a partir de lançamentos anteriores. Para ver o resumo das questões conhecidas incluídas nos lançamentos anteriores, vá a [questões conhecidas no Update 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Passos seguintes

Baixe KB4502035 e [Aplique a atualização através](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui)da Web UI local .

## <a name="references"></a>Referências

À procura de uma nota de libertação mais antiga? Vá para:
* [Notas de lançamento da StorSimple Virtual Array Update 1.1](storsimple-virtual-array-update-11-release-notes.md)
* [Notas de lançamento da StorSimple Virtual Array Update 1.0](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple Virtual Array Update 0.6 Notas de lançamento](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple Virtual Array Update 0.5 Notas de lançamento](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple Virtual Array Update 0.4 Notas de lançamento](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple Virtual Array Update 0.3 Notas de lançamento](storsimple-ova-update-03-release-notes.md)
* [StorSimple Virtual Array Update 0.1 e 0.2 Notas de lançamento](storsimple-ova-update-01-release-notes.md)
* [Notas de lançamento de disponibilidade geral de matriz virtual StorSimple](storsimple-ova-pp-release-notes.md)
