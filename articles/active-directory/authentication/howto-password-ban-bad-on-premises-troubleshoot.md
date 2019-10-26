---
title: Solução de problemas na proteção de senha do Azure AD-Azure Active Directory
description: Entender a solução de problemas comuns da proteção por senha do Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62395b0b6f1ed152292106a774c1e2f7c6d4f11f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893276"
---
# <a name="azure-ad-password-protection-troubleshooting"></a>Solução de problemas de proteção de senha do Azure AD

Após a implantação da proteção de senha do Azure AD, a solução de problemas pode ser necessária. Este artigo apresenta detalhes para ajudá-lo a entender algumas etapas comuns de solução de problemas.

## <a name="the-dc-agent-cannot-locate-a-proxy-in-the-directory"></a>O agente de controlador de domínio não pode localizar um proxy no diretório

O principal sintoma desse problema é 30017 eventos no log de eventos do administrador do agente de DC.

A causa normal desse problema é que um proxy ainda não foi registrado. Se um proxy tiver sido registrado, pode haver algum atraso devido à latência de replicação do AD até que um agente de controlador de domínio específico possa ver esse proxy.

## <a name="the-dc-agent-is-not-able-to-communicate-with-a-proxy"></a>O agente de controlador de domínio não é capaz de se comunicar com um proxy

O principal sintoma desse problema é 30018 eventos no log de eventos do administrador do agente de DC. Esse problema pode ter várias causas possíveis:

1. O agente de DC está localizado em uma parte isolada da rede que não permite a conectividade de rede com os proxy (s) registrados. Esse problema pode ser benigno, desde que outros agentes de DC possam se comunicar com os proxy para baixar políticas de senha do Azure. Depois de baixado, essas políticas serão obtidas pelo controlador de domínio isolado por meio da replicação dos arquivos de política no compartilhamento SYSVOL.

1. O computador host proxy está bloqueando o acesso ao ponto de extremidade do mapeador de ponto de extremidade RPC (porta 135)

   O instalador de proxy de proteção por senha do Azure AD cria automaticamente uma regra de entrada do firewall do Windows que permite o acesso à porta 135. Se essa regra for excluída ou desabilitada posteriormente, os agentes de DC não poderão se comunicar com o serviço de proxy. Se o firewall interno do Windows tiver sido desabilitado no lugar de outro produto de firewall, você deverá configurar esse firewall para permitir o acesso à porta 135.

1. O computador host proxy está bloqueando o acesso ao ponto de extremidade RPC (dinâmico ou estático) escutado pelo serviço de proxy

   O instalador de proxy de proteção de senha do Azure AD cria automaticamente uma regra de entrada do firewall do Windows que permite o acesso a qualquer porta de entrada escutada pelo serviço de proxy de proteção de senha do Azure AD. Se essa regra for excluída ou desabilitada posteriormente, os agentes de DC não poderão se comunicar com o serviço de proxy. Se o firewall interno do Windows tiver sido desabilitado no lugar de outro produto de firewall, você deverá configurar esse firewall para permitir o acesso a qualquer porta de entrada escutada pelo serviço de proxy de proteção de senha do Azure AD. Essa configuração pode se tornar mais específica se o serviço de proxy tiver sido configurado para escutar em uma porta RPC estática específica (usando o cmdlet `Set-AzureADPasswordProtectionProxyConfiguration`).

1. O computador host proxy não está configurado para permitir que os controladores de domínio possam fazer logon no computador. Esse comportamento é controlado por meio da atribuição de privilégio de usuário "acessar este computador pela rede". Todos os controladores de domínio em todos os domínios na floresta devem receber esse privilégio. Essa configuração geralmente é restrita como parte de um esforço maior de proteção de rede.

## <a name="proxy-service-is-unable-to-communicate-with-azure"></a>O serviço de proxy não pode se comunicar com o Azure

1. Verifique se o computador proxy tem conectividade com os pontos de extremidade listados nos [requisitos de implantação](howto-password-ban-bad-on-premises-deploy.md).

