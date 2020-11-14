---
title: Utilizar uma partilha de ficheiros do Azure com o Windows | Microsoft Docs
description: Aprenda a utilizar as partilhas de ficheiros Azure com o Windows e o Windows Server. Utilize ações de ficheiros Azure com SMB 3.0 em instalações do Windows que estão a funcionar no local ou em VMs Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e64b7efdd430287a7a3a969c5bf62b0c0e2aec9c
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626899"
---
# <a name="use-an-azure-file-share-with-windows"></a>Utilizar uma partilha de ficheiros do Azure com o Windows
[Ficheiros do Azure](storage-files-introduction.md) é o sistema de ficheiros na cloud fácil de utilizar da Microsoft. As partilhas de ficheiros do Azure podem ser utilizadas de forma totalmente integrada no Windows e no Windows Server. Este artigo aborda as considerações relativas à utilização de uma partilha de ficheiros do Azure com o Windows e o Windows Server.

Para utilizar uma partilha de ficheiros do Azure fora da região do Azure na qual está alojada, como, por exemplo, no local ou noutra região do Azure, o SO tem de suportar SMB 3.0. 

Pode utilizar as partilhas de ficheiros do Azure numa instalação do Windows que esteja a ser executada numa VM do Azure ou no local. A tabela seguinte mostra as versões de SO que suportam o acesso a partilhas de ficheiros e em que ambiente:

| Versão do Windows        | Versão do SMB | Montável em VM do Azure | Montado no local |
|------------------------|-------------|-----------------------|-----------------------|
| Windows Server 2019 | SMB 3.0 | Yes | Yes |
| Windows 10<sup>1</sup> | SMB 3.0 | Yes | Yes |
| Windows Server semi-anual canal<sup>2</sup> | SMB 3.0 | Yes | Yes |
| Windows Server 2016 | SMB 3.0 | Yes | Yes |
| Windows 8.1 | SMB 3.0 | Yes | Yes |
| Windows Server 2012 R2 | SMB 3.0 | Yes | Yes |
| Windows Server 2012 | SMB 3.0 | Yes | Yes |
| Windows 7<sup>3</sup> | SMB 2.1 | Yes | No |
| Windows Server 2008 R2<sup>3</sup> | SMB 2.1 | Yes | No |

