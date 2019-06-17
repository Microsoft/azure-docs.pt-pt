---
title: Utilizar o Azure DevTest Labs para programadores | Documentos da Microsoft
description: Saiba como utilizar o Azure DevTest Labs para cenários de programação.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 22e070e5-3d1a-49fe-9d4c-5e07cb0b7fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 5a293946e4672e7737f912f42511ad0907ba4a81
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61294004"
---
# <a name="use-azure-devtest-labs-for-developers"></a>Utilizar o Azure DevTest Labs para programadores
O Azure DevTest Labs pode ser utilizado para implementar vários cenários-chave, mas apenas os cenários principais envolvem a utilização do DevTest Labs para alojar computadores de desenvolvimento para programadores. Neste cenário, o DevTest Labs fornece estas vantagens:

- Os desenvolvedores podem aprovisionar suas máquinas de desenvolvimento a pedido.
- Os programadores podem personalizar facilmente os seus computadores de desenvolvimento quando for necessário.
- Os administradores podem controlar os custos, assegurando que:
  - Os desenvolvedores não é possível obter as VMs de mais do que o necessário para o desenvolvimento.
  - As VMs são encerradas quando não está em utilização. 

![Utilizar o DevTest Labs para formação](./media/devtest-lab-developer-lab/devtest-lab-developer-lab.png)

Neste artigo, ficará a conhecer vários recursos do Azure DevTest Labs que podem ser utilizados para satisfazer os requisitos de desenvolvedor e os passos detalhados que pode seguir para configurar um laboratório.

## <a name="implementing-developer-environments-with-azure-devtest-labs"></a>Implementação de ambientes de programador com o Azure DevTest Labs
1. **Criar o laboratório** 
   
    Os laboratórios são o ponto de partida no Azure DevTest Labs. Depois de criar um laboratório, pode realizar tarefas como adicionar usuários (desenvolvedores) para o laboratório, definir políticas para controlar os custos, definindo as imagens VM que podem criar rapidamente e muito mais.  
   
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório no Azure DevTest Labs](devtest-lab-create-lab.md) |Saiba como criar um laboratório no Azure DevTest Labs no portal do Azure. |
2. **Criar VMs em minutos com imagens do marketplace prontas a utilizar e imagens personalizadas** 
   
    Pode escolher prontas a utilizar imagens de uma ampla variedade de imagens no Azure Marketplace e disponibilizá-los no laboratório. Se as imagens prontas a utilizar não atenderem às suas necessidades, pode criar uma imagem personalizada através da criação de um laboratório de VM a utilizar uma imagem pronta a utilizar do Azure Marketplace, a instalação do software que precisa, e a guardar a VM como uma imagem personalizada no laboratório.

    Se utilizar imagens personalizadas, considere a utilização de uma fábrica de imagem para criar e distribuir as imagens. Uma fábrica de imagem é uma solução de configuração como código que regularmente cria e distribui as imagens configuradas automaticamente. Isso economiza o tempo necessário para configurar manualmente o sistema depois de uma VM tiver sido criada com o sistema operacional base.
  
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Configurar imagens do Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Saiba como pode imagens do Azure Marketplace de lista aprovada, tornando disponíveis para seleção apenas as imagens desejadas para os desenvolvedores.|
   | [Criar uma imagem personalizada](devtest-lab-create-template.md) |Crie uma imagem personalizada, pré-instalando o software que necessário para que os desenvolvedores podem criar rapidamente uma VM com a imagem personalizada.|
   | [Saiba mais sobre a fábrica de imagem](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/) |Ver um vídeo que descreve como configurar e utilizar uma fábrica de imagem.|

3. **Criar modelos reutilizáveis para computadores de desenvolvedores** 
   
    Uma fórmula no Azure DevTest Labs é uma lista de valores da propriedade padrão usado para criar uma VM. Pode criar uma fórmula do laboratório ao escolher uma imagem, um tamanho de VM (uma combinação de CPU e RAM) e uma rede virtual. Cada desenvolvedor pode ver a fórmula do laboratório e utilizá-lo para criar uma VM. 
   
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Gerir fórmulas de DevTest Labs para criar VMs](devtest-lab-manage-formulas.md) |Saiba como pode criar uma fórmula, escolha de uma imagem, o tamanho VM (combinação de CPU e RAM) e uma rede virtual.|

