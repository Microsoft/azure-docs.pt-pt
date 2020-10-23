---
title: Conceitos de DevTest Labs Microsoft Docs
description: Aprenda os conceitos básicos da DevTest Labs, e como pode facilitar a criação, gestão e monitorização de máquinas virtuais Azure
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b184f835ee99d47ddb843e5b02136299c26bfcdc
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92327863"
---
# <a name="devtest-labs-concepts"></a>Conceitos de Laboratórios DevTest
## <a name="overview"></a>Descrição geral
A lista a seguir contém conceitos e definições principais da DevTest Labs:

## <a name="labs"></a>Laboratórios
Um laboratório é a infraestrutura que engloba um grupo de recursos, como máquinas virtuais (VMs), que permite gerir melhor esses recursos especificando limites e quotas.

## <a name="virtual-machine"></a>Máquina virtual
Um Azure VM é um dos vários tipos de [recursos de computação a pedido e escaláveis](/azure/architecture/guide/technology-choices/compute-decision-tree) que a Azure oferece. Os VMs Azure dão-lhe a flexibilidade da virtualização sem ter de comprar e manter o hardware físico que o executa, embora ainda precise de manter o VM executando determinadas tarefas, tais como configurar, remendar e instalar o software que o executa.

[A visão geral das máquinas virtuais do Windows em Azure](../virtual-machines/windows/overview.md) dá-lhe informações sobre o que deve considerar antes de criar um VM, como a cria e como a gere.

## <a name="claimable-vm"></a>VM reclamatável
Um Azure Claimable VM é uma máquina virtual que está disponível para ser usada por qualquer utilizador de laboratório com permissões. Um administrador de laboratório pode preparar VMs com imagens e artefactos específicos e guardá-los para uma piscina partilhada. Um utilizador de laboratório pode então reivindicar um VM funcionando da piscina quando precisar de um com essa configuração específica.

Um VM que seja reclamado não é inicialmente atribuído a nenhum utilizador em particular, mas aparecerá na lista de todos os utilizadores em "Máquinas virtuais Reclamadas". Depois de um VM ser reclamado por um utilizador, é transferido para a sua área de "As minhas máquinas virtuais" e já não é reclamado por qualquer outro utilizador.

## <a name="environment"></a>Ambiente
Em DevTest Labs, um ambiente refere-se a uma coleção de recursos Azure em um laboratório. [Esta publicação de blog](./devtest-lab-faq.md#blog-post) discute como criar ambientes multi-VM a partir dos seus modelos de Gestor de Recursos Azure.

## <a name="base-images"></a>Imagens de base
As imagens base são imagens VM com todas as ferramentas e configurações pré-instaladas e configuradas para criar rapidamente um VM. Pode providenciar um VM escolhendo uma base existente e adicionando um artefacto para instalar o seu agente de teste. Em seguida, pode guardar o VM a provisionado como base para que a base possa ser utilizada sem ter de reinstalar o agente de teste para cada provisão do VM.

## <a name="artifacts"></a>Artefactos
Os artefactos são utilizados para implantar e configurar a sua aplicação após o fornecimento de um VM. Os artefactos podem ser:

* Ferramentas que pretende instalar no VM - como agentes, Fiddler e Visual Studio.
* Ações que quer executar no VM - como clonar um repo.
* Aplicações que quer testar.

Os artefactos são ficheiros JSON [do Gestor de Recursos Azure](../azure-resource-manager/management/overview.md) que contêm instruções para executar a implementação e aplicar a configuração.

## <a name="artifact-repositories"></a>Repositórios de artefactos
Repositórios de artefactos são repositórios onde os artefactos são verificados. Os repositórios de artefactos podem ser adicionados a vários laboratórios da sua organização, permitindo a reutilização e partilha.

## <a name="formulas"></a>Fórmulas
As fórmulas, para além das imagens base, fornecem um mecanismo para o fornecimento rápido de VM. Uma fórmula em DevTest Labs é uma lista de valores de propriedade padrão usados para criar um VM de laboratório.
Com fórmulas, os VMs com o mesmo conjunto de propriedades - como imagem base, tamanho VM, rede virtual e artefactos - podem ser criados sem necessidade de especificar essas propriedades cada vez. Ao criar um VM a partir de uma fórmula, os valores predefinidos podem ser utilizados como -é ou modificado.

## <a name="policies"></a>Políticas
As políticas ajudam a controlar os custos no seu laboratório. Por exemplo, pode criar uma política para desligar automaticamente os VMs com base num calendário definido.

## <a name="caps"></a>Caps
As tampas são um mecanismo para minimizar o desperdício no seu laboratório. Por exemplo, pode definir uma tampa para restringir o número de VMs que podem ser criados por utilizador, ou em laboratório.

## <a name="security-levels"></a>Níveis de segurança
O acesso à segurança é determinado pelo controlo de acesso baseado em funções Azure (Azure RBAC). Para entender como o acesso funciona, ajuda a entender as diferenças entre uma permissão, um papel e um âmbito definido pelo Azure RBAC.

* Permissão - Uma permissão é um acesso definido a uma ação específica (por exemplo, acesso de leitura a todas as máquinas virtuais).
* Função - Um papel é um conjunto de permissões que podem ser agrupadas e atribuídas a um utilizador. Por exemplo, a *função do proprietário* da subscrição tem acesso a todos os recursos dentro de uma subscrição.
* Âmbito - Um âmbito é um nível dentro da hierarquia de um recurso Azure, como um grupo de recursos, um único laboratório, ou toda a subscrição.

No âmbito da DevTest Labs, existem dois tipos de funções para definir permissões de utilizador: dono de laboratório e utilizador de laboratório.

* Dono de laboratório- Um dono de laboratório tem acesso a quaisquer recursos dentro do laboratório. Portanto, um proprietário de laboratório pode modificar políticas, ler e escrever quaisquer VMs, alterar a rede virtual, e assim por diante.
* Utilizador de Laboratório - Um utilizador de laboratório pode visualizar todos os recursos do laboratório, tais como VMs, políticas e redes virtuais, mas não pode modificar políticas ou quaisquer VMs criados por outros utilizadores.

Para ver como criar funções personalizadas na DevTest Labs, consulte o artigo, [conceda permissões ao utilizador para políticas específicas do laboratório.](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)

Uma vez que os âmbitos são hierárquicos, quando um utilizador tem permissões num determinado âmbito, é-lhes automaticamente concedidas essas permissões em todos os âmbitos de nível inferior englobados. Por exemplo, se um utilizador é atribuído ao papel de proprietário de subscrição, então eles têm acesso a todos os recursos de uma subscrição, que incluem todas as máquinas virtuais, todas as redes virtuais, e todos os laboratórios. Portanto, um proprietário de subscrição herda automaticamente o papel de proprietário de laboratório. No entanto, o contrário não é verdade. Um dono de laboratório tem acesso a um laboratório, que é um âmbito inferior ao nível de subscrição. Portanto, um proprietário de laboratório não será capaz de ver máquinas virtuais ou redes virtuais ou quaisquer recursos que estejam fora do laboratório.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Todos os conceitos discutidos neste artigo podem ser configurados utilizando modelos de Gestor de Recursos Azure, que permitem definir a infraestrutura/configuração da sua solução Azure e implementá-la repetidamente num estado consistente.

[Compreenda a estrutura e a sintaxe dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md#template-format) descreve a estrutura de um modelo de Gestor de Recursos Azure e as propriedades que estão disponíveis nas diferentes secções de um modelo.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos seguintes
[Criar um laboratório em Laboratórios DevTest](devtest-lab-create-lab.md)
