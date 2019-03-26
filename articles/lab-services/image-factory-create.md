---
title: Criar uma fábrica de imagem no Azure DevTest Labs | Documentos da Microsoft
description: Saiba como criar uma fábrica de imagem personalizada no Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: c7923282396481dcfd6aa19b75d110fa03e50931
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58440031"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Criar uma fábrica de imagem personalizada no Azure DevTest Labs
Este artigo mostra-lhe como configurar uma fábrica de imagem personalizada, utilizando scripts de exemplo disponíveis no [repositório de Git](https://github.com/Azure/azure-devtestlab/tree/master/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>O que é uma fábrica de imagem?
Uma fábrica de imagem é uma solução de configuração como código que cria e distribui imagens automaticamente em intervalos regulares com todas as configurações desejadas. As imagens na fábrica de imagem são sempre atualizadas e a manutenção contínua é praticamente zero assim que todo o processo é automatizado. E, porque as configurações necessárias já estão a ser a imagem, guarda a hora de configurar manualmente o sistema depois de uma VM tiver sido criada com o sistema operacional base.

O acelerador significativo para obter uma área de trabalho de programador para um estado estável no DevTest Labs está a utilizar imagens personalizadas. A desvantagem de imagens personalizadas é que há algo extra para manter no laboratório. Por exemplo, versões de avaliação de produtos expirarem ao longo do tempo (ou) não são aplicadas as atualizações de segurança lançadas recentemente, que nos forçaria a atualizar a imagem personalizada periodicamente. Com uma fábrica de imagem, tem uma definição da imagem do Check-in de controle de código-fonte e ter um processo automatizado para produzir imagens personalizadas com base na definição.

A solução permite que a velocidade de criação de máquinas virtuais de imagens personalizadas, tornando os custos de manutenção em curso adicionais. Com esta solução, pode automaticamente criar imagens personalizadas, distribuí-los para outros laboratórios Dev/Test e extinguir as imagens antigas. No vídeo seguinte, ficará a conhecer a fábrica de imagem, e como ele é implementado com os laboratórios DevTest.  Todos os scripts do Powershell do Azure estão livremente disponíveis e localizado aqui: [ http://aka.ms/dtlimagefactory ](http://aka.ms/dtlimagefactory).

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Visão geral da solução
A solução permite que a velocidade de criação de máquinas virtuais de imagens personalizadas, tornando os custos de manutenção em curso adicionais. Com esta solução, pode criar imagens personalizadas e distribuí-los para outros laboratórios Dev/Test automaticamente. Vai utilizar o Azure DevOps (anteriormente conhecido como Visual Studio Team Services) como o motor de orquestração para automatizar as todas as operações nos laboratórios DevTest.

![Visão geral da solução](./media/create-image-factory/high-level-view-of-solution.png)

Há uma [extensão do VSTS para laboratórios Dev/Test](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que permite-lhe executar estes passos individuais: 

- Criar uma imagem personalizada
- Criar VM
- Eliminar VM
- Criar ambiente
- Eliminar ambiente
- Preencher o ambiente

Com a extensão do DevTest Labs é uma forma fácil de começar com a criação de imagens personalizadas automaticamente no DevTest Labs.

Há uma implementação alternativa com o script do PowerShell para um cenário mais complexo. Com o PowerShell, pode automatizar totalmente uma fábrica de imagem com base no DevTest Labs, que podem ser utilizadas na sua integração contínua e a coleção de ferramentas de entrega contínua (CI/CD). São os princípios seguidos nesta solução alternativa:

- Atualizações comuns não devem exigir nenhuma alteração à fábrica de imagem. (por exemplo, adicionar um novo tipo de imagem personalizada, extinguir automaticamente imagens antigas, adicionar um novo "ponto final" DevTest Labs para receber imagens personalizadas e assim por diante).
- Alterações comuns são apoiadas por controle de código de origem (infraestrutura como código)
- DevTest Labs receber imagens personalizadas podem não ser na mesma subscrição do Azure (laboratórios span subscrições)
- Scripts do PowerShell tem de ser reutilizáveis, de modo que pode acelerar fábricas adicionais conforme necessário

## <a name="next-steps"></a>Passos Seguintes
Avance para o próximo artigo nesta secção: [Executar uma fábrica de imagem a partir do Azure DevOps](image-factory-set-up-devops-lab.md)
