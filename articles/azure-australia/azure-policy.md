---
title: Conformidade de segurança com o Azure Policy e plantas do Azure
description: Garantindo a conformidade e impondo a segurança com Azure Policy e plantas do Azure para agências governamentais da Austrália, já que se relaciona com o ISM e o Essential 8 da ASD.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 0eb3e669f622a9c09c903530cbbab19206ce227f
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571747"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>Conformidade de segurança com o Azure Policy e plantas do Azure

O desafio de impor a governança em seu ambiente de ti, seja um ambiente local, nativo da nuvem ou híbrido, existe para todas as organizações. Uma estrutura de governança técnica robusta precisa estar em vigor para garantir que seu ambiente de Microsoft Azure esteja em conformidade com os requisitos de design, regulatório e segurança.

Para agências governamentais da Austrália, os principais controles a serem considerados ao avaliar o risco estão no ISM ( [manual de segurança de informações) do acsc (centro de segurança da Austrália)](https://acsc.gov.au/infosec/ism/index.htm) . A maioria dos controles detalhados no ISM exige que a aplicação do controle técnico seja efetivamente gerenciada e imposta. É importante que você tenha as ferramentas apropriadas para avaliar e impor a configuração em seus ambientes.

O Microsoft Azure fornece dois serviços complementares para ajudar com esses desafios, Azure Policy e plantas do Azure.

## <a name="azure-policy"></a>Política do Azure

Azure Policy habilita a aplicação dos elementos técnicos da governança de ti de uma organização. O Azure Policy contém uma biblioteca em constante crescimento de políticas internas. Cada política impõe regras e efeitos sobre os recursos de destino do Azure.

Depois que uma política é atribuída aos recursos, a conformidade geral com relação a essa política pode ser avaliada e ser corrigida, se necessário.

Essa biblioteca de políticas internas do Azure permite que uma organização aplique rapidamente os tipos de controles encontrados no ISM ACSC. Exemplos de controles incluem:

* Monitorando máquinas virtuais para atualizações de sistema ausentes
* Auditando contas com permissões elevadas para autenticação multifator
* Identificando bancos de dados SQL não criptografados
* Monitorando o uso do RBAC (controle de acesso baseado em função) do Azure personalizado
* Restringir as regiões do Azure em que os recursos podem ser criados

Se os controles de governança ou regulatórios não forem atendidos por uma definição de Azure Policy interna, uma definição personalizada poderá ser criada e atribuída. Todas as definições de Azure Policy são definidas em JSON e seguem uma [estrutura de definição](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)padrão. As definições de Azure Policy existentes também podem ser duplicadas e usadas para formar a base de uma definição de política personalizada.

A atribuição de políticas individuais do Azure a recursos, especialmente em ambientes complexos ou em ambientes com requisitos regulatórios estritos, pode criar uma grande sobrecarga para seus administradores. Para ajudar com esses desafios, um conjunto de políticas do Azure pode ser agrupado para formar uma iniciativa de Azure Policy. As iniciativas de política são usadas para combinar políticas do Azure relacionadas que, quando aplicadas em conjunto como um grupo, formam a base de um objetivo de segurança ou de conformidade específico. A Microsoft está adicionando definições de iniciativa de Azure Policy internas, incluindo definições criadas para atender a requisitos regulatórios específicos:

![Iniciativas de política de conformidade regulatória](media/regulatory-initiatives.png)

Todas as políticas e iniciativas do Azure são atribuídas a um escopo de atribuição. Esse escopo é definido na assinatura do Azure, no grupo de gerenciamento do Azure ou nos níveis do grupo de recursos do Azure. Depois que as políticas do Azure ou as iniciativas de política necessárias tiverem sido atribuídas, uma organização poderá impor os requisitos de configuração em todos os recursos do Azure recém-criados.

A atribuição de um novo Azure Policy ou iniciativa não afetará os recursos existentes do Azure. O Azure Policy pode; no entanto, habilite uma organização para exibir a conformidade dos recursos existentes do Azure. Todos os recursos que foram identificados como sem conformidade podem ser corrigidos no critério da organização

### <a name="azure-policy-and-initiatives-in-action"></a>Azure Policy e iniciativas em ação

As definições de Azure Policy e iniciativa internas disponíveis podem ser encontradas no nó definição na seção política do portal do Azure:

![Definições de Azure Policy internas](media/policy-definitions.png)

Usando a biblioteca de definições internas, você pode pesquisar rapidamente políticas que atendam a um requisito de organização, examinar a definição de política e atribuir a política aos recursos apropriados. Por exemplo, o ISM requer a MFA (autenticação multifator) para todos os usuários privilegiados e para todos os usuários com acesso a repositórios de dados importantes. No Azure Policy você pode pesquisar por "MFA" entre as definições de Azure Policy:

![Políticas do Azure MFA](media/mfa-policies.png)

Depois que uma política adequada é identificada, você atribui a política ao escopo desejado. Se não houver nenhuma política interna que atenda aos seus requisitos, você poderá duplicar a política existente e fazer as alterações desejadas:

![Duplicar Azure Policy existente](media/duplicate-policy.png)

A Microsoft também fornece uma coleção de exemplos de Azure Policy no [GitHub](https://github.com/Azure/azure-policy) como um ' início rápido ' para que você crie políticas personalizadas do Azure. Esses exemplos de política podem ser copiados diretamente para o editor de Azure Policy dentro do portal do Azure.

Ao criar Azure Policy iniciativas, você pode classificar a lista de definições de políticas disponíveis, internas e personalizadas, adicionando as definições necessárias.

Por exemplo, você pode pesquisar na lista de definições de Azure Policy disponíveis para todas as políticas relacionadas às máquinas virtuais do Windows. Em seguida, você tem essas definições para uma iniciativa projetada para impor as práticas recomendadas de proteção de máquina virtual:

![Lista de políticas do Azure](media/initiative-definitions.png)

Ao atribuir uma iniciativa de Azure Policy ou de política a um escopo de atribuição, é possível que você exclua os recursos do Azure dos efeitos das políticas, excluindo os Grupos de Gerenciamento do Azure ou os grupos de recursos do Azure.

### <a name="real-time-enforcement-and-compliance-assessment"></a>Imposição em tempo real e avaliação de conformidade

Azure Policy verificações de conformidade de recursos no escopo do Azure são executadas quando as seguintes condições são atendidas:

* Quando uma iniciativa de Azure Policy ou Azure Policy é atribuída
* Quando o escopo de um Azure Policy ou iniciativa existente é alterado
* Sob demanda por meio da API até um máximo de 10 verificações por hora
* Uma vez a cada 24 horas-o comportamento padrão

Uma verificação de conformidade de política para um único recurso do Azure é executada 15 minutos após uma alteração ter sido feita no recurso.

Uma visão geral do Azure Policy conformidade dos recursos pode ser analisada dentro do portal do Azure por meio do painel de conformidade de política:

![Pontuação de conformidade Azure Policy](media/simple-compliance.png)

A figura total de porcentagem de conformidade do recurso é uma agregação da conformidade de todos os recursos implantados no escopo em relação a todas as suas políticas do Azure atribuídas. Isso permite que você identifique os recursos em um ambiente que não esteja em conformidade e planeje o plano para melhor corrigir esses recursos.

O painel de conformidade de política também inclui o histórico de alterações para cada recurso. Se um recurso for identificado como não sendo mais compatível com a política atribuída e a correção automática não estiver habilitada, você poderá exibir quem fez a alteração, o que foi alterado e quando as alterações foram feitas nesse recurso.

## <a name="azure-blueprints"></a>Azure Blueprints

Os planos gráficos do Azure estendem a capacidade de Azure Policy combinando-os com:

* RBAC do Azure
* Grupos de recursos do Azure
* [Modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)

Os planos gráficos permitem a criação de designs de ambiente que implantam recursos do Azure de modelos do Resource Manager, configurem o RBAC e imponham e auditem a configuração atribuindo Azure Policy. Plantas formam um modelo de ambiente editável e reimplantável. Depois que o plano gráfico tiver sido criado, ele poderá ser atribuído a uma assinatura do Azure. Uma vez atribuída, todos os recursos do Azure definidos no plano gráfico serão criados e as políticas do Azure aplicadas. A implantação e a configuração de recursos definidos em um Azure Blueprint podem ser monitoradas no console do Azure Blueprint no portal do Azure.

Os planos gráficos do Azure que foram editados devem ser republicados no portal do Azure. Cada vez que um plano gráfico é republicado, o número de versão do plano gráfico é incrementado. O número de versão permite que você determine qual versão específica de um Blueprint foi implantada nas assinaturas do Azure de uma organização. Se desejar, a versão atualmente atribuída do plano gráfico pode ser atualizada para a versão mais recente.

Os recursos implantados usando um Azure Blueprint podem ser configurados com bloqueios de [recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources) no momento da implantação. Os bloqueios de recursos impedem que os recursos sejam modificados ou excluídos acidentalmente.

A Microsoft está desenvolvendo modelos de Azure Blueprint para uma variedade de setores e requisitos regulatórios. A biblioteca atual de definições de Azure Blueprint disponíveis pode ser exibida no portal do Azure ou na página [Blueprint de segurança e conformidade do Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/) no portal de confiança do serviço.

### <a name="azure-blueprint-artifacts"></a>Artefatos de Azure Blueprint

Para criar um Azure Blueprint, você pode começar com um modelo de plano gráfico em branco ou usar um dos planos de exemplo existentes como um ponto de partida. Você pode adicionar artefatos ao plano gráfico que será configurado como parte da implantação:

![Artefatos de Azure Blueprint](media/blueprint-artifacts.png)

Esses artefatos podem incluir o grupo de recursos do Azure e os recursos e as iniciativas de Azure Policy e políticas associadas para impor a configuração necessária para que seu ambiente seja compatível, você é o seu requisitos regulatórios, por exemplo, os controles do ISM para a proteção do sistema.

Cada um desses artefatos também pode ser configurado com parâmetros. Esses valores são fornecidos quando o plano gráfico foi atribuído a uma assinatura do Azure e implantado. Os parâmetros permitem que um único plano gráfico seja criado e usado para implantar recursos em ambientes diferentes sem a necessidade de editar o plano gráfico subjacente.

A Microsoft está desenvolvendo cmdlets Azure PowerShell e CLI para criar e gerenciar plantas do Azure com a intenção de que um plano gráfico possa ser mantido e implantado por uma organização por meio de um pipeline de CI/CD.

## <a name="next-steps"></a>Passos Seguintes

Este artigo explicou como a governança e a segurança podem ser impostas com Azure Policy e plantas do Azure. Agora que você já expôs em um alto nível, saiba como usar cada serviço com mais detalhes:

* [Visão geral de Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)
* [Visão geral dos planos gráficos do Azure](https://azure.microsoft.com/services/blueprints/)
* [Exemplos do Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index)
* [Repositório de exemplos de Azure Policy](https://github.com/Azure/azure-policy)
* [Estrutura de definição de Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)
* [Efeitos de Azure Policy](https://docs.microsoft.com/azure/governance/policy/concepts/effects)
