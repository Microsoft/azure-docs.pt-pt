---
title: Criar uma fábrica de imagens na Azure DevTest Labs Microsoft Docs
description: Este artigo mostra-lhe como configurar uma fábrica de imagem personalizada usando scripts de amostra disponíveis no repositório git (Azure DevTest Labs).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 14c8338d6a5ae21847da3a9c774ea6dcdac33ae4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85482076"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Criar uma fábrica de imagem personalizada em Azure DevTest Labs
Este artigo mostra-lhe como configurar uma fábrica de imagem personalizada usando scripts de amostra disponíveis no [repositório git](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory).

## <a name="whats-an-image-factory"></a>O que é uma fábrica de imagens?
Uma fábrica de imagens é uma solução de configuração como código que constrói e distribui automaticamente as imagens numa base regular com todas as configurações desejadas. As imagens na fábrica de imagens estão sempre atualizadas, e a manutenção em curso é quase nula uma vez que todo o processo é automatizado. E, como todas as configurações necessárias já estão na imagem, poupa o tempo de configurar manualmente o sistema depois de ter sido criado um VM com o OS base.

O acelerador significativo para levar um ambiente de trabalho de desenvolvedor a um estado pronto na DevTest Labs está a usar imagens personalizadas. O lado negativo das imagens personalizadas é que há algo extra para manter no laboratório. Por exemplo, as versões de teste dos produtos expiram ao longo do tempo (ou) atualizações de segurança recentemente lançadas não são aplicadas, o que nos força a refrescar periodicamente a imagem personalizada. Com uma fábrica de imagens, você tem uma definição da imagem verificada para o controlo de código fonte e tem um processo automatizado para produzir imagens personalizadas com base na definição.

A solução permite a rapidez de criar máquinas virtuais a partir de imagens personalizadas, eliminando custos adicionais de manutenção em curso. Com esta solução, pode automaticamente criar imagens personalizadas, distribuí-las para outros Laboratórios DevTest e retirar as imagens antigas. No vídeo seguinte, aprende-se sobre a fábrica de imagens e como é implementada com a DevTest Labs.  Todos os scripts Azure Powershell estão disponíveis e estão localizados aqui:  [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory) .

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>Vista de alto nível da solução
A solução permite a rapidez de criar máquinas virtuais a partir de imagens personalizadas, eliminando custos adicionais de manutenção em curso. Com esta solução, pode automaticamente criar imagens personalizadas e distribuí-las para outros Laboratórios DevTest. Você usa Azure DevOps (anteriormente Visual Studio Team Services) como motor de orquestração para automatizar todas as operações nos Laboratórios DevTest.

![Vista de alto nível da solução](./media/create-image-factory/high-level-view-of-solution.png)

Há uma [extensão VSTS para DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que lhe permite executar estes passos individuais:

- Criar uma imagem personalizada
- Criar VM
- Eliminar VM
- Criar ambiente
- Apagar ambiente
- Ambiente de povoar

A utilização da extensão DevTest Labs é uma maneira fácil de começar com a criação automática de imagens personalizadas em DevTest Labs.

Há uma implementação alternativa usando o script PowerShell para um cenário mais complexo. Utilizando o PowerShell, pode automatizar totalmente uma fábrica de imagens baseada em Laboratórios DevTest que podem ser utilizados na sua ferramenta de integração contínua e entrega contínua (CI/CD). Os princípios seguidos nesta solução alternativa são:

- As atualizações comuns não devem exigir alterações na fábrica de imagens. (por exemplo, adicionar um novo tipo de imagem personalizada, retirar automaticamente imagens antigas, adicionar um novo 'ponto final' DevTest Labs para receber imagens personalizadas, e assim por diante.)
- As alterações comuns são apoiadas pelo controlo do código fonte (infraestrutura como código)
- DevTest Labs que recebe imagens personalizadas pode não estar na mesma Subscrição Azure (subscrições de laboratórios abrangem)
- Os scripts PowerShell devem ser reutilizáveis para que possamos girar fábricas adicionais conforme necessário

## <a name="next-steps"></a>Passos seguintes
Passe para o próximo artigo nesta secção: [Gere uma fábrica de imagem a partir de Azure DevOps](image-factory-set-up-devops-lab.md)
