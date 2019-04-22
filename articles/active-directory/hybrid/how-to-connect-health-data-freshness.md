---
title: O Azure AD Connect Health - dados de estado de funcionamento do serviço não estão à altura do alerta de data | Documentos da Microsoft
description: Este documento descreve a causa do alerta de "dados de estado de funcionamento do serviço não estão atualizados" e a resolução de problemas.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: zhiweiw
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3ffd783ec41b1b0c4a11ee426648c1e36fbbbf75
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58883705"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Dados de estado de funcionamento do serviço não são atualizada alerta

## <a name="overview"></a>Descrição geral

Os agentes nas máquinas no local que o Azure AD Connect Health monitorize periodicamente carregar dados para o serviço do Azure AD Connect Health. Se o serviço não receber dados de um agente, as informações que o portal apresenta será obsoletas. Para realçar o problema, o serviço irá elevar o **dados de estado de funcionamento do serviço não estão atualizados** alerta. Este alerta é gerado quando o serviço não recebeu completa de dados nas últimas duas horas.  

- O **aviso** alerta de estado é acionado se o serviço de estado de funcionamento recebeu apenas **parcial** tipos de dados enviados do servidor nas últimas duas horas. O alerta de estado de aviso não acionar notificações por e-mail aos destinatários configurados. 
- O **erro** alerta de estado é acionado se o serviço de estado de funcionamento não recebeu quaisquer tipos de dados do servidor nas últimas duas horas. Os acionadores de alerta de estado de erro notificações por correio eletrónico aos destinatários configurados.

O serviço obtém os dados de agentes que estão em execução nas máquinas no local, dependendo do tipo de serviço. A tabela seguinte lista os agentes em execução a máquina, o que fazer e os tipos de dados que o serviço gera. Em alguns casos, existem vários serviços envolvidos no processo, para que qualquer um deles pode ser o culpado. 

## <a name="understanding-the-alert"></a>Noções básicas sobre o alerta

O **detalhes do alerta** painel mostra quando o alerta ocorreu e foi detectado pela última vez. Um processo em segundo plano que executa a cada duas horas gera e reavalia o alerta. No exemplo a seguir, o alerta inicial ocorreu no 03/10 às 9h: 59. O alerta ainda existisse na 03/12 às 10h, quando o alerta foi avaliado novamente. O painel também detalha o tempo que o serviço de estado de funcionamento recebeu pela última vez um tipo de dados específico. 
 
 ![Detalhes do alerta do Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
A tabela seguinte mapeia tipos de serviço para tipos de dados necessários correspondentes:

| Tipo de serviço | Agente (nome de serviço do Windows) | Objetivo | Tipo de dados gerado  |
| --- | --- | --- | --- |  
| O Azure AD Connect (sincronização) | Serviço de Informações do Azure AD Connect Health Sincronização | Recolher informações específicas do AAD Connect (conectores, regras de sincronização, etc.) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Serviço de Monitorização do Azure AD Connect Health Sincronização | Recolher contadores de desempenho específicas do AAD Connect, rastreios ETW, ficheiros | Contador de desempenho |
| AD DS | Serviço de Informações do Azure AD Connect Health AD DS | Executar testes de sintéticas, recolher informações de topologia, metadados de replicação |  -Adiciona-TopologyInfo-Json <br /> -Comum-TestData-Json (cria os resultados do teste)   | 
|  | Serviço de Monitorização do Azure AD Connect Health AD DS | Recolher contadores de desempenho do ADDS específicos, rastreios ETW, ficheiros | -Contador de desempenho  <br /> -Comum-TestData-Json (carrega os resultados do teste)  |
| AD FS | Serviço de Diagnóstico do Azure AD Connect Health AD FS | Executar testes de sintéticos | TestResult (cria os resultados do teste) | 
| | Serviço de Informações do Azure AD Connect Health AD FS  | Recolher métricas de utilização do AD FS | Adfs-UsageMetrics |
| | Serviço de Monitorização do Azure AD Connect Health AD FS | Recolher contadores de desempenho do AD FS específicos, rastreios ETW, ficheiros | TestResult (carrega os resultados do teste) |

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas 

Os passos necessários para diagnosticar o problema é indicada abaixo. A primeira é um conjunto de verificações básicas que são comuns a todos os tipos de serviço. A tabela abaixo que lista os passos específicos para cada tipo de serviço e o tipo de dados. 

> [!IMPORTANT] 
> Este alerta segue Connect Health [política de retenção de dados](reference-connect-health-user-privacy.md#data-retention-policy)

* Certifique-se de que as versões mais recentes dos agentes são instaladas. Modo de exibição [histórico de versões](reference-connect-health-version-history.md). 
* Certifique-se de que os serviços de agentes do Azure AD Connect Health são **em execução** na máquina. Por exemplo, Connect Health para AD FS deve ter três serviços.
  ![Verificar o Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Certifique-se entrar e cumprir os [secção de requisitos](how-to-connect-health-agent-install.md#requirements).
* Uso [ferramenta de teste de conectividade](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) para detetar problemas de conectividade.
* Se tiver um Proxy de HTTP, siga estes [passos de configuração](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Passos Seguintes
Se qualquer um dos passos acima identificado um problema, corrigi-lo e aguardar que o alerta resolver. O processo de plano de fundo de alerta é executado a cada 2 horas, pelo que vai demorar até 2 horas para resolver o alerta. 

* [Política de retenção de dados do Azure AD Connect Health](reference-connect-health-user-privacy.md#data-retention-policy)
* [FAQ do Azure AD Connect Health](reference-connect-health-faq.md)
