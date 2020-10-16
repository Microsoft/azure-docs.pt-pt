---
title: O que é a pontuação segura da identidade? - Diretório Ativo Azure
description: Como pode usar a pontuação de segurança de identidade para melhorar a postura de segurança do seu diretório
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c319489fe4c884cd5de48ac2d3e47e7beb3026f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90705491"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>Qual é a pontuação de identidade segura no Azure Ative Directory?

Qual é o nível de segurança do seu inquilino do Azure AD? Se não sabe como responder a esta pergunta, este artigo explica como a pontuação de segurança de identidade o ajuda a monitorizar e melhorar a sua postura de segurança de identidade.

## <a name="what-is-an-identity-secure-score"></a>O que é uma classificação de segurança de identidade?

A pontuação de segurança de identidade é um número entre 1 e 223 que funciona como um indicador de como está alinhado com as recomendações de boas práticas da Microsoft para a segurança. Cada ação de melhoria na pontuação de segurança de identidade é adaptada à sua configuração específica.  

![Classificação de segurança](./media/identity-secure-score/identity-secure-score-overview.png)

A classificação ajuda a:

- Medir objetivamente a sua postura de segurança de identidade
- Planear melhorias de segurança de identidade
- Analisar o sucesso das suas melhorias

Pode aceder à classificação e a informações relacionadas no dashboard de classificação de segurança de identidade. Neste dashboard, encontrará:

- A sua pontuação de segurança de identidade
- Um gráfico de comparação mostrando como a sua pontuação de identidade segura se compara a outros inquilinos da mesma indústria e tamanho semelhante
- Um gráfico de tendência que mostra como a sua pontuação de segurança de identidade mudou ao longo do tempo
- Uma lista de possíveis melhorias

Ao seguir as ações de melhoria, poderá:

- Melhore a sua postura de segurança e a sua pontuação
- Aproveite as funcionalidades disponíveis para a sua organização como parte dos seus investimentos identitários

## <a name="how-do-i-get-my-secure-score"></a>Como posso obter a minha classificação de segurança?

A pontuação de segurança de identidade está disponível em todas as edições do Azure AD. As organizações podem aceder à sua pontuação de identidade segura a partir do **portal**  >  **Azure Ative Directory**  >  **Security**  >  **Identity Secure Score**.

## <a name="how-does-it-work"></a>Como funciona?

A cada 48 horas, o Azure analisa a configuração de segurança e compara as definições com as melhores práticas recomendadas. Com base no resultado desta avaliação, uma nova pontuação é calculada para o seu diretório. É possível que a sua configuração de segurança não esteja totalmente alinhada com a orientação das melhores práticas e as ações de melhoria só sejam parcialmente cumpridas. Nestes cenários, só lhe será atribuída uma parte da pontuação máxima disponível para o controlo.

Cada recomendação é medida com base na configuração do Azure AD. Se estiver a utilizar produtos de terceiros para ativar uma recomendação de boas práticas, pode indicar esta configuração nas definições de uma ação de melhoria. Também tem a opção de definir recomendações a serem ignoradas se não se aplicarem ao seu ambiente. Uma recomendação ignorada não contribui para o cálculo da sua classificação.

