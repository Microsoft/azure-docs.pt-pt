---
title: Sincronização de hash de palavra-passe de troubleshoot com sincronização Azure AD Connect / Microsoft Docs
description: Este artigo fornece informações sobre como resolver problemas de sincronização de hash de palavra-passe.
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
ms.openlocfilehash: 6feed11fcfc597658f3ec148b5dd18bb7e3f8f83
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376225"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Sincronização de hash de palavra-passe de resolução de problemas com sincronização azure AD Connect

Este tópico fornece passos para como resolver problemas com sincronização de hash password. Se as palavras-passe não estiverem sincronizadas como esperado, pode ser para um subconjunto de utilizadores ou para todos os utilizadores.

Para o Azure Ative Directory (Azure AD) Ligue a implementação com a versão 1.1.614.0 ou depois, utilize a tarefa de resolução de problemas no assistente para resolver problemas de sincronização de hash:

* Se tiver um problema em que nenhuma palavra-passe seja sincronizada, consulte as [palavras-passe Sem são sincronizadas: resolução](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task) de problemas utilizando a secção de tarefas de resolução de problemas.

* Se tiver algum problema com objetos individuais, consulte o [objeto One não é sincronizar palavras-passe: resolução](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task) de problemas utilizando a secção de tarefas de resolução de problemas.

Para implantação com a versão 1.1.524.0 ou posterior, existe um cmdlet de diagnóstico que pode usar para resolver problemas de sincronização de hash:

* Se tiver um problema em que nenhuma palavra-passe seja sincronizada, consulte as [palavras-passe Sem são sincronizadas: resolução de problemas utilizando a secção de diagnóstico cmdlet.](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet)

* Se tiver algum problema com objetos individuais, consulte o [objeto One não é sincronizar palavras-passe: resolução de problemas utilizando a secção de diagnóstico cmdlet.](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet)

Para versões mais antigas da implantação do Azure AD Connect:

* Se tiver um problema em que nenhuma palavra-passe seja sincronizada, consulte as [palavras-passe Sem palavras-passe sincronizadas:](#no-passwords-are-synchronized-manual-troubleshooting-steps) secção de passos de resolução manual de problemas.

* Se tiver algum problema com objetos individuais, consulte o [objeto One não é sincronizar palavras-passe:](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps) secção de passos de resolução manual de problemas.



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Nenhuma palavra-passe é sincronizada: resolução de problemas usando a tarefa de resolução de problemas

Pode usar a tarefa de resolução de problemas para descobrir por que razão não há palavras-passe sincronizadas.

> [!NOTE]
> A tarefa de resolução de problemas está disponível apenas para a versão 1.1.614.0 do Azure AD Connect.

### <a name="run-the-troubleshooting-task"></a>Executar a tarefa de resolução de problemas

Para resolver problemas em que não há palavras-passe sincronizadas:

1. Abra uma nova sessão do Windows PowerShell no seu servidor Azure AD Connect com a opção **Executar como Administrador.**

2. Executar `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Inicie o assistente Azure AD Connect.

4. Navegue na página **De Tarefas Adicionais,** selecione **Troubleshoot**, e clique **em Next**.

5. Na página Deresolução de Problemas, clique em **Lançar** para iniciar o menu de resolução de problemas no PowerShell.

6. No menu principal, selecione A sincronização de **hash de palavra-passe Desmarcação**de problemas .

7. No menu sub, selecione A sincronização de **hash password não funciona**de todo .

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Compreenda os resultados da tarefa de resolução de problemas

A tarefa de resolução de problemas realiza as seguintes verificações:

* Valida que a funcionalidade de sincronização de hash de palavra-passe está ativada para o seu inquilino Azure AD.

* Valida que o servidor Azure AD Connect não esteja em modo de encenação.

* Para cada conector ativo de diretório existente no local (que corresponde a uma floresta de Diretório Ativo existente):

   * Valida que a função de sincronização de hash de palavra-passe esteja ativada.
   
   * Pesquisas por eventos de batimentocardíaco de sincronização de hash de palavra-passe nos registos do Windows Application Event.

   * Para cada domínio de Diretório Ativo sob o conector ative diretório no local:

      * Valida que o domínio é acessível a partir do servidor Azure AD Connect.

      * Valida que as contas de Serviços de Domínio de Diretório Ativo (AD DS) utilizadas pelo conector Ative Directory no local têm o nome de utilizador, senha e permissões corretas necessárias para a sincronização de hash de palavra-passe.

O diagrama seguinte ilustra os resultados do cmdlet para um único domínio, no local, topologia de diretório ativo:

![Saída de diagnóstico para sincronização de hash de palavra-passe](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

O resto desta secção descreve resultados específicos que são devolvidos pela tarefa e questões correspondentes.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>funcionalidade de sincronização de hash de palavra-passe não está ativada

Se não tiver ativado a sincronização de hash de palavra-passe utilizando o assistente Azure AD Connect, o seguinte erro é devolvido:

![sincronização de hash de palavra-passe não está ativada](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>O servidor Azure AD Connect está em modo de encenação

Se o servidor Azure AD Connect estiver no modo de preparação, a sincronização de hash de palavra-passe é temporariamente desativada e o seguinte erro é devolvido:

![O servidor Azure AD Connect está em modo de encenação](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Sem eventos de batimentocardíaco de sincronização de hash sincronização de palavra-passe

Cada conector ative diretório no local tem o seu próprio canal de sincronização de hash de senha. Quando o canal de sincronização de hash de palavra-passe é estabelecido e não há alterações de senha a sincronizar, um evento de batimentocardíaco (EventId 654) é gerado uma vez a cada 30 minutos no Registo de Eventos de Aplicação do Windows. Para cada conector de Diretório Ativo no local, o cmdlet procura eventos cardíacos correspondentes nas últimas três horas. Se não for encontrado nenhum evento cardíaco, o seguinte erro é devolvido:

![Sem evento de batimento cardíaco de sincronização de hash sincronização de hash](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>A conta AD DS não tem permissões corretas

Se a conta AD DS utilizada pelo conector Ative Directory no local para sincronizar hashes de palavra-passe não tiver as permissões adequadas, o seguinte erro é devolvido:

![Credencial incorreta](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nome de utilizador ou palavra-passe da conta AD DS incorreto

Se a conta AD DS utilizada pelo conector Ative Directory no local para sincronizar hashes de palavra-passe tiver um nome de utilizador ou palavra-passe incorreto, o seguinte erro é devolvido:

![Credencial incorreta](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Um dos objetos não é sincronizar palavras-passe: resolução de problemas usando a tarefa de resolução de problemas

Pode utilizar a tarefa de resolução de problemas para determinar porque é que um objeto não está a sincronizar palavras-passe.

> [!NOTE]
> A tarefa de resolução de problemas está disponível apenas para a versão 1.1.614.0 do Azure AD Connect.

### <a name="run-the-diagnostics-cmdlet"></a>Executar o cmdlet de diagnóstico

Para resolver problemas para um objeto específico do utilizador:

1. Abra uma nova sessão do Windows PowerShell no seu servidor Azure AD Connect com a opção **Executar como Administrador.**

2. Executar `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Inicie o assistente Azure AD Connect.

4. Navegue na página **De Tarefas Adicionais,** selecione **Troubleshoot**, e clique **em Next**.

5. Na página Deresolução de Problemas, clique em **Lançar** para iniciar o menu de resolução de problemas no PowerShell.

6. No menu principal, selecione A sincronização de **hash de palavra-passe Desmarcação**de problemas .

7. No menu sub, selecione **Password não é sincronizado para uma conta de utilizador específica**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Compreenda os resultados da tarefa de resolução de problemas

A tarefa de resolução de problemas realiza as seguintes verificações:

* Examina o estado do objeto de Diretório Ativo no espaço do conector ative diretivo, metaverse e espaço de conector Azure AD.

* Valida que existam regras de sincronização com sincronização de hash de palavra-passe ativada e aplicada ao objeto De Diretório Ativo.

* Tenta recuperar e exibir os resultados da última tentativa de sincronizar a palavra-passe para o objeto.

O diagrama que se segue ilustra os resultados do cmdlet ao resolver a sincronização da palavra-passe para um único objeto:

![Saída de diagnóstico para sincronização de hash de palavra-passe - único objeto](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

O resto desta secção descreve resultados específicos devolvidos pelo cmdlet e questões correspondentes.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>O objeto de Diretório Ativo não é exportado para a AD Azure

a sincronização de hash de palavra-passe para esta conta de Diretório Ativo no local falha porque não existe nenhum objeto correspondente no inquilino da AD Azure. O seguinte erro é devolvido:

![Objeto da AD Azure está desaparecido](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>Utilizador tem uma senha temporária

Atualmente, o Azure AD Connect não suporta sincronizar senhas temporárias com a AD Azure. Uma palavra-passe é considerada temporária se a **palavra-passe Alterar na próxima** opção de início de sessão for definida no utilizador do Diretório Ativo no local. O seguinte erro é devolvido:

![A senha temporária não é exportada](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Os resultados da última tentativa de sincronização da palavra-passe não estão disponíveis

Por padrão, o Azure AD Connect armazena os resultados das tentativas de sincronização de hash por sete dias. Se não houver resultados disponíveis para o objeto de Diretório Ativo selecionado, o seguinte aviso é devolvido:

![Saída de diagnóstico para um único objeto - sem histórico de sincronização de palavra-passe](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Nenhuma palavra-passe é sincronizada: resolução de problemas usando o cmdlet de diagnóstico

Pode utilizar o `Invoke-ADSyncDiagnostics` cmdlet para descobrir por que razão não há palavras-passe sincronizadas.

> [!NOTE]
> O `Invoke-ADSyncDiagnostics` cmdlet está disponível apenas para a versão 1.1.524.0 do Azure AD Connect.

### <a name="run-the-diagnostics-cmdlet"></a>Executar o cmdlet de diagnóstico

Para resolver problemas em que não há palavras-passe sincronizadas:

1. Abra uma nova sessão do Windows PowerShell no seu servidor Azure AD Connect com a opção **Executar como Administrador.**

2. Executar `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Execute `Import-Module ADSyncDiagnostics`.

4. Execute `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Um dos objetos não é sincronizar palavras-passe: resolução de problemas utilizando o cmdlet de diagnóstico

Pode utilizar o `Invoke-ADSyncDiagnostics` cmdlet para determinar porque é que um objeto não está a sincronizar palavras-passe.

> [!NOTE]
> O `Invoke-ADSyncDiagnostics` cmdlet está disponível apenas para a versão 1.1.524.0 do Azure AD Connect.

### <a name="run-the-diagnostics-cmdlet"></a>Executar o cmdlet de diagnóstico

Para resolver problemas em que não há palavras-passe sincronizadas para um utilizador:

1. Abra uma nova sessão do Windows PowerShell no seu servidor Azure AD Connect com a opção **Executar como Administrador.**

2. Executar `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted`.

3. Execute `Import-Module ADSyncDiagnostics`.

4. Execute o seguinte cmdlet:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Por exemplo:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Nenhuma palavra-passe é sincronizada: passos manuais de resolução de problemas

Siga estes passos para determinar por que razão não há palavras-passe sincronizadas:

1. O servidor Connect está em [modo de encenação?](how-to-connect-sync-staging-server.md) Um servidor em modo de encenação não sincroniza quaisquer palavras-passe.

2. Executar o script na secção Obter o estado da secção [de definições de sincronização de palavras-passe.](#get-the-status-of-password-sync-settings) Dá-lhe uma visão geral da configuração da sincronização da palavra-passe.  

    ![Saída de script PowerShell a partir de definições de sincronização de palavras-passe](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Se a funcionalidade não estiver ativada em AD Azure ou se o estado do canal de sincronização não estiver ativado, execute o assistente de instalação Connect. Selecione **Personalizar opções**de sincronização e desseleccionar a sincronização de palavras-passe. Esta alteração desativa temporariamente a funcionalidade. Em seguida, volte a executar o assistente e volte a ativar a sincronização da palavra-passe. Volte a executar o script para verificar se a configuração está correta.

4. Procure no registo do caso erros. Procure os seguintes eventos, o que indicaria um problema:
    * Fonte: ID "Sincronização de diretórios": 0, 611, 652, 655 Se vir estes eventos, tem um problema de conectividade. A mensagem de registo do evento contém informações da floresta onde você tem um problema. Para mais informações, consulte o [problema da conectividade.](#connectivity problem)

5. Se não vir batimentos cardíacos ou se nada mais funcionar, faça [ao Trigger uma sincronização completa de todas as palavras-passe](#trigger-a-full-sync-of-all-passwords). Só uma vez, executa o guião.

6. Consulte a secção Troubleshoot one object that is not synchronizing passwords section.

### <a name="connectivity-problems"></a>Problemas de conectividade

Tem conectividade com a Azure AD?

A conta requerperiu permissões para ler as hashes de senha em todos os domínios? Se instalou o Connect utilizando as definições do Express, as permissões já devem estar corretas. 

Se utilizou a instalação personalizada, detete as permissões manualmente fazendo o seguinte:
    
1. Para encontrar a conta utilizada pelo conector Ative Directory, inicie o **Gestor de Serviços de Sincronização**. 
 
2. Vá a **Conectores**e procure a floresta de Diretório Ativo no local que você está a resolver problemas. 
 
3. Selecione o conector e, em seguida, clique em **Propriedades**. 
 
4. Ir para **Connect to Ative Directory Forest**.  
    
    ![Conta utilizada pelo conector ative do Diretório](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Note o nome de utilizador e o domínio onde a conta está localizada.
    
5. Inicie **utilizadores e computadores de Diretório Ativo**e verifique depois que a conta que encontrou anteriormente tem as permissões de seguimento definidas na raiz de todos os domínios da sua floresta:
    * Replicar alterações de diretório
    * Replicar mudanças de diretório todos

6. Os controladores de domínio são acessíveis pelo Azure AD Connect? Se o servidor Connect não conseguir ligar a todos os controladores de domínio, configure **apenas utilize o controlador**de domínio preferido .  
    
    ![Controlador de domínio utilizado pelo conector Ative Directory](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  
    
7. Volte ao **Gestor de Serviços** de Sincronização e à Partição de **Diretórios de Configuração.** 
 
8. Selecione o seu domínio em **divisórias de diretório,** selecione a caixa de verificação de verificação de controladores de **domínio preferido apenas utilizar** e, em seguida, clique em **Configurar**. 

9. Na lista, introduza os controladores de domínio que o Connect deve utilizar para sincronização de palavras-passe. A mesma lista também é utilizada para a importação e exportação. Faça estes passos para todos os seus domínios.

10. Se o guião mostrar que não há batimentocardíaco, execute o script no [Trigger uma sincronização completa de todas as palavras-passe](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Um dos objetos não é sincronizar palavras-passe: passos manuais de resolução de problemas

Pode facilmente resolver problemas com a sincronização de hash de palavra-passe, revendo o estado de um objeto.

1. No **Ative Directory Users and Computers,** procure o utilizador e verifique se o **Utilizador tem de alterar a palavra-passe na próxima** caixa de verificação de logon.  

    ![Senhas produtivas de Diretório Ativo](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Se a caixa de verificação for selecionada, peça ao utilizador para iniciar sessão e alterar a palavra-passe. As palavras-passe temporárias não são sincronizadas com a AD Azure.

2. Se a palavra-passe estiver correta no Diretório Ativo, siga o utilizador no motor de sincronização. Seguindo o utilizador desde o Diretório Ativo no local até à AD Azure, pode ver se existe um erro descritivo no objeto.

    a. Inicie o [Gestor de Serviços de Sincronização.](how-to-connect-sync-service-manager-ui.md)

    b. Clique em **Conectores**.

    c. Selecione o **Conector de Diretório Ativo** onde o utilizador está localizado.

    d. Selecione **Search Connector Space**.

    e. Na caixa **Scope,** selecione **DN ou Anchor**e, em seguida, introduza o DN completo do utilizador que está a resolver problemas.

    ![Procure o utilizador no espaço do conector com o DN](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Localize o utilizador que procura e, em seguida, clique em **Propriedades** para ver todos os atributos. Se o utilizador não estiver no resultado da pesquisa, verifique [as suas regras de filtragem](how-to-connect-sync-configure-filtering.md) e certifique-se de que executa O Aplicar e verificar as [alterações](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) para que o utilizador apareça no Connect.

    g. Para ver os detalhes da sincronização da palavra-passe do objeto durante a semana passada, clique em **Registar**.  

    ![Detalhes do registo de objetos](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Se o registo do objeto estiver vazio, o Azure AD Connect não conseguiu ler o hash de palavra-passe do Ative Directory. Continue a sua resolução de problemas com erros de conectividade. Se vir qualquer outro valor que não o **sucesso,** consulte a tabela no registo de [sincronização](#password-sync-log)password .

    h. Selecione o separador **de linhagem** e certifique-se de que pelo menos uma regra de sincronização na coluna **PasswordSync** é **verdadeira**. Na configuração predefinida, o nome da regra de sincronização é in a partir de AD - Conta de **utilizadorActivada**.  

    ![Informações de linhagem sobre um utilizador](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Clique em **Propriedades de Objetometaversos** para apresentar uma lista de atributos do utilizador.  

    ![Informação metaversa](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Verifique se não existe nenhum atributo **com cloudFiltered** presente. Certifique-se de que os atributos de domínio (domínioFQDN e domainNetBios) têm os valores esperados.

    j. Clique no separador **DeCotores.** Certifique-se de que vê conectores tanto no local como no Anúncio Azure.

    ![Informação metaversa](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Selecione a linha que representa o Azure AD, clique em **Propriedades,** e clique no separador **Lineage.** O objeto espacial do conector deve ter uma regra de saída na coluna **PasswordSync** definida para **True**. Na configuração predefinida, o nome da regra de sincronização é **out to AAD - User Join**.  

    ![Caixa de diálogo propriedades de objeto sinuoso](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Registo de sincronização de palavra-passe

A coluna de estado pode ter os seguintes valores:

| Estado | Descrição |
| --- | --- |
| Êxito |A palavra-passe foi sincronizada com sucesso. |
| FilteredByTarget |A palavra-passe é definida para **o Utilizador deve alterar a palavra-passe no próximo início de sessão**. A palavra-passe não foi sincronizada. |
| NoTargetConnection |Nenhum objeto no metaverso ou no espaço do conector Azure AD. |
| SourceConnectorNotPresent |Nenhum objeto encontrado no espaço do conector Ative Diretório no local. |
| TargetNotExportedToDirectory |O objeto no espaço do conector Azure AD ainda não foi exportado. |
| MigratedCheckDetailsForMoreInfo |A entrada de registo foi criada antes da construção 1.0.9125.0 e é mostrada no seu estado legado. |
| Erro |O serviço devolveu um erro desconhecido. |
| Desconhecido |Ocorreu um erro ao tentar processar um lote de hashes de senha.  |
| Atribuído desaparecido |Não estão disponíveis atributos específicos (por exemplo, hash Kerberos) exigidos pelos Serviços de Domínio Da Azure AD. |
| RetryRequestedByTarget |Atributos específicos (por exemplo, hash Kerberos) exigidos pelos Serviços de Domínio Da Azure AD não estavam disponíveis anteriormente. É feita uma tentativa de resincronização do hash de senha do utilizador. |

## <a name="scripts-to-help-troubleshooting"></a>Scripts para ajudar a resolver problemas

### <a name="get-the-status-of-password-sync-settings"></a>Obtenha o estado das definições de sincronização de palavras-passe

```powershell
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

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Desencadear uma sincronização completa de todas as palavras-passe

> [!NOTE]
> Executa este guião apenas uma vez. Se precisar esquecê-lo mais de uma vez, outra coisa é o problema. Para resolver o problema, contacte o suporte da Microsoft.

Pode desencadear uma sincronização completa de todas as palavras-passe utilizando o seguinte script:

```powershell
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

## <a name="next-steps"></a>Passos seguintes

* [Implementação de sincronização de hash de senha com sincronização Azure AD Connect](how-to-connect-password-hash-synchronization.md)
* [Azure AD Connect Sync: Opções de sincronização personalizando](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)