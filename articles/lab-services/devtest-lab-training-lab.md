---
title: Utilizar o Azure DevTest Labs para formação | Documentos da Microsoft
description: Saiba como utilizar o Azure DevTest Labs para cenários de treinamento.
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 0a7ce1640636c6fba246584d098043a91990b9a0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60622848"
---
# <a name="use-azure-devtest-labs-for-training"></a>Utilizar o Azure DevTest Labs para formação
O Azure DevTest Labs pode ser usado para implementar muitos cenários-chave, além de programador/teste. Um desses cenários é configurar um laboratório para treinamento. Os laboratórios DevTest do Azure permite-lhe criar um laboratório em que pode fornecer modelos personalizados que cada trainee pode utilizar para criar ambientes idênticos e isoladas para treinamento. Pode aplicar políticas para garantir que os ambientes de treinamento estão disponíveis para cada trainee apenas quando precisam e contêm recursos suficientes - como máquinas virtuais - necessários para o treinamento. Por fim, pode partilhar facilmente o laboratório com os formandos, que podem aceder num só clique.

![Utilizar o DevTest Labs para formação](./media/devtest-lab-training-lab/devtest-lab-training.png)

O Azure DevTest Labs cumpre os seguintes requisitos que são necessárias para realizar treinamento em qualquer ambiente virtual: 

* Formandos não é possível ver as VMs criadas por outros formandos
* Todas as máquinas de treinamento devem ser idêntica
* Formandos rapidamente podem aprovisionar ambientes de treinamento
* Controlar o custo, garantindo que os formandos não é possível obter as VMs de mais do que o necessário para o treinamento e também o encerramento VMs quando não estão a utilizá-los
* Partilhe facilmente o laboratório de treinamento com cada trainee
* Reutilizar o laboratório de treinamento e novamente

Neste artigo, ficará a conhecer vários recursos do Azure DevTest Labs que podem ser utilizados para satisfazer os requisitos de treinamento descrito anteriormente e passos detalhados que pode seguir para configurar um laboratório para treinamento.  

## <a name="implementing-training-with-azure-devtest-labs"></a>Implementação de treinamento com o Azure DevTest Labs
1. **Criar o laboratório** 
   
    Os laboratórios são o ponto de partida no Azure DevTest Labs. Depois de criar um laboratório, pode executar tarefas, tais como adicionar utilizadores (formandos) para o laboratório, definir políticas para controlar os custos, definir as imagens VM que podem criar rapidamente e muito mais.   
   
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório no Azure DevTest Labs](devtest-lab-create-lab.md) |Saiba como criar um laboratório no Azure DevTest Labs no portal do Azure. |
2. **Criar VMs de treinamento em minutos com imagens do marketplace prontas a utilizar e imagens personalizadas** 
   
    Pode escolher prontas a utilizar imagens de uma ampla variedade de imagens no Azure Marketplace e disponibilizá-los para os formandos no laboratório. Se as imagens prontas a utilizar não atenderem às suas necessidades, pode criar uma imagem personalizada através da criação de um laboratório de VM com uma imagem pronta a utilizar do Azure Marketplace, a instalação do software que precisa para a formação e guardar a VM como imagem personalizada no laboratório. 
   
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Configurar imagens do Azure Marketplace](devtest-lab-configure-marketplace-images.md) |Saiba como pode imagens do Azure Marketplace de lista de permissões; pretende tornar disponível para seleção apenas as imagens para o treinamento. |
   | [Criar uma imagem personalizada](devtest-lab-create-template.md) |Crie uma imagem personalizada através da pré-instalação do software que necessário para a formação, para que os formandos podem criar rapidamente uma VM com a imagem personalizada. |
3. **Criar modelos reutilizáveis para máquinas de treinamento** 
   
    Uma fórmula no Azure DevTest Labs é uma lista de valores da propriedade padrão usado para criar uma VM. Pode criar uma fórmula do laboratório ao escolher uma imagem, um tamanho de VM (uma combinação de CPU e RAM) e uma rede virtual. Cada trainee pode ver a fórmula do laboratório e utilizá-lo para criar uma VM. 
   
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Gerir fórmulas de DevTest Labs para criar VMs](devtest-lab-manage-formulas.md) |Saiba como pode criar uma fórmula, escolha de uma imagem, o tamanho VM (combinação de CPU e RAM) e uma rede virtual. |
4. **Controlar os custos**
   
    Os laboratórios DevTest do Azure permite-lhe definir uma política no laboratório para especificar o número máximo de VMs que podem ser criadas por um trainee no laboratório. 
   
    Se estiver realizando treinamento de vários dia e pretende parar todas as VMs num determinado momento do dia e, em seguida, automaticamente reiniciá-las do dia seguinte, pode facilmente fazer através da definição de encerramento automático e início automático políticas no laboratório. 
   
    Por fim, quando o treinamento é concluído pode eliminar todas as VMs ao mesmo tempo ao executar um único script do PowerShell. 
   
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Definir políticas de laboratório](devtest-lab-set-lab-policy.md) |Controlar os custos através da definição de políticas no laboratório. |
   | [Eliminar todo o laboratório de VMs com um script do PowerShell](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) |Elimine todos os laboratórios numa única operação quando o treinamento estiver concluído. |
5. **Partilhar o laboratório com cada trainee**
   
    Laboratórios podem ser diretamente acessados usando um link que partilhar com os formandos. Os formandos nem precisa ter uma conta do Azure, desde que tenham um [conta Microsoft](devtest-lab-faq.md#what-is-a-microsoft-account). Formandos não é possível ver as VMs criadas por outros formandos.  
   
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Adicionar um trainee a um laboratório no Azure DevTest Labs](devtest-lab-add-devtest-user.md) |Utilize o portal do Azure para adicionar os formandos para o laboratório de treinamento. |
   | [Adicionar os formandos para o laboratório através de um script do PowerShell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell) |Utilize o PowerShell para automatizar a adição de formandos para o laboratório de treinamento. |
   | [Obtenha uma ligação para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab) |Saiba como um laboratório pode ser diretamente acessado por meio de um hiperlink. |
6. **Reutilizar o laboratório e novamente** 
   
    Pode automatizar a criação de laboratório, incluindo as configurações personalizadas, criando um modelo do Resource Manager e utilizá-lo para criar laboratórios idênticos e novamente. 
   
    Saiba mais ao clicar nos links na tabela a seguir:
   
   | Tarefa | O que irá aprender |
   | --- | --- |
   | [Criar um laboratório com um modelo do Resource Manager](devtest-lab-faq.md#how-do-i-create-a-lab-from-a-resource-manager-template) |Crie laboratórios no Azure DevTest Labs com modelos do Resource Manager. |

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

