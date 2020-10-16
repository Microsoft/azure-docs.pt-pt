---
title: Resolução de problemas no local Azure AD Password Protection
description: Saiba como resolver problemas com a proteção de senhas Azure AD para um ambiente de Serviços de Domínio de Diretório Ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3508d3942626c319221f4b690aaf444e034195bf
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966632"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Resolução de problemas: Proteção de senha azure AD

Após a implementação da Proteção de Passwords AD Azure, poderá ser necessária uma resolução de problemas. Este artigo entra em detalhes para ajudá-lo a entender alguns passos comuns de resolução de problemas.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>O agente de DC não consegue localizar um representante no diretório.

O principal sintoma deste problema são 30017 eventos no registo de eventos do agente dc Admin.

A causa habitual desta questão é que um representante ainda não foi registado. Se um representante tiver sido registado, pode haver algum atraso devido à latência da replicação da AD até que um agente em DC em particular possa ver esse representante.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>O agente dc não é capaz de comunicar com um representante

O principal sintoma deste problema são 30018 eventos no registo de eventos do agente dc Admin. Este problema pode ter várias causas possíveis:

1. O agente DC está localizado numa parte isolada da rede que não permite a conectividade da rede com o(s) procuração registado. Este problema pode ser benigno desde que outros agentes de DC possam comunicar com o(s) proxy(s) a fim de descarregar políticas de palavra-passe a partir do Azure. Uma vez descarregadas, essas políticas serão então obtidas pelo DC isolado através da replicação dos ficheiros políticos na partilha sysvol.

1. A máquina de hospedeiro proxy está bloqueando o acesso ao ponto final do ponto final do RPC (porta 135)

   O instalador Azure AD Password Protection Proxy cria automaticamente uma regra de entrada do Windows Firewall que permite o acesso à porta 135. Se esta regra for posteriormente eliminada ou desativada, os agentes de DC não poderão comunicar com o serviço Proxy. Se o Windows Firewall incorporado tiver sido desativado em vez de outro produto de firewall, deve configurar essa firewall para permitir o acesso à porta 135.

