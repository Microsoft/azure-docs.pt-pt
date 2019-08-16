---
title: Introdução à máquina virtual de aprendizado profundo – Azure | Microsoft Docs
description: Cenários e componentes de análise de chave para máquinas virtuais de aprendizado profundo.
keywords: aprendizado profundo, ia, ferramentas de ciência de dados, máquina virtual de ciência de dados, ferramentas para ciência de dados, ciência de dados do Linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/16/2018
ms.author: vijetaj
ms.openlocfilehash: 8541713219eb8daf880605089fe49fc585836bdc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534950"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Introdução à máquina virtual de aprendizado profundo

> [!NOTE]
> Todas as ferramentas de VM de aprendizado profundo (DLVM) agora foram dobradas em [VM de ciência de dados (DSVM)](dsvm-tools-overview.md). DLVM e DSVM agora são os mesmos. Considere criar um DSVM em vez de DLVM.

## <a name="why-deep-learning-virtual-machine"></a>Por que a máquina virtual de aprendizado profundo? 

Cada vez mais, algoritmos de aprendizado profundo/redes neurais profundas estão se tornando um dos métodos populares empregados em muitos problemas de aprendizado de máquina. Eles são especialmente bons em tarefas de cognição de máquina, como imagem, texto, compreensão de áudio/vídeo, com frequência, que se aproxima de níveis cognitivas humanos em alguns domínios específicos com arquiteturas de rede neurais avançadas e acesso a grandes conjuntos de dados para treinar modelos. O aprendizado profundo exige uma grande quantidade de poder computacional para treinar modelos com esses grandes conjuntos de altos volumes. Com a nuvem e a disponibilidade de GPUs (unidades de processamento gráfico), é possível criar arquiteturas de neurais profundas sofisticadas e treiná-las em um conjunto de dados grande em uma infraestrutura de computação avançada na nuvem.  O [máquina virtual de ciência de dados](overview.md) forneceu um rico conjunto de ferramentas e exemplos para preparação de dados, aprendizado de máquina e aprendizado profundo. Mas um dos desafios enfrentados pelos usuários é descobrir as ferramentas e os exemplos para cenários específicos, como o aprendizado profundo com facilidade e também para provisionar instâncias de VM baseadas em GPU com mais facilidade. Essa DLVM (máquina virtual de aprendizado profundo) resolve esses desafios. 

## <a name="what-is-deep-learning-virtual-machine"></a>O que é a máquina virtual de aprendizado profundo? 
A máquina virtual de aprendizado profundo é uma variante especialmente configurada do [máquina virtual de ciência de dados](overview.md) (DSVM) para tornar mais simples o uso de instâncias de VM baseadas em GPU para treinar modelos de aprendizado profundo. Há suporte no Windows 2016 e no Ubuntu Máquina Virtual de Ciência de Dados.  Ele compartilha as mesmas imagens de VM principais (e, portanto, todo o rico conjunto de ferramentas) como DSVM, mas está configurado para facilitar o aprendizado profundo. Também fornecemos exemplos de ponta a ponta para compreensão de imagem e texto, que são amplamente aplicáveis a muitos cenários de ia da vida real. A máquina virtual de aprendizado profundo também tenta disponibilizar o rico conjunto de ferramentas e exemplos no DSVM mais facilmente detectável por identificando um catálogo das ferramentas e exemplos na máquina virtual. Em termos de ferramentas, a máquina virtual de aprendizado profundo fornece várias estruturas de aprendizado profundo populares, ferramentas para adquirir e pré-processar dados textuais e imagens. Para obter uma lista abrangente de ferramentas, consulte a [página Visão geral do máquina virtual de ciência de dados](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Próximos Passos

Comece a usar a máquina virtual de aprendizado profundo com as seguintes etapas:

* [Provisionar um Máquina Virtual de Ciência de Dados do Windows](provision-vm.md)
* [Provisionar um Máquina Virtual de Ciência de Dados Ubuntu](dsvm-ubuntu-intro.md)

