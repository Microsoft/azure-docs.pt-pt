---
title: Política da empresa e conformidade em Azure DevTest Labs
description: Este artigo fornece orientações sobre a política da empresa e o cumprimento da infraestrutura azure DevTest Labs.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74560512"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Governação da infraestrutura azure DevTest Labs - política e conformidade da empresa
Este artigo fornece orientações sobre a política da empresa e o cumprimento da infraestrutura azure DevTest Labs. 

## <a name="public-vs-private-artifact-repository"></a>Repositório público vs. artefacto privado

### <a name="question"></a>Pergunta
Quando deve uma organização usar um repositório de artefactos públicos vs. repositório de artefactos privados em DevTest Labs?

### <a name="answer"></a>Resposta
O [repositório de artefactos públicos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) fornece um conjunto inicial de pacotes de software que são mais utilizados. Ajuda na rápida implantação sem ter de investir tempo para reproduzir ferramentas e add-ins comuns. Pode optar por implantar o seu próprio repositório privado. Você pode usar um rescrito público e privado em paralelo. Pode também optar por desativar o repositório público. Os critérios para a implantação de um repositório privado devem ser orientados pelas seguintes perguntas e considerações:

- A organização tem a obrigação de ter software licenciado corporativo como parte da oferta da DevTest Labs? Se a resposta for sim, então deve ser criado um repositório privado.
- A organização desenvolve software personalizado que fornece uma operação específica, que é necessária como parte do processo global de provisionamento? Se a resposta for sim, então um repositório privado deve ser implementado.
- Se a política de governação da organização requer isolamento, e os repositórios externos não estão sob gestão de configuração direta pela organização, um repositório de artefactos privados deve ser implementado. Como parte deste processo, uma cópia inicial do repositório público pode ser copiada e integrada com o repositório privado. Depois, o repositório público pode ser desativado para que ninguém dentro da organização possa mais acessá-lo. Esta abordagem obriga todos os utilizadores dentro da organização a ter apenas um único repositório que é aprovado pela organização e minimizar a deriva de configuração.

### <a name="single-repository-or-multiple-repositories"></a>Repositório único ou múltiplos repositórios 

### <a name="question"></a>Pergunta
Deve uma organização planear um único repositório ou permitir múltiplos repositórios?

### <a name="answer"></a>Resposta
Como parte da estratégia global de governação e gestão de configurações da sua organização, recomendamos que utilize um repositório centralizado. Quando se utiliza vários repositórios, podem tornar-se silos de software não gerido ao longo do tempo. Com um repositório central, várias equipas podem consumir artefactos deste repositório para os seus projetos. Impõe normalização, segurança, facilidade de gestão e elimina a duplicação de esforços. Como parte da centralização, as seguintes ações são práticas recomendadas para a gestão e sustentabilidade a longo prazo:

- Associe o Azure Repos ao mesmo inquilino do Azure Ative Directory que a assinatura Azure está a utilizar para autenticação e autorização.
- Crie um grupo chamado **All DevTest Labs Developers** em Azure Ative Directory que seja gerido centralmente. Qualquer desenvolvedor que contribua para o desenvolvimento de artefactos deve ser colocado neste grupo.
- O mesmo grupo azure Ative Diretório pode ser usado para fornecer acesso ao repositório Azure Repos e ao laboratório.
- Em Azure Repos, a ramificação ou a forrking devem ser utilizadas para separar um repositório em desenvolvimento do repositório de produção primário. O conteúdo só é adicionado ao ramo principal com um pedido de puxão após uma revisão adequada do código. Uma vez que o revisor de código aprova a alteração, um desenvolvedor principal, responsável pela manutenção da sucursal principal, funde o código atualizado. 

## <a name="corporate-security-policies"></a>Políticas de segurança corporativa

### <a name="question"></a>Pergunta
Como pode uma organização garantir que as políticas de segurança corporativa estão em vigor?

### <a name="answer"></a>Resposta
Uma organização pode alcançá-lo fazendo as seguintes ações:

1. Desenvolver e publicar uma política de segurança abrangente. A política articula as regras de uso aceitável associadas ao software de utilização, ativos em nuvem. Define também o que viola claramente a política. 
2. Desenvolver uma imagem personalizada, artefactos personalizados e um processo de implantação que permita a orquestração dentro do reino de segurança que é definido com diretório ativo. Esta abordagem impõe a fronteira das empresas e estabelece um conjunto comum de controlos ambientais. Estes controlos contra o ambiente que um desenvolvedor pode considerar à medida que se desenvolvem e seguem um ciclo de vida seguro para o desenvolvimento como parte do seu processo global. O objetivo é também proporcionar um ambiente que não seja excessivamente restritivo e que possa dificultar o desenvolvimento, mas um conjunto razoável de controlos. As políticas do grupo na unidade de organização (OU) que contém máquinas virtuais de laboratório podem ser um subconjunto das políticas totais de grupo que são encontradas na produção. Em alternativa, podem ser um conjunto adicional para mitigar adequadamente quaisquer riscos identificados.

## <a name="data-integrity"></a>Integridade dos dados

### <a name="question"></a>Pergunta
Como pode uma organização garantir a integridade dos dados para garantir que os desenvolvedores em remo não podem remover código ou introduzir malware ou software não aprovado?

### <a name="answer"></a>Resposta
Existem várias camadas de controlo para mitigar a ameaça de consultores externos, empreiteiros ou funcionários que estão a colaborar na DevTest Labs. 

Como já foi referido, o primeiro passo deve ter uma política de utilização aceitável elaborada e definida que delineie claramente as consequências quando alguém viola a política. 

A primeira camada de controlos para acesso remoto é aplicar uma política de acesso remoto através de uma ligação VPN que não esteja diretamente ligada ao laboratório. 

A segunda camada de controlos é aplicar um conjunto de objetos de política de grupo que impedem a cópia e a pasta através de ambiente seletiva remota. Poderia ser implementada uma política de rede para não permitir serviços de saída do ambiente, como serviços FTP e RDP fora do ambiente. O encaminhamento definido pelo utilizador poderia forçar todo o tráfego da rede Azure de volta ao local, mas o encaminhamento não poderia explicar todos os URLs que poderiam permitir o upload de dados a menos que fosse controlado através de um representante para digitalizar conteúdos e sessões. Os IPs públicos poderiam ser restringidos dentro da rede virtual que suporta a DevTest Labs para não permitir a ponte de um recurso de rede externo.

Em última análise, o mesmo tipo de restrições deve ser aplicada em toda a organização, que também teria de responder a todos os métodos possíveis de meios amovíveis ou URLs externos que pudessem aceitar um post de conteúdo. Consulte o seu profissional de segurança para rever e implementar uma política de segurança. Para mais recomendações, consulte [a Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Passos seguintes
Ver [Migração e integração de aplicações.](devtest-lab-guidance-governance-application-migration-integration.md)
