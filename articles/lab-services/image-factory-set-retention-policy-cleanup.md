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
ms.openlocfilehash: 5c1465f31c8b5eb15b6fe63ed61a946e3b32d550
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58440091"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Criar uma fábrica de imagem personalizada no Azure DevTest Labs
Este artigo aborda a definição de uma política de retenção, limpar a fábrica e extinção imagens antigas de todos os outros laboratórios de Dev/Test na organização. 

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que seguiu estes artigos antes de avançar:

- [Criar uma fábrica de imagem](image-factory-create.md)
- [Executar uma fábrica de imagem a partir do Azure DevOps](image-factory-set-up-devops-lab.md)
- [Guardar imagens personalizadas e distribuir a vários laboratórios](image-factory-save-distribute-custom-images.md)

Os itens seguintes já deverá estar no local:

- Um laboratório para a fábrica de imagem no Azure DevTest Labs
- Azure DevTest Labs em que a fábrica de distribuir imagens de ouro de destino de uma ou mais
- Um projeto de DevOps do Azure utilizados para automatizar a fábrica de imagem.
- Localização do código de origem que contém os scripts e a configuração (no nosso exemplo, no mesmo projeto de DevOps utilizado acima)
- Uma definição de compilação para orquestrar as tarefas do Azure Powershell
 
## <a name="setting-the-retention-policy"></a>Definir a política de retenção
Antes de configurar os passos de limpeza, defina o número de imagens históricas que pretende manter nos laboratórios DevTest. Quando tiver seguido os [execute uma fábrica de imagem a partir do Azure DevOps](image-factory-set-up-devops-lab.md) artigo, configurou criar várias variáveis. Um deles estava **ImageRetention**. Definir esta variável como `1`, que significa que o DevTest Labs não irá manter um histórico de imagens personalizadas. Apenas as imagens distribuídas mais recente estará disponíveis. Se alterar esta variável para `2`, a versão mais recente distribuída a imagem, além dos anteriores serão mantidos. Pode definir este valor para definir o número de imagens de históricos que deseja manter no seu DevTest Labs.

## <a name="cleaning-up-the-factory"></a>A limpar a fábrica
O primeiro passo para limpar a fábrica é remover as VMs de imagem dourada da fábrica de imagem. Existe um script para executar esta tarefa exatamente como nossos scripts anteriores. A primeira etapa é adicionar outro **do Azure Powershell** tarefas para a definição de compilação, conforme mostrado na imagem seguinte:

![Passo do PowerShell](./media/set-retention-policy-cleanup/powershell-step.png)

Assim que tiver a nova tarefa na lista, selecione o item e preencha todos os detalhes, conforme mostrado na imagem seguinte:

![Tarefa de PowerShell do antiga imagens de limpeza](./media/set-retention-policy-cleanup/configure-powershell-task.png)

Os parâmetros de script são: `-DevTestLabName $(devTestLabName)`.

## <a name="retire-old-images"></a>Extinguir imagens antigas 
Esta tarefa remove quaisquer imagens antigas, mantendo a apenas um histórico que corresponda a **ImageRetention** criar variável. Adicionar adicional **do Azure Powershell** tarefa à nossa definição de compilação de compilação. Assim que for adicionado, selecione a tarefa e preencha os detalhes, conforme mostrado na imagem seguinte: 

![Extinguir a tarefa de PowerShell do antiga imagens](./media/set-retention-policy-cleanup/retire-old-image-task.png)

Os parâmetros de script são: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`

## <a name="queue-the-build"></a>A compilação de fila
Agora que concluiu a definição de compilação, enfileirar uma nova compilação para se certificar de que tudo está funcionando. Após a compilação for concluída com êxito os novas imagens personalizadas são apresentados no laboratório de destino e se verificar o laboratório de fábrica de imagem, verá sem VMs aprovisionadas. Além disso se colocar em fila mais compilações, verá as tarefas de limpeza abandonar o out antigas imagens personalizadas laboratórios DevTest de acordo com o valor de retenção definido nas variáveis de compilação.

> [!NOTE]
> Se tiver executado o pipeline de compilação no final do último artigo da série, elimine manualmente as máquinas virtuais que foram criadas no laboratório de fábrica de imagem antes de enfileirar uma nova compilação.  O passo de limpeza manual só é necessário enquanto definir tudo e certifique-se de que ele funciona.



## <a name="summary"></a>Resumo
Agora tem uma fábrica de imagem em execução que pode gerar e distribuir imagens personalizadas para seus laboratórios a pedido. Neste ponto, é apenas uma questão de suas imagens a obter corretamente configurada e identificar os laboratórios de destino. Conforme mencionado no artigo anterior, o **Labs.json** arquivo localizado no seu **configuração** pasta especifica quais imagens devem ser efetuadas em cada um dos laboratórios de destino. À medida que adiciona outros laboratórios Dev/Test para a sua organização, basta adicionar uma entrada no Labs.json para o novo laboratório.

Adicionar uma nova imagem à sua fábrica também é simple. Quando quiser incluir uma nova imagem na sua fábrica abre o [portal do Azure](https://portal.azure.com), navegue para a fábrica de DevTest Labs, selecione o botão para adicionar uma VM e selecione a imagem do marketplace pretendido e artefactos. Em vez de selecionar o **criar** botão para fazer a nova VM, selecione **modelo de exibição do Azure Resource Manager**"e guardar o modelo como um ficheiro. JSON em algum lugar dentro do **GoldenImages** pasta no seu repositório. Da próxima vez que executar sua fábrica de imagem, irá criar a sua imagem personalizada.


## <a name="next-steps"></a>Passos Seguintes
1. [Agendar sua compilação/liberação](/devops/pipelines/build/triggers?view=azure-devops&tabs=designer) para executar periodicamente a fábrica de imagem. Ele atualiza as imagens de gerado por fábrica de mensagens em fila em intervalos regulares.
2. Faça mais imagens de ouro da sua fábrica. Também pode considerar [criar artefactos](devtest-lab-artifact-author.md) partes adicionais de suas tarefas de configuração VM do script e incluir os artefactos de suas imagens de fábrica.
4. Criar uma [separar compilação/liberação](/devops/pipelines/overview.md?view=azure-devops-2019) para executar o **DistributeImages** separadamente do script. Pode executar este script quando efetuar alterações ao Labs.json e obter imagens copiadas para laboratórios de destino sem ter de recriar todas as imagens novamente.

