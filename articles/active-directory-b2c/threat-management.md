---
title: Gerir ameaças a recursos e dados
titleSuffix: Azure AD B2C
description: Conheça técnicas de deteção e mitigação para ataques de negação de serviço e ataques de senha sacado no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: fbccfe170b72770a1b14994e506a117ce0e4371e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848203"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gerir ameaças a recursos e dados no Diretório Ativo Azure B2C

O Azure Ative Directory B2C (Azure AD B2C) tem funcionalidades incorporadas que podem ajudá-lo a proteger contra ameaças aos seus recursos e dados. Estas ameaças incluem ataques de negação de serviço e ataques de senhas. Os ataques de negação de serviço podem tornar os recursos indisponíveis para os utilizadores pretendidos. Ataques de senha levam a acesso não autorizado a recursos.

## <a name="denial-of-service-attacks"></a>Ataques de negação de serviço

Azure AD B2C defende-se contra ataques de inundação syn usando um cookie SYN. O Azure AD B2C também protege contra ataques de negação de serviço, utilizando limites para tarifas e ligações.

## <a name="password-attacks"></a>Ataques de senha

As palavras-passe definidas pelos utilizadores são necessárias para serem razoavelmente complexas. O Azure AD B2C tem técnicas de mitigação em vigor para ataques de senhas. A mitigação inclui a deteção de ataques de senhas de força bruta e ataques de senhas de dicionário. Ao utilizar vários sinais, o Azure AD B2C analisa a integridade dos pedidos. O Azure AD B2C foi concebido para diferenciar inteligentemente os utilizadores pretendidos de hackers e botnets.

O Azure AD B2C usa uma estratégia sofisticada para bloquear contas. As contas estão bloqueadas com base no IP do pedido e nas palavras-passe introduzidas. A duração do bloqueio também aumenta com base na probabilidade de ser um ataque. Depois de uma palavra-passe ser experimentada 10 vezes sem sucesso (o limiar de tentativa padrão), ocorre um bloqueio de um minuto. Da próxima vez que um login for infrutífero após o desbloqueio da conta (isto é, depois de a conta ter sido automaticamente desbloqueada pelo serviço uma vez expirado o prazo de bloqueio), ocorre outro bloqueio de um minuto e continua para cada início de sessão mal sucedido. Introduzir a mesma senha repetidamente não conta como múltiplos logins mal sucedidos.

Os primeiros 10 períodos de bloqueio têm um minuto de duração. Os próximos 10 períodos de bloqueio são ligeiramente mais longos e aumentam a duração após cada 10 períodos de bloqueio. O contador de bloqueio reinicia para zero após um login bem sucedido quando a conta não está bloqueada. Os períodos de bloqueio podem durar até cinco horas.

## <a name="manage-password-protection-settings"></a>Gerir as definições de proteção de passwords

Para gerir as definições de proteção de passwords, incluindo o limiar de bloqueio:

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Utilize o filtro de **subscrição Diretório +** no menu superior para selecionar o diretório que contém o seu inquilino Azure AD B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Em **Segurança,** selecione métodos de **autenticação (Pré-visualização)** e, em seguida, selecione **a proteção da palavra-passe**.
1. Introduza as definições de proteção de senha desejadas e, em seguida, selecione **Guardar**.

    ![página de proteção de senha do portal Azure nas definições de Anúncio sintetizar](./media/threat-management/portal-02-password-protection.png)
    <br />*Regulação do limiar de bloqueio para 5 nas definições de proteção de **palavras-passe*** .

## <a name="view-locked-out-accounts"></a>Ver contas bloqueadas

Para obter informações sobre contas bloqueadas, pode consultar o [relatório de atividade](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)sessão do Diretório Ativo . Em **'Estado',** selecione **Falha**. Tentativas de inessão falhadas com um **código de erro** de `50053` indicam uma conta bloqueada:

![Secção do relatório de entrada da AD Azure mostrando conta bloqueada](./media/threat-management/portal-01-locked-account.png)

Para conhecer o relatório de atividade de entrada no Diretório Ativo do Azure, consulte [os códigos de erro](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md)do relatório de atividade de entrada .