4. **Criar artefactos para possibilitar a personalização de VM flexível**

   Artefactos são utilizados para implementar e configurar a sua aplicação depois de aprovisionar uma VM. Os artefactos podem ser:

   - Ferramentas de que pretende instalar na VM - por exemplo, agentes, Fiddler e o Visual Studio.
   - Ações que pretende executar na VM - por exemplo, a clonagem de um repositório.
   - Aplicações que pretende testar.

   Muitos artefatos já estão disponível out-of-the-box. Pode criar seu próprio artefacto personalizado se pretender mais personalização para suas necessidades específicas.

   Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar artefactos personalizados para a sua VM de laboratórios DevTest](devtest-lab-artifact-author.md) |Crie seu próprio artefactos personalizados para as máquinas virtuais no seu laboratório.|
   | [Adicionar um repositório de Git para armazenar artefactos personalizados e modelos do Azure Resource Manager para utilização no Azure DevTest Labs](devtest-lab-add-artifact-repo.md) |Saiba como armazenar os artefactos personalizados no seu próprio repositório de Git privado.|

5. **Controlar os custos**
   
    Os laboratórios DevTest do Azure permite-lhe definir uma política no laboratório para especificar o número máximo de VMs que podem ser criadas por um desenvolvedor no laboratório. 
   
    Se a sua equipa de programadores tem um conjunto de trabalho agenda e pretende parar todas as VMs num determinado momento do dia e, em seguida, automaticamente reiniciá-las do dia seguinte, pode realizar facilmente que ao definir políticas de encerramento automático e início automático no laboratório. 
   
    Por fim, quando o desenvolvimento de aplicações estiver concluído, pode eliminar todas as VMs ao mesmo tempo ao executar um único script do PowerShell. 
   
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) |Controlar os custos através da definição de políticas no laboratório. |
   | [Eliminar todo o laboratório de VMs com um script do PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Elimine todos os laboratórios numa única operação quando o desenvolvimento é concluído.|

1. **Adicionar uma rede virtual a uma VM** 
   
    DevTest Labs cria uma nova rede virtual (VNET) sempre que for criado um laboratório. Se tiver configurado a sua própria VNET – por exemplo, ao utilizar o ExpressRoute ou VPN de site a site – pode adicionar esta VNET para definições de rede virtual do seu laboratório, de modo a que esteja disponível durante a criação de VMs.

    Além disso, há disponível que irá associar uma VM a um domínio quando a VM está a ser criada um artefato de associação do domínio do Azure Active Directory. 
   
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Configurar uma rede virtual no Azure DevTest Labs](devtest-lab-configure-vnet.md) |Saiba como configurar uma rede virtual no Azure DevTest Labs no portal do Azure.|

6. **Partilhar o laboratório com cada desenvolvedor**
   
    Laboratórios podem ser diretamente acessados usando um link que partilha com seus desenvolvedores. Até mesmo não têm de ter uma conta do Azure, desde que tenham um [conta Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Os desenvolvedores não é possível ver as VMs criadas por outros desenvolvedores.  
   
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Adicionar um desenvolvedor a um laboratório no Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Utilize o portal do Azure para adicionar os desenvolvedores para o laboratório.|
   | [Adicionar os desenvolvedores para o laboratório através de um script do PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Utilize o PowerShell para automatizar os desenvolvedores adicionar seu laboratório. |
   | [Obtenha uma ligação para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Saiba como os desenvolvedores podem acessar diretamente um laboratório através de um hiperlink.|

7. **Automatizar a criação de laboratório para mais equipas** 
   
    Pode automatizar a criação de laboratório, incluindo as configurações personalizadas, criando um modelo do Resource Manager e utilizá-lo para criar laboratórios idênticos e novamente. 
   
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório com um modelo do Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Crie laboratórios no Azure DevTest Labs com modelos do Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

