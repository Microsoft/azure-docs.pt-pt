---
title: 'Sincronização Azure AD Connect: Tarefas operacionais e considerações / Microsoft Docs'
description: Este tópico descreve tarefas operacionais para o sincronizacro Azure AD Connect e como se preparar para operar este componente.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: b29c1790-37a3-470f-ab69-3cee824d220d
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/27/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc88640cdff4f716902a80bb149913b961d40ae3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79261024"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: Servidor de encenação e recuperação de desastres
Com um servidor em modo de encenação, pode fazer alterações na configuração e pré-visualizar as alterações antes de tornar o servidor ativo. Também permite executar a importação completa e a sincronização completa para verificar se todas as mudanças são esperadas antes de fazer estas alterações no seu ambiente de produção.

## <a name="staging-mode"></a>Modo de teste
O modo de encenação pode ser usado para vários cenários, incluindo:

* Elevada disponibilidade.
* Teste e implemente novas alterações de configuração.
* Introduza um novo servidor e desaque o antigo.

Durante a instalação, pode selecionar o servidor para estar no modo de **paragem**. Esta ação torna o servidor ativo para importação e sincronização, mas não executa quaisquer exportações. Um servidor no modo de encenação não está a executar sincronização de palavra-passe ou redação de palavra-passe, mesmo que tenha selecionado estas funcionalidades durante a instalação. Quando desativa o modo de preparação, o servidor começa a exportar, ativa a sincronização de palavras-passe e permite a reescrita da palavra-passe.

> [!NOTE]
> Suponha que tenha um Azure AD Connect com a funcionalidade de sincronização de hash de palavra-passe ativada. Quando ativa o modo de preparação, o servidor para de sincronizar alterações de palavra-passe a partir de AD no local. Quando desativa o modo de preparação, o servidor retoma as alterações de senha sincronizadas de onde ficou desativada. Se o servidor for deixado em modo de paragem durante um longo período de tempo, pode demorar algum tempo até que o servidor sincronize todas as alterações de palavra-passe que ocorreram durante o período de tempo.
>
>

Você ainda pode forçar uma exportação usando o gestor de serviços de sincronização.

Um servidor em modo de encenação continua a receber alterações do Ative Directory e da AD Azure e pode assumir rapidamente as responsabilidades de outro servidor em caso de falha. Se fizer alterações de configuração no seu servidor principal, é da sua responsabilidade fazer as mesmas alterações no servidor no modo de preparação.

Para aqueles de vocês com conhecimento de tecnologias de sincronização mais antigas, o modo de encenação é diferente uma vez que o servidor tem a sua própria base de dados SQL. Esta arquitetura permite que o servidor de modo de encenação esteja localizado num datacenter diferente.

### <a name="verify-the-configuration-of-a-server"></a>Verifique a configuração de um servidor
Para aplicar este método, siga estes passos:

