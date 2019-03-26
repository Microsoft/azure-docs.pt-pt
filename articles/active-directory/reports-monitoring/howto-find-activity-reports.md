---
title: Encontrar relatórios de atividade de utilizador do Azure Active Directory no portal do Azure | Documentos da Microsoft
description: Saiba onde os relatórios de atividade de utilizador do Azure Active Directory estão no portal do Azure.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d47072713c57576abe780134792c3a5cbc27127c
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58439044"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Encontrar relatórios de atividade no portal do Azure

Neste artigo, irá aprender a localizar os relatórios de atividade de utilizador do Azure Active Directory (Azure AD) no portal do Azure.

## <a name="audit-logs-report"></a>Relatório de registos de auditoria

O relatório de registos de auditoria combina vários relatórios em torno de atividades do aplicativo numa única exibição para relatórios com base no contexto. Para aceder ao relatório de registos de auditoria:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Selecione o seu diretório no canto superior direito, em seguida, selecione o **do Azure Active Directory** painel a partir do painel de navegação à esquerda.
3. Selecione **registos de auditoria** partir do **atividade** seção do painel Azure Active Directory. 

    ![Registos de auditoria](./media/howto-find-activity-reports/482.png "Registos de auditoria")

O relatório de registos de auditoria consolida os seguintes relatórios:

* Relatório de auditoria
* Atividade de reposição de palavra-passe
* Atividade de registo de reposição de palavra-passe
* Atividade de grupos self-service
* Alterações de nome de grupo do Office 365
* Atividade de aprovisionamento de contas
* Estado de rollover de palavra-passe
* Erros de aprovisionamento de contas

### <a name="filtering-on-audit-logs"></a>Filtragem em registos de auditoria

Pode utilizar a filtragem avançada no relatório de auditoria para aceder a uma determinada categoria de dados de auditoria, especificando-na **categoria** filtro. Por exemplo, para ver todas as atividades relacionadas aos utilizadores, selecione o **UserManagement** categoria. 

Categorias incluem:

- Todos
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Autorização
- Contacto
- Dispositivo
- DeviceConfiguration
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Outros
- Política
- ResourceManagement
- RoleManagement
- UserManagement

Pode também filtrar num serviço específico com o **serviço** filtro de lista pendente. Por exemplo, para obter todos os eventos de auditoria relacionados com a gestão de palavras-passe self-service, selecione o **gestão de palavras-passe Self-Service** filtro.

Os serviços incluem:

- Todos
- Revisões de Acesso
- Aprovisionamento de Contas 
- Aplicação SSO
- Métodos de Autenticação
- B2C
- Acesso Condicional
- Diretório do Núcleo
- Gestão de Direitos
- Identity Protection
- Utilizadores Convidados
- PIM
- Gestão de Grupos Personalizada
- Gestão de Palavra-passe Personalizada
- Termos de Utilização

## <a name="sign-ins-report"></a>Relatório de inícios de sessão 

O **inícios de sessão** modo de exibição inclui todos os utilizadores inícios de sessão, bem como os **utilização da aplicação** relatório. Também pode ver informações de utilização da aplicação no **gerir** secção a **aplicações empresariais** descrição geral.

Para aceder ao relatório de inícios de sessão:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Selecione o seu diretório no canto superior direito, em seguida, selecione o **do Azure Active Directory** painel a partir do painel de navegação à esquerda.
3. Selecione **Signins** partir do **atividade** seção do painel Azure Active Directory. 

    ![Vista de inícios de sessão](./media/howto-find-activity-reports/483.png "ver inícios de sessão")


### <a name="filtering-on-application-name"></a>Filtrar em nome da aplicação

Pode utilizar o relatório de inícios de sessão para ver os detalhes sobre a utilização de aplicações, ao filtrar em nome de utilizador ou o nome da aplicação.

![Página de início de sessão de eventos de filtro](./media/howto-find-activity-reports/07.png "página Eventos de início de sessão de filtragem")

## <a name="security-reports"></a>Relatórios de segurança

### <a name="anomalous-activity-reports"></a>Relatórios de atividade anómala

Relatórios de atividade anómala fornecem informações sobre eventos de risco relacionado com a segurança que o Azure AD pode detetar e comunicar em.

A seguinte tabela apresenta o Azure AD atividade anómala segurança os relatórios e tipos correspondentes de eventos de risco no portal do Azure. Para obter mais informações, consulte [Eventos de risco do Azure Active Directory](concept-risk-events.md).  


| Relatório de atividade anómala do Azure AD |  Tipo de evento de risco de proteção de identidade|
| :--- | :--- |
| Utilizadores com fuga de credenciais | Fuga de credenciais |
| Atividades irregulares de início de sessão | Deslocação impossível para localizações atípicas |
| Inícios de sessão de dispositivos possivelmente infetados | Inícios de sessão de dispositivos infetados|
| Inícios de sessão de fontes desconhecidas | Inícios de sessão de endereços IP anónimos |
| Inícios de sessão de endereços IP com atividade suspeita | Inícios de sessão de endereços IP com atividade suspeita |
| - | Inícios de sessão de localizações desconhecidas |

