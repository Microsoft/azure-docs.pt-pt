---
title: Azure AD Connect códigos e descrições de erro sincronizados em nuvem
description: artigo de referência para códigos de erro sincronizados em nuvem
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22e677e5a86f60627552161f7b2115c08695dbf0
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98613527"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Azure AD Connect códigos e descrições de erro sincronizados em nuvem
Segue-se uma lista de códigos de erro e a sua descrição


## <a name="error-codes"></a>Códigos de erro

|Código de erro|Detalhes|Cenário|Resolução|
|-----|-----|-----|-----|
|TimeOut|Error Message: Detetámos um erro de tempo limite de pedido ao contactar o agente no local e sincronizar a sua configuração. Para questões adicionais relacionadas com o seu agente de sincronização de nuvem, consulte a nossa orientação de resolução de problemas.|Pedido ao HIS cronometrado. O valor atual do tempo limite é de 10 minutos.|Consulte a nossa [orientação de resolução de problemas](how-to-troubleshoot.md)|
|HybridSynchronizationActiveDirectoryInternalServerError|Error Message: Não conseguimos processar este pedido neste momento. Se esta emissão persistir, contacte o suporte e forneça o seguinte identificador de trabalho: AD2AADProvisioning.30b500eaf9c643b2b78804e80c1421fe.5c291d3c-d29f-4570-9d6b-f0c2fa3d5926. Detalhes adicionais: O processamento do pedido HTTP resultou numa exceção. |Não foi possível processar os parâmetros recebidos no pedido do SCIM a um pedido de Pesquisa.|Consulte a resposta HTTP devolvida pela propriedade 'Resposta' desta exceção para mais detalhes.|
|HybridIdentityServiceNoAgentsAssigned|Error Message: Somos incapazes de encontrar um agente ativo para o domínio que está a tentar sincronizar. Por favor, verifique se os agentes foram removidos. Em caso afirmativo, volte a instalar o agente.|Não há agentes a fugir. Provavelmente os agentes foram removidos. Registe um novo agente.|"Neste caso, não verá nenhum agente atribuído ao domínio no portal.|
|HybridIdentityServiceNoActiveAgents|Error Message: Somos incapazes de encontrar um agente ativo para o domínio que está a tentar sincronizar. Por favor, verifique se o agente está a funcionar indo para o servidor, onde o agente está instalado, e verifique se "Microsoft Azure AD Cloud Sync Agent" em Serviços está em execução.|"Os agentes não estão a ouvir o ponto final do ServiceBus. [O agente está por trás de uma firewall que não permite ligações ao autocarro de serviço](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|HybridIdentityServiceInvalidResource|Error Message: Não conseguimos processar este pedido neste momento. Se esta emissão persistir, contacte o suporte e forneça o seguinte identificador de trabalho: AD2AADProvisioning.3a2a0d8418f34f54a03da5b70b1f7b0c0c.d583090-9cd3-4d0a-aee6-8d66658c3e9. Detalhes adicionais: Parece haver um problema com a configuração da sincronização da nuvem. Por favor, registe novamente o seu agente de sincronização de nuvem no seu domínio AD on-prem e reinicie a configuração a partir do Portal Azure.|O nome do recurso deve ser definido para que a HIS saiba qual o agente a contactar.|Por favor, registe novamente o seu agente de sincronização de nuvem no seu domínio AD on-prem e reinicie a configuração a partir do Portal Azure.|
|HybridIdentityServiceAgentSignalingError|Error Message: Não conseguimos processar este pedido neste momento. Se esta emissão persistir, contacte o suporte e forneça o seguinte identificador de trabalho: AD2AADProvisioning.92d2e8750f37407fa2301c9e52ad7e9b.efb835ef-62e8-42e3-b495-18d5272eb3f9. Detalhes adicionais: Não fomos capazes de processar este pedido neste momento. Se este problema persistir, contacte o suporte com o Job ID (a partir do painel de estado da sua configuração).|A Service Bus não pode enviar uma mensagem ao agente. Pode ser uma falha no autocarro de serviço, ou o agente não responde.|Se este problema persistir, contacte o suporte com o Job ID (a partir do painel de estado da sua configuração).|
|AzureDirectoryServiceServerBusy|Error Message: Ocorreu um erro. Código de erro: 81. Descrição do erro: O Azure Ative Directory está atualmente ocupado. Esta operação será novamente julgada. Se este problema persistir por mais de 24 horas, contacte o Suporte Técnico. Identificação de rastreio: 8a4ab3b5-3664-4278-ab64-9cff37f3f4f4f Nome do servidor:|O Azure Ative Directory está ocupado.|Se este problema persistir por mais de 24 horas, contacte o Suporte Técnico.|
|AzureActiveDirectoryInvalidCredential|Error Message: Encontramos um problema com a conta de serviço que é usada para executar Azure AD Connect Cloud Sync. Pode reparar a conta de serviço na nuvem seguindo as instruções [aqui.](https://go.microsoft.com/fwlink/?linkid=2150988) Se o erro persistir, contacte o suporte com o ID do trabalho (a partir do painel de estado da sua configuração). Detalhes adicionais de erro: CredenciaisInvalid AADSTS50034: A conta de utilizador {EmailHidden} não existe no diretório skydrive365.onmicrosoft.com. Para assinar este pedido, a conta deve ser adicionada ao diretório. ID de vestígio: 14b63033-3bc9-4bd4-b871-5eb4b350020 Correlação ID: 57d93ed1-be4d-483c-997c-a3b6f03deb00 Timestamp: 2021-01-12 21:08:29Z |Este erro é lançado quando a conta de serviço de sincronização ADToAADSyncServiceAccount não existe no arrendatário. Pode ser devido à supressão acidental da conta.|Utilize [o reparo-AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) para corrigir a conta de serviço.|
|AzureActiveDirectoryExpiredCredentis|Error Message: Não conseguimos processar este pedido neste momento. Se este problema persistir, contacte o suporte com o Job ID (a partir do painel de estado da sua configuração). Detalhes adicionais de erro: CredenciaisExpirado AADSTS50055: A palavra-passe expirou. Identificação de vestígios: 989b1841-dbe5-49c9-ab6c-9aa25f7b0e00 Correlation ID: 1c69b196-1c3a-4381-9187-c84747807155 Timestamp: 2021-01-12 20:59:31Z | O código de estado de resposta não indica sucesso: 401 (Não autorizado).|As credenciais de conta de serviço AAD Sync estão caducadas.|Pode reparar a conta de serviço na nuvem seguindo as instruções em https://go.microsoft.com/fwlink/?linkid=2150988 . Se o erro persistir, contacte o suporte com o ID do trabalho (a partir do painel de estado da sua configuração).  Detalhes adicionais de erro: As suas credenciais de inquilino administrativo Azure Ative Directory foram trocadas por um token OAuth que já expirou."|
|AzureActiveDirectoryAuthenticationFailed|Error Message: Não conseguimos processar este pedido neste momento. Se esta emissão persistir, contacte o suporte e forneça o seguinte identificador de trabalho: AD2AADProvisioning.60b943e88f234db2b887f8cb91e87c.707be0d2-c6a9-405d-a3b9-de87761dc3ac. Detalhes adicionais: Não fomos capazes de processar este pedido neste momento. Se este problema persistir, contacte o suporte com o Job ID (a partir do painel de estado da sua configuração). Detalhes adicionais de erro: UnexpectedError.|Uma margem de erro desconhecida.|Se este problema persistir, contacte o suporte com o Job ID (a partir do painel de estado da sua configuração).|

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)
