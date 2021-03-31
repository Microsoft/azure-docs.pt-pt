---
title: Cenários populares para usar Azure DevTest Labs
description: Este artigo fornece os cenários primários para a utilização da Azure DevTest Labs e dois caminhos gerais para começar a usar o serviço na sua organização.
ms.topic: article
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1adb9b009a250942bab7e9b0e3b4b3e52840341c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "85481600"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Cenários populares para usar Azure DevTest Labs
Dependendo das necessidades de uma empresa, a DevTest Labs pode ser configurada para satisfazer diferentes requisitos.  Este artigo discute os cenários populares. Cada cenário cobre os benefícios trazidos pela utilização de Laboratórios DevTest e recursos para usar para implementar esses cenários.  

- Desktops de desenvolvedores
- Ambientes de teste
- Sessões de treino, laboratórios práticos e hackathons
- Investigações de sandboxed
- Laboratórios de sala de aula

## <a name="developer-desktops"></a>Desktops de desenvolvedores
Os desenvolvedores têm frequentemente requisitos diferentes para máquinas de desenvolvimento para diferentes projetos. Com a DevTest Labs, os desenvolvedores podem ter acesso a máquinas virtuais a pedido que são configuradas de acordo com os seus cenários mais comuns. A DevTest Labs oferece os seguintes benefícios:

- As organizações podem fornecer máquinas de desenvolvimento comuns que garantam a consistência entre as equipas.
- Os desenvolvedores podem rapidamente abastecer as suas máquinas de desenvolvimento a pedido ou [reivindicar uma máquina pré-configurada existente.](devtest-lab-add-claimable-vm.md)
- Os desenvolvedores podem providenciar recursos de forma self-service sem precisar de permissões de nível de subscrição.
- Os administradores de TI ou administradores podem [pré-definir a topologia de rede](devtest-lab-configure-vnet.md) e os desenvolvedores podem usá-lo diretamente de uma forma simples e intuitiva sem exigir qualquer acesso especial.
- Os desenvolvedores podem facilmente [personalizar](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) as suas máquinas de desenvolvimento conforme necessário.
- Os administradores podem controlar os custos garantindo que:
    - Os desenvolvedores [não conseguem mais VMs](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) do que precisam para o desenvolvimento
    - [Os VMs são desligados](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando não estão a ser utilizados
    - Apenas [permitindo um subconjunto de tamanhos de instância VM](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) para os laboratórios específicos
    - [Gerir alvos de custos e notificações](devtest-lab-configure-cost-management.md) para cada laboratório.

Para mais leituras, consulte [a Utilização de Laboratórios Azure DevTest para desenvolvedores](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Ambientes de teste
Criar e gerir ambientes de teste em toda uma empresa pode exigir um esforço significativo. Com a DevTest Labs, os ambientes de teste podem ser facilmente criados, atualizados ou duplicados. Permite que as equipas acedam a um ambiente totalmente configurado quando é necessário. Neste cenário, a DevTest Labs oferece os seguintes benefícios:

- As organizações podem fornecer ambientes de testes comuns, garantindo consistência entre as equipas.
- Os testadores podem testar a versão mais recente da sua aplicação fornecendo rapidamente ambientes Windows e Linux utilizando modelos reutilizáveis.
- Os administradores podem ligar o laboratório ao Azure DevOps para permitir cenários de DevOps
- Os proprietários de laboratório podem controlar os custos garantindo que:
    - [Os VMs em ambientes são desligados](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando não estão a ser utilizados
    - Apenas [permitindo um subconjunto de tamanhos de instância VM para](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) os laboratórios específicos
    - [Gerir alvos de custos e notificações](devtest-lab-configure-cost-management.md) para cada laboratório.

Para obter mais informações, consulte [Use Azure DevTest Labs para ambientes de teste VM e PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Investigações de sandboxed
Os desenvolvedores frequentemente investigam diferentes tecnologias ou design de infraestrutura. Por padrão, todos os ambientes criados com a DevTest Labs são criados no seu próprio grupo de recursos. O utilizador da DevTest Labs só tem acesso a esses recursos. No entanto, para os desenvolvedores que precisam de mais controlo, uma configuração em todo o laboratório pode ser atualizada para dar direitos aos [contribuintes](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) do utilizador de DevTest Labs originário para cada ambiente que criam.  Com a DevTest Labs, os desenvolvedores podem receber permissão de contribuinte automaticamente para ambientes que criam no laboratório.  Este cenário permite que os desenvolvedores adicionem e/ou alterem os recursos Azure como precisam para o seu desenvolvimento ou ambientes de teste. O custo por página [de recursos](devtest-lab-configure-cost-management.md#view-cost-by-resource) permite aos proprietários de laboratório acompanhar o custo de cada ambiente utilizado para investigações.

Para obter mais informações, consulte [definir os direitos de acesso a um grupo de recursos ambientais.](https://aka.ms/dtl-sandbox)

## <a name="trainings-hands-on-labs-and-hackathons"></a>Treinos, laboratórios práticos e hackathons 
Um laboratório em Azure DevTest Labs funciona como um ótimo recipiente para atividades transitórias como workshops, laboratórios práticos, treinos ou hackathons.  O serviço permite-lhe criar um laboratório onde pode fornecer modelos personalizados que cada estagiário pode usar para criar ambientes idênticos e isolados para treinar. Neste cenário, a DevTest Labs oferece os seguintes benefícios:

- [As políticas](devtest-lab-set-lab-policy.md) garantem que os estagiários só obtêm o número de recursos, como as máquinas virtuais, de que necessitam.
- As máquinas pré-configuradas e criadas são [reclamadas](devtest-lab-add-claimable-vm.md) com uma única ação do estagiário.
- Os laboratórios são partilhados com estagiários acedendo [a URL para o laboratório.](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)
- [As datas de validade](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) das máquinas virtuais asseguram que as máquinas são eliminadas depois de já não serem necessárias.
- É fácil [apagar um laboratório](devtest-lab-delete-lab-vm.md#delete-a-lab) e todos os recursos [relacionados](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) quando o treino acaba.

Para obter mais informações, consulte [a Utilização Azure DevTest Labs para treinar](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Prova de conceito vs. implantação em escala
Uma vez que você decide explorar DevTest Labs, existem dois caminhos gerais para a frente: Prova de Conceito vs Implantação Escalada.  

Uma **implementação em escala** consiste em semanas/meses de revisão e planeamento com a intenção de implantar a DevTest Labs para toda a empresa que tem centenas ou milhares de desenvolvedores.

Uma prova de implementação **de conceito** centra-se num esforço concentrado de uma única equipa para estabelecer valor organizacional. Embora possa ser tentador pensar numa implementação em escala, a abordagem tende a falhar mais frequentemente do que a prova de opção de conceito. Por isso, recomendamos que comece minúsculo, aprenda com a primeira equipa, repita a mesma abordagem com duas a três equipas adicionais e, em seguida, planeie uma implantação em escala com base no conhecimento adquirido. Para uma prova bem sucedida de conceito, recomendamos que escolha uma ou duas equipas e identifique os seus cenários (dev ambiente vs ambientes de teste), documente os seus casos de uso atual e implemente a DevTest Labs.

## <a name="next-steps"></a>Passos seguintes
Leia os seguintes artigos:

- [Conceitos de Laboratórios DevTest](devtest-lab-concepts.md)
- [DevTest Labs FAQ](devtest-lab-faq.md)

