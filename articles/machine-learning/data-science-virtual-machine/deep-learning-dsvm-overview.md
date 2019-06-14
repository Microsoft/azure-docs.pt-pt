---
title: Introdução ao aprendizagem de Máquina Virtual - Azure | Documentos da Microsoft
description: Cenários de análise de chave e componentes para profunda máquinas de virtuais de aprendizagem.
keywords: as ferramentas de aprendizagem, IA, ferramentas de ciência de dados, máquina de virtual de ciência de dados, para ciência de dados, ciência de dados do linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 879f5939f110fb841ad160bf09f597edcdd86d31
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60502202"
---
# <a name="introduction-to-the-deep-learning-virtual-machine"></a>Introdução ao aprendizagem de Máquina Virtual

## <a name="why-deep-learning-virtual-machine"></a>Por que a Máquina Virtual de aprendizagem profunda? 

Algoritmos de aprendizagem de cada vez mais, profunda / redes neurais profundas estão se tornando um dos métodos populares empregados em muitos problemas de aprendizado de máquina. Eles são especialmente bons em tarefas de cognition de máquina, como imagens, texto, compreender, muitas vezes, a atingir níveis cognitivos humanas em alguns domínios específicos com avançadas arquiteturas de rede neural profunda e acesso a grande conjunto de dados a criar modelos de áudio/vídeo. Aprendizagem profunda requer grande quantidade de capacidade computacional a criar modelos com estes conjuntos de dados grandes. Com a cloud e a disponibilidade de unidades de processamento gráfico (GPUs), ele está se tornando possível crie sofisticadas arquiteturas neurais profundas e treiná-la de um grande conjunto de dados no poderosa infraestrutura de informática na cloud.  O [máquina de Virtual de ciência de dados](overview.md) forneceu um conjunto avançado de ferramentas e exemplos para preparação de dados, machine learning e aprendizagem aprofundada. Mas um dos desafios enfrentados pelos utilizadores é detetar as ferramentas e exemplos para cenários específicos, como a aprendizagem profunda facilmente e também aprovisionar mais facilmente as instâncias VM baseada em GPU. Esta máquina Virtual de aprendizagem profunda (DLVM) resolve esses desafios. 

## <a name="what-is-deep-learning-virtual-machine"></a>O que é o Deep Learning Virtual Machine? 
A máquina de Virtual de aprendizagem profunda é uma variante especialmente configurada dos [máquina de Virtual de ciência de dados](overview.md) (DSVM) para que seja mais simples de usar a VM baseada em GPU instâncias para modelos de aprendizagem profunda de formação. É suportado no Windows 2016 e a máquina de Virtual de ciência de dados do Ubuntu.  Ele compartilha as imagens de VM de núcleo mesmo (e, portanto, todo o conjunto de ferramentas avançado) como a DSVM mas está configurado para facilitar a aprendizagem profunda. Também fornecemos amostras ponto a ponto para a imagem e compreensão de texto, que são amplamente aplicável a vários cenários de IA da vida real. A máquina virtual de aprendizagem profunda também tenta tornar os ao conjunto avançado de ferramentas e exemplos sobre a DSVM mais facilmente Detetáveis colocando um catálogo com as ferramentas e exemplos na máquina virtual. Em termos de ferramentas, a máquina de Virtual de aprendizagem profunda fornece várias estruturas de aprendizagem profunda populares, ferramentas para adquirir e pré-processar imagem, dados textuais. Para obter uma lista abrangente de ferramentas, pode consultar o [página de descrição geral de Máquina Virtual de ciência de dados](overview.md#whats-included-in-the-data-science-vm). 

## <a name="next-steps"></a>Próximos Passos

Começar a trabalhar com o Deep Learning Virtual Machine com os seguintes passos:

* [Aprovisionar uma Máquina Virtual de aprendizagem profunda](provision-deep-learning-dsvm.md)
* [Utilizar a Máquina Virtual de aprendizagem profunda](use-deep-learning-dsvm.md)
* [Referência da ferramenta](dsvm-deep-learning-ai-frameworks.md)
* [Amostras](dsvm-samples-and-walkthroughs.md)
