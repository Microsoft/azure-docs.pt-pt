---
title: Use Azure DevTest Labs para desenvolvedores | Microsoft Docs
description: Conheça as funcionalidades da Azure DevTest Labs que podem ser usadas para satisfazer os requisitos do desenvolvedor e os passos detalhados que pode seguir para configurar um laboratório.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4f195f843e30ad1a56bad7d5b9ca9e73888783b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89296248"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Use Azure DevTest Labs para desenvolvedores
O Azure DevTest Labs pode ser utilizado para implementar vários cenários-chave, mas apenas os cenários principais envolvem a utilização do DevTest Labs para alojar computadores de desenvolvimento para programadores. Neste cenário, o DevTest Labs fornece estas vantagens:

- Os desenvolvedores podem rapidamente abastecer as suas máquinas de desenvolvimento a pedido.
- Os programadores podem personalizar facilmente os seus computadores de desenvolvimento quando for necessário.
- Os administradores podem controlar os custos garantindo que:
  - Os desenvolvedores não podem obter mais VMs do que precisam para o desenvolvimento.
  - Os VMs são desligados quando não estão a ser utilizados. 

![Use Laboratórios DevTest para treinar](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Neste artigo, você aprende sobre várias funcionalidades da Azure DevTest Labs que podem ser usadas para satisfazer os requisitos do desenvolvedor e os passos detalhados que você pode seguir para configurar um laboratório.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementação de ambientes de desenvolvedores com Azure DevTest Labs
1. **Criar o laboratório** 
   
    Os laboratórios são o ponto de partida nos laboratórios Azure DevTest. Uma vez criado um laboratório, pode executar tarefas como adicionar utilizadores (desenvolvedores) ao laboratório, definir políticas para controlar custos, definir imagens VM que podem criar rapidamente, e muito mais.  
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório no Azure DevTest Labs](devtest-lab-create-lab.md) |Aprenda a criar um laboratório em Azure DevTest Labs no portal Azure. |
2. **Criar VMs em minutos usando imagens de marketplace prontas e imagens personalizadas** 
   
    Você pode escolher imagens prontas a partir de uma grande variedade de imagens no Azure Marketplace e disponibilizá-las em laboratório. Se as imagens prontas não satisfaçam os seus requisitos, pode criar uma imagem personalizada criando um VM de laboratório usando uma imagem pronta a partir do Azure Marketplace, instalando todo o software de que necessita e guardando o VM como uma imagem personalizada no laboratório.

    Se utilizar imagens personalizadas, considere usar uma fábrica de imagens para criar e distribuir as suas imagens. Uma fábrica de imagens é uma solução de configuração como código que constrói e distribui regularmente as suas imagens configuradas automaticamente. Isto poupa o tempo necessário para configurar manualmente o sistema depois de ter sido criado um VM com o SISTEMA base.
  
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Configurar imagens do Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Saiba como pode permitir imagens do Azure Marketplace, disponibilizando apenas para seleção as imagens que pretende para os desenvolvedores.|
   | [Criar uma imagem personalizada](devtest-lab-create-template.md) |Crie uma imagem personalizada pré-instalando o software de que necessita para que os desenvolvedores possam criar rapidamente um VM utilizando a imagem personalizada.|
   | [Conheça a fábrica de imagem](./devtest-lab-faq.md#blog-post) |Veja um vídeo que descreve como configurar e usar uma fábrica de imagens.|

3. **Criar modelos reutilizáveis para máquinas de programação** 
   
    Uma fórmula em Azure DevTest Labs é uma lista de valores de propriedade padrão usados para criar um VM. Pode criar uma fórmula em laboratório escolhendo uma imagem, um tamanho VM (uma combinação de CPU e RAM) e uma rede virtual. Cada desenvolvedor pode ver a fórmula no laboratório e usá-la para criar um VM. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Gerir fórmulas da DevTest Labs para criar VMs](devtest-lab-manage-formulas.md) |Saiba como pode criar uma fórmula captando uma imagem, tamanho VM (combinação de CPU e RAM) e uma rede virtual.|

