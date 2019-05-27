---
title: O que é a pontuação de seguro de identidade? - Azure Active Directory
description: Como pode usar a pontuação de seguro de identidade para melhorar a postura de segurança do seu diretório
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tilarso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6cdff2305914ca6e4144f7784d1a60026a1d27c0
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988689"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory"></a>O que é a pontuação de seguro de identidade no Azure Active Directory?

Qual é o nível de segurança do seu inquilino do Azure AD? Se não sabe como responder a essa pergunta, este artigo explica como a pontuação de seguro de identidade ajuda-na monitorizar e melhorar a sua postura de segurança de identidade.

## <a name="what-is-an-identity-secure-score"></a>O que é uma classificação de segurança de identidade?

A pontuação de seguro de identidade é o número entre 1 e 223 que funciona como um indicador de como alinhados estiver com recomendações de melhores práticas da Microsoft para segurança. Cada ação de melhoria na pontuação de seguro de identidade é adequada para a configuração específica.  

![Pontuação segura](./media/identity-secure-score/identity-secure-score-overview.png)

A classificação ajuda a:

- Medir objetivamente a sua postura de segurança de identidade
- Planear melhorias de segurança de identidade
- Analisar o sucesso das suas melhorias

Pode aceder à classificação e a informações relacionadas no dashboard de classificação de segurança de identidade. Neste dashboard, encontrará:

- Sua pontuação de seguro de identidade
- Um gráfico de comparação que mostra como sua pontuação de segura de identidade se compara aos outros inquilinos no mesmo setor e semelhante tamanho
- Um gráfico de tendência que mostra como a sua pontuação de segura de identidade foi alterada ao longo do tempo
- Uma lista de possíveis melhorias

Ao seguir as ações de melhoria, poderá:

- Melhorar a sua postura de segurança e a sua pontuação
- Tire partido de funcionalidades disponíveis para a sua organização como parte dos seus investimentos de identidade

## <a name="how-do-i-get-my-secure-score"></a>Como posso obter a minha classificação de segurança?

A pontuação de seguro de identidade está disponível em todas as edições do Azure AD. Para aceder à sua classificação, vá para o [dashboard de Descrição geral do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).

## <a name="how-does-it-work"></a>Como funciona?

A cada 48 horas, o Azure analisa a configuração de segurança e compara as definições com as melhores práticas recomendadas. Com base no resultado final desta avaliação, uma nova classificação é calculada para o seu diretório. É possível que a configuração de segurança não é totalmente alinhada com as diretrizes de práticas recomendadas e as ações de aperfeiçoamento apenas parcialmente são cumpridas. Nestes cenários, só serão atribuídos uma parte da pontuação máxima disponível para o controle.

Cada recomendação é medida com base na configuração do Azure AD. Se estiver a utilizar os produtos de terceiros para permitir uma prática recomendada, pode indicar esta configuração nas definições de uma ação de aprimoramento. Tem também a opção de definir as recomendações para ser ignorado se eles não se aplicam ao seu ambiente. Uma recomendação ignorada não contribui para o cálculo da sua classificação.

![Ignore ou marcar ação, como abordado por terceiros.](./media/identity-secure-score/identity-secure-score-ignore-or-third-party-reccomendations.png)

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

### <a name="how-are-controls-scored"></a>Como são classificados de controles?

Controles podem ser classificados de duas formas. Algumas são classificadas de maneira binária - obtenha 100% da pontuação, se tiver a funcionalidade ou definição configurada com base em nossa recomendação. Outras pontuações são calculadas como uma percentagem da configuração do total. Por exemplo, se a recomendação de melhoria afirma que obterá pontos 30 se proteger todos os seus utilizadores com a MFA e tiver apenas 5 de 100 utilizadores total protegidos, receberiam uma pontuação parcial cerca de 2 pontos (5 protegidos / total de 100 * pts máximos 30 = 2 pts pontuação de parcial) .

### <a name="what-does-not-scored-mean"></a>O que significa [Não Classificadas]?

As ações identificadas como [Não Classificadas] são ações que pode executar na sua organização, mas não serão classificadas porque (ainda!) não estão ligadas na ferramenta. Por isso, pode melhorar a segurança à mesma, mas não receberá pontos por essas ações neste momento.

### <a name="how-often-is-my-score-updated"></a>Com que frequência é atualizada a minha classificação?

A classificação é calculada uma vez por dia (aproximadamente à 01:00 PST). Se fizer uma alteração a uma ação medida, a classificação será automaticamente atualizada no dia seguinte. Demora até 48 horas para que uma alteração seja refletida na sua classificação.

### <a name="my-score-changed-how-do-i-figure-out-why"></a>A minha classificação foi alterada. Como posso saber porquê?

Vá para o [Centro de segurança do Microsoft 365](https://security.microsoft.com/), onde encontrará sua pontuação segura completa da Microsoft. Pode ver facilmente todas as alterações para a sua pontuação segura ao rever as alterações detalhadas no separador de histórico.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>A pontuação segura medir o meu risco de introdução infringido?

Resumindo, não. A pontuação segura não expressam uma medida absoluta de quão provável que está a obter infringido. Expressa em que medida adotou funcionalidades que podem compensar o risco de sofrer falhas de segurança. Nenhum serviço pode garantir que não vai ser infringido, e a pontuação segura não deve ser interpretada como uma garantia de qualquer forma.

### <a name="how-should-i-interpret-my-score"></a>Como posso interpretar a minha classificação?

Recebe pontos por configurar funcionalidades recomendadas de segurança ou por realizar tarefas relacionadas com segurança (como ler relatórios). Algumas ações são classificadas por conclusão parcial, como a ativação da autenticação multifator (MFA) para os seus utilizadores. Sua pontuação segura diretamente é representativa dos serviços de segurança da Microsoft que utiliza. Lembre-se de que a segurança deve ser balanceada com facilidade de utilização. Todos os controlos de segurança têm um componente de impacto para o utilizador. Os controlos com um impacto reduzido para o utilizador devem ter pouco ou nenhum efeito nas operações diárias dos seus utilizadores.

Para ver o histórico da pontuação, vá para o [Centro de segurança do Microsoft 365](https://security.microsoft.com/) e reveja a sua pontuação segura global da Microsoft. Pode rever as alterações para geral pontuação segura estar clicando em ver o histórico. Escolha uma data específica para ver os controlos que foram ativados nesse dia e quantos pontos obteve por cada um deles.

### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Como é que a classificação de segurança de identidade se relaciona com a classificação de segurança do Office 365?

O [seguras da Microsoft pontuação](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score) contém cinco controle distinto e categorias de pontuação:

- Identidade
- Dados
- Dispositivos
- Infraestrutura
- Aplicações

A pontuação de seguro de identidade representa a parte de identidade da pontuação de segura da Microsoft. Essa sobreposição significa que as suas recomendações para a identidade secure pontuação e a pontuação de identidade no Microsoft é os mesmos.

## <a name="next-steps"></a>Passos Seguintes

[Obter mais informações sobre a pontuação segura da Microsoft](https://docs.microsoft.com/office365/securitycompliance/microsoft-secure-score)
