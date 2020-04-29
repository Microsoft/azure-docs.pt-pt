---
title: Implementação orquestrada de Azure DevTest Labs
description: Este artigo fornece orientação para orquestrar a implementação de Azure DevTest Labs na sua organização.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: fc6cbbd0aa9cb0750e497c7cc7edbd42f21bda55
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025004"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Orquestrar a implementação da Azure DevTest Labs
Este artigo fornece uma abordagem recomendada para a rápida implementação e implementação de Azure DevTest Labs. A imagem seguinte enfatiza o processo global como orientação prescritiva, observando simultaneamente a flexibilidade para apoiar vários requisitos e cenários do setor.

![Passos para a implementação de Laboratórios Azure DevTest](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Pressupostos
Este artigo assume que tem os seguintes itens em vigor antes de implementar um piloto da DevTest Labs:

- **Assinatura Azure**: A equipa piloto tem acesso à implantação de recursos numa subscrição azure. Se as cargas de trabalho forem apenas desenvolvimento e teste, é aconselhável selecionar a oferta Enterprise DevTest para imagens disponíveis adicionais e taxas mais baixas em máquinas virtuais do Windows.
- **Acesso no local**: Se necessário, o acesso no local já foi configurado. O acesso no local pode ser realizado através de uma ligação VPN site-to-site ou via Rota Expresso. A conectividade via Rota Expresso pode normalmente levar muitas semanas para estabelecer, é recomendado ter a Rota Expresso no lugar antes de iniciar o projeto.
- **Equipas Piloto**: A equipa de projetos de desenvolvimento inicial que utiliza a DevTest Labs foi identificada juntamente com as atividades de desenvolvimento ou teste aplicáveis e estabelece requisitos/objetivos/objetivos para essas equipas.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Marco 1: Estabelecer topologia e design de rede inicial
A primeira área de foco ao implementar uma solução Azure DevTest Labs é estabelecer a conectividade planeada para as máquinas virtuais. As seguintes etapas descrevem os procedimentos necessários:

1. Defina **as gamas de endereços IP iniciais** que são atribuídas à subscrição do DevTest Labs em Azure. Este passo requer a previsão da utilização esperada em número de VMs para que possa fornecer um bloco suficientemente grande para a expansão futura.
2. Identificar **métodos de acesso desejado** nos Laboratórios DevTest (por exemplo, acesso externo/interno). Um ponto chave neste passo é determinar se as máquinas virtuais têm endereços IP públicos (isto é, acessíveis diretamente a partir da internet).
3. Identifique e estabeleça **métodos de conectividade** com o resto do ambiente de nuvem Azure e no local. Se o encaminhamento forçado com a Rota Expresso estiver ativado, é provável que as máquinas virtuais precisem de configurações de procuração apropriadas para atravessar a firewall corporativa.
4. Se os VMs forem **unidos,** determine se se juntam a um domínio baseado em nuvem (Serviços de Diretório AAD, por exemplo) ou a um domínio no local. Para as instalações, determine qual a unidade organizacional (OU) dentro do diretório ativo a que as máquinas virtuais se juntam. Além disso, confirme que os utilizadores têm acesso a aderir (ou estabelecer uma conta de serviço que tenha a capacidade de criar registos de máquinas no domínio)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Marco 2: Implementar o laboratório piloto
Uma vez que a topologia da rede esteja em vigor, o primeiro/laboratório piloto pode ser criado tomando os seguintes passos:

1. Crie um ambiente inicial de Laboratórios DevTest.
2. Determine imagens e tamanhos vm admissíveis para utilização com laboratório. Decida se as imagens personalizadas podem ser enviadas para Azure para utilização com o DevTest Labs.
3. Acesso seguro ao laboratório através da criação de controlos iniciais de acesso à base de base (RBAC) para o laboratório (proprietários de laboratórios e utilizadores de laboratório). Recomendamos que utilize contas de diretório sincronia sincronizadas com o Diretório Ativo Azure para identidade com a DevTest Labs.
4. Configure DevTest Labs para usar políticas como horários, gestão de custos, VMs reiváveis, imagens personalizadas ou fórmulas.
5. Estabeleça um repositório online como O Azure Repos/Git.
6. Decida sobre o uso de repositórios públicos ou privados ou a combinação de ambos. Organize modelos JSON para implementações e sustentação a longo prazo.
7. Se necessário, crie artefactos personalizados. Este passo é opcional. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Marco 3: Documentação, suporte, aprendizagem e melhoria
As equipas piloto iniciais podem necessitar de apoio aprofundado para começar. Use as suas experiências para garantir que a documentação e suporte certos estão em vigor para a continuação do lançamento da Azure DevTest Labs.

1. Introduza as equipas piloto nos seus novos recursos da DevTest Labs (demonstrações, documentação)
2. Com base nas experiências das equipas piloto, planeie e entregue documentação conforme necessário
3. Formalizar o processo de embarque de novas equipas (criação e configuração de laboratórios, fornecimento de acesso, etc.
4. Com base na aceitação inicial, verifique que a previsão original do espaço de endereço IP ainda é razoável e precisa
5. Garantir que foram concluídas as avaliações adequadas de conformidade e segurança

## <a name="next-steps"></a>Passos seguintes
Veja o próximo artigo desta série: [Governação da infraestrutura azure DevTest Labs](devtest-lab-guidance-governance-resources.md)
