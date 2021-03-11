---
title: Estabeleça política de retenção na Azure DevTest Labs | Microsoft Docs
description: Aprenda a configurar uma política de retenção, limpe a fábrica e retire imagens antigas da DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85384e88f8d456c7bf67302a57618d7a9703a5ee
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550030"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Estabeleça a política de retenção na Azure DevTest Labs
Este artigo abrange a definição de uma política de retenção, a limpeza da fábrica e a retirada de imagens antigas de todos os outros Laboratórios DevTest da organização. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que seguiu estes artigos antes de prosseguir:

- [Criar uma fábrica de imagens](image-factory-create.md)
- [Executar uma fábrica de imagens a partir do Azure DevOps](image-factory-set-up-devops-lab.md)
- [Guardar imagens personalizadas e distribuir para vários laboratórios](image-factory-save-distribute-custom-images.md)

Os seguintes itens já devem estar no lugar:

- Um laboratório para a fábrica de imagens em Azure DevTest Labs
- Um ou mais alvo Azure DevTest Labs onde a fábrica vai distribuir imagens douradas
- Um Projeto Azure DevOps usado para automatizar a fábrica de imagens.
- Localização do código fonte que contém os scripts e configuração (no nosso exemplo, no mesmo Projeto DevOps usado acima)
- Uma definição de construção para orquestrar as tarefas de Azure Powershell
 
## <a name="setting-the-retention-policy"></a>Definição da política de retenção
Antes de configurar os passos de limpeza, defina quantas imagens históricas pretende reter nos Laboratórios DevTest. Quando acompanhou a Run uma fábrica de imagens a partir do artigo [do Azure DevOps,](image-factory-set-up-devops-lab.md) configuraste várias variáveis de construção. Um deles foi **ImageRetention.** Você define esta variável para `1` , o que significa que os DevTest Labs não vão manter um histórico de imagens personalizadas. Apenas as últimas imagens distribuídas estarão disponíveis. Se alterar esta variável para `2` , a mais recente imagem distribuída mais as anteriores será mantida. Pode definir este valor para definir o número de imagens históricas que deseja manter nos seus Laboratórios DevTest.

## <a name="cleaning-up-the-factory"></a>Limpeza da fábrica
O primeiro passo para a limpeza da fábrica é remover os VMs de imagem dourados da fábrica de imagens. Há um guião para fazer esta tarefa tal como os nossos scripts anteriores. O primeiro passo é adicionar outra tarefa **de Azure Powershell** à definição de construção, como mostrado na imagem seguinte:

![Passo PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Assim que tiver a nova tarefa na lista, selecione o item e preencha todos os detalhes como mostrado na imagem seguinte:

![Limpe imagens antigas Tarefa PowerShell](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Os parâmetros do guião são: `-DevTestLabName $(devTestLabName)` .

## <a name="retire-old-images"></a>Retire imagens antigas 
Esta tarefa remove quaisquer imagens antigas, mantendo apenas um histórico correspondente à variável **imageRetention** build. Adicione uma tarefa adicional de construção **de Azure Powershell** à nossa definição de construção. Uma vez adicionada, selecione a tarefa e preencha os detalhes como mostrado na imagem seguinte: 

![Aposentar imagens antigas Tarefa PowerShell](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Os parâmetros do guião são: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>Fila da construção
Agora que concluiu a definição de construção, faça fila para garantir que tudo esteja funcionando. Após a construção completar com sucesso as novas imagens personalizadas aparecem no laboratório de destino e se verificar o laboratório da fábrica de imagens, não vê VMs a provisionados. Além disso, se fizer fila para aumentar ainda mais, vê as tarefas de limpeza retirando imagens personalizadas antigas dos Laboratórios DevTest de acordo com o valor de retenção definido nas variáveis de construção.

> [!NOTE]
> Se executou o gasoduto de construção no final do último artigo da série, elimine manualmente as máquinas virtuais que foram criadas no laboratório de fábrica de imagens antes de fazer uma nova construção.  O passo de limpeza manual só é necessário enquanto configuramos tudo e verificamos se funciona.



## <a name="summary"></a>Resumo
Agora tem uma fábrica de imagem em execução que pode gerar e distribuir imagens personalizadas para os seus laboratórios a pedido. Neste momento, é só uma questão de configurar as suas imagens corretamente e identificar os laboratórios-alvo. Como mencionado no artigo anterior, o **Labs.jsno** ficheiro localizado na sua pasta **de Configuração** especifica quais as imagens que devem ser disponibilizadas em cada um dos laboratórios-alvo. Ao adicionar outros DevTest Labs à sua organização, basta adicionar uma entrada no Labs.jspara o novo laboratório.

Adicionar uma nova imagem à sua fábrica também é simples. Quando quiser incluir uma nova imagem na sua fábrica, abra o [portal Azure,](https://portal.azure.com)navegue na sua fábrica DevTest Labs, selecione o botão para adicionar um VM e escolha a imagem e artefactos de mercado desejados. Em vez de selecionar o botão **Criar** para fazer o novo VM, selecione **ver o modelo do Gestor de Recursos Azure**" e guarde o modelo como um ficheiro .json algures dentro da pasta **GoldenImages** no seu repositório. Da próxima vez que gerir a sua fábrica de imagens, criará a sua imagem personalizada.


## <a name="next-steps"></a>Passos seguintes
1. [Agende a sua construção/lançamento](/azure/devops/pipelines/build/triggers?tabs=designer) para executar periodicamente a fábrica de imagens. Atualiza regularmente as suas imagens geradas pela fábrica.
2. Faça mais imagens douradas para a sua fábrica. Também pode considerar [a criação](devtest-lab-artifact-author.md) de artefactos para scripts adicionais das suas tarefas de configuração VM e incluir os artefactos nas suas imagens de fábrica.
4. Crie uma [construção/versão separada](/azure/devops/pipelines/overview?view=azure-devops-2019) para executar o script **DistributeImages** separadamente. Pode executar este script quando fizer alterações para Labs.jse obter imagens copiadas para laboratórios-alvo sem ter que recriar todas as imagens novamente.

