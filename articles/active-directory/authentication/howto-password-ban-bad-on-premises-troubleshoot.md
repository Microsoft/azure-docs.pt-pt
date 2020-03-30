---
title: Resolução de problemas no local Azure AD Password Protection
description: Saiba como resolver problemas na Proteção de Passwords Azure AD para um ambiente de Serviços de Domínio de Diretório Ativo no local
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79ebf543a3880a4f2c8ee8c0d706c268ef3f08d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263650"
---
# <a name="troubleshoot-on-premises-azure-ad-password-protection"></a>Troubleshoot: No local AD Password Protection

Após a implementação da Proteção de Passwords Azure AD, pode ser necessário resolução de problemas. Este artigo entra em detalhes para ajudá-lo a entender alguns passos comuns de resolução de problemas.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>O agente de DC não pode localizar um representante no diretório

O principal sintoma deste problema são os eventos de 30017 no registo de eventos do agente dC Admin.

A causa habitual desta questão é que ainda não foi registado um procurador. Se um representante tiver sido registado, pode haver algum atraso devido à latência de replicação da AD até que um determinado agente de DC possa ver esse representante.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>O agente de DC não é capaz de comunicar com um representante

O principal sintoma deste problema são os eventos de 30018 no registo de eventos do agente dC Admin. Este problema pode ter várias possíveis causas:

1. O agente DC está localizado numa parte isolada da rede que não permite a conectividade da rede com o procuração registado. Este problema pode ser benigno desde que outros agentes de DC possam comunicar com o(s) proxy(s) para descarregar políticas de senha do Azure. Uma vez descarregados, essas políticas serão então obtidas pelo DC isolado através da replicação dos ficheiros políticos na parte sysvol.

1. A máquina de hospedeiro proxy está bloqueando o acesso ao ponto final do ponto final do ponto final do RPC (porta 135)

   O instalador de proxy de proteção de senhas AD Azure cria automaticamente uma regra de entrada do Windows Firewall que permite o acesso à porta 135. Se esta regra for posteriormente eliminada ou desativada, os agentes de DC não poderão comunicar com o serviço Proxy. Se o Windows Firewall incorporado tiver sido desativado em vez de outro produto de firewall, tem de configurar essa firewall para permitir o acesso à porta 135.

1. A máquina de hospedeiro proxy está bloqueando o acesso ao ponto final rPC (dinâmico ou estático) ouvido pelo serviço Proxy

   O instalador de proxy de proteção de passwords Azure AD cria automaticamente uma regra de entrada do Windows Firewall que permite o acesso a quaisquer portas de entrada ouvidas pelo serviço de proxy de proteção de passwords AD Azure. Se esta regra for posteriormente eliminada ou desativada, os agentes de DC não poderão comunicar com o serviço Proxy. Se o Windows Firewall incorporado tiver sido desativado em vez de outro produto de firewall, deve configurar essa firewall para permitir o acesso a quaisquer portas de entrada ouvidas pelo serviço de proxy de proteção de passwords Azure AD. Esta configuração pode ser tornada mais específica se o serviço Proxy tiver sido `Set-AzureADPasswordProtectionProxyConfiguration` configurado para ouvir uma porta RPC estática específica (utilizando o cmdlet).

1. A máquina de hospedeiro proxy não está configurada para permitir aos controladores de domínio a capacidade de iniciar sessão na máquina. Este comportamento é controlado através da atribuição de privilégios de utilizador "Access this computer from the network". Todos os controladores de domínio em todos os domínios da floresta devem ter este privilégio. Esta configuração é muitas vezes limitada como parte de um esforço de endurecimento da rede maior.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>Serviço proxy é incapaz de comunicar com Azure

1. Certifique-se de que a máquina proxy tem conectividade com os pontos finais listados nos requisitos de [implementação](howto-password-ban-bad-on-premises-deploy.md).

1. Certifique-se de que a floresta e todos os servidores proxy estão registados contra o mesmo inquilino Azure.

   Pode verificar este requisito `Get-AzureADPasswordProtectionProxy` executando os cmdlets `Get-AzureADPasswordProtectionDCAgent` `AzureTenant` e powerShell e, em seguida, comparar a propriedade de cada item devolvido. Para uma operação correta, o nome do inquilino reportado deve ser o mesmo em todos os agentes de DC e servidores proxy.

   Se existir uma condição de incompatibilidade de registo de `Register-AzureADPasswordProtectionProxy` inquilinos `Register-AzureADPasswordProtectionForest` Azure, este problema pode ser corrigido executando os cmdlets e/ou PowerShell conforme necessário, certificando-se de usar credenciais do mesmo inquilino Azure para todas as inscrições.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>O agente dc é incapaz de encriptar ou desencriptar ficheiros de política de passwords