Relatórios de segurança seguinte para a atividade anómala do Azure AD não são incluídos como eventos de risco no portal do Azure:

* Inícios de sessão após várias falhas
* Inícios de sessão de várias localizações geográficas


### <a name="detected-risk-events"></a>Eventos de risco detetados

Pode aceder a relatórios sobre eventos de risco detetados no **segurança** secção a **Azure Active Directory** painel no [portal do Azure](https://portal.azure.com). Eventos de risco detetados são controlados nos seguintes relatórios:   

- [Utilizadores em risco](concept-user-at-risk.md)
- [Inícios de sessão de risco](concept-risky-sign-ins.md)

    ![Relatórios de segurança](./media/howto-find-activity-reports/04.png "relatórios de segurança")

## <a name="troubleshoot-issues-with-activity-reports"></a>Resolver problemas com relatórios de atividade

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Dados em falta nos registos de atividades transferidos

#### <a name="symptoms"></a>Sintomas 

Transferi os registos de atividades (auditorias ou inícios de sessão) e não vejo todos os registos para o período de tempo que escolhi. Porquê? 

 ![Relatórios](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Causa

Quando transfere registos de atividades no portal do Azure, limitamos o dimensionamento para 250000 registos, ordenados pelos mais recente primeiro. 

#### <a name="resolution"></a>Resolução

Pode tirar partido das [APIs de Relatórios do Azure AD](concept-reporting-api.md) para obter até um milhão de registos num determinado período.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Dados de auditoria para ações recentes no portal do Azure em falta

#### <a name="symptoms"></a>Sintomas

Efetuei algumas ações no portal do Azure e esperava ver os registos de auditoria dessas ações no painel `Activity logs > Audit Logs`, mas não consegui encontrá-los.

 ![Relatórios](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Causa

As ações não aparecem de imediato nos registos de atividade. A tabela abaixo enumera os nossos números de latência para registos de atividades. 

| Relatório | &nbsp; | Latência (P95) | Latência (P99) |
|--------|--------|---------------|---------------|
| Auditoria de diretórios | &nbsp; | 2 mins | 5 mins |
| Atividade de início de sessão | &nbsp; | 2 mins | 5 mins | 

#### <a name="resolution"></a>Resolução

Aguarde entre 15 minutos a duas horas e veja se as ações aparecem no registo. Se não vir os registos após duas horas, [envie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e nós analisamos o problema.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Registar registos em falta para recentes utilizador inícios de sessão na atividade de inícios de sessão do Azure AD

#### <a name="symptoms"></a>Sintomas

Iniciei sessão recentemente no portal do Azure e esperava ver os registos de início de sessão dessas ações no painel `Activity logs > Sign-ins`, mas não consigo encontrá-los.

 ![Relatórios](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Causa

As ações não aparecem de imediato nos registos de atividade. A tabela abaixo enumera os nossos números de latência para registos de atividades. 

| Relatório | &nbsp; | Latência (P95) | Latência (P99) |
|--------|--------|---------------|---------------|
| Auditoria de diretórios | &nbsp; | 2 mins | 5 mins |
| Atividade de início de sessão | &nbsp; | 2 mins | 5 mins | 

#### <a name="resolution"></a>Resolução

Aguarde entre 15 minutos a duas horas e veja se as ações aparecem no registo. Se não vir os registos após duas horas, [envie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e nós analisamos o problema.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Não consigo ver mais de 30 dias de dados de relatório no portal do Azure

#### <a name="symptoms"></a>Sintomas

Não consigo ver mais de 30 dias de dados de início de sessão e auditoria no portal do Azure. Porquê? 

 ![Relatórios](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Causa

Dependendo da sua licença, as Ações do Azure Active Directory armazena relatórios de atividades para as durações seguintes:

| Relatório           | &nbsp; |  Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Auditoria de Diretórios  | &nbsp; |   7 dias     | 30 dias             | 30 dias             |
| Atividade de Início de Sessão | &nbsp; | Não disponível. Pode aceder aos seus próprios inícios de sessão por 7 dias a partir do painel de perfil de utilizador individual | 30 dias | 30 dias             |

Para obter mais informações, veja [Políticas de retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Resolução

Tem duas opções para manter os dados durante mais de 30 dias. Pode utilizar as [APIs de Relatórios do Azure AD](concept-reporting-api.md) para recuperar os dados por meio programático e armazená-los numa base de dados. Em alternativa, pode integrar registos de auditoria num sistema SIEM de terceiros, como o Splunk ou o SumoLogic.

## <a name="next-steps"></a>Passos Seguintes

* [Descrição geral de registos de auditoria](concept-audit-logs.md)
* [Descrição geral de inícios de sessão](concept-sign-ins.md)
* [Visão geral de eventos de risco](concept-risk-events.md)
