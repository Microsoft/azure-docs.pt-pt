---
title: Tamanhos VM Azure FAQ sem disco temporário local
description: Este artigo fornece respostas a perguntas frequentes (FAQ) sobre tamanhos de VM microsoft Azure que não têm disco temporário local.
author: brbell
ms.service: virtual-machines
ms.topic: article
ms.author: brbell
ms.reviewer: mimckitt
ms.date: 06/15/2020
ms.openlocfilehash: 7ce816aba6bee36cd20eed6d31b0642dc8931e0b
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86503035"
---
# <a name="azure-vm-sizes-with-no-local-temporary-disk"></a>Tamanhos Azure VM sem disco temporário local 
Este artigo fornece respostas a perguntas frequentes (FAQ) sobre tamanhos Azure VM que não têm um disco temporário local (ou seja, nenhum balcão temporário local). Para obter mais informações sobre estes tamanhos VM, consulte [especificações para séries Dv4 e Dsv4 (Cargas de trabalho de finalidade geral)](dv4-dsv4-series.md) ou [especificações para sérieS Ev4 e Esv4 (Trabalhos de Trabalho Otimizados de Memória)](ev4-esv4-series.md).

> [!IMPORTANT]
> Os tamanhos VM da série Dv4, Dsv4, Ev4 e Esv4 estão agora em Visualização Pública. Para se inscrever para Pré-visualização Pública, preencha este [Formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRURE1ZSkdDUzg1VzJDN0cwWUlKTkcyUlo5Mi4u). 

## <a name="what-does-no-local-temp-disk-mean"></a>O que significa nenhum disco temporário local? 
Tradicionalmente, temos tido tamanhos VM (por exemplo, Standard_D2s_v3, Standard_E48_v3) que incluem um pequeno disco local (ou seja, um D: Drive). Agora com estes novos tamanhos VM, esse pequeno disco local já não existe; no entanto, ainda pode anexar Standard HDD, Premium SSD ou Ultra SSD.

## <a name="what-if-i-still-want-a-local-temp-disk"></a>E se eu ainda quiser um disco temporário local?
Se a sua carga de trabalho necessitar de um disco temporário local, também temos novos [tamanhos Ddv4 e Ddsv4](ddv4-ddsv4-series.md) ou [Edv4 e Edsv4](edv4-edsv4-series.md) VM disponíveis. Estes tamanhos oferecem um disco temporário 50% maior em comparação com os tamanhos v3 anteriores.

> [!NOTE]
> O disco temporário local não é persistente; para garantir que os seus dados são persistentes, por favor utilize as opções Standard HDD, Premium SSD ou Ultra SSD. 

## <a name="what-are-the-differences-between-these-new-vm-sizes-and-the-general-purpose-dv3dsv3-or-the-memory-optimized-ev3esv3-vm-sizes-that-i-am-used-to"></a>Quais são as diferenças entre estes novos tamanhos de VM e os tamanhos Dv3/Dsv3 de Propósito Geral ou os tamanhos Ev3/Esv3 VM otimizados de memória a que estou habituado? 
| v3 VM famílias com disco temporário local   | Novas famílias v4 com disco temporário local | Novas famílias v4 sem disco temporário local |
|---|---|---|
| Dv3   | Ddv4 | Dv4 |
| Dsv3 | Ddsv4  | Dsv4 |
| Ev3   | Edv4  | Ev4 |
| Esv3 | Edsv4 |    Esv4 | 

## <a name="can-i-resize-a-vm-size-that-has-a-local-temp-disk-to-a-vm-size-with-no-local-temp-disk"></a>Posso redimensionar um tamanho VM que tem um disco temporário local para um tamanho VM sem disco temporário local?  
Não. As únicas combinações permitidas para a redimensionamento são: 

1. VM (com disco temporário local) -> VM (com disco temporário local); e 
2. VM (sem disco temporário local) -> VM (sem disco temporário local). 

> [!NOTE]
> Se uma imagem depender do disco de recursos, ou de existir um ficheiro de página ou de um ficheiro de troca no disco temporário local, as imagens sem disco não funcionarão — em vez disso, utilize a alternativa 'com disco'. 

## <a name="do-these-vm-sizes-support-both-linux-and-windows-operating-systems-os"></a>Estes tamanhos VM suportam sistemas operativos Linux e Windows (OS)?
Yes.

## <a name="will-this-break-my-custom-scripts-custom-images-or-os-images-that-have-scratch-files-or-page-files-on-a-local-temp-disk"></a>Será que isto quebrará os meus scripts personalizados, imagens personalizadas ou imagens de SO que têm ficheiros de riscas ou ficheiros de página num disco temporário local?
Se a imagem personalizada do SO aponta para o disco temporário local, a imagem pode não funcionar corretamente com este tamanho sem disco.

## <a name="have-questions-or-feedback"></a>Tem perguntas ou comentários?
Preencha o [formulário de feedback]( https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_Y3toRKxchLjARedqtguBRUMzdCQkw0OVVRTldFUUtXSTlLQVBPUkVHSy4u). 

## <a name="next-steps"></a>Próximos passos 
Neste documento, aprendeu mais sobre as questões mais frequentes relacionadas com VMs Azure com disco temporário local. Para obter mais informações sobre estes tamanhos VM, consulte os seguintes artigos:

- [Especificações para séries Dv4 e Dsv4 (Carga de Trabalho para Fins Gerais)](dv4-dsv4-series.md)
- [Especificações para série Ev4 e Esv4 (Função de Trabalho Otimizada de Memória)](ev4-esv4-series.md)
