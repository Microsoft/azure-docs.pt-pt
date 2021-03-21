---
title: Implementação orquestrada de Azure DevTest Labs
description: Este artigo fornece orientações para orquestrar a implementação da Azure DevTest Labs na sua organização.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1958e818f014b7419a1a33e9453fbad460dfc159
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92330619"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Orquestrar a implementação do Azure DevTest Labs
Este artigo fornece uma abordagem recomendada para a rápida implantação e implementação da Azure DevTest Labs. A imagem a seguir enfatiza o processo global como orientação prescritiva, observando a flexibilidade para apoiar vários requisitos e cenários da indústria.

![Passos para implementar Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Pressupostos
Este artigo pressupõe que tem os seguintes itens no lugar antes de implementar um piloto da DevTest Labs:

- **Assinatura Azure**: A equipa piloto tem acesso à implantação de recursos numa subscrição do Azure. Se as cargas de trabalho forem apenas de desenvolvimento e testes, é aconselhável selecionar a oferta do Enterprise DevTest para imagens disponíveis adicionais e taxas mais baixas em máquinas virtuais do Windows.
- **Acesso às instalações**: Se necessário, o acesso ao local já foi configurado. O acesso no local pode ser realizado através de uma ligação VPN site-to-site ou via Via Express. A conectividade via Rota Expresso pode normalmente levar muitas semanas para estabelecer, é recomendado ter a Rota Expresso no lugar antes de iniciar o projeto.
- **Equipas Piloto**: A equipa inicial de projetos de desenvolvimento que utiliza a DevTest Labs foi identificada juntamente com as atividades de desenvolvimento ou teste aplicáveis e estabelece requisitos/objetivos/objetivos para essas equipas.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Marco 1: Estabelecer topologia e design de rede iniciais
A primeira área de foco ao implementar uma solução Azure DevTest Labs é estabelecer a conectividade planeada para as máquinas virtuais. Os seguintes passos descrevem os procedimentos necessários:

1. Defina **os intervalos iniciais de endereços IP** que são atribuídos à subscrição da DevTest Labs em Azure. Este passo requer a previsão do uso esperado em número de VMs para que você possa fornecer um bloco grande o suficiente para a expansão futura.
2. Identificar **os métodos de acesso desejado** aos Laboratórios DevTest (por exemplo, acesso externo/interno). Um ponto-chave neste passo é determinar se as máquinas virtuais têm endereços IP públicos (isto é, acessíveis diretamente a partir da internet).
3. Identificar e estabelecer **métodos de conectividade** com o resto do ambiente de nuvem Azure e no local. Se o encaminhamento forçado com a Rota Expressa estiver ativado, é provável que as máquinas virtuais necessitem de configurações de procuração apropriadas para atravessar a firewall corporativa.
4. Se os VMs forem associados ao **domínio,** determine se se juntam a um domínio baseado na nuvem (Serviços de Diretório AAD, por exemplo) ou a um domínio no local. Para as instalações, determine qual a unidade organizacional (OU) dentro do diretório ativo que as máquinas virtuais unem. Além disso, confirme que os utilizadores têm acesso a aderir (ou estabelecer uma conta de serviço que tenha a capacidade de criar registos de máquinas no domínio)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Marco 2: Implantar o laboratório piloto
Uma vez que a topologia da rede está em vigor, o primeiro/laboratório piloto pode ser criado tomando os seguintes passos:

1. Crie um ambiente inicial da DevTest Labs.
2. Determine imagens e tamanhos VM admissíveis para uso com laboratório. Decida se as imagens personalizadas podem ser enviadas para a Azure para uso com a DevTest Labs.
3. Acesso seguro ao laboratório criando o controlo inicial de acesso baseado em funções (Azure RBAC) para o laboratório (proprietários de laboratório e utilizadores de laboratório). Recomendamos que utilize contas de diretório ativo sincronizadas com o Azure Ative Directory para identidade com a DevTest Labs.
4. Configure a DevTest Labs para usar políticas como horários, gestão de custos, VMs reclamados, imagens personalizadas ou fórmulas.
5. Estabeleça um repositório online como Azure Repos/Git.
6. Decida-se sobre a utilização de repositórios públicos ou privados ou a combinação de ambos. Organize modelos JSON para implementações e sustentação a longo prazo.
7. Se necessário, crie artefactos personalizados. Este passo é opcional. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Marco 3: Documentação, suporte, aprender e melhorar
As equipas piloto iniciais podem necessitar de apoio aprofundado para começar. Use as suas experiências para garantir que a documentação e suporte certos estão em vigor para o lançamento contínuo da Azure DevTest Labs.

1. Introduza as equipas piloto nos seus novos recursos da DevTest Labs (demos, documentação)
2. Com base nas experiências das equipas piloto, planeie e entregue documentação conforme necessário
3. Formalizar o processo de embarque de novas equipas (criação e configuração de laboratórios, acesso, etc.
4. Com base na absorção inicial, verifique se a previsão original do espaço do endereço IP ainda é razoável e precisa
5. Garantir que as revisões de conformidade e segurança adequadas foram concluídas

## <a name="next-steps"></a>Passos seguintes
Veja o próximo artigo desta série: [Governance of Azure DevTest Labs infrastructure](devtest-lab-guidance-governance-resources.md)
