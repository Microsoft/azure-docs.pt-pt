---
title: Como utilizar o DFS-N com ficheiros Azure
description: Casos comuns de utilização dfs-N com ficheiros Azure
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 3/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a8f1b8c54ad4fd42cc8ebda4965aaf1233143f39
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741990"
---
# <a name="how-to-use-dfs-namespaces-with-azure-files"></a>Como utilizar espaços de nomes DFS com ficheiros Azure
[Os espaços de nome de sistemas de ficheiros distribuídos](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview), vulgarmente designados por DFS Namespaces ou DFS-N, é uma função de servidor do Windows Server que é amplamente utilizada para simplificar a implementação e manutenção de ações de ficheiros SMB na produção. Dfs Namespaces é uma tecnologia de virtualização do espaço de armazenamento, o que significa que permite fornecer uma camada de indireção entre o caminho unc das suas partilhas de ficheiros e as próprias partilhas de ficheiros. Os espaços de nomes DFS funcionam com ações de ficheiros SMB, agnóstico dessas ações de ficheiros estão hospedados: pode ser usado com ações SMB hospedadas num Windows File Server com ou sem Azure File Sync, ações de ficheiros Azure diretamente, ações de ficheiro SMB hospedadas em Ficheiros Azure NetApp ou outras ofertas de terceiros, e mesmo com ações de ficheiros hospedadas noutras nuvens. 

No seu cerne, os dfs Namespaces fornecem um mapeamento entre um caminho unc amigável, como `\\contoso\shares\ProjectX` e o caminho subjacente da UNC da partilha SMB como ou `\\Server01-Prod\ProjectX` `\\storageaccount.file.core.windows.net\projectx` . Quando o utilizador final quer navegar para a sua partilha de ficheiros, eles digitam o caminho unc amigável do utilizador, mas o seu cliente SMB acede ao caminho SMB subjacente do mapeamento. Também pode estender este conceito básico para assumir um nome de servidor de ficheiros existente, como `\\MyServer\ProjectX` . Pode utilizar esta capacidade para alcançar os seguintes cenários:

- Forneça um nome à prova de migração para um conjunto lógico de dados. Neste exemplo, você tem um mapeamento como `\\contoso\shares\Engineering` aquele mapa para `\\OldServer\Engineering` . Quando completar a sua migração para ficheiros Azure, pode alterar o seu mapeamento para que o seu caminho unc amigável para o utilizador aponte para `\\storageaccount.file.core.windows.net\engineering` . Quando um utilizador final acede ao caminho unc amigável, eles serão redirecionados perfeitamente para o caminho da partilha de ficheiros Azure.

- Estabeleça um nome comum para um conjunto lógico de dados que é distribuído a vários servidores em diferentes sites físicos, tais como através do Azure File Sync. Neste exemplo, um nome como `\\contoso\shares\FileSyncExample` é mapeado para vários caminhos da UNC, tais `\\FileSyncServer1\ExampleShare` `\\FileSyncServer2\DifferentShareName` como, `\\FileSyncServer3\ExampleShare` . . . Quando o utilizador acede ao UNC amigável, é-lhes dada uma lista de possíveis caminhos unc e escolhe o mais próximo deles com base nas definições do site do Windows Server Ative (AD).

- Estender um conjunto lógico de dados através do tamanho, IO ou outros limiares de escala. Isto é comum quando se lida com diretórios de utilizadores, onde cada utilizador obtém a sua própria pasta numa ação, ou com ações de risco, onde os utilizadores obtêm espaço arbitrário para lidar com necessidades temporárias de dados. Com os espaços de nomes DFS, juntas várias pastas num espaço coeso de nomes. Por exemplo, `\\contoso\shares\UserShares\user1` mapas para `\\storageaccount.file.core.windows.net\user1` , `\\contoso\shares\UserShares\user2` mapas para `\\storageaccount.file.core.windows.net\user2` , e assim por diante.  

Pode ver um exemplo de como utilizar os espaços de nomes DFS com a sua implementação de Ficheiros Azure na seguinte visão geral de vídeo.

