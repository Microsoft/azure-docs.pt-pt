---
title: 'Sincronização do Azure AD Connect: Considerações e tarefas operacionais | Documentos da Microsoft'
description: Este tópico descreve as tarefas operacionais para a sincronização do Azure AD Connect e para se preparar para o funcionamento deste componente.
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
ms.openlocfilehash: 176b8509892ef16b631697a686471e7fa52bb380
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57196370"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: Servidor de preparação e recuperação após desastre
Com um servidor no modo de teste, pode efetuar alterações à configuração e visualizar as alterações antes de tornar o servidor Active Directory. Ele também permite a execução de importação completa e uma sincronização completa para verificar que todas as alterações esperadas antes de efetuar estas alterações no seu ambiente de produção.

## <a name="staging-mode"></a>Modo de teste
O modo de teste pode ser utilizado para vários cenários, incluindo:

* Elevada disponibilidade.
* Testar e implementar novas alterações de configuração.
* Introduzir um novo servidor e desativar o antigo.

Durante a instalação, pode selecionar o servidor estar no **modo de teste**. Esta ação torna o servidor Active Directory para importação e sincronização, mas não executa quaisquer exportações. Um servidor no modo de teste não está em execução a sincronização de palavra-passe ou a repetição de escrita de palavra-passe, mesmo se tiver selecionado esses recursos durante a instalação. Quando desativar o modo de teste, o servidor inicia a exportar, permite a sincronização de palavra-passe e permite que a repetição de escrita de palavra-passe.

> [!NOTE]
> Suponha que tem um Azure AD Connect com a funcionalidade de sincronização de Hash de palavra-passe ativada. Quando ativa o modo de teste, o pára de servidor muda de sincronização de palavra-passe do AD no local. Quando desativar o modo de teste, o servidor retoma a sincronização de alterações de palavra-passe de onde pela última vez parou. Se o servidor ficar no modo de teste por um longo período de tempo, pode demorar algum tempo para o servidor sincronizar todas as alterações de palavra-passe que tiveram ocorrido durante o período de tempo.
>
>

Ainda pode forçar uma exportação ao utilizar o synchronization service manager.

Um servidor no modo de teste continua a receber as alterações do Active Directory e o Azure AD. Tem sempre uma cópia das alterações mais recentes e pode fazer de muito rápida sobre as responsabilidades de outro servidor. Se efetuar alterações de configuração para o servidor primário, é sua responsabilidade assegurar as mesmas alterações para o servidor no modo de teste.

Para aqueles que com conhecimento das tecnologias de sincronização mais antigos, o modo de teste é diferente, uma vez que o servidor tem sua própria base de dados SQL. Esta arquitetura permite no servidor do modo de preparação estar localizados num datacenter diferente.

### <a name="verify-the-configuration-of-a-server"></a>Verificar a configuração de um servidor
Para aplicar este método, siga estes passos:

