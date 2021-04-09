---
title: Controle o que um utilizador pode fazer ao nível do ficheiro - Azure file shares
description: Saiba como configurar permissões do Windows ACLs para autenticação AD DS no local para ações de ficheiros Azure. Permitindo-lhe aproveitar o controlo de acesso granular.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/16/2020
ms.author: rogarana
ms.openlocfilehash: 698b4ebedfc9b41e8c5732a0a81226a971d65585
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103470754"
---
# <a name="part-three-configure-directory-and-file-level-permissions-over-smb"></a>Parte três: configurar o diretório e as permissões de nível de ficheiro sobre o SMB 

Antes de iniciar este artigo, certifique-se de que preencheu o artigo anterior, [atribua permissões de nível de partilha a uma identidade](storage-files-identity-ad-ds-assign-permissions.md) para garantir que as suas permissões de nível de partilha estão em vigor.

Depois de atribuir permissões de nível de partilha com o Azure RBAC, tem de configurar ACLs do Windows adequados no nível de raiz, diretório ou ficheiro, para tirar partido do controlo de acesso granular. Pense nas permissões de nível de partilha do Azure RBAC como o gatekeeper de alto nível que determina se um utilizador pode aceder à partilha. Enquanto os ACLs do Windows funcionam a um nível mais granular para determinar que operações o utilizador pode fazer ao nível do diretório ou do ficheiro. Tanto as permissões de nível de partilha como de nível de ficheiro/diretório são aplicadas quando um utilizador tenta aceder a um ficheiro/diretório, por isso, se houver uma diferença entre qualquer um deles, apenas o mais restritivo será aplicado. Por exemplo, se um utilizador tiver acesso de leitura/escrita ao nível do ficheiro, mas apenas ler a nível de partilha, então só pode ler esse ficheiro. O mesmo seria verdade se fosse invertido, e um utilizador tivesse acesso lido/escrito ao nível da partilha, mas apenas lido ao nível do ficheiro, ainda só pode ler o ficheiro.

## <a name="azure-rbac-permissions"></a>Permissões Azure RBAC

A tabela a seguir contém as permissões Azure RBAC relacionadas com esta configuração:


| Papel incorporado  | Permissão NTFS  | Acesso resultante  |
|---------|---------|---------|
|Leitor de Partilhas SMB de Dados de Ficheiros de Armazenamento | Controlo total, Modificar, Ler, Escrever, Executar | Ler e executar  |
|     |   Leitura |     Leitura  |
|Contribuinte de Partilhas SMB de Dados de Ficheiros de Armazenamento  |  Controlo total    |  Modificar, Ler, Escrever, Executar |
|     |  Modificar         |  Modificar    |
|     |  Ler e executar |  Ler e executar |
|     |  Leitura           |  Leitura    |
|     |  Escrita          |  Escrita   |
|Contribuinte Elevado de Partilhas SMB de Dados de Ficheiros de Armazenamento | Controlo total  |  Modificar, Ler, Escrever, Editar, Executar |
|     |  Modificar          |  Modificar |
|     |  Ler e executar  |  Ler e executar |
|     |  Leitura            |  Leitura   |
|     |  Escrita           |  Escrita  |



## <a name="supported-permissions"></a>Permissões apoiadas

O Azure Files suporta todo o conjunto de ACLs básicos e avançados do Windows. Pode visualizar e configurar OS ACLs do Windows em diretórios e ficheiros numa partilha de ficheiros Azure, montando a partilha e, em seguida, utilizando o Windows File Explorer, executando o comando Windows [ICACLs](/windows-server/administration/windows-commands/icacls) ou o comando [Set-ACL.](/powershell/module/microsoft.powershell.security/set-acl) 

Para configurar ACLs com permissões de super-er, tem de montar a parte utilizando a chave da sua conta de armazenamento a partir do seu VM ligado ao domínio. Siga as instruções na secção seguinte para montar uma partilha de ficheiroS Azure a partir da solicitação de comando e para configurar ACLs do Windows.

As seguintes permissões estão incluídas no diretório de raiz de uma partilha de ficheiros:

- BUILTIN\Administradores:(OI)(CI)(F)
- BUILTIN\Utilizadores:(RX)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Utilizadores autenticados:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(F)
- PROPRIETÁRIO DO CRIADOR:(OI)(CI)(IO)(F)

