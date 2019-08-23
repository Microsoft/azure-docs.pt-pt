---
title: 'Sincronização de Azure AD Connect: Considerações e tarefas operacionais | Microsoft Docs'
description: Este tópico descreve as tarefas operacionais para Azure AD Connect sincronização e como preparar para operar esse componente.
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
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900064"
---
# <a name="azure-ad-connect-staging-server-and-disaster-recovery"></a>Azure AD Connect: Testar o servidor e a recuperação após desastre
Com um servidor no modo de preparo, você pode fazer alterações na configuração e visualizar as alterações antes de tornar o servidor ativo. Ele também permite que você execute a importação completa e a sincronização completa para verificar se todas as alterações são esperadas antes de fazer essas alterações em seu ambiente de produção.

## <a name="staging-mode"></a>Modo de teste
O modo de preparo pode ser usado para vários cenários, incluindo:

* Elevada disponibilidade.
* Testar e implantar novas alterações de configuração.
* Introduza um novo servidor e encerre o antigo.

Durante a instalação, você pode selecionar o servidor no **modo de preparo**. Essa ação torna o servidor ativo para importação e sincronização, mas não executa nenhuma exportação. Um servidor no modo de preparo não está executando A sincronização de senha ou o Write-back de senha, mesmo que você tenha selecionado esses recursos durante a instalação. Quando você desabilita o modo de preparo, o servidor inicia a exportação, habilita a sincronização de senha e habilita o Write-back de senha.

> [!NOTE]
> Suponha que você tenha uma Azure AD Connect com o recurso de sincronização de hash de senha habilitado. Quando você habilita o modo de preparo, o servidor para de sincronizar as alterações de senha do AD local. Quando você desabilita o modo de preparo, o servidor retoma a sincronização de alterações de senha de onde ela foi deixada pela última vez. Se o servidor for deixado no modo de preparo por um longo período, pode levar algum tempo para que o servidor sincronize todas as alterações de senha que ocorreram durante o período de tempo.
>
>

Você ainda pode forçar uma exportação usando o Synchronization Service Manager.

Um servidor no modo de preparo continua a receber alterações do Active Directory e do Azure AD e pode assumir rapidamente as responsabilidades de outro servidor em caso de falha. Se você fizer alterações de configuração em seu servidor primário, será sua responsabilidade fazer as mesmas alterações no servidor no modo de preparo.

Para aqueles com conhecimento das tecnologias de sincronização mais antigas, o modo de preparo é diferente, uma vez que o servidor tem seu próprio banco de dados SQL. Essa arquitetura permite que o servidor de modo de preparo esteja localizado em um datacenter diferente.

### <a name="verify-the-configuration-of-a-server"></a>Verificar a configuração de um servidor
Para aplicar esse método, siga estas etapas:

1. [Deixar](#prepare)
2. [Configuração](#configuration)
3. [Importar e sincronizar](#import-and-synchronize)
4. [Confirme](#verify)
5. [Alternar servidor ativo](#switch-active-server)

#### <a name="prepare"></a>Preparar
1. Instale Azure AD Connect, selecione o **modo de preparo**e desmarque **Iniciar sincronização** na última página do assistente de instalação. Esse modo permite que você execute o mecanismo de sincronização manualmente.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/readytoconfigure.png)
2. Desconecte/entre e, no menu Iniciar, selecione **serviço de sincronização**.

#### <a name="configuration"></a>Configuração
Se você tiver feito alterações personalizadas no servidor primário e quiser comparar a configuração com o servidor de preparo, use Azure AD Connect o Documentador de [configuração](https://github.com/Microsoft/AADConnectConfigDocumenter).

#### <a name="import-and-synchronize"></a>Importar e sincronizar
1. Selecione **conectores**e selecione o primeiro conector com o tipo **Active Directory Domain Services**. Clique em **executar**, selecione **importação completa**e **OK**. Siga estas etapas para todos os conectores desse tipo.
2. Selecione o conector com o tipo **Azure Active Directory (Microsoft)** . Clique em **executar**, selecione **importação completa**e **OK**.
3. Verifique se a guia conectores ainda está selecionada. Para cada conector com o tipo **Active Directory Domain Services**, clique em **executar**, selecione **sincronização Delta**e **OK**.
4. Selecione o conector com o tipo **Azure Active Directory (Microsoft)** . Clique em **executar**, selecione **sincronização Delta**e **OK**.

Agora você preparou a exportação de alterações para o Azure AD e o AD local (se você estiver usando a implantação híbrida do Exchange). As próximas etapas permitem que você inspecione o que está prestes a ser alterado antes de realmente iniciar a exportação para os diretórios.

#### <a name="verify"></a>Verificar
1. Inicie um prompt cmd e vá para`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Execução: `csexport "Name of Connector" %temp%\export.xml /f:x`O nome do conector pode ser encontrado no serviço de sincronização. Ele tem um nome semelhante a "contoso.com – AAD" para o Azure AD.
3. Execução: `CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`Você tem um arquivo em% temp% chamado Export. csv que pode ser examinado no Microsoft Excel. Esse arquivo contém todas as alterações que estão prestes a ser exportadas.
4. Faça as alterações necessárias nos dados ou na configuração e execute essas etapas novamente (importar e sincronizar e verificar) até que as alterações que estão prestes a ser exportadas sejam esperadas.

**Noções básicas sobre o arquivo Export. csv** A maior parte do arquivo é auto-explicativa. Algumas abreviações para entender o conteúdo:
* OMODT – tipo de modificação de objeto. Indica se a operação em um nível de objeto é uma adição, atualização ou exclusão.
* AMODT – tipo de modificação de atributo. Indica se a operação em um nível de atributo é uma adição, atualização ou exclusão.

**Recuperar identificadores comuns** O arquivo Export. csv contém todas as alterações que estão prestes a ser exportadas. Cada linha corresponde a uma alteração de um objeto no espaço do conector e o objeto é identificado pelo atributo DN. O atributo DN é um identificador exclusivo atribuído a um objeto no espaço do conector. Quando você tem muitas linhas/alterações no export. csv para analisar, pode ser difícil descobrir para quais objetos as alterações se baseiam apenas no atributo DN. Para simplificar o processo de análise das alterações, use o script do PowerShell csanalyzer. ps1. O script recupera identificadores comuns (por exemplo, displayName, userPrincipalName) dos objetos. Para usar o script:
1. Copie o script do PowerShell da seção [CSAnalyzer](#appendix-csanalyzer) para um arquivo chamado `csanalyzer.ps1`.
2. Abra uma janela do PowerShell e navegue até a pasta em que você criou o script do PowerShell.
3. Executar: `.\csanalyzer.ps1 -xmltoimport %temp%\export.xml`.
4. Agora você tem um arquivo chamado **processedusers1. csv** que pode ser examinado no Microsoft Excel. Observe que o arquivo fornece um mapeamento do atributo DN para identificadores comuns (por exemplo, displayName e userPrincipalName). Atualmente, ele não inclui as alterações de atributo reais que estão prestes a ser exportadas.

#### <a name="switch-active-server"></a>Alternar servidor ativo
1. No servidor ativo no momento, desligue o servidor (DirSync/FIM/Azure AD Sync) para que ele não seja exportado para o Azure AD ou defina-o no modo de preparo (Azure AD Connect).
2. Execute o assistente de instalação do no servidor no **modo de preparo** e desabilite o modo de **preparo**.
   ![ReadyToConfigure](./media/how-to-connect-sync-staging-server/additionaltasks.png)

## <a name="disaster-recovery"></a>Recuperação após desastre
Parte do design de implementação é planejar o que fazer caso haja um desastre em que você perca o servidor de sincronização. Há modelos diferentes a serem usados e qual deles usar depende de vários fatores, incluindo:

* Qual é a sua tolerância para não ser possível fazer alterações em objetos no Azure AD durante o tempo de inatividade?
* Se você usar a sincronização de senha, os usuários aceitarão que eles tenham que usar a senha antiga no Azure AD, caso eles alterem o local?
* Você tem uma dependência em operações em tempo real, como o Write-back de senha?

Dependendo das respostas a essas perguntas e da política da sua organização, uma das estratégias a seguir pode ser implementada:

* Recompilar quando necessário.
* Ter um servidor de espera sobressalente, conhecido como **modo de preparo**.
* Usar máquinas virtuais.

Se você não usar o banco de dados interno do SQL Express, também deverá examinar a seção de [alta disponibilidade do SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Recompilar quando necessário
Uma estratégia viável é planejar uma recompilação do servidor quando necessário. Geralmente, instalar o mecanismo de sincronização e fazer a importação e a sincronização iniciais podem ser concluídos em algumas horas. Se não houver um servidor sobressalente disponível, é possível usar temporariamente um controlador de domínio para hospedar o mecanismo de sincronização.

O servidor do mecanismo de sincronização não armazena nenhum Estado sobre os objetos, de modo que o banco de dados pode ser recriado a partir do Active Directory e do Azure AD. O atributo **sourceAnchor** é usado para unir os objetos do local e da nuvem. Se você recriar o servidor com objetos existentes no local e na nuvem, o mecanismo de sincronização corresponderá esses objetos novamente na reinstalação. As coisas que você precisa documentar e salvar são as alterações de configuração feitas no servidor, como regras de filtragem e de sincronização. Essas configurações personalizadas devem ser reaplicadas antes de iniciar a sincronização.

### <a name="have-a-spare-standby-server---staging-mode"></a>Ter um servidor em espera reserva-modo de preparo
Se você tiver um ambiente mais complexo, é recomendável ter um ou mais servidores em espera. Durante a instalação, você pode habilitar um servidor para estar no **modo de preparo**.

Para obter mais informações, consulte [modo de preparo](#staging-mode).

### <a name="use-virtual-machines"></a>Usar máquinas virtuais
Um método comum e com suporte é executar o mecanismo de sincronização em uma máquina virtual. Caso o host tenha um problema, a imagem com o servidor do mecanismo de sincronização pode ser migrada para outro servidor.

### <a name="sql-high-availability"></a>Alta disponibilidade do SQL
Se você não estiver usando o SQL Server Express fornecido com o Azure AD Connect, a alta disponibilidade para SQL Server também deverá ser considerada. As soluções de alta disponibilidade com suporte incluem clustering do SQL e AOA (Always On grupos de disponibilidade). Soluções sem suporte incluem espelhamento.

O suporte para SQL AOA foi adicionado a Azure AD Connect na versão 1.1.524.0. Você deve habilitar o SQL AOA antes de instalar Azure AD Connect. Durante a instalação, Azure AD Connect detecta se a instância do SQL fornecida está habilitada para SQL AOA ou não. Se o SQL AOA estiver habilitado, Azure AD Connect outras figuras se o SQL AOA estiver configurado para usar replicação síncrona ou replicação assíncrona. Ao configurar o ouvinte do grupo de disponibilidade, é recomendável que você defina a propriedade RegisterAllProvidersIP como 0. Isso ocorre porque Azure AD Connect atualmente usa SQL Native Client para se conectar ao SQL e o SQL Native Client não oferece suporte ao uso da propriedade MultiSubNetFailover.

## <a name="appendix-csanalyzer"></a>Apêndice CSAnalyzer
Consulte a seção [verificar](#verify) como usar esse script.

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

* [Sincronização do Azure AD Connect: Entender e personalizar a sincronização](how-to-connect-sync-whatis.md)  
* [Integrar as identidades no local ao Azure Active Directory](whatis-hybrid-identity.md)  
