---
title: Como investigar riscos no Azure Active Directory a proteção de identidade (atualizada) | Microsoft Docs
description: Saiba como investigar os usuários arriscados, as detecções e as entradas no Azure Active Directory a proteção de identidade (atualizada).
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
ms.openlocfilehash: a6e9715b63ccfbf1da3bdaedf947ea4cd65109b7
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129721"
---
# <a name="how-to-investigate-risk"></a>Como: Investigar o risco

Usando os relatórios de entradas arriscadas, usuários arriscados e detecções de risco, você pode investigar e obter informações sobre o risco em seu ambiente. Com a capacidade de filtrar e classificar as entradas arriscadas, os usuários e as detecções, você pode entender melhor a possibilidade de invasão em sua organização. 

## <a name="risky-users-report"></a>Relatório dos utilizadores de risco

Com as informações fornecidas pelo relatório de usuários arriscados, você pode encontrar respostas para perguntas como:

- Quais usuários são de alto risco?
- Quais usuários têm um estado de risco corrigido?

Seu primeiro ponto de entrada para esse relatório é a seção **investigar** na página segurança.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/01.png)

O relatório usuários arriscados tem uma exibição padrão que mostra:

- Name
- Estado de risco
- Nível de risco
- Detalhe de risco
- Última atualizado de risco
- Type
- State

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/03.png)

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/04.png)

A caixa de diálogo colunas permite exibir campos adicionais ou remover campos que já são exibidos.

Ao clicar num item na vista de lista, obtém todos os detalhes disponíveis sobre o mesmo numa vista horizontal.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/05.png)

A exibição de detalhes mostra:

- Informações básicas
- Entradas de risco recentes
- Detecções de risco não vinculadas a uma entrada
- Histórico de risco

Além disso, você pode:

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/08.png)

- Veja o atalho de todas as entradas para exibir o relatório de entradas para esse usuário.
- Exiba todas as entradas arriscadas para exibir todas as entradas desse usuário que foram sinalizadas como arriscadas.
- Redefina a senha de um usuário se você acreditar que a identidade do usuário foi comprometida.
- Ignore o risco do usuário se você considerar que as detecções de riscos ativas de um usuário são falsos positivos. Para obter mais informações, consulte o artigo [fornecer comentários sobre as detecções de risco no Azure ad Identity Protection](howto-provide-risk-event-feedback.md).

### <a name="filter-risky-users"></a>Filtrar usuários arriscados

Para restringir os dados relatados a um nível que funciona para você, você pode filtrar os dados de usuário arriscados usando os seguintes campos padrão:

- Name
- Nome de utilizador
- Estado de risco
- Nível de risco
- Type
- State

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

- Quantas entradas bem-sucedidas estavam com as detecções de risco de endereço IP anônimo na última semana?
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

Para obter mais informações, consulte o artigo [fornecer comentários sobre as detecções de risco no Azure ad Identity Protection](howto-provide-risk-event-feedback.md).

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


## <a name="risk-detections-report"></a>Relatório de detecções de risco

O relatório de detecções de risco fornece informações sobre cada detecção de risco de proteção de identidade em seu locatário.


O relatório de detecção de riscos tem uma exibição padrão que mostra:

- Date

- Utilizador

- Endereço IP

- Location

- Tipo de deteção

- Estado de risco

- Nível de risco

- ID do pedido
 

Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas. A caixa de diálogo colunas permite exibir campos adicionais ou remover campos que já são exibidos.

Ao clicar num item na vista de lista, obtém todos os detalhes disponíveis sobre o mesmo numa vista horizontal.


A exibição detalhes mostra informações adicionais sobre a detecção de riscos.

A exibição de detalhes também inclui links para

- Exibir relatório de risco do usuário
- Exibir entradas do usuário
- Exibir entradas arriscadas do usuário
- Exibir entrada arriscada vinculada
- Exibir as detecções de risco do usuário


### <a name="filter-risk-detections"></a>Filtrar detecções de riscos

Para restringir os dados relatados a um nível que funciona para você, você pode filtrar os dados de detecção de risco usando os seguintes campos padrão:

- Hora de deteção
- Tipo de deteção
- Estado de risco
- Nível de risco
- Utilizador
- Nome de utilizador
- Endereço IP
- Location
- Tempo de detecção
- Atividade 
- Source
- Tipo de emissor do token
- ID do pedido
- ID de Correlação


O filtro **tempo de detecção** permite que você defina um período para os dados retornados. Este filtro permite que você selecione:
- Últimos 90 dias
- Últimos 30 dias
- Últimos 7 dias
- Últimas 24 horas
- Intervalo de Tempo Personalizado

O filtro **tipo de detecção** permite que você especifique o tipo de detecção de risco (como propriedades de entrada desconhecidas).

O filtro de **estado de risco** permite que você selecione:

- Em risco
- Confirmar comprometido
- Confirmado (seguro)
- Eliminado
- Corrigido

O filtro de **nível de risco** permite que você selecione:

- Alta
- Médio
- Baixa

O filtro de **usuário** permite que você especifique o nome ou a ID de objeto do usuário sobre o qual você se preocupa.

O filtro de **nome** de usuário permite que você especifique o nome UPN do usuário ao qual você se preocupa.

O filtro de **tempo de detecção** permite que você especifique se a detecção estava em tempo real ou offline. Nota: Você pode desafiar entradas com detecções em tempo real usando a política de risco de entrada. 

O filtro de **atividade** permite que você especifique se a detecção foi vinculada a uma entrada (por exemplo, endereço IP anônimo) ou a um usuário (por exemplo, credenciais vazadas).

O filtro de **origem** permite que você especifique a origem da detecção de riscos (como o Azure AD ou Microsoft Cloud app Security). 

O filtro de **tipo de emissor do token** permite que você especifique onde o token foi emitido (como o Azure ad ou serviços de Federação do AD)


### <a name="download-risk-detections-data"></a>Baixar dados de detecções de risco

Você pode baixar os dados de detecções de risco se quiser trabalhar com ele fora do portal do Azure. Clicar em baixar cria um arquivo CSV dos registros 5.000 mais recentes. 

## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral do Azure AD Identity Protection, consulte a [visão geral Azure ad Identity Protection](overview-v2.md).
