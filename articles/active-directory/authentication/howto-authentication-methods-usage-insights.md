---
title: Métodos de autenticação utilização & insights - Diretório Ativo Azure
description: Reportagem sobre reset de senha de autosserviço da AD Azure e utilização do método de autenticação multi-factor
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1730939de399cacd13c62988259904ba84ee78ff
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654134"
---
# <a name="authentication-methods-usage--insights-preview"></a>Métodos de autenticação utilização & insights (pré-visualização)

O uso & insights permite-lhe entender como os métodos de autenticação para funcionalidades como a Autenticação Multi-Factor E o reset de palavra-passe de autosserviço estão a funcionar na sua organização. Esta capacidade de reporte fornece à sua organização os meios para entender quais os métodos que estão a ser registados e como estão a ser utilizados.

## <a name="permissions-and-licenses"></a>Permissões e licenças

As seguintes funções podem aceder ao uso e insights:

- Administrador Global
- Leitor de Segurança
- Administrador de Segurança
- Relatórios Leitor

Não é necessário um licenciamento adicional para aceder ao uso e insights. As informações de autenticação de vários fatores azure e de redefinição de senha de autosserviço (SSPR) podem ser encontradas no site de [preços do Diretório Ativo Azure](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="how-it-works"></a>Como funciona

Para aceder à utilização e insights do método de autenticação:

1. Navegue pelo [portal Azure.](https://portal.azure.com)
1. Navegue para **o Azure Ative Directory** > **Password redefinir** > **a utilização & insights**.
1. A partir das visões gerais de **Registo** ou **Utilização,** pode optar por abrir os relatórios pré-filtrados para filtrar com base nas suas necessidades.

![Visão geral do & de utilização](./media/howto-authentication-methods-usage-insights/usage-insights-overview.png)

Para aceder diretamente ao uso [https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade](https://portal.azure.com/#blade/Microsoft_AAD_IAM/AuthMethodsOverviewBlade)& insights, vá a . Este link irá levá-lo à visão geral do registo.

Os Utilizadores registados, Utilizadores habilitados e Os Utilizadores capazes de mostrar os seguintes dados de registo para os seus utilizadores:

- Registado: Um utilizador é considerado registado se (ou administrador) tiver registado métodos de autenticação suficientes para cumprir a política de Autenticação SSPR ou Multi-Factor da sua organização.
- Ativado: Um utilizador é considerado ativado se estiver em âmbito para a política sspr. Se o SSPR estiver ativado para um grupo, o utilizador é considerado ativado se estiver nesse grupo. Se o SSPR estiver ativado para todos os utilizadores, então todos os utilizadores do inquilino (excluindo os hóspedes) são considerados habilitados.
- Capaz: Um utilizador é considerado capaz se ambos estiverem registados e ativados. Este estado significa que podem realizar SSPR a qualquer momento, se necessário.

Clicar em qualquer um destes azulejos ou as informações mostradas neles irá levá-lo a uma lista pré-filtrada de detalhes de registo.

O gráfico **de registos** no separador **Registration** mostra o número de registos de métodos de autenticação bem sucedidos e falhados por método de autenticação. O gráfico **de Resets** no separador **Utilização** mostra o número de autenticações bem sucedidas e falhadas durante o fluxo de redefinição da palavra-passe por método de autenticação.

Clicar em qualquer uma das tabelas irá levá-lo a uma lista pré-filtrada de eventos de registo ou reset.

Utilizando o controlo no canto superior, direito, pode alterar a gama de datas para os dados de auditoria mostrados nos gráficos de Registos e Resets para 24 horas, 7 dias ou 30 dias.

### <a name="registration-details"></a>Detalhes da inscrição

Clicar nos **Utilizadores registados,** **Utilizadores ativados,** ou **os utilizadores capazes** de azulejos ou insights irão levá-lo aos detalhes do registo.

O relatório de dados de registo mostra as seguintes informações para cada utilizador:

- Nome
- Nome de utilizador
- Estado de registo (Todos, Registados, Não Registados)
- Estado ativado (Tudo, Ativado, Não Ativado)
- Estatuto capaz (Todos, Capazes, Não Capazes)
- Métodos (notificação de aplicativos, código de aplicação, chamada telefónica, SMS, e-mail, questões de segurança)

Utilizando os controlos no topo da lista, pode procurar um utilizador e filtrar a lista de utilizadores com base nas colunas apresentadas.

### <a name="reset-details"></a>Redefinir detalhes

Clicar nas tabelas de Registos ou Resets irá trazê-lo aos detalhes de reset.

O relatório de detalhes de reset mostra registo e reset eventos dos últimos 30 dias, incluindo:

- Nome
- Nome de utilizador
- Recurso (Tudo, Registo, Reset)
- Método de autenticação (notificação de aplicativos, código de aplicação, chamada telefónica, chamada de escritório, SMS, Email, Questões de Segurança)
- Estado (Todos, Sucesso, Fracasso)

Utilizando os controlos no topo da lista, pode procurar um utilizador e filtrar a lista de utilizadores com base nas colunas apresentadas.

## <a name="limitations"></a>Limitações

Os dados apresentados nestes relatórios serão adiados até 60 minutos. Existe um campo "Última refrescada" no portal Azure para identificar o quão recentes são os seus dados.

Os dados de utilização e insights não substituem os relatórios ou informações de autenticação de multi-factor sinuosos do Azure.

## <a name="next-steps"></a>Passos seguintes

- [Trabalhar com o relatório de utilização de métodos de autenticação API](https://docs.microsoft.com/graph/api/resources/authenticationmethods-usage-insights-overview?view=graph-rest-beta)
- [Escolher métodos de autenticação para a sua organização](concept-authentication-methods.md)
- [Experiência de registo combinado](concept-registration-mfa-sspr-combined.md)
