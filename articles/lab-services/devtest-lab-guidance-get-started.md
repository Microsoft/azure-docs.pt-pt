---
title: Cenários populares para usar laboratórios Azure DevTest
description: Este artigo fornece os cenários primários para a utilização de Azure DevTest Labs e dois caminhos gerais para começar a usar o serviço na sua organização.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60773813"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Cenários populares para usar laboratórios Azure DevTest
Dependendo das necessidades de uma empresa, a DevTest Labs pode ser configurada para satisfazer diferentes requisitos.  Este artigo discute os cenários populares. Cada cenário cobre benefícios trazidos através da utilização de DevTest Labs e recursos para usar para implementar esses cenários.  

- Desktops de desenvolvedores
- Ambientes de teste
- Sessões de treino, laboratórios práticos e hackathons
- Investigações de sandboxed
- Laboratórios de sala de aula

## <a name="developer-desktops"></a>Desktops de desenvolvedores
Os desenvolvedores têm frequentemente requisitos diferentes para máquinas de desenvolvimento para diferentes projetos. Com a DevTest Labs, os desenvolvedores podem ter acesso a máquinas virtuais a pedido que estão configuradas para se adequarem aos seus cenários mais comuns. A DevTest Labs proporciona os seguintes benefícios:

- As organizações podem fornecer máquinas comuns de desenvolvimento garantindo a consistência entre as equipas.
- Os desenvolvedores podem fornecer rapidamente as suas máquinas de desenvolvimento a pedido ou [reivindicar uma máquina pré-configurada existente](devtest-lab-add-claimable-vm.md).
- Os desenvolvedores podem fornecer recursos de forma auto-útil sem precisar de permissões de nível de subscrição.
- As TI ou administradores podem [pré-definir a topologia de networking](devtest-lab-configure-vnet.md) e os desenvolvedores podem usá-lo diretamente de forma simples e intuitiva sem necessitar de qualquer acesso especial.
- Os desenvolvedores podem facilmente [personalizar](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) as suas máquinas de desenvolvimento conforme necessário.
- Os administradores podem controlar os custos garantindo que:
    - Os desenvolvedores [não podem obter mais VMs](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) do que precisam para o desenvolvimento
    - [VMs são desligados](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando não estão a ser utilizados
    - Apenas [permitindo um subconjunto de tamanhos de instância VM](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) para os laboratórios específicos
    - Gerir alvos de [custos e notificações](devtest-lab-configure-cost-management.md) para cada laboratório.

Para mais leituras, consulte [Use Azure DevTest Labs para programadores](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Ambientes de teste
Criar e gerir ambientes de teste em toda uma empresa pode exigir um esforço significativo. Com a DevTest Labs, os ambientes de teste podem ser facilmente criados, atualizados ou duplicados. Permite que as equipas acedam a um ambiente totalmente configurado quando é necessário. Neste cenário, a DevTest Labs proporciona os seguintes benefícios:

- As organizações podem fornecer ambientes comuns de teste garantindo a consistência entre as equipas.
- Os testadores podem testar a versão mais recente da sua aplicação, aprovisionando rapidamente ambientes Windows e Linux utilizando modelos reutilizáveis.
- Os administradores podem ligar o laboratório ao Azure DevOps para permitir cenários de DevOps
- Os Proprietários de Laboratórios podem controlar os custos garantindo que:
    - [VMs em ambientes são desligados](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando não estão em uso
    - Apenas [permitindo um subconjunto de tamanhos de instância VM para](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) os laboratórios específicos
    - Gerir alvos de [custos e notificações](devtest-lab-configure-cost-management.md) para cada laboratório.

Para mais informações, consulte [Use Azure DevTest Labs para ambientes de teste VM e PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Investigações de sandboxed
Os desenvolvedores frequentemente investigam diferentes tecnologias ou design de infraestruturas. Por padrão, todos os ambientes criados com DevTest Labs são criados no seu próprio grupo de recursos. O utilizador da DevTest Labs só tem acesso a esses recursos. No entanto, para os desenvolvedores que precisam de mais controlo, uma configuração em todo o laboratório pode ser atualizada para dar direitos aos [colaboradores](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) do utilizador originário do DevTest Labs para todos os ambientes que criam.  Com a DevTest Labs, os desenvolvedores podem ser automaticamente autorizados a ambientes que criam em laboratório.  Este cenário permite que os desenvolvedores adicionem e/ou alterem os recursos do Azure conforme precisam para o seu desenvolvimento ou ambientes de teste. O [custo por](devtest-lab-configure-cost-management.md#view-cost-by-resource) página de recursos permite que os Proprietários de Laboratórios rastreiem o custo de cada ambiente usado para investigações.

Para mais informações, consulte Definir direitos de [acesso a um grupo de recursos ambientais](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Treinos, laboratórios práticos e hackathons 
Um laboratório em Azure DevTest Labs funciona como um ótimo recipiente para atividades transitórias como workshops, laboratórios práticos, treinos ou hackathons.  O serviço permite-lhe criar um laboratório onde possa fornecer modelos personalizados que cada estagiário pode usar para criar ambientes idênticos e isolados para o treino. Neste cenário, a DevTest Labs proporciona os seguintes benefícios:

- [As políticas](devtest-lab-set-lab-policy.md) garantem que os estagiários só recebem o número de recursos, como as máquinas virtuais, de que necessitam.
- As máquinas pré-configuradas e criadas são [reivindicadas](devtest-lab-add-claimable-vm.md) com uma única ação do estagiário.
- Os laboratórios são partilhados com os estagiários acedendo a [URL para o laboratório.](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)
- [As datas](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) de validade nas máquinas virtuais asseguram que as máquinas são eliminadas depois de já não serem necessárias.
- É fácil [apagar um laboratório](devtest-lab-delete-lab-vm.md#delete-a-lab) e todos os recursos [relacionados](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) quando o treino acaba.

Para mais informações, consulte [A Utilização de Laboratórios Azure DevTest para formação](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Prova de conceito vs. implantação em escala
Uma vez que decida explorar devTest Labs, existem dois caminhos gerais a seguir: Prova de Conceito vs Implantação Dimensionada.  

Uma **implantação em escala** consiste em semanas/meses de revisão e planeamento com o intuito de implantar a DevTest Labs para toda a empresa que tem centenas ou milhares de desenvolvedores.

Uma prova de implantação **de conceito** centra-se num esforço concentrado de uma única equipa para estabelecer valor organizacional. Embora possa ser tentador pensar numa implantação à escala, a abordagem tende a falhar mais vezes do que a prova da opção de conceito. Por isso, recomendamos que comece pequeno, aprenda com a primeira equipa, repita a mesma abordagem com duas a três equipas adicionais, e depois planeie uma implantação em escala com base no conhecimento adquirido. Para uma prova de conceito bem sucedida, recomendamos que escolha uma ou duas equipas e identifique os seus cenários (ambiente de v vs ambientes de teste), documente os seus casos de utilização atual e implemente deVTest Labs.

## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos:

- [Conceitos de Laboratórios DevTest](devtest-lab-concepts.md)
- [DevTest Labs FAQ](devtest-lab-faq.md)

