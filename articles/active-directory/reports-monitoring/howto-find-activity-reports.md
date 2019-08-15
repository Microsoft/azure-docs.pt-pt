---
title: Localizar Azure Active Directory relatórios de atividade do usuário no portal do Azure | Microsoft Docs
description: Saiba onde os relatórios de atividade de usuário do Azure Active Directory estão no portal do Azure.
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 356412bfca19e72d09338faf8c8bcac5ec8f273a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68988291"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Localizar relatórios de atividade no portal do Azure

Neste artigo, você aprenderá a localizar os relatórios de atividade de usuário do Azure Active Directory (Azure AD) no portal do Azure.

## <a name="audit-logs-report"></a>Relatório de logs de auditoria

O relatório de logs de auditoria combina vários relatórios sobre as atividades do aplicativo em uma única exibição para relatórios baseados em contexto. Para acessar o relatório de logs de auditoria:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Selecione o diretório no canto superior direito e, em seguida, selecione a folha **Azure Active Directory** no painel de navegação à esquerda.
3. Selecione **logs de auditoria** na seção **atividade** da folha Azure Active Directory. 

    ![Registos de auditoria](./media/howto-find-activity-reports/482.png "Registos de auditoria")

O relatório de logs de auditoria consolida os seguintes relatórios:

* Relatório de auditoria
* Atividade de reposição de palavra-passe
* Atividade de registro de redefinição de senha
* Atividade de grupos de autoatendimento
* Alterações no nome do grupo do Office365
* Atividade de provisionamento de conta
* Status de substituição de senha
* Erros de aprovisionamento de contas

### <a name="filtering-on-audit-logs"></a>Filtrando logs de auditoria

Você pode usar a filtragem avançada no relatório de auditoria para acessar uma categoria específica de dados de auditoria, especificando-a no filtro de **categoria** . Por exemplo, para exibir todas as atividades relacionadas a usuários, selecione a categoria usermanagement. 

As categorias incluem:

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

Você também pode filtrar em um serviço específico usando o filtro suspenso de **serviço** . Por exemplo, para obter todos os eventos de auditoria relacionados ao gerenciamento de senhas de autoatendimento, selecione o filtro de **Gerenciamento de senhas** de autoatendimento.

Os serviços incluem:

- Todos
- Revisões de Acesso
- Aprovisionamento de Contas 
- SSO de aplicativo
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

## <a name="sign-ins-report"></a>Relatório de entradas 

A exibição de **entradas** inclui todas as entradas de usuário, bem como o relatório de **uso do aplicativo** . Você também pode exibir informações de uso do aplicativo na seção **gerenciar** da visão geral dos **aplicativos empresariais** .

Para acessar o relatório de entradas:

1. Navegue para o [portal do Azure](https://portal.azure.com).
2. Selecione o diretório no canto superior direito e, em seguida, selecione a folha **Azure Active Directory** no painel de navegação à esquerda.
3. Selecione **entrada** na seção **atividade** da folha Azure Active Directory. 

    ![Exibição de entradas](./media/howto-find-activity-reports/483.png "Exibição de entradas")


### <a name="filtering-on-application-name"></a>Filtrando o nome do aplicativo

Você pode usar o relatório de entradas para exibir detalhes sobre o uso do aplicativo, filtrando o nome de usuário ou o nome do aplicativo.

![Página filtrar eventos de entrada](./media/howto-find-activity-reports/07.png "Página filtrar eventos de entrada")

## <a name="security-reports"></a>Relatórios de segurança

### <a name="anomalous-activity-reports"></a>Relatórios de atividade anômala

Os relatórios de atividade anômala fornecem informações sobre eventos de risco relacionados à segurança que o Azure AD pode detectar e relatar.

A tabela a seguir lista os relatórios de segurança da atividade anômala do Azure AD e os tipos de evento de risco correspondentes no portal do Azure. Para obter mais informações, consulte [Eventos de risco do Azure Active Directory](concept-risk-events.md).  


| Relatório de atividade anômala do Azure AD |  Tipo de evento de risco de proteção de identidade|
| :--- | :--- |
| Utilizadores com fuga de credenciais | Fuga de credenciais |
| Atividades irregulares de início de sessão | Deslocação impossível para localizações atípicas |
| Inícios de sessão de dispositivos possivelmente infetados | Inícios de sessão de dispositivos infetados|
| Inícios de sessão de fontes desconhecidas | Inícios de sessão de endereços IP anónimos |
| Inícios de sessão de endereços IP com atividade suspeita | Inícios de sessão de endereços IP com atividade suspeita |
| - | Inícios de sessão de localizações desconhecidas |

Os seguintes relatórios de segurança de atividade anômala do Azure AD não são incluídos como eventos de risco no portal do Azure:

* Inícios de sessão após várias falhas
* Inícios de sessão de várias localizações geográficas


### <a name="detected-risk-events"></a>Eventos de risco detectados

Você pode acessar relatórios sobre eventos de risco detectados na seção **segurança** da folha **Azure Active Directory** na [portal do Azure](https://portal.azure.com). Os eventos de risco detectados são acompanhados nos seguintes relatórios:   

- [Utilizadores em risco](concept-user-at-risk.md)
- [Inícios de sessão de risco](concept-risky-sign-ins.md)

    ![Relatórios de segurança](./media/howto-find-activity-reports/04.png "Relatórios de segurança")

## <a name="troubleshoot-issues-with-activity-reports"></a>Solucionar problemas com relatórios de atividade

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Dados ausentes nos logs de atividades baixados

#### <a name="symptoms"></a>Sintomas 

Transferi os registos de atividades (auditorias ou inícios de sessão) e não vejo todos os registos para o período de tempo que escolhi. Porquê? 

 ![Relatórios](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Causa

Quando você baixa os logs de atividade no portal do Azure, limitamos a escala a 250000 registros, classificados por mais recentes primeiro. 

#### <a name="resolution"></a>Resolução

Pode tirar partido das [APIs de Relatórios do Azure AD](concept-reporting-api.md) para obter até um milhão de registos num determinado período.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Dados de auditoria ausentes para ações recentes no portal do Azure

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

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Logs ausentes para entradas recentes de usuário no log de atividades de entradas do Azure AD

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

## <a name="next-steps"></a>Passos seguintes

* [Visão geral dos logs de auditoria](concept-audit-logs.md)
* [Visão geral de entradas](concept-sign-ins.md)
* [Visão geral dos eventos arriscados](concept-risk-events.md)
