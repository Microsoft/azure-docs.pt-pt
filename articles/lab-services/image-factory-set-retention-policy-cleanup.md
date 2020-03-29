---
title: Criação de política de retenção em Azure DevTest Labs [ Laboratórios DevTest] Microsoft Docs
description: Aprenda a configurar uma política de retenção, limpe a fábrica e retire imagens antigas da DevTest Labs.
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
ms.openlocfilehash: a472c500ee6b968b1459e65e49a352b81e5ea6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759419"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Criação de política de retenção em Azure DevTest Labs
Este artigo abrange a definição de uma política de retenção, a limpeza da fábrica e a retirada de imagens antigas de todos os outros Laboratórios DevTest da organização. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que seguiu estes artigos antes de prosseguir:

- [Criar uma fábrica de imagens](image-factory-create.md)
- [Executar uma fábrica de imagens a partir do Azure DevOps](image-factory-set-up-devops-lab.md)
- [Guardar imagens personalizadas e distribuir para vários laboratórios](image-factory-save-distribute-custom-images.md)

Os seguintes itens já devem estar no lugar:

- Um laboratório para a fábrica de imagem em Azure DevTest Labs
- Um ou mais alvos Azure DevTest Labs onde a fábrica vai distribuir imagens douradas
- Um Projeto Azure DevOps usado para automatizar a fábrica de imagem.
- Localização do código fonte contendo os scripts e configuração (no nosso exemplo, no mesmo Projeto DevOps usado acima)
- Uma definição de construção para orquestrar as tarefas Azure Powershell
 
## <a name="setting-the-retention-policy"></a>Definição da política de retenção
Antes de configurar os passos de limpeza, defina quantas imagens históricas pretende reter nos Laboratórios DevTest. Quando seguiu a Run uma fábrica de imagem a partir do artigo [da Azure DevOps,](image-factory-set-up-devops-lab.md) configuraste várias Variáveis de construção. Um deles foi **ImageRetention.** Você definiu `1`esta variável para , o que significa que os Laboratórios DevTest não manterão um histórico de imagens personalizadas. Apenas as últimas imagens distribuídas estarão disponíveis. Se alterar esta `2`variável para , a mais recente imagem distribuída mais a anterior será mantida. Pode definir este valor para definir o número de imagens históricas que deseja manter nos seus Laboratórios DevTest.

## <a name="cleaning-up-the-factory"></a>Limpeza da fábrica
O primeiro passo na limpeza da fábrica é remover os VMs de imagem dourada da fábrica de imagem. Há um guião para fazer esta tarefa tal como os nossos scripts anteriores. O primeiro passo é adicionar outra tarefa **Azure Powershell** à definição de construção, como mostra a seguinte imagem:

![Passo PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Assim que tiver a nova tarefa na lista, selecione o item e preencha todos os detalhes, conforme mostrado na seguinte imagem:

![Limpar imagens antigas PowerShell tarefa](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Os parâmetros do `-DevTestLabName $(devTestLabName)`guião são: .

## <a name="retire-old-images"></a>Reformar imagens antigas 
Esta tarefa remove quaisquer imagens antigas, mantendo apenas um histórico correspondente à variável de construção **ImageRetention.** Adicione uma tarefa adicional de construção **de Powershell Azure** à nossa definição de construção. Uma vez adicionado, selecione a tarefa e preencha os detalhes como mostrado na seguinte imagem: 

![Retire imagens antigas PowerShell tarefa](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Os parâmetros do guião são:`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Fila da construção
Agora que concluiu a definição de construção, faça uma nova construção para garantir que tudo está funcionando. Depois da construção completar com sucesso as novas imagens personalizadas aparecem no laboratório de destino e se verificar o laboratório de fábrica de imagem, não vê VMs provisionáveis. Além disso, se fizer mais acumulações, vê as tarefas de limpeza retirando imagens personalizadas antigas dos Laboratórios DevTest de acordo com o valor de retenção definido nas variáveis de construção.

> [!NOTE]
> Se executou o oleoduto de construção no final do último artigo da série, elimine manualmente as máquinas virtuais que foram criadas no laboratório de fábrica de imagem antes de fazer fila para uma nova construção.  O passo de limpeza manual só é necessário enquanto preparamos tudo e verificamos se funciona.



## <a name="summary"></a>Resumo
Agora você tem uma fábrica de imagem em execução que pode gerar e distribuir imagens personalizadas para os seus laboratórios a pedido. Neste momento, é só uma questão de configurar as suas imagens corretamente e identificar os laboratórios-alvo. Como mencionado no artigo anterior, o ficheiro **Labs.json** localizado na sua pasta **de Configuração** especifica quais as imagens que devem ser disponibilizadas em cada um dos laboratórios-alvo. Ao adicionar outros Laboratórios DevTest à sua organização, basta adicionar uma entrada no Labs.json para o novo laboratório.

Adicionar uma nova imagem à sua fábrica também é simples. Quando quiser incluir uma nova imagem na sua fábrica, abra o [portal Azure,](https://portal.azure.com)navegue para a sua fábrica DevTest Labs, selecione o botão para adicionar um VM e escolha a imagem e artefactos de mercado desejados. Em vez de selecionar o botão **Criar** para fazer o novo VM, selecione o modelo do Gestor de **Recursos Do View Azure**" e guarde o modelo como um ficheiro .json algures dentro da pasta **GoldenImages** no seu repositório. Da próxima vez que executar a sua fábrica de imagem, criará a sua imagem personalizada.


## <a name="next-steps"></a>Passos seguintes
1. [Agende a sua construção/libertação](/azure/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) para executar periodicamente a fábrica de imagem. Refresca regularmente as suas imagens geradas pela fábrica.
2. Faça mais imagens douradas para a sua fábrica. Também pode considerar [criar artefactos](devtest-lab-artifact-author.md) para scriptr peças adicionais das suas tarefas de configuração VM e incluir os artefactos nas imagens da sua fábrica.
4. Crie uma [construção/lançamento separada](/azure/devops/pipelines/overview?view=azure-devops-2019) para executar o script **DistributeImages** separadamente. Pode executar este guião quando fizer alterações no Labs.json e obter imagens copiadas para laboratórios de alvos sem ter que recriar todas as imagens novamente.

