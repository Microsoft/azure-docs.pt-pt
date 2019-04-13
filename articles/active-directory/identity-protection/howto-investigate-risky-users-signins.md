---
title: Como investigar a utilizadores de risco e inícios de sessão na proteção de identidade do Azure Active Directory (atualizada) | Documentos da Microsoft
description: Saiba como investigar a utilizadores de risco e inícios de sessão na proteção de identidade do Azure Active Directory (atualizado).
services: active-directory
keywords: proteção de identidade do Azure Active Directory, descoberta de aplicações na cloud, gestão de aplicações, a segurança, a risco, a nível de risco, a vulnerabilidade, a política de segurança
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.author: joflore
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.date: 01/25/2019
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 646119f223067fac5ee36574fb10273819251cce
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547061"
---
# <a name="how-to-investigate-risky-users-and-sign-ins"></a>Como: Investigar utilizadores e inícios de sessão de risco 


Utilizar os relatórios de inícios de sessão de risco e utilizadores de risco pode investigar e obtenha uma perceção de risco no seu ambiente. Com a capacidade de filtrar e ordenar os inícios de sessão de risco e usuários, possa entender melhor intrusões potenciais na sua organização. 


## <a name="risky-users-report"></a>Relatório dos utilizadores de risco

Com as informações fornecidas pelo relatório de utilizadores de risco, pode encontrar respostas a perguntas como:

- Os utilizadores que são de alto risco?
- Os utilizadores que têm o estado de risco remediado?



O primeiro ponto de entrada para este relatório é o **investigar** secção na página de segurança.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/01.png)


O relatório de utilizadores de risco tem uma vista predefinida que mostra:

- Name

- Estado de risco

- Nível de risco

- Detalhe de risco

- Última atualizado de risco

- Type

- Estado
 

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/03.png)


Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/04.png)

A caixa de diálogo colunas permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

Ao clicar num item na vista de lista, obtém todos os detalhes disponíveis sobre o mesmo numa vista horizontal.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/05.png)


Os detalhes ver mostra:

- Informações básicas

- Inícios de sessão arriscados recentes

- Eventos de risco que não estão ligados a um início de sessão

- Histórico de risco



Além disso, pode:

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/08.png)

- Ver todos os inícios de sessão atalho para ver o relatório de inícios de sessão desse utilizador.

- Ver todos os risco inícios de sessão para ver todos os inícios de sessão para que o utilizador que foram sinalizados como perigoso.

- Repor palavra-passe de um usuário se considerar que a identidade do utilizador foi comprometida.

- Dispensa o risco de utilizador se considera que os eventos de risco ativo de um utilizador falsos positivos. Para obter mais informações, consulte [como melhorar a precisão de deteção](howto-improve-detection-accuracy.md).



### <a name="filter-risky-users"></a>Filtrar utilizadores de risco

Para limitar os dados comunicados para um nível que funcione para si, pode filtrar os dados de utilizador de risco através dos seguintes campos predefinidos:

- Name

- Nome de utilizador

- Estado de risco

- Nível de risco

- Type

- Estado

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/06.png)



O **nome** filtro permite-lhe especificar o nome ou o nome principal de utilizador (UPN) do utilizador que mais lhe interessa.


O **corre o risco de estado** filtro permite-lhe selecionar:

- Em risco
- Remediados
- Eliminado


O **nível de risco** filtro permite-lhe selecionar:

- Elevado
- Médio
- Baixa


O **tipo** filtro permite-lhe selecionar:

- Convidado
- Membro

O **estado** filtro permite-lhe selecionar:

- Eliminada
- Ativa


### <a name="download-risky-users-data"></a>Transferir dados de utilizadores de risco

Pode transferir os dados de utilizadores de risco, se quiser trabalhar com eles fora do portal do Azure. Clicar no Download cria um ficheiro CSV, os registos de 2.500 mais recente. 

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/07.png)


Pode personalizar a vista de lista ao clicar em colunas na barra de ferramentas.
 
Isto permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.
 
