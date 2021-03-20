---
title: Use laboratórios Azure DevTest para treinar | Microsoft Docs
description: Este artigo fornece passos detalhados que pode seguir para criar um laboratório para treino em Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b2de9550c62f04286a4f9ad42238bfefb9846477
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89294854"
---
# <a name="use-azure-devtest-labs-for-training"></a>Use Azure DevTest Labs para treinar
A azure DevTest Labs pode ser usado para implementar muitos cenários chave para além de dev/teste. Um desses cenários é criar um laboratório para treinar. A Azure DevTest Labs permite-lhe criar um laboratório onde pode fornecer modelos personalizados que cada estagiário pode usar para criar ambientes idênticos e isolados para treinar. Pode aplicar políticas para garantir que os ambientes de formação só estejam disponíveis para cada estagiário quando estes necessitem e contenham recursos suficientes - como máquinas virtuais - necessários para a formação. Finalmente, pode facilmente partilhar o laboratório com os estagiários, aos quais podem aceder num clique.

![Use Laboratórios DevTest para treinar](./media/devtest-lab-training-lab/devtest-lab-training.png)

A Azure DevTest Labs cumpre os seguintes requisitos que são necessários para realizar treinos em qualquer ambiente virtual: 

* Estagiários não podem ver VMs criados por outros estagiários
* Todas as máquinas de treino devem ser idênticas.
* Os estagiários podem rapidamente providenciar os seus ambientes de formação
* Custo de controlo, garantindo que os estagiários não podem obter mais VMs do que precisam para a formação e também desligar os VMs quando não estão a usá-los
* Partilhe facilmente o laboratório de treino com cada estagiário
* Reutilizar o laboratório de treino uma e outra vez

Neste artigo, você aprende sobre várias funcionalidades da Azure DevTest Labs que podem ser usadas para satisfazer os requisitos de treino previamente descritos e passos detalhados que você pode seguir para configurar um laboratório para treino.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementação de formação com a Azure DevTest Labs
1. **Criar o laboratório** 
   
    Os laboratórios são o ponto de partida nos laboratórios Azure DevTest. Uma vez criado um laboratório, pode executar tarefas como adicionar utilizadores (estagiários) ao laboratório, definir políticas para controlar custos, definir imagens VM que podem criar rapidamente, e muito mais.   
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório no Azure DevTest Labs](devtest-lab-create-lab.md) |Aprenda a criar um laboratório em Azure DevTest Labs no portal Azure. |
2. **Crie VMs de treino em minutos usando imagens de marketplace prontas e imagens personalizadas** 
   
    Você pode escolher imagens prontas a partir de uma grande variedade de imagens no Azure Marketplace e disponibilizá-las para os estagiários no laboratório. Se as imagens prontas não satisfaçam os seus requisitos, pode criar uma imagem personalizada criando um VM de laboratório usando uma imagem pronta a partir do Azure Marketplace, instalando todo o software que precisa para o treino, e guardando o VM como imagem personalizada no laboratório. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Configurar imagens do Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Saiba como pode permitir imagens do Azure Marketplace; disponibilizando apenas para seleção as imagens que deseja para o treino. |
   | [Criar uma imagem personalizada](devtest-lab-create-template.md) |Crie uma imagem personalizada pré-instalando o software de que necessita para o treino para que os formandos possam criar rapidamente um VM utilizando a imagem personalizada. |
3. **Criar modelos reutilizáveis para máquinas de treino** 
   
    Uma fórmula em Azure DevTest Labs é uma lista de valores de propriedade padrão usados para criar um VM. Pode criar uma fórmula em laboratório escolhendo uma imagem, um tamanho VM (uma combinação de CPU e RAM) e uma rede virtual. Cada estagiário pode ver a fórmula no laboratório e usá-la para criar um VM. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Gerir fórmulas da DevTest Labs para criar VMs](devtest-lab-manage-formulas.md) |Saiba como pode criar uma fórmula captando uma imagem, tamanho VM (combinação de CPU e RAM) e uma rede virtual. |
4. **Controlar os custos**
   
    A Azure DevTest Labs permite-lhe definir uma política no laboratório para especificar o número máximo de VMs que podem ser criados por um estagiário no laboratório. 
   
    Se estiver a realizar treinos de vários dias e quiser parar todos os VMs a uma determinada hora do dia e, em seguida, reiniciá-los automaticamente no dia seguinte, pode facilmente conseguir isso definindo políticas de encerramento automático e de arranque automático no laboratório. 
   
    Finalmente, quando o treino estiver concluído, pode eliminar todos os VMs de uma só vez, executando um único script PowerShell. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) |Custos de controlo, definindo políticas no laboratório. |
   | [Elimine todos os VMs de laboratório usando um script PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Apague todos os laboratórios numa operação quando o treino estiver completo. |
5. **Partilhe o laboratório com cada estagiário**
   
    Os laboratórios podem ser acedidos diretamente usando um link que partilha com os seus estagiários. Os seus estagiários nem precisam de ter uma conta Azure, desde que tenham uma [conta Microsoft.](devtest-lab-faq.md#what-is-a-microsoft-account) Os estagiários não podem ver VMs criados por outros estagiários.  
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Adicione um estagiário a um laboratório em Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Use o portal Azure para adicionar estagiários ao seu laboratório de treino. |
   | [Adicione estagiários ao laboratório usando um script PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Use o PowerShell para automatizar a adição de estagiários ao seu laboratório de treino. |
   | [Obter um link para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Saiba como um laboratório pode ser acedido diretamente através de uma hiperligação. |
6. **Reutilizar o laboratório uma e outra vez** 
   
    Você pode automatizar a criação de laboratório, incluindo configurações personalizadas, criando um modelo de Gestor de Recursos e usando-o para criar laboratórios idênticos uma e outra vez. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório usando um modelo de gestor de recursos](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Crie laboratórios em Azure DevTest Labs utilizando modelos de Gestor de Recursos. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