1. Verifique se a floresta e todos os servidores proxy estão registrados no mesmo locatário do Azure.

   Você pode verificar esse requisito executando os cmdlets `Get-AzureADPasswordProtectionProxy` e `Get-AzureADPasswordProtectionDCAgent` PowerShell e, em seguida, comparar a propriedade `AzureTenant` de cada item retornado. Para a operação correta, o nome do locatário relatado deve ser o mesmo em todos os agentes de DC e servidores proxy.

   Se houver uma condição de incompatibilidade de registro de locatário do Azure, esse problema poderá ser corrigido executando o `Register-AzureADPasswordProtectionProxy` e/ou `Register-AzureADPasswordProtectionForest` cmdlets do PowerShell, conforme necessário, certificando-se de usar credenciais do mesmo locatário do Azure para todos os registros.

## <a name="dc-agent-is-unable-to-encrypt-or-decrypt-password-policy-files"></a>O agente de DC não pode criptografar ou descriptografar arquivos de política de senha

A proteção por senha do Azure AD tem uma dependência crítica da funcionalidade de criptografia e descriptografia fornecida pelo serviço de distribuição de chaves da Microsoft. Falhas de criptografia ou descriptografia podem ser manifestadas com vários sintomas e têm várias causas potenciais.

1. Verifique se o serviço KDS está habilitado e funcional em todos os controladores de domínio do Windows Server 2012 e posteriores em um domínio.

   Por padrão, o modo de início do serviço do serviço KDS é configurado como manual (início do gatilho). Essa configuração significa que, na primeira vez que um cliente tenta usar o serviço, ele é iniciado sob demanda. Esse modo de início de serviço padrão é aceitável para que a proteção de senha do Azure AD funcione.

   Se o modo de início do serviço KDS tiver sido configurado para desabilitado, essa configuração deverá ser corrigida antes que a proteção de senha do Azure AD funcione corretamente.

   Um teste simples para esse problema é iniciar manualmente o serviço KDS, seja pelo console do MMC de gerenciamento de serviços ou usando outras ferramentas de gerenciamento (por exemplo, execute "net start kdssvc" em um console de prompt de comando). Espera-se que o serviço KDS seja iniciado com êxito e permaneça em execução.

   A causa raiz mais comum para o serviço KDS não conseguir iniciar é que o objeto do controlador de domínio Active Directory está localizado fora da UO dos controladores de domínio padrão. Essa configuração não é suportada pelo serviço KDS e não é uma limitação imposta pela proteção de senha do Azure AD. A correção para essa condição é mover o objeto do controlador de domínio para um local na UO Controladores de domínio padrão.

1. Alteração de formato de buffer criptografado KDS incompatível do Windows Server 2012 R2 para o Windows Server 2016

   Uma correção de segurança KDS foi introduzida no Windows Server 2016 que modifica o formato dos buffers criptografados KDS; às vezes, esses buffers falharão ao descriptografar no Windows Server 2012 e no Windows Server 2012 R2. A direção inversa é um problema-os buffers que são criptografados KDS no Windows Server 2012 e no Windows Server 2012 R2 sempre serão descriptografados com êxito no Windows Server 2016 e posterior. Se os controladores de domínio em seus domínios de Active Directory estiverem executando uma combinação desses sistemas operacionais, falhas ocasionais de descriptografia de proteção de senha do Azure AD poderão ser relatadas. Não é possível prever com precisão o tempo ou os sintomas dessas falhas devido à natureza da correção de segurança e, Considerando que ela não é determinística, qual agente de DC da proteção de senha do Azure AD em que controlador de domínio criptografará os dados em um determinado momento.

   A Microsoft está investigando uma correção para esse problema, mas nenhum ETA está disponível ainda. Enquanto isso, não há nenhuma solução alternativa para esse problema além de não executar uma combinação desses sistemas operacionais incompatíveis em seus domínios de Active Directory. Em outras palavras, você deve executar somente controladores de domínio do Windows Server 2012 e do Windows Server 2012 R2 ou deve executar somente os controladores de domínio do Windows Server 2016 e superior.

## <a name="weak-passwords-are-being-accepted-but-should-not-be"></a>Senhas fracas estão sendo aceitas, mas não devem ser

Esse problema pode ter várias causas.