Para saber mais sobre um utilizador de risco, clique na gaveta de detalhes para expandi-lo

 



## <a name="risky-sign-ins-report"></a>Relatório de inícios de sessão de risco

Com as informações fornecidas pelo relatório de inícios de sessão de risco, pode encontrar respostas a perguntas como:

- Quantos inícios de sessão bem-sucedido estavam lá que tinha eventos de risco de endereço IP anónimos na última semana?

- Os utilizadores que foram confirmados comprometido no último mês?

- Os utilizadores que tinham risco inícios de sessão no portal do Office 365?




O primeiro ponto de entrada para este relatório é o **investigar** secção na página de segurança.

![Relatório de inícios de sessão de risco](./media/howto-investigate-risky-users-signins/02.png)

O relatório de inícios de sessão arriscados tem uma vista predefinida que mostra:

- Date

- Utilizador

- Aplicação

- Estado de início de sessão

- Estado de risco

- Nível de risco (agregar)

- Nível de risco (em tempo real)

- Acesso condicional

- MFA necessário  
 

![Relatório de inícios de sessão de risco](./media/howto-investigate-risky-users-signins/09.png)


Pode personalizar a vista de lista ao clicar em **Colunas** na barra de ferramentas.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/11.png)

A caixa de diálogo colunas permite-lhe apresentar campos adicionais ou remover campos que já são apresentados.

Ao clicar num item na vista de lista, obtém todos os detalhes disponíveis sobre o mesmo numa vista horizontal.

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/12.png)


Os detalhes ver mostra:

- Informações básicas

- Informações do dispositivo

- Informações de risco

- Informações da MFA

- Acesso condicional





Além disso, pode:

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/13.png)

- Confirmar comprometido 

- Confirmar seguro

Para obter mais informações, consulte [como melhorar a precisão de deteção](howto-improve-detection-accuracy.md).




### <a name="filter-risky-sign-ins"></a>Filtrar inícios de sessão arriscados

Para limitar os dados comunicados para um nível que funcione para si, pode filtrar os dados de utilizador de risco através dos seguintes campos predefinidos:

- Utilizador
- Aplicação
- Estado de início de sessão
- Estado de risco
- Nível de risco (agregar)
- Nível de risco (em tempo real)
- Acesso condicional
- Date
- Tipo de nível de risco

![Relatório de inícios de sessão de risco](./media/howto-investigate-risky-users-signins/14.png)



O **nome** filtro permite-lhe especificar o nome ou o nome principal de utilizador (UPN) do utilizador que mais lhe interessa.

O **aplicativo** filtro permite-lhe especificar a aplicação na cloud, o utilizador tentou aceder.

O filtro **Estado do início de sessão** permite-lhe selecionar:

- Todos
- Êxito
- Falha


O **corre o risco de estado** filtro permite-lhe selecionar:

- Em risco
- Confirmar comprometido
- Confirmado (seguro)
- Eliminado
- Remediados


O **(agregar) de nível de risco** filtro permite-lhe selecionar:

- Elevado
- Médio
- Baixa

O **(em tempo real) de nível de risco** filtro permite-lhe selecionar:

- Elevado
- Médio
- Baixa


O **acesso condicional** filtro permite-lhe selecionar:

- Todos
- Não aplicado
- Êxito
- Falha


O filtro **Data** permite-lhe definir um período de tempo para os dados devolvidos.
Os valores possíveis são:

- Último mês
- Últimos 7 dias
- Últimas 24 horas
- Intervalo de tempo personalizado





### <a name="download-risky-sign-ins-data"></a>Transferir dados de inícios de sessão arriscados

Pode transferir os dados de inícios de sessão arriscados se quiser trabalhar com eles fora do portal do Azure. Clicar no Download cria um ficheiro CSV, os registos de 2.500 mais recente. 

![Relatório dos utilizadores de risco](./media/howto-investigate-risky-users-signins/15.png)



## <a name="next-steps"></a>Passos Seguintes

Para obter uma visão geral do Azure AD Identity Protection, consulte a [descrição geral do Azure AD Identity Protection](overview-v2.md).
