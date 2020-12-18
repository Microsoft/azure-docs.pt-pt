---
title: Métodos de autenticação utilização & insights - Azure Ative Directory
description: Reportagem sobre o reset da palavra-passe de autosserviço AZure e a utilização do método de autenticação multi-factor
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 12/17/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfff8d450a6506cb37730838e4f70609080ca7b1
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97670796"
---
# <a name="authentication-methods-usage--insights-preview"></a>Métodos de autenticação utilização & insights (pré-visualização)

As informações de utilização & permitem-lhe compreender como os métodos de autenticação para funcionalidades como Azure AD Multi-Factor Authentication e self-service password reset estão a funcionar na sua organização. Esta capacidade de reporte fornece à sua organização os meios para entender que métodos estão a ser registados e como estão a ser utilizados.

## <a name="permissions-and-licenses"></a>Permissões e licenças

As seguintes funções podem aceder ao uso e insights:

- Administrador Global
- Leitor de Segurança
- Administrador de Segurança
- Leitor de Relatórios

 Uma licença Azure AD Premium P1 ou P2 é necessária para aceder ao uso e insights. As informações de autenticação multi-factor AD E autosserviço de autosserviço podem ser encontradas no site de preços do [Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Como funciona

Para aceder à utilização e insights do método de autenticação:

1. Navegue pelo [portal Azure](https://portal.azure.com).
1. Navegue para **Azure Ative Directory**  >  **Password redefinir** o uso &  >  **insights**.
1. A partir das vistas gerais **de Registo** ou **Utilização,** pode optar por abrir os relatórios pré-filtrados para filtrar com base nas suas necessidades.

![Visão geral do & de utilização](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Para aceder diretamente à utilização & insights, vá a [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade) . Este link irá levá-lo à visão geral do registo.

Os Utilizadores registados, utilizadores habilitados e azulejos capazes de Utilizadores mostram os seguintes dados de registo para os seus utilizadores:

- Registado: Um utilizador é considerado registado se (ou um administrador) tiver registado métodos de autenticação suficientes para cumprir a política de autenticação SSPR ou Multi-Factor da sua organização.
- Ativado: Considera-se que um utilizador está ativado se estiver no âmbito da política SSPR. Se o SSPR estiver ativado para um grupo, então o utilizador é considerado ativado se estiver nesse grupo. Se a SSPR estiver ativada para todos os utilizadores, todos os utilizadores do arrendatário (excluindo os hóspedes) são considerados habilitados.
- Capaz: Um utilizador é considerado capaz se ambos estiverem registados e habilitados. Este estado significa que podem realizar SSPR a qualquer momento, se necessário.

Clicar em qualquer um destes azulejos ou os insights neles mostrados irá levá-lo a uma lista pré-filtrada de detalhes de registo.

O gráfico **de registos** no separador **Registo** mostra o número de registos de métodos de autenticação bem sucedidos e falhados por método de autenticação. O gráfico **'Resets'** no separador **Utilização** mostra o número de autenticações bem sucedidas e falhadas durante o fluxo de reset da palavra-passe por método de autenticação.

Clicar em qualquer um dos gráficos irá levá-lo a uma lista pré-filtrada de eventos de registo ou reset.

Utilizando o controlo no canto superior direito, pode alterar o intervalo de datas para os dados de auditoria indicados nos gráficos de Registos e Resets para 24 horas, 7 dias ou 30 dias.

### <a name="registration-details"></a>Detalhes de inscrição

Clicando nos **Utilizadores registados,** **os Utilizadores ativados,** ou **os Utilizadores capazes** de azulejos ou insights irão levá-lo aos detalhes do registo.

O relatório de dados de registo mostra as seguintes informações para cada utilizador:

- Name
- Nome de utilizador
- Estado de registo (Todos, Registados, Não Registados)
- Estado ativado (Tudo, Ativado, Não Ativado)
- Estado capaz (Tudo, Capaz, Não Capaz)
- Métodos (notificação de aplicativo, código de aplicação, chamada telefónica, SMS, Email, Questões de Segurança)

Utilizando os controlos no topo da lista, pode procurar um utilizador e filtrar a lista de utilizadores com base nas colunas mostradas.

### <a name="reset-details"></a>Redefinir detalhes

Clicar nos gráficos de Registos ou Resets irá levá-lo aos detalhes do reset.

O relatório de detalhes de reset mostra eventos de registo e reposição dos últimos 30 dias, incluindo:

- Name
- Nome de utilizador
- Recurso (Todos, Registo, Reset)
- Método de autenticação (notificação de aplicação, código de aplicação, chamada telefónica, chamada do Office, SMS, Email, Questões de Segurança)
- Estado (Tudo, Sucesso, Fracasso)

Utilizando os controlos no topo da lista, pode procurar um utilizador e filtrar a lista de utilizadores com base nas colunas mostradas.

## <a name="limitations"></a>Limitações

Os dados apresentados nestes relatórios serão adiados até 60 minutos. Existe um campo "Última atualização" no portal Azure para identificar a recente data dos seus dados.

Os dados de utilização e insights não substituem os relatórios de atividade de autenticação multi-factor Azure AD ou informações contidas no relatório de inscrições Azure AD.

O relatório não pode ser atualmente filtrado para excluir utilizadores externos.

## <a name="next-steps"></a>Passos seguintes

- [Trabalhar com os métodos de autenticação relatório de utilização API](/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Escolher métodos de autenticação para a sua organização](concept-authentication-methods.md)
- [Experiência de registo combinado](concept-registration-mfa-sspr-combined.md)