![Ignorar ou marcar a ação como coberto por terceiros](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

## <a name="how-does-it-help-me"></a>Como é que me ajuda?

A classificação de segurança ajuda a:

- Medir objetivamente a sua postura de segurança de identidade
- Planear melhorias de segurança de identidade
- Analisar o sucesso das suas melhorias

## <a name="what-you-should-know"></a>O que deve saber

### <a name="who-can-use-the-identity-secure-score"></a>Quem pode utilizar a classificação de segurança de identidade?

A classificação de segurança de identidade pode ser utilizada pelas seguintes funções:

- Administrador global
- Administrador de segurança
- Leitores de segurança

### <a name="how-are-controls-scored"></a>Como são pontuados os controlos?

Os controlos podem ser marcados de duas formas. Alguns são pontuados de forma binária - obtém-se 100% da pontuação se tiver a funcionalidade ou configuração configurada com base na nossa recomendação. Outras pontuações são calculadas em percentagem da configuração total. Por exemplo, se a recomendação de melhoria indicar que obterá 30 pontos se proteger todos os seus utilizadores com MFA e só tiver 5 de 100 utilizadores totais protegidos, receberá uma pontuação parcial de cerca de 2 pontos (5 protegidos / 100 no total * 30 max pts = 2 pts pontuação parcial).

### <a name="what-does-not-scored-mean"></a>O que significa [Não Classificadas]?

As ações identificadas como [Não Classificadas] são ações que pode executar na sua organização, mas não serão classificadas porque (ainda!) não estão ligadas na ferramenta. Por isso, pode melhorar a segurança à mesma, mas não receberá pontos por essas ações neste momento.

### <a name="how-often-is-my-score-updated"></a>Com que frequência é atualizada a minha classificação?

A classificação é calculada uma vez por dia (aproximadamente à 01:00 PST). Se fizer uma alteração a uma ação medida, a classificação será automaticamente atualizada no dia seguinte. Demora até 48 horas para que uma alteração seja refletida na sua classificação.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>A minha classificação foi alterada. Como posso saber porquê?

Dirija-se ao [centro de segurança Microsoft 365,](https://security.microsoft.com/)onde encontrará a sua pontuação completa de segurança da Microsoft. Pode facilmente ver todas as alterações na sua pontuação segura, revendo as alterações aprofundadas no separador histórico.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>A pontuação segura mede o meu risco de ser violada?

Resumindo, não. A pontuação segura não expressa uma medida absoluta da probabilidade de ser violada. Expressa em que medida adotou funcionalidades que podem compensar o risco de sofrer falhas de segurança. Nenhum serviço pode garantir que não será violado, e a pontuação segura não deve ser interpretada como uma garantia de forma alguma.

### <a name="how-should-i-interpret-my-score"></a>Como posso interpretar a minha classificação?

Recebe pontos por configurar funcionalidades recomendadas de segurança ou por realizar tarefas relacionadas com segurança (como ler relatórios). Algumas ações são classificadas por conclusão parcial, como a ativação da autenticação multifator (MFA) para os seus utilizadores. A sua pontuação segura é diretamente representativa dos serviços de segurança da Microsoft que utiliza. Lembre-se que a segurança deve ser equilibrada com a usabilidade. Todos os controlos de segurança têm um componente de impacto para o utilizador. Os controlos com um impacto reduzido para o utilizador devem ter pouco ou nenhum efeito nas operações diárias dos seus utilizadores.

Para ver o histórico de pontuação, dirija-se ao [centro de segurança Microsoft 365](https://security.microsoft.com/) e reveja a sua pontuação de segurança total da Microsoft. Pode rever as alterações à sua pontuação máxima segura, clicando no Ver Histórico. Escolha uma data específica para ver os controlos que foram ativados nesse dia e quantos pontos obteve por cada um deles.

### <a name="how-does-the-identity-secure-score-relate-to-the-microsoft-365-secure-score"></a>Como é que a pontuação de segurança de identidade diz respeito à pontuação segura da Microsoft 365?

A [pontuação segura da Microsoft](/office365/securitycompliance/microsoft-secure-score) contém cinco categorias distintas de controlo e pontuação:

- Identidade
- Dados
- Dispositivos
- Infraestrutura
- Apps

A pontuação de segurança de identidade representa a parte de identidade da pontuação segura da Microsoft. Esta sobreposição significa que as suas recomendações para a pontuação de segurança de identidade e a pontuação de identidade na Microsoft são as mesmas.

## <a name="next-steps"></a>Passos seguintes

[Saiba mais sobre a pontuação segura da Microsoft](/office365/securitycompliance/microsoft-secure-score)