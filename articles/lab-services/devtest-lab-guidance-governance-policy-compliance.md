---
title: Política da empresa e conformidade no Azure DevTest Labs
description: Este artigo fornece orientação sobre como controlar a conformidade e a política da empresa para Azure DevTest Labs infraestrutura.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 81ca2a90b1940d70e170cab3f8d18144a5d5e5a8
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2019
ms.locfileid: "74560512"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Governança da infraestrutura de Azure DevTest Labs-política e conformidade da empresa
Este artigo fornece orientação sobre como controlar a conformidade e a política da empresa para Azure DevTest Labs infraestrutura. 

## <a name="public-vs-private-artifact-repository"></a>Repositório de artefatos público versus privado

### <a name="question"></a>Pergunta
Quando uma organização deve usar um repositório de artefato público versus um repositório de artefato privado no DevTest Labs?

### <a name="answer"></a>Resposta
O [repositório público de artefatos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) fornece um conjunto inicial de pacotes de software que são usados com mais frequência. Isso ajuda na implantação rápida sem a necessidade de investir tempo para reproduzir ferramentas de desenvolvimento e suplementos comuns. Você pode optar por implantar seu próprio repositório privado. Você pode usar um repositório público e um privado em paralelo. Você também pode optar por desabilitar o repositório público. Os critérios para implantar um repositório privado devem ser orientados pelas perguntas e considerações a seguir:

- A organização tem a necessidade de ter software licenciado corporativo como parte de sua oferta do DevTest Labs? Se a resposta for sim, um repositório privado deverá ser criado.
- A organização desenvolve software personalizado que fornece uma operação específica, que é necessária como parte do processo geral de provisionamento? Se a resposta for sim, um repositório privado deverá ser implantado.
- Se a política de governança da organização exigir isolamento e os repositórios externos não estiverem sob o gerenciamento de configuração direta pela organização, um repositório de artefatos privado deverá ser implantado. Como parte desse processo, uma cópia inicial do repositório público pode ser copiada e integrada com o repositório privado. Em seguida, o repositório público pode ser desabilitado para que ninguém na organização possa acessá-lo mais. Essa abordagem força todos os usuários na organização a terem apenas um único repositório que é aprovado pela organização e minimizam a descompasso de configuração.

### <a name="single-repository-or-multiple-repositories"></a>Repositório único ou vários repositórios 

### <a name="question"></a>Pergunta
Uma organização deve planejar um único repositório ou permitir vários repositórios?

### <a name="answer"></a>Resposta
Como parte da estratégia geral de gerenciamento de configuração e governança da sua organização, recomendamos que você use um repositório centralizado. Quando você usa vários repositórios, eles podem se tornar silos de software não gerenciado no decorrer do tempo. Com um repositório central, várias equipes podem consumir artefatos desse repositório para seus projetos. Ela impõe a padronização, a segurança, a facilidade de gerenciamento e elimina a duplicação de esforços. Como parte da centralização, as seguintes ações são práticas recomendadas para gerenciamento e sustentabilidade de longo prazo:

- Associe o Azure Repos com o mesmo locatário Azure Active Directory que a assinatura do Azure está usando para autenticação e autorização.
- Crie um grupo chamado **todos os desenvolvedores do DevTest Labs** em Azure Active Directory que seja gerenciado centralmente. Qualquer desenvolvedor que contribui para o desenvolvimento de artefatos deve ser colocado nesse grupo.
- O mesmo grupo de Azure Active Directory pode ser usado para fornecer acesso ao repositório de Azure Repos e ao laboratório.
- Em Azure Repos, a ramificação ou a bifurcação devem ser usadas para separar um repositório no desenvolvimento do repositório de produção primário. O conteúdo é adicionado somente ao Branch mestre com uma solicitação pull após uma revisão de código adequada. Depois que o revisor de código aprovar a alteração, um desenvolvedor líder, responsável pela manutenção do Branch mestre, mesclará o código atualizado. 

## <a name="corporate-security-policies"></a>Políticas de segurança corporativa

### <a name="question"></a>Pergunta
Como uma organização pode garantir que as políticas de segurança corporativas estejam em vigor?

### <a name="answer"></a>Resposta
Uma organização pode conseguir isso fazendo as seguintes ações:

1. Desenvolver e publicar uma política de segurança abrangente. A política articula as regras de uso aceitável associado ao software usando os ativos de nuvem. Ele também define o que claramente viola a política. 
2. Desenvolva uma imagem personalizada, artefatos personalizados e um processo de implantação que permita a orquestração dentro do realm de segurança definido com o Active Directory. Essa abordagem impõe o limite corporativo e define um conjunto comum de controles ambientais. Esses controles no ambiente que um desenvolvedor pode considerar ao desenvolver e seguir um ciclo de vida de desenvolvimento seguro como parte de seu processo geral. O objetivo também é fornecer um ambiente que não seja excessivamente restritivo que possa impedir o desenvolvimento, mas um conjunto razoável de controles. As diretivas de grupo na UO (unidade organizacional) que contém máquinas virtuais de laboratório podem ser um subconjunto do total de políticas de grupo encontradas na produção. Como alternativa, eles podem ser um conjunto adicional para atenuar corretamente quaisquer riscos identificados.

## <a name="data-integrity"></a>Integridade dos dados

### <a name="question"></a>Pergunta
Como uma organização pode garantir a integridade dos dados para garantir que os desenvolvedores de comunicação remota não possam remover código ou introduzir malware ou software não aprovado?

### <a name="answer"></a>Resposta
Há várias camadas de controle para atenuar a ameaça de consultores externos, prestadores de contrato ou funcionários que são de comunicação remota para colaborar no DevTest Labs. 

Conforme mencionado anteriormente, a primeira etapa deve ter uma política de uso aceitável, rascunho e definida que descreve claramente as consequências quando alguém viola a política. 

A primeira camada de controles para acesso remoto é aplicar uma política de acesso remoto por meio de uma conexão VPN que não está conectada diretamente ao laboratório. 

A segunda camada de controles é aplicar um conjunto de objetos de diretiva de grupo que impeçam a cópia e a colagem da área de trabalho remota. Uma política de rede pode ser implementada para não permitir serviços de saída do ambiente, como serviços FTP e RDP do ambiente. O roteamento definido pelo usuário pode forçar todo o tráfego de rede do Azure de volta para o local, mas o roteamento não pôde considerar todas as URLs que podem permitir o carregamento de dados, a menos que sejam controlados por meio de um proxy para verificar o conteúdo e as sessões. Os IPs públicos podem ser restritos na rede virtual que dá suporte ao DevTest Labs para não permitir a ponte de um recurso de rede externa.

Por fim, o mesmo tipo de restrições precisa ser aplicado em toda a organização, o que teria que também considerar todos os métodos possíveis de mídia removível ou URLs externas que poderiam aceitar uma postagem de conteúdo. Consulte seu profissional de segurança para examinar e implementar uma política de segurança. Para obter mais recomendações, consulte [Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Passos seguintes
Consulte [migração e integração de aplicativos](devtest-lab-guidance-governance-application-migration-integration.md).
