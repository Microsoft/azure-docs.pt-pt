---
title: Criar uma fábrica de imagem em Azure DevTest Labs Microsoft Docs
description: Este artigo mostra-lhe como configurar uma fábrica de imagem personalizada utilizando scripts de amostra disponíveis no repositório Git (Azure DevTest Labs).
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 2c5a44a9505d4a312be521cdc3219c5e4ce95a42
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76759453"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Criar uma fábrica de imagem personalizada em Azure DevTest Labs
Este artigo mostra-lhe como configurar uma fábrica de imagem personalizada utilizando scripts de amostra disponíveis no [repositório Git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>O que é uma fábrica de imagem?
Uma fábrica de imagem é uma solução de configuração como código que constrói e distribui imagens automaticamente regularmente com todas as configurações desejadas. As imagens na fábrica de imagem estão sempre atualizadas, e a manutenção em curso é quase nula uma vez que todo o processo é automatizado. E, como todas as configurações necessárias já estão na imagem, poupa tempo de configurar manualmente o sistema depois de ter sido criado um VM com o SISTEMA base.

O acelerador significativo para obter um ambiente de trabalho de desenvolvedor para um estado pronto em DevTest Labs está usando imagens personalizadas. A desvantagem das imagens personalizadas é que há algo extra para manter no laboratório. Por exemplo, versões experimentais de produtos expiram ao longo do tempo (ou) não são aplicadas atualizações de segurança recentemente lançadas, o que nos obriga a refrescar a imagem personalizada periodicamente. Com uma fábrica de imagem, tem uma definição da imagem verificada para o controlo de código de origem e tem um processo automatizado para produzir imagens personalizadas com base na definição.

A solução permite a velocidade de criação de máquinas virtuais a partir de imagens personalizadas, eliminando ao mesmo tempo custos adicionais de manutenção contínua. Com esta solução, pode criar automaticamente imagens personalizadas, distribuí-las a outros Laboratórios DevTest e retirar as imagens antigas. No vídeo seguinte, aprende-se sobre a fábrica de imagens e como é implementada com a DevTest Labs.  Todos os scripts Azure Powershell estão [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory)disponíveis livremente e estão localizados aqui: .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Visão de alto nível da solução
A solução permite a velocidade de criação de máquinas virtuais a partir de imagens personalizadas, eliminando ao mesmo tempo custos adicionais de manutenção contínua. Com esta solução, pode criar automaticamente imagens personalizadas e distribuí-las a outros Laboratórios DevTest. Você usa Azure DevOps (anteriormente Visual Studio Team Services) como motor de orquestração para automatizar todas as operações nos Laboratórios DevTest.

![Visão de alto nível da solução](./media/create-image-factory/high-level-view-of-solution.png)

Há uma [extensão VSTS para DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que lhe permite executar estes passos individuais:

- Criar uma imagem personalizada
- Criar VM
- Eliminar VM
- Criar ambiente
- Eliminar o ambiente
- Ambiente de povoado

Usar a extensão DevTest Labs é uma maneira fácil de começar com a criação automática de imagens personalizadas em DevTest Labs.

Há uma implementação alternativa usando o script PowerShell para um cenário mais complexo. Utilizando o PowerShell, pode automatizar totalmente uma fábrica de imagem baseada em Laboratórios DevTest que podem ser utilizados na sua cadeia de ferramentas de Integração Contínua e Entrega Contínua (CI/CD). Os princípios seguidos nesta solução alternativa são:

- Atualizações comuns não devem exigir alterações na fábrica de imagem. (por exemplo, adicionando um novo tipo de imagem personalizada, aposentando automaticamente imagens antigas, adicionando um novo 'endpoint' DevTest Labs para receber imagens personalizadas, e assim por diante.)
- As alterações comuns são apoiadas pelo controlo do código-fonte (infraestrutura como código)
- A DevTest Labs que recebe imagens personalizadas pode não estar na mesma Subscrição Azure (subscrições de intervalo de laboratórios)
- Os scripts PowerShell devem ser reutilizáveis para que possamos girar fábricas adicionais conforme necessário

## <a name="next-steps"></a>Passos seguintes
Passe para o próximo artigo nesta secção: [Executar uma fábrica de imagem da Azure DevOps](image-factory-set-up-devops-lab.md)
