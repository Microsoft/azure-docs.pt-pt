---
title: Encontre relatórios de atividade do utilizador no portal Azure / Microsoft Docs
description: Saiba onde estão os relatórios de atividade do Diretório Ativo Azure no portal Azure.
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
ms.openlocfilehash: d356f8c10ae3770d9f4ade100ab0496ee58d772f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74008218"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Encontrar relatórios de atividade no portal do Azure

Neste artigo, aprende-se a encontrar relatórios de atividade de utilizador do Azure Ative Directory (Azure AD) no portal Azure.

## <a name="audit-logs-report"></a>Relatório de registos de auditoria

O relatório de registos de auditoria combina vários relatórios em torno das atividades de aplicação numa única perspetiva para relatórios baseados em contexto. Para aceder ao relatório de registos de auditoria:

1. Navegue para o [portal Azure.](https://portal.azure.com)
2. Selecione o seu diretório a partir do canto superior direito e, em seguida, selecione a lâmina do **Diretório Ativo Azure** a partir do painel de navegação esquerdo.
3. Selecione **registos** de auditoria da secção **atividade** da lâmina do Diretório Ativo Azure. 

    ![Registos de auditoria](./media/howto-find-activity-reports/482.png "Registos de auditoria")

O relatório de registos de auditoria consolida os seguintes relatórios:

* Relatório de auditoria
* Atividade de reposição de palavra-passe
* Atividade de redefinição de palavra-passe
* Atividade de grupos de auto-atendimento
* Alterações no nome do grupo Office365
* Atividade de prestação de contas
* Estado de capotamento de palavra-passe
* Erros de aprovisionamento de contas

### <a name="filtering-on-audit-logs"></a>Filtragem em registos de auditoria

Pode utilizar uma filtragem avançada no relatório de auditoria para aceder a uma categoria específica de dados de auditoria, especificando-os no filtro **categoria.** Por exemplo, para visualizar todas as atividades relacionadas com os utilizadores, selecione a categoria **UserManagement.** 

As categorias incluem:

- Todos
- AdministrativeUnit
- Gestão de Aplicações
- Autenticação
- Autorização
- Contacto
- Dispositivo
- DeviceConfiguration
- Gestão de Diretório
- Gestão de Direitos
- Gestão de Grupos
- Outros
- Política
- Gestão de Recursos
- Gestão de Funções
- Gestão de Utilizadores

Também pode filtrar um serviço específico utilizando o filtro de dropdown **service.** Por exemplo, para obter todos os eventos de auditoria relacionados com a gestão de passwords self-service, selecione o filtro **de Gestão de Passwords self-service.**

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

## <a name="sign-ins-report"></a>Relatório de inscrição 

A vista **Sign-ins** inclui todos os sign-ins do utilizador, bem como o relatório de utilização da **aplicação.** Também pode ver informações de utilização de aplicações na secção **Gerir** as aplicações da **Enterprise.**

Para aceder ao relatório de inscrição:

1. Navegue para o [portal Azure.](https://portal.azure.com)
2. Selecione o seu diretório a partir do canto superior direito e, em seguida, selecione a lâmina do **Diretório Ativo Azure** a partir do painel de navegação esquerdo.
3. Selecione **Signins** da secção **atividade** da lâmina de diretório Ativo Azure. 

    ![Vista de inscrição](./media/howto-find-activity-reports/483.png "Vista de inscrição")


### <a name="filtering-on-application-name"></a>Filtragem no nome da aplicação

Pode utilizar o relatório de insapara ver detalhes sobre a utilização da aplicação, filtrando o nome do utilizador ou da aplicação.

![Página de eventos de inscrição de filtro](./media/howto-find-activity-reports/07.png "Página de eventos de inscrição de filtro")

## <a name="security-reports"></a>Relatórios de segurança

### <a name="anomalous-activity-reports"></a>Relatórios de atividadeanómala

Relatórios de atividade anómalos fornecem informações sobre deteções de risco relacionadas com a segurança que a AD Azure pode detetar e reportar.

A tabela que se segue lista os relatórios de segurança anómalas da atividade da AD Azure e os correspondentes tipos de deteção de riscos no portal Azure. Para mais informações, consulte deteções de risco do [Diretório Ativo Azure.](concept-risk-events.md)  


| Relatório de atividade anómala da AD azure |  Tipo de deteção de risco de proteção de identidade|
| :--- | :--- |
| Utilizadores com fuga de credenciais | Credenciais vazadas |
| Atividades irregulares de início de sessão | Deslocação impossível para localizações atípicas |
| Inícios de sessão de dispositivos possivelmente infetados | Inícios de sessão de dispositivos infetados|
| Inícios de sessão de fontes desconhecidas | Inícios de sessão de endereços IP anónimos |
| Inícios de sessão de endereços IP com atividade suspeita | Inícios de sessão de endereços IP com atividade suspeita |
| - | Inícios de sessão de localizações desconhecidas |

Os seguintes relatórios de segurança de atividade anómala sanómalo da AD Azure não estão incluídos como deteções de risco no portal Azure:

* Inícios de sessão após várias falhas
* Inícios de sessão de várias localizações geográficas


### <a name="detected-risk-detections"></a>Deteções de risco detetadas

Pode aceder a relatórios sobre deteções de risco detetadas na secção de **Segurança** da lâmina **de Diretório Ativo Azure** no [portal Azure](https://portal.azure.com). As deteções de risco detetadas são rastreadas nos seguintes relatórios:   

- [Utilizadores em risco](concept-user-at-risk.md)
- [Inícios de sessão de risco](concept-risky-sign-ins.md)

    ![Relatórios de segurança](./media/howto-find-activity-reports/04.png "Relatórios de segurança")

## <a name="troubleshoot-issues-with-activity-reports"></a>Problemas de resolução de problemas com relatórios de atividade

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Dados em falta nos registos de atividades descarregados

#### <a name="symptoms"></a>Sintomas 

Transferi os registos de atividades (auditorias ou inícios de sessão) e não vejo todos os registos para o período de tempo que escolhi. Porquê? 

 ![Relatórios](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Causa

Ao descarregar registos de atividade no portal Azure, limitamos a escala a 250000 discos, classificados pela primeira vez mais recente. 

#### <a name="resolution"></a>Resolução

Pode tirar partido das [APIs de Relatórios do Azure AD](concept-reporting-api.md) para obter até um milhão de registos num determinado período.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Falta de dados de auditoria para ações recentes no portal Azure

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

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Registos em falta para registos recentes de entrada de utilizadores no registo de atividades de entrada de anúncios da AD Azure

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

* [Visão geral dos registos de auditoria](concept-audit-logs.md)
* [Visão geral de inscrições](concept-sign-ins.md)
* [Visão geral de eventos de risco](concept-risk-events.md)
