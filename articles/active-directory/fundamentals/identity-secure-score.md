---
title: O que é a pontuação de segura de identidade? -O azure Active Directory (pré-visualização pública) | Documentos da Microsoft
description: Instruções sobre como pode utilizar a identidade seguro pontuação para melhorar a postura de segurança do seu inquilino do Azure AD.
services: active-directory
keywords: classificação de segurança de identidade, Azure AD, acesso seguro aos recursos da empresa
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: overview
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2018
ms.author: markvi
ms.reviewer: nigu
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8617442c811dc2fe8f961038f35ebcf218a58302
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60247398"
---
# <a name="what-is-the-identity-secure-score-in-azure-active-directory-public-preview"></a>O que é a pontuação de seguro de identidade no Azure Active Directory? (Pré-visualização pública)

Qual é o nível de segurança do seu inquilino do Azure AD? Se não sabe responder a esta pergunta, leia este artigo para saber como a classificação de segurança de identidade ajuda a monitorizar e melhorar a sua postura de segurança de identidade. 

## <a name="what-is-an-identity-secure-score"></a>O que é uma classificação de segurança de identidade?

A classificação de segurança de identidade é um número entre 1 e 248 que funciona como indicador do seu nível de alinhamento com as recomendações de melhores práticas de segurança da Microsoft.


![Classificação de segurança](./media/identity-secure-score/01.png)



A classificação ajuda a:

- Medir objetivamente a sua postura de segurança de identidade

- Planear melhorias de segurança de identidade

- Analisar o sucesso das suas melhorias 


Pode aceder à classificação e a informações relacionadas no dashboard de classificação de segurança de identidade. Neste dashboard, encontrará:

- A sua classificação

    ![Classificação de segurança](./media/identity-secure-score/02.png)

- Um gráfico de comparação

    ![Classificação de segurança](./media/identity-secure-score/03.png)

- Um gráfico de tendências

    ![Classificação de segurança](./media/identity-secure-score/04.png)

- Uma lista de melhores práticas da segurança de identidade. 

    ![Classificação de segurança](./media/identity-secure-score/05.png)


Ao seguir as ações de melhoria, poderá:

- Melhorar a sua postura de segurança e a sua classificação.
 
- Tirar partido das funcionalidades de Identidade da Microsoft. 



## <a name="how-do-i-get-my-secure-score"></a>Como posso obter a minha classificação de segurança?

A Classificação de Segurança de Identidade está disponível em todas as edições do Azure AD.

Para aceder à sua classificação, vá para o [dashboard de Descrição geral do Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/IdentitySecureScore).



## <a name="how-does-it-work"></a>Como funciona?

A cada 48 horas, o Azure analisa a configuração de segurança e compara as definições com as melhores práticas recomendadas. Com base no resultado desta avaliação, é calculada uma nova classificação para o seu inquilino. Isto significa que pode demorar até 48 horas até que uma alteração de configuração que tenha feito se reflita na sua classificação. 

Cada recomendação é medida com base na configuração do Azure AD. Se estiver a utilizar produtos de terceiros para ativar uma recomendação de melhor prática, pode indicar isto nas definições de uma ação de melhoria.

![Classificação de segurança](./media/identity-secure-score/07.png)


Além disso, tem também a opção de definir recomendações para serem ignoradas caso não se apliquem ao seu ambiente. Uma recomendação ignorada não contribui para o cálculo da sua classificação. 
 
![Classificação de segurança](./media/identity-secure-score/06.png)



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

### <a name="what-does-not-scored-mean"></a>O que significa [Não Classificadas]?

As ações identificadas como [Não Classificadas] são ações que pode executar na sua organização, mas não serão classificadas porque (ainda!) não estão ligadas na ferramenta. Por isso, pode melhorar a segurança à mesma, mas não receberá pontos por essas ações neste momento.

### <a name="how-often-is-my-score-updated"></a>Com que frequência é atualizada a minha classificação?

A classificação é calculada uma vez por dia (aproximadamente à 01:00 PST). Se fizer uma alteração a uma ação medida, a classificação será automaticamente atualizada no dia seguinte. Demora até 48 horas para que uma alteração seja refletida na sua classificação.


### <a name="my-score-changed-how-do-i-figure-out-why"></a>A minha classificação foi alterada. Como posso saber porquê?

Na página do analisador de classificação, no [portal de classificação de segurança](https://securescore.microsoft.com/#!/score), clique num ponto de dados de um dia específico e desloque a página para baixo para ver as ações concluídas e incompletas desse dia para saber o que mudou.

### <a name="does-the-secure-score-measure-my-risk-of-getting-breached"></a>O serviço Secure Score mede o risco de falha de segurança?

Resumindo, não. O serviço Secure Score não expressa uma medida absoluta de probabilidade de sofrer uma falha de segurança. Expressa em que medida adotou funcionalidades que podem compensar o risco de sofrer falhas de segurança. Nenhum serviço pode garantir que não vai sofrer falhas de segurança e o Secure Score não deve ser interpretado como uma garantia de forma alguma.

### <a name="how-should-i-interpret-my-score"></a>Como posso interpretar a minha classificação?

Recebe pontos por configurar funcionalidades recomendadas de segurança ou por realizar tarefas relacionadas com segurança (como ler relatórios). Algumas ações são classificadas por conclusão parcial, como a ativação da autenticação multifator (MFA) para os seus utilizadores. A sua classificação de segurança é diretamente representativa dos serviços de segurança da Microsoft que utiliza. Lembre-se que a segurança deve ser sempre equilibrada com a usabilidade. Todos os controlos de segurança têm um componente de impacto para o utilizador. Os controlos com um impacto reduzido para o utilizador devem ter pouco ou nenhum efeito nas operações diárias dos seus utilizadores.

Para ver o histórico de classificação, vá para a página do analisador de classificação no [portal de classificação de segurança](https://securescore.microsoft.com/#!/score). Escolha uma data específica para ver os controlos que foram ativados nesse dia e quantos pontos obteve por cada um deles.


### <a name="how-does-the-identity-secure-score-relate-to-the-office-365-secure-score"></a>Como é que a classificação de segurança de identidade se relaciona com a classificação de segurança do Office 365? 

A [classificação de segurança do Office 365](https://docs.microsoft.com/office365/securitycompliance/office-365-secure-score) está prestes a ser migrada para um conjunto de cinco classificações diferentes:

- Identidade

- Dados

- Dispositivos

- Infraestrutura

- Aplicações

A classificação de segurança de identidade representa a parte da identidade da classificação de segurança do Office 365. Isto significa que as recomendações para a classificação de segurança de identidade e a classificação de identidade no Office 365 são idênticas. 


## <a name="next-steps"></a>Passos Seguintes

Caso pretenda ver um vídeo sobre a classificação de segurança do Office 365, clique [aqui](https://www.youtube.com/watch?v=jzfpDJ9Kg-A).
 