A Proteção de Palavras-passe Azure AD tem uma dependência crítica da funcionalidade de encriptação e desencriptação fornecida pelo Serviço de Distribuição de Chaves da Microsoft. Falhas de encriptação ou desencriptação podem manifestar-se com uma variedade de sintomas e têm várias causas potenciais.

1. Certifique-se de que o serviço KDS está ativado e funcional em todos os controladores de domínio do Windows Server 2012 e posteriormente num domínio.

   Por predefinição, o modo de arranque do serviço KDS está configurado como Manual (Trigger Start). Esta configuração significa que a primeira vez que um cliente tenta usar o serviço, é iniciado a pedido. Este modo de arranque do serviço predefinido é aceitável para que a Proteção de Passwords AD do Azure funcione.

   Se o modo de arranque do serviço KDS tiver sido configurado para Desativado, esta configuração deve ser corrigida antes que a Proteção da Palavra-passe Azure AD funcione corretamente.

   Um teste simples para este problema é iniciar manualmente o serviço KDS, seja através da consola MMC de gestão de serviços, ou utilizando outras ferramentas de gestão (por exemplo, executar "net start kdssvc" a partir de uma consola de comando). Espera-se que o serviço KDS comece com sucesso e permaneça em funcionamento.

   A causa mais comum para o serviço KDS não poder iniciar é que o objeto do controlador de domínio ative directory está localizado fora dos controladores de domínio padrão OU. Esta configuração não é suportada pelo serviço KDS e não é uma limitação imposta pela Proteção de Passwords Azure AD. A correção para esta condição é mover o objeto do controlador de domínio para um local sob os controladores de domínio padrão OU.

1. Alteração do formato tampão encriptado KDS incompatível do Windows Server 2012 R2 para Windows Server 2016

   Foi introduzida uma correção de segurança KDS no Windows Server 2016 que modifica o formato dos amortecedores encriptados KDS; estes tampões por vezes não desencriptam no Windows Server 2012 e no Windows Server 2012 R2. A direção inversa é boa - os tampões que são encriptados por KDS no Windows Server 2012 e windows Server 2012 R2 sempre desencriptarão com sucesso no Windows Server 2016 e mais tarde. Se os controladores de domínio nos seus domínios de Diretório Ativo estiverem a executar uma mistura destes sistemas operativos, podem ser reportadas falhas ocasionais de desencriptação da Proteção de Passwords Azure AD. Não é possível prever com precisão o tempo ou sintomas destas falhas dada a natureza da correção de segurança, e dado que não é determinista qual agente dc de proteção de senha seletiva azure em que o controlador de domínio irá encriptar dados num dado momento.

   A Microsoft está a investigar uma correção para este problema, mas ainda não existe eTA. Entretanto, não existe qualquer supressão para esta questão a não ser não executar uma mistura destes sistemas operativos incompatíveis no seu domínio de Diretório Ativo. Por outras palavras, deve executar apenas controladores de domínio Windows Server 2012 e Windows Server 2012 R2, ou deve executar apenas controladores de domínio Windows Server 2016 e acima.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Palavras-passe fracas estão a ser aceites, mas não devem ser

Este problema pode ter várias causas.

