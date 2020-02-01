---
title: Azure AD Connect Health - Os dados do serviço de saúde não estão atualizados em alerta / Microsoft Docs
description: Este documento descreve a causa do alerta "Os dados do serviço de saúde não estão atualizados" e como os resolver.
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
ms.topic: conceptual
ms.date: 02/26/2018
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a94bd07cf5020981cdf028ec0eccfa8fa531d240
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76897175"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Dados do serviço de saúde não estão atualizados alerta

## <a name="overview"></a>Visão geral

Os agentes nas máquinas no local que o Azure AD Connect Health monitoriza periodicamente fazem o upload de dados para o Serviço de Saúde Azure AD Connect. Se o serviço não receber dados de um agente, a informação que o portal apresenta será estadre. Para destacar o problema, o serviço irá elevar os dados do serviço de **Saúde não está atualizado** de alerta. Este alerta é gerado quando o serviço não recebeu dados completos nas últimas duas horas.  

- O alerta de alerta de **alerta** dispara se o Serviço de Saúde tiver recebido apenas tipos de dados **parciais** enviados do servidor nas últimas duas horas. O alerta de estado de alerta não desencadeia notificações de e-mail para destinatários configurados. 
- O estado de **erro** alerta incêndios se o Serviço de Saúde não tiver recebido quaisquer tipos de dados do servidor nas últimas duas horas. O alerta de estado de erro desencadeia notificações de e-mail para destinatários configurados.

O serviço obtém os dados dos agentes que estão a funcionar nas máquinas no local, dependendo do tipo de serviço. A tabela seguinte lista os agentes que funcionam na máquina, o que fazem e os tipos de dados que o serviço gera. Em alguns casos, existem vários serviços envolvidos no processo, por isso qualquer um deles pode ser o culpado. 

## <a name="understanding-the-alert"></a>Compreender o alerta

A lâmina **de alerta detalha** quando o alerta ocorreu e foi detetada pela última vez. Um processo de fundo que funciona de duas em duas horas gera e reavalia o alerta. No exemplo seguinte, o alerta inicial ocorreu às 03/10 às 9h59. O alerta ainda existia às 03/12 às 10h00 quando o alerta foi novamente avaliado. A lâmina também detalha a hora em que o Serviço de Saúde recebeu pela última vez um determinado tipo de dados. 
 
 ![Azure AD Connect Health alerta detalhes](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
Os seguintes tipos de serviço de mapas de tabelas aos tipos de dados correspondentes:

| Tipo de serviço | Agente (nome do Serviço Windows) | Finalidade | Tipo de dados gerado  |
| --- | --- | --- | --- |  
| Ligação Azure AD (Sincronização) | Serviço de Informações do Azure AD Connect Health Sincronização | Recolher informações específicas de ligação AAD (conectores, regras de sincronização, etc.) | - AadSyncService-SynchronizationRules <br />  - AadSyncService-Conectores <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> - AadSyncService-ServiceConfigurations <br /> - AadSyncService-ServiceStatus   |
|  | Serviço de Monitorização do Azure AD Connect Health Sincronização | Recolher contadores de perf específicos de Ligação AAD, vestígios eTW, ficheiros | Contador de desempenho |
| AD DS | Serviço de Informações do Azure AD Connect Health AD DS | Realizar testes sintéticos, recolher informações de topologia, replicar metadados |  - Acrescenta-TopologyInfo-Json <br /> - Common-TestData-Json (cria os resultados dos testes)   | 
|  | Serviço de Monitorização do Azure AD Connect Health AD DS | Recolher contadores perf específicos de ADDS, vestígios eTW, ficheiros | - Contador de desempenho  <br /> - Common-TestData-Json (faz upload dos resultados dos testes)  |
| AD FS | Serviço de Diagnóstico do Azure AD Connect Health AD FS | Realizar testes sintéticos | TestResult (cria os resultados dos testes) | 
| | Serviço de Informações do Azure AD Connect Health AD FS  | Recolher métricas de utilização aDFS | Adfs-UsageMetrics |
| | Serviço de Monitorização do Azure AD Connect Health AD FS | Recolher contadores de perf específicos da ADFS, vestígios eTW, ficheiros | TestResult (carrega os resultados dos testes) |

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas 

Os passos necessários para diagnosticar a questão são dados abaixo. A primeira é um conjunto de verificações básicas que são comuns a todos os Tipos de Serviço. 

> [!IMPORTANT] 
> Este alerta segue a política de [retenção](reference-connect-health-user-privacy.md#data-retention-policy) de dados connect Health

* Certifique-se de que as versões mais recentes dos agentes estão instaladas. Ver história de [lançamento.](reference-connect-health-version-history.md) 
* Certifique-se de que os serviços de Agentes de Saúde Azure AD Connect estão **a funcionar** na máquina. Por exemplo, a Connect Health for AD FS deve ter três serviços.
  ![verificar a](./media/how-to-connect-health-agent-install/install5.png) de saúde azure AD Connect

* Certifique-se de que vai até ao encontro da [secção de requisitos](how-to-connect-health-agent-install.md#requirements).
* Utilize [a ferramenta](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) de conectividade de teste para descobrir problemas de conectividade.
* Se tiver um Http Proxy, siga estes [passos de configuração](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Passos seguintes
Se algum dos passos acima identificado um problema, corrija-o e aguarde que o alerta seja resolvido. O processo de fundo de alerta decorre a cada 2 horas, pelo que levará até 2 horas para resolver o alerta. 

* [Política de retenção de dados da Azure AD Connect Health](reference-connect-health-user-privacy.md#data-retention-policy)
* [FAQ do Azure AD Connect Health](reference-connect-health-faq.md)
