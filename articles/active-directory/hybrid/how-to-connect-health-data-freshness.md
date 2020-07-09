---
title: Azure AD Connect Health - Os dados do serviço de saúde não estão atualizados / Microsoft Docs
description: Este documento descreve a causa do alerta "Os dados do serviço de saúde não estão atualizados" e como resolvê-lo.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: SamuelD
editor: ''
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 63d1d87d9b576a8e181b5b339052a6b6512f18a9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85359233"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Dados do serviço de saúde não estão atualizados alerta

## <a name="overview"></a>Descrição geral

Os agentes nas máquinas no local que o Azure AD Connect Health monitoriza periodicamente carregam dados para o Serviço de Saúde Azure AD Connect. Se o serviço não receber dados de um agente, a informação que o portal apresenta será velha. Para destacar a questão, o serviço irá elevar os dados do **serviço de Saúde não está atualizado.** Este alerta é gerado quando o serviço não recebeu dados completos nas últimas duas horas.  

- O estado de alerta de **alerta** dispara se o Serviço de Saúde tiver recebido apenas tipos **parciais** de dados enviados do servidor nas últimas duas horas. O alerta de estado de aviso não desencadeia notificações por e-mail para destinatários configurados. 
- O estado de **erro** alerta se o Serviço de Saúde não tiver recebido nenhum tipo de dados do servidor nas últimas duas horas. O alerta de estado de erro desencadeia notificações de email a destinatários configurados.

O serviço obtém os dados de agentes que estão a funcionar nas máquinas no local, dependendo do tipo de serviço. A tabela que se segue lista os agentes que funcionam na máquina, o que fazem e os tipos de dados que o serviço gera. Em alguns casos, existem vários serviços envolvidos no processo, pelo que qualquer um deles pode ser o culpado. 

## <a name="understanding-the-alert"></a>Compreender o alerta

A lâmina **Detalhes de Alerta** mostra quando o alerta ocorreu e foi detetada pela última vez. Um processo de fundo que funciona de duas em duas horas gera e reavalia o alerta. No exemplo seguinte, o alerta inicial ocorreu às 03/10 às 9h59. O alerta ainda existia no dia 03/12 às 10:00 quando o alerta foi novamente avaliado. A lâmina também detalha a hora em que o Serviço de Saúde recebeu pela última vez um determinado tipo de dados. 
 
 ![Detalhes do alerta de Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Os seguintes tipos de serviço de mapas de tabelas para os tipos de dados necessários correspondentes:

| Tipo de serviço | Agente (nome de Serviço do Windows) | Objetivo | Tipo de dados gerado  |
| --- | --- | --- | --- |  
| Azure Ad Connect (Sincronização) | Serviço de Informações do Azure AD Connect Health Sincronização | Recolher informações específicas do AAD Connect (conectores, regras de sincronização, etc.) | - Regras AadSyncService-SynchronizationRules <br />  - AadSyncService-Connectors <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Serviço de Monitorização do Azure AD Connect Health Sincronização | Recolha balcões perf específicos do AAD Connect, vestígios etw, ficheiros | Contador de desempenho |
| AD DS | Serviço de Informações do Azure AD Connect Health AD DS | Realizar testes sintéticos, recolher informações de topologia, metadados de replicação |  - Adds-TopologyInfo-Json <br /> - Common-TestData-Json (cria os resultados dos testes)   | 
|  | Serviço de Monitorização do Azure AD Connect Health AD DS | Recolha contadores perf específicos de ADDS, vestígios etw, ficheiros | - Contador de desempenho  <br /> - Common-TestData-Json (carrega os resultados dos testes)  |
| AD FS | Serviço de Diagnóstico do Azure AD Connect Health AD FS | Realizar testes sintéticos | TestResult (cria os resultados dos testes) | 
| | Serviço de Informações do Azure AD Connect Health AD FS  | Recolher métricas de utilização do ADFS | Adfs-UsageMetrics |
| | Serviço de Monitorização do Azure AD Connect Health AD FS | Recolha balcões perf específicos do ADFS, vestígios etw, ficheiros | TestResult (carrega os resultados dos testes) |

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas 

Os passos necessários para diagnosticar o problema são dados abaixo. O primeiro é um conjunto de verificações básicas que são comuns a todos os tipos de serviço. 

> [!IMPORTANT] 
> Este alerta segue a política de [retenção de dados](reference-connect-health-user-privacy.md#data-retention-policy) da Connect Health

* Certifique-se de que as versões mais recentes dos agentes estão instaladas. Ver [o histórico de lançamentos](reference-connect-health-version-history.md). 
* Certifique-se de que os serviços de agentes de saúde Azure AD Connect estão **a funcionar** na máquina. Por exemplo, a Connect Health para AD FS deve ter três serviços.
  ![Verificar o Azure AD Connect Health](./media/how-to-connect-health-agent-install/install5.png)

* Certifique-se de que vai até aqui e cumpre a [secção de requisitos.](how-to-connect-health-agent-install.md#requirements)
* Utilize [a ferramenta de conectividade de teste](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) para descobrir problemas de conectividade.
* Se tiver um HTTP Proxy, siga estes [passos de configuração](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Próximos passos
Se algum dos passos acima identificados um problema, corrija-o e aguarde que o alerta seja resolvido. O processo de fundo de alerta decorre a cada 2 horas, pelo que levará até 2 horas para resolver o alerta. 

* [Política de retenção de dados Azure AD Connect Health](reference-connect-health-user-privacy.md#data-retention-policy)
* [FAQ do Azure AD Connect Health](reference-connect-health-faq.md)