1. [Preparar](#prepare)
2. [Configuração](#configuration)
3. [Importar e Sincronizar](#import-and-synchronize)
4. [Verificar](#verify)
5. [Mudar servidor ativo](#switch-active-server)

#### <a name="prepare"></a>Preparação
1. Instale o Azure AD Connect, selecione o modo de **paragem**e desmarque a **sincronização** de arranque na última página do assistente de instalação. Este modo permite-lhe executar manualmente o motor de sincronização.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Inscreva-se/inicie o menu de início selecione **Serviço de Sincronização**.

#### <a name="configuration"></a>Configuração
Se então fez alterações personalizadas no servidor primário e pretende comparar a configuração com o servidor de encenação, utilize o [documentador de configuração Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importar e Sincronizar
1. Selecione **Conectores,** e selecione o primeiro Conector com o tipo Serviços de **Domínio de Diretório Ativo**. Clique em **Executar,** selecione **Importação completa,** e **OK**. Faça estes passos para todos os Conectores deste tipo.
2. Selecione o Conector com o tipo **Azure Ative Directory (Microsoft)** . Clique em **Executar,** selecione **Importação completa,** e **OK**.
3. Certifique-se de que os conectores do separador ainda estão selecionados. Para cada Conector com serviços de domínio de **diretório ativo,** clique em **Executar,** selecione **Delta Synchronization**, e **OK**.
4. Selecione o Conector com o tipo **Azure Ative Directory (Microsoft)** . Clique em **Executar,** selecione **Delta Synchronization**, e **OK**.

Já encenou alterações de exportação para a AD Azure e para a AD (se estiver a utilizar a implantação híbrida exchange). Os próximos passos permitem-lhe inspecionar o que está prestes a mudar antes de iniciar a exportação para os diretórios.

#### <a name="verify"></a>Verificar
1. Inicie uma solicitação cmd e vá para `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Executar: `csexport "Name of Connector" %temp%\export.xml /f:x` O nome do Conector pode ser encontrado no Serviço de Sincronização. Tem um nome semelhante a "contoso.com – AAD" para a Azure AD.
3. Executar: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` Tem um ficheiro em %temporário chamado export.csv que pode ser examinado no Microsoft Excel. Este ficheiro contém todas as alterações que estão prestes a ser exportadas.
4. Faça alterações necessárias nos dados ou configurações e faça estes passos novamente (Importar e Sincronizar e Verificar) até que sejam esperadas as alterações que estão prestes a ser exportadas.

**Compreender o ficheiro export.csv** A maior parte do ficheiro é autoexplicativa. Algumas abreviaturas para entender o conteúdo:
* OMODT - Tipo de modificação de objetos. Indica se a operação a um nível de objeto é um Add, Update ou Delete.
* AMODT – Tipo de modificação do atributo. Indica se a operação a um nível de atributo é um Add, Update ou delete.

**Recuperar identificadores comuns** O ficheiro export.csv contém todas as alterações que estão prestes a ser exportadas. Cada linha corresponde a uma alteração de um objeto no espaço do conector e o objeto é identificado pelo atributo dN. O atributo DN é um identificador único atribuído a um objeto no espaço do conector. Quando você tem muitas linhas/mudanças no export.csv para analisar, pode ser difícil para você descobrir quais objetos as alterações são para com base apenas no atributo DN. Para simplificar o processo de análise das alterações, utilize o script csanalyzer.ps1 PowerShell. O script recupera identificadores comuns (por exemplo, displayName, userPrincipalName) dos objetos. Para utilizar o guião:
1. Copie o script PowerShell da secção [CSAnalyzer](#appendix-csanalyzer) para um ficheiro chamado `csanalyzer.ps1`.
2. Abra uma janela PowerShell e navegue para a pasta onde criou o script PowerShell.
3. Corrida: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Tem agora um ficheiro chamado **processedusers1.csv** que pode ser examinado no Microsoft Excel. Note que o ficheiro fornece um mapeamento do atributo DN a identificadores comuns (por exemplo, nome de exibição e nome principal do utilizador). Atualmente, não inclui as alterações reais dos atributos que estão prestes a ser exportadas.

#### <a name="switch-active-server"></a>Mudar servidor ativo
1. No servidor atualmente ativo, desligue o servidor (DirSync/FIM/Azure AD Sync) para que não exporte para o Azure AD ou o coloque em modo de paragem (Azure AD Connect).
2. Execute o assistente de instalação no servidor no modo de **paragem** e desative o **modo de preparação**.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Recuperação após desastre
Parte do design de implementação é planear o que fazer no caso de haver um desastre onde você perde o servidor de sincronização. Existem diferentes modelos para usar e quais os que se utilizam depende de vários fatores, incluindo:

* Qual é a sua tolerância para não poder fazer alterações em objetos em Azure AD durante o tempo de inatividade?
* Se utilizar a sincronização de palavras-passe, os utilizadores aceitam que têm de utilizar a antiga senha em AD Azure no caso de a alterarem no local?
* Tem alguma dependência de operações em tempo real, como a redação de palavras-passe?

Dependendo das respostas a estas questões e da política da sua organização, uma das seguintes estratégias pode ser implementada:

* Reconstruir quando necessário.
* Tenha um servidor de espera sobressalente, conhecido como **modo de encenação**.
* Utilize máquinas virtuais.

Se não utilizar a base de dados SQL Express incorporada, então também deverá rever a secção [de Alta Disponibilidade SQL.](#sql-high-availability)

### <a name="rebuild-when-needed"></a>Reconstruir quando necessário
Uma estratégia viável é planear a reconstrução de um servidor quando necessário. Normalmente, instalar o motor de sincronização e fazer a importação e sincronização iniciais pode ser concluído dentro de algumas horas. Se não houver um servidor sobressalente disponível, é possível utilizar temporariamente um controlador de domínio para alojar o motor de sincronização.

O servidor do motor sincronizado não armazena qualquer estado sobre os objetos para que a base de dados possa ser reconstruída a partir dos dados em Ative Directory e Azure AD. O atributo **sourceAnchor** é usado para juntar os objetos a partir do local e da nuvem. Se reconstruir o servidor com objetos existentes no local e na nuvem, então o motor sincronizado combina novamente com esses objetos na reinstalação. As coisas que precisa de documentar e guardar são as alterações de configuração feitas no servidor, tais como regras de filtragem e sincronização. Estas configurações personalizadas devem ser reaplicadas antes de começar a sincronizar.

### <a name="have-a-spare-standby-server---staging-mode"></a>Tenha um servidor de espera de reserva - modo de encenação
Se tiver um ambiente mais complexo, então é recomendado ter um ou mais servidores de espera. Durante a instalação, pode ativar um servidor em modo de **paragem**.

Para mais informações, consulte o [modo de encenação](#staging-mode).

### <a name="use-virtual-machines"></a>Utilize máquinas virtuais
Um método comum e suportado é executar o motor de sincronização numa máquina virtual. Caso o anfitrião tenha um problema, a imagem com o servidor do motor de sincronização pode ser migrada para outro servidor.

### <a name="sql-high-availability"></a>Alta Disponibilidade SQL
Se não estiver a utilizar o SQL Server Express que vem com o Azure AD Connect, então também deve ser considerada uma elevada disponibilidade para o Servidor SQL. As soluções de alta disponibilidade suportadas incluem clustering SQL e AOA (Sempre Em Grupos de Disponibilidade). Soluções não suportadas incluem espelhamento.

O suporte para o SQL AOA foi adicionado ao Azure AD Connect na versão 1.1.524.0. Tem de ativar o SQL AOA antes de instalar o Azure AD Connect. Durante a instalação, o Azure AD Connect deteta se a instância SQL fornecida está ativada para o SQL AOA ou não. Se o SQL AOA estiver ativado, o Azure AD Connect descobre ainda se o SQL AOA estiver configurado para utilizar replicação sincronizada ou replicação assíncrona. Ao configurar o Ouvinte do Grupo de Disponibilidade, recomenda-se que defina a propriedade RegisterAllProvidersIP para 0. Isto porque o Azure AD Connect utiliza atualmente o Cliente Nativo SQL para se ligar à SQL e ao SQL Native Client não suporta o uso da propriedade MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Apêndice CSAnalyzer
Consulte a secção [para verificar](#verify) como utilizar este script.

```
Param(
    [Parameter(Mandatory=$true, HelpMessage="Must be a file generated using csexport 'Name of Connector' export.xml /f:x)")]
    [string]$xmltoimport="%temp%\exportedStage1a.xml",
    [Parameter(Mandatory=$false, HelpMessage="Maximum number of users per output file")][int]$batchsize=1000,
    [Parameter(Mandatory=$false, HelpMessage="Show console output")][bool]$showOutput=$false
)

#LINQ isn't loaded automatically, so force it
[Reflection.Assembly]::Load("System.Xml.Linq, Version=3.5.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089") | Out-Null

[int]$count=1
[int]$outputfilecount=1
[array]$objOutputUsers=@()

#XML must be generated using "csexport "Name of Connector" export.xml /f:x"
write-host "Importing XML" -ForegroundColor Yellow

#XmlReader.Create won't properly resolve the file location,
#so expand and then resolve it
$resolvedXMLtoimport=Resolve-Path -Path ([Environment]::ExpandEnvironmentVariables($xmltoimport))

#use an XmlReader to deal with even large files
$result=$reader = [System.Xml.XmlReader]::Create($resolvedXMLtoimport) 
$result=$reader.ReadToDescendant('cs-object')
do 
{
    #create the object placeholder
    #adding them up here means we can enforce consistency
    $objOutputUser=New-Object psobject
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name ID -Value ""
    Add-Member -InputObject $objOutputUser -MemberType NoteProperty -Name Type -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name DN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name operation -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name UPN -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name displayName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name sourceAnchor -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name alias -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name primarySMTP -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name onPremisesSamAccountName -Value ""
    Add-Member -inputobject $objOutputUser -MemberType NoteProperty -Name mail -Value ""

    $user = [System.Xml.Linq.XElement]::ReadFrom($reader)
    if ($showOutput) {Write-Host Found an exported object... -ForegroundColor Green}

    #object id
    $outID=$user.Attribute('id').Value
    if ($showOutput) {Write-Host ID: $outID}
    $objOutputUser.ID=$outID

    #object type
    $outType=$user.Attribute('object-type').Value
    if ($showOutput) {Write-Host Type: $outType}
    $objOutputUser.Type=$outType

    #dn
    $outDN= $user.Element('unapplied-export').Element('delta').Attribute('dn').Value
    if ($showOutput) {Write-Host DN: $outDN}
    $objOutputUser.DN=$outDN

    #operation
    $outOperation= $user.Element('unapplied-export').Element('delta').Attribute('operation').Value
    if ($showOutput) {Write-Host Operation: $outOperation}
    $objOutputUser.operation=$outOperation

    #now that we have the basics, go get the details

    foreach ($attr in $user.Element('unapplied-export-hologram').Element('entry').Elements("attr"))
    {
        $attrvalue=$attr.Attribute('name').Value
        $internalvalue= $attr.Element('value').Value

        switch ($attrvalue)
        {
            "userPrincipalName"
            {
                if ($showOutput) {Write-Host UPN: $internalvalue}
                $objOutputUser.UPN=$internalvalue
            }
            "displayName"
            {
                if ($showOutput) {Write-Host displayName: $internalvalue}
                $objOutputUser.displayName=$internalvalue
            }
            "sourceAnchor"
            {
                if ($showOutput) {Write-Host sourceAnchor: $internalvalue}
                $objOutputUser.sourceAnchor=$internalvalue
            }
            "alias"
            {
                if ($showOutput) {Write-Host alias: $internalvalue}
                $objOutputUser.alias=$internalvalue
            }
            "proxyAddresses"
            {
                if ($showOutput) {Write-Host primarySMTP: ($internalvalue -replace "SMTP:","")}
                $objOutputUser.primarySMTP=$internalvalue -replace "SMTP:",""
            }
        }
    }

    $objOutputUsers += $objOutputUser

    Write-Progress -activity "Processing ${xmltoimport} in batches of ${batchsize}" -status "Batch ${outputfilecount}: " -percentComplete (($objOutputUsers.Count / $batchsize) * 100)

    #every so often, dump the processed users in case we blow up somewhere
    if ($count % $batchsize -eq 0)
    {
        Write-Host Hit the maximum users processed without completion... -ForegroundColor Yellow

        #export the collection of users as a CSV
        Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
        $objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation

        #increment the output file counter
        $outputfilecount+=1

        #reset the collection and the user counter
        $objOutputUsers = $null
        $count=0
    }

    $count+=1

    #need to bail out of the loop if no more users to process
    if ($reader.NodeType -eq [System.Xml.XmlNodeType]::EndElement)
    {
        break
    }

} while ($reader.Read)

#need to write out any users that didn't get picked up in a batch of 1000
#export the collection of users as CSV
Write-Host Writing processedusers${outputfilecount}.csv -ForegroundColor Yellow
$objOutputUsers | Export-Csv -path processedusers${outputfilecount}.csv -NoTypeInformation
```

## <a name="next-steps"></a>Passos seguintes
**Tópicos de visão geral**  

* [Sincronização Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)  
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)  