1. O seu agente dc(s) está a executar uma versão pública de software de pré-visualização que expirou. Ver [Público pré-visualização O software do agente DC expirou](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. O seu(s) agente(s) de DC não pode descarregar uma política ou é incapaz de desencriptar as políticas existentes. Verifique eventuais causas nos tópicos acima referidos.

1. A política de passwords O modo de aplicação ainda está definido para auditoria. Se esta configuração estiver em vigor, reconfigure-a para impor utilizando o portal de proteção de passwords Azure AD. Para mais informações, consulte [modos de funcionamento](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. A política de senhas foi desativada. Se esta configuração estiver em vigor, reconfigure-a para ativar utilizando o portal de proteção de passwords Azure AD. Para mais informações, consulte [modos de funcionamento](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

1. Não instalou o software do agente DC em todos os controladores de domínio no domínio. Nesta situação, é difícil garantir que os clientes remotos do Windows direcionam um determinado controlador de domínio durante uma operação de alteração de palavra-passe. Se acha que apontou com sucesso a um determinado DC onde o software do agente DC está instalado, pode verificar verificando duas vezes o registo de eventos do agente dc: independentemente do resultado, haverá pelo menos um evento para documentar o resultado da senha validação. Se não houver nenhum evento presente para o utilizador cuja palavra-passe é alterada, então a alteração da palavra-passe foi provavelmente processada por um controlador de domínio diferente.

   Como um teste alternativo, tente configurar as palavras-passe com a alteração de palavras-passe enquanto faz login diretamente num DC onde o software do agente DC está instalado. Esta técnica não é recomendada para a produção de domínios de Diretório Ativo.

   Embora a implementação incremental do software do agente DC seja suportada sob estas limitações, a Microsoft recomenda vivamente que o software do agente DC seja instalado em todos os controladores de domínio num domínio o mais rapidamente possível.

1. O algoritmo de validação de palavras-passe pode estar a funcionar como esperado. Ver [Como são avaliadas as palavras-passe](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>Ntdsutil.exe não define uma senha dSRM fraca

O Diretório Ativo validará sempre uma nova senha do Modo de Reparação de Serviços de Diretório para se certificar de que satisfaz os requisitos de complexidade de palavras-passe do domínio; esta validação também chama para dlls de filtro de senha como AD Password Protection Azure. Se a nova palavra-passe dSRM for rejeitada, a seguinte mensagem de erro resulta:

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

Quando a Proteção de Palavra-passe Azure AD regista o evento de registo de registo de eventos de validação de passwords para uma senha DSRM de Diretório Ativo, espera-se que as mensagens de registo do evento não incluam um nome de utilizador. Este comportamento ocorre porque a conta DSRM é uma conta local que não faz parte do domínio real do Diretório Ativo.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>A promoção da réplica do controlador de domínio falha devido a uma palavra-passe dSRM fraca

Durante o processo de promoção de DC, a nova senha do Modo de Reparação de Serviços de Diretório será submetida a um DC existente no domínio para validação. Se a nova palavra-passe dSRM for rejeitada, a seguinte mensagem de erro resulta:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Tal como no edição acima referido, qualquer evento de validação de senha de senha da AD Azure terá nomes de utilizadores vazios para este cenário.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>A despromoção do controlador de domínio falha devido a uma fraca senha do Administrador Local

É suportado para despromover um controlador de domínio que ainda está a executar o software do agente DC. No entanto, os administradores devem estar cientes de que o software do agente DC continua a aplicar a atual política de passwords durante o procedimento de despromoção. A nova senha de conta do Administrador local (especificada como parte da operação de despromoção) é validada como qualquer outra palavra-passe. A Microsoft recomenda que sejam escolhidas senhas seguras para contas de Administrador local como parte de um procedimento de despromoção em DC.

Uma vez que a despromoção tenha sido bem sucedida, e o controlador de domínio foi reiniciado e está novamente a funcionar como um servidor de membro normal, o software do agente DC volta a funcionar num modo passivo. Pode então ser desinstalado a qualquer momento.

## <a name="booting-into-directory-services-repair-mode"></a>Iniciar no modo de reparação de serviços de diretório

Se o controlador de domínio for iniciado no Modo de Reparação de Serviços de Diretório, o filtro de senha do agente DC deteta esta condição e fará com que todas as medidas de validação ou execução de palavras-passe sejam desativadas, independentemente da política atualmente ativa configuração. O dll de filtro de senha do agente DC irá registar um evento de aviso 10023 no registo do evento Admin, por exemplo:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>O software do agente DC de pré-visualização pública expirou

Durante o período de pré-visualização pública de proteção de senhas Azure AD, o software do agente DC foi codificado para parar de processar pedidos de validação de passwords nas seguintes datas:

* A versão 1.2.65.0 deixará de processar pedidos de validação de passwords no dia 1 de setembro de 2019.
* Versão 1.2.25.0 e anterior estiveram a processar pedidos de validação de passwords a 1 de julho de 2019.

À medida que o prazo se aproxima, todas as versões de agente de DC limitadas no tempo emitirão um evento de 10021 no registo de eventos do agente dC Admin no momento de arranque que se parece com este:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Uma vez ultrapassado o prazo, todas as versões limitadas de agente de DC emitirão um evento de 10022 no registo de eventos do agente dC Admin no momento de arranque que se parece com este:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Uma vez que o prazo é apenas verificado na bota inicial, pode não ver estes eventos até muito tempo depois do prazo de calendário ter passado. Uma vez reconhecido o prazo, nenhum efeito negativo no controlador de domínio ou no ambiente maior ocorrerá para além de todas as palavras-passe serautomaticamente aprovadas.

> [!IMPORTANT]
> A Microsoft recomenda que os agentes de pré-visualização pública expirados sejam imediatamente atualizados para a versão mais recente.

Uma maneira fácil de descobrir agentes de DC no seu `Get-AzureADPasswordProtectionDCAgent` ambiente que precisam de ser atualizados é executando o cmdlet, por exemplo:

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

Para este tópico, o campo SoftwareVersion é obviamente a propriedade chave para se olhar. Também pode utilizar a filtragem PowerShell para filtrar os agentes DC que já estão na versão de base necessária, por exemplo:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

O software Proxy de Proteção de Passwords AD Azure não é limitado em qualquer versão. A Microsoft recomenda ainda que tanto os agentes DEC como os agentes proxy sejam atualizados para as versões mais recentes à medida que são lançados. O `Get-AzureADPasswordProtectionProxy` cmdlet pode ser usado para encontrar agentes Proxy que requeiram upgrades, semelhantes ao exemplo acima para os agentes de DC.

Consulte [a atualização do agente DC](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) e [a atualização do serviço Proxy](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-service) para obter mais detalhes sobre procedimentos específicos de atualização.

## <a name="emergency-remediation"></a>Reparação de emergência

Se ocorrer uma situação em que o serviço de agente dc esteja a causar problemas, o serviço de agente dc pode ser imediatamente desligado. O filtro de senha do agente DC ainda tenta ligar para o serviço de não execução e irá registar eventos de alerta (10012, 10013), mas todas as senhas de entrada são aceites durante esse tempo. O serviço de agente DC também pode ser configurado através do Gestor de Controlo de Serviços do Windows com um tipo de arranque de "Desativado" conforme necessário.

Outra medida de reparação seria definir o modo Activato para Não no portal de proteção de passwords Azure AD. Uma vez que a política atualizada tenha sido descarregada, cada serviço de agente DC entrará num modo quiescente onde todas as palavras-passe são aceites como é. Para mais informações, consulte [modos de funcionamento](howto-password-ban-bad-on-premises-operations.md#modes-of-operation).

## <a name="removal"></a>Remoção

Se for decidido desinstalar o software de proteção de senhas Azure AD e limpar todo o estado relacionado a partir do domínio ou floresta, esta tarefa pode ser realizada utilizando os seguintes passos:

> [!IMPORTANT]
> É importante executar estes passos em ordem. Se alguma instância do serviço Proxy for deixada em funcionamento, irá recriar periodicamente o seu objeto de serviçoConnectionPoint. Se alguma instância do serviço de agente DC for deixada em funcionamento, irá periodicamente recriar o seu objeto de serviçoConnectionPoint e o estado sysvol.

1. Desinstale o software Proxy de todas as máquinas. Este passo **não** requer um reboot.
2. Desinstale o software do Agente DC de todos os controladores de domínio. Este passo **requer** um reboot.
3. Remova manualmente todos os pontos de ligação proxy em cada contexto de nomeação de domínio. A localização destes objetos pode ser descoberta com o seguinte comando Ative Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Não omita o asterisco ("*") no final do valor variável $keywords.

   O ou o objeto resultante `Get-ADObject` encontrado através do `Remove-ADObject`comando pode então ser canalizado para, ou apagado manualmente.

4. Remova manualmente todos os pontos de ligação do agente DC em cada contexto de nomeação de domínio. Pode haver um destes objetos por controlador de domínio na floresta, dependendo da largura do software. A localização desse objeto pode ser descoberta com o seguinte comando Ative Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   O ou o objeto resultante `Get-ADObject` encontrado através do `Remove-ADObject`comando pode então ser canalizado para, ou apagado manualmente.

   Não omita o asterisco ("*") no final do valor variável $keywords.

5. Remova manualmente o estado de configuração ao nível da floresta. O estado de configuração da floresta é mantido num recipiente no contexto de nomeação de configuração do Diretório Ativo. Pode ser descoberto e apagado da seguinte forma:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Remova manualmente todo o estado relacionado com sysvol, eliminando manualmente a seguinte pasta e todo o seu conteúdo:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Se necessário, este caminho também pode ser acedido localmente num determinado controlador de domínio; a localização padrão seria algo como o seguinte caminho:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Este caminho é diferente se a parte sysvol tiver sido configurada num local não predefinido.

## <a name="next-steps"></a>Passos seguintes

[Perguntas frequentes para proteção de senha seletiva azure](howto-password-ban-bad-on-premises-faq.md)

Para obter mais informações sobre as listas de senhas banidas globais e personalizadas, consulte o artigo [Ban bad passwords](concept-password-ban-bad.md)