4. **Criar artefactos para permitir a personalização flexível do VM**

   Os artefactos são utilizados para implantar e configurar a sua aplicação após o fornecimento de um VM. Os artefactos podem ser:

   - Ferramentas que pretende instalar no VM - como agentes, Fiddler e Visual Studio.
   - Ações que quer executar no VM - como clonar um repo.
   - Aplicações que quer testar.

   Muitos artefactos já estão disponíveis fora da caixa. Pode criar os seus próprios artefactos personalizados se quiser mais personalização para as suas necessidades específicas.

   Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Crie artefactos personalizados para o seu DevTest Labs VM](devtest-lab-artifact-author.md) |Crie os seus próprios artefactos personalizados para as máquinas virtuais do seu laboratório.|
   | [Adicione um repositório Git para armazenar artefactos personalizados e modelos de Gestor de Recursos Azure para uso em Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Aprenda a armazenar os seus artefactos personalizados no seu próprio repo Git privado.|

5. **Controlar os custos**
   
    A Azure DevTest Labs permite-lhe definir uma política no laboratório para especificar o número máximo de VMs que podem ser criados por um desenvolvedor no laboratório. 
   
    Se a sua equipa de desenvolvedores tiver um horário de trabalho definido e quiser parar todos os VMs numa determinada hora do dia e, em seguida, reiniciá-los automaticamente no dia seguinte, pode facilmente fazê-lo definindo políticas de encerramento automático e de arranque automático no laboratório. 
   
    Finalmente, quando o desenvolvimento da aplicação estiver concluído, pode eliminar todos os VMs de uma só vez, executando um único script PowerShell. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) |Custos de controlo, definindo políticas no laboratório. |
   | [Elimine todos os VMs de laboratório usando um script PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Apague todos os laboratórios numa operação quando o desenvolvimento estiver completo.|

1. **Adicione uma rede virtual a um VM** 
   
    A DevTest Labs cria uma nova rede virtual (VNET) sempre que um laboratório é criado. Se configurar o seu próprio VNET – por exemplo, utilizando o ExpressRoute ou o VPN site-to-site – pode adicionar este VNET às definições de rede virtual do seu laboratório para que esteja disponível na criação de VMs.

    Além disso, existe um domínio de azure Ative Directory disponível que irá juntar um VM a um domínio quando o VM estiver sendo criado. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Configurar uma rede virtual em Azure DevTest Labs](devtest-lab-configure-vnet.md) |Saiba como configurar uma rede virtual em Azure DevTest Labs utilizando o portal Azure.|

6. **Partilhe o laboratório com cada desenvolvedor**
   
    Os laboratórios podem ser acedidos diretamente usando um link que partilha com os seus desenvolvedores. Nem sequer têm de ter uma conta Azure, desde que tenham uma [conta Microsoft.](devtest-lab-faq.md#what-is-a-microsoft-account) Os desenvolvedores não podem ver VMs criados por outros desenvolvedores.  
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Adicione um desenvolvedor a um laboratório em Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Use o portal Azure para adicionar desenvolvedores ao seu laboratório.|
   | [Adicione desenvolvedores ao laboratório usando um script PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Utilize o PowerShell para automatizar a adição de desenvolvedores ao seu laboratório. |
   | [Obter um link para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Saiba como os desenvolvedores podem aceder diretamente a um laboratório através de uma hiperligação.|

7. **Automatizar a criação de laboratório para mais equipas** 
   
    Você pode automatizar a criação de laboratório, incluindo configurações personalizadas, criando um modelo de Gestor de Recursos e usando-o para criar laboratórios idênticos uma e outra vez. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório usando um modelo de gestor de recursos](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Crie laboratórios em Azure DevTest Labs utilizando modelos de Gestor de Recursos. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