1. A máquina de hospedeiro proxy está bloqueando o acesso ao ponto final RPC (dinâmico ou estático) escutado pelo serviço Proxy

   O instalador Azure AD Password Protection Proxy cria automaticamente uma regra de entrada do Windows Firewall que permite o acesso a quaisquer portas de entrada ouvidas pelo serviço de procuração de senha azure AD. Se esta regra for posteriormente eliminada ou desativada, os agentes de DC não poderão comunicar com o serviço Proxy. Se o Windows Firewall incorporado tiver sido desativado em vez de outro produto de firewall, deve configurar essa firewall para permitir o acesso a quaisquer portas de entrada ouvidas pelo serviço Azure AD Password Protection Proxy. Esta configuração pode ser tornada mais específica se o serviço Proxy tiver sido configurado para ouvir sobre uma porta RPC estática específica (utilizando o `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet).

1. A máquina de hospedeiro proxy não está configurada para permitir aos controladores de domínio a capacidade de iniciar sessão na máquina. Este comportamento é controlado através da atribuição de privilégios de utilizador "Aceda a este computador a partir da rede". Todos os controladores de domínio em todos os domínios da floresta devem ter este privilégio. Esta definição é muitas vezes limitada como parte de um esforço de endurecimento de rede maior.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Serviço proxy é incapaz de comunicar com Azure

1. Certifique-se de que a máquina proxy tem conectividade com os pontos finais indicados nos [requisitos de implantação](howto-password-ban-bad-on-premises-deploy.md).

1. Certifique-se de que a floresta e todos os servidores proxy estão registados contra o mesmo inquilino Azure.

   Pode verificar este requisito executando os  `Get-AzureADPasswordProtectionProxy` `Get-AzureADPasswordProtectionDCAgent` cmdlets e PowerShell e, em seguida, comparar a `AzureTenant` propriedade de cada item devolvido. Para uma operação correta, o nome do inquilino reportado deve ser o mesmo em todos os agentes de DC e servidores proxy.

   Se existir uma condição de incompatibilidade de inscrição do inquilino Azure, este problema pode ser corrigido executando os `Register-AzureADPasswordProtectionProxy` cmdlets e/ou `Register-AzureADPasswordProtectionForest` PowerShell conforme necessário, certificando-se de que utiliza credenciais do mesmo inquilino Azure para todas as inscrições.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>O agente DC não consegue encriptar ou desencriptar ficheiros de política de palavras-passe

O Azure AD Password Protection tem uma dependência crítica da funcionalidade de encriptação e desencriptação fornecida pelo Microsoft Key Distribution Service. Falhas de encriptação ou desencriptação podem manifestar-se com uma variedade de sintomas e ter várias causas potenciais.

1. Certifique-se de que o serviço KDS está ativado e funcional em todos os comandos de domínio do Windows Server 2012 e posteriormente num domínio.

   Por predefinição, o modo de arranque de serviço do serviço KDS está configurado como Manual (Trigger Start). Esta configuração significa que a primeira vez que um cliente tenta usar o serviço, é iniciado a pedido. Este modo de arranque de serviço predefinido é aceitável para que o Azure AD Password Protection funcione.

   Se o modo de arranque do serviço KDS tiver sido configurado para Desativar, esta configuração tem de ser corrigida antes de a Azure AD Password Protection funcionar corretamente.

   Um teste simples para este problema é iniciar manualmente o serviço KDS, quer através da consola MMC de gestão de serviço, quer utilizando outras ferramentas de gestão (por exemplo, executar "net start kdssvc" a partir de uma consola de pedido de comando). Espera-se que o serviço KDS comece com sucesso e permaneça em funcionamento.

   A causa de raiz mais comum para o serviço KDS não poder iniciar é que o objeto controlador de domínio do Diretório Ativo está localizado fora dos controladores de domínio padrão OU. Esta configuração não é suportada pelo serviço KDS e não é uma limitação imposta pela Azure AD Password Protection. A correção para esta condição é mover o objeto do controlador de domínio para uma localização sob os controladores de domínio padrão OU.

1. Alteração incompatível do formato de tampão encriptado KDS do Windows Server 2012 R2 para Windows Server 2016

   Foi introduzida uma correção de segurança KDS no Windows Server 2016 que modifica o formato dos buffers encriptados KDS; estes buffers por vezes não conseguem desencriptar no Windows Server 2012 e no Windows Server 2012 R2. A direção inversa está bem - os buffers que estão encriptados com KDS no Windows Server 2012 e o Windows Server 2012 R2 irá sempre desencriptar com sucesso no Windows Server 2016 e posteriormente. Se os controladores de domínio nos seus domínios ative directory estiverem a executar uma mistura destes sistemas operativos, podem ser reportadas falhas ocasionais de desencriptação de proteção de passwords Azure AD. Não é possível prever com precisão o tempo ou sintomas destas falhas dada a natureza da correção de segurança, e dado que não é determinista qual o agente Azure AD Password Protection DC agente em que o controlador de domínio irá encriptar dados num dado momento.

   Não existe uma solução alternativa para esta questão que não seja para não executar uma mistura destes sistemas operativos incompatíveis no seu(s) ative directy(s). Por outras palavras, deverá executar apenas controladores de domínio Windows Server 2012 e Windows Server 2012 R2, OU só deverá executar controladores de domínio Windows Server 2016 e acima.

## <a name="dc-agent-thinks-the-forest-has-not-been-registered"></a>O agente da DC acha que a floresta não foi registada.

O sintoma desta edição é que 30016 eventos estão a ser registados no canal DC Agent\Admin que diz em parte:

```text
The forest has not been registered with Azure. Password policies cannot be downloaded from Azure unless this is corrected.
```

Há duas causas possíveis para esta questão.

1. A floresta não foi, de facto, registada. Para resolver o problema, por favor, executar o comando Register-AzureADPasswordProtectionForest conforme descrito nos [requisitos de implantação](howto-password-ban-bad-on-premises-deploy.md).
1. A floresta foi registada, mas o agente de DC não consegue desencriptar os dados do registo florestal. Este caso tem a mesma causa de raiz que a questão #2 listados acima sob [o agente DC é incapaz de encriptar ou desencriptar ficheiros de política de palavra-passe](howto-password-ban-bad-on-premises-troubleshoot.md#dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files). Uma forma fácil de confirmar esta teoria é que você verá este erro apenas em agentes DC em execução no Windows Server 2012 ou nos controladores de domínio Windows Server 2012R2, enquanto os agentes DC que executam no Windows Server 2016 e posteriormente os controladores de domínio estão bem. A solução alternativa é a mesma: atualize todos os controladores de domínio para o Windows Server 2016 ou mais tarde.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Senhas fracas estão sendo aceites, mas não devem ser

Este problema pode ter várias causas.

1. Os seus agentes(s) de DC estão a executar uma versão de software de pré-visualização pública que expirou. Veja [o software do agente DC de pré-visualização pública expirou](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. O seu(s) agente(s) não pode descarregar uma apólice ou não é capaz de desencriptar as políticas existentes. Verifique se há possíveis causas nos tópicos acima.

1. O modo de aplicação da política de palavra-passe ainda está definido para Auditoria. Se esta configuração estiver em vigor, reconfigure-a para impor usando o portal de proteção de senhas Azure AD. Para obter mais informações, consulte [Modos de funcionamento.](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)

1. A política da palavra-passe foi desativada. Se esta configuração estiver em vigor, reconfigure-a para ativar utilizando o portal Azure AD Password Protection. Para obter mais informações, consulte [Modos de funcionamento.](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)

1. Não instalou o software do agente DC em todos os controladores de domínio do domínio. Nesta situação, é difícil garantir que os clientes remotos do Windows direcionam um determinado controlador de domínio durante uma operação de mudança de senha. Se acha que direcionou com sucesso um DC em particular onde o software do agente DC está instalado, pode verificar verificando duas vezes o registo de eventos do agente DC: independentemente do resultado, haverá pelo menos um evento para documentar o resultado da validação da palavra-passe. Se não houver nenhum evento presente para o utilizador cuja palavra-passe é alterada, então a alteração da palavra-passe foi provavelmente processada por um controlador de domínio diferente.

   Como teste alternativo, tente configurar as palavras-passe de alteração de palavras-passe enquanto inicia sessão diretamente num DC onde o software do agente DC está instalado. Esta técnica não é recomendada para a produção de domínios ative directy.

   Embora a implementação incremental do software do agente DC seja suportada sob estas limitações, a Microsoft recomenda vivamente que o software do agente DC seja instalado em todos os controladores de domínio num domínio o mais rapidamente possível.

1. O algoritmo de validação de palavras-passe pode estar a funcionar como esperado. Veja [como são avaliadas as palavras-passe.](concept-password-ban-bad.md#how-are-passwords-evaluated)

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe não define uma senha DSRM fraca

O Ative Directory validará sempre uma nova palavra-passe do Modo de Reparação de Serviços de Diretório para garantir que satisfaz os requisitos de complexidade da palavra-passe do domínio; esta validação também chama para o filtro de senha dlls como Azure AD Password Protection. Se a nova palavra-passe DSRM for rejeitada, a seguinte mensagem de erro resulta:

```text
C:\>ntdsutil.exe
ntdsutil: set dsrm password
Reset DSRM Administrator Password: reset password on server null
Please type password for DS Restore Mode Administrator Account: ********
Please confirm new password: ********
Setting password failed.
        WIN32 Error Code: 0xa91
        Error Message: Password doesn't meet the requirements of the filter dll's
```

Quando o Azure AD Password Protection regista o evento de registo de validação de palavra-passe para uma palavra-passe DSRM do Diretório Ativo, espera-se que as mensagens de registo do evento não incluam um nome de utilizador. Este comportamento ocorre porque a conta DSRM é uma conta local que não faz parte do domínio real do Ative Directory.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>A promoção da réplica do controlador de domínio falha devido a uma senha DSRM fraca

Durante o processo de promoção de DC, a nova senha do Modo de Reparação de Serviços de Diretório será submetida a um DC existente no domínio para validação. Se a nova palavra-passe DSRM for rejeitada, a seguinte mensagem de erro resulta:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Tal como na edição acima, qualquer evento de validação de senha de senha Azure AD Dispõe de senha terá nomes de utilizador vazios para este cenário.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>A despromoção do controlador de domínio falha devido a uma senha de administrador local fraca

É suportado para despromuminar um controlador de domínio que ainda está a executar o software do agente DC. No entanto, os administradores devem estar cientes de que o software do agente DC continua a aplicar a atual política de palavra-passe durante o procedimento de despromoção. A nova senha de conta do administrador local (especificada como parte da operação de despromoção) é validada como qualquer outra palavra-passe. A Microsoft recomenda que as palavras-passe seguras sejam escolhidas para as contas de administrador local como parte de um procedimento de despromoção em DC.

Uma vez que a despromoção tenha sido bem sucedida, e o controlador de domínio foi reiniciado e está novamente funcionando como um servidor de membro normal, o software do agente DC volta a funcionar em modo passivo. Pode então ser desinstalado a qualquer momento.

## <a name="booting-into-directory-services-repair-mode"></a>Arranque no modo de reparação de serviços de diretório

Se o controlador de domínio for iniciado no Modo de Reparação de Serviços de Diretório, o filtro de senha do agente DC dll deteta esta condição e fará com que todas as atividades de validação ou execução de palavras-passe sejam desativadas, independentemente da configuração de política atualmente ativa. O dll do filtro de senha do agente DC registará um evento de aviso 10023 no registo do evento de Administração, por exemplo:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>O software de agente dc de pré-visualização pública expirou

Durante o período de pré-visualização da Azure AD Password Protection, o software do agente DC foi codificado para parar de processar pedidos de validação de palavras-passe nas seguintes datas:

* A versão 1.2.65.0 deixará de processar pedidos de validação de passwords no dia 1 de setembro de 2019.
* Versão 1.2.25.0 e pedidos de validação de senha de processamento anteriores a 1 de julho de 2019.

À medida que o prazo se aproxima, todas as versões de agente dc limitadas em tempo emitirão um evento de 10021 no registo de eventos do agente dc Admin na hora do arranque que se parece com isto:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Uma vez ultrapassado o prazo, todas as versões de agente dc limitadas em tempo emitirão um evento de 10022 no registo de eventos do agente dc Admin na hora de arranque que se parece com isto:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Uma vez que o prazo é apenas verificado no arranque inicial, você pode não ver estes eventos até muito tempo após o prazo do calendário ter passado. Uma vez reconhecido o prazo, não serão automaticamente aprovados efeitos negativos no controlador de domínio ou no ambiente maior, para além de todas as palavras-passe.

> [!IMPORTANT]
> A Microsoft recomenda que os agentes de pré-visualização públicos expirados sejam imediatamente atualizados para a versão mais recente.

Uma maneira fácil de descobrir agentes de DC no seu ambiente que precisam de ser atualizados é executando o `Get-AzureADPasswordProtectionDCAgent` cmdlet, por exemplo:

```powershell
PS C:\> Get-AzureADPasswordProtectionDCAgent

ServerFQDN            : bpl1.bpl.com
SoftwareVersion       : 1.2.125.0
Domain                : bpl.com
Forest                : bpl.com
PasswordPolicyDateUTC : 8/1/2019 9:18:05 PM
HeartbeatUTC          : 8/1/2019 10:00:00 PM
AzureTenant           : bpltest.onmicrosoft.com
```

Para este tópico, o campo SoftwareVersion é obviamente a propriedade chave para olhar. Também pode utilizar a filtragem PowerShell para filtrar agentes DC que já estão na versão de base necessária, por exemplo:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

O software Azure AD Password Protection Proxy não é limitado por tempo em nenhuma versão. A Microsoft ainda recomenda que os agentes dc e proxy sejam atualizados para as versões mais recentes à medida que forem lançados. O `Get-AzureADPasswordProtectionProxy` cmdlet pode ser usado para encontrar agentes proxy que requerem upgrades, semelhantes ao exemplo acima para agentes de DC.

Consulte a [atualização do agente DC](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) e [a atualização do serviço Proxy](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service) para obter mais detalhes sobre procedimentos específicos de atualização.

## <a name="emergency-remediation"></a>Remediação de emergência

Se ocorrer uma situação em que o serviço de agente dc está a causar problemas, o serviço de agente dc pode ser imediatamente encerrado. O filtro de senha do agente DC ainda tenta ligar para o serviço de não funcionamento e registará eventos de alerta (10012, 10013), mas todas as palavras-passe recebidas são aceites durante esse período. O serviço de agente DC também pode ser configurado através do Gestor de Controlo de Serviços do Windows com um tipo de startup de "Desativado" conforme necessário.

Outra medida de reparação seria definir o modo Ativar para Não no portal Azure AD Password Protection. Uma vez que a política atualizada tenha sido descarregada, cada serviço de agente DC entrará num modo quiescente onde todas as palavras-passe são aceites como está. Para obter mais informações, consulte [Modos de funcionamento.](howto-password-ban-bad-on-premises-operations.md#modes-of-operation)

## <a name="removal"></a>Remoção

Se for decidido desinstalar o software de proteção de senhas AZure AD e limpar todo o estado relacionado a partir do(s) domínio(s) e floresta, esta tarefa pode ser realizada usando os seguintes passos:

> [!IMPORTANT]
> É importante executar estes passos em ordem. Se alguma instância do serviço Proxy for deixada em funcionamento, irá recriar periodicamente o seu objeto ServiceConnectionPoint. Se alguma instância do serviço de agente DC for deixada em funcionamento, irá recriar periodicamente o seu objeto serviceConnectionPoint e o estado sysvol.

1. Desinstale o software Proxy de todas as máquinas. Este passo **não** requer um reinício.
2. Desinstale o software do Agente DC de todos os controladores de domínio. Este passo **requer** um reboot.
3. Remova manualmente todos os pontos de ligação de serviço Proxy em cada contexto de nomeação de domínio. A localização destes objetos pode ser descoberta com o seguinte comando Ative Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Não omita o asterisco ("*") no final do $keywords valor variável.

   Os objetos resultantes encontrados através do `Get-ADObject` comando podem então ser canalizados `Remove-ADObject` para , ou eliminados manualmente.

4. Remova manualmente todos os pontos de ligação do agente DC em cada contexto de nomeação de domínio. Pode haver um destes objetos por controlador de domínio na floresta, dependendo da largura do software. A localização desse objeto pode ser descoberta com o seguinte comando Ative Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Os objetos resultantes encontrados através do `Get-ADObject` comando podem então ser canalizados `Remove-ADObject` para , ou eliminados manualmente.

   Não omita o asterisco ("*") no final do $keywords valor variável.

5. Remova manualmente o estado de configuração ao nível da floresta. O estado de configuração da floresta é mantido num recipiente no contexto de nomeação de configuração do Diretório Ativo. Pode ser descoberto e eliminado da seguinte forma:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Remova manualmente todo o estado relacionado com a sysvol, eliminando manualmente a seguinte pasta e todo o seu conteúdo:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Se necessário, este caminho também pode ser acedido localmente num determinado controlador de domínio; a localização padrão seria algo como o seguinte caminho:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Este caminho é diferente se a partilha sysvol tiver sido configurada num local não padrão.

## <a name="next-steps"></a>Passos seguintes

[Perguntas frequentes para proteção de senha AD AZure](howto-password-ban-bad-on-premises-faq.md)

Para obter mais informações sobre as listas de senhas proibidas global e personalizada, consulte o artigo [Proibir más senhas](concept-password-ban-bad.md)