1. [Preparar](#prepare)
2. [Configuração](#configuration)
3. [Importar e sincronizar](#import-and-synchronize)
4. [Certifique-se](#verify)
5. [Servidor de Active Directory do comutador](#switch-active-server)

#### <a name="prepare"></a>Preparação
1. Instalar o Azure AD Connect, selecione **modo de teste**e anule a seleção **Iniciar sincronização** na última página no Assistente de instalação. Este modo permite-lhe executar manualmente o motor de sincronização.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Início de sessão desativado/início de sessão no e a partir de menu Iniciar, selecione **serviço de sincronização**.

#### <a name="configuration"></a>Configuração
Se fez alterações personalizadas para o servidor primário e deseja comparar a configuração com o servidor de preparação, em seguida, utilize [documenter de configuração do Azure AD Connect](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importar e sincronizar
1. Selecione **conectores**e selecione o primeiro conector com o tipo **serviços de domínio do Active Directory**. Clique em **execute**, selecione **importação completa**, e **OK**. Efetue estes passos para todos os conectores deste tipo.
2. Seleccione o conector com o tipo **do Azure Active Directory (Microsoft)**. Clique em **execute**, selecione **importação completa**, e **OK**.
3. Certifique-se de que o separador conectores ainda está selecionado. Para cada conector com o tipo **serviços de domínio do Active Directory**, clique em **execute**, selecione **sincronização Delta**, e **OK**.
4. Seleccione o conector com o tipo **do Azure Active Directory (Microsoft)**. Clique em **execute**, selecione **sincronização Delta**, e **OK**.

Agora tem testado exportação muda para o Azure AD e AD no local (se estiver a utilizar a implementação híbrida do Exchange). Os passos seguintes permitem-lhe inspecionar o que está prestes a mudar antes de começar, na verdade, a exportação para os diretórios.

#### <a name="verify"></a>Verificar
1. Iniciar um prompt de comando e vá para `%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Execute: `csexport "Name of Connector" %temp%\export.xml /f:x` O nome do conector pode ser encontrado no serviço de sincronização. Ele tem um nome semelhante a "contoso.com – AAD" para o Azure AD.
3. Execute: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv` Possui um arquivo em % temp % com o nome export.csv que pode ser examinada no Microsoft Excel. Esse arquivo contém todas as alterações que estão prestes a ser exportado.
4. Faça as alterações necessárias para a configuração ou de dados e executar estes passos novamente (importar e sincronizar e certifique-se) até que a espera-se que as alterações que estão prestes a ser exportado.

**Noções básicas sobre o ficheiro de export.csv** maioria do ficheiro é auto-explicativa. Alguns abreviações para compreender o conteúdo:
* OMODT – tipo de modificação do objeto. Indica se a operação ao nível do objeto é um Add, Update ou Delete.
* AMODT – tipo de modificação de atributo. Indica se a operação num nível de atributo é um Add, Update ou delete.

**Obter identificadores comuns** o ficheiro de export.csv contém todas as alterações que estão prestes a ser exportado. Cada linha corresponde a uma alteração para um objeto no espaço conector e o objeto é identificado pelo atributo DN. O atributo de DN é um identificador exclusivo atribuído a um objeto no espaço conector. Quando tiver muitas linhas/alterações na export.csv para analisar, poderá ser difícil para descobrir que objetos que as alterações são para baseado no atributo DN sozinho. Para simplificar o processo de analisar as alterações, utilize o csanalyzer.ps1 script do PowerShell. O script obtém identificadores comuns (por exemplo, displayName, userPrincipalName) dos objetos. Para utilizar o script:
1. Copie o script do PowerShell da secção [CSAnalyzer](#appendix-csanalyzer) num ficheiro denominado `csanalyzer.ps1`.
2. Abra uma janela do PowerShell e navegue até à pasta onde criou o script do PowerShell.
3. Run: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Agora tem um ficheiro denominado **processedusers1.csv** que pode ser examinada no Microsoft Excel. Tenha em atenção que o ficheiro fornece um mapeamento do atributo do DN para identificadores comuns (por exemplo, displayName e userPrincipalName). Atualmente não inclui as alterações de atributo real que estão prestes a ser exportado.

#### <a name="switch-active-server"></a>Servidor de Active Directory do comutador
1. No servidor atualmente ativo, desligue o servidor (DirSync/FIM/Azure AD Sync) para que não está a ser exportado para o Azure AD ou defini-lo no modo (Azure AD Connect) de teste.
2. Execute o Assistente de instalação no servidor, na **modo de teste** e desativar **modo de teste**.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Recuperação após desastre
Parte do design de implementação é planejar para o que fazer no caso de um desastre em que perde o servidor de sincronização. Existem diferentes modelos de utilização e qual devo utilizar depende de vários fatores, incluindo:

* O que é a sua tolerância para não ser capaz de fazer alterações a objetos no Azure AD durante o período de indisponibilidade?
* Se utilizar a sincronização de palavra-passe, os utilizadores aceitamos que têm de utilizar a palavra-passe antiga no Azure AD, caso eles alteração-lo no local?
* Tem uma dependência em operações em tempo real, como a repetição de escrita de palavra-passe?

Consoante as respostas a estas questões e a política da sua organização, uma das seguintes estratégias pode ser implementada:

* Reconstrua quando necessário.
* Ter um servidor de reserva dinâmica reserva, conhecido como **modo de teste**.
* Utilize máquinas virtuais.

Se não utilizar a base de dados incorporada SQL Express, também deverá rever os [elevada disponibilidade de SQL](#sql-high-availability) secção.

### <a name="rebuild-when-needed"></a>Reconstruir quando necessário
Uma estratégia viável é planejar de reconstrução de um servidor quando necessário. Normalmente, a instalar o motor de sincronização e faça que a importação inicial e a sincronização podem ser concluídas em algumas horas. Se existir um servidor de reserva não estiver disponível, é possível usar temporariamente um controlador de domínio para alojar o motor de sincronização.

O servidor de motor de sincronização não armazena qualquer estado sobre os objetos para que a base de dados pode ser reconstruída dos dados no Active Directory e o Azure AD. O **sourceAnchor** atributo é utilizado para associar os objetos no local e na cloud. Se recriar o servidor com objetos no local existentes e a nuvem, em seguida, o motor de sincronização corresponde a esses objetos em conjunto novamente na reinstalação. As coisas que precisa documentar e guardar são as alterações de configuração efetuadas no servidor, tais como regras de sincronização e filtragem. Estas configurações personalizadas tem de ser reaplicadas antes de iniciá-la.

### <a name="have-a-spare-standby-server---staging-mode"></a>Ter um servidor de reserva dinâmica reserva - modo de teste
Se tiver um ambiente mais complexo, em seguida, ter um ou mais servidores de reserva dinâmica é recomendado. Durante a instalação, pode ativar um servidor de estar no **modo de teste**.

Para obter mais informações, consulte [modo de teste](#staging-mode).

### <a name="use-virtual-machines"></a>Utilize máquinas virtuais
É um método comum e com suporte executar o motor de sincronização numa máquina virtual. No caso do anfitrião tem um problema, a imagem com o servidor de motor de sincronização pode ser migrada para outro servidor.

### <a name="sql-high-availability"></a>Elevada disponibilidade SQL
Se não estiver usando o SQL Server Express que vem com o Azure AD Connect, em seguida, alta disponibilidade do SQL Server também deve ser considerada. As soluções de elevada disponibilidade suportadas incluem o agrupamento do SQL e AOA (grupos de Disponibilidade AlwaysOn). Soluções não suportadas incluem espelhamento.

Foi adicionado suporte para AOA de SQL para o Azure AD Connect, na versão 1.1.524.0. Tem de ativar AOA de SQL antes de instalar o Azure AD Connect. Durante a instalação, o Azure AD Connect Deteta se a instância SQL fornecida está ativada para SQL AOA ou não. Se estiver ativada AOA de SQL, Azure AD Connect ainda mais descobre se o SQL AOA está configurado para utilizar a replicação síncrona ou replicação assíncrona. Quando configurar o serviço de escuta do grupo de disponibilidade, recomenda-se que defina a propriedade RegisterAllProvidersIP para 0. Isto acontece porque o Azure AD Connect utiliza atualmente o SQL Native Client para ligar ao SQL e SQL Native Client não suporta a utilização da propriedade MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Apêndice CSAnalyzer
Consulte a secção [verificar](#verify) sobre como utilizar este script.

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

## <a name="next-steps"></a>Passos Seguintes
**Tópicos de descrição geral**  

* [Sincronização do Azure AD Connect: Compreender e personalizar a sincronização](how-to-connect-sync-whatis.md)  
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)  
