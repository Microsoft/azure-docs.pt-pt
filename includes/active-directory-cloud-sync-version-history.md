---
ms.openlocfilehash: 69f0da2f1528ad1f45762a8f754cc2020b4cb880
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98901120"
---
Este artigo lista as versões e funcionalidades do Azure Ative Directory Connect Provisioning Agent que foram lançados. A equipa Azure AD atualiza regularmente o Agente de Provisionamento com novas funcionalidades e funcionalidades. O Agente Provisionante é atualizado automaticamente quando uma nova versão é lançada. 

A Microsoft fornece suporte direto para a versão mais recente do agente e uma versão anterior.

## <a name="113540"></a>1.1.354.0

20 de janeiro de 2021: Lançamento para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias
- Melhoria da experiência GMSA, incluindo suporte para conta GMSA criada pré-personalizada
- [Suporte de cmdlets PowerShell](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) para a configuração da GMSA
- [Suporte CLI](../articles/active-directory/cloud-sync/how-to-install-pshell.md) para instalação de agentes (instalação silenciosa)
- Diagnósticos adicionais para problemas de quarentena de fonte de agente
- Correções de bugs que incluem a redução do uso da memória de filtros de scoping ou, executando PHS apenas para utilizadores em âmbito, manuseamento de objetos aninhados em U quando se utilizam scoping ou etc. 


### <a name="fixed-issues"></a>Problemas corrigidos
-    Previne a quarentena quando o grupo de scoping está fora de alcance
-   quando os filtros de deteção são configurados - o trabalho phs agora só funciona para utilizadores em âmbito
-   Agente penduraria algum dia durante a atualização
-   Sincronização inicial para objetos em OUs aninhados ao utilizar a deteção de OU
-   Tornar o Repair-AADCloudSyncToolsAccount mais robusto
-   Reduzir o uso de memória grande de filtros de deteção de OU
-   A verificação de funções de administração falha se os membros da função contiverem um grupo de segurança
-   Corrija o problema de permissão da pasta GMSA que impede a renovação do Agente Cert







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>Estado de lançamento

23 de novembro de 2020: Lançado para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Suporte para [gMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts)
* Suporte para grupos até tamanho inferior a 1500 membros durante o ciclo de sincronização incremental ou delta. Isto é aplicável ao usar o filtro de deteção de grupo
* Suporte para grandes grupos com tamanho de membro até 15K
* Melhorias iniciais do sincronização
* Registo avançado de verbose
* Introdução do [módulo AADCloudSyncTools PowerShell](../articles/active-directory/cloud-sync/reference-powershell.md)
* Limitações fixas para permitir a instalação do agente em servidor não inglês
* Suporte para filtragem de PHS apenas para objetos de âmbito (Originalmente, estávamos a sincronizar hashes de palavra-passe para todos os objetos)
* Corrigiu o problema da fuga de memória no agente
* Registos de provisionamento melhorados
* Suporte para configurar o tempo limite de [ligação LDAP](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 
* Apoio à configuração da perseguição de [encaminhamento](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Estado de lançamento

4 de dezembro de 2019: Lançamento para download

### <a name="new-features-and-improvements"></a>Novas funcionalidades e melhorias

* Inclui suporte para [sincronização de nuvem Azure AD Connect](../articles/active-directory/cloud-sync/what-is-cloud-sync.md) para sincronizar dados de utilizador, contacto e grupo de diretórios no local para Azure AD


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