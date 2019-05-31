---
title: Notas de versão do Microsoft Azure StorSimple Virtual matriz atualização 1.2 | Documentos da Microsoft
description: Descreve problemas em aberto críticos e resoluções para o StorSimple Virtual Array atualização 1.2 a executar.
services: storsimple
author: alkohli
ms.service: storsimple
ms.topic: article
ms.date: 05/29/2019
ms.author: alkohli
ms.openlocfilehash: ea7e4801dfaad533403c0f927a03735ae409cc52
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66420607"
---
# <a name="storsimple-virtual-array-update-12-release-notes"></a>Notas de versão 1.2 de atualização de matriz Virtual StorSimple

As seguintes notas de versão identificam os problemas em aberto críticos e os problemas resolvidos para atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de versão são atualizadas continuamente. À medida que são descobertos problemas críticos que requerem uma solução, eles são adicionados. Antes de implementar a sua matriz Virtual StorSimple, reveja com atenção as informações contidas nas notas de versão.

Atualização 1.2 corresponde à versão software **10.0.10311.0**.

> [!IMPORTANT]
> - As atualizações são disruptivas e reinicie o seu dispositivo. Se forem e/s em curso, o dispositivo incorre em tempo de inatividade. Para obter instruções detalhadas sobre os pacotes utilizados para aplicar esta atualização, aceda a [transferir 1.2 atualizar](#download-update-12).
> - Atualização 1.2 está disponível através do portal do Azure apenas se o seu dispositivo está a executar atualização 1.0 ou 1.1.

## <a name="whats-new-in-update-12"></a>O que há de novo na atualização 1.2

Esta atualização contém as seguintes correções de bugs:

- Resiliência melhorada ao processar os arquivos excluídos.
- O processamento melhorado exceções no caminho da inicialização de código que leva a reduzida falhas em cópias de segurança, restauro, leituras da cloud e automatizada de recuperação de espaço.

## <a name="download-update-12"></a>Transferir a atualização 1.2

Para transferir esta atualização, vá para o [catálogo Microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) server e o pacote de transferência a KB4502035. Este pacote contém os seguintes pacotes:

 - **KB4493446** que contém as atualizações cumulativas do Windows para o 2012 R2 até de 2019 de Abril. Para obter mais informações sobre o que inclui este rollup, aceda a [agregação de segurança mensal de Abril](https://support.microsoft.com/help/4493446/windows-8-1-update-kb4493446).
 - **KB3011067** que é um ficheiro de pacote do Microsoft Update autónomo WindowsTH-KB3011067-x64. Este ficheiro é utilizado para atualizar o software de dispositivo.

Transferir KB4502035 e siga estas instruções para [aplicar a atualização através da IU web local](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="issues-fixed-in-update-12"></a>Problemas corrigidos na atualização 1.2

A tabela seguinte fornece um resumo dos problemas corrigidos nesta versão.

| Não. | Funcionalidade | Problema |
| --- | --- | --- |
| 1 |Eliminação| Nas versões anteriores do software, Ocorreu um problema quando a utilização do dispositivo não alterou, mesmo quando os ficheiros foram eliminados. Este problema ser corrigido nesta versão. Caminho do código camadas foi efetuado mais resiliente durante o processamento de ficheiros eliminados.|
| 2 |Manipulação de exceção| Nas versões anteriores do software, Ocorreu um problema após a reinicialização do sistema que pode potencialmente conduzir a falhas em cópias de segurança, restauro, ler a partir da cloud e recuperação de espaço de automatizadas. Esta versão contém alterações em relação a como as exceções foram processadas no caminho da inicialização.|

## <a name="known-issues-in-update-12"></a>Problemas conhecidos na atualização 1.2

Não existem problemas novos foram versão observado na atualização 1.2. Todos os problemas de indicação de versão são transportados de versões anteriores. Para ver o resumo dos problemas conhecidos, incluído a partir de versões anteriores, aceda à [problemas conhecidos no Update 1.1](storsimple-virtual-array-update-11-release-notes.md#known-issues-in-update-11).

## <a name="next-steps"></a>Passos Seguintes

Transferir KB4502035 e [aplicar a atualização através da IU web local](storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="references"></a>Referências

À procura de uma nota de versão mais antiga? Vá para:
* [Notas de versão 1.1 do StorSimple Virtual Array atualização](storsimple-virtual-array-update-11-release-notes.md)
* [Notas de versão 1.0 do StorSimple Virtual Array atualização](storsimple-virtual-array-update-1-release-notes.md)
* [Notas de versão 0,6 de atualização de matriz Virtual StorSimple](storsimple-virtual-array-update-06-release-notes.md)
* [Notas de versão 0,5 de atualização de matriz Virtual StorSimple](storsimple-virtual-array-update-05-release-notes.md)
* [Notas de versão 0.4 de atualização de matriz Virtual StorSimple](storsimple-virtual-array-update-04-release-notes.md)
* [Notas de versão 0.3 de atualização de matriz Virtual StorSimple](storsimple-ova-update-03-release-notes.md)
* [Notas de versão do StorSimple Virtual Array atualização 0,1 e 0,2](storsimple-ova-update-01-release-notes.md)
* [Notas de versão de disponibilidade de geral de matriz Virtual StorSimple](storsimple-ova-pp-release-notes.md)