<sup>1</sup> Windows 10, versões 1507, 1607, 1803, 1809, 1903, 1909 e 2004.  
<sup>2</sup> Windows Server, versões 1809, 1903, 1909, 2004.  
<sup>3</sup> O suporte regular da Microsoft para o Windows 7 e o Windows Server 2008 R2 terminou. Só é possível adquirir suporte adicional para atualizações de segurança através do [programa Extended Security Update (ESU).](https://support.microsoft.com/help/4497181/lifecycle-faq-extended-security-updates) Recomendamos vivamente a migração destes sistemas operativos.

> [!Note]  
> Recomendamos obter sempre o KB mais recente para a sua versão do Windows.

## <a name="prerequisites"></a>Pré-requisitos 

Confirmar que a porta 445 está aberta: o protocolo SMB requer que a porta TCP 445 esteja aberta; se estive bloqueada, as ligações falham. Pode verificar se a sua firewall está a bloquear a porta 445 com o `Test-NetConnection` cmdlet. Para saber como trabalhar em torno de uma porta 445 bloqueada, consulte a [Causa 1: O Porto 445 está bloqueado](storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) na secção do nosso guia de resolução de problemas do Windows.

## <a name="using-an-azure-file-share-with-windows"></a>Utilizar uma partilha de ficheiros do Azure com o Windows
Para utilizar uma partilha de ficheiros do Azure com o Windows, tem de montá-la, o que significa atribuir uma letra de unidade ou um caminho de ponto de montagem, ou aceder-lhe através do respetivo [caminho UNC](/windows/win32/fileio/naming-a-file). 

Este artigo utiliza a chave da conta de armazenamento para aceder à partilha de ficheiros. Uma chave de conta de armazenamento é uma chave de administrador para uma conta de armazenamento, incluindo permissões de administrador para todos os ficheiros e pastas dentro da partilha de ficheiros a que está a aceder, e para todas as ações de ficheiros e outros recursos de armazenamento (bolhas, filas, tabelas, etc.) contidas na sua conta de armazenamento. Se isto não for suficiente para a sua carga de trabalho, o [Azure File Sync](storage-sync-files-planning.md) pode ser utilizado, ou poderá utilizar [a autenticação baseada na identidade em SMB](storage-files-active-directory-overview.md).

Um padrão comum para fazer a migração lift and shift para o Azure de aplicações de linha de negócio (LOB) que esperam uma partilha de ficheiros SMB é utilizar uma partilha de ficheiros do Azure como alternativa à execução de um servidor de ficheiros do Windows dedicado numa VM do Azure. Uma consideração importante para a migração bem-sucedida de aplicações de linha de negócio de modo a utilizarem uma partilha de ficheiros do Azure é o facto de muitas dessas aplicações serem executadas no contexto de uma conta de serviço dedicada com permissões de sistema limitadas em vez de no contexto da conta administrativa da VM. Por esse motivo, tem de garantir que monta/guarda as credenciais da partilha de ficheiros do Azure no contexto da conta de serviço em vez da conta administrativa.

### <a name="mount-the-azure-file-share"></a>Monte a partilha de ficheiros Azure

O portal Azure fornece-lhe um script que pode usar para montar a sua partilha de ficheiros diretamente para um anfitrião. Recomendamos a utilização deste script fornecido.

Para obter este roteiro:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
1. Navegue para a conta de armazenamento que contém a partilha de ficheiros que gostaria de montar.
1. Selecione **Partilhas de ficheiros**.
1. Selecione a partilha de ficheiros que gostaria de montar.

    :::image type="content" source="media/storage-how-to-use-files-windows/select-file-shares.png" alt-text="exemplo":::

1. Selecione **Connect** (Ligar).

    :::image type="content" source="media/storage-how-to-use-files-windows/file-share-connect-icon.png" alt-text="Screenshot do ícone de ligação para a sua partilha de ficheiros.":::

1. Selecione a letra de unidade para montar a partilha para.
1. Copie o guião fornecido.

    :::image type="content" source="media/storage-how-to-use-files-windows/files-portal-mounting-cmdlet-resize.png" alt-text="Texto de exemplo":::

1. Cole o guião numa concha no anfitrião para onde gostaria de montar a partilha de ficheiros e executá-lo.

Já montou a sua partilha de ficheiros Azure.

### <a name="mount-the-azure-file-share-with-file-explorer"></a>Montar a partilha de ficheiros do Azure com o Explorador de Ficheiros
> [!Note]  
> Tenha em conta que as instruções seguintes são mostradas no Windows 10 e podem ser ligeiramente diferentes nas versões mais antigas. 

1. Abra o Explorador de Ficheiros. Pode fazê-lo através do Menu Iniciar ou ao premir o atalho Win+E.

1. Navegue para **este PC** no lado esquerdo da janela. Esta ação altera os menus disponíveis no friso. No menu Computador, selecione **Mapear unidade de rede**.
    
    ![Instantâneo do menu pendente "Mapear unidade de rede"](./media/storage-how-to-use-files-windows/1_MountOnWindows10.png)

1. Selecione a letra de unidade e entre no caminho unc, o formato de caminho unc é `\\<storageAccountName>.file.core.windows.net\<fileShareName>` . Por exemplo: `\\anexampleaccountname.file.core.windows.net\example-share-name`.
    
    ![Instantâneo da caixa de diálogo “Mapear Unidade de Rede”](./media/storage-how-to-use-files-windows/2_MountOnWindows10.png)

1. Utilize o nome da conta de armazenamento com o prefixo `AZURE\` como o nome de utilizador e a chave da conta de armazenamento como a palavra-passe.
    
    ![Captura de ecrã da caixa de diálogo de credenciais de rede](./media/storage-how-to-use-files-windows/3_MountOnWindows10.png)

1. Utilize a partilha de ficheiros conforme pretendido.
    
    ![A partilha de ficheiros do Azure está agora montada](./media/storage-how-to-use-files-windows/4_MountOnWindows10.png)

1. Quando estiver pronto para desmontar a partilha de ficheiros do Azure, pode fazê-lo ao clicar no botão direito do rato na entrada da partilha, em **Localizações de rede** no Explorador de Ficheiros, e selecionar **Desligar**.

### <a name="accessing-share-snapshots-from-windows"></a>Aceder a instantâneos de partilha do Windows
Se obteve um instantâneo de partilha, manual ou automaticamente através de um script ou um serviço como o Azure Backup, pode ver as versões anteriores de uma partilha, um diretório ou um ficheiro específico a partir da partilha de ficheiros no Windows. Pode tirar uma fotografia de partilha utilizando [a Azure PowerShell,](storage-how-to-use-files-powershell.md) [Azure CLI](storage-how-to-use-files-cli.md)ou o [portal Azure](storage-how-to-use-files-portal.md).

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
| Windows Server 2019                       | Desativado             | Funcionalidade Remover com o Windows |
| Windows Server, versões 1709+            | Desativado             | Funcionalidade Remover com o Windows |
| Windows 10, versões 1709+                | Desativado             | Funcionalidade Remover com o Windows |
| Windows Server 2016                       | Ativado              | Funcionalidade Remover com o Windows |
| Windows 10, versões 1507, 1607 e 1703 | Ativado              | Funcionalidade Remover com o Windows |
| Windows Server 2012 R2                    | Ativado              | Funcionalidade Remover com o Windows | 
| Windows 8.1                               | Ativado              | Funcionalidade Remover com o Windows | 
| Windows Server 2012                       | Ativado              | Desativar com o Registo       | 
| Windows Server 2008 R2                    | Ativado              | Desativar com o Registo       |
| Windows 7                                 | Ativado              | Desativar com o Registo       | 

### <a name="auditing-smb-1-usage"></a>Auditorias à utilização de SMB 1
> Aplica-se ao Windows Server 2019, canal semi-anual do Windows Server (versões 1709 e 1803), Windows Server 2016, Windows 10 (versões 1507, 1607, 1703, 1709 e 1803), Windows Server 2012 R2 e Windows 8.1

Antes de remover o SMB 1 do seu ambiente, poderá ser útil auditar a utilização do protocolo para ver se a remoção danificará algum cliente. Se forem feitos pedidos para partilhas SMB com SMB 1, será registado um evento de auditoria em `Applications and Services Logs > Microsoft > Windows > SMBServer > Audit`. 

> [!Note]  
> Para ativar o suporte para auditorias no Windows Server 2012 R2 e no Windows 8.1, instale pelo menos o [KB4022720](https://support.microsoft.com/help/4022720/windows-8-1-windows-server-2012-r2-update-kb4022720).

Para ativar a auditoria, execute o seguinte cmdlet numa sessão elevada do PowerShell:

```powershell
Set-SmbServerConfiguration –AuditSmb1Access $true
```

### <a name="removing-smb-1-from-windows-server"></a>Remover o SMB 1 do Windows Server
> Aplica-se ao Windows Server 2019, canal semi-anual do Windows Server (versões 1709 e 1803), Windows Server 2016, Windows Server 2012 R2

Para remover o SMB 1 numa instância do Windows Server, execute o seguinte cmdlet numa sessão elevada do PowerShell:

```powershell
Remove-WindowsFeature -Name FS-SMB1
```

Para concluir o processo de remoção, reinicie o servidor. 

> [!Note]  
> A partir do Windows 10 e do Windows Server versão 1709, o SMB 1 não vem instalado por predefinição e tem funcionalidades do Windows separadas para o cliente SMB 1 e o servidor SMB 1. Recomendamos sempre deixar o servidor SMB 1 (`FS-SMB1-SERVER`) e o cliente SMB 1 (`FS-SMB1-CLIENT`) desinstalados.

### <a name="removing-smb-1-from-windows-client"></a>Remover o SMB 1 do cliente Windows
> Aplica-se a Windows 10 (versões 1507, 1607, 1703, 1709 e 1803) e Windows 8.1

Para remover o SMB 1 do cliente Windows, execute o seguinte cmdlet numa sessão elevada do PowerShell:

```powershell
Disable-WindowsOptionalFeature -Online -FeatureName SMB1Protocol
```

Para concluir o processo de remoção, reinicie o PC.

### <a name="disabling-smb-1-on-legacy-versions-of-windowswindows-server"></a>Desativar o SMB 1 em versões antigas do Windows/Windows Server
> Aplica-se a Windows Server 2012, Windows Server 2008 R2 e Windows 7

O SMB 1 não pode ser removido por completo em versões antigas do Windows/Windows Server, mas pode ser desativado através do Registo. Para desativar o SMB 1, crie uma chave de registo `SMB1` nova do tipo `DWORD` com o valor `0` em `HKEY_LOCAL_MACHINE > SYSTEM > CurrentControlSet > Services > LanmanServer > Parameters`.

Também o pode fazer facilmente com o seguinte cmdlet do PowerShell:

```powershell
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters" SMB1 -Type DWORD -Value 0 –Force
```

Depois de criar a chave de registo, tem de reiniciar o servidor para desativar o SMB 1.

### <a name="smb-resources"></a>Recursos do SMB
- [Stop using SMB 1](https://blogs.technet.microsoft.com/filecab/2016/09/16/stop-using-smb1/) (Deixar de utilizar o SMB 1)
- [SMB 1 Product Clearinghouse](https://blogs.technet.microsoft.com/filecab/2017/06/01/smb1-product-clearinghouse/) (Centro de Informações de Produtos que Utilizam o SMB 1)
- [Discover SMB 1 in your environment with DSCEA](/archive/blogs/ralphkyttle/discover-smb1-in-your-environment-with-dscea) (Descobrir o SMB 1 no seu ambiente com DSCEA)
- [Disabling SMB 1 through Group Policy](/archive/blogs/secguide/disabling-smbv1-through-group-policy) (Desativar o SMB 1 através da Política de Grupo)

## <a name="next-steps"></a>Passos seguintes
Veja estas ligações para obter mais informações sobre os Ficheiros do Azure:
- [Planear uma implementação de Ficheiros do Azure](storage-files-planning.md)
- [FAQ](./storage-files-faq.md)
- [Resolução de Problemas no Windows](storage-troubleshoot-windows-file-connection-problems.md)