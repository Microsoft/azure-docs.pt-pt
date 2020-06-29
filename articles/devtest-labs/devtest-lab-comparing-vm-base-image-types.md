---
title: Comparar imagens e fórmulas personalizadas em DevTest Labs Microsoft Docs
description: Conheça as diferenças entre imagens personalizadas e fórmulas como bases VM para que possa decidir qual melhor se adequa ao seu ambiente.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: dac820d0600745906e4b1aeef95806ed5679488a
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85484031"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Comparar imagens e fórmulas personalizadas em Laboratórios DevTest
Tanto [imagens personalizadas](devtest-lab-create-template.md) como fórmulas podem ser [usadas](devtest-lab-manage-formulas.md) como bases para [novos VMs criados.](devtest-lab-add-vm.md) No entanto, a principal distinção entre imagens e fórmulas personalizadas é que uma imagem personalizada é simplesmente uma imagem baseada num VHD, enquanto uma fórmula é uma imagem baseada num VHD, *além* de configurações pré-configuradas - como tamanho VM, rede virtual, sub-rede e artefactos. Estas definições pré-configuradas são configuradas com valores predefinidos que podem ser ultrapassados no momento da criação de VM. Este artigo explica algumas das vantagens (prós) e desvantagens (contras) de usar imagens personalizadas versus usando fórmulas.

## <a name="custom-image-pros-and-cons"></a>Prós e contras de imagem personalizados
As imagens personalizadas proporcionam uma forma estática e imutável de criar VMs a partir de um ambiente desejado. 

**Prós**

* O fornecimento de VM a partir de uma imagem personalizada é rápido, uma vez que nada muda após o VM ser girado a partir da imagem. Por outras palavras, não existem configurações para aplicar, uma vez que a imagem personalizada é apenas uma imagem sem definições. 
* Os VMs criados a partir de uma única imagem personalizada são idênticos.

**Contras**

* Se precisar de atualizar algum aspeto da imagem personalizada, a imagem deve ser recriada.  

## <a name="formula-pros-and-cons"></a>Fórmula prós e contras
As fórmulas fornecem uma forma dinâmica de criar VMs a partir das configurações/configurações desejadas.

**Prós**

* As mudanças no ambiente podem ser capturadas em voo através de artefactos. Por exemplo, se pretender um VM instalado com os bits mais recentes do seu pipeline de lançamento ou recrutar o código mais recente do seu repositório, pode simplesmente especificar um artefacto que implemente os bits mais recentes ou alimente o código mais recente na fórmula juntamente com uma imagem base-alvo. Sempre que esta fórmula é utilizada para criar VMs, os bits/códigos mais recentes são implantados/alistados no VM. 
* As fórmulas podem definir definições padrão que as imagens personalizadas não podem fornecer - tais como tamanhos VM e configurações de rede virtuais. 
* As definições guardadas numa fórmula são apresentadas como valores predefinidos, mas podem ser modificadas quando o VM é criado. 

**Contras**

* Criar um VM a partir de uma fórmula pode levar mais tempo do que criar um VM a partir de uma imagem personalizada.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Publicações de blogs relacionadas
* [Imagens ou fórmulas personalizadas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Passos seguintes
- [DevTest Labs FAQ](devtest-lab-faq.md)