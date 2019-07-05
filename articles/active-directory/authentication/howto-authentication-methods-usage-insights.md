---
title: Utilização de métodos de autenticação e informações de relatório (pré-visualização) - Azure Active Directory
description: Relatórios sobre a palavra-passe self-service do Azure AD reposição e o uso de método de autenticação do multi-factor Authentication
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
ms.openlocfilehash: a0f6a74308f1bc4a7b77576fb9f39f965de0a4f8
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561029"
---
# <a name="authentication-methods-usage--insights-preview"></a>Utilização de métodos de autenticação e insights (pré-visualização)

A utilização e insights permite-lhe compreender a forma como os métodos de autenticação para as funcionalidades, como o Azure multi-factor Authentication e reposição de palavra-passe self-service estiver a trabalhar na sua organização. Esta capacidade de relatórios oferece à sua organização com os meios para compreender quais métodos estão sendo registrados e como eles estão sendo usados.

## <a name="permissions-and-licenses"></a>Permissões e licenças

As seguintes funções podem aceder a utilização e informações:

- Administrador Global
- Leitor de segurança
- Administrador de Segurança
- Leitor de relatórios

Não existem licenças adicionais é necessário para a utilização de acesso e informações. O Azure multi-factor Authentication e informações de licenciamento de reposição (SSPR) de palavra-passe self-service podem ser encontrados no [Azure Active Directory preços site](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Como funciona

Para a utilização de método de autenticação de acesso e informações:

1. Navegue para o [portal do Azure](https://portal.azure.com).
1. Navegue até **do Azure Active Directory** > **reposição de palavra-passe** > **utilização & insights**.
1. Do **Registro** ou **utilização** visões gerais, pode optar por abrir previamente filtrado relatórios para filtrar com base nas suas necessidades.

![Descrição geral de utilização e informações](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Para aceder diretamente a utilização e informações, aceda a [ https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade ](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade). Esta ligação irá direcioná-lo para a descrição geral do registo.

Os utilizadores registados, os usuários habilitados e mosaicos de utilizadores com capacidade para mostram os dados de registo seguintes para os seus utilizadores:

- Registado: Um utilizador é considerado registado se eles (ou um administrador) registou suficiente métodos de autenticação para satisfazer a política SSPR ou multi-factor Authentication da sua organização.
- Ativado: Um utilizador é considerado ativada se eles estão no âmbito para a política da SSPR. Se SSPR está ativada para um grupo, em seguida, o utilizador é considerado ativada se eles estão nesse grupo. Se a SSPR estiver ativada para todos os utilizadores e, em seguida, todos os utilizadores no inquilino (excluindo convidados) são considerados ativada.
- Com capacidade para: Um utilizador é considerado compatível com se eles forem tanto registados e ativados. Este estado significa que realizarem a SSPR em qualquer altura se for necessário.

Clicar em qualquer um destes mosaicos ou as informações apresentadas nos mesmos é apresentada uma lista filtrada previamente de detalhes de registo.

O **registos** do gráfico no **registo** separador mostra o número de autenticação com êxito ou falhada registos de método por método de autenticação. O **repõe** do gráfico no **utilização** separador mostra o número de êxito e fluxo de reposição de autenticação falhadas durante a palavra-passe pelo método de autenticação.

Clicar em qualquer um dos gráficos irá direcioná-lo para uma lista filtrada previamente de registo ou eventos de redefinição.

Usando o controle no canto superior, direita, pode alterar o intervalo de datas para os dados de auditoria mostrados nos registos e reposições de gráficos para 24 horas, 7 dias ou 30 dias.

Dados de registo a partir do 

### <a name="registration-details"></a>Detalhes de registo

Clicar no **utilizadores registados**, **utilizadores ativados**, ou **utilizadores com capacidade** mosaicos ou informações irão direcioná-lo para os detalhes de registo.

O relatório de detalhes do registo mostra as seguintes informações para cada utilizador:

- Name
- Nome de utilizador
- Estado de registo (todas, registrado, não registada)
- Ativado o estado (todos, ativado, não ativado)
- Estado de capacidade (todos os, com capacidade, não compatível com)
- Métodos (notificação de aplicação, o código de aplicação, chamada telefónica, SMS, E-Mail, perguntas de segurança)

Usando os controles na parte superior da lista, pode pesquisar por um usuário e filtrar a lista de utilizadores com base nas colunas mostradas.

### <a name="reset-details"></a>Detalhes de reposição

Clicar em gráficos de registos ou reposições irão direcioná-lo para os detalhes de reposição.

O relatório de detalhes de reposição mostra os eventos de registro e redefinição dos últimos 30 dias incluindo:

- Name
- Nome de utilizador
- Funcionalidade (todos, registo, Redefinir)
- Método de autenticação (notificação de aplicação, o código de aplicação, chamada telefónica, chamada para o escritório, SMS, E-Mail, perguntas de segurança)
- Estado (tudo, sucesso, falha)

Usando os controles na parte superior da lista, pode pesquisar por um usuário e filtrar a lista de utilizadores com base nas colunas mostradas.

## <a name="limitations"></a>Limitações

Os dados apresentados nestes relatórios serão adiados até 60 minutos. Existe um campo de "Última atualização" no portal do Azure para identificar como recentes os seus dados estão.

A utilização e informações de dados não são um substituto para os relatórios de atividade do Azure multi-factor Authentication ou as informações contidas no relatório de inícios de sessão do Azure AD.

## <a name="next-steps"></a>Passos Seguintes

- [Trabalhar com o relatório de utilização de métodos de autenticação API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Escolher métodos de autenticação para a sua organização](concept-authentication-methods.md)
- [Combinados a experiência de registo](concept-registration-mfa-sspr-combined.md)
