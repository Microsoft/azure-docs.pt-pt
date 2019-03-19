---
title: Resolução de problemas na pré-visualização de proteção de palavra-passe do Azure AD
description: Compreender o Azure AD palavra-passe proteção pré-visualização comum de resolução de problemas
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 760ad30daabee61300768b7c67824f39437ac87f
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58006951"
---
# <a name="preview-azure-ad-password-protection-troubleshooting"></a>Pré-visualização: Resolução de problemas de proteção de palavra-passe do AD do Azure

|     |
| --- |
| Proteção de palavra-passe do Azure AD é uma funcionalidade de pré-visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

Após a implementação de proteção de palavra-passe do Azure AD, a resolução de problemas pode ser necessária. Este artigo apresenta detalhes para ajudar a compreender alguns passos de resolução de problemas comuns.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>O agente de controlador de domínio não é possível localizar um proxy no diretório

O sintoma principal deste problema é 30017 eventos no registo de eventos de administração do agente de controlador de domínio.

A causa comum deste problema é que um proxy ainda não ter sido registado. Se tiver sido registado um proxy, pode haver algum atraso devido a latência de replicação do AD até que um agente de controlador de domínio específico é capaz de ver que o proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>O agente de controlador de domínio não é capaz de comunicar com um proxy

O sintoma principal deste problema é 30018 eventos no registo de eventos de administração do agente de controlador de domínio. Isto pode ter várias causas possíveis:

1. O agente de controlador de domínio está localizado numa parte isolada da rede que não permite a conectividade de rede para o proxy(s) registados. Esse problema, por conseguinte, pode ser expected\benign, desde que outros agentes do DC podem comunicar com o proxy(s) para transferir políticas de palavra-passe do Azure, em seguida, será obtido por controlador de domínio isolado por meio da replicação dos ficheiros de política no compartilhamento de sysvol.

1. A máquina de anfitrião do proxy está a bloquear o acesso aos finais de mapeador de ponto de extremidade RPC (porta 135)

   O instalador do Proxy de proteção de palavra-passe do Azure AD cria automaticamente uma regra de entrada da Firewall do Windows que permite o acesso a porta 135. Se esta regra mais tarde seja eliminada ou desabilitada, os agentes do DC será não é possível comunicar com o serviço de Proxy. Se o elemento incorporado Decompress Firewall do Windows tem sido desativada em lugar de outro produto de firewall, tem de configurar essa firewall para permitir o acesso a porta 135.

