---
title: Uso de métodos de autenticação & relatórios de insights (versão prévia) – Azure Active Directory
description: Relatórios sobre a redefinição de senha de autoatendimento do Azure AD e o uso do método de autenticação de autenticação multifator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/06/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: ddfea07989f52c463816318276fd5b6643cb2041
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255060"
---
# <a name="authentication-methods-usage--insights-preview"></a>Uso de métodos de autenticação & insights (versão prévia)

O uso & insights permite que você entenda como os métodos de autenticação para recursos como a autenticação multifator do Azure e a redefinição de senha de autoatendimento estão funcionando em sua organização. Essa funcionalidade de relatório fornece à sua organização os meios para entender quais métodos estão sendo registrados e como eles estão sendo usados.

## <a name="permissions-and-licenses"></a>Permissões e licenças

As funções a seguir podem acessar o uso e as informações:

- Administrador Global
- Leitor de segurança
- Administrador de segurança
- Leitor de relatórios

Nenhum licenciamento adicional é necessário para acessar o uso e as informações. A autenticação multifator do Azure e as informações de licenciamento de redefinição de senha de autoatendimento (SSPR) podem ser encontradas no [site de preços Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Como funciona

Para acessar o uso e as informações do método de autenticação:

1. Navegue para o [portal do Azure](https://portal.azure.com).
1. Navegue até **Azure Active Directory** > **redefinição de senha** > **uso & insights**.
1. Nas visões gerais de **registro** ou **uso** , você pode optar por abrir os relatórios previamente filtrados para filtrar com base em suas necessidades.

![Visão geral do uso & insights](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Para acessar o uso do & de informações diretamente, acesse [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade). Esse link levará você à visão geral do registro.

Os blocos usuários registrados, usuários habilitados e usuários compatíveis mostram os seguintes dados de registro para seus usuários:

- Registrado: um usuário será considerado registrado se eles (ou um administrador) tiverem registrado métodos de autenticação suficientes para atender à política de SSPR ou autenticação multifator da sua organização.
- Habilitado: um usuário será considerado habilitado se estiver no escopo da política SSPR. Se SSPR estiver habilitado para um grupo, o usuário será considerado habilitado se ele estiver nesse grupo. Se SSPR estiver habilitado para todos os usuários, todos os usuários no locatário (excluindo convidados) serão considerados habilitados.
- Compatível: um usuário é considerado compatível se eles estiverem registrados e habilitados. Esse status significa que eles podem executar SSPR a qualquer momento, se necessário.

Clicar em qualquer um desses blocos ou nas informações mostradas nele o levará a uma lista previamente filtrada de detalhes do registro.

O gráfico de **registros** na guia **registro** mostra o número de registros do método de autenticação com êxito e com falha por método de autenticação. O gráfico **redefine** a guia **uso** mostra o número de autenticações com êxito e com falha durante o método de autenticação do fluxo de redefinição de senha.

Clicar em um dos gráficos irá levá-lo para uma lista filtrada previamente de registro ou redefinição de eventos.

Usando o controle no canto superior direito, você pode alterar o intervalo de datas para os dados de auditoria mostrados nos registros e redefine os gráficos para 24 horas, 7 dias ou 30 dias.

### <a name="registration-details"></a>Detalhes do registro

Clicar nos blocos **usuários registrados**, **usuários habilitados**ou **usuários com capacidade** de inscrição levará você aos detalhes do registro.

O relatório detalhes do registro mostra as seguintes informações para cada usuário:

- Nome
- Nome de utilizador
- Status do registro (tudo, registrado, não registrado)
- Status habilitado (tudo, habilitado, não habilitado)
- Status com capacidade (tudo, capaz, sem capacidade)
- Métodos (notificação do aplicativo, código do aplicativo, chamada telefônica, SMS, email, perguntas de segurança)

Usando os controles na parte superior da lista, você pode pesquisar um usuário e filtrar a lista de usuários com base nas colunas mostradas.

### <a name="reset-details"></a>Redefinir detalhes

Clicar nos gráficos de registros ou redefinições irá levá-lo para os detalhes de redefinição.

O relatório detalhes de redefinir mostra o registro e a redefinição dos eventos dos últimos 30 dias, incluindo:

- Nome
- Nome de utilizador
- Recurso (tudo, registro, redefinição)
- Método de autenticação (notificação do aplicativo, código do aplicativo, chamada telefônica, chamada do Office, SMS, email, perguntas de segurança)
- Status (tudo, êxito, falha)

Usando os controles na parte superior da lista, você pode pesquisar um usuário e filtrar a lista de usuários com base nas colunas mostradas.

## <a name="limitations"></a>Limitações

Os dados mostrados nesses relatórios serão atrasados em até 60 minutos. Um campo "última atualização" existe no portal do Azure para identificar o quanto os dados são recentes.

Os dados de uso e insight não são uma substituição para os relatórios de atividade da autenticação multifator do Azure ou informações contidas no relatório de entradas do Azure AD.

## <a name="next-steps"></a>Passos seguintes

- [Trabalhando com a API de relatório de uso dos métodos de autenticação](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Escolhendo métodos de autenticação para sua organização](concept-authentication-methods.md)
- [Experiência de registro combinada](concept-registration-mfa-sspr-combined.md)
