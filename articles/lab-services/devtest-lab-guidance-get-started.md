---
title: Introdução ao Azure DevTest Labs
description: Este artigo fornece os principais cenários para utilizar o Azure DevTest Labs e dois caminhos gerais para começar a utilizar o serviço na sua organização.
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
ms.openlocfilehash: 219697451174811fab7880ba0d5930ba85edf8fb
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803804"
---
# <a name="get-started-with-using-azure-devtest-labs"></a>Introdução ao Azure DevTest Labs
Consoante as necessidades de uma empresa, o DevTest Labs pode ser configurado para atender aos requisitos diferentes.  Este artigo aborda os cenários populares. Cada cenário aborda os benefícios trazidos por utilizar DevTest Labs e recursos para utilizar para implementar esses cenários.  

- Áreas de trabalho de programador
- Ambientes de teste
- Sessões de formação, laboratórios práticos e programadores dos encontros
- Investigações em área restrita
- Laboratórios de sala de aula

## <a name="developer-desktops"></a>Áreas de trabalho de programador
Os desenvolvedores geralmente têm diferentes requisitos para máquinas de desenvolvimento de projetos diferentes. DevTest Labs, os desenvolvedores podem ter acesso às máquinas virtuais a pedido que estão configurados de acordo com os seus cenários mais comuns. DevTest Labs fornece as seguintes vantagens:

- As organizações podem fornecer máquinas de desenvolvimento comuns garantindo a consistência entre as equipes.
- Os desenvolvedores podem aprovisionar suas máquinas de desenvolvimento a pedido ou [uma máquina previamente configurada existente de afirmação](devtest-lab-add-claimable-vm.md).
- Os desenvolvedores podem Aprovisionar recursos de uma forma de self-service sem precisarem de permissões de nível de assinatura.
- IT ou os administradores podem [predefinidos a topologia de rede](devtest-lab-configure-vnet.md) e os desenvolvedores podem diretamente utilizá-lo de uma forma simples, intuitiva e sem a necessidade de qualquer acesso especial.
- Os desenvolvedores podem facilmente [personalizar](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) seu desenvolvimento máquinas conforme necessário.
- Os administradores podem controlar os custos, assegurando que:
    - Os desenvolvedores [não é possível obter o mais VMs](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) do que o necessário para o desenvolvimento
    - [As VMs são encerradas](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando não está em utilização
    - Apenas [que permite um subconjunto de tamanhos de instâncias VM](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) para os laboratórios específicos
    - [Gerir os destinos de custo e notificações](devtest-lab-configure-cost-management.md) para cada um deles.

Para ler mais, consulte [Use Azure DevTest Labs para os desenvolvedores](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Ambientes de teste
Criar e gerir ambientes de teste de uma empresa podem exigir um esforço significativo. DevTest Labs, ambientes de teste podem ser facilmente criados, atualizados ou duplicados. Ele permite que as equipes aceder a um ambiente totalmente configurado quando for necessário. Neste cenário, o DevTest Labs fornece as seguintes vantagens:

- As organizações podem fornecer ambientes de teste comuns, garantindo a consistência entre as equipes.
- Testadores podem testar a versão mais recente de seu aplicativo ao aprovisionar rapidamente ambientes Windows e Linux utilizando modelos reutilizáveis.
- Os administradores podem ligar-se o laboratório para DevOps do Azure para ativar cenários de DevOps
- Os proprietários de laboratório podem controlar os custos, assegurando que:
    - [As VMs em ambientes são encerradas](devtest-lab-set-lab-policy.md#set-auto-shutdown) quando não está em utilização
    - Apenas [que permite um subconjunto de tamanhos de instância VM para](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) os laboratórios específicos
    - [Gerir os destinos de custo e notificações](devtest-lab-configure-cost-management.md) para cada um deles.

Para obter mais informações, consulte [ambientes de teste de utilização do Azure DevTest Labs para VM e PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Investigações em área restrita
Os desenvolvedores muitas vezes, investigam diferentes tecnologias ou design de infra-estrutura. Por predefinição, todos os ambientes criados com o DevTest Labs são criados no seu próprio grupo de recursos. O utilizador de DevTest Labs obtém apenas o acesso de leitura a esses recursos. No entanto, para desenvolvedores que precisam de mais controle, uma definição de todo o laboratório pode ser atualizada para dar [direitos de contribuição](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) para o utilizador de DevTest Labs origem para cada ambiente que criaram.  DevTest Labs, os desenvolvedores podem receber permissão de contribuinte automaticamente para ambientes que criaram o laboratório.  Este cenário permite aos desenvolvedores adicionar e/ou alterar recursos do Azure à medida que necessitam para seus ambientes de desenvolvimento ou teste. O [de custos por recurso](devtest-lab-configure-cost-management.md#view-cost-by-resource) página permite que os proprietários de laboratório controlar o custo de cada ambiente utilizado para as investigações.

Para obter mais informações, consulte [definir os direitos de acesso a um grupo de recursos de ambiente](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Treinamentos, laboratórios práticos e programadores dos encontros 
Um laboratório no Azure DevTest Labs funciona como um contêiner excelente para atividades transitórias como workshops, laboratórios práticos, treinamentos ou programadores dos encontros.  O serviço permite-lhe criar um laboratório em que pode fornecer modelos personalizados que cada trainee pode utilizar para criar ambientes idênticos e isoladas para treinamento. Neste cenário, o DevTest Labs fornece as seguintes vantagens:

- [Políticas](devtest-lab-set-lab-policy.md) Certifique-se de formandos apenas obtém o número de recursos, tais como máquinas de virtuais, o que eles precisam.
- São máquinas previamente configuradas e criadas [solicitadas](devtest-lab-add-claimable-vm.md) com uma ação única de trainee.
- Laboratórios são partilhados com os formandos acessando [URL para o laboratório](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Datas de expiração](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) em máquinas virtuais Certifique-se de que as máquinas são eliminadas depois de já não são necessários.
- É fácil [eliminar um laboratório](devtest-lab-delete-lab-vm.md#delete-a-lab) e todos os [recursos relacionados](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) quando o treinamento está acima.

Para obter mais informações, consulte [Use Azure DevTest Labs para treinamento](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Prova de conceito vs. implementação dimensionado
Assim que decidir explorar o DevTest Labs, existem dois caminhos gerais para a frente: Prova de conceito vs implementação aumentados.  

R **dimensionado implementação** consiste em semanas/meses de revisão e planejamento com um objetivo de implementação de DevTest Labs para toda a empresa com centenas ou milhares de desenvolvedores.

R **prova de conceito do** implantação se concentra num esforço concentrarem de uma única equipa para estabelecer o valor organizacional. Embora possa ser tentador pensar numa implantação dimensionada, a abordagem tende a falhar com mais frequência do que a comprovação da opção de conceito. Por conseguinte, recomendamos que comece por algo pequeno, aprenda com a equipe primeiro, repetir a mesma abordagem com duas ou três equipes de adicionais e, em seguida, planejar uma implantação dimensionada com base nos dados recolhidos. Para uma bem-sucedida prova de conceito, recomendamos que escolha uma ou duas equipes e identificar os seus cenários (vs de ambiente de desenvolvimento os ambientes de teste), seus casos de utilização atual de documentos e implementar o DevTest Labs.

## <a name="next-steps"></a>Passos Seguintes
Leia os artigos seguintes:

- [Conceitos de Laboratórios DevTest](devtest-lab-concepts.md)
- [FAQ de laboratórios DevTest](devtest-lab-faq.md)