1. A máquina de anfitrião do proxy está a bloquear o acesso ao ponto de extremidade RPC (dinâmico ou estático) escutámos pelo serviço de Proxy

   O instalador do Proxy de proteção de palavra-passe do Azure AD cria automaticamente uma Firewall do Windows regra de entrada que permite o acesso a nenhuma porta de entrada abertas pelo serviço de Proxy de proteção de palavra-passe do Azure AD. Se esta regra mais tarde seja eliminada ou desabilitada, os agentes do DC será não é possível comunicar com o serviço de Proxy. Se o elemento incorporado Decompress Firewall do Windows foi desativada em lugar de outro produto de firewall, tem de configurar que firewall para permitir o acesso a nenhuma porta de entrada abertas pelo serviço de Proxy de proteção de palavra-passe do Azure AD. Esta configuração poderão ser estabelecida mais específica, se o serviço de Proxy tiver sido configurado para escutar numa porta específica de RPC estática (usando o `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet).

## <a name="the-proxy-service-can-receive-calls-from-dc-agents-in-the-domain-but-is-unable-to-communicate-with-azure"></a>O serviço de Proxy pode receber chamadas de agentes do DC no domínio, mas não consegue comunicar com o Azure

Certifique-se de que a máquina de proxy tem conectividade aos pontos finais listados na [requisitos de implementação](howto-password-ban-bad-on-premises-deploy.md).

## <a name="the-dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files-and-other-state"></a>O agente de controlador de domínio não é possível ao encriptar ou desencriptar ficheiros de política de palavra-passe e outro Estado

Esse problema pode se manifestar com uma variedade de sintomas, mas normalmente tem uma causa comum.

Proteção de palavra-passe do AD do Azure tem uma dependência essencial sobre a funcionalidade de criptografia e descriptografia fornecida pelo serviço de distribuição de chaves Microsoft, que está disponível nos controladores de domínio com o Windows Server 2012 e posterior. O serviço KDS tem de ser ativado e funcional em todos os Windows Server 2012 e posteriores controladores de domínio num domínio.

Por predefinição o KDS o modo de início de serviço do serviço está configurado como Manual (acionador de início). Esta configuração significa que a primeira vez que um cliente tenta utilizar o serviço, é iniciada a pedido. Este modo de início de serviço predefinida é aceitável para a proteção de palavra-passe do Azure AD trabalhar.

Se o modo de início de serviço KDS tiver sido configurado para desativado, esta configuração tem de ser corrigida antes de proteção de palavra-passe do Azure AD funcionem corretamente.

Um teste simples para este problema é iniciar manualmente o serviço KDS, seja por meio do console MMC de gestão de serviço, ou com outras ferramentas de gestão de serviço (por exemplo, execute "net start kdssvc" partir de uma consola de linha de comandos). O serviço KDS é esperado para iniciar com êxito e se manter em execução.

A causa mais comum para o serviço KDS a ser não é possível iniciar é que o objeto de controlador de domínio do Active Directory está localizado fora a UO de controladores de domínio predefinida. Esta configuração não é suportada pelo serviço KDS e não é uma limitação imposta pela proteção de palavra-passe do Azure AD. A correção para esta condição é mover o objeto de controlador de domínio para uma localização em que a UO de controladores de domínio predefinida.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Senhas fracas estão a ser aceites, mas não devem ser

Esse problema pode ter várias causas.

1. Os agentes do DC não é possível transferir uma política ou não consegue desencriptar as políticas existentes. Verifique as causas possíveis nos tópicos acima.

1. O modo de imposição de política de palavra-passe ainda está definido para auditoria. Se esta configuração está em vigor, reconfigurá-la para impor com o portal de proteção de palavra-passe do Azure AD. Ver [proteção por senha ativar](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. A política de palavra-passe foi desativada. Se esta configuração está em vigor, reconfigurá-la ativada com o portal de proteção de palavra-passe do Azure AD. Ver [proteção por senha ativar](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Não tiver instalado o software do agente DC em todos os controladores de domínio no domínio. Nesta situação, é difícil garantir que os clientes remotos do Windows de destino um controlador de domínio específico durante uma operação de alteração de palavra-passe. Se tiver pensar que com êxito destina-se a um determinado controlador de domínio onde está instalado o software do agente DC, pode verificar para confirmar o registo de eventos de administração de agente do controlador de domínio: independentemente do resultado, haverá, pelo menos, um evento para documentar o resultado da palavra-passe validação. Se não houver nenhum evento presente para o utilizador cuja senha for alterada, a alteração de palavra-passe provavelmente foi processada por um controlador de domínio diferente.

   Como um teste de alternativo, tente setting\changing palavras-passe enquanto tiver sessão iniciada diretamente para um controlador de domínio onde está instalado o software do agente DC. Essa técnica não é recomendada para domínios do Active Directory de produção.

   Embora uma implementação incremental de software do agente de controlador de domínio seja suportada sujeitos a essas limitações, a Microsoft recomenda vivamente que o software do agente DC está instalado em todos os controladores de domínio num domínio logo que possível.

1. O algoritmo de validação da palavra-passe, na verdade, pode funcionar como esperado. Ver [como a palavras-passe são avaliadas](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="directory-services-repair-mode"></a>Modo de reparação de serviços de diretório

Se o controlador de domínio é iniciado no modo de reparação dos serviços de diretório, o serviço do agente DC Deteta esta condição e fará com que todas as atividades de imposição para ser desativado, independentemente da configuração de diretiva atualmente ativo ou validação da palavra-passe.

## <a name="emergency-remediation"></a>Remediação de emergência

Caso de uma situação em que o serviço de agente do controlador de domínio está a causar problemas, o serviço de agente do controlador de domínio pode ser imediatamente desligado. A dll de filtro de palavras-passe de agente do DC ainda tenta chamar o serviço de não execução e registrará em log os eventos de aviso (10012, 10013), mas todas as senhas de entrada são aceites durante esse período. O serviço do agente DC, em seguida, também pode ser configurado através do Gestor de controlo de serviço de Windows com um tipo de arranque de "Disabled" conforme necessário.

Outra medida de remediação seria definir o modo de ativação para não no portal de proteção de palavra-passe do Azure AD. Assim que tiver sido baixada a política atualizada, cada serviço de agente do controlador de domínio irão entrar num modo inativo em que todas as senhas são aceites como-é. Para obter mais informações, consulte [modo imposição](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="domain-controller-demotion"></a>Despromoção do controlador de domínio

É suportada para despromover um controlador de domínio que ainda está a executar o software do agente DC. Os administradores devem estar cientes entretanto que o software do agente DC continua a impor a política de palavra-passe atual durante o procedimento de despromoção. A nova senha da conta administrador local (especificada como parte da operação de despromoção) é validada como qualquer outra palavra-passe. A Microsoft recomenda que as palavras-passe seguras ser escolhido para as contas de administrador locais como parte de um procedimento de despromoção do controlador de domínio; No entanto, a validação da nova senha de conta de administrador local, o software do agente DC pode ser perturbador para pré-existente procedimentos operacionais de despromoção.

Depois da despromoção foi concluída com êxito, e o controlador de domínio foi reinicializado e é executado novamente como um servidor membro normal, o software do agente DC é revertido para em execução num modo passivo. Em seguida, pode ser desinstalado em qualquer altura.

## <a name="removal"></a>Remoção

Se é decidido para desinstalar o software de pré-visualização pública e a limpeza de estado de todas as respetivas dos domínios e floresta, esta tarefa pode ser feita usando os seguintes passos:

> [!IMPORTANT]
> É importante executar estes passos por ordem. Se qualquer instância do serviço de Proxy é deixada em execução periodicamente novamente criará seu objeto de serviceConnectionPoint. Se qualquer instância do serviço de agente do controlador de domínio for deixada em execução periodicamente novamente criará seu objeto de serviceConnectionPoint e o estado de sysvol.

1. Desinstale o software de Proxy de todas as máquinas. Este passo faz **não** requerem um reinício.
2. Desinstale o software do agente de controlador de domínio de todos os controladores de domínio. Este passo **requer** um reinício.
3. Remova manualmente todos os pontos de ligação de serviço de Proxy em cada contexto de nomenclatura de domínio. A localização desses objetos pode ser detetada com o seguinte comando do PowerShell do Active Directory:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Não é omitir o asterisco ("*") no final o valor da variável $keywords.

   O objeto resultante (s) encontrada por meio da `Get-ADObject` comando, em seguida, pode ser enviado por pipe para `Remove-ADObject`, ou eliminada manualmente.

4. Remova manualmente todos os pontos de ligação do agente de controlador de domínio em cada contexto de nomenclatura de domínio. Pode haver um desses objetos por controlador de domínio na floresta, dependendo de quão amplamente foi implementado o software de pré-visualização pública. A localização desse objeto pode ser detetada com o seguinte comando do PowerShell do Active Directory:

   ```PowerShell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   O objeto resultante (s) encontrada por meio da `Get-ADObject` comando, em seguida, pode ser enviado por pipe para `Remove-ADObject`, ou eliminada manualmente.

   Não é omitir o asterisco ("*") no final o valor da variável $keywords.

5. Remova manualmente o estado de configuração ao nível da floresta. O estado de configuração de floresta é mantido num contentor no contexto de nomenclatura de configuração do Active Directory. Pode ser detetado e eliminado da seguinte forma:

   ```PowerShell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Remova todos os sysvol relacionadas com o estado da manualmente eliminar manualmente a pasta seguinte e todos os respetivos conteúdos:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Se necessário, este caminho pode também ser acedido localmente num controlador de domínio de determinado; a localização predefinida deve ser algo como o seguinte caminho:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Este caminho é diferente se tiver configurada a partilha sysvol numa localização não padrão.

## <a name="next-steps"></a>Passos Seguintes

[Perguntas mais frequentes sobre proteção de palavra-passe do Azure AD](howto-password-ban-bad-on-premises-faq.md)

Para obter mais informações sobre as listas globais e personalizadas banidas palavra-passe, consulte o artigo [banir palavras-passe incorretas](concept-password-ban-bad.md)
