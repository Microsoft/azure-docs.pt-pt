---
title: Comparar imagens personalizadas e fórmulas no DevTest Labs | Documentos da Microsoft
description: Saiba mais sobre as diferenças entre as imagens personalizadas e fórmulas, como bases de VM para que possa decidir qual destes melhor se adequa aos seu ambiente.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a3cb259a-7d80-40ec-8ee8-45105704d589
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: ae7556eda817b9eb7be84f9d4a23ea91d3d5440d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60311323"
---
# <a name="comparing-custom-images-and-formulas-in-devtest-labs"></a>Comparar imagens personalizadas e fórmulas no DevTest Labs
Ambos [imagens personalizadas](devtest-lab-create-template.md) e [fórmulas](devtest-lab-manage-formulas.md) podem ser utilizados como as bases para [criadas novas VMs](devtest-lab-add-vm.md). No entanto, a principal diferença entre as imagens personalizadas e fórmulas é que uma imagem personalizada é simplesmente uma imagem com base num VHD, enquanto uma fórmula é uma imagem com base num VHD *além* predefinições - por exemplo, o tamanho da VM, rede virtual, sub-rede e artefactos. Estas definições pré-configuradas são configuradas com valores predefinidos que podem ser substituídas no momento da criação da VM. Este artigo explica alguns dos (profissionais) de vantagens e desvantagens (contras) para utilizar imagens personalizadas e o uso de fórmulas.

## <a name="custom-image-pros-and-cons"></a>Imagem personalizada prós e contras
Imagens personalizadas fornecem uma forma estática e imutável para criar VMs a partir de um ambiente pretendido. 

**Profissionais de TI**

* Aprovisionamento de VM a partir de uma imagem personalizada é rápido, nada muda depois da VM é rotacionada da imagem. Em outras palavras, não há nenhum definições se apliquem à medida que a imagem personalizada é apenas uma imagem sem definições. 
* As VMs criadas a partir de uma única imagem personalizada são idênticas.

**Contras**

* Se precisar de atualizar algum aspeto da imagem personalizada, a imagem tem de ser recriada.  

## <a name="formula-pros-and-cons"></a>Fórmulas prós e contras
Fórmulas proporcionam uma forma de dinâmica para criar VMs a partir da configuração/definições pretendidas.

**Profissionais de TI**

* Alterações no ambiente podem ser capturadas rapidamente através de artefactos. Por exemplo, se quiser que uma VM instalada com os bits mais recentes do seu pipeline de lançamento ou inscrever o código mais recente a partir do seu repositório, pode simplesmente especificar um artefato que implementa os bits mais recentes ou inscreve o código mais recente na fórmula, juntamente com uma base de destino imagem. Sempre que esta fórmula é utilizada para criar VMs, o código/bits mais recentes são implementadas/inscritas à VM. 
* As fórmulas, podem definir as predefinições que não é possível fornecer imagens personalizadas -, tais como definições de rede virtual e tamanhos VM. 
* As configurações salvas numa fórmula, são apresentadas como valores predefinidos, mas podem ser modificadas quando a VM é criada. 

**Contras**

* Criar uma VM a partir de uma fórmula pode demorar mais tempo do que criar uma VM a partir de uma imagem personalizada.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Postagens de blogs relacionados
* [Imagens personalizadas ou fórmulas?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/06/custom-images-or-formulas/)

## <a name="next-steps"></a>Passos Seguintes
- [FAQ de laboratórios DevTest](devtest-lab-faq.md)