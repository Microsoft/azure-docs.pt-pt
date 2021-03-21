---
title: Compreender políticas de segurança, iniciativas e recomendações no Centro de Segurança Azure
description: Conheça as políticas de segurança, iniciativas e recomendações no Centro de Segurança Azure.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 4dc29c8b52a3d0953445666672a716af013ee408
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176438"
---
# <a name="what-are-security-policies-initiatives-and-recommendations"></a>O que são políticas de segurança, iniciativas e recomendações?

O Security Center aplica iniciativas de segurança às suas assinaturas. Estas iniciativas contêm uma ou mais políticas de segurança. Cada uma dessas políticas resulta numa recomendação de segurança para melhorar a sua postura de segurança. Esta página explica cada uma destas ideias em detalhe.


## <a name="what-is-a-security-policy"></a>O que é uma política de segurança?

Uma definição de política Azure, criada na Política Azure, é uma regra sobre condições de segurança específicas que pretende controlar. As definições incluem coisas como controlar que tipo de recursos podem ser implantados ou impor a utilização de etiquetas em todos os recursos. Também pode criar as suas próprias definições de política personalizada.

Para implementar estas definições de política (seja incorporada ou personalizada), terá de as atribuir. Pode atribuir qualquer uma destas políticas através do portal do Azure, do PowerShell ou da CLI do Azure.

Existem diferentes tipos de políticas na Política Azure. O Centro de Segurança utiliza principalmente políticas de "Auditoria" que verificam condições e configurações específicas e depois reportam sobre o cumprimento. Existem também políticas de "Impor" que podem ser usadas para aplicar configurações seguras.

## <a name="what-is-a-security-initiative"></a>O que é uma iniciativa de segurança?

Uma iniciativa Azure é uma coleção de definições ou regras políticas do Azure, que são agrupadas em conjunto para um objetivo ou propósito específico. As iniciativas do Azure simplificam a gestão das suas políticas, agrupar um conjunto de políticas, logicamente, como um único item.

Uma iniciativa de segurança define a configuração desejada das suas cargas de trabalho e ajuda a garantir que está a cumprir os requisitos de segurança da sua empresa ou reguladores.

Tal como as políticas de segurança, as iniciativas do Centro de Segurança também são criadas na Política Azure. Você pode usar [a Azure Policy](../governance/policy/overview.md) para gerir as suas políticas, construir iniciativas e atribuir iniciativas a várias subscrições ou a grupos de gestão inteiros.

