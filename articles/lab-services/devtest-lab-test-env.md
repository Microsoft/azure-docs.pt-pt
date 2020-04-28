---
title: Utilize laboratórios Azure DevTest para ambientes de teste VM e PaaS [ Microsoft Docs
description: Aprenda a utilizar os Laboratórios Azure DevTest para cenários de ambiente de teste VM e PaaS.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: d4e2c334-643a-40c9-9051-625b8f39fc86
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: c6b458091a8e5e22cca55d401e89e5e13bcf9de9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "60623188"
---
# <a name="use-azure-devtest-labs-for-vm-and-paas-test-environments"></a>Utilize laboratórios Azure DevTest para ambientes de teste VM e PaaS

Pode utilizar o Azure DevTest Labs para implementar vários cenários-chave, mas apenas os cenários principais envolvem a utilização do DevTest Labs para alojar computadores para técnicos de teste. 

Neste cenário, o DevTest Labs fornece estas vantagens:

- Os técnicos de teste podem testar a última versão da sua aplicação ao aprovisionar rapidamente ambientes do Windows e do Linux através de modelos e artefactos reutilizáveis.
- Os técnicos de teste podem dimensionar o teste de carga ao aprovisionar múltiplos agentes de teste.
- Os administradores podem controlar os custos garantindo que:
  - Os testadores não podem ter mais VMs do que precisam.
  - Os VMs são desligados quando não estão a ser utilizados.

![Use Laboratórios DevTest para treino](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Neste artigo, você aprende sobre várias funcionalidades do Azure DevTest Labs usados para satisfazer os requisitos de teste e os passos detalhados a seguir para criar um laboratório.

## <a name="implementing-test-environments-with-azure-devtest-labs"></a>Implementação de ambientes de teste com laboratórios Azure DevTest
1. **Criar o laboratório** 
   
    Os laboratórios são o ponto de partida nos Laboratórios Azure DevTest. Uma vez criado um laboratório, pode executar tarefas como adicionar utilizadores (testadores) ao laboratório, definindo políticas para controlar custos, definindo imagens VM que podem criar rapidamente, e muito mais.  
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório no Azure DevTest Labs](devtest-lab-create-lab.md) |Aprenda a criar um laboratório em Azure DevTest Labs no portal Azure. |
2. **Crie VMs em minutos usando imagens de mercado prontas e imagens personalizadas** 
   
    Você pode escolher imagens prontas a partir de uma grande variedade de imagens no Mercado Azure e disponibilizá-las em laboratório. Se as imagens prontas não cumprirem os seus requisitos, pode criar uma imagem personalizada criando um VM de laboratório utilizando uma imagem pronta do Azure Marketplace, instalando todo o software de que necessita e guardando o VM como uma imagem personalizada no laboratório.

    Se estiver a utilizar imagens personalizadas, considere utilizar uma fábrica de imagens para criar e distribuir as suas imagens. Uma fábrica de imagem é uma solução de configuração como código que constrói e distribui regularmente as suas imagens configuradas automaticamente. Isto poupa o tempo necessário para configurar manualmente o sistema depois de um VM ter sido criado com o Sistema Base.
  
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Configurar imagens do Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Saiba como pode whitelist Imagens do Azure Marketplace, disponibilizando para seleção apenas as imagens que deseja para os testadores.|
   | [Criar uma imagem personalizada](devtest-lab-create-template.md) |Crie uma imagem personalizada pré-instalando o software de que necessita para que os testadores possam criar rapidamente um VM usando a imagem personalizada.|
   | [Saiba mais sobre a fábrica de imagem](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Veja um vídeo que descreve como configurar e usar uma fábrica de imagem.|

3. **Criar modelos reutilizáveis para máquinas de teste** 
   
    Uma fórmula em Azure DevTest Labs é uma lista de valores de propriedade padrão usados para criar um VM. Você pode criar uma fórmula no laboratório escolhendo uma imagem, um tamanho VM (uma combinação de CPU e RAM) e uma rede virtual. Cada teste pode ver a fórmula no laboratório e usá-la para criar um VM. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Gerir fórmulas da DevTest Labs para criar VMs](devtest-lab-manage-formulas.md) |Saiba como pode criar uma fórmula captando uma imagem, tamanho VM (combinação de CPU e RAM) e uma rede virtual.|

3. **Criar ambientes de teste multi-VM** 
   
    Pode utilizar modelos do Gestor de Recursos Azure para definir a infraestrutura e configuração da sua solução Azure e implementar repetidamente vários VMs de teste num estado consistente.

    Os recursos Azure PaaS podem ser provisionados num ambiente a partir de um modelo de Gestor de Recursos e aparecem no rastreio de custos. No entanto, a paragem automática vM não se aplica aos recursos da PaaS.

    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar ambientes multi-VM e recursos PaaS com modelos do Azure Resource Manager](devtest-lab-create-environment-from-arm.md) |Saiba como pode implementar vários VMs num estado consistente para o seu ambiente de teste.|

