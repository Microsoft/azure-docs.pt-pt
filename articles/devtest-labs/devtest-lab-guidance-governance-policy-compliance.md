---
title: Política da empresa e conformidade na Azure DevTest Labs
description: Este artigo fornece orientações sobre a política da empresa e o cumprimento da infraestrutura da Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 89fe380c3a8e0e0a82cc806bf19f9a3454041d94
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592352"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Governação da infraestrutura Azure DevTest Labs - Política e conformidade da empresa
Este artigo fornece orientações sobre a política da empresa e o cumprimento da infraestrutura da Azure DevTest Labs. 

## <a name="public-vs-private-artifact-repository"></a>Repositório de artefactos públicos vs. privados

### <a name="question"></a>Pergunta
Quando é que uma organização deve usar um repositório de artefactos públicos vs. repositório de artefactos privados em Laboratórios DevTest?

### <a name="answer"></a>Resposta
O [repositório de artefactos públicos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) fornece um conjunto inicial de pacotes de software que são mais utilizados. Ajuda na rápida implantação sem ter de investir tempo para reproduzir ferramentas e add-ins comuns de desenvolvimento. Pode optar por implantar o seu próprio repositório privado. Você pode usar um repositório público e privado em paralelo. Também pode optar por desativar o repositório público. Os critérios de implantação de um repositório privado devem ser conduzidos pelas seguintes questões e considerações:

- A organização tem a obrigação de ter software licenciado corporativo como parte da oferta da DevTest Labs? Se a resposta for sim, então um repositório privado deve ser criado.
- A organização desenvolve software personalizado que fornece uma operação específica, que é necessária como parte do processo global de provisionamento? Se a resposta for sim, então um repositório privado deve ser implantado.
- Se a política de governação da organização requer isolamento, e os repositórios externos não estiverem sob gestão de configuração direta pela organização, deve ser implantado um repositório de artefactos privados. Como parte deste processo, uma cópia inicial do repositório público pode ser copiada e integrada com o repositório privado. Depois, o repositório público pode ser desativado para que ninguém dentro da organização possa aceder mais a ele. Esta abordagem obriga todos os utilizadores dentro da organização a ter apenas um único repositório que é aprovado pela organização e minimizar a deriva de configuração.

### <a name="single-repository-or-multiple-repositories"></a>Repositório único ou vários repositórios 

### <a name="question"></a>Pergunta
Deve uma organização planear um único repositório ou permitir vários repositórios?

### <a name="answer"></a>Resposta
Como parte da estratégia geral de governação e gestão de configuração da sua organização, recomendamos que utilize um repositório centralizado. Quando se usam vários repositórios, podem tornar-se silos de software não gerido ao longo do tempo. Com um repositório central, várias equipas podem consumir artefactos deste repositório para os seus projetos. Impõe a normalização, a segurança, a facilidade de gestão e elimina a duplicação de esforços. No âmbito da centralização, recomendam-se práticas recomendadas para a gestão e sustentabilidade a longo prazo:

- Associe o Azure Repos ao mesmo inquilino do Azure Ative Directory que a assinatura Azure está a utilizar para autenticação e autorização.
- Crie um grupo chamado **All DevTest Labs Developers** in Azure Ative Directory que é gerido centralmente. Qualquer desenvolvedor que contribua para o desenvolvimento de artefactos deve ser colocado neste grupo.
- O mesmo grupo Azure Ative Directory pode ser usado para fornecer acesso ao repositório Azure Repos e ao laboratório.
- Em Azure Repos, a ramificação ou a forragem devem ser utilizadas num repositório separado do repositório de produção primário. O conteúdo só é adicionado ao ramo principal com um pedido de puxar após uma revisão de código adequada. Uma vez que o revisor de código aprova a alteração, um desenvolvedor de chumbo, responsável pela manutenção do ramo principal, funde o código atualizado. 

## <a name="corporate-security-policies"></a>Políticas de segurança corporativa

### <a name="question"></a>Pergunta
Como pode uma organização garantir que as políticas de segurança corporativa estão em vigor?

### <a name="answer"></a>Resposta
Uma organização pode alcançá-lo fazendo as seguintes ações:

1. Desenvolver e publicar uma política de segurança abrangente. A política articula as regras de uso aceitável associadas ao software de utilização, ativos em nuvem. Define também o que viola claramente a política. 
2. Desenvolva uma imagem personalizada, artefactos personalizados e um processo de implantação que permita a orquestração dentro do reino de segurança que é definido com diretório ativo. Esta abordagem impõe a fronteira corporativa e estabelece um conjunto comum de controlos ambientais. Estes controlos contra o ambiente, um desenvolvedor pode considerar à medida que se desenvolvem e seguem um ciclo de vida de desenvolvimento seguro como parte do seu processo global. O objetivo é também proporcionar um ambiente que não seja excessivamente restritivo que possa dificultar o desenvolvimento, mas sim um conjunto razoável de controlos. As políticas de grupo na unidade de organização (OU) que contém máquinas virtuais de laboratório podem ser um subconjunto das políticas totais de grupo que são encontradas na produção. Em alternativa, podem ser um conjunto adicional para atenuar adequadamente quaisquer riscos identificados.

## <a name="data-integrity"></a>Integridade dos dados

### <a name="question"></a>Pergunta
Como pode uma organização garantir a integridade dos dados para garantir que os desenvolvedores de remoing não podem remover código ou introduzir malware ou software não aprovado?

### <a name="answer"></a>Resposta
Existem várias camadas de controlo para mitigar a ameaça de consultores externos, empreiteiros ou funcionários que estão a remoe para colaborar na DevTest Labs. 

Tal como anteriormente afirmado, o primeiro passo deve ter uma política de utilização aceitável elaborada e definida que delineie claramente as consequências quando alguém viola a política. 

A primeira camada de controlos para acesso remoto é aplicar uma política de acesso remoto através de uma ligação VPN que não está diretamente ligada ao laboratório. 

A segunda camada de controlos consiste em aplicar um conjunto de objetos de política de grupo que impedem a cópia e a pasta através de um ambiente de trabalho remoto. Poderia ser implementada uma política de rede para não permitir que serviços de saída do ambiente, como os serviços FTP e RDP, fossem implementados fora do ambiente. O encaminhamento definido pelo utilizador poderia forçar todo o tráfego da rede Azure de volta ao local, mas o encaminhamento não poderia explicar todos os URLs que pudessem permitir o upload de dados, a menos que fosse controlado através de um proxy para digitalizar conteúdo e sessões. Os IPs públicos poderiam ser restringidos dentro da rede virtual que suporta a DevTest Labs para não permitir a ponte de um recurso de rede externa.

Em última análise, o mesmo tipo de restrições deve ser aplicado em toda a organização, o que teria também de explicar todos os métodos possíveis de meios amovíveis ou URLs externos que pudessem aceitar um post de conteúdo. Consulte o seu profissional de segurança para rever e implementar uma política de segurança. Para mais recomendações, consulte [a Microsoft Cyber Security.](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs)


## <a name="next-steps"></a>Passos seguintes
Consulte [a migração e integração de aplicações.](devtest-lab-guidance-governance-application-migration-integration.md)
