---
title: FAQ e problemas conhecidos com a proteção de identidade (atualizada) no Azure Active Directory | Documentos da Microsoft
description: FAQ e problemas conhecidos com a proteção de identidade (atualizada) no Azure Active Directory.
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1cc8a9305103a30fc0ea0fc40c23a522e136d80
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56807189"
---
# <a name="faqs-and-known-issues-with-identity-protection-refreshed-in-azure-active-directory"></a>FAQ e problemas conhecidos com a proteção de identidade (atualizada) no Azure Active Directory


## <a name="dismiss-user-risk-known-issues"></a>Dispensar o risco de utilizador problemas conhecidos

**Dispensar o risco de utilizador** clássica Identity Protection define o ator no histórico de risco do utilizador no Identity Protection (atualizadas) para **do Azure AD**.


**Dispensar o risco de utilizador** Identity Protection (atualizadas) define o ator no histórico de risco do utilizador no Identity Protection (atualizadas) para **\<nome do administrador com um hiperlink que aponta para o painel do utilizador\>**.

Existe um problema conhecido atual, fazendo com que latências no fluxo de exoneração do risco de utilizador. Se tiver uma "política de risco do utilizador", esta política irá deixar de aplicar-se aos utilizadores ignorados dentro de minutos de clicar em "Dispensar o risco de utilizador". No entanto, são conhecidos atrasos com a experiência do Usuário atualizando o "Estado de risco" de utilizadores ignorados. Como solução, atualize a página no nível do navegador para ver o utilizador mais recente "Estado de risco".


## <a name="risky-users-report-known-issues"></a>Problemas conhecidos de relatórios de utilizadores de risco

Consulta a **nome de utilizador** campo diferenciam maiúsculas de minúsculas, ao mesmo tempo consultas no **nome** campo são agnóstico do caso.

Alternar **Mostrar datas como** oculta a **última ATUALIZAÇÃO do risco** coluna. Para readd a coluna, clique em **colunas** na parte superior do painel de utilizadores de risco.

**Dispensar todos os eventos** clássica Identity Protection define o estado dos eventos de risco para **fechado (resolvido)**.


## <a name="risky-sign-ins-report-known-issues"></a>Relatório de inícios de sessão arriscados problemas conhecidos

**Resolver** num risco de evento define o estado como **utilizadores foram aprovados na MFA por política com base em riscos**.


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="why-cant-i-set-my-own-risk-levels-for-each-risk-event"></a>Por que não é possível definir os meus próprios níveis de risco para cada evento de risco?

Níveis de risco no Identity Protection são baseiam a precisão da deteção de e com tecnologia nosso supervisionado machine learning. Para personalizar o que os utilizadores de experiência são apresentados, administrador pode incluir/excluir determinados utilizadores/grupos do risco de utilizador e políticas de risco de início de sessão.


### <a name="why-does-the-location-of-a-sign-in-not-match-where-the-user-truly-signed-in-from"></a>Por que a localização de um início de sessão não corresponde em que o usuário realmente iniciou sessão a partir?

Mapeamento de localização geográfica IP é um desafio de toda a indústria. Se sentir que a localização listada no relatório de inícios de sessão não corresponde a localização real, contacte o suporte. 


### <a name="how-do-the-feedback-mechanisms-in-identity-protection-work"></a>Como funcionam os mecanismos de comentários no Identity Protection?

**Confirmar comprometido** (num início de sessão) – informa o Azure AD Identity Protection, que o início de sessão não foi efetuada pelo proprietário de identidade e indica um comprometimento.

- Após receber seus comentários, podemos mover o estado do risco de início de sessão e utilizador para **Confirmed comprometido** e o risco de nível para **elevada**.

- Além disso, fornecemos as informações para nossos sistemas para futuras melhorias na avaliação de riscos de aprendizagem automática.

    > [!NOTE]
    > Se o utilizador já é corrigido, não clique **confirmar comprometido** uma vez que ele se mova o estado do risco de início de sessão e utilizador para **Confirmed comprometido** e o risco de nível para **elevada**.

**Confirmar seguro** (num início de sessão) – informa o Azure AD Identity Protection, que o início de sessão foi realizado pelo proprietário de identidade e não indica um comprometimento.

- Após receber seus comentários, foi possível mover o início de sessão (e não pelo usuário) o estado para o risco **Confirmed safe** e o nível de risco para **-**.

- Além disso, fornecemos as informações para nossos sistemas para futuras melhorias na avaliação de riscos de aprendizagem automática.

    > [!NOTE]
    > Se acreditar que o utilizador não for comprometido, utilize **dispensar o risco de utilizador** no nível do usuário em vez de usar **Confirmed safe** no nível de início de sessão. R **dispensar o risco de utilizador** no utilizador nível fecha o risco de utilizador e todos os anteriores inícios de sessão de risco e eventos de risco.



### <a name="why-am-i-seeing-a-user-with-a-low-or-above-risk-score-even-if-no-risky-sign-ins-or-risk-events-are-shown-in-identity-protection"></a>Por que estou a ver uma pontuação de risco do utilizador com um pouco (ou superior), mesmo que não existem inícios de sessão arriscados ou eventos de risco são mostrados no Identity Protection?

Tendo em conta o usuário não expira e é a natureza cumulativo de risco, um usuário pode ter um risco de utilizador de menor ou acima, mesmo se não há recentes risco inícios de sessão ou eventos de risco, mostrados na proteção de identidade. Isto pode acontecer se a atividade maliciosa apenas num utilizador ocorria além do período de tempo para as quais podemos armazenar os detalhes de inícios de sessão de risco e eventos de risco. Não podemos expirar o risco de utilizador porque mal-intencionadas já são conhecidas para manter o ambiente dos clientes mais de 140 dias atrás de uma identidade comprometido antes aumentando gradualmente seus ataques. Os clientes podem rever a linha cronológica de risco do utilizador para compreender por que um utilizador está em risco indo para: `Azure Portal > Azure Active Directory > Risky users’ report > Click on an at-risk user > Details’ drawer > Risk history tab`

### <a name="why-does-a-sign-in-have-a-sign-in-risk-aggregate-score-of-high-when-the-detections-associated-with-it-are-of-low-or-medium-risk"></a>Por que motivo o um início de sessão tem uma "início de sessão (agregar)" pontuação de risco alta quando as deteções associadas, pelo que são de risco baixo ou médio?

A pontuação de risco elevado de agregação pode ser baseada em outros recursos de entrada ou o fato de que mais do que uma deteção acionados para esse início de sessão. E, por outro lado, um início de sessão pode ter um início de sessão risco (agregação) média, mesmo que as deteções associadas com o início de sessão são de alto risco. 
