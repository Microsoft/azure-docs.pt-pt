---
title: Resolver problemas de sincronização de hash de palavra-passe com o Azure AD Connect sync | Documentos da Microsoft
description: Este artigo fornece informações sobre como solucionar problemas de sincronização de hash de palavra-passe.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56fda1110218910f8fbd8aa9597195f37444e01c
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57193335"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Resolver problemas de sincronização de hash de palavra-passe com a sincronização do Azure AD Connect
Este tópico fornece os passos sobre como resolver problemas com a sincronização de hash de palavra-passe. Se as palavras-passe não estão sincronizado como esperado, pode ser para um subconjunto de utilizadores ou para todos os utilizadores.

Para a implementação do Azure Active Directory (Azure AD) Connect com a versão 1.1.614.0 ou depois, utilize a tarefa de resolução de problemas no Assistente para resolver problemas de sincronização de hash de palavra-passe:

* Se tiver um problema onde sem palavras-passe são sincronizadas, consulte a [sem palavras-passe são sincronizadas: resolver problemas utilizando a tarefa de resolução de problemas](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) secção.

* Se tiver um problema com objetos individuais, consulte a [um objeto não está a sincronizar as palavras-passe: resolver problemas utilizando a tarefa de resolução de problemas](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) secção.

Para a implementação com a versão 1.1.524.0 ou posterior, existe um cmdlet de diagnóstico que pode utilizar para resolver problemas de sincronização de hash de palavra-passe:

* Se tiver um problema onde sem palavras-passe são sincronizadas, consulte a [sem palavras-passe são sincronizadas: resolver problemas com o cmdlet de diagnóstico](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet) secção.

* Se tiver um problema com objetos individuais, consulte a [um objeto não está a sincronizar as palavras-passe: resolver problemas com o cmdlet de diagnóstico](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet) secção.

Para versões mais antigas de implementação do Azure AD Connect:

