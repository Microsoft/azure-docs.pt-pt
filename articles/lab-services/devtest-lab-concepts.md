---
title: Conceitos de DevTest Labs / Microsoft Docs
description: Aprenda os conceitos básicos de DevTest Labs, e como pode facilitar a criação, gestão e monitorização de máquinas virtuais Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75428906"
---
# <a name="devtest-labs-concepts"></a>Conceitos de Laboratórios DevTest
## <a name="overview"></a>Descrição geral
A lista seguinte contém conceitos e definições chave DevTest Labs:

## <a name="labs"></a>Laboratórios
Um laboratório é a infraestrutura que engloba um conjunto de recursos, como as Máquinas Virtuais (VMs), que permite gerir melhor esses recursos especificando limites e quotas.

## <a name="virtual-machine"></a>Máquina virtual
Um Azure VM é um dos vários tipos de [recursos de computação a pedido e escalável](/azure/architecture/guide/technology-choices/compute-decision-tree) que o Azure oferece. Os VMs Azure dão-lhe a flexibilidade da virtualização sem ter de comprar e manter o hardware físico que o executa, embora ainda precise de manter o VM executando certas tarefas, tais como configurar, corrigir e instalar o software que o executa.

[A visão geral das máquinas virtuais do Windows em Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) dá-lhe informações sobre o que deve considerar antes de criar um VM, como o cria e como o gere.

## <a name="claimable-vm"></a>VM repreensível
Um VM Uager able é uma máquina virtual que está disponível para utilização por qualquer utilizador de laboratório com permissões. Um administrador de laboratório pode preparar VMs com imagens e artefactos de base específicos e guardá-los para uma piscina partilhada. Um utilizador de laboratório pode então reclamar um VM de trabalho da piscina quando precisa de um com essa configuração específica.

Um VM que seja repreensível não é inicialmente atribuído a nenhum utilizador em particular, mas que aparecerá na lista de cada utilizador em "Máquinas virtuais repetíveis". Depois de um VM ser reivindicado por um utilizador, é transferido para a sua área "As minhas máquinas virtuais" e já não é reivindicada por nenhum outro utilizador.

## <a name="environment"></a>Ambiente
Na DevTest Labs, um ambiente refere-se a uma coleção de recursos Azure em laboratório. [Esta publicação de blog](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) discute como criar ambientes multi-VM a partir dos seus modelos de Gestor de Recursos Azure.

## <a name="base-images"></a>Imagens de base
As imagens base são imagens VM com todas as ferramentas e configurações pré-instaladas e configuradas para criar rapidamente um VM. Pode fornecer um VM escolhendo uma base existente e adicionando um artefacto para instalar o seu agente de teste. Em seguida, pode guardar o VM provisionado como base para que a base possa ser utilizada sem ter de reinstalar o agente de ensaio para cada fornecimento do VM.

## <a name="artifacts"></a>Artefactos
Os artefactos são usados para implantar e configurar a sua aplicação depois de um VM ser provisionado. Os artefactos podem ser:

* Ferramentas que pretende instalar no VM - como agentes, Fiddler e Visual Studio.
* Ações que pretende executar no VM - como clonar um repo.
* Aplicações que quer testar.

Os artefactos são ficheiros JSON do Gestor de [Recursos Azure](../azure-resource-manager/management/overview.md) que contêm instruções para executar a implementação e aplicar a configuração.

## <a name="artifact-repositories"></a>Repositórios de artefactos
Os repositórios de artefactos são repositórios git onde os artefactos são verificados. Os repositórios de artefactos podem ser adicionados a vários laboratórios da sua organização permitindo a reutilização e partilha.

## <a name="formulas"></a>Fórmulas
As fórmulas, além das imagens base, fornecem um mecanismo para o fornecimento rápido de VM. Uma fórmula em DevTest Labs é uma lista de valores de propriedade padrão usados para criar um VM de laboratório.
Com fórmulas, VMs com o mesmo conjunto de propriedades - como imagem base, tamanho VM, rede virtual e artefactos - podem ser criados sem necessidade de especificar essas propriedades de cada vez. Ao criar um VM a partir de uma fórmula, os valores predefinidos podem ser usados como -e ou modificados.

## <a name="policies"></a>Políticas
As políticas ajudam a controlar os custos no seu laboratório. Por exemplo, pode criar uma política para desligar automaticamente os VMs com base num calendário definido.

## <a name="caps"></a>Caps
Caps é um mecanismo para minimizar o desperdício no seu laboratório. Por exemplo, pode definir um limite para restringir o número de VMs que podem ser criados por utilizador, ou em laboratório.

## <a name="security-levels"></a>Níveis de segurança
O acesso à segurança é determinado pelo Azure Role-Based Access Control (RBAC). Para entender como funciona o acesso, ajuda a compreender as diferenças entre uma permissão, um papel e um âmbito definido pelo RBAC.

* Permissão - Uma permissão é um acesso definido a uma ação específica (por exemplo, acesso de leitura a todas as máquinas virtuais).
* Função - Um conjunto de permissões que podem ser agruparadas e atribuídas a um utilizador. Por exemplo, a função do proprietário da *subscrição* tem acesso a todos os recursos dentro de uma subscrição.
* Âmbito - Um âmbito é um nível dentro da hierarquia de um recurso Azure, como um grupo de recursos, um único laboratório ou toda a subscrição.

No âmbito da DevTest Labs, existem dois tipos de funções para definir permissões de utilizador: proprietário de laboratório e utilizador de laboratório.

* Dono de laboratório - Um dono de laboratório tem acesso a todos os recursos dentro do laboratório. Portanto, um proprietário de laboratório pode modificar políticas, ler e escrever quaisquer VMs, alterar a rede virtual, e assim por diante.
* Utilizador de Laboratório - Um utilizador de laboratório pode ver todos os recursos de laboratório, tais como VMs, políticas e redes virtuais, mas não pode modificar políticas ou quaisquer VMs criados por outros utilizadores.

Para ver como criar papéis personalizados em DevTest Labs, consulte o artigo, [Grant permissões](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)de utilizador para políticas específicas de laboratório .

Uma vez que os âmbitos são hierárquicos, quando um utilizador tem permissões num determinado âmbito, é-lhes automaticamente concedidas essas permissões em todos os âmbitos de nível inferior englobados. Por exemplo, se um utilizador é atribuído ao papel de proprietário de subscrição, então eles têm acesso a todos os recursos numa subscrição, que incluem todas as máquinas virtuais, todas as redes virtuais e todos os laboratórios. Portanto, um proprietário de subscrição herda automaticamente o papel de proprietário de laboratório. No entanto, o contrário não é verdade. Um dono de laboratório tem acesso a um laboratório, que é um âmbito inferior ao nível de subscrição. Portanto, um dono de laboratório não será capaz de ver máquinas virtuais ou redes virtuais ou quaisquer recursos que estejam fora do laboratório.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Todos os conceitos discutidos neste artigo podem ser configurados utilizando modelos do Gestor de Recursos Do Azure, que permitem definir a infraestrutura/configuração da sua solução Azure e implantá-la repetidamente num estado consistente.

[Compreender a estrutura e a sintaxe dos modelos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) do Gestor de Recursos Azure descreve a estrutura de um modelo de Gestor de Recursos Azure e as propriedades que estão disponíveis nas diferentes secções de um modelo.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
[Criar um laboratório em DevTest Labs](devtest-lab-create-lab.md)
