---
title: Gerenciar ameaças a recursos e dados no Azure Active Directory B2C
description: Saiba mais sobre as técnicas de detecção e mitigação para ataques de negação de serviço e ataques de senha no Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: eae82fbd56782faf5b15479c13fe530e189a0e3e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256907"
---
# <a name="manage-threats-to-resources-and-data-in-azure-active-directory-b2c"></a>Gerenciar ameaças a recursos e dados no Azure Active Directory B2C

O Azure Active Directory B2C (Azure AD B2C) tem recursos internos que podem ajudá-lo a proteger contra ameaças a seus recursos e dados. Essas ameaças incluem ataques de negação de serviço e ataques de senha. Os ataques de negação de serviço podem tornar os recursos indisponíveis para os usuários pretendidos. Os ataques de senha levam a acesso não autorizado aos recursos.

## <a name="denial-of-service-attacks"></a>Ataques de negação de serviço

Azure AD B2C defende contra ataques de inundação SYN usando um cookie SYN. O Azure AD B2C também protege contra ataques de negação de serviço usando limites de tarifas e conexões.

## <a name="password-attacks"></a>Ataques de senha

As senhas definidas pelos usuários são obrigadas a serem razoavelmente complexas. Azure AD B2C tem técnicas de mitigação em vigor para ataques de senha. A mitigação inclui a detecção de ataques de senha de força bruta e ataques de senha de dicionário. Usando vários sinais, Azure AD B2C analisa a integridade das solicitações. O Azure AD B2C foi projetado para diferenciar de forma inteligente os usuários pretendidos de hackers e botnets.

Azure AD B2C usa uma estratégia sofisticada para bloquear contas. As contas são bloqueadas com base no IP da solicitação e as senhas inseridas. A duração do bloqueio também aumenta com base na probabilidade de que seja um ataque. Depois que uma senha é tentada 10 vezes sem êxito (o limite de tentativas padrão), ocorre um bloqueio de um minuto. Na próxima vez que um logon não for bem-sucedido depois que a conta for desbloqueada (ou seja, depois que a conta for desbloqueada automaticamente pelo serviço depois que o período de bloqueio expirar), ocorrerá outro bloqueio de um minuto e continuará para cada logon malsucedido. Inserir a mesma senha repetidamente não conta como vários logons malsucedidos.

Os 10 primeiros períodos de bloqueio têm um minuto de duração. Os próximos 10 períodos de bloqueio são um pouco mais longos e aumentam a duração após cada 10 períodos de bloqueio. O contador de bloqueio é redefinido para zero após um logon bem-sucedido quando a conta não está bloqueada. Os períodos de bloqueio podem durar até cinco horas.

## <a name="manage-password-protection-settings"></a>Gerenciar configurações de proteção de senha

Para gerenciar as configurações de proteção de senha, incluindo o limite de bloqueio:

1. Inicie sessão no [portal do Azure](https://portal.azure.com)
1. Use o filtro **diretório + assinatura** no menu superior para selecionar o diretório que contém seu locatário Azure ad B2C.
1. No menu à esquerda, selecione **Azure ad B2C**. Ou então, selecione **todos os serviços** e procure e selecione **Azure ad B2C**.
1. Em **segurança**, selecione **métodos de autenticação (versão prévia)** e selecione **proteção por senha**.
1. Insira as configurações de proteção de senha desejadas e, em seguida, selecione **salvar**.

    ![Página de proteção de senha portal do Azure nas configurações do Azure AD](media/active-directory-b2c-reference-threat-management/portal-02-password-protection.png)
    <br />*Definindo o limite de bloqueio como 5 em configurações de **proteção de senha*** .

## <a name="view-locked-out-accounts"></a>Exibir contas bloqueadas

Para obter informações sobre contas bloqueadas, você pode verificar o Active Directory [relatório de atividade de entrada](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md). Em **status**, selecione **falha**. Tentativas de entrada com falha com um **código de erro de entrada** de `50053` indicar uma conta bloqueada:

![Seção do relatório de entrada do Azure AD mostrando a conta bloqueada](media/active-directory-b2c-reference-threat-management/portal-01-locked-account.png)

Para saber mais sobre como exibir o relatório de atividade de entrada no Azure Active Directory, consulte [códigos de erro de relatório de atividade de entrada](../active-directory/reports-monitoring/reference-sign-ins-error-codes.md).