* Se tiver um problema onde sem palavras-passe são sincronizadas, consulte a [sem palavras-passe são sincronizadas: manual passos de resolução de problemas](#no-passwords-are-synchronized-manual-troubleshooting-steps) secção.

* Se tiver um problema com objetos individuais, consulte a [um objeto não está a sincronizar as palavras-passe: manual passos de resolução de problemas](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) secção.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Sem palavras-passe são sincronizadas: resolver problemas utilizando a tarefa de resolução de problemas
Pode utilizar a tarefa de resolução de problemas para descobrir por que motivo sem palavras-passe são sincronizadas.

> [!NOTE]
> A tarefa de resolução de problemas está disponível apenas para o Azure AD Connect versão 1.1.614.0 ou posterior.

### <a name="run-the-troubleshooting-task"></a>Executar a tarefa de resolução de problemas
Para resolver problemas em que são sincronizadas sem palavras-passe:

1. Abra uma nova sessão do Windows PowerShell no seu servidor do Azure AD Connect com o **executar como administrador** opção.

2. Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Inicie o Assistente do Azure AD Connect.

4. Navegue para o **tarefas adicionais** página, selecione **resolução de problemas**e clique em **seguinte**.

5. Na página de resolução de problemas, clique em **inicie** para iniciar o menu de resolução de problemas no PowerShell.

6. No menu principal, selecione **resolver problemas de sincronização de hash de palavra-passe**.

7. No menu do sub, selecione **sincronização de hash de palavra-passe não funciona em todos os**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Compreender os resultados da tarefa de resolução de problemas
A tarefa de resolução de problemas realiza as seguintes verificações:

* Valida que a funcionalidade de sincronização de hash de palavra-passe está ativada para o seu inquilino do Azure AD.

* Valida que o servidor do Azure AD Connect não está no modo de teste.

* Para cada existente no local Active Directory connector (que corresponde a uma floresta existente do Active Directory):

   * Valida que a funcionalidade de sincronização de hash de palavra-passe está ativada.
   
   * Pesquisas de palavra-passe de hash eventos de heartbeat de sincronização nos logs de eventos de aplicativo do Windows.

   * Para cada domínio do Active Directory no conector do Active Directory no local:

      * Valida que o domínio está acessível a partir do servidor do Azure AD Connect.

      * Valida que as contas de serviços de domínio do Active Directory (AD DS) utilizadas pelo conector do Active Directory no local tem o nome de utilizador correto, senha e as permissões necessárias para a sincronização de hash de palavra-passe.

O diagrama seguinte ilustra os resultados do cmdlet para uma topologia do domínio único, no local do Active Directory:

![Resultados de diagnóstico para a sincronização de hash de palavra-passe](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

O resto desta secção descreve os resultados específicos que são devolvidos pelas tarefas e problemas correspondentes.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>recurso de sincronização de hash de palavra-passe não está ativado
Se ainda não ativou a sincronização de hash de palavra-passe utilizando o Assistente do Azure AD Connect, é devolvido o erro seguinte:

![sincronização de hash de palavra-passe não está ativada](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>Servidor do Azure AD Connect está no modo de teste
Se o servidor do Azure AD Connect está no modo de teste, sincronização de hash de palavra-passe está temporariamente desativada e, é devolvido o erro seguinte:

![Servidor do Azure AD Connect está no modo de teste](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Não existem eventos de heartbeat de sincronização de hash de palavra-passe
Cada conector do Active Directory no local tem seu próprio canal de sincronização de hash de palavra-passe. Quando o canal de sincronização de hash de palavra-passe é estabelecido e não há qualquer alteração de palavra-passe sejam sincronizadas, uma vez a cada 30 minutos sob o registo de eventos do aplicativo do Windows é gerado um evento de heartbeat (EventId 654). Para cada conector do Active Directory no local, o cmdlet procura os eventos de heartbeat correspondentes nas últimas três horas. Se não for encontrado nenhum evento de heartbeat, é devolvido o erro seguinte:

![Evento de atingir não núcleo de sincronização de hash de palavra-passe](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>Conta do AD DS não tem permissões corretas
Se a conta do AD DS que é utilizada pelo conector do Active Directory no local para sincronizar os hashes de palavra-passe não tiver as permissões adequadas, é devolvido o erro seguinte:

![Credenciais incorretas](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nome de utilizador ou palavra-passe da conta incorretas AD DS
Se a conta de AD DS utilizada pelo conector do Active Directory no local para sincronizar os hashes de palavra-passe tem um nome de utilizador incorreta ou a palavra-passe, é devolvido o erro seguinte:

![Credenciais incorretas](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Um objeto não está a sincronizar as palavras-passe: resolver problemas utilizando a tarefa de resolução de problemas

Pode utilizar a tarefa de resolução de problemas para determinar por que um objeto não está a sincronizar as palavras-passe.

> [!NOTE]
> A tarefa de resolução de problemas está disponível apenas para o Azure AD Connect versão 1.1.614.0 ou posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Execute o cmdlet de diagnóstico
Para resolver problemas de um objeto de utilizador específico:

1. Abra uma nova sessão do Windows PowerShell no seu servidor do Azure AD Connect com o **executar como administrador** opção.

2. Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Inicie o Assistente do Azure AD Connect.

4. Navegue para o **tarefas adicionais** página, selecione **resolução de problemas**e clique em **seguinte**.

5. Na página de resolução de problemas, clique em **inicie** para iniciar o menu de resolução de problemas no PowerShell.

6. No menu principal, selecione **resolver problemas de sincronização de hash de palavra-passe**.

7. No menu do sub, selecione **palavra-passe não está sincronizado para uma conta de utilizador específico**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Compreender os resultados da tarefa de resolução de problemas
A tarefa de resolução de problemas realiza as seguintes verificações:

* Examina o estado do objeto do Active Directory no espaço conector do Active Directory, Metaverso e Azure espaço conector do AD.

* Valida que existem regras de sincronização com a sincronização de hash de palavra-passe ativada e aplicadas ao objeto do Active Directory.

* Tenta recuperar e exibir os resultados da última tentativa para sincronizar a palavra-passe para o objeto.

O diagrama seguinte ilustra os resultados do cmdlet quando resolver problemas de sincronização de hash de palavra-passe para um único objeto:

![Resultados de diagnóstico para a sincronização de hash de palavra-passe - único objeto](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

O resto desta secção descreve os resultados específicos devolvidos pelo cmdlet e problemas correspondentes.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>O objeto do Active Directory não é exportado para o Azure AD
sincronização de hash de palavra-passe para esta conta do Active Directory no local não consegue porque não existe nenhum objeto correspondente no inquilino do Azure AD. É devolvido o erro seguinte:

![Objetos do Azure AD está em falta](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Utilizador tem uma palavra-passe temporária
Atualmente, o Azure AD Connect não suporta a sincronização de palavras-passe temporárias com o Azure AD. Uma palavra-passe é considerada como estando temporário se a **alterar palavra-passe no próximo logon** opção está definida no utilizador do Active Directory no local. É devolvido o erro seguinte:

![Não é exportada a palavra-passe temporária](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Resultados da última tentativa de sincronização de palavra-passe não estão disponíveis
Por predefinição, o Azure AD Connect armazena os resultados de tentativas de sincronização de hash de palavra-passe durante sete dias. Se não existirem não existem resultados disponíveis para o objeto selecionado do Active Directory, é devolvido o seguinte aviso:

![Resultados de diagnóstico para o objeto único - nenhum histórico de sincronização de palavra-passe](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Sem palavras-passe são sincronizadas: resolver problemas com o cmdlet de diagnóstico
Pode utilizar o `Invoke-ADSyncDiagnostics` cmdlet para descobrir por que motivo sem palavras-passe são sincronizadas.

> [!NOTE]
> O `Invoke-ADSyncDiagnostics` cmdlet está disponível apenas para o Azure AD Connect na versão 1.1.524.0 ou posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Execute o cmdlet de diagnóstico
Para resolver problemas em que são sincronizadas sem palavras-passe:

1. Abra uma nova sessão do Windows PowerShell no seu servidor do Azure AD Connect com o **executar como administrador** opção.

2. Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Execute `Import-Module ADSyncDiagnostics`.

4. Execute `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Um objeto não está a sincronizar as palavras-passe: resolver problemas com o cmdlet de diagnóstico
Pode utilizar o `Invoke-ADSyncDiagnostics` cmdlet para determinar por que um objeto não está a sincronizar as palavras-passe.

> [!NOTE]
> O `Invoke-ADSyncDiagnostics` cmdlet está disponível apenas para o Azure AD Connect na versão 1.1.524.0 ou posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Execute o cmdlet de diagnóstico
Para resolver problemas de onde sem palavras-passe são sincronizadas para um utilizador:

1. Abra uma nova sessão do Windows PowerShell no seu servidor do Azure AD Connect com o **executar como administrador** opção.

2. Execute `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Execute `Import-Module ADSyncDiagnostics`.

4. Execute o seguinte cmdlet:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```
   Por exemplo:
   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Sem palavras-passe são sincronizadas: manual passos de resolução de problemas
Siga estes passos para determinar por que são sincronizadas sem palavras-passe:

1. For o Connect [modo de teste](how-to-connect-sync-staging-server.md)? Um servidor no modo de teste não sincroniza as palavras-passe.

2. Executar o script a [obter o estado das definições de sincronização de palavra-passe](#get-the-status-of-password-sync-settings) secção. Ele fornece uma descrição geral da configuração de sincronização de palavra-passe.  

    ![Saída do script do PowerShell de definições de sincronização de palavra-passe](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Se a funcionalidade não está ativada no Azure AD, ou se o estado de canal de sincronização não estiver ativado, execute o Assistente de instalação do Connect. Selecione **personalizar as opções de sincronização**e anule a seleção de sincronização de palavra-passe. Esta alteração desativa temporariamente a funcionalidade. Em seguida, execute novamente o assistente e volte a ativar a sincronização de palavra-passe. Execute o script novamente para verificar se a configuração está correta.

4. Procure no registo de eventos para erros. Procure os eventos seguintes, que poderia indicar um problema:
    * Origem: ID de "Sincronização de diretórios": 0, 611, 652, 655 se vir estes eventos, tem um problema de conectividade. A mensagem de registo de eventos contém informações de floresta em que tem um problema. Para obter mais informações, consulte [problema de conectividade](#connectivity problem).

5. Não se vir nenhum heartbeat ou se nada mais funciona, execute [acionar uma sincronização completa de todas as senhas](#trigger-a-full-sync-of-all-passwords). Execute o script apenas uma vez.

6. Ver o objeto de uma resolução de problemas que não está a sincronizar a secção de palavras-passe.

### <a name="connectivity-problems"></a>Problemas de conectividade

Tem conectividade com o Azure AD?

A conta tem permissões necessárias para ler os hashes de palavra-passe em todos os domínios? Se tiver instalado o Connect utilizando as definições rápidas, as permissões já devem estar corretas. 

Se utilizou uma instalação personalizada, defina as permissões manualmente ao fazer o seguinte:
    
1. Para localizar a conta utilizada pelo conector do Active Directory, começa **Synchronization Service Manager**. 
 
2. Aceda a **conectores**e, em seguida, procure a floresta do Active Directory no local que esteja a resolver. 
 
3. Selecione o conector e, em seguida, clique em **propriedades**. 
 
4. Aceda a **ligar à floresta do Active Directory**.  
    
    ![Conta utilizada pelo conector do Active Directory](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Tenha em atenção o nome de utilizador e o domínio onde está localizada a conta.
    
5. Inicie **Active Directory Users and Computers**e, em seguida, certifique-se de que a conta encontrada anteriormente tem as permissões de siga definida na raiz de todos os domínios na sua floresta:
    * Replicar as alterações de diretório
    * Diretório de replicar muda tudo

6. Os controladores de domínio são acessíveis pelo Azure AD Connect? Se o servidor do Connect não é possível ligar a todos os controladores de domínio, configure **utilizar apenas o controlador de domínio preferencial**.  
    
    ![Controlador de domínio utilizado pelo conector do Active Directory](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  
    
7. Volte ao **Synchronization Service Manager** e **configurar a partição de diretório**. 
 
8. Selecione o seu domínio no **selecionar partições de diretório**, selecione a **utilizar apenas controladores de domínio preferencial** caixa de verificação e, em seguida, clique em **configurar**. 

9. Na lista, introduza os controladores de domínio que Connect deve utilizar para sincronização de palavra-passe. A mesma lista é utilizada para a importação e exportação também. Realize estes passos para todos os seus domínios.

10. Se o script mostra que não existe nenhum heartbeat, execute o script no [acionar uma sincronização completa de todas as senhas](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Um objeto não está a sincronizar as palavras-passe: manual passos de resolução de problemas
Pode facilmente resolver problemas de sincronização de hash de palavra-passe ao rever o estado de um objeto.

1. Na **Active Directory Users and Computers**, procure o utilizador e, em seguida, certifique-se de que o **o utilizador deve alterar a palavra-passe no próximo início de sessão** caixa de verificação está desmarcada.  

    ![Active Directory produtivas palavras-passe](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Se a caixa de verificação está selecionada, peça ao utilizador para iniciar sessão e alterar a palavra-passe. Palavras-passe temporárias não são sincronizadas com o Azure AD.

2. Se a palavra-passe parecer correta no Active Directory, siga o utilizador no motor de sincronização. Ao seguir o utilizador do Active Directory no local para o Azure AD, pode ver se existe um erro de descritivo no objeto.

    a. Iniciar o [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md).

    b. Clique em **conectores**.

    c. Selecione o **conector do Active Directory** onde o utilizador está localizado.

    d. Selecione **procurar espaço conector**.

    e. Na **âmbito** caixa, selecione **DN ou âncora**e, em seguida, introduza o DN completo do utilizador que esteja a resolver.

    ![Procurar por utilizador no espaço conector com o DN](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Localizar o utilizador que está procurando e, em seguida, clique em **propriedades** para ver todos os atributos. Se o utilizador não está a ser o resultado da pesquisa, verifique se sua [regras de filtragem](how-to-connect-sync-configure-filtering.md) e certifique-se de que execute [aplicar e verificar as alterações](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) para o utilizador sejam apresentadas no Connect.

    g. Para ver os detalhes de sincronização de palavra-passe do objeto da semana passada, clique em **Log**.  

    ![Detalhes de registo do objeto](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Se o registo de objeto estiver vazio, o Azure AD Connect foi não é possível ler o hash de palavra-passe do Active Directory. Continue a sua solução de problemas com erros de conectividade. Se vir de qualquer outro valor que **sucesso**, consulte a tabela na [log de sincronização de palavra-passe](#password-sync-log).

    h. Selecione o **linhagem** separador e certificar-se de que essa regra de sincronização, pelo menos, um da **PasswordSync** coluna é **verdadeiro**. A configuração predefinida, o nome da regra de sincronização é **do AD - utilizador AccountEnabled**.  

    ![Informações de linhagem sobre um utilizador](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Clique em **propriedades do objeto de Metaverso** para apresentar uma lista de atributos do utilizador.  

    ![Informações de Metaverso](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Certifique-se de que não há nenhuma **cloudFiltered** existovat atribut. Certifique-se de que os atributos de domínio (domainFQDN e domainNetBios) tem os valores esperados.

    j. Clique nas **conectores** separador. Certifique-se de que vê conectores para o Active Directory no local e o Azure AD.

    ![Informações de Metaverso](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Selecione a linha que representa o Azure AD, clique em **propriedades**e, em seguida, clique nas **linhagem** separador. O objeto de espaço conector deve ter uma regra de saída no **PasswordSync** coluna definida como **verdadeiro**. A configuração predefinida, o nome da regra de sincronização é **expansão para AAD - associação de utilizador**.  

    ![Caixa de diálogo de propriedades do objeto de espaço conector](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Log de sincronização de palavra-passe
A coluna de estado pode ter os seguintes valores:

| Estado | Descrição |
| --- | --- |
| Êxito |Palavra-passe foram sincronizado com êxito. |
| FilteredByTarget |Palavra-passe está definido como **o utilizador deve alterar a palavra-passe no próximo início de sessão**. Palavra-passe não foi sincronizada. |
| NoTargetConnection |Nenhum objeto no metaverse ou no espaço conector do Azure AD. |
| SourceConnectorNotPresent |Nenhum objeto encontrado no espaço de conector do Active Directory no local. |
| TargetNotExportedToDirectory |O objeto no espaço conector do Azure AD ainda não foram exportado. |
| MigratedCheckDetailsForMoreInfo |Entrada de registo foi criada antes da compilação 1.0.9125.0 e é apresentada no respetivo estado herdado. |
| Erro |Serviço devolveu um erro desconhecido. |
| Desconhecidos |Ocorreu um erro ao tentar processar um lote de hashes de palavra-passe.  |
| MissingAttribute |Atributos específicos (por exemplo, o hash de Kerberos) exigidos pelo Azure AD Domain Services não estão disponíveis. |
| RetryRequestedByTarget |Atributos específicos (por exemplo, o hash de Kerberos) exigidos pelo Azure AD Domain Services não estavam disponíveis anteriormente. É efetuada uma tentativa para ressincronizar o hash de palavra-passe do utilizador. |

## <a name="scripts-to-help-troubleshooting"></a>Scripts para o ajudar a resolução de problemas

### <a name="get-the-status-of-password-sync-settings"></a>Obter o estado das definições de sincronização de palavra-passe
```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Acionar uma sincronização completa de todas as senhas
> [!NOTE]
> Execute este script apenas uma vez. Se tiver de executar mais do que uma vez, outra coisa é o problema. Para resolver o problema, contacte o suporte da Microsoft.

Pode acionar uma sincronização completa de todas as senhas usando o script seguinte:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Passos Seguintes
* [Implementar a sincronização de hash de palavra-passe com a sincronização do Azure AD Connect](how-to-connect-password-hash-synchronization.md)
* [Sincronização do Azure AD Connect: Personalizando opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