1. Seus agentes de DC estão executando uma versão de software de visualização pública que expirou. Veja se o [software do agente DC da visualização pública expirou](howto-password-ban-bad-on-premises-troubleshoot.md#public-preview-dc-agent-software-has-expired).

1. Seus agentes de DC não podem baixar uma política ou não é possível descriptografar políticas existentes. Verifique as possíveis causas nos tópicos acima.

1. O modo de aplicação de política de senha ainda está definido como auditoria. Se essa configuração estiver em vigor, reconfigure-a para impor usando o portal de proteção de senha do Azure AD. Consulte [habilitar a proteção por senha](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. A política de senha foi desabilitada. Se essa configuração estiver em vigor, reconfigure-a para habilitada usando o portal de proteção de senha do Azure AD. Consulte [habilitar a proteção por senha](howto-password-ban-bad-on-premises-operations.md#enable-password-protection).

1. Você não instalou o software do agente DC em todos os controladores de domínio no domínio. Nessa situação, é difícil garantir que os clientes remotos do Windows tenham como destino um determinado controlador de domínio durante uma operação de alteração de senha. Se você acredita que tenha direcionado com êxito um determinado controlador de domínio em que o software do agente do DC está instalado, você pode verificar verificando novamente o log de eventos do administrador do agente de DC: independentemente do resultado, haverá pelo menos um evento para documentar o resultado da senha confirmação. Se não houver nenhum evento presente para o usuário cuja senha seja alterada, a alteração de senha provavelmente será processada por um controlador de domínio diferente.

   Como um teste alternativo, tente setting\changing senhas enquanto estiver conectado diretamente a um controlador de domínio em que o software do agente DC está instalado. Essa técnica não é recomendada para domínios de Active Directory de produção.

   Embora a implantação incremental do software do agente de DC tenha suporte para essas limitações, a Microsoft recomenda enfaticamente que o software do agente de DC esteja instalado em todos os controladores de domínio em um domínio assim que possível.

1. O algoritmo de validação de senha pode realmente estar funcionando conforme o esperado. Veja [como as senhas são avaliadas](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="ntdsutilexe-fails-to-set-a-weak-dsrm-password"></a>O Ntdsutil. exe falha ao definir uma senha de DSRM fraca

Active Directory sempre validará uma nova senha do modo de reparo dos serviços de diretório para garantir que ele atenda aos requisitos de complexidade de senha do domínio; essa validação também chama as DLLs de filtro de senha, como a proteção de senha do Azure AD. Se a nova senha do DSRM for rejeitada, os seguintes resultados da mensagem de erro:

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

Quando a proteção de senha do Azure AD registra os eventos de log de eventos de validação de senha para uma senha Active Directory DSRM, espera-se que as mensagens do log de eventos não incluam um nome de usuário. Esse comportamento ocorre porque a conta DSRM é uma conta local que não faz parte do domínio Active Directory real.  

## <a name="domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password"></a>Falha na promoção da réplica do controlador de domínio devido a uma senha fraca de DSRM

Durante o processo de promoção de DC, a nova senha do modo de reparo dos serviços de diretório será enviada a um DC existente no domínio para validação. Se a nova senha do DSRM for rejeitada, os seguintes resultados da mensagem de erro:

```powershell
Install-ADDSDomainController : Verification of prerequisites for Domain Controller promotion failed. The Directory Services Restore Mode password does not meet a requirement of the password filter(s). Supply a suitable password.
```

Assim como no problema acima, qualquer evento de resultado de validação de senha de proteção de senha do Azure AD terá nomes de usuário vazios para esse cenário.

## <a name="domain-controller-demotion-fails-due-to-a-weak-local-administrator-password"></a>O rebaixamento do controlador de domínio falha devido a uma senha de administrador local fraca

Há suporte para rebaixar um controlador de domínio que ainda está executando o software do agente do DC. No entanto, os administradores devem estar cientes de que o software do agente DC continua impõem a política de senha atual durante o procedimento de rebaixamento. A nova senha da conta de administrador local (especificada como parte da operação de rebaixamento) é validada como qualquer outra senha. A Microsoft recomenda que as senhas seguras sejam escolhidas para contas de administrador local como parte de um procedimento de rebaixamento de DC.

Depois que o rebaixamento for bem-sucedido e o controlador de domínio tiver sido reinicializado e estiver novamente sendo executado como um servidor membro normal, o software do agente DC será revertido para executado em um modo passivo. Em seguida, ele pode ser desinstalado a qualquer momento.

## <a name="booting-into-directory-services-repair-mode"></a>Inicializando no modo de reparo dos serviços de diretório

Se o controlador de domínio for inicializado no modo de reparo de serviços de diretório, a DLL de filtro de senha do agente DC detectará essa condição e fará com que todas as atividades de validação ou de imposição de senha sejam desabilitadas, independentemente da política ativa atualmente configuração. A DLL de filtro de senha do agente de DC registrará um evento de aviso 10023 no log de eventos do administrador, por exemplo:

```text
The password filter dll is loaded but the machine appears to be a domain controller that has been booted into Directory Services Repair Mode. All password change and set requests will be automatically approved. No further messages will be logged until after the next reboot.
```
## <a name="public-preview-dc-agent-software-has-expired"></a>O software do agente DC da visualização pública expirou

Durante o período de visualização pública da proteção de senha do Azure AD, o software do agente de DC estava embutido em código para interromper o processamento de solicitações de validação de senha nas seguintes datas:

* A versão 1.2.65.0 interromperá o processamento de solicitações de validação de senha em setembro de 1 2019.
* A versão 1.2.25.0 e anterior interrompeu o processamento de solicitações de validação de senha em julho de 1 2019.

À medida que o prazo se aproximar, todas as versões de agente DC com tempo limitado emitirão um evento 10021 no log de eventos do administrador do agente de DC no momento da inicialização que tem esta aparência:

```text
The password filter dll has successfully loaded and initialized.

The allowable trial period is nearing expiration. Once the trial period has expired, the password filter dll will no longer process passwords. Please contact Microsoft for an newer supported version of the software.

Expiration date:  9/01/2019 0:00:00 AM

This message will not be repeated until the next reboot.
```

Depois que o prazo tiver passado, todas as versões de agente DC com limitação de tempo emitirão um evento 10022 no log de eventos do administrador do agente de DC no momento da inicialização que tem esta aparência:

```text
The password filter dll is loaded but the allowable trial period has expired. All password change and set requests will be automatically approved. Please contact Microsoft for a newer supported version of the software.

No further messages will be logged until after the next reboot.
```

Como o prazo final é verificado apenas na inicialização inicial, você poderá não ver esses eventos até o tempo decorrido do prazo do calendário. Depois que o prazo for reconhecido, nenhum efeito negativo no controlador de domínio ou no ambiente maior ocorrerá além de todas as senhas que serão aprovadas automaticamente.

> [!IMPORTANT]
> A Microsoft recomenda que os agentes de DC da visualização pública expirados sejam atualizados imediatamente para a versão mais recente.

Uma maneira fácil de descobrir agentes de DC em seu ambiente que precisam ser atualizados é executando o cmdlet `Get-AzureADPasswordProtectionDCAgent`, por exemplo:

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

Para este tópico, o campo SoftwareVersion é, obviamente, a propriedade de chave a ser examinada. Você também pode usar a filtragem do PowerShell para filtrar agentes de DC que já estão na versão de linha de base necessária, por exemplo:

```powershell
PS C:\> $LatestAzureADPasswordProtectionVersion = "1.2.125.0"
PS C:\> Get-AzureADPasswordProtectionDCAgent | Where-Object {$_.SoftwareVersion -lt $LatestAzureADPasswordProtectionVersion}
```

O software de proxy de proteção de senha do Azure AD não é limitado por tempo em nenhuma versão. A Microsoft ainda recomenda que ambos os agentes de DC e proxy sejam atualizados para as versões mais recentes à medida que são lançados. O cmdlet `Get-AzureADPasswordProtectionProxy` pode ser usado para localizar agentes de proxy que exigem atualizações, semelhante ao exemplo acima para agentes de DC.

Consulte [atualizando o agente de DC](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-dc-agent) e [atualizando o agente de proxy](howto-password-ban-bad-on-premises-deploy.md#upgrading-the-proxy-agent) para obter mais detalhes sobre os procedimentos de atualização específicos.

## <a name="emergency-remediation"></a>Correção de emergência

Se ocorrer uma situação em que o serviço do agente DC está causando problemas, o serviço do agente DC poderá ser desligado imediatamente. A DLL de filtro de senha do agente de DC ainda tenta chamar o serviço que não está em execução e registrará eventos de aviso (10012, 10013), mas todas as senhas de entrada são aceitas durante esse tempo. O serviço de agente de controlador de domínio também pode ser configurado por meio do Gerenciador de controle de serviço do Windows com um tipo de inicialização "desabilitado", conforme necessário.

Outra medida de correção seria definir o modo de habilitação como não no portal de proteção de senha do Azure AD. Depois que a política atualizada for baixada, cada serviço de agente de DC entrará em um modo inativo onde todas as senhas são aceitas no estado em que se encontram. Para obter mais informações, consulte [impor modo](howto-password-ban-bad-on-premises-operations.md#enforce-mode).

## <a name="removal"></a>Removidos

Se for decidido desinstalar o software de proteção de senha do Azure AD e limpar todo o estado relacionado dos domínios e da floresta, essa tarefa poderá ser realizada usando as seguintes etapas:

> [!IMPORTANT]
> É importante executar essas etapas na ordem. Se qualquer instância do serviço de proxy for deixada em execução, ela recriará periodicamente seu objeto serviceConnectionPoint. Se qualquer instância do serviço de agente de DC for deixada em execução, ela recriará periodicamente seu objeto serviceConnectionPoint e o estado SYSVOL.

1. Desinstale o software proxy de todos os computadores. Esta etapa **não** requer uma reinicialização.
2. Desinstale o software do agente de DC de todos os controladores de domínio. Esta etapa **requer** uma reinicialização.
3. Remova manualmente todos os pontos de conexão de serviço de proxy em cada contexto de nomenclatura de domínio. O local desses objetos pode ser descoberto com o seguinte comando Active Directory PowerShell:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{ebefb703-6113-413d-9167-9f8dd4d24468}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Não omita o asterisco ("*") no final do valor da variável $keywords.

   Os objetos resultantes encontrados por meio do comando `Get-ADObject` podem ser canalizados para `Remove-ADObject`ou excluídos manualmente.

4. Remova manualmente todos os pontos de conexão do agente DC em cada contexto de nomenclatura de domínio. Pode haver um desses objetos por controlador de domínio na floresta, dependendo de quanto o software foi implantado. O local desse objeto pode ser descoberto com o seguinte comando do PowerShell Active Directory:

   ```powershell
   $scp = "serviceConnectionPoint"
   $keywords = "{2bac71e6-a293-4d5b-ba3b-50b995237946}*"
   Get-ADObject -SearchScope Subtree -Filter { objectClass -eq $scp -and keywords -like $keywords }
   ```

   Os objetos resultantes encontrados por meio do comando `Get-ADObject` podem ser canalizados para `Remove-ADObject`ou excluídos manualmente.

   Não omita o asterisco ("*") no final do valor da variável $keywords.

5. Remova manualmente o estado de configuração no nível da floresta. O estado de configuração da floresta é mantido em um contêiner no contexto de nomenclatura da configuração Active Directory. Ele pode ser descoberto e excluído da seguinte maneira:

   ```powershell
   $passwordProtectionConfigContainer = "CN=Azure AD Password Protection,CN=Services," + (Get-ADRootDSE).configurationNamingContext
   Remove-ADObject -Recursive $passwordProtectionConfigContainer
   ```

6. Remova manualmente todos os Estados relacionados ao SYSVOL excluindo manualmente a seguinte pasta e todo o seu conteúdo:

   `\\<domain>\sysvol\<domain fqdn>\AzureADPasswordProtection`

   Se necessário, esse caminho também pode ser acessado localmente em um determinado controlador de domínio; o local padrão seria algo semelhante ao seguinte caminho:

   `%windir%\sysvol\domain\Policies\AzureADPasswordProtection`

   Esse caminho será diferente se o compartilhamento de SYSVOL tiver sido configurado em um local não padrão.

## <a name="next-steps"></a>Passos seguintes

[Perguntas frequentes sobre a proteção de senha do Azure AD](howto-password-ban-bad-on-premises-faq.md)

Para obter mais informações sobre as listas de senhas banidas globais e personalizadas, consulte o artigo [proibir senhas ruins](concept-password-ban-bad.md)
