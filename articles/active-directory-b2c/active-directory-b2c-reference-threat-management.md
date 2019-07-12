---
title: Gerir as ameaças aos recursos e dados no Azure Active Directory B2C
description: Saiba mais sobre técnicas de deteção e mitigação de ataques denial-of-service e ataques de palavra-passe no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 7232917df6018c9c8afc7e7edd3730a277b193f4
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798232"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gerir as ameaças aos recursos e dados no Azure Active Directory B2C

O Azure Active Directory (Azure AD) B2C tem recursos incorporados que podem ajudar a proteger contra ameaças para os dados e recursos. Estas ameaças incluem ataques denial-of-service e ataques de palavra-passe. Ataques denial-of-service podem tornar recursos indisponível para usuários. Ataques de palavra-passe levam ao acesso não autorizado aos recursos.

## <a name="denial-of-service-attacks"></a>Ataques denial-of-service

O Azure AD B2C faz a defesa contra ataques de inundação SYN através de um cookie SYN. O Azure AD B2C também protege contra ataques de negação de serviço através de limites para ligações e taxas.

## <a name="password-attacks"></a>Ataques de palavra-passe

Palavras-passe que são definidas por utilizadores têm de ser razoavelmente complexo. O Azure AD B2C tem técnicas de atenuação para ataques de palavra-passe. Atenuação inclui a deteção de ataques de palavra-passe de dicionário e ataques de força bruta a senhas. Ao utilizar vários sinais, o Azure AD B2C analisa a integridade dos pedidos. O Azure AD B2C foi concebido para inteligência diferenciar usuários de hackers e botnets.

O Azure AD B2C utiliza uma estratégia sofisticada para bloquear as contas. As contas estão bloqueadas com base no IP do pedido e as palavras-passe que introduziu. A duração do bloqueio também aumenta com base na probabilidade que é um ataque. Depois de uma palavra-passe é experimentada 10 vezes sem êxito ocorre do (o tentativa de limiar predefinido), um bloqueio de um minuto. Da próxima vez que um início de sessão for bem-sucedido após a conta está desbloqueada (ou seja, depois da conta tem de foi desbloqueada automaticamente pelo serviço assim que o período de bloqueio expira), outro bloqueio de um minuto ocorre e continua para cada início de sessão sem êxito. Introduzir a mesma palavra-passe repetidamente não contabiliza como vários inícios de sessão sem êxito.

Os períodos de 10 bloqueio primeiro são longo de um minuto. Os períodos de bloqueio de 10 a seguir são ligeiramente maiores e aumentam a duração depois de todos os períodos de bloqueio de 10. Repõe o contador de bloqueio para zero após um início de sessão com êxito, quando a conta não está bloqueada. Períodos de bloqueio podem sobreviver a várias até cinco horas.

## <a name="manage-password-protection-settings"></a>Gerir as definições de proteção de palavra-passe

Para gerir definições de proteção de palavra-passe, incluindo o limiar de bloqueio:

1. Navegue para o [portal do Azure](https://portal.azure.com).
1. Selecione o **diretório + subscrição** filtrar no menu superior direito do portal, em seguida, selecione o seu inquilino do Azure AD B2C.
1. Selecione **do Azure Active Directory** no menu da esquerda (ou selecione **todos os serviços** na secção de canto superior esquerdo do portal, em seguida, procure e selecione *Azure Active Directory*).
1. Sob **Security**, selecione **métodos de autenticação**, em seguida, selecione **proteção de palavra-passe**.
1. Introduzir as definições de proteção de palavra-passe pretendida, em seguida, selecione **guardar**.

    ![Palavra-passe proteção página do portal Azure nas definições do Azure AD](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*Definir o limiar de bloqueio para 5 na **proteção por senha** definições*.

## <a name="view-locked-out-accounts"></a>Contas de saída bloqueado do Vista

Para obter informações sobre contas de saída bloqueado, pode verificar o Active Directory [relatório de atividade de início de sessão](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md). Sob **Status**, selecione **falha**. Tentativas de início de sessão com uma **início de sessão do código de erro** de `50053` indicar uma conta bloqueada:

![Secção do Azure AD início de sessão no relatório que mostra a conta de saída bloqueado](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

Para saber mais sobre como ver o relatório de atividade de início de sessão no Azure Active Directory, veja [início de sessão códigos de erro de relatórios de atividades](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