|Utilizadores|Definição|
|---|---|
|BUILTIN\Administrators|Todos os utilizadores que sejam administradores de domínio do ambiente DS AD on-prem.
|BUILTIN\Utilizadores|Grupo de segurança incorporado em AD. Inclui NT AUTHORITY\Utilizadores autenticados por padrão. Para um servidor de ficheiros tradicional, pode configurar a definição de membro por servidor. Para os Ficheiros Azure, não existe um servidor de hospedagem, daí o BUILTIN\Utilizadores inclui o mesmo conjunto de utilizadores que os Utilizadores NT AUTHORITY\Autenticados.|
|NT AUTHORITY\SYSTEM|A conta de serviço do sistema operativo do servidor de ficheiros. Esta conta de serviço não se aplica no contexto Azure Files. Está incluído no diretório de raiz para ser consistente com a experiência do Windows Files Server para cenários híbridos.|
|NT AUTHORITY\Utilizadores autenticados|Todos os utilizadores em AD que podem obter um token Kerberos válido.|
|PROPRIETÁRIO DO CRIADOR|Cada objeto, ou diretório ou ficheiro, tem um proprietário para esse objeto. Se existirem ACLs atribuídos a "CREATOR OWNER" nesse objeto, então o utilizador que é o proprietário deste objeto tem as permissões para o objeto definido pela ACL.|



## <a name="mount-a-file-share-from-the-command-prompt"></a>Monte uma partilha de ficheiro a partir do pedido de comando

Utilize o comando Do Windows `net use` para montar a partilha de ficheiros Azure. Lembre-se de substituir os valores do espaço reservado no exemplo seguinte pelos seus próprios valores. Para obter mais informações sobre a montagem de ações de ficheiros, consulte [utilizar uma partilha de ficheiros Azure com o Windows](storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
  net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> /user:Azure\<storage-account-name> <storage-account-key>
} 
else 
{
  Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN,   Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Se tiver problemas na ligação aos Ficheiros Azure, consulte a [ferramenta de resolução de problemas que publicámos para erros de montagem de Ficheiros Azure no Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Também fornecemos [orientações](./storage-files-faq.md#on-premises-access) para trabalhar em torno de cenários quando a porta 445 está bloqueada. 

## <a name="configure-windows-acls"></a>Configurar ACLs windows

Uma vez montada a sua partilha de ficheiros com a chave da conta de armazenamento, tem de configurar os ACLs do Windows (também conhecidos como permissões NTFS). Pode configurar os ACLs do Windows utilizando o Windows File Explorer ou icacls.

Se tiver diretórios ou ficheiros em servidores de ficheiros no local com DACLs do Windows configurados contra as identidades AD DS, pode copiá-lo para a Azure Files persistindo os ACLs com ferramentas tradicionais de cópia de ficheiros como Robocopy ou [AzCopy AzCopy vs 10.4+](https://github.com/Azure/azure-storage-azcopy/releases). Se os seus diretórios e ficheiros forem hierárquicos para ficheiros Azure através do Azure File Sync, os seus ACLs são transportados e persistidos no seu formato nativo.

### <a name="configure-windows-acls-with-icacls"></a>Configurar ACLs windows com icacls

Utilize o seguinte comando do Windows para conceder permissões completas a todos os diretórios e ficheiros sob a partilha de ficheiros, incluindo o diretório de raiz. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Para obter mais informações sobre como utilizar os ICACLs para definir ACLs do Windows e sobre os diferentes tipos de permissões suportadas, consulte [a referência da linha de comando para icacls](/windows-server/administration/windows-commands/icacls).

### <a name="configure-windows-acls-with-windows-file-explorer"></a>Configure ACLs do Windows com o Explorador de Ficheiros do Windows

Utilize o Windows File Explorer para conceder a permissão total a todos os diretórios e ficheiros sob a partilha de ficheiros, incluindo o diretório de raiz. Se não conseguir carregar corretamente as informações de domínio de AD no Windows File Explorer, é provável que tal seja devido à configuração de confiança no ambiente de AD on-prem. A máquina cliente não foi capaz de chegar ao controlador de domínio AD registado para a autenticação de Ficheiros Azure. Neste caso, utilize icacls para configurar ACLs windows.

1. Abra o Explorador de Ficheiros do Windows e clique no ficheiro/diretório e selecione **Propriedades**.
1. Selecione o separador **Segurança.**
1. **Selecione Edit..** para mudar permissões.
1. Pode alterar as permissões dos utilizadores existentes ou selecionar **Adicionar...** para conceder permissões a novos utilizadores.
1. Na janela de acesso para adicionar novos utilizadores, introduza o nome de utilizador-alvo para que pretenda conceder permissões no **'Introduzir os nomes do objeto' para selecionar** a caixa e selecione **Verificar nomes** para encontrar o nome UPN completo do utilizador-alvo.
1.    Selecione **OK**.
1.    No separador **Segurança,** selecione todas as permissões que pretende conceder ao seu novo utilizador.
1.    Selecione **Aplicar**.


## <a name="next-steps"></a>Passos seguintes

Agora que a funcionalidade está ativada e configurada, continue para o artigo seguinte, onde monta a sua partilha de ficheiros Azure a partir de um VM unido a domínio.

[Parte quatro: montar uma partilha de ficheiros a partir de um VM de domínio](storage-files-identity-ad-ds-mount-file-share.md)