A iniciativa padrão atribuída automaticamente a cada subscrição no Azure Security Center é Azure Security Benchmark. Este benchmark é o conjunto de diretrizes específicas da Microsoft para a segurança e conformidade das melhores práticas com base em quadros comuns de conformidade. Este referencial amplamente respeitado baseia-se nos controlos do [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) e do Instituto Nacional de [Normalização e Tecnologia (NIST)](https://www.nist.gov/) com foco na segurança centrada na nuvem. Saiba mais sobre a [Referência de Segurança do Azure](../security/benchmarks/introduction.md).

O Centro de Segurança oferece as seguintes opções para trabalhar com iniciativas e políticas de segurança:

- **Ver e editar a iniciativa por defeito incorporada** - Quando ativar o Centro de Segurança, a iniciativa denominada 'Azure Security Benchmark' é automaticamente atribuída a todas as subscrições registadas do Security Center. Para personalizar esta iniciativa, pode ativar ou desativar políticas individuais dentro dela, editando os parâmetros de uma apólice. Consulte a lista de [políticas de segurança incorporadas](./policy-reference.md) para entender as opções disponíveis fora da caixa.

- **Adicione as suas próprias iniciativas personalizadas** - Se quiser personalizar as iniciativas de segurança aplicadas à sua subscrição, pode fazê-lo dentro do Security Center. Em seguida, receberá recomendações se as suas máquinas não seguirem as políticas que cria. Para obter instruções sobre a construção e atribuição de políticas [personalizadas, consulte utilizando iniciativas e políticas de segurança personalizadas.](custom-security-policies.md)

- **Adicione normas de conformidade regulamentar como iniciativas** - O painel de conformidade regulamentar do Centro de Segurança mostra o estado de todas as avaliações dentro do seu ambiente no contexto de uma determinada norma ou regulamento (como Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Para obter mais informações, consulte [Melhorar a sua conformidade regulamentar.](security-center-compliance-dashboard.md)

## <a name="what-is-a-security-recommendation"></a>O que é uma recomendação de segurança?

Utilizando as políticas, o Security Center analisa periodicamente o estado de conformidade dos seus recursos para identificar potenciais erros de segurança e fraquezas. Em seguida, fornece-lhe recomendações sobre como remediar essas questões. As recomendações são o resultado da avaliação dos seus recursos contra as políticas relevantes e da identificação de recursos que não estão a cumprir os seus requisitos definidos.

O Centro de Segurança faz as suas recomendações de segurança com base nas suas iniciativas escolhidas. Quando uma política da sua iniciativa é comparada com os seus recursos e encontra uma ou mais que não estão em conformidade, é apresentada como uma recomendação no Centro de Segurança.

Recomendações são ações a tomar para garantir e endurecer os seus recursos. Cada recomendação fornece-lhe as seguintes informações:

- Uma breve descrição da questão
- As medidas de reparação a realizar para implementar a recomendação
- Os recursos afetados

Na prática, funciona assim:

1. Azure Security Benchmark é uma ***iniciativa***
1. Inclui uma ***política*** para exigir que todas as contas de Armazenamento Azure restrinjam o acesso à rede, reduzindo assim a sua superfície de ataque. Esta política chama-se "As contas de armazenamento devem restringir o acesso à rede utilizando regras de rede virtuais" e podem ser desativadas ou ativadas a partir da Política de Azure
1. Se o Azure Security Center encontrar uma conta de Armazenamento Azure em qualquer uma das suas subscrições protegidas, avalia essas contas para ver se estão protegidas com regras de rede virtuais. Se não forem, apresenta uma ***recomendação*** para corrigir essa situação e endurecer a segurança desses recursos. 

Assim, uma iniciativa (1) inclui políticas (2) que geram recomendações quando apropriado (3). 

## <a name="viewing-the-relationship-between-a-recommendation-and-a-policy"></a>Ver a relação entre uma recomendação e uma política

Como mencionado acima, o Centro de Segurança é construído em recomendações são baseados no Azure Security Benchmark. Quase todas as recomendações têm uma política subjacente que deriva de uma exigência no benchmark.

Quando se está a rever os detalhes de uma recomendação, muitas vezes é útil ser capaz de ver a política subjacente. Para cada recomendação apoiada por uma política, utilize o link de **definição** de política Ver a partir da página de detalhes da recomendação para ir diretamente à entrada da Política Azure para a política relevante:

:::image type="content" source="media/release-notes/view-policy-definition.png" alt-text="Link para a página política do Azure para a política específica que apoia uma recomendação":::

Utilize este link para ver a definição de política e rever a lógica de avaliação. 

Se estiver a rever a lista de recomendações no nosso guia de referência de recomendações de [Segurança,](recommendations-reference.md)também verá ligações às páginas de definição de políticas:

:::image type="content" source="media/release-notes/view-policy-definition-from-documentation.png" alt-text="Aceder à página política do Azure para uma política específica diretamente a partir da página de referência do Centro de Segurança Azure":::


## <a name="next-steps"></a>Passos seguintes

Esta página explicou, a um nível elevado, os conceitos básicos e as relações entre políticas, iniciativas e recomendações. Para obter informações relacionadas, consulte:

- [Criar iniciativas personalizadas](custom-security-policies.md)
- [Desativar as políticas de segurança para desativar recomendações](tutorial-security-policy.md#disable-security-policies-and-disable-recommendations)
- [Saiba como editar uma política de segurança na Política Azure](../governance/policy/tutorials/create-and-manage.md)