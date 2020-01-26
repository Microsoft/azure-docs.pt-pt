---
title: Use azure DevTest Labs para treino Microsoft Docs
description: Este artigo fornece passos detalhados que pode seguir para criar um laboratório para treino em Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: 8f0a930d6e3c04548ade71f6d4e3294114eb60a2
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759982"
---
# <a name="use-azure-devtest-labs-for-training"></a>Use azure DevTest Labs para formação
Os Laboratórios Azure DevTest podem ser usados para implementar muitos cenários-chave, além de dev/teste. Um desses cenários é criar um laboratório para treinar. O Azure DevTest Labs permite-lhe criar um laboratório onde possa fornecer modelos personalizados que cada estagiário pode usar para criar ambientes idênticos e isolados para treino. Só é possível aplicar políticas que garantam que os ambientes de formação só estão disponíveis para cada estagiário quando necessitam e contêm recursos suficientes - como máquinas virtuais - necessários para a formação. Finalmente, você pode facilmente partilhar o laboratório com estagiários, que eles podem aceder em um clique.

![Use Laboratórios DevTest para treino](./media/devtest-lab-training-lab/devtest-lab-training.png)

A Azure DevTest Labs satisfaz os seguintes requisitos que são necessários para realizar formação em qualquer ambiente virtual: 

* Estagiários não podem ver VMs criados por outros estagiários
* Todas as máquinas de treino devem ser idênticas.
* Os estagiários podem fornecer rapidamente os seus ambientes de formação
* Custo de controlo garantindo que os estagiários não podem obter mais VMs do que precisam para a formação e também desligar VMs quando não estão a usá-los
* Partilhe facilmente o laboratório de formação com cada estagiário
* Reutilizar o laboratório de treino uma e outra vez

Neste artigo, você aprende sobre várias funcionalidades do Azure DevTest Labs que podem ser usados para atender aos requisitos de treino previamente descritos e passos detalhados que você pode seguir para configurar um laboratório para o treino.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementação de formação com a Azure DevTest Labs
1. **Criar o laboratório** 
   
    Os laboratórios são o ponto de partida nos Laboratórios Azure DevTest. Uma vez criado um laboratório, pode executar tarefas como adicionar utilizadores (estagiários) ao laboratório, definir políticas para controlar custos, definir imagens VM que podem criar rapidamente, e muito mais.   
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório em Azure DevTest Labs](devtest-lab-create-lab.md) |Aprenda a criar um laboratório em Azure DevTest Labs no portal Azure. |
2. **Crie VMs de treino em minutos usando imagens de mercado prontas e imagens personalizadas** 
   
    Você pode escolher imagens prontas a partir de uma grande variedade de imagens no Mercado Azure e disponibilizá-las para os estagiários no laboratório. Se as imagens prontas não cumprirem os seus requisitos, pode criar uma imagem personalizada criando um VM de laboratório utilizando uma imagem pronta do Azure Marketplace, instalando todo o software que precisa para o treino e salvando o VM como imagem personalizada no laboratório. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Configure imagens do Mercado Azure](devtest-lab-configure-marketplace-images.md) |Saiba como pode whitelist Imagens do Azure Marketplace; disponibilizando para seleção apenas as imagens que deseja para o treino. |
   | [Criar uma imagem personalizada](devtest-lab-create-template.md) |Crie uma imagem personalizada pré-instalando o software de que necessita para o treino para que os estagiários possam criar rapidamente um VM usando a imagem personalizada. |
3. **Criar modelos reutilizáveis para máquinas de treino** 
   
    Uma fórmula em Azure DevTest Labs é uma lista de valores de propriedade padrão usados para criar um VM. Você pode criar uma fórmula no laboratório escolhendo uma imagem, um tamanho VM (uma combinação de CPU e RAM) e uma rede virtual. Cada estagiário pode ver a fórmula no laboratório e usá-la para criar um VM. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Gerir fórmulas da DevTest Labs para criar VMs](devtest-lab-manage-formulas.md) |Saiba como pode criar uma fórmula captando uma imagem, tamanho VM (combinação de CPU e RAM) e uma rede virtual. |
4. **Custos de controlo**
   
    A Azure DevTest Labs permite-lhe definir uma política no laboratório para especificar o número máximo de VMs que podem ser criados por um estagiário no laboratório. 
   
    Se estiver a realizar treinos de vários dias e quiser parar todos os VMs numa determinada hora do dia e depois reiniciá-los automaticamente no dia seguinte, pode facilmente fazê-lo definindo políticas de paragem automática e arranque automático no laboratório. 
   
    Finalmente, quando o treino estiver completo, pode eliminar todos os VMs ao mesmo tempo executando um único script PowerShell. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) |Controle os custos definindo políticas no laboratório. |
   | [Elimine todos os VMs de laboratório usando um script PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Apague todos os laboratórios numa operação quando o treino estiver completo. |
5. **Partilhe o laboratório com cada estagiário**
   
    Os laboratórios podem ser acedidos diretamente através de um link que partilha com os seus estagiários. Os seus estagiários nem sequer têm de ter uma conta Azure, desde que tenham uma [conta Microsoft.](devtest-lab-faq.md#what-is-a-microsoft-account) Os estagiários não podem ver VMs criados por outros estagiários.  
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Adicione um estagiário a um laboratório em Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Utilize o portal Azure para adicionar estagiários ao seu laboratório de treino. |
   | [Adicione estagiários ao laboratório usando um script PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Use a PowerShell para automatizar a adição de estagiários ao seu laboratório de treino. |
   | [Obter uma ligação para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Saiba como um laboratório pode ser diretamente acedido através de uma hiperligação. |
6. **Reutilizar o laboratório uma e outra vez.** 
   
    Você pode automatizar a criação de laboratório, incluindo configurações personalizadas, criando um modelo de Gestor de Recursos e usando-o para criar laboratórios idênticos uma e outra vez. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Crie um laboratório usando um modelo de Gestor de Recursos](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Crie laboratórios em Laboratórios Azure DevTest usando modelos de Gestor de Recursos. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

