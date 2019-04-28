---
title: Orquestrar a implementação do Azure DevTest Labs
description: Este artigo fornece orientações para a implementação de orquestração de Azure DevTest Labs na sua organização.
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
ms.openlocfilehash: e0ac09a68bda539fe7abd05fce1739d1a58a3c99
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127349"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Orquestrar a implementação do Azure DevTest Labs
Este artigo fornece uma abordagem recomendada para implementação rápida e a implementação do Azure DevTest Labs. A imagem seguinte enfatiza o processo geral de como uma orientação prescritiva ao observar a flexibilidade para oferecer suporte a vários cenários e requisitos do setor.

![Passos para implementar o Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Suposições
Este artigo pressupõe que tem os seguintes itens no local antes de implementar um piloto do DevTest Labs:

- **Subscrição do Azure**: A equipa piloto tem acesso para a implantação de recursos para uma subscrição do Azure. Se as cargas de trabalho são apenas desenvolvimento e teste, é recomendado para selecionar a oferta Enterprise DevTest para as imagens disponíveis adicionais e taxas mais baixas em máquinas de virtuais do Windows.
- **Acesso no local**: Se necessário, acesso no local já foi configurado. O acesso no local pode ser feito por meio de uma ligação VPN de Site a site ou Express Route. Conectividade através do Express Route, normalmente, pode demorar várias semanas para estabelecer, é recomendado para ter a via expressa em vigor antes de iniciar o projeto.
- **Criar um piloto Equipes**: As equipas de projeto de desenvolvimento inicial que utiliza o DevTest Labs tem sido identificada, juntamente com desenvolvimento aplicável ou atividades de teste e estabelecer metas/requisitos/objetivos por essas equipes.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Etapa 1: Estabelecer e design da topologia de rede inicial
A primeira área de foco ao implementar uma solução de Azure DevTest Labs é estabelecer a conectividade planeada para as máquinas virtuais. Os passos seguintes descrevem os procedimentos necessários:

1. Definir **inicial intervalos de endereços IP** que estão atribuídos à subscrição no Azure DevTest Labs. Este passo requer a utilização esperada no número de VMs de previsão, para que pode fornecer um bloco de suficiente grande para expansão futura.
2. Identificar **métodos de acesso desejado** em laboratórios DevTest (por exemplo, o acesso externo / interno). Um ponto importante neste passo é determinar se as máquinas virtuais têm endereços IP públicos (ou seja, acessível a partir da internet diretamente).
3. Identificar e estabelecer **métodos de conectividade** com o restante do Azure e na cloud ambiente no local. Se o encaminhamento com o Express Route forçada estiver ativado, é provável que as máquinas virtuais devem configurações de proxy apropriados para atravessar do firewall Corporativo.
4. Se as VMs devem ser **associados a um domínio**, determinar se associar um domínio com base na cloud (Serviços de diretório do AAD por exemplo) ou um domínio no local. No local, determine qual unidade organizacional (UO) no active directory que junta as máquinas virtuais. Além disso, confirme que os utilizadores têm acesso a associar (ou estabelecer uma conta de serviço que tem a capacidade de criar registos de computador no domínio)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Etapa 2: Implementar o laboratório piloto
Assim que estiver a topologia de rede no local, o laboratório de primeiro/piloto pode ser criado, efetuando os seguintes os passos:

1. Criar um ambiente de DevTest Labs inicial (pode encontrar instruções passo a passo [aqui](https://github.com/Azure/fta-devops/blob/master/devtest-labs/articles/devtest-labs-walkthrough-it.md))
2. Determine as imagens de VM permitidas e os tamanhos para utilização com o laboratório. Decida se as imagens personalizadas podem ser carregadas para o Azure para utilização com o DevTest Labs.
3. Proteger o acesso ao laboratório através da criação inicial funções Base acesso controles (RBAC) para o laboratório (os proprietários de laboratório e os utilizadores de laboratório). Recomendamos que utilize contas sincronizadas do Active Directory com o Azure Active Directory para a identidade com os laboratórios DevTest.
4. Configure o DevTest Labs para utilizar políticas, tais como agendas, custos de gestão, VMs reclamáveis, imagens personalizadas ou fórmulas.
5. Estabelece um repositório online, como o Azure Repos/Git.
6. Decida sobre a utilização de repositórios públicos ou privados ou combinação de ambos. Organize os modelos JSON para implementações e sustainment de longo prazo.
7. Se for necessário, crie artefactos personalizados. Este passo é opcional. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Etapa 3: Documentação, suporte, aprenda e melhorar
As equipes de pilotos iniciais podem exigir suporte aprofundado para começar a trabalhar. Utilize as suas experiências para garantir que a documentação adequada e o suporte está no local para a implementação contínua do Azure DevTest Labs.

1. Apresentar as equipes de pilotos a seus novos recursos de laboratórios DevTest (demonstrações, documentação)
2. Com base nas experiências de piloto equipes, planejar e fornecer documentação, conforme necessário
3. Formalizar o processo de inclusão novas equipes (criar e configurar os laboratórios, fornecer acesso, etc)
4. Com base na adoção inicial, certifique-se de previsão original do espaço de endereços IP é ainda razoável e precisas
5. Certifique-se de que concluiu as revisões de segurança e conformidade adequadas

## <a name="next-steps"></a>Passos Seguintes
Consulte o artigo seguinte nesta série: [Governação da infraestrutura do Azure DevTest Labs](devtest-lab-guidance-governance-resources.md)