[![Demo sobre como configurar DFS-N com Ficheiros Azure - clique para reproduzir!](./media/files-manage-namespaces/video-snapshot-dfsn.png)](https://www.youtube.com/watch?v=jd49W33DxkQ)
> [!NOTE]  
> Salte para as 10:10 no vídeo para ver como configurar os espaços de nomes DFS.

Se já tiver um ESPAÇO DE NOME DFS no lugar, não são necessários passos especiais para usá-lo com ficheiros Azure e File Sync. Se estiver a aceder à sua partilha de ficheiros Azure a partir do local, aplicam-se considerações normais de networking; ver [considerações de networking de Ficheiros Azure](./storage-files-networking-overview.md) para obter mais informações.

## <a name="namespace-types"></a>Tipos de espaço de nome
Dfs Namespaces fornece dois tipos principais de espaço de nome:

- **Espaço de nome baseado em domínio**: Um espaço de nome hospedado como parte do seu domínio de AD do Servidor do Windows. Os espaços de nome hospedados como parte da AD terão um caminho UNC contendo o nome do seu domínio, por exemplo, `\\contoso.com\shares\myshare` se o seu domínio for `contoso.com` . Os espaços de nome baseados em domínio suportam limites de escala maiores e redundância incorporada através de AD. Os espaços de nome baseados em domínio não podem ser um recurso agrupado num cluster de falhas. 
- **Espaço de nome autónomo**: Um espaço de nome hospedado num servidor individual, não hospedado como parte do Windows Server AD. Os espaços de nome autónomos terão um nome baseado no nome do servidor autónomo, `\\MyStandaloneServer\shares\myshare` como, por exemplo, onde o seu servidor autónomo é nomeado `MyStandaloneServer` . Os espaços de nome autónomo suportam alvos de escala mais baixos do que os espaços de nome baseados em domínio, mas podem ser hospedados como um recurso agrupado num cluster de failover.

## <a name="requirements"></a>Requisitos
Para utilizar espaços de nomes DFS com ficheiros Azure e Sincronização de Ficheiros, tem de ter os seguintes recursos:

- Um domínio de Diretório Ativo. Isto pode ser hospedado em qualquer lugar que você quiser, como no local, em uma máquina virtual Azure (VM), ou mesmo em outra nuvem.
- Um Servidor Windows que pode hospedar o espaço de nome. Um padrão comum de implementação para DFS Namespaces é usar o controlador de domínio do Diretório Ativo para hospedar os espaços de nome, no entanto os espaços de nome podem ser configurados a partir de qualquer servidor com a função de servidor DFS Namespaces instalada. Os espaços de nome DFS estão disponíveis em todas as versões suportadas do Windows Server.
- Uma partilha de ficheiros SMB hospedada num ambiente ligado a domínios, como uma partilha de ficheiros Azure hospedada numa conta de armazenamento unida a domínio, ou uma partilha de ficheiros hospedada num Servidor de Ficheiros do Windows ligado a domínios utilizando o Azure File Sync. Para obter mais informações sobre a adesão ao domínio da sua conta de armazenamento, consulte [a autenticação baseada na identidade.](storage-files-active-directory-overview.md) Os Servidores de Ficheiros do Windows são unidos pelo domínio da mesma forma, independentemente de estar a utilizar o Azure File Sync.
- As partilhas de ficheiros SMB que pretende utilizar com os espaços de nome DFS devem ser alcançáveis a partir das suas redes no local. Isto é principalmente uma preocupação para as ações de ficheiros Azure, no entanto, tecnicamente aplica-se a qualquer ação de ficheiro hospedada em Azure ou em qualquer outra nuvem. Para obter mais informações sobre networking, consulte [considerações de networking para acesso direto](storage-files-networking-overview.md).

## <a name="install-the-dfs-namespaces-server-role"></a>Instale a função de servidor DFS Namespaces
Se já estiver a utilizar espaços de nomes DFS ou pretender configurar espaços de nome DFS no seu controlador de domínio, poderá saltar com segurança estes passos.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para instalar a função de servidor DFS Namespaces, abra o Gestor do Servidor no seu servidor. **Selecione Gerir** e, em seguida, selecione **Adicionar Funções e Funcionalidades**. O assistente resultante guia-o através da instalação dos componentes necessários do Windows para executar e gerir os espaços de nomes DFS. 

Na secção tipo de **instalação** do assistente de instalação, selecione o botão de rádio **de instalação baseado em funções ou baseado em recursos** e selecione **Seguinte**. Na secção seleção do **servidor,** selecione os servidores(s) pretendidos nos quais pretende instalar a função de servidor DFS Namespaces e selecione **Seguinte**. 

Na secção **'Funções do Servidor',** selecione e verifique a função **DFS Namespaces** da lista de funções. Pode encontrá-lo em **Ficheiros de Serviços de Arquivo e Armazenamento** e  >  **serviços ISCSI.** Quando selecionar a função do servidor DFS Namespaces, também pode adicionar quaisquer funções ou funcionalidades necessárias do servidor de suporte que ainda não tenha instalado.

![Uma imagem do assistente **Add Roles and Features** com o papel **DFS Namespaces** selecionado.](./media/files-manage-namespaces/dfs-namespaces-install.png)

Depois de ter verificado a função **DFS Namespaces,** pode selecionar **Next** em todos os ecrãs subsequentes e selecionar **instalar** assim que o assistente ativar o botão. Quando a instalação estiver concluída, poderá configurar o seu espaço de nome.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A partir de uma sessão PowerShell elevada (ou utilizando a remoting PowerShell), execute os seguintes comandos.

```PowerShell
Install-WindowsFeature -Name "FS-DFS-Namespace", "RSAT-DFS-Mgmt-Con"
```
---

## <a name="take-over-existing-server-names-with-root-consolidation"></a>Assuma os nomes dos servidores existentes com a consolidação de raiz
Uma utilização importante para os espaços de nomes DFS é assumir um nome de servidor existente para efeitos de refacção do layout físico das partilhas de ficheiros. Por exemplo, pode pretender consolidar as ações de ficheiros de vários servidores antigos de ficheiros num único servidor de ficheiros durante uma migração de modernização. Tradicionalmente, a familiaridade do utilizador final e a ligação de documentos limitam a sua capacidade de consolidar as partilhas de ficheiros de servidores de ficheiros diferentes em conjunto num hospedeiro, mas a funcionalidade de consolidação de raiz dfs Namespaces permite-lhe levantar um único servidor para vários nomes de servidores e encaminhar para o nome de partilha apropriado.

Embora útil para vários cenários de migração do datacenter, a consolidação de raiz é especialmente útil para a adoção de ações de ficheiros Azure nativos da nuvem porque:

- As ações de ficheiros Azure não permitem manter os nomes dos servidores existentes no local.
- As ações de ficheiros Azure devem ser acedidas através do nome de domínio totalmente qualificado (FQDN) da conta de armazenamento. Por exemplo, uma partilha de ficheiros Azure chamada `share` na conta de armazenamento é sempre `storageaccount` acedida através do `\\storageaccount.file.core.windows.net\share` caminho da UNC. Isto pode ser confuso para os utilizadores finais que esperam um nome curto (ex. `\\MyServer\share`) ou um nome que seja um subdomínio do nome de domínio da organização (ex. `\\MyServer.contoso.com\share`).

A consolidação de raiz só pode ser utilizada com espaços de nome autónomos. Se já tem um espaço de nome baseado em domínios existente para as suas partilhas de ficheiros, não precisa de criar um espaço de nome consolidado de raiz.

### <a name="enabling-root-consolidation"></a>Permitir a consolidação de raízes
A consolidação de raiz pode ser ativada definindo as seguintes teclas de registo a partir de uma sessão elevada de PowerShell (ou utilizando a remoting PowerShell).

```PowerShell
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters" `
    -Type Registry `
    -ErrorAction SilentlyContinue
New-Item `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Type Registry `
    -ErrorAction SilentlyContinue
Set-ItemProperty `
    -Path "HKLM:SYSTEM\CurrentControlSet\Services\Dfs\Parameters\Replicated" `
    -Name "ServerConsolidationRetry" `
    -Value 1
```

### <a name="creating-dns-entries-for-existing-file-server-names"></a>Criação de entradas DNS para nomes de servidores de ficheiros existentes
Para que os espaços de nomes DFS respondam aos nomes existentes do servidor de ficheiros, crie registos de pseudónimos (CNAME) para os servidores de ficheiros existentes que apontam para o nome do servidor DFS Namespaces. O procedimento exato para atualizar os registos DNS pode depender dos servidores que a sua organização está a utilizar e se a sua organização estiver a utilizar ferramentas personalizadas para automatizar a gestão do DNS. Os seguintes passos são mostrados para o servidor DNS incluído com o Windows Server e automaticamente utilizados pelo Windows AD.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Num servidor DNS do Windows, abra a consola de gestão DNS. Isto pode ser encontrado selecionando o botão **Iniciar** e digitando **DNS**. Navegue para a zona de procuração para a frente para o seu domínio. Por exemplo, se o seu domínio `contoso.com` for, a zona de procura para a frente pode ser encontrada em **Zonas de Procuração Avançada**  >  **`contoso.com`** na consola de gestão. A hierarquia exata mostrada neste diálogo dependerá da configuração de DNS para a sua rede.

Clique com o botão direito na sua zona de procuração para a frente e selecione **Novos Pseudónimos (CNAME)**. No diálogo resultante, introduza o nome curto para o servidor de ficheiros que está a substituir (o nome de domínio totalmente qualificado será preenchido automaticamente na caixa de texto com **o nome de domínio totalmente qualificado).** Na caixa de texto rotulada **nome de domínio totalmente qualificado (FQDN) para o anfitrião-alvo,** insira o nome do servidor DFS-N que criou. Pode utilizar o botão **Procurar** para ajudá-lo a selecionar o servidor, se desejar. Selecione **OK** para criar o registo CNAME para o seu servidor.

![Uma imagem que retrata o **New Resource Record** para uma entrada DE DNS CNAME.](./media/files-manage-namespaces/root-consolidation-cname.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Num servidor DNS do Windows, abra uma sessão PowerShell (ou utilize a remoagem PowerShell) para executar os seguintes comandos, povoamento `$oldServer` `$dfsnServer` e, com os valores relevantes para o seu ambiente `$domain` (irá preencher automaticamente com o nome de domínio, mas também pode escrever manualmente também).

```PowerShell
# Variables
$oldServer = "MyServer"
$domain = Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
$dfsnServer = "CloudDFSN.$domain"

# Create CNAME record
Import-Module -Name DnsServer
Add-DnsServerResourceRecordCName `
    -Name $oldServer `
    -HostNameAlias $dfsnServer `
    -ZoneName $domain
```

---

## <a name="create-a-namespace"></a>Criar um espaço de nomes
A unidade básica de gestão para dfs Namespaces é o espaço de nome. A raiz do espaço de nome, ou nome, é o ponto de partida do espaço de nome, de tal forma que no caminho do `\\contoso.com\Public\` UNC, a raiz do espaço de nome é `Public` . 

Se estiver a utilizar os espaços de nomes DFS para assumir um nome de servidor existente com consolidação de raiz, o nome do espaço de nome deve ser o nome do nome do servidor que pretende assumir, pré-preparado com o `#` personagem. Por exemplo, se quisesse assumir um servidor existente chamado `MyServer` , criaria um espaço de nome DFS-N chamado `#MyServer` . A secção PowerShell abaixo cuida de pré-gastos, `#` mas se criar através da consola de Gestão DFS, terá de se preparar conforme apropriado. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar um novo espaço de nome, abra a consola **dfs Management.** Isto pode ser encontrado selecionando o botão **Iniciar** e digitando **a DfS Management**. A consola de gestão resultante tem duas secções **De nomes** e **replicação,** que se referem a dfs Namespaces e REPLICAtion DFS (DFS-R), respectivamente. O Azure File Sync fornece um mecanismo moderno de replicação e sincronização que pode ser usado no lugar de DFS-R se a replicação também for desejada.

Selecione a secção **Espaços nomes** e selecione o botão **Novo Espaço nome** (também pode clicar no botão **'Espaços nomes').** O novo assistente do **novo namespace** que o acompanha através da criação de um espaço de nome. 

A primeira secção do assistente requer que escolha o servidor DFS Namespace para hospedar o espaço de nome. Vários servidores podem hospedar um espaço de nome, mas terá de configurar os espaços de nomes DFS com um servidor de cada vez. Introduza o nome do servidor DFS Namespace pretendido e selecione **Next**. Na secção **Nome e Definições do Espaço Nome,** pode introduzir o nome desejado do seu espaço de nome e selecionar **Em seguida**. 

A secção **Namespace Type** **permite-lhe** escolher entre um espaço de nome baseado em domínio e um **espaço de nome autónomo.** Se pretender utilizar os espaços de nome DFS para preservar o nome do servidor de ficheiros/dispositivo NAS existente, deverá selecionar a opção espaço de nome autónomo. Para outros cenários, deve selecionar um espaço de nome baseado em domínio. Consulte os [tipos de espaço de nome](#namespace-types) acima para obter mais informações sobre a escolha entre os tipos de espaço de nome.

![Uma imagem de seleção entre um espaço de nome baseado em domínio e um espaço de nome autónomo no **New Namespace Wizard**.](./media/files-manage-namespaces/dfs-namespace-type.png)

Selecione o tipo de espaço de nome desejado para o seu ambiente e selecione **Next**. O assistente irá então resumir o espaço de nomes a criar. Selecione **Criar** para criar o espaço de nomes e **fechar** quando o diálogo estiver concluído.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
A partir de uma sessão PowerShell no servidor DFS Namespace, execute os seguintes comandos PowerShell, `$namespace` povoando, e com os `$type` `$takeOverName` valores relevantes para o seu ambiente.

```PowerShell
# Variables
$namespace = "Public"
$type = "DomainV2" # "Standalone"
$takeOverName = $false # $true

$namespace = if ($takeOverName -and $type -eq "Standalone" -and $namespace[0] -ne "#") { 
    "#$namespace" 
} else { $namespace }
$dfsnServer = $env:ComputerName
$namespaceServer = if ($type -eq "DomainV2") { 
    Get-CimInstance -ClassName "Win32_ComputerSystem" | `
    Select-Object -ExpandProperty Domain
} else { $dfsnServer }

# Create share for DFS-N namespace
$smbShare = "C:\DFSRoots\$namespace"
if (!(Test-Path -Path $smbShare)) { New-Item -Path $smbShare -ItemType Directory }
New-SmbShare -Name $namespace -Path $smbShare -FullAccess Everyone

# Create DFS-N namespace
Import-Module -Name DFSN
$namespacePath = "\\$namespaceServer\$namespace"
$targetPath = "\\$dfsnServer\$namespace"
New-DfsnRoot -Path $namespacePath -TargetPath $targetPath -Type $type
```
---

## <a name="configure-folders-and-folder-targets"></a>Configurar pastas e alvos de pastas
Para que um espaço de nome seja útil, deve ter pastas e alvos de pasta. Cada pasta pode ter um ou mais alvos de pasta, que são ponteiros para a(s) partilha de ficheiros SMB que hospedam esse conteúdo. Quando os utilizadores navegam numa pasta com alvos de pasta, o computador cliente recebe uma referência que redireciona o computador cliente de forma transparente para um dos alvos da pasta. Também pode ter pastas sem alvos de pasta para adicionar estrutura e hierarquia ao espaço de nomes.

Pode pensar nas pastas DFS Namespaces como análogas às partilhas de ficheiros. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Na consola dfs Management, selecione o espaço de nomes que acabou de criar e selecione **Nova Pasta**. O diálogo **novo da pasta** resultante permitir-lhe-á criar tanto a pasta como os seus alvos.

![Uma imagem do diálogo **New Folder** .](./media/files-manage-namespaces/dfs-folder-targets.png)

Na caixa de texto o **nome** indicado fornece o nome da pasta. **Selecione Adicionar...** para adicionar alvos de pasta para esta pasta. O diálogo **de adicionar a pasta** resultante fornece uma caixa de texto rotulada Caminho para o alvo da **pasta** onde pode fornecer o caminho unc para a pasta desejada. Selecione **OK** no diálogo **'Adicionar' 'Escolha' do 'Adicionar' 'S'a'.** Se estiver a adicionar um caminho unc a uma partilha de ficheiros Azure, poderá receber uma mensagem a informar que o servidor `storageaccount.file.core.windows.net` não pode ser contactos. Isto é esperado; **selecione Sim** para continuar. Por fim, selecione **OK** no diálogo **New Folder** para criar as metas da pasta e da pasta.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables
$shareName = "MyShare"
$targetUNC = "\\storageaccount.file.core.windows.net\myshare"

# Create folder and folder targets
$sharePath = "$namespacePath\$shareName"
New-DfsnFolder -Path $sharePath -TargetPath $targetUNC
```

---

Agora que criou um espaço de nome, uma pasta e um alvo de pasta, deverá ser capaz de montar a sua partilha de ficheiros através de espaços de nomes DFS. Se estiver a utilizar um espaço de nome baseado em domínio, o caminho completo para a sua parte deve ser `\\<domain-name>\<namespace>\<share>` . Se estiver a utilizar um espaço de nome autónomo, o caminho completo para a sua parte deve ser `\\<DFS-server>\<namespace>\<share>` . Se estiver a utilizar um espaço de nome autónomo com consolidação de raiz, pode aceder diretamente através do nome do seu antigo servidor, como `\\<old-server>\<share>` .

## <a name="see-also"></a>Ver também
- Implantação de uma partilha de ficheiros Azure: [Planeamento para uma implementação de Ficheiros Azure](storage-files-planning.md) e [Como criar uma partilha de ficheiros](storage-how-to-create-file-share.md).
- Configurar o acesso à partilha de [ficheiros: Considerações](storage-files-networking-overview.md) [de autenticação baseadas na identidade](storage-files-active-directory-overview.md) e em rede para acesso direto .
- [Espaços de nome do sistema de ficheiros distribuídos pelo servidor do Windows](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)