---
title: Conceitos de laboratórios DevTest | Documentos da Microsoft
description: Conheça os conceitos básicos do DevTest Labs, e como ele pode facilitar a criar, gerir e monitorizar máquinas virtuais do Azure
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
ms.openlocfilehash: 08cae51da20b6093b284618de92c61aab4bf5b55
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65508390"
---
# <a name="devtest-labs-concepts"></a>Conceitos de Laboratórios DevTest
## <a name="overview"></a>Descrição geral
A lista seguinte contém os principais conceitos de laboratórios Dev/Test e definições:

## <a name="labs"></a>Laboratórios
Um laboratório é a infraestrutura que abrange um grupo de recursos, como máquinas virtuais (VMs), que lhe permite melhor gerir esses recursos, especificando os limites e quotas.

## <a name="virtual-machine"></a>Máquina virtual
Uma VM do Azure é um dos vários tipos de [recursos de computação a pedido e dimensionáveis](/azure/architecture/guide/technology-choices/compute-decision-tree) que o Azure oferece. VMs do Azure dão-lhe a flexibilidade de Virtualização sem ter de comprar e manter o hardware físico que executa, embora ainda precisa de manter a VM a executar determinadas tarefas, como a configuração, aplicação de patches e instalar o software que é executado no ele é.

[Descrição geral de máquinas de virtuais do Windows no Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) fornece informações sobre o que deve considerar antes de criar uma VM, como criá-la e como gerenciá-lo.

## <a name="claimable-vm"></a>VM reclamável
VM Reclamáveis do Azure é uma máquina virtual que está disponível para utilização por nenhum utilizador de laboratório com permissões. Um administrador de laboratório pode preparar as VMs com imagens de bases específicas e artefactos e salvá-los num pool compartilhado. Um utilizador de laboratório, em seguida, poderá solicitar um VM do conjunto de trabalho quando precisam de uma com essa configuração específica.

Uma VM que é reclamáveis inicialmente não está atribuída a qualquer utilizador específico, mas irão aparecer na lista de todos os usuários em "Máquinas de virtuais Reclamáveis". Depois de uma VM foi reclamada por um utilizador, é movido até seus "Minhas máquinas virtuais" área e já não está reclamáveis por qualquer outro utilizador.

## <a name="environment"></a>Ambiente
No DevTest Labs, um ambiente refere-se a uma coleção de recursos do Azure num laboratório. [Esta postagem de blog](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) aborda como criar ambientes multi-VM a partir de modelos do Azure Resource Manager.

## <a name="base-images"></a>Imagens de base
Imagens de base são imagens VM com todas as ferramentas e definições pré-instalada e configurada para criar rapidamente uma VM. Pode aprovisionar uma VM ao escolher uma base existente e adicionar um artefato para instalar o agente de teste. Em seguida, pode salvar a VM aprovisionada como base para que a base pode ser utilizada sem ter de reinstalar o agente de teste para cada aprovisionamento da VM.

## <a name="artifacts"></a>Artefactos
Artefactos são utilizados para implementar e configurar a sua aplicação depois de aprovisionar uma VM. Os artefactos podem ser:

* Ferramentas de que pretende instalar na VM - por exemplo, agentes, Fiddler e o Visual Studio.
* Ações que pretende executar na VM - por exemplo, a clonagem de um repositório.
* Aplicações que pretende testar.

Artefactos são [do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ficheiros JSON que contém instruções para efetuar a implementação e aplicar a configuração.

## <a name="artifact-repositories"></a>Repositórios de artefacto
Repositórios de artefactos são repositórios git, onde os artefactos são verificados. Repositórios de artefacto podem ser adicionados aos vários laboratórios na sua organização, permitindo a reutilização e a partilha.

## <a name="formulas"></a>Fórmulas
Fórmulas, além de imagens de base, fornecem um mecanismo para o aprovisionamento rápido de VM. Uma fórmula no DevTest Labs é uma lista de valores da propriedade padrão usado para criar um laboratório de VM.
Com fórmulas, as VMs com o mesmo conjunto de propriedades - como imagem base, tamanho VM, rede virtual e artefactos - podem ser criadas sem a necessidade de especificar essas propriedades de cada vez. Ao criar uma VM a partir de uma fórmula, os valores predefinidos podem ser usados como-é ou modificado.

## <a name="policies"></a>Políticas
Ajuda para as políticas controla os custos em seu laboratório. Por exemplo, pode criar uma política para encerrar automaticamente VMs com base numa agenda definida.

## <a name="caps"></a>Limites
Limites é um mecanismo para minimizar o desperdício em seu laboratório. Por exemplo, pode definir um limite para restringir o número de VMs que podem ser criadas por utilizador ou num laboratório.

## <a name="security-levels"></a>Níveis de segurança
Acesso de segurança é determinado pelo controlo de acesso de controlo (RBAC). Para compreender como funciona o acesso, ele ajuda a compreender as diferenças entre uma permissão, uma função e um âmbito conforme definido pelo RBAC.

* Permissão - uma permissão é um acesso definidas para uma ação específica (por exemplo, acesso de leitura para todas as máquinas virtuais).
* Função - uma função é um conjunto de permissões que podem ser agrupados e atribuídos a um usuário. Por exemplo, o *proprietário da subscrição* função tem acesso a todos os recursos numa subscrição.
* Âmbito - um âmbito é um nível na hierarquia de um recurso do Azure, como um grupo de recursos, um único laboratório ou a subscrição completa.

Dentro do escopo do DevTest Labs, existem dois tipos de funções para definir as permissões de utilizador: proprietário de laboratório e o utilizador de laboratório.

* Proprietário do laboratório - proprietário de um laboratório tem acesso a quaisquer recursos no laboratório. Por conseguinte, proprietário de um laboratório pode modificar as políticas, ler e escrever todas as VMs, altere a rede virtual e assim por diante.
* Utilizador do laboratório – um utilizador de laboratório, pode ver todos os recursos de laboratório, como VMs, políticas e redes virtuais, mas não é possível modificar as políticas ou todas as VMs criadas por outros utilizadores.

Para ver como criar funções personalizadas no DevTest Labs, consulte o artigo [conceder permissões de utilizador para políticas de laboratório específico](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Uma vez que os âmbitos são hierárquicos, quando um utilizador tem permissões num determinado âmbito, eles recebem automaticamente essas permissões em cada âmbito de nível inferior incluídos. Por exemplo, se um utilizador for atribuído à função de proprietário da subscrição, em seguida, eles têm acesso a todos os recursos numa subscrição, que incluem todas as máquinas virtuais, todas as redes virtuais e todos os laboratórios. Por conseguinte, um proprietário da subscrição herda automaticamente a função de proprietário de laboratório. No entanto, o oposto não é verdade. Proprietário de um laboratório tem acesso a um laboratório, que é um escopo menor que o nível de subscrição. Por conseguinte, o proprietário de um laboratório não será capaz de ver as máquinas virtuais ou redes virtuais ou todos os recursos que estão fora do laboratório.

## <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Todos os conceitos discutidos neste artigo podem ser configurados com os modelos Azure Resource Manager, que lhe permite definir a infraestrutura/configuração da sua solução do Azure e implementar repetidamente num estado consistente.

[Compreender a estrutura e a sintaxe de modelos Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) descreve a estrutura de um modelo Azure Resource Manager e as propriedades que estão disponíveis nas secções de um modelo diferentes.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Passos Seguintes
[Criar um laboratório no DevTest Labs](devtest-lab-create-lab.md)
