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
ms.openlocfilehash: 16794dfdcdc6ed9c2effe412237d2681fca4f394
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803299"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Dados de estado de funcionamento do serviço não são atualizada alerta

## <a name="overview"></a>Descrição geral
Os agentes nas máquinas no local que o Azure AD Connect Health monitorize periodicamente carrega dados para o serviço do Azure AD Connect Health. Se o serviço não receber dados de um agente, as informações apresentadas no portal será obsoletas. Para realçar o problema, o serviço irá gerar **dados de estado de funcionamento do serviço não estão atualizados** alerta. Este é gerado quando o serviço não recebeu dados concluídos nas últimas duas horas.  

* O **aviso** alerta de estado é acionado se o serviço de estado de funcionamento recebeu apenas **parcial** tipos de dados enviados do servidor nas últimas duas horas. Alerta de estado de aviso não acionar notificações por e-mail aos destinatários configurados. 
* O **erro** alerta de estado é acionado se o serviço de estado de funcionamento não recebeu quaisquer tipos de dados do servidor nas últimas duas horas. Acionadores de alerta de estado de erro notificações por correio eletrónico aos destinatários configurados.

O serviço obtém os dados de agentes que estão em execução nas máquinas no local. Dependendo do tipo de serviço, a tabela seguinte lista os agentes que são executados na máquina, o que fazem, bem como os tipos de dados que são gerados pelo serviço. Em alguns casos, há vários serviços envolvidos no processo, por isso, um deles podem ser o culpado. 

## <a name="understanding-the-alert"></a>Noções básicas sobre o alerta
O painel de detalhes do alerta indica a hora quando o alerta é gerado e última deteção. O alerta é gerado/voltar-evaluated por um processo em segundo plano, que é executado a cada duas horas. No exemplo abaixo, foi gerado o alerta inicial em 03/10 às 9h: 59. Ele continua a existir até mesmo em 03/12 10:00 quando o alerta foi avaliado novamente.
O painel também fornece detalhes sobre a hora quando um determinado tipo de dados pela última vez foi recebido pelo serviço de integridade. 
 
 ![Detalhes do alerta do Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Segue-se o mapa de tipos de serviço e tipo de dados necessários correspondente.

| Tipo de Serviço | Agente (nome de serviço do Windows) | Objetivo | Tipo de dados gerado  |
| --- | --- | --- | --- |  
| O Azure AD Connect (sincronização) | Serviço de Informações do Azure AD Connect Health Sincronização | Recolher informações específicas AAD Connect (conectores, sincronização de regras etc.) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Serviço de Monitorização do Azure AD Connect Health Sincronização | Recolher ficheiros de contadores de desempenho (o AAD Connect específico), rastreios ETW, | Contador de Desempenho |
| AD DS | Serviço de Informações do Azure AD Connect Health AD DS | Executar testes de sintéticas, recolher informações de topologia, metadados de replicação |  -Adiciona-TopologyInfo-Json <br /> -Comum-TestData-Json (cria os resultados do teste)   | 
|  | Serviço de Monitorização do Azure AD Connect Health AD DS | Recolher ficheiros de contadores, rastreios ETW, de desempenho (ADDS específicos) | -Contador de desempenho  <br /> -Comum-TestData-Json (carrega os resultados do teste)  |
| AD FS | Serviço de Diagnóstico do Azure AD Connect Health AD FS | Executar testes de sintéticos | TestResult (cria os resultados do teste) | 
| | Serviço de Informações do Azure AD Connect Health AD FS  | Recolher métricas de utilização do AD FS | Adfs-UsageMetrics |
| | Serviço de Monitorização do Azure AD Connect Health AD FS | Recolher ficheiros de contadores, rastreios ETW, de desempenho (específicos do AD FS) | TestResult (carrega os resultados do teste) |

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
