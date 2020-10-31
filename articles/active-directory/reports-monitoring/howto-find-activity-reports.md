---
title: Encontre relatórios de atividade dos utilizadores no portal Azure ! Microsoft Docs
description: Saiba onde estão no portal Azure Ative Directory os relatórios de atividade do utilizador.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42afa073da9197c12e4cbd316d311a7699d9a95f
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93131059"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Encontrar relatórios de atividade no portal do Azure

Neste artigo, aprende a encontrar relatórios de atividade do utilizador do Azure Ative Directory (Azure AD) no portal Azure.

## <a name="audit-logs-report"></a>Relatório de registos de auditoria

O relatório de registos de auditoria combina vários relatórios em torno das atividades de aplicação numa única visão para relatórios baseados em contextos. Para aceder ao relatório de registos de auditoria:

1. Navegue até ao [portal Azure.](https://portal.azure.com)
2. Selecione o seu diretório a partir do canto superior direito e, em seguida, selecione a lâmina **do Diretório Azure Ative** a partir do painel de navegação esquerdo.
3. Selecione **registos** de auditoria da secção **de Atividades** da lâmina do Diretório Ativo Azure. 

    ![Registos de auditoria](./media/howto-find-activity-reports/482.png "Registos de auditoria")

O relatório de registos de auditoria consolida os seguintes relatórios:

* Relatório de auditoria
* Atividade de reposição de palavra-passe
* Atividade de registo de redefinição de palavra-passe
* Atividade de grupos de self-service
* Alterações de nome de grupo Office365
* Atividade de provisionamento de conta
* Estado de capotamento de palavra-passe
* Erros de aprovisionamento de contas

### <a name="filtering-on-audit-logs"></a>Filtragem nos registos de auditoria

Pode utilizar a filtragem avançada no relatório de auditoria para aceder a uma categoria específica de dados de auditoria, especificando-os no filtro **categoria.** Por exemplo, para visualizar todas as atividades relacionadas com os utilizadores, selecione a categoria **UserManagement.** 

As categorias incluem:

- Todos
- AdministrativeUnit
- Gestão de Aplicações
- Autenticação
- Autorização
- Contacto
- Dispositivo
- DeviceConfiguration
- Diretório
- Gestão de Direitos
- Gestão de Grupos
- Outro
- Política
- Gestão de Recursos
- Gestão de Papéis
- Gestão de Utilizador

Também pode filtrar um serviço específico utilizando o filtro de entrega do **Serviço.** Por exemplo, para obter todos os eventos de auditoria relacionados com a gestão de passwords self-service, selecione o filtro **Self-service Password Management.**

Os serviços incluem:

- Todos
- Revisões de Acesso
- Aprovisionamento de Contas 
- SSO de aplicação
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

## <a name="sign-ins-report"></a>Relatório de inscrições 

A vista **'Iniciar sposição'** inclui todos os pedidos de informação do utilizador, bem como o relatório **de utilização da aplicação.** Também pode ver as informações de utilização da aplicação na secção **Gerir** as aplicações da **Enterprise.**

Para aceder ao relatório de inscrições:

1. Navegue até ao [portal Azure.](https://portal.azure.com)
2. Selecione o seu diretório a partir do canto superior direito e, em seguida, selecione a lâmina **do Diretório Azure Ative** a partir do painel de navegação esquerdo.
3. Selecione **Signins** da secção **de Atividades** da lâmina do Diretório Ativo Azure. 

    ![Vista de entradas](./media/howto-find-activity-reports/483.png "Vista de entradas")


### <a name="filtering-on-application-name"></a>Filtragem no nome da aplicação

Pode utilizar o relatório de insusimento para visualizar detalhes sobre a utilização da aplicação, filtrando o nome de utilizador ou o nome da aplicação.

![Filtrar Sign-In página eventos](./media/howto-find-activity-reports/07.png "Filtrar Sign-In página eventos")

## <a name="security-reports"></a>Relatórios de segurança

### <a name="anomalous-activity-reports"></a>Relatórios de atividades anómalas

Relatórios de atividade anómala fornecem informações sobre deteções de risco relacionadas com a segurança que a Azure AD pode detetar e reportar.

A tabela que se segue lista os relatórios de segurança de atividade anómalas Azure AD e os respetivos tipos de deteção de riscos no portal Azure. Para obter mais informações, consulte [as deteções de risco do Azure Ative Directory](../identity-protection/overview-identity-protection.md).  


| Relatório de atividade anómala AZure AD |  Tipo de deteção de risco de proteção de identidade|
| :--- | :--- |
| Utilizadores com fuga de credenciais | Fuga de credenciais |
| Atividades irregulares de início de sessão | Deslocação impossível para localizações atípicas |
| Inícios de sessão de dispositivos possivelmente infetados | Inícios de sessão de dispositivos infetados|
| Inícios de sessão de fontes desconhecidas | Inícios de sessão de endereços IP anónimos |
| Inícios de sessão de endereços IP com atividade suspeita | Inícios de sessão de endereços IP com atividade suspeita |
| - | Inícios de sessão de localizações desconhecidas |

Os seguintes relatórios de segurança de atividade anómala da Azure AD não são incluídos como deteções de risco no portal Azure:

* Inícios de sessão após várias falhas
* Inícios de sessão de várias localizações geográficas


### <a name="detected-risk-detections"></a>Deteção de riscos detetadas

Pode aceder a relatórios sobre deteções de risco detetadas na secção de **Segurança** da lâmina do **Diretório Ativo Azure** no [portal Azure](https://portal.azure.com). As deteções de risco detetadas são rastreadas nos seguintes relatórios:   

- [Utilizadores em risco](../identity-protection/overview-identity-protection.md)
- [Inícios de sessão de risco](../identity-protection/overview-identity-protection.md)

    ![Relatórios de segurança](./media/howto-find-activity-reports/04.png "Relatórios de segurança")

## <a name="troubleshoot-issues-with-activity-reports"></a>Problemas de resolução de problemas com relatórios de atividade

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Dados em falta nos registos de atividades descarregados

#### <a name="symptoms"></a>Sintomas 

Transferi os registos de atividades (auditorias ou inícios de sessão) e não vejo todos os registos para o período de tempo que escolhi. Porquê? 

 ![A screenshot mostra o botão descarregar no relatório de atividade.](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Causa

Quando descarrega registos de atividades no portal Azure, limitamos a escala a 250000 registos, classificados pela primeira vez mais recente. 

#### <a name="resolution"></a>Resolução

Pode tirar partido das [APIs de Relatórios do Azure AD](concept-reporting-api.md) para obter até um milhão de registos num determinado período.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Faltam dados de auditoria para ações recentes no portal Azure

#### <a name="symptoms"></a>Sintomas

Efetuei algumas ações no portal do Azure e esperava ver os registos de auditoria dessas ações no painel `Activity logs > Audit Logs`, mas não consegui encontrá-los.

 ![A imagem mostra o relatório da atividade.](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Causa

As ações não aparecem de imediato nos registos de atividade. A tabela abaixo enumera os nossos números de latência para registos de atividades. 

| Relatório | Latência (P95) | Latência (P99) |
|--------|---------------|---------------|
| Auditoria de diretórios | 2 mins | 5 mins |
| Atividade de início de sessão | 2 mins | 5 mins |

#### <a name="resolution"></a>Resolução

Aguarde entre 15 minutos a duas horas e veja se as ações aparecem no registo. Se não vir os registos após duas horas, [envie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e nós analisamos o problema.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Registos em falta para logins recentes do utilizador no registo de atividades de login AZure AD

#### <a name="symptoms"></a>Sintomas

Iniciei sessão recentemente no portal do Azure e esperava ver os registos de início de sessão dessas ações no painel `Activity logs > Sign-ins`, mas não consigo encontrá-los.

 ![A screenshot mostra inscrições para o Azure Ative Directory.](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Causa

As ações não aparecem de imediato nos registos de atividade. A tabela abaixo enumera os nossos números de latência para registos de atividades. 

| Relatório | Latência (P95) | Latência (P99) |
|--------|---------------|---------------|
| Auditoria de diretórios | 2 mins | 5 mins |
| Atividade de início de sessão | 2 mins | 5 mins |

#### <a name="resolution"></a>Resolução

Aguarde entre 15 minutos a duas horas e veja se as ações aparecem no registo. Se não vir os registos após duas horas, [envie um pedido de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e nós analisamos o problema.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Não consigo ver mais de 30 dias de dados de relatório no portal do Azure

#### <a name="symptoms"></a>Sintomas

Não consigo ver mais de 30 dias de dados de início de sessão e auditoria no portal do Azure. Porquê? 

 ![A screenshot mostra o menu Date.](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Causa

Dependendo da sua licença, as Ações do Azure Active Directory armazena relatórios de atividades para as durações seguintes:

| Relatório           | Azure AD Gratuito | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ---           | ---                 | ---
| Auditoria de Diretórios  | 7 dias        | 30 dias             | 30 dias             |
| Atividade de Início de Sessão | Não disponível. Pode aceder aos seus próprios inícios de sessão por 7 dias a partir do painel de perfil de utilizador individual | 30 dias | 30 dias             |

Para obter mais informações, veja [Políticas de retenção de relatórios do Azure Active Directory](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Resolução

Tem duas opções para manter os dados durante mais de 30 dias. Pode utilizar as [APIs de Relatórios do Azure AD](concept-reporting-api.md) para recuperar os dados por meio programático e armazená-los numa base de dados. Em alternativa, pode integrar registos de auditoria num sistema SIEM de terceiros, como o Splunk ou o SumoLogic.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral dos registos de auditoria](concept-audit-logs.md)
* [Visão geral dos insus e visão geral](concept-sign-ins.md)
* [Visão geral de eventos de risco](../identity-protection/overview-identity-protection.md)