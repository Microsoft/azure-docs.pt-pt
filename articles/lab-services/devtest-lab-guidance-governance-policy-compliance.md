---
title: Governação da infraestrutura do Azure DevTest Labs
description: Este artigo fornece orientações de governação da infraestrutura do Azure DevTest Labs.
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
ms.openlocfilehash: e02400ef940efdf42370fbdc1da75bdc7062a8ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127366"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Governação da infraestrutura do Azure DevTest Labs - política da empresa e a conformidade
Este artigo fornece orientações sobre que regem a política da empresa e a conformidade para a infraestrutura do Azure DevTest Labs. 

## <a name="public-vs-private-artifact-repository"></a>Público versus o repositório de artefactos privados

### <a name="question"></a>Pergunta
Quando uma organização utilizar um repositório público de artefactos vs. o repositório de artefactos privados no DevTest Labs?

### <a name="answer"></a>Resposta
O [repositório público de artefactos](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) fornece um conjunto inicial de pacotes de software que são utilizados mais frequentemente. Ajuda com a implementação rápida sem ter de investir tempo para reproduzir as ferramentas de desenvolvedor comuns e suplementos. Pode optar por implementar seu próprio repositório privado. Pode usar um público e um repositório privado em paralelo. Também pode optar por desativar o repositório público. Os critérios para implementar um repositório privado devem ser controlados pelas seguintes perguntas e considerações:

- A organização tem um requisito para que o software licenciado empresarial como parte da sua oferta de laboratórios DevTest? Se a resposta é Sim, deve ser criado um repositório privado.
- A organização desenvolver software personalizado que fornece uma operação específica, o que é necessária como parte do processo geral de aprovisionamento? Se a resposta for Sim, em seguida, um repositório privado deve ser implementado.
- Se a política de Governança da organização requer isolamento e repositórios externos não estão sob a gestão da configuração direta pela organização, um repositório de artefactos privados deve ser implementado. Como parte deste processo, uma cópia inicial do repositório público pode ser copiada e integrada com o repositório privado. Em seguida, o repositório público pode ser desativado para que ninguém dentro da organização consegue aceder-lhe mais. Essa abordagem força todos os utilizadores dentro da organização ter apenas um único repositório for aprovado pela organização e minimizar os descompassos de configuração.

### <a name="single-repository-or-multiple-repositories"></a>Repositório único ou vários repositórios 

### <a name="question"></a>Pergunta
Deve-se uma organização planear para um único repositório ou permitir que vários repositórios?

### <a name="answer"></a>Resposta
Como parte da estratégia de gestão de configuração e de governação de global da sua organização, recomendamos que utilize um repositório centralizado. Quando usa vários repositórios, pode-se tornar silos de software não-gerenciado no tempo. Com um repositório central, várias equipes podem consumir artefactos deste repositório para seus projetos. Impõe a padronização, a segurança, a facilidade de gerenciamento e elimina a duplicação de esforços. Como parte de relação à centralização, as seguintes ações são recomendadas práticas recomendadas para gerenciamento a longo prazo e de sustentabilidade:

- Associe os repositórios do Azure com o mesmo inquilino do Azure Active Directory que a subscrição do Azure está a utilizar para autenticação e autorização.
- Crie um grupo chamado **todos os desenvolvedores de laboratórios DevTest** no Azure Active Directory que será gerido centralmente. Qualquer desenvolvedor que contribui para o desenvolvimento de artefacto deve ser colocado neste grupo.
- O mesmo grupo de Azure Active Directory pode ser utilizado para fornecer acesso para o repositório de repositórios do Azure e para o laboratório.
- No Azure repositórios, ramificação ou criar o fork deve ser utilizado para um repositório separado no desenvolvimento do repositório principal de produção. Conteúdo só é adicionado ao ramo principal com um pedido pull após uma revisão de código adequado. Assim que o revisor de código aprova a alteração, um desenvolvedor-chefe, o que é responsável pela manutenção do ramo principal, mescla o código atualizado. 

## <a name="corporate-security-policies"></a>Políticas de segurança empresarial

### <a name="question"></a>Pergunta
Como pode uma organização Certifique-se de políticas de segurança empresarial no local?

### <a name="answer"></a>Resposta
Uma organização pode obtê-lo ao fazer as seguintes ações:

1. Desenvolver e publicar uma política de segurança abrangente. As regras de uso aceitável associado à utilização de articule toda a política de software, os recursos na cloud. Também define o que claramente viola a política. 
2. Desenvolva uma imagem personalizada, artefacto personalizado e um processo de implantação que permite a orquestração dentro de território de segurança, que é definido com o active directory. Essa abordagem impõe limite da empresa e define um conjunto comum de controlos ambientais. Esses controles contra o ambiente de um desenvolvedor pode considerar à medida que desenvolveram e seguem um ciclo de vida de desenvolvimento seguro como parte de seu processo geral. O objetivo é também a disponibilizar um ambiente que não seja excessivamente restritivo esse Maio atrapalham desenvolvimento, mas um conjunto razoável de controles. As políticas de grupo, a unidade organizacional (UO) que contém as máquinas virtuais do laboratório podem ser um subconjunto das políticas de grupo total que se encontram na produção. Em alternativa, eles podem ser um conjunto adicional para corretamente atenuar quaisquer riscos identificados.

## <a name="data-integrity"></a>Integridade dos dados

### <a name="question"></a>Pergunta
Como uma organização pode garantir a integridade dos dados para se certificar de que os desenvolvedores de comunicação remota não é possível remover o código ou introduzir malware ou software não aprovado?

### <a name="answer"></a>Resposta
Existem várias camadas de controle para atenuar a ameaça de consultores externos, prestadores de serviços ou funcionários que estão a comunicação remota no colaborar no DevTest Labs. 

Conforme indicado anteriormente, o primeiro passo tem de ter uma política de uso aceitável esboçada e definidos que descreve claramente as conseqüências quando alguém viola a política. 

A primeira camada de controles para acesso remoto é aplicar uma política de acesso remoto através de uma ligação de VPN que não estejam ligada diretamente ao laboratório. 

A segunda camada de controles é aplicar um conjunto de objetos de política de grupo que evitar copiar e colar através do ambiente de trabalho remoto. Uma política de rede poderão ser implementada por não permitir a saída dos serviços de ambiente, como o FTP e serviços RDP fora do ambiente. Encaminhamento definido pelo utilizador foi forçar todo o tráfego de rede do Azure para o local, mas o roteamento não foi possível conta para todos os URLs que podem permitir o carregamento de dados, a menos que controlado através de um proxy para analisar o conteúdo e sessões. IPs públicos poderia haver restrições dentro da rede virtual que suporta o DevTest Labs para não permitir o protocolo de bridge de um recurso de rede externa.

Por fim, o mesmo tipo de restrições de tem de ser aplicado em toda a organização, que teria que também em conta todos os métodos possíveis de mídia removível ou URLs externos que podem aceitar uma publicação de conteúdo. Consulte a segurança profissional para rever e implementar uma política de segurança. Para obter mais recomendações, veja [Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Passos Seguintes
Ver [migração de aplicativos e a integração](devtest-lab-guidance-governance-application-migration-integration.md).
