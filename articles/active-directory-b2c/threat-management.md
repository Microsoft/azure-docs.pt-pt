---
title: Gerir ameaças a recursos e dados
titleSuffix: Azure AD B2C
description: Saiba mais sobre técnicas de deteção e mitigação para ataques de negação de serviço e ataques de palavra-passe no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e5184698cab1874f327173fb30cf527feee48cad
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85384979"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gerir ameaças a recursos e dados no Azure Ative Directory B2C

O Azure Ative Directory B2C (Azure AD B2C) tem funcionalidades incorporadas que podem ajudá-lo a proteger contra ameaças aos seus recursos e dados. Estas ameaças incluem ataques de negação de serviço e ataques de palavra-passe. Os ataques de negação de serviço podem tornar os recursos indisponíveis para os utilizadores pretendidos. Os ataques de palavra-passe levam a acesso não autorizado aos recursos.

## <a name="denial-of-service-attacks"></a>Ataques de negação de serviço

Azure AD B2C defende contra ataques de inundação SYN usando um cookie SYN. O Azure AD B2C também protege contra ataques de negação de serviço utilizando limites para tarifas e ligações.

## <a name="password-attacks"></a>Ataques de palavra-passe

As palavras-passe definidas pelos utilizadores devem ser razoavelmente complexas. Azure AD B2C tem técnicas de mitigação para ataques de senha. A mitigação inclui a deteção de ataques de senhas de força bruta e ataques de palavras-passe do dicionário. Ao utilizar vários sinais, o Azure AD B2C analisa a integridade dos pedidos. O Azure AD B2C foi concebido para diferenciar inteligentemente os utilizadores pretendidos de hackers e botnets.

A azure AD B2C usa uma estratégia sofisticada para bloquear contas. As contas estão bloqueadas com base no IP do pedido e nas palavras-passe inseridas. A duração do bloqueio também aumenta com base na probabilidade de ser um ataque. Depois de uma palavra-passe ser experimenta 10 vezes sem sucesso (o limiar de tentativa por defeito), ocorre um bloqueio de um minuto. A próxima vez que um login não for desbloqueado após o desbloqueio da conta (isto é, depois de a conta ter sido desbloqueada automaticamente pelo serviço uma vez expirado o período de bloqueio), ocorre outro bloqueio de um minuto e continua para cada login mal sucedido. Introduzir a mesma palavra-passe repetidamente não conta como múltiplos logins mal sucedidos.

Os primeiros 10 períodos de bloqueio têm um minuto de duração. Os próximos 10 períodos de bloqueio são ligeiramente mais longos e aumentam a duração após cada 10 períodos de bloqueio. O contador de bloqueios reinicia para zero após um login bem sucedido quando a conta não está bloqueada. Os períodos de bloqueio podem durar até cinco horas.

## <a name="manage-password-protection-settings"></a>Gerir as definições de proteção de palavras-passe

Para gerir as definições de proteção de palavras-passe, incluindo o limiar de bloqueio:

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Utilize o filtro **de subscrição Diretório +** no menu superior para selecionar o diretório que contém o seu inquilino Azure AD B2C.
1. No menu esquerdo, selecione **Azure AD B2C**. Ou, selecione **Todos os serviços** e procure e selecione **Azure AD B2C**.
1. Em **Segurança**, selecione **métodos de autenticação (pré-visualização)** e, em seguida, selecione **a proteção da palavra-passe**.
1. Introduza as definições de proteção de palavras-passe desejadas e, em seguida, **selecione Guardar**.

    ![Página de proteção de palavras-passe do portal Azure nas definições de AD Azure](./media/threat-management/portal-02-password-protection.png)
    <br />*Definição do limiar de bloqueio para 5 nas definições **de proteção de palavras-passe** *.

## <a name="view-locked-out-accounts"></a>Ver contas bloqueadas

Para obter informações sobre contas bloqueadas, pode consultar o [relatório de atividades de entrada no](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)Ative Directory . Em **Estado de Estado**, selecione **Falha**. Tentativas falhadas de inscrição com um **código de erro de inscrição** indicam uma conta `50053` bloqueada:

![Relatório de inscrição da Azure AD mostrando conta bloqueada](./media/threat-management/portal-01-locked-account.png)

Para saber mais sobre a visualização do relatório de atividades de inscrição no Diretório Ativo Azure, consulte [códigos de erro do relatório de atividades de inscrição](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
