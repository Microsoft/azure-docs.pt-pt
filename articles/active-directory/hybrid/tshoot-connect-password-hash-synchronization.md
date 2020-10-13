---
title: Resolução de problemas sincronização de hash de palavra-passe com sincronização Azure AD Connect / Microsoft Docs
description: Este artigo fornece informações sobre como resolver problemas de sincronização de haxixe de palavras-passe.
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
ms.topic: troubleshooting
ms.date: 03/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77271679306b0fbde10c748afc7535f3ad3d0945
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91317570"
---
# <a name="troubleshoot-password-hash-synchronization-with-azure-ad-connect-sync"></a>Resolver problemas da sincronização hash de palavras-passe com a sincronização do Azure AD Connect

Este tópico fornece passos para como resolver problemas com a sincronização de hash de palavra-passe. Se as palavras-passe não estiverem sincronizadas como esperado, pode ser para um subconjunto de utilizadores ou para todos os utilizadores.

Para o Azure Ative Directory (Azure AD) Ligue a implementação com a versão 1.1.614.0 ou depois, utilize a tarefa de resolução de problemas no assistente para resolver problemas de sincronização de haxixe de palavra-passe:

* Se tiver um problema em que não existam palavras-passe sincronizadas, consulte as [palavras-passe "Não" sincronizadas: resolução de problemas utilizando a secção de tarefas de resolução de problemas.](#no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task)

* Se tiver um problema com objetos individuais, consulte o objeto One não está a [sincronizar palavras-passe: resolução de problemas utilizando a secção de tarefas de resolução de problemas.](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task)

Para a implementação com a versão 1.1.524.0 ou posterior, existe um cmdlet de diagnóstico que pode utilizar para resolver problemas de sincronização de haxixe de palavra-passe:

* Se tiver um problema em que não existam palavras-passe sincronizadas, consulte as [palavras-passe "Não" sincronizadas: resolução de problemas utilizando a secção de cmdlet de diagnóstico.](#no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet)

* Se tiver um problema com objetos individuais, consulte o objeto One não está a [sincronizar palavras-passe: resolução de problemas utilizando a secção de cmdlet de diagnóstico.](#one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet)

Para versões mais antigas da implementação do Azure AD Connect:

* Se tiver um problema em que não existam palavras-passe sincronizadas, consulte as [palavras-passe "Não" sincronizadas: secção de etapas de resolução manual de problemas.](#no-passwords-are-synchronized-manual-troubleshooting-steps)

* Se tiver um problema com objetos individuais, consulte o objeto One não está a [sincronizar palavras-passe: secção de etapas de resolução manual de problemas.](#one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-troubleshooting-task"></a>Não há palavras-passe sincronizadas: resolução de problemas utilizando a tarefa de resolução de problemas

Pode utilizar a tarefa de resolução de problemas para descobrir por que razão não são sincronizadas palavras-passe.

> [!NOTE]
> A tarefa de resolução de problemas só está disponível para a versão 1.1.614.0 ou posterior.

### <a name="run-the-troubleshooting-task"></a>Executar a tarefa de resolução de problemas

Para resolver problemas em que não há palavras-passe sincronizadas:

1. Abra uma nova sessão Windows PowerShell no seu servidor Azure AD Connect com a opção **Executar como Administrador.**

2. Corra `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted` . .

3. Inicie o assistente Azure AD Connect.

4. Navegue na página **Tarefas Adicionais,** selecione **Resolução de Problemas**e clique em **Seguinte**.

5. Na página troubleshooting, clique em **Lançamento** para iniciar o menu de resolução de problemas em PowerShell.

6. No menu principal, selecione **Sincronização de hash de palavra-passe de resolução de problemas**.

7. No sub menu, a **sincronização de hash password não funciona de todo**.

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Compreender os resultados da tarefa de resolução de problemas

A tarefa de resolução de problemas executa as seguintes verificações:

* Valida que a função de sincronização de hash de palavra-passe está ativada para o seu inquilino AZure AD.

* Valida que o servidor Azure AD Connect não está no modo de preparação.

* Para cada conector ative diretório existente no local (que corresponde a uma floresta de diretório ativo existente):

   * Valida que a função de sincronização de hash de palavra-passe está ativada.
   
   * Procura eventos de batimentos cardíacos de sincronização de hash de palavra-passe nos registos do Evento de Aplicação do Windows.

   * Para cada domínio do Diretório Ativo no conector ative diretório:

      * Valida que o domínio é acessível a partir do servidor Azure AD Connect.

      * Valida que as contas Ative Directory Domain Services (AD DS) utilizadas pelo conector Ative Directory no local têm o nome de utilizador, palavra-passe e permissões necessárias para a sincronização de hash de palavra-passe.

O diagrama seguinte ilustra os resultados do cmdlet para um topologia de diretório ativo de domínio único:

![Saída de diagnóstico para sincronização de haxixe de palavra-passe](./media/tshoot-connect-password-hash-synchronization/phsglobalgeneral.png)

O resto desta secção descreve resultados específicos que são devolvidos pela tarefa e questões correspondentes.

#### <a name="password-hash-synchronization-feature-isnt-enabled"></a>recurso de sincronização de hash de palavra-palavra não está ativado

Se não tiver ativado a sincronização do hash da palavra-passe utilizando o assistente Azure AD Connect, o seguinte erro é devolvido:

![sincronização de hash palavra palavra não está ativada](./media/tshoot-connect-password-hash-synchronization/phsglobaldisabled.png)

#### <a name="azure-ad-connect-server-is-in-staging-mode"></a>O servidor Azure AD Connect está em modo de preparação

Se o servidor Azure AD Connect estiver no modo de fase, a sincronização de hash de palavra-passe é temporariamente desativada e o seguinte erro é devolvido:

![O servidor Azure AD Connect está em modo de preparação](./media/tshoot-connect-password-hash-synchronization/phsglobalstaging.png)

#### <a name="no-password-hash-synchronization-heartbeat-events"></a>Sem eventos de sincronização de sincronização de palavra-passe

Cada conector ative directory tem o seu próprio canal de sincronização de hash de palavra-passe. Quando o canal de sincronização de hash de palavra-passe é estabelecido e não há alterações de senha a serem sincronizadas, um evento de batimentocardíaco (EventId 654) é gerado uma vez a cada 30 minutos no Registo de Eventos da Aplicação do Windows. Para cada conector Ative Directory no local, o cmdlet procura eventos cardíacos correspondentes nas últimas três horas. Se não for encontrado nenhum evento de batimentos cardíacos, o seguinte erro é devolvido:

![Sem evento de sincronização de sincronização de palavra-passe](./media/tshoot-connect-password-hash-synchronization/phsglobalnoheartbeat.png)

#### <a name="ad-ds-account-does-not-have-correct-permissions"></a>Conta DS AD não tem permissões corretas

Se a conta DS AD que é utilizada pelo conector Ative Directory para sincronizar hashes de palavra-passe não tiver as permissões adequadas, o seguinte erro é devolvido:

![Screenshot que mostra o erro que é devolvido quando a conta DS AD tem um nome de utilizador ou senha incorreto.](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectpermission.png)

#### <a name="incorrect-ad-ds-account-username-or-password"></a>Nome de utilizador ou palavra-passe de conta DS AD incorreto

Se a conta DS AD utilizada pelo conector Ative Directory para sincronizar hashes de palavra-passe tiver um nome de utilizador ou palavra-passe incorreto, o seguinte erro é devolvido:

![Credencial incorreta](./media/tshoot-connect-password-hash-synchronization/phsglobalaccountincorrectcredential.png)



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-troubleshooting-task"></a>Um dos objetos não é sincronizar palavras-passe: resolução de problemas utilizando a tarefa de resolução de problemas

Pode utilizar a tarefa de resolução de problemas para determinar por que razão um objeto não está a sincronizar palavras-passe.

> [!NOTE]
> A tarefa de resolução de problemas só está disponível para a versão 1.1.614.0 ou posterior.

### <a name="run-the-diagnostics-cmdlet"></a>Executar o cmdlet de diagnóstico

Para resolver problemas para um objeto específico do utilizador:

1. Abra uma nova sessão Windows PowerShell no seu servidor Azure AD Connect com a opção **Executar como Administrador.**

2. Corra `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted` . .

3. Inicie o assistente Azure AD Connect.

4. Navegue na página **Tarefas Adicionais,** selecione **Resolução de Problemas**e clique em **Seguinte**.

5. Na página troubleshooting, clique em **Lançamento** para iniciar o menu de resolução de problemas em PowerShell.

6. No menu principal, selecione **Sincronização de hash de palavra-passe de resolução de problemas**.

7. No sub menu, o **selecionador Palavra-passe não é sincronizado para uma conta de utilizador específica.**

### <a name="understand-the-results-of-the-troubleshooting-task"></a>Compreender os resultados da tarefa de resolução de problemas

A tarefa de resolução de problemas executa as seguintes verificações:

* Examina o estado do objeto ative directory no espaço de conector ative Directory, Metaverse e Azure AD.

* Valida que existem regras de sincronização com sincronização de hash de palavra-passe ativada e aplicada no objeto Ative Directory.

* Tentativas de recuperar e visualizar os resultados da última tentativa de sincronizar a palavra-passe do objeto.

O seguinte diagrama ilustra os resultados do cmdlet ao resolver problemas de sincronização de hash de palavra-passe para um único objeto:

![Saída de diagnóstico para sincronização de hash de palavra-passe - único objeto](./media/tshoot-connect-password-hash-synchronization/phssingleobjectgeneral.png)

O resto desta secção descreve resultados específicos devolvidos pelo cmdlet e questões correspondentes.

#### <a name="the-active-directory-object-isnt-exported-to-azure-ad"></a>O objeto ative directy não é exportado para Azure AD

sincronização de hash password para esta conta ative Directy falha porque não há nenhum objeto correspondente no inquilino AZURE AD. O seguinte erro é devolvido:

![O objeto AD de Azure está desaparecido](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnotexported.png)

#### <a name="user-has-a-temporary-password"></a>O utilizador tem uma senha temporária

Atualmente, o Azure AD Connect não suporta sincronizar senhas temporárias com Azure AD. Uma palavra-passe é considerada temporária se a **palavra-passe change na próxima** opção de início de sê-lo for definida no utilizador ative directy no local. O seguinte erro é devolvido:

![A senha temporária não é exportada](./media/tshoot-connect-password-hash-synchronization/phssingleobjecttemporarypassword.png)

#### <a name="results-of-last-attempt-to-synchronize-password-arent-available"></a>Os resultados da última tentativa de sincronização da palavra-passe não estão disponíveis

Por padrão, o Azure AD Connect armazena os resultados das tentativas de sincronização de hash de palavra-passe durante sete dias. Se não houver resultados disponíveis para o objeto ative diretório selecionado, é devolvido o seguinte aviso:

![Saída de diagnóstico para um único objeto - sem histórico de sincronização de palavras-passe](./media/tshoot-connect-password-hash-synchronization/phssingleobjectnohistory.png)



## <a name="no-passwords-are-synchronized-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Não há palavras-passe sincronizadas: resolução de problemas utilizando o cmdlet de diagnóstico

Pode utilizar o `Invoke-ADSyncDiagnostics` cmdlet para descobrir por que razão não há palavras-passe sincronizadas.

> [!NOTE]
> O `Invoke-ADSyncDiagnostics` cmdlet só está disponível para a versão 1.1.524.0 ou mais tarde do Azure AD Connect.

### <a name="run-the-diagnostics-cmdlet"></a>Executar o cmdlet de diagnóstico

Para resolver problemas em que não há palavras-passe sincronizadas:

1. Abra uma nova sessão Windows PowerShell no seu servidor Azure AD Connect com a opção **Executar como Administrador.**

2. Corra `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted` . .

3. Execute `Import-Module ADSyncDiagnostics`.

4. Execute `Invoke-ADSyncDiagnostics -PasswordSync`.



## <a name="one-object-is-not-synchronizing-passwords-troubleshoot-by-using-the-diagnostic-cmdlet"></a>Um dos objetos não é sincronizar palavras-passe: resolução de problemas utilizando o cmdlet de diagnóstico

Pode utilizar o `Invoke-ADSyncDiagnostics` cmdlet para determinar por que razão um objeto não está a sincronizar palavras-passe.

> [!NOTE]
> O `Invoke-ADSyncDiagnostics` cmdlet só está disponível para a versão 1.1.524.0 ou mais tarde do Azure AD Connect.

### <a name="run-the-diagnostics-cmdlet"></a>Executar o cmdlet de diagnóstico

Para resolver problemas em que não há palavras-passe sincronizadas para um utilizador:

1. Abra uma nova sessão Windows PowerShell no seu servidor Azure AD Connect com a opção **Executar como Administrador.**

2. Corra `Set-ExecutionPolicy RemoteSigned` ou `Set-ExecutionPolicy Unrestricted` . .

3. Execute `Import-Module ADSyncDiagnostics`.

4. Execute o seguinte cmdlet:

   ```
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName <Name-of-AD-Connector> -DistinguishedName <DistinguishedName-of-AD-object>
   ```

   Por exemplo:

   ```powershell
   Invoke-ADSyncDiagnostics -PasswordSync -ADConnectorName "contoso.com" -DistinguishedName "CN=TestUserCN=Users,DC=contoso,DC=com"
   ```



## <a name="no-passwords-are-synchronized-manual-troubleshooting-steps"></a>Não há palavras-passe sincronizadas: etapas manuais de resolução de problemas

Siga estes passos para determinar por que não há palavras-passe sincronizadas:

1. O servidor Connect está no [modo de paragem?](how-to-connect-sync-staging-server.md) Um servidor no modo de fase não sincroniza quaisquer palavras-passe.

2. Execute o script na secção [Obter o estado das definições de sincronização de palavras-passe.](#get-the-status-of-password-sync-settings) Dá-lhe uma visão geral da configuração da sincronização de passwords.  

    ![Saída do script PowerShell a partir de definições de sincronização de palavra-passe](./media/tshoot-connect-password-hash-synchronization/psverifyconfig.png)  

3. Se a função não estiver ativada no AD Azure ou se o estado do canal de sincronização não estiver ativado, execute o assistente de instalação 'Ligar'. **Selecione Personalize as opções de sincronização**e desescole a sincronização da palavra-passe. Esta alteração desativa temporariamente a funcionalidade. Em seguida, volte a executar o assistente e volte a ativar a sincronização da palavra-passe. Volte a executar o script para verificar se a configuração está correta.

4. Procure no registo de eventos por erros. Procure os seguintes eventos, o que indica um problema:
    * Fonte: ID de "sincronização de diretório": 0, 611, 652, 655 Se vir estes eventos, tem um problema de conectividade. A mensagem de registo do evento contém informações florestais onde você tem um problema. Para obter mais informações, consulte o [problema da Conectividade.](#connectivity problem)

5. Se não vir nenhum batimento cardíaco ou se nada mais funcionou, [faça do Trigger uma sincronização completa de todas as palavras-passe](#trigger-a-full-sync-of-all-passwords). Executar o guião apenas uma vez.

6. Consulte a secção Troubleshoot one object que não está a sincronizar a secção de palavras-passe.

### <a name="connectivity-problems"></a>Problemas de conectividade

Tem conectividade com a Azure AD?

A conta tem permissões necessárias para ler as hashes de palavra-passe em todos os domínios? Se instalou o Connect utilizando as definições express, as permissões já devem estar corretas. 

Se utilizar a instalação personalizada, desa estale as permissões manualmente fazendo o seguinte:
    
1. Para encontrar a conta utilizada pelo conector Ative Directory, inicie **o Gestor de Serviços de Sincronização**. 
 
2. Vá a **Connectors**e, em seguida, procure a floresta ative directory que você está a resolver problemas. 
 
3. Selecione o conector e, em seguida, clique em **Propriedades**. 
 
4. Ir para **Ligar à Floresta de Diretório Ativo.**  
    
    ![Conta utilizada pelo conector Ative Directory](./media/tshoot-connect-password-hash-synchronization/connectoraccount.png)  
    Note o nome de utilizador e o domínio onde a conta está localizada.
    
5. Inicie **Utilizadores e Computadores de Diretório Ativo**e, em seguida, verifique se a conta encontrada anteriormente tem as permissões de seguimento definidas na raiz de todos os domínios da sua floresta:
    * Alterar o diretório de replicação
    * Replicar mudanças de diretório tudo

6. Os controladores de domínio são alcançáveis pelo Azure AD Connect? Se o servidor 'Ligar' não conseguir ligar-se a todos os controladores de domínio, **configurar apenas utilize o controlador de domínio preferido**.  
    
    ![Controlador de domínio utilizado pelo conector Ative Directory](./media/tshoot-connect-password-hash-synchronization/preferreddc.png)  

7. Volte para **o Gestor de Serviços de Sincronização** **eConfigure Directoriatório Partition**. 
 
8. Selecione o seu domínio em **Select directy partitions**, selecione a única caixa de verificação **de controladores de domínio preferidos** e, em seguida, clique em **Configurar**. 

9. Na lista, introduza os controladores de domínio que o Connect deve utilizar para sincronização de palavras-passe. A mesma lista é utilizada também para a importação e exportação. Faça estes passos para todos os seus domínios.

> [!NOTE]
> Para aplicar estas alterações, reinicie o serviço **Microsoft Azure AD Sync** (ADSync).

10. Se o script mostrar que não há batimentos cardíacos, execute o script no [Trigger uma sincronização completa de todas as palavras-passe](#trigger-a-full-sync-of-all-passwords).

## <a name="one-object-is-not-synchronizing-passwords-manual-troubleshooting-steps"></a>Um dos objetos não é sincronizar palavras-passe: etapas manuais de resolução de problemas

Pode resolver facilmente problemas com problemas de sincronização de haxixe de palavra-passe, revendo o estado de um objeto.

1. Nos **Utilizadores e Computadores do Diretório Ativo,** procure o utilizador e verifique se o Utilizador tem de alterar a **palavra-passe na caixa de** verificação de início de sílido seguinte.  

    ![Passwords produtivas do Diretório Ativo](./media/tshoot-connect-password-hash-synchronization/adprodpassword.png)  

    Se a caixa de verificação for selecionada, peça ao utilizador para iniciar sôm e altere a palavra-passe. As palavras-passe temporárias não são sincronizadas com a Azure AD.

2. Se a palavra-passe parecer correta no Ative Directory, siga o utilizador no motor de sincronização. Ao seguir o utilizador do Ative Directory para Azure AD, pode ver se existe um erro descritivo no objeto.

    a. Inicie o [Gestor de Serviços de Sincronização.](how-to-connect-sync-service-manager-ui.md)

    b. Clique **em Conectores**.

    c. Selecione o **Conector ative directory** onde o utilizador está localizado.

    d. Selecione **espaço de conector de pesquisa**.

    e. Na caixa **'Âmbito',** selecione **DN ou Anchor**, e, em seguida, introduza o DN completo do utilizador que está a resolver problemas.

    ![Pesquisa rumo ao utilizador no espaço do conector com DN](./media/tshoot-connect-password-hash-synchronization/searchcs.png)  

    f. Localize o utilizador que procura e, em seguida, clique em **Propriedades** para ver todos os atributos. Se o utilizador não estiver no resultado da pesquisa, verifique [as suas regras de filtragem](how-to-connect-sync-configure-filtering.md) e certifique-se de que executa [Aplicar e verificar alterações](how-to-connect-sync-configure-filtering.md#apply-and-verify-changes) para o utilizador aparecer no Connect.

    exemplo, Para ver os detalhes da sincronização de palavra-passe do objeto durante a semana passada, clique em **Iniciar sessão**.  

    ![Detalhes do registo de objetos](./media/tshoot-connect-password-hash-synchronization/csobjectlog.png)  

    Se o registo do objeto estiver vazio, o Azure AD Connect não conseguiu ler o hash da palavra-passe do Ative Directory. Continue a resolução de problemas com erros de conectividade. Se vir outro valor que não o **sucesso,** consulte a tabela no [registo de sincronização de passwords](#password-sync-log).

    h. Selecione o **separador de linhagem** e certifique-se de que pelo menos uma regra de sincronização na coluna **PasswordSync** é **verdadeira**. Na configuração padrão, o nome da regra de sincronização é **In from AD - User AccountEnabled**.  

    ![Informação de linhagem sobre um utilizador](./media/tshoot-connect-password-hash-synchronization/cspasswordsync.png)  

    i. Clique **em Metaverse Object Properties** para apresentar uma lista de atributos do utilizador.  

    ![Screenshot que mostra a lista de atributos do utilizador para as Propriedades do Objeto Metaverso.](./media/tshoot-connect-password-hash-synchronization/mvpasswordsync.png)  

    Verifique se não existe nenhum atributo **cloudFiltered** presente. Certifique-se de que os atributos de domínio (domainFQDN e domainNetBios) têm os valores esperados.

    j. Clique no **separador Conectores.** Certifique-se de que vê conectores tanto no local como no Ad AZure.

    ![Informação metaversa](./media/tshoot-connect-password-hash-synchronization/mvconnectors.png)  

    k. Selecione a linha que representa Azure AD, clique em **Propriedades**e, em seguida, clique no **separador Linhagem.** O objeto espacial do conector deve ter uma regra de saída na coluna **PasswordSync** definida como **True**. Na configuração padrão, o nome da regra de sincronização é **out to AAD - User Join**.  

    ![Caixa de diálogo de propriedades de objetos de espaço do conector](./media/tshoot-connect-password-hash-synchronization/cspasswordsync2.png)  

### <a name="password-sync-log"></a>Registo de sincronização de palavra-passe

A coluna de estado pode ter os seguintes valores:

| Estado | Descrição |
| --- | --- |
| Success |A palavra-passe foi sincronizada com sucesso. |
| FiltradoByTarget |A palavra-passe está definida para **O Utilizador tem de alterar a palavra-passe no próximo início de sê-lo.** A palavra-passe não foi sincronizada. |
| NoTargetConnection |Nenhum objeto no metaverso ou no espaço do conector Azure AD. |
| SourceConnectorNotPresent |Nenhum objeto encontrado no espaço do conector Ative Directory. |
| TargetNotExportedToDirectory |O objeto no espaço do conector Azure AD ainda não foi exportado. |
| CheckDetailsForMoreInfo migrado |A entrada de registo foi criada antes da construção 1.0.9125.0 e é mostrada no seu estado legado. |
| Erro |O serviço devolveu um erro desconhecido. |
| Desconhecido |Ocorreu um erro ao tentar processar um lote de hashes de palavra-passe.  |
| MissingAttribute |Os atributos específicos (por exemplo, haxixe Kerberos) exigidos pelos Serviços de Domínio AD Azure não estão disponíveis. |
| RetryRequestedByTarget |Os atributos específicos (por exemplo, haxixe Kerberos) exigidos pelos Serviços de Domínio AD Azure não estavam disponíveis anteriormente. É feita uma tentativa de ressincronizar o hash da palavra-passe do utilizador. |

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

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Desencadeie uma sincronização completa de todas as palavras-passe

> [!NOTE]
> Executar este guião apenas uma vez. Se precisar de correr mais do que uma vez, outra coisa é o problema. Para resolver o problema, contacte o suporte da Microsoft.

Pode ativar uma sincronização completa de todas as palavras-passe utilizando o seguinte script:

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

* [Implementação de sincronização de hash de palavra-passe com sincronização Azure AD Connect](how-to-connect-password-hash-synchronization.md)
* [Azure AD Connect Sync: Personalizar opções de sincronização](how-to-connect-sync-whatis.md)
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)
