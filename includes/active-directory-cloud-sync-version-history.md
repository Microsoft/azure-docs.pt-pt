---
ms.openlocfilehash: 3fc2475569765116d46a175629f25d9d49634942
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993959"
---
Este artigo lista as versões e funcionalidades do Azure Ative Directory Connect Provisioning Agent que foram lançados. A equipa Azure AD atualiza regularmente o Agente de Provisionamento com novas funcionalidades e funcionalidades. O Agente Provisionante é atualizado automaticamente quando uma nova versão é lançada. 

A Microsoft fornece suporte direto para a versão mais recente do agente e uma versão anterior.

## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Estado de lançamento

23 de novembro de 2020: Lançado para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Suporte para [gMSA](../articles/active-directory/cloud-provisioning/how-to-prerequisites.md#group-managed-service-accounts)
* Suporte para grupos até tamanho inferior a 1500 membros durante o ciclo de sincronização incremental ou delta. Isto é aplicável ao usar o filtro de deteção de grupo
* Suporte para grandes grupos com tamanho de membro até 15K
* Melhorias iniciais do sincronização
* Registo avançado de verbose
* Introdução do [módulo AADCloudSyncTools PowerShell](../articles/active-directory/cloud-provisioning/reference-powershell.md)
* Limitações fixas para permitir a instalação do agente em servidor não inglês
* Suporte para filtragem de PHS apenas para objetos de âmbito (Originalmente, estávamos a sincronizar hashes de palavra-passe para todos os objetos)
* Corrigiu o problema da fuga de memória no agente
* Registos de provisionamento melhorados


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Estado de lançamento

4 de dezembro de 2019: Lançamento para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Inclui suporte para [fornecimento de nuvem Azure AD Connect](../articles/active-directory/cloud-provisioning/what-is-cloud-provisioning.md) para sincronizar dados de utilizador, contato e grupo de diretórios ativos no local para Azure AD


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Estado de lançamento

9 de setembro de 2019: Lançado para atualização automática

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Capacidade de configurar rastreios adicionais e registos para depuração de problemas de agente de provisionamento
* Capacidade de obter apenas os atributos AD Azure que estão configurados no mapeamento para melhorar o desempenho da sincronização

### <a name="fixed-issues"></a>Problemas corrigidos

* Corrigiu um bug em que o agente entrou em estado sem resposta se houvesse problemas com falhas de ligação AD Azure
* Corrigi um bug que causou problemas quando os dados binários foram lidos a partir do Azure Ative Directory
* Corrigiu um bug em que o agente não renovou a confiança com o serviço de identidade híbrida em nuvem

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Estado de lançamento

23 de janeiro de 2019: Lançamento para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Renovado o Agente de Provisionamento e a arquitetura do conector para um melhor desempenho, estabilidade e fiabilidade 
* Simplificado a configuração do Agente de Provisioning utilizando o assistente de instalação orientado para a UI 
* Apoio adicional para atualizações automáticas de agentes