4. **Criar artefactos para permitir a personalização flexível de VM**

   Os artefactos são usados para implantar e configurar a sua aplicação depois de um VM ser provisionado. Os artefactos podem ser:

   - Ferramentas que pretende instalar no VM - como agentes, Fiddler e Visual Studio.
   - Ações que pretende executar no VM - como clonar um repo.
   - Aplicações que quer testar.

   Muitos artefactos já estão disponíveis fora da caixa. Mas se quiser mais personalização para as suas necessidades específicas, pode criar os seus próprios artefactos personalizados.

   Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Crie artefactos personalizados para o seu VM DevTest Labs](devtest-lab-artifact-author.md) |Crie os seus próprios artefactos personalizados para as máquinas virtuais do seu laboratório.|
   | [Adicione um repositório Git para armazenar artefactos personalizados e modelos de Gestor de Recursos Azure para uso em Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Aprenda a armazenar os seus artefactos personalizados no seu próprio repo git privado.|

5. **Controlar os custos**
   
    A Azure DevTest Labs permite-lhe definir uma política no laboratório para especificar o número máximo de VMs que podem ser criados por um teste no laboratório. 
   
    Se a sua equipa de teste tiver um horário de trabalho definido e quiser parar todos os VMs numa determinada hora do dia e, em seguida, reiniciá-los automaticamente no dia seguinte, pode facilmente fazê-lo definindo políticas de paragem automática e arranque automático no laboratório. 
   
    Finalmente, quando o desenvolvimento da aplicação estiver concluído, pode eliminar todos os VMs ao mesmo tempo executando um único script PowerShell. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) |Controle os custos definindo políticas no laboratório. |
   | [Elimine todos os VMs de laboratório usando um script PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Elimine todos os laboratórios numa operação quando os testes estiverem completos.|

1. **Adicione uma rede virtual a um Laboratório** 
   
    A DevTest Labs cria uma nova rede virtual (VNET) sempre que um laboratório é criado. Se configurar o seu próprio VNET – por exemplo, utilizando o ExpressRoute ou o site-to-site VPN – pode adicionar este VNET às definições de rede virtual do seu laboratório para que esteja disponível na criação de VMs.

    Além disso, existe um domínio azure Ative Directory que une artefacto disponível que une um VM a um domínio quando o VM está sendo criado. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Configure uma rede virtual em Azure DevTest Labs](devtest-lab-configure-vnet.md) |Aprenda a configurar uma rede virtual em Azure DevTest Labs usando o portal Azure.|

6. **Partilhe o laboratório com cada teste**
   
    Os laboratórios podem ser diretamente acedidos usando um link que partilha com os seus testadores. Nem sequer têm de ter uma conta Azure, desde que tenham uma [conta Microsoft.](devtest-lab-faq.md#what-is-a-microsoft-account) Os testadores não conseguem ver VMs criados por outros testadores.  
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Adicione um teste a um laboratório em Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Use o portal Azure para adicionar testadores ao seu laboratório.|
   | [Adicione testadores ao laboratório usando um script PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Utilize o PowerShell para automatizar a adição de testadores ao seu laboratório. |
   | [Obter uma ligação para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Saiba como os testadores podem aceder diretamente a um laboratório através de uma hiperligação.|

7. **Automate criação de laboratório para mais equipas** 
   
    Você pode automatizar a criação de laboratório, incluindo configurações personalizadas, criando um modelo de Gestor de Recursos e usando-o para criar laboratórios idênticos uma e outra vez. 
   
    Saiba mais clicando nos links na tabela seguinte:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Crie um laboratório usando um modelo de Gestor de Recursos](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Crie laboratórios em Laboratórios Azure DevTest usando modelos de Gestor de Recursos. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

