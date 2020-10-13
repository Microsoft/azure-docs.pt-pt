---
title: Monitorizar a identidade e o acesso no Centro de Segurança do Azure | Microsoft Docs
description: Saiba como utilizar a capacidade de identidade e acesso no Centro de Segurança do Azure para monitorizar a atividade de acesso dos seus utilizadores e os problemas relacionados com identidades.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2020
ms.author: memildin
ms.openlocfilehash: 042780c313c444062fd512ab0d9f38aaeb6cf170
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90894558"
---
# <a name="monitor-identity-and-access"></a>Monitorizar a identidade e o acesso

> [!TIP]
> A partir de março de 2020, as recomendações de identidade e acesso do Azure Security Center estão incluídas em todas as subscrições do nível de preços gratuitos. Se tiver subscrições no nível livre, a sua Pontuação Segura será afetada, uma vez que não foram previamente avaliadas pela sua identidade e segurança de acesso. 

Quando o Security Center identifica potenciais vulnerabilidades de segurança, cria recomendações que o orientam através do processo de configuração dos controlos necessários para endurecer e proteger os seus recursos.

O perímetro de segurança evoluiu de um perímetro de rede para um perímetro de identidade. A segurança torna-se menos sobre a defesa da sua rede e mais sobre a defesa dos seus dados, bem como a gestão da segurança das suas apps e utilizadores. Hoje em dia, com mais dados e mais aplicações a mudarem para a cloud, a identidade torna-se no novo perímetro.

Ao monitorizar as atividades de identidade, você pode tomar ações proativas antes de um incidente ocorrer, ou ações reativas para parar uma tentativa de ataque. Por exemplo, o Security Center pode sinalizar contas precíídas (contas que já não são necessárias, e impedidas de assinar pelo Azure Ative Directory) para remoção. 

Exemplos de recomendações que pode ver na secção de segurança de **identidade e acesso** do Centro de Segurança Azure incluem:

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- As contas preprecadas devem ser removidas da sua subscrição

Para mais informações sobre estas recomendações, bem como uma lista completa das recomendações que pode ver aqui, consulte [recomendações de Identidade e Acesso.](recommendations-reference.md#recs-identity)

> [!NOTE]
> Se a sua subscrição tiver mais de 600 contas, o Security Center não pode executar as recomendações de Identidade contra a sua subscrição. As recomendações que não são executadas estão listadas em "avaliações indisponíveis" abaixo.
O Security Center não consegue executar as recomendações de identidade contra os agentes administrativos do Fornecedor de Soluções Cloud (CSP).
>


Todas as recomendações de identidade e acesso estão disponíveis dentro de dois controlos de segurança na página **recomendações:**

- Gerir acessos e permissões 
- Ativar MFA

![Os dois controlos de segurança com as recomendações relacionadas com a identidade e o acesso](media/security-center-identity-access/two-security-controls-for-identity-and-access.png)


## <a name="enable-multi-factor-authentication-mfa"></a>Ativar a autenticação de vários fatores (MFA)

Habilitar o MFA requer [permissões de inquilinos do Azure Ative Directory (AD).](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

- Se tiver uma edição premium de AD, ative o MFA utilizando [o Acesso Condicional.](../active-directory/conditional-access/concept-conditional-access-policy-common.md)

- Se estiver a utilizar a edição gratuita da AD, ative os **incumprimentos** de segurança no Diretório Ativo Azure, conforme descrito na documentação da [AD](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults).


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre recomendações aplicáveis a outros tipos de recursos Azure, consulte os seguintes artigos:

- [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)