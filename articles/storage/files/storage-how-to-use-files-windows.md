---
title: Utilizar uma partilha de ficheiros do Azure com o Windows | Microsoft Docs
description: Saiba como utilizar uma partilha de ficheiros do Azure com o Windows e o Windows Server.
services: storage
author: RenaShahMSFT
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: renash
ms.subservice: files
ms.openlocfilehash: f12f5f1f7791fa09b07c3035db65f2f488f36d7f
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57776585"
---
# <a name="use-an-azure-file-share-with-windows"></a>Utilizar uma partilha de ficheiros do Azure com o Windows
[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. As partilhas de ficheiros do Azure podem ser utilizadas de forma totalmente integrada no Windows e no Windows Server. Este artigo aborda as considerações relativas à utilização de uma partilha de ficheiros do Azure com o Windows e o Windows Server.

Para utilizar uma partilha de ficheiros do Azure fora da região do Azure na qual está alojada, como, por exemplo, no local ou noutra região do Azure, o SO tem de suportar SMB 3.0. 

Pode utilizar as partilhas de ficheiros do Azure numa instalação do Windows que esteja a ser executada numa VM do Azure ou no local. A tabela seguinte mostra as versões de SO que suportam o acesso a partilhas de ficheiros e em que ambiente:

| Versão do Windows        | Versão do SMB | Montável em VM do Azure | Montável no Local |
|------------------------|-------------|-----------------------|----------------------|
| Windows Server 2019    | SMB 3.0 | Sim | Sim |
| Windows 10<sup>1</sup> | SMB 3.0 | Sim | Sim |
| Canal semianual do Windows Server<sup>2</sup> | SMB 3.0 | Sim | Sim |
| Windows Server 2016    | SMB 3.0     | Sim                   | Sim                  |
| Windows 8.1            | SMB 3.0     | Sim                   | Sim                  |
| Windows Server 2012 R2 | SMB 3.0     | Sim                   | Sim                  |
| Windows Server 2012    | SMB 3.0     | Sim                   | Sim                  |
| Windows 7              | SMB 2.1     | Sim                   | Não                   |
| Windows Server 2008 R2 | SMB 2.1     | Sim                   | Não                   |

<sup>1</sup>Windows 10, versões 1507, 1607, 1703, 1709, versão 1803 e 1809.  
<sup>2</sup>Windows Server, versão 1709 e versão 1803.

> [!Note]  
> Recomendamos obter sempre o KB mais recente para a sua versão do Windows.

## <a name="prerequisites"></a>Pré-requisitos 
* **Nome da conta de armazenamento**: Montar uma partilha de ficheiros do Azure, terá o nome da conta de armazenamento.

* **Chave da conta de armazenamento**: Para montar uma partilha de ficheiros do Azure, terá da chave de armazenamento primária (ou secundário). Atualmente, não são suportadas chaves SAS para a montagem.

* **Certifique-se de que a porta 445 está aberta**: Porta TCP 445 estar abertas; requer que o protocolo SMB ligações irão falhar se a porta 445 estiver bloqueada. Pode utilizar o cmdlet `Test-NetConnection` para verificar se a firewall está a bloqueá-la. Pode saber mais sobre [várias formas de resolver bloqueada a porta 445 aqui](https://docs.microsoft.com/en-us/azure/storage/files/storage-troubleshoot-windows-file-connection-problems#cause-1-port-445-is-blocked).

    O seguinte código do PowerShell pressupõe que tem o módulo do PowerShell AzureRM instalado; veja [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps) para obter mais informações. Não se esqueça de substituir `<your-storage-account-name>` e `<your-resource-group-name>` pelos nomes relevantes para a sua conta de armazenamento.

    ```PowerShell
    $resourceGroupName = "<your-resource-group-name>"
    $storageAccountName = "<your-storage-account-name>"

    # This command requires you to be logged into your Azure account, run Login-AzureRmAccount if you haven't
    # already logged in.
    $storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName

    # The ComputerName, or host, is <storage-account>.file.core.windows.net for Azure Public Regions.
    # $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign clouds
    # or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
    Test-NetConnection -ComputerName ([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) -Port 445
    ```

    Se a ligação for bem-sucedida, deverá ver o resultado seguinte:

    ```
    ComputerName     : <storage-account-host-name>
    RemoteAddress    : <storage-account-ip-address>
    RemotePort       : 445
    InterfaceAlias   : <your-network-interface>
    SourceAddress    : <your-ip-address>
    TcpTestSucceeded : True
    ```

    > [!Note]  
    > O comando acima devolve o endereço IP atual da conta de armazenamento. Não é garantido que este endereço IP permaneça igual e poderá sofrer alterações em qualquer altura. Não codifique este endereço IP em scripts nem numa configuração de firewall. 

## <a name="using-an-azure-file-share-with-windows"></a>Utilizar uma partilha de ficheiros do Azure com o Windows
Para utilizar uma partilha de ficheiros do Azure com o Windows, tem de montá-la, o que significa atribuir uma letra de unidade ou um caminho de ponto de montagem, ou aceder-lhe através do respetivo [caminho UNC](https://msdn.microsoft.com/library/windows/desktop/aa365247.aspx). 

Ao contrário de outras partilhas SMB com que possa ter interagido, como as que estão alojadas em servidores do Windows Server, em servidores Linux Samba ou em dispositivos NAS, as partilhas de ficheiros do Azure não suportam, atualmente, a autenticação Kerberos com a sua identidade do Active Directory (AD) nem do Azure Active Directory (AAD), embora estejamos a [trabalhar](https://feedback.azure.com/forums/217298-storage/suggestions/6078420-acl-s-for-azurefiles) nesta funcionalidade. Em vez disso, tem de aceder à partilha de ficheiros do Azure com a chave da conta de armazenamento que contém a sua partilha. A chave da conta de administrador é uma chave de administrador para as contas de armazenamento, que inclui permissões de administrador para todos os ficheiros e pastas dentro da partilha de ficheiros a que está a tentar aceder, bem como outros recursos de armazenamento (blobs, filas, tabelas, etc.) contidos nas contas de armazenamento. Se isso não for suficiente para a sua carga de trabalho, o [Azure File Sync](storage-files-planning.md#data-access-method) poderá satisfazer a ausência da autenticação Kerberos e do suporte da ACL enquanto a autenticação Kerberos baseada no AAD e o suporte da ACL não estiverem disponíveis publicamente.

Um padrão comum para fazer a migração lift and shift para o Azure de aplicações de linha de negócio (LOB) que esperam uma partilha de ficheiros SMB é utilizar uma partilha de ficheiros do Azure como alternativa à execução de um servidor de ficheiros do Windows dedicado numa VM do Azure. Uma consideração importante para a migração bem-sucedida de aplicações de linha de negócio de modo a utilizarem uma partilha de ficheiros do Azure é o facto de muitas dessas aplicações serem executadas no contexto de uma conta de serviço dedicada com permissões de sistema limitadas em vez de no contexto da conta administrativa da VM. Por esse motivo, tem de garantir que monta/guarda as credenciais da partilha de ficheiros do Azure no contexto da conta de serviço em vez da conta administrativa.

### <a name="persisting-azure-file-share-credentials-in-windows"></a>Persistir as credenciais da partilha de ficheiros do Azure no Windows  
O utilitário [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) permite-lhe armazenar as credenciais da conta de armazenamento no Windows. Isto significa que, quando tenta aceder a uma partilha de ficheiros do Azure através do caminho UNC ou quando a monta, não terá de especificar as credenciais. Para guardar as credenciais da conta de armazenamento, execute os seguintes comandos do PowerShell, substituindo `<your-storage-account-name>` e `<your-resource-group-name>` sempre que adequado.

```PowerShell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName

# The cmdkey utility is a command-line (rather than PowerShell) tool. We use Invoke-Expression to allow us to 
# consume the appropriate values from the storage account variables. The value given to the add parameter of the
# cmdkey utility is the host address for the storage account, <storage-account>.file.core.windows.net for Azure 
# Public Regions. $storageAccount.Context.FileEndpoint is used because non-Public Azure regions, such as sovereign 
# clouds or Azure Stack deployments, will have different hosts for Azure file shares (and other storage resources).
Invoke-Expression -Command ("cmdkey /add:$([System.Uri]::new($storageAccount.Context.FileEndPoint).Host) " + `
    "/user:AZURE\$($storageAccount.StorageAccountName) /pass:$($storageAccountKeys[0].Value)")
```

Pode utilizar o parâmetro “list” para verificar se o utilitário cmdkey armazenou a credencial da conta de armazenamento:

```PowerShell
cmdkey /list
```

Se as credenciais da partilha de ficheiros do Azure forem armazenadas, o resultado esperado é o seguinte (poderá haver chaves adicionais armazenadas na lista):

```
Currently stored credentials:

Target: Domain:target=<storage-account-host-name>
Type: Domain Password
User: AZURE\<your-storage-account-name>
```

Agora, deverá conseguir montar ou aceder à partilha sem ter de indicar credenciais adicionais.

#### <a name="advanced-cmdkey-scenarios"></a>Cenários de cmdkey avançados
Existem outros dois cenários para considerar a utilização de cmdkey. Um é armazenar as credenciais de outro utilizador no computador, como uma conta de serviço e o outro é armazenar as credenciais num computador remoto com o PowerShell remoto.

É muito fácil armazenar as credenciais de outro utilizador no computador. Quando tiver sessão iniciada na sua conta, basta executar o seguinte comando do PowerShell:

```PowerShell
$password = ConvertTo-SecureString -String "<service-account-password>" -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "<service-account-username>", $password
Start-Process -FilePath PowerShell.exe -Credential $credential -LoadUserProfile
```

É aberta uma janela do PowerShell nova sob o contexto do utilizador da sua conta de serviço (ou conta de utilizador). Depois, pode utilizar o utilitário cmdkey conforme descrito [acima](#persisting-azure-file-share-credentials-in-windows).

Contudo, armazenar as credenciais num computador remoto com o PowerShell remoto não é possível, uma vez que cmdkey não permite o acesso, nem mesmo para adições, ao respetivo arquivo de credenciais se o utilizador tiver sessão iniciada através daquele. Recomendamos iniciar sssão no computador com o [Ambiente de Trabalho Remoto](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/windows).

### <a name="mount-the-azure-file-share-with-powershell"></a>Montar a partilha de ficheiros do Azure com o PowerShell
Para montar a partilha de ficheiros do Azure, eecute os seguintes comandos numa sessão do PowerShell normal (ou seja, não numa sessão elevada). Não se esqueça de substituir `<your-resource-group-name>`, `<your-storage-account-name>`, `<your-file-share-name>` e `<desired-drive-letter>` pelas informações adequadas.

```PowerShell
$resourceGroupName = "<your-resource-group-name>"
$storageAccountName = "<your-storage-account-name>"
$fileShareName = "<your-file-share-name>"

# These commands require you to be logged into your Azure account, run Login-AzAccount if you haven't
# already logged in.
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName
$storageAccountKeys = Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object { 
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("Azure file share not found")
}

# The value given to the root parameter of the New-PSDrive cmdlet is the host address for the storage account, 
# <storage-account>.file.core.windows.net for Azure Public Regions. $fileShare.StorageUri.PrimaryUri.Host is 
# used because non-Public Azure regions, such as sovereign clouds or Azure Stack deployments, will have different 
# hosts for Azure file shares (and other storage resources).
$password = ConvertTo-SecureString -String $storageAccountKeys[0].Value -AsPlainText -Force
$credential = New-Object System.Management.Automation.PSCredential -ArgumentList "AZURE\$($storageAccount.StorageAccountName)", $password
New-PSDrive -Name <desired-drive-letter> -PSProvider FileSystem -Root "\\$($fileShare.StorageUri.PrimaryUri.Host)\$($fileShare.Name)" -Credential $credential -Persist
```

> [!Note]  
> Utilizar a opção `-Persist` no cmdlet `New-PSDrive` só permitirá que a partilha de ficheiros seja montada novamente no arranque se as credenciais forem guardadas. Pode utilizar o cmdkey conforme [descrito anteriormente](#persisting-azure-file-share-credentials-in-windows) para guardar as credenciais. 

Se pretendido, pode desmontar a partilha de ficheiros do Azure com o seguinte cmdlet do PowerShell.

```PowerShell
Remove-PSDrive -Name <desired-drive-letter>
```

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Montar a partilha de ficheiros do Azure com o Explorador de Ficheiros
> [!Note]  
> Tenha em conta que as instruções seguintes são mostradas no Windows 10 e podem ser ligeiramente diferentes nas versões mais antigas. 

1. Abra o Explorador de Ficheiros. Pode fazê-lo através do Menu Iniciar ou ao premir o atalho Win+E.

2. Navegue para o item **Este PC**, no lado esquerdo da janela. Esta ação altera os menus disponíveis no friso. No menu Computador, selecione **Mapear unidade de rede**.
    
    ![Instantâneo do menu pendente "Mapear unidade de rede"](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

3. Copie o caminho UNC do painel **Ligar** para o portal do Azure. 

    ![O caminho UNC a partir do painel Ligar dos Ficheiros do Azure](./media/storage-how-to-use-files-windows/portal_netuse_connect.png)

4. Selecione a letra de unidade e introduza o caminho UNC. 
    
    ![Instantâneo da caixa de diálogo “Mapear Unidade de Rede”](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

5. Utilize o nome da conta de armazenamento com o prefixo `AZURE\` como o nome de utilizador e a chave da conta de armazenamento como a palavra-passe.
    
    ![Captura de ecrã da caixa de diálogo de credenciais de rede](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

6. Utilize a partilha de ficheiros conforme pretendido.
    
    ![A partilha de ficheiros do Azure está agora montada](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

7. Quando estiver pronto para desmontar a partilha de ficheiros do Azure, pode fazê-lo ao clicar no botão direito do rato na entrada da partilha, em **Localizações de rede** no Explorador de Ficheiros, e selecionar **Desligar**.

### <a name="accessing-share-snapshots-from-windows"></a>Aceder a instantâneos de partilha do Windows
Se obteve um instantâneo de partilha, manual ou automaticamente através de um script ou um serviço como o Azure Backup, pode ver as versões anteriores de uma partilha, um diretório ou um ficheiro específico a partir da partilha de ficheiros no Windows. Pode obter um instantâneo de partilha a partir do [Portal do Azure](storage-how-to-use-files-portal.md), do [Azure PowerShell](storage-how-to-use-files-powershell.md) e da [CLI do Azure](storage-how-to-use-files-cli.md).

#### <a name="list-previous-versions"></a>Listar versões anteriores
Navegue para o item ou o item principal que precisa de restaurar. Faça duplo clique para ir para o diretório pretendido. Clique com o botão direito do rato e selecione **Propriedades** no menu.

![Clique com o botão direito do rato no menu para um diretório selecionado](./media/storage-how-to-use-files-windows/snapshot-windows-previous-versions.png)

Selecione **Versões Anteriores** para ver a lista de instantâneos de partilha para este diretório. A lista poderá demorar alguns segundos para carregar, consoante a velocidade da rede e o número de instantâneos de partilha no diretório.

![Separador Versões Anteriores](./media/storage-how-to-use-files-windows/snapshot-windows-list.png)

Pode selecionar **Abrir** para abrir um instantâneo específico. 

![Instantâneo aberto](./media/storage-how-to-use-files-windows/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Restaurar de uma versão anterior
Selecione **Restaurar** para copiar os conteúdos do diretório inteiro recursivamente no momento de criação do instantâneo de partilha para a localização original.
 ![Botão de restauro na mensagem de aviso](./media/storage-how-to-use-files-windows/snapshot-windows-restore.png) 

## <a name="securing-windowswindows-server"></a>Proteger o Windows/Windows Server
Para poder montar uma partilha de ficheiros do Azure no Windows, a porta 445 tem de estar acessível. Muitas organizações optam por bloquear esta porta devido a riscos de segurança inerentes ao SMB 1. O SMB 1, também conhecido como CIFS (Common Internet File System), é um protocolo de sistema de ficheiros legado incluído no Windows e no Windows Server. O SMB 1 é um protocolo desatualizado, ineficiente e, acima de tudo, inseguro. A boa notícia é que os Ficheiros do Azure não suportam SMB 1 e todas as versões suportadas do Windows e do Windows Server permitem removê-lo ou desativá-lo. Antes de utilizar partilhas de ficheiros do Azure em produção, [recomendamos vivamente](https://aka.ms/stopusingsmb1) remover ou desativar sempre o cliente SMB 1 e o servidor no Windows.

A tabela abaixo contém informações detalhadas sobre o estado de SMB 1 em cada versão do Windows:

| Versão do Windows                           | Estado predefinido de SMB 1 | Método de Desativação/Remoção       | 
|-------------------------------------------|----------------------|-----------------------------|
| Windows Server 2019 (pré-visualização)             | Desactivado             | Funcionalidade Remover com o Windows |
| Windows Server, versões 1709+            | Desactivado             | Funcionalidade Remover com o Windows |
| Windows 10, versões 1709+                | Desactivado             | Funcionalidade Remover com o Windows |
| Windows Server 2016                       | Activado              | Funcionalidade Remover com o Windows |
| Windows 10, versões 1507, 1607 e 1703 | Activado              | Funcionalidade Remover com o Windows |
| Windows Server 2012 R2                    | Activado              | Funcionalidade Remover com o Windows | 
| Windows 8.1                               | Activado              | Funcionalidade Remover com o Windows | 
| Windows Server 2012                       | Activado              | Desativar com o Registo       | 
| Windows Server 2008 R2                    | Activado              | Desativar com o Registo       |
| Windows 7                                 | Activado              | Desativar com o Registo       | 

### <a name="auditing-smb-1-usage"></a>Auditorias à utilização de SMB 1
> Aplica-se a Windows Server 2019 (pré-visualização), canal semianual do Windows Server (versões 1709 e 1803), Windows Server 2016, Windows 10 (versões 1507, 1607, 1703, 1709 e 1803), Windows Server 2012 R2 e Windows 8.1

Antes de remover o SMB 1 do seu ambiente, poderá ser útil auditar a utilização do protocolo para ver se a remoção danificará algum cliente. Se forem feitos pedidos para partilhas SMB com SMB 1, será registado um evento de auditoria em `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit`. 

> [!Note]  
> Para ativar o suporte para auditorias no Windows Server 2012 R2 e no Windows 8.1, instale pelo menos o [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Para ativar a auditoria, execute o seguinte cmdlet numa sessão elevada do PowerShell:

```PowerShell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Remover o SMB 1 do Windows Server
> Aplica-se a Windows Server 2019 (pré-visualização), canal semianual do Windows Server (versões 1709 e 1803), Windows Server 2016 e Windows Server 2012 R2

Para remover o SMB 1 numa instância do Windows Server, execute o seguinte cmdlet numa sessão elevada do PowerShell:

```PowerShell
Remove-WindowsFeature -Name FS-SMB1
```

Para concluir o processo de remoção, reinicie o servidor. 

> [!Note]  
> A partir do Windows 10 e do Windows Server versão 1709, o SMB 1 não vem instalado por predefinição e tem funcionalidades do Windows separadas para o cliente SMB 1 e o servidor SMB 1. Recomendamos sempre deixar o servidor SMB 1 (`FS-SMB1-SERVER`) e o cliente SMB 1 (`FS-SMB1-CLIENT`) desinstalados.

### <a name="removing-smb-1-from-windows-client"></a>Remover o SMB 1 do cliente Windows
> Aplica-se a Windows 10 (versões 1507, 1607, 1703, 1709 e 1803) e Windows 8.1

Para remover o SMB 1 do cliente Windows, execute o seguinte cmdlet numa sessão elevada do PowerShell:

```PowerShell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Para concluir o processo de remoção, reinicie o PC.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Desativar o SMB 1 em versões antigas do Windows/Windows Server
> Aplica-se a Windows Server 2012, Windows Server 2008 R2 e Windows 7

O SMB 1 não pode ser removido por completo em versões antigas do Windows/Windows Server, mas pode ser desativado através do Registo. Para desativar o SMB 1, crie uma chave de registo `SMB1` nova do tipo `DWORD` com o valor `0` em `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters`.

Também o pode fazer facilmente com o seguinte cmdlet do PowerShell:

```PowerShell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Depois de criar a chave de registo, tem de reiniciar o servidor para desativar o SMB 1.

### <a name="smb-resources"></a>Recursos do SMB
- [Stop using SMB 1](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/) (Deixar de utilizar o SMB 1)
- [SMB 1 Product Clearinghouse](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/) (Centro de Informações de Produtos que Utilizam o SMB 1)
- [Discover SMB 1 in your environment with DSCEA](https://blogs.technet.microsoft.com/ralphkyttle/2017/04/07/discover-smb1-in-your-environment-with-dscea/) (Descobrir o SMB 1 no seu ambiente com DSCEA)
- [Disabling SMB 1 through Group Policy](https://blogs.technet.microsoft.com/secguide/2017/06/15/disabling-smbv1-through-group-policy/) (Desativar o SMB 1 através da Política de Grupo)

## <a name="next-steps"></a>Passos Seguintes
Veja estas ligações para obter mais informações sobre os Ficheiros do Azure:
- [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [Resolução de Problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)      
