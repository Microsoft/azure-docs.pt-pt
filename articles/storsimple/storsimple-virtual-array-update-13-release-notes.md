---
title: Microsoft Azure StorSimple Virtual Array Update 1.3 notas de lançamento | Microsoft Docs
description: Descreve problemas e resoluções abertos críticos para o Azure StorSimple Virtual Array em execução Update 1.3.
ms.service: storsimple
author: v-dalc
ms.topic: article
ms.date: 03/17/2021
ms.author: alkohli
ms.openlocfilehash: b2c2af4267140370c2aa9421a67cb3a6f413165f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104657557"
---
# <a name="storsimple-virtual-array-update-13-release-notes"></a>StorSimple Virtual Array Update 1.3 notas de lançamento

As seguintes notas de lançamento identificam os problemas abertos críticos e os problemas resolvidos para as atualizações do Microsoft Azure StorSimple Virtual Array.

As notas de lançamento são continuamente atualizadas. À medida que são descobertos problemas críticos que requerem uma solução, os mesmos são adicionados a este documento. Antes de implementar o seu StorSimple Virtual Array, reveja cuidadosamente as informações contidas nas notas de lançamento.

A atualização 1.3 corresponde à versão do software 10.0.10319.0.

> [!IMPORTANT]
> - A atualização 1.3 é uma atualização crítica. Recomendamos vivamente que o instale o mais rapidamente possível.
> - Só pode instalar o Update 1.3 nos dispositivos em execução update 1.2.
> - As atualizações são disruptivas e reiniciam o seu dispositivo. Se a I/O estiver em curso, o dispositivo incorre em tempo de inatividade. Para instruções detalhadas sobre os pacotes utilizados para aplicar esta atualização, aceda ao [Download Update 1.3](#download-update-13).

## <a name="whats-new-in-update-13"></a>Novidades na Atualização 1.3

Esta atualização contém as seguintes melhorias:KB4540725

- A Segurança da Camada de Transporte (TLS) 1.2 é uma atualização obrigatória e deve ser instalada. A partir desta versão, o TLS 1.2 torna-se o protocolo padrão para toda a comunicação do portal Azure.
  
   Se vir o seguinte aviso, deve atualizar o software do dispositivo antes de prosseguir:

   Um ou mais dispositivos StorSimple estão a executar uma versão de software mais antiga. A mais recente atualização disponível para OLS 1.2 é uma atualização obrigatória e deve ser instalada imediatamente nestes dispositivos. O TLS 1.2 é utilizado para toda a comunicação do portal Azure e sem esta atualização, o dispositivo não será capaz de comunicar com o serviço StorSimple.

- As correções de bugs de recolha de lixo melhoram o desempenho do ciclo de recolha de lixo quando o dispositivo e a conta de armazenamento estão em duas regiões distantes.
- Correção para falhas de backup devido a intervalos de tempo de bolhas.
- Patches de segurança de quadros atualizados OS/NET:
  - [KB4540725](https://support.microsoft.com/topic/servicing-stack-update-for-windows-8-1-rt-8-1-and-server-2012-r2-march-10-2020-cfa082a3-0b58-a8a3-7dc7-ab424de91b86): março 2020 SSU (atualização de pilha de manutenção)
  - [KB4565541](https://support.microsoft.com/topic/july-14-2020-kb4565541-monthly-rollup-fed6b2b1-3d23-5981-34df-9215a8d8ce01): Rollup de julho de 2020
  - [KB4565622](https://support.microsoft.com/topic/security-and-quality-rollup-for-net-framework-4-6-4-6-1-4-6-2-4-7-4-7-1-4-7-2-for-windows-8-1-rt-8-1-and-windows-server-2012-r2-kb4565622-b7320848-1889-a624-da01-719f55ee8a00): julho 2020 .NET Atualização do Quadro

## <a name="download-update-13"></a>Baixar atualização 1.3

Para descarregar esta atualização, aceda ao servidor [do Catálogo microsoft Update](https://www.catalog.update.microsoft.com/Home.aspx) e descarregue o pacote KB4575898. Este pacote contém os seguintes pacotes:

- **KB4540725**, que contém atualizações cumulativas do Windows para 2012 R2 até março de 2020. Para obter mais informações sobre o que está incluído neste rollup, vá até [março de segurança mensal.](https://support.microsoft.com/help/4540725)
- **KB4565541**, que contém atualizações cumulativas do Windows para 2012 R2 até julho de 2020. Para obter mais informações sobre o que está incluído neste rollup, vá até [fevereiro de segurança mensal.](https://support.microsoft.com/help/4565541)
- **KB4565622**, que contém atualizações do Quadro cumulative.NET até julho de 2020. Para obter mais informações sobre o que está incluído neste rollup, vá até [fevereiro de segurança mensal.](https://support.microsoft.com/help/4565622)
- **KB3011067**, que contém atualizações de software de dispositivos.

Baixe O KB4575898 e siga estas instruções para [aplicar a atualização através da web UI local](./storsimple-virtual-array-install-update-11.md#use-the-local-web-ui).

## <a name="known-issues-in-update-13"></a>Problemas conhecidos na Atualização 1.3
Não foram divulgados novos problemas no Update 1.3. Todos os problemas notados pelo lançamento são transitados de lançamentos anteriores. Para ver o resumo das questões conhecidas incluídas nas versões anteriores, vá a [edições conhecidas na Atualização 1.2](./storsimple-virtual-array-update-12-release-notes.md#known-issues-in-update-12).

## <a name="next-steps"></a>Passos seguintes
Baixe o KB4575898 e [aplique a atualização através da web UI local](./storsimple-virtual-array-install-update-1.md#use-the-local-web-ui).

## <a name="references"></a>Referências
À procura de uma nota de lançamento mais antiga? Vá para:

- [StorSimple Virtual Array Update 1.2 Notas de lançamento](./storsimple-virtual-array-update-12-release-notes.md)
- [StorSimple Virtual Array Update 1.0 Notas de lançamento](./storsimple-virtual-array-update-1-release-notes.md)
- [StorSimple Virtual Array Update 0.6 Notas de lançamento](./storsimple-virtual-array-update-06-release-notes.md)
- [StorSimple Virtual Array Update 0.5 Notas de lançamento](./storsimple-virtual-array-update-05-release-notes.md)
- [StorSimple Virtual Array Update 0.4 Notas de lançamento](./storsimple-virtual-array-update-04-release-notes.md)
- [StorSimple Virtual Array Update 0.3 Notas de lançamento](./storsimple-ova-update-03-release-notes.md)
- [StorSimple Virtual Array Update 0.1 e 0.2 Notas de lançamento](./storsimple-ova-update-01-release-notes.md)
- [StorSimple Notas de lançamento de disponibilidade geral de matriz virtual](./storsimple-virtual-array-update-06-release-notes.md)