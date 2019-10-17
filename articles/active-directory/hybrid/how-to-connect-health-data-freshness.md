---
title: Azure AD Connect Health-alerta os dados do serviço de integridade não está atualizado | Microsoft Docs
description: Este documento descreve a causa do alerta "os dados do serviço de integridade não estão atualizados" e como solucioná-los.
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
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 41c1c102e88e1712d561874aef87a6f22ed250a9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430209"
---
# <a name="health-service-data-is-not-up-to-date-alert"></a>Alerta os dados do serviço de integridade não estão atualizados

## <a name="overview"></a>Visão geral

Os agentes nos computadores locais que Azure AD Connect Health monitora periodicamente carregam dados para o serviço de Azure AD Connect Health. Se o serviço não receber dados de um agente, as informações apresentadas pelo portal ficarão obsoletas. Para realçar o problema, o serviço irá gerar o alerta os **dados do serviço de integridade não está atualizado** . Esse alerta é gerado quando o serviço não recebe dados completos nas últimas duas horas.  

- O alerta de status de **aviso** será acionado se o serviço de integridade tiver recebido apenas os tipos de dados **parciais** enviados do servidor nas últimas duas horas. O alerta de status de aviso não dispara notificações por email para destinatários configurados. 
- O alerta de status de **erro** será acionado se o serviço de integridade não tiver recebido nenhum tipo de dados do servidor nas últimas duas horas. O alerta de status de erro dispara notificações por email aos destinatários configurados.

O serviço Obtém os dados de agentes que estão em execução nos computadores locais, dependendo do tipo de serviço. A tabela a seguir lista os agentes que são executados no computador, o que eles fazem e os tipos de dados que o serviço gera. Em alguns casos, há vários serviços envolvidos no processo, portanto, qualquer um deles poderia ser o culpado. 

## <a name="understanding-the-alert"></a>Noções básicas sobre o alerta

A folha **detalhes do alerta** mostra quando o alerta ocorreu e foi detectado pela última vez. Um processo em segundo plano que é executado a cada duas horas gera e reavalia o alerta. No exemplo a seguir, o alerta inicial ocorreu em 03/10 às 9:59. O alerta ainda existia em 03/12 às 10:00, quando o alerta foi avaliado novamente. A folha também detalha a hora em que o Serviço de Integridade recebeu por último um determinado tipo de dados. 
 
 ![Detalhes do alerta de Azure AD Connect Health](./media/how-to-connect-health-data-freshness/data-freshness-details.png)
 
A tabela a seguir mapeia os tipos de serviço para os tipos de dados necessários correspondentes:

| Tipo de serviço | Agente (nome do serviço do Windows) | Finalidade | Tipo de dados gerado  |
| --- | --- | --- | --- |  
| Azure AD Connect (sincronização) | Serviço de Informações do Azure AD Connect Health Sincronização | Coletar informações específicas do AAD Connect (conectores, regras de sincronização, etc.) | - AadSyncService-SynchronizationRules <br />  -AadSyncService-conectores <br /> - AadSyncService-GlobalConfigurations  <br />  - AadSyncService-RunProfileResults <br /> -AadSyncService-configurações <br /> -AadSyncService-perstatus   |
|  | Serviço de Monitorização do Azure AD Connect Health Sincronização | Coletar contadores de desempenho específicos do AAD Connect, rastreamentos de ETW, arquivos | Contador de desempenho |
| AD DS | Serviço de Informações do Azure AD Connect Health AD DS | Executar testes sintéticos, coletar informações de topologia, metadados de replicação |  -Adds-TopologyInfo-JSON <br /> -Common-TestData-JSON (cria os resultados de teste)   | 
|  | Serviço de Monitorização do Azure AD Connect Health AD DS | Coletar contadores de desempenho específicos do ADDS, rastreamentos de ETW, arquivos | -Contador de desempenho  <br /> -Common-TestData-JSON (carrega os resultados de teste)  |
| AD FS | Serviço de Diagnóstico do Azure AD Connect Health AD FS | Executar testes sintéticos | TestResult (cria os resultados de teste) | 
| | Serviço de Informações do Azure AD Connect Health AD FS  | Coletar métricas de uso do ADFS | ADFS-UsageMetrics |
| | Serviço de Monitorização do Azure AD Connect Health AD FS | Coletar contadores de desempenho específicos do ADFS, rastreamentos de ETW, arquivos | TestResult (carrega os resultados de teste) |

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas 

As etapas necessárias para diagnosticar o problema são fornecidas abaixo. A primeira é um conjunto de verificações básicas que são comuns a todos os tipos de serviço. 

> [!IMPORTANT] 
> Este alerta segue a [política de retenção de dados](reference-connect-health-user-privacy.md#data-retention-policy) do Connect Health

* Verifique se as versões mais recentes dos agentes estão instaladas. Exibir o [histórico de versões](reference-connect-health-version-history.md). 
* Verifique se os serviços do Azure AD Connect Health Agents estão **em execução** no computador. Por exemplo, o Connect Health para AD FS deve ter três serviços.
  ![Verify Azure AD Connect Health @ no__t-1

* Certifique-se de seguir e atender à [seção de requisitos](how-to-connect-health-agent-install.md#requirements).
* Use a [ferramenta de conectividade de teste](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service) para descobrir problemas de conectividade.
* Se você tiver um proxy HTTP, siga estas [etapas de configuração](how-to-connect-health-agent-install.md#configure-azure-ad-connect-health-agents-to-use-http-proxy). 


## <a name="next-steps"></a>Passos seguintes
Se qualquer uma das etapas acima identificou um problema, corrija-o e aguarde a resolução do alerta. O processo de segundo plano de alerta é executado a cada 2 horas, portanto, levará até 2 horas para resolver o alerta. 

* [Azure AD Connect Health política de retenção de dados](reference-connect-health-user-privacy.md#data-retention-policy)
* [Azure AD Connect Health FAQ (FAQ do Azure AD Connect Health)](reference-connect-health-faq.md)
