---
title: Conceitos do DevTest Labs | Microsoft Docs
description: Aprenda os conceitos básicos do DevTest Labs e como ele pode facilitar a criação, o gerenciamento e o monitoramento de máquinas virtuais do Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 22fd78ccd58be1790fcd167da396600e8b876564
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428906"
---
# <a name="devtest-labs-concepts"></a>Conceitos de Laboratórios DevTest
## <a name="overview"></a>Visão geral
A lista a seguir contém os principais conceitos e definições do DevTest Labs:

## <a name="labs"></a>Laboratórios
Um laboratório é a infraestrutura que abrange um grupo de recursos, como VMs (máquinas virtuais), que permite que você gerencie melhor esses recursos especificando limites e cotas.

## <a name="virtual-machine"></a>Máquina virtual
Uma VM do Azure é um dos vários tipos de [recursos de computação sob demanda e escalonáveis](/azure/architecture/guide/technology-choices/compute-decision-tree) que o Azure oferece. As VMs do Azure oferecem a flexibilidade da virtualização sem a necessidade de comprar e manter o hardware físico que a executa, embora você ainda precise manter a VM executando determinadas tarefas, como configurar, aplicar patches e instalar o software que é executado em fosse.

[Visão geral das máquinas virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) fornece informações sobre o que você deve considerar antes de criar uma VM, como criá-la e como gerenciá-la.

## <a name="claimable-vm"></a>VM declarável
Uma VM Claimável do Azure é uma máquina virtual que está disponível para uso por qualquer usuário do laboratório com permissões. Um administrador de laboratório pode preparar VMs com imagens e artefatos de base específicos e salvá-las em um pool compartilhado. Um usuário de laboratório pode, então, reivindicar uma VM de trabalho do pool quando precisar de uma com essa configuração específica.

Uma VM que é claimável não é atribuída inicialmente a nenhum usuário específico, mas será exibida na lista de cada usuário em "máquinas virtuais declaráveis". Depois que uma VM é reivindicada por um usuário, ela é movida para a área "minhas máquinas virtuais" e não é mais alegada por nenhum outro usuário.

## <a name="environment"></a>Ambiente
No DevTest Labs, um ambiente refere-se a uma coleção de recursos do Azure em um laboratório. [Esta postagem de blog](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) discute como criar ambientes com várias VMs de seus modelos de Azure Resource Manager.

## <a name="base-images"></a>Imagens de base
As imagens base são imagens de VM com todas as ferramentas e configurações pré-instalados e configuradas para criar rapidamente uma VM. Você pode provisionar uma VM escolhendo uma base existente e adicionando um artefato para instalar o agente de teste. Em seguida, você pode salvar a VM provisionada como base para que a base possa ser usada sem precisar reinstalar o agente de teste para cada provisionamento da VM.

## <a name="artifacts"></a>Artefactos
Os artefatos são usados para implantar e configurar seu aplicativo depois que uma VM é provisionada. Os artefactos podem ser:

* Ferramentas que você deseja instalar na VM, como agentes, Fiddler e Visual Studio.
* Ações que você deseja executar na VM, como clonar um repositório.
* Aplicações que pretende testar.

Os artefatos são [Azure Resource Manager](../azure-resource-manager/management/overview.md) arquivos JSON que contêm instruções para executar a implantação e aplicar a configuração.

## <a name="artifact-repositories"></a>Repositórios de artefatos
Os repositórios de artefato são repositórios git nos quais os artefatos são verificados. Os repositórios de artefato podem ser adicionados a vários laboratórios em sua organização, permitindo a reutilização e o compartilhamento.

## <a name="formulas"></a>Fórmulas
As fórmulas, além das imagens base, fornecem um mecanismo para o provisionamento rápido de VMs. Uma fórmula no DevTest Labs é uma lista de valores de propriedade padrão usados para criar uma VM de laboratório.
Com fórmulas, as VMs com o mesmo conjunto de propriedades, como imagem base, tamanho da VM, rede virtual e artefatos, podem ser criadas sem a necessidade de especificar essas propriedades a cada vez. Ao criar uma VM com base em uma fórmula, os valores padrão podem ser usados como estão ou modificados.

## <a name="policies"></a>Políticas
As políticas ajudam a controlar os custos em seu laboratório. Por exemplo, você pode criar uma política para desligar automaticamente as VMs com base em um agendamento definido.

## <a name="caps"></a>Caps
O Caps é um mecanismo para minimizar o desperdício em seu laboratório. Por exemplo, você pode definir um limite para restringir o número de VMs que podem ser criadas por usuário ou em um laboratório.

## <a name="security-levels"></a>Níveis de segurança
O acesso de segurança é determinado pelo RBAC (controle de acesso baseado em função) do Azure. Para entender como funciona o Access, ele ajuda a entender as diferenças entre uma permissão, uma função e um escopo, conforme definido pelo RBAC.

* Permissão-uma permissão é um acesso definido a uma ação específica (por exemplo, acesso de leitura a todas as máquinas virtuais).
* Função-uma função é um conjunto de permissões que podem ser agrupadas e atribuídas a um usuário. Por exemplo, a função *proprietário da assinatura* tem acesso a todos os recursos em uma assinatura.
* Escopo-um escopo é um nível dentro da hierarquia de um recurso do Azure, como um grupo de recursos, um único laboratório ou a assinatura inteira.

Dentro do escopo do DevTest Labs, há dois tipos de funções para definir permissões de usuário: proprietário do laboratório e usuário do laboratório.

* Proprietário do laboratório-um proprietário do laboratório tem acesso a todos os recursos do laboratório. Portanto, um proprietário de laboratório pode modificar políticas, ler e gravar quaisquer VMs, alterar a rede virtual e assim por diante.
* Usuário de laboratório – um usuário de laboratório pode exibir todos os recursos de laboratório, como VMs, políticas e redes virtuais, mas não pode modificar políticas ou quaisquer VMs criadas por outros usuários.

Para saber como criar funções personalizadas no DevTest Labs, consulte o artigo [conceder permissões de usuário para políticas de laboratório específicas](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Como os escopos são hierárquicos, quando um usuário tem permissões em um determinado escopo, eles recebem automaticamente essas permissões em cada escopo de nível inferior que abrange. Por exemplo, se um usuário for atribuído à função de proprietário da assinatura, ele terá acesso a todos os recursos em uma assinatura, que inclui todas as máquinas virtuais, todas as redes virtuais e todos os laboratórios. Portanto, um proprietário de assinatura herda automaticamente a função de proprietário do laboratório. No entanto, o oposto não é verdadeiro. Um proprietário de laboratório tem acesso a um laboratório, que é um escopo inferior ao nível de assinatura. Portanto, um proprietário de laboratório não será capaz de ver máquinas virtuais ou redes virtuais ou quaisquer recursos que estejam fora do laboratório.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Todos os conceitos discutidos neste artigo podem ser configurados usando modelos de Azure Resource Manager, que permitem definir a infraestrutura/configuração de sua solução do Azure e implantá-la repetidamente em um estado consistente.

[Entenda a estrutura e a sintaxe dos modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) descreve a estrutura de um modelo de Azure Resource Manager e as propriedades que estão disponíveis nas diferentes seções de um modelo.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
[Criar um laboratório no DevTest Labs](devtest-lab-create-lab.md)
