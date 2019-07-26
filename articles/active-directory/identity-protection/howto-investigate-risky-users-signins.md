---
title: Como investigar usuários arriscados e entradas no Azure Active Directory a proteção de identidade (atualizada) | Microsoft Docs
description: Saiba como investigar usuários e entradas arriscadas no Azure Active Directory Identity Protection (atualizado).
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c89658e962654f005eaee5ceff220d5fb343e86e
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68370319"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>Como: Investigar utilizadores e inícios de sessão de risco 

Usando os relatórios de entradas arriscadas e usuários arriscados, você pode investigar e obter informações sobre riscos em seu ambiente. Com a capacidade de filtrar e classificar as entradas e os usuários arriscados, você pode entender melhor a possibilidade de invasão em sua organização. 

## <a name="risky-users-report"></a>Relatório dos utilizadores de risco

Com as informações fornecidas pelo relatório de usuários arriscados, você pode encontrar respostas para perguntas como:

- Quais usuários são de alto risco?
- Quais usuários têm um estado de risco corrigido?

Seu primeiro ponto de entrada para esse relatório é a seção **investigar** na página segurança.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/01.png)

O relatório usuários arriscados tem uma exibição padrão que mostra:

- Nome
- Estado de risco
- Nível de risco
- Detalhe de risco
- Última atualizado de risco
- Type
- Estado

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/03.png)

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/04.png)

A caixa de diálogo colunas permite exibir campos adicionais ou remover campos que já são exibidos.

Ao clicar num item na vista de lista, obtém todos os detalhes disponíveis sobre o mesmo numa vista horizontal.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/05.png)

A exibição de detalhes mostra:

- Informações básicas
- Inícios de sessão arriscados recentes
- Eventos de risco que não estão ligados a um início de sessão
- Histórico de risco

Além disso, você pode:

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/08.png)

- Veja o atalho de todas as entradas para exibir o relatório de entradas para esse usuário.
- Exiba todas as entradas arriscadas para exibir todas as entradas desse usuário que foram sinalizadas como arriscadas.
- Redefina a senha de um usuário se você acreditar que a identidade do usuário foi comprometida.
- Ignore o risco do usuário se você pensar que os eventos de risco ativos de um usuário são falsos positivos. Para obter mais informações, consulte o artigo [fornecer comentários sobre eventos de risco em Azure ad Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-users"></a>Filtrar usuários arriscados

Para restringir os dados relatados a um nível que funciona para você, você pode filtrar os dados de usuário arriscados usando os seguintes campos padrão:

- Nome
- Nome de utilizador
- Estado de risco
- Nível de risco
- Type
- Estado

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/06.png)

O filtro de **nome** permite que você especifique o nome ou o UPN (nome principal do usuário) do usuário ao qual você se preocupa.

O filtro de **estado de risco** permite que você selecione:

- Em risco
- Corrigido
- Eliminado

O filtro de **nível de risco** permite que você selecione:

- Alta
- Médio
- Baixa

O filtro **tipo** permite que você selecione:

- Convidado
- Membro

O filtro de **status** permite que você selecione:

- Eliminado
- Active

### <a name="download-risky-users-data"></a>Baixar dados de usuários arriscados

Você pode baixar os dados de usuários arriscados se quiser trabalhar com eles fora do portal do Azure. Clicar em baixar cria um arquivo CSV dos registros 2.500 mais recentes. 

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/07.png)

Você pode personalizar o modo de exibição de lista clicando em colunas na barra de ferramentas.
 
Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.
 
Para saber mais sobre um usuário arriscado, clique na gaveta de detalhes para expandi-lo

## <a name="risky-sign-ins-report"></a>Relatório de inícios de sessão de risco

Com as informações fornecidas pelo relatório de entradas arriscadas, você pode encontrar respostas para perguntas como:

- Quantas entradas bem-sucedidas estavam com eventos de risco de endereço IP anônimo na última semana?
- Quais usuários foram confirmados comprometidos no último mês?
- Quais usuários tinham entradas arriscadas no portal do Office 365?

Seu primeiro ponto de entrada para esse relatório é a seção **investigar** na página segurança.

![Relatório de inícios de sessão de risco](./media/howto-investigate-risky-users-signins/02.png)

O relatório de entradas arriscadas tem uma exibição padrão que mostra:

- Date
- Utilizador
- Aplicação
- Estado de início de sessão
- Estado de risco
- Nível de risco (agregação)
- Nível de risco (em tempo real)
- Acesso Condicional
- MFA necessária  

![Relatório de inícios de sessão de risco](./media/howto-investigate-risky-users-signins/09.png)

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/11.png)

A caixa de diálogo colunas permite exibir campos adicionais ou remover campos que já são exibidos.

Ao clicar num item na vista de lista, obtém todos os detalhes disponíveis sobre o mesmo numa vista horizontal.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/12.png)

A exibição de detalhes mostra:

- Informações básicas
- Informações do dispositivo
- Informações de risco
- Informações da MFA
- Acesso Condicional

Além disso, você pode:

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/13.png)

- Confirmar comprometido 
- Confirmar seguro

Para obter mais informações, consulte o artigo [fornecer comentários sobre eventos de risco em Azure ad Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-sign-ins"></a>Filtrar entradas arriscadas

Para restringir os dados relatados a um nível que funciona para você, você pode filtrar os dados de usuário arriscados usando os seguintes campos padrão:

- Utilizador
- Aplicação
- Estado de início de sessão
- Estado de risco
- Nível de risco (agregação)
- Nível de risco (em tempo real)
- Acesso Condicional
- Date
- Tipo de nível de risco

![Relatório de inícios de sessão de risco](./media/howto-investigate-risky-users-signins/14.png)

O filtro de **nome** permite que você especifique o nome ou o UPN (nome principal do usuário) do usuário ao qual você se preocupa.

O filtro de **aplicativo** permite que você especifique o aplicativo de nuvem que o usuário tentou acessar.

O filtro **Estado do início de sessão** permite-lhe selecionar:

- Todos
- Êxito
- Falha

O filtro de **estado de risco** permite que você selecione:

- Em risco
- Confirmar comprometido
- Confirmado (seguro)
- Eliminado
- Corrigido

O filtro de **nível de risco (agregação)** permite que você selecione:

- Alta
- Médio
- Baixa

O filtro de **nível de risco (em tempo real)** permite que você selecione:

- Alta
- Médio
- Baixa

O filtro de **acesso condicional** permite que você selecione:

- Todos
- Não aplicado
- Êxito
- Falha

O filtro **Data** permite-lhe definir um período de tempo para os dados devolvidos.
Os valores possíveis são:

- Último mês
- Últimos 7 dias
- Últimas 24 horas
- Intervalo de Tempo Personalizado

### <a name="download-risky-sign-ins-data"></a>Baixar dados de entradas arriscadas

Você pode baixar os dados de entradas arriscadas se quiser trabalhar com eles fora do portal do Azure. Clicar em baixar cria um arquivo CSV dos registros 2.500 mais recentes. 

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/15.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral do Azure AD Identity Protection, consulte a [visão geral Azure ad Identity Protection](overview-v2.md).
