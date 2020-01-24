---
title: Solucionar problemas Sincronização de Arquivos do Azure | Microsoft Docs
description: Solucionar problemas comuns com o Sincronização de Arquivos do Azure.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 1/22/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 009d9e864773fb3a2578504b043fb30302cedb22
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2020
ms.locfileid: "76704549"
---
# <a name="troubleshoot-azure-file-sync"></a>Resolver problemas da Sincronização de Ficheiros do Azure
Use Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em arquivos do Azure, mantendo, ao mesmo tempo, a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Você pode usar qualquer protocolo que esteja disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter quantos caches forem necessários em todo o mundo.

Este artigo foi criado para ajudá-lo a solucionar problemas e solucionar questões que podem ser encontradas com sua implantação do Sincronização de Arquivos do Azure. Também descreveremos como coletar logs importantes do sistema se uma investigação mais profunda do problema for necessária. Se você não vir a resposta para sua pergunta, poderá entrar em contato conosco por meio dos seguintes canais (em ordem de escalonamento):

1. [Fórum do armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [UserVoice de arquivos do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Suporte da Microsoft. Para criar uma nova solicitação de suporte, na portal do Azure, na guia **ajuda** , selecione o botão **ajuda + suporte** e, em seguida, selecione **nova solicitação de suporte**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Estou tendo um problema com Sincronização de Arquivos do Azure no meu servidor (sincronização, camadas de nuvem, etc.). Devo remover e recriar meu ponto de extremidade do servidor?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Instalação do agente e registro de servidor
<a id="agent-installation-failures"></a>**Solucionar problemas de falhas de instalação do agente**  
Se a instalação do agente de Sincronização de Arquivos do Azure falhar, em um prompt de comando com privilégios elevados, execute o seguinte comando para ativar o registro em log durante a instalação do agente:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Examine o Installer. log para determinar a causa da falha na instalação.

<a id="agent-installation-on-DC"></a>**Falha na instalação do agente no controlador de Domínio do Active Directory**  
Se você tentar instalar o agente de sincronização em um controlador de domínio Active Directory em que o proprietário da função de PDC está em uma versão do sistema operacional Windows Server 2008 R2 ou inferior, você poderá atingir o problema em que o agente de sincronização não será instalado.

Para resolver, transfira a função de PDC para outro controlador de domínio que executa o Windows Server 2012 R2 ou mais recente e, em seguida, instale a sincronização.

<a id="parameter-is-incorrect"></a>**Falha ao acessar um volume no Windows Server 2012 R2 com o erro: o parâmetro está incorreto**  
Depois de criar um ponto de extremidade de servidor no Windows Server 2012 R2, o seguinte erro ocorrerá ao acessar o volume:

letra_da_unidade: \ Não está acessível.  
Parâmetro incorreto.

Para resolver, instale as atualizações mais recentes do Windows Server 2012 R2 e reinicie o servidor.

<a id="server-registration-missing-subscriptions"></a>**O registro do servidor não lista todas as assinaturas do Azure**  
Ao registrar um servidor usando o ServerRegistration. exe, as assinaturas estão ausentes quando você clica na lista suspensa assinatura do Azure.

Esse problema ocorre porque o ServerRegistration. exe não oferece suporte a ambientes de multilocatário no momento. Esse problema será corrigido em uma atualização futura do agente de Sincronização de Arquivos do Azure.

Para solucionar esse problema, use os seguintes comandos do PowerShell para registrar o servidor:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**O registro do servidor exibe a seguinte mensagem: "pré-requisitos estão ausentes"**  
Essa mensagem será exibida se o módulo do PowerShell AZ ou AzureRM não estiver instalado no PowerShell 5,1. 

> [!Note]  
> ServerRegistration. exe não dá suporte ao PowerShell 6. x. Você pode usar o cmdlet Register-AzStorageSyncServer no PowerShell 6. x para registrar o servidor.

Para instalar o módulo AZ ou AzureRM no PowerShell 5,1, execute as seguintes etapas:

1. Digite **PowerShell** em um prompt de comandos com privilégios elevados e pressione Enter.
2. Instale o módulo AZ ou AzureRM mais recente seguindo a documentação:
    - [Módulo AZ (requer .NET 4.7.2)](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [Módulo AzureRM]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Execute ServerRegistration. exe e conclua o assistente para registrar o servidor com um serviço de sincronização de armazenamento.

<a id="server-already-registered"></a>**O registro do servidor exibe a seguinte mensagem: "este servidor já está registrado"** 

![Uma captura de tela da caixa de diálogo de registro do servidor com a mensagem de erro "o servidor já está registrado"](media/storage-sync-files-troubleshoot/server-registration-1.png)

Essa mensagem será exibida se o servidor tiver sido registrado anteriormente com um serviço de sincronização de armazenamento. Para cancelar o registro do servidor do serviço de sincronização de armazenamento atual e, em seguida, registrar-se com um novo serviço de sincronização de armazenamento, conclua as etapas descritas em [cancelar o registro de um servidor com o sincronização de arquivos do Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se o servidor não estiver listado em **servidores registrados** no serviço de sincronização de armazenamento, no servidor no qual você deseja cancelar o registro, execute os seguintes comandos do PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se o servidor fizer parte de um cluster, você poderá usar o parâmetro opcional *Reset-StorageSyncServer-CleanClusterRegistration* para também remover o registro do cluster.

<a id="web-site-not-trusted"></a>**Quando registro um servidor, vejo várias respostas de "sites não confiáveis". Por?**  
Esse problema ocorre quando a política de **segurança aprimorada do Internet Explorer** é habilitada durante o registro do servidor. Para obter mais informações sobre como desabilitar corretamente a política de **segurança aprimorada do Internet Explorer** , consulte [preparar o Windows Server para usar com sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) e [como implantar sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**O servidor não está listado em servidores registrados no portal do Azure**  
Se um servidor não estiver listado em **servidores registrados** para um serviço de sincronização de armazenamento:
1. Entre no servidor que você deseja registrar.
2. Abra o explorador de arquivos e vá para o diretório de instalação do agente de sincronização de armazenamento (o local padrão é C:\Program Files\Azure\StorageSyncAgent). 
3. Execute ServerRegistration. exe e conclua o assistente para registrar o servidor com um serviço de sincronização de armazenamento.

## <a name="sync-group-management"></a>Gerenciamento de grupo de sincronização
<a id="cloud-endpoint-using-share"></a>**Falha na criação do ponto de extremidade de nuvem, com este erro: "o FileShare do Azure especificado já está em uso por um diferente diferente"**  
Este erro ocorrerá se a partilha de ficheiros do Azure já estiver a ser utilizada por outro ponto final da cloud. 

Se você vir essa mensagem e o compartilhamento de arquivos do Azure não estiver em uso no momento por um ponto de extremidade de nuvem, conclua as seguintes etapas para limpar os metadados de Sincronização de Arquivos do Azure no compartilhamento de arquivos do Azure:

> [!Warning]  
> Excluir os metadados em um compartilhamento de arquivos do Azure que está atualmente em uso por um ponto de extremidade de nuvem faz com que Sincronização de Arquivos do Azure operações falhem. 

1. Na portal do Azure, vá para o compartilhamento de arquivos do Azure.  
2. Clique com o botão direito do mouse no compartilhamento de arquivos do Azure e selecione **Editar metadados**.
3. Clique com o botão direito do mouse em **SyncService**e selecione **excluir**.

<a id="cloud-endpoint-authfailed"></a>**Falha na criação do ponto de extremidade de nuvem, com este erro: "AuthorizationFailed"**  
Esse erro ocorrerá se sua conta de usuário não tiver direitos suficientes para criar um ponto de extremidade de nuvem. 

Para criar um ponto de extremidade de nuvem, sua conta de usuário deve ter as seguintes permissões de autorização da Microsoft:  
* Leitura: obter definição de função
* Gravação: criar ou atualizar definição de função personalizada
* Leitura: obter atribuição de função
* Gravação: criar atribuição de função

As seguintes funções internas têm as permissões de autorização da Microsoft necessárias:  
* Proprietário
* Administrador de Acesso de Utilizador

Para determinar se sua função de conta de usuário tem as permissões necessárias:  
1. Na portal do Azure, selecione **grupos de recursos**.
2. Selecione o grupo de recursos em que a conta de armazenamento está localizada e, em seguida, selecione **controle de acesso (iam)** .
3. Selecione a guia **atribuições de função** .
4. Selecione a **função** (por exemplo, proprietário ou colaborador) para sua conta de usuário.
5. Na lista **provedor de recursos** , selecione **autorização da Microsoft**. 
    * A **atribuição de função** deve ter permissões de **leitura** e **gravação** .
    * A **definição de função** deve ter permissões de **leitura** e **gravação** .

<a id="-2134375898"></a>**Falha na criação do ponto de extremidade do servidor, com este erro: "MgmtServerJobFailed" (código de erro:-2134375898 ou 0x80c80226)**  
Este erro ocorrerá se o caminho do ponto final do servidor estiver no volume de sistema e o arrumo na cloud estiver ativado. O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.

<a id="-2147024894"></a>**Falha na criação do ponto de extremidade do servidor, com este erro: "MgmtServerJobFailed" (código de erro:-2147024894 ou 0x80070002)**  
Este erro ocorrerá se o caminho do ponto final do servidor especificado não for válido. Confirme se o caminho do ponto final do servidor especificado é um volume NTFS ligado localmente. Observe que Sincronização de Arquivos do Azure não oferece suporte a unidades mapeadas como um caminho de ponto de extremidade do servidor.

<a id="-2134375640"></a>**Falha na criação do ponto de extremidade do servidor, com este erro: "MgmtServerJobFailed" (código de erro:-2134375640 ou 0x80c80328)**  
Esse erro ocorrerá se o caminho do ponto de extremidade do servidor especificado não for um volume NTFS. Confirme se o caminho do ponto final do servidor especificado é um volume NTFS ligado localmente. Observe que Sincronização de Arquivos do Azure não oferece suporte a unidades mapeadas como um caminho de ponto de extremidade do servidor.

<a id="-2134347507"></a>**Falha na criação do ponto de extremidade do servidor, com este erro: "MgmtServerJobFailed" (código de erro:-2134347507 ou 0x80c8710d)**  
Este erro ocorre porque o Azure File Sync não suporta pontos finais de servidor em volumes que tenham uma pasta de Informações de Volume de Sistema comprimida. Para resolver este problema, descomprima a pasta de Informações de Volume de Sistema. Se a pasta de Informações de Volume de Sistema for a única pasta comprimida no volume, execute os seguintes passos:

1. Baixe a ferramenta [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec) .
2. Execute o seguinte comando em um prompt de comando elevado para iniciar um prompt de comando em execução na conta do sistema: **PsExec. exe-i-s-d cmd**
3. Na linha de comandos em execução na conta do sistema, escreva os seguintes comandos e prima enter:   
    **CD/d "letra da unidade: \ informações do volume do sistema"**  
    **compactar/u/s**

<a id="-2134376345"></a>**Falha na criação do ponto de extremidade do servidor, com este erro: "MgmtServerJobFailed" (código de erro:-2134376345 ou 0x80C80067)**  
Este erro ocorrerá se o limite de pontos finais do servidor por servidor for atingido. O Azure File Sync suporta atualmente até 30 pontos finais por servidor. Para obter mais informações, consulte [sincronização de arquivos do Azure dimensionar destinos](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Falha na criação do ponto de extremidade do servidor, com este erro: "MgmtServerJobFailed" (código de erro:-2134376427 ou 0x80c80015)**  
Este erro ocorrerá se outro ponto final do servidor já estiver a sincronizar o caminho do ponto final do servidor especificado. O Azure File Sync não suporta múltiplos pontos finais de servidor a sincronizar o mesmo diretório ou volume.

<a id="-2160590967"></a>**Falha na criação do ponto de extremidade do servidor, com este erro: "MgmtServerJobFailed" (código de erro:-2160590967 ou 0x80c80077)**  
Esse erro ocorrerá se o caminho do ponto de extremidade do servidor contiver arquivos em camadas órfãos. Se um ponto de extremidade do servidor tiver sido removido recentemente, aguarde a conclusão da limpeza de arquivos em camadas órfãos. Uma ID de evento 6662 é registrada no log de eventos de telemetria quando a limpeza de arquivos em camadas órfãos é iniciada. Uma ID de evento 6661 será registrada depois que a limpeza de arquivos em camadas órfãos for concluída e um ponto de extremidade do servidor puder ser recriado usando o caminho. Se a criação do ponto de extremidade do servidor falhar depois que uma ID de evento 6661 for registrada, remova os arquivos em camadas órfãos executando as etapas documentadas nos [arquivos em camadas não estarão acessíveis no servidor após a exclusão de uma seção de ponto de extremidade do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) .

<a id="-2134347757"></a>**Falha na exclusão do ponto de extremidade do servidor, com este erro: "MgmtServerJobExpired" (código de erro:-2134347757 ou 0x80c87013)**  
Este erro ocorrerá se o servidor estiver offline ou não tiver conectividade de rede. Se o servidor deixar de estar disponível, anule o registo do servidor no portal, o que eliminará os pontos finais do servidor. Para excluir os pontos de extremidade do servidor, siga as etapas descritas em [cancelar o registro de um servidor com sincronização de arquivos do Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Não é possível abrir a página de propriedades do ponto de extremidade do servidor ou atualizar política de camadas de nuvem**  
Esse problema pode ocorrer se uma operação de gerenciamento no ponto de extremidade do servidor falhar. Se a página de propriedades do ponto de extremidade do servidor não abrir no portal do Azure, atualizar o ponto de extremidade do servidor usando comandos do PowerShell do servidor pode corrigir esse problema. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**O ponto de extremidade do servidor tem um status de integridade "nenhuma atividade" ou "pendente" e o estado do servidor na folha servidores registrados é "aparece offline"**  

Esse problema pode ocorrer se o processo do monitor de sincronização de armazenamento (AzureStorageSyncMonitor. exe) não estiver em execução ou se o servidor não puder acessar o serviço de Sincronização de Arquivos do Azure.

No servidor que está sendo exibido como "aparece offline" no portal, examine a ID do evento 9301 no log de eventos de telemetria (localizado em Applications and Services\Microsoft\FileSync\Agent in Visualizador de Eventos) para determinar por que o servidor não consegue acessar o Sincronização de Arquivos do Azure serviço. 

- Se **GetNextJob concluído com o status: 0** for registrado, o servidor poderá se comunicar com o serviço de sincronização de arquivos do Azure. 
    - Abra o Gestor de Tarefas no servidor e verifique se o processo Monitor da Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) está em execução. Se não estiver em execução, experimente primeiro reiniciar o servidor. Se o reinício do servidor não resolver o problema, atualize para a [versão do agente](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) do Azure File Sync mais recente. 

- Se **GetNextJob concluído com status:-2134347756** for registrado, o servidor não poderá se comunicar com o serviço de sincronização de arquivos do Azure devido a um firewall ou proxy. 
    - Se o servidor estiver por trás de uma firewall, verifique se a porta 443 de saída é permitida. Se o firewall restringe o tráfego para domínios específicos, confirme se os domínios listados na [documentação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) do firewall estão acessíveis.
    - Se o servidor estiver protegido por um proxy, defina as configurações de proxy específicas do computador ou do aplicativo seguindo as etapas na [documentação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)do proxy.
    - Use o cmdlet Test-StorageSyncNetworkConnectivity para verificar a conectividade de rede para os pontos de extremidade de serviço. Para saber mais, confira [testar a conectividade de rede para pontos de extremidade de serviço](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).

- Se **GetNextJob concluído com status:-2134347764** for registrado, o servidor não poderá se comunicar com o serviço de sincronização de arquivos do Azure devido a um certificado expirado ou excluído.  
    - Execute o seguinte comando do PowerShell no servidor para redefinir o certificado usado para autenticação:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**O ponto de extremidade do servidor tem um status de integridade de "nenhuma atividade" e o estado do servidor na folha servidores registrados é "online"**  

Um status de integridade do ponto de extremidade do servidor de "nenhuma atividade" significa que o ponto de extremidade do servidor não registrou a atividade de sincronização nas últimas duas horas.

Para verificar a atividade de sincronização atual em um servidor, consulte [como fazer monitorar o progresso de uma sessão de sincronização atual?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

Um ponto de extremidade do servidor pode não registrar a atividade de sincronização por várias horas devido a um bug ou recursos do sistema insuficientes. Verifique se a versão mais recente do [agente](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) de sincronização de arquivos do Azure está instalada. Se o problema persistir, abra uma solicitação de suporte.

> [!Note]  
> Se o estado do servidor na folha servidores registrados for "aparecer offline", execute as etapas documentadas no [ponto de extremidade do servidor tem um status de integridade "sem atividade" ou "pendente" e o estado do servidor na folha servidores registrados é "aparece offline"](#server-endpoint-noactivity) .

## <a name="sync"></a>Sincronizar
<a id="afs-change-detection"></a>**Se eu criei um arquivo diretamente no meu compartilhamento de arquivos do Azure sobre SMB ou por meio do portal, quanto tempo leva para que o arquivo seja sincronizado com os servidores no grupo de sincronização?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**A integridade do ponto de extremidade do servidor está em um estado pendente por várias horas**  
Esse problema será esperado se você criar um ponto de extremidade de nuvem e usar um compartilhamento de arquivos do Azure que contém dados. O trabalho de enumeração de alteração que procura alterações no compartilhamento de arquivos do Azure deve ser concluído antes que os arquivos possam ser sincronizados entre os pontos de extremidade de nuvem e de servidor. O tempo para concluir o trabalho depende do tamanho do namespace no compartilhamento de arquivos do Azure. A integridade do ponto de extremidade do servidor deve ser atualizada após a conclusão do trabalho de enumeração de alteração.

### <a id="broken-sync"></a>Como fazer monitorar a integridade da sincronização?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Em cada grupo de sincronização, você pode fazer uma busca detalhada em seus pontos de extremidade de servidor individuais para ver o status das últimas sessões de sincronização concluídas. Uma coluna de integridade verde e um valor de não sincronização de arquivos de 0 indicam que a sincronização está funcionando conforme o esperado. Se esse não for o caso, veja abaixo uma lista de erros comuns de sincronização e como lidar com arquivos que não estão sincronizando. 

![Uma captura de tela da portal do Azure](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Servidor](#tab/server)
Vá para os logs de telemetria do servidor, que podem ser encontrados na Visualizador de Eventos em `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`. O evento 9102 corresponde a uma sessão de sincronização concluída; para obter o status mais recente da sincronização, procure o evento mais recente com a ID 9102. SyncDirection informa se esta sessão foi um upload ou um download. Se o HResult for 0, a sessão de sincronização foi bem-sucedida. Um HResult diferente de zero significa que houve um erro durante a sincronização; Veja abaixo uma lista de erros comuns. Se o PerItemErrorCount for maior que 0, isso significa que alguns arquivos ou pastas não foram sincronizados corretamente. É possível ter um HResult de 0, mas um PerItemErrorCount maior que 0.

Abaixo está um exemplo de um upload bem-sucedido. Para fins de brevidade, apenas alguns dos valores contidos em cada evento 9102 são listados abaixo. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Por outro lado, um upload sem êxito pode ser assim:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Às vezes, as sessões de sincronização falham em geral ou têm um PerItemErrorCount diferente de zero, mas ainda encaminham o progresso, com a sincronização de alguns arquivos com êxito. Isso pode ser visto nos campos * aplicados (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes), que informam quanto da sessão é bem sucedido. Se você vir várias sessões de sincronização em uma linha com falha, mas tiver uma contagem cada vez maior aplicada, deverá dar tempo de sincronização para tentar novamente antes de abrir um tíquete de suporte.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Como fazer monitorar o progresso de uma sessão de sincronização atual?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Em seu grupo de sincronização, vá até o ponto de extremidade do servidor em questão e examine a seção atividade de sincronização para ver a contagem de arquivos carregados ou baixados na sessão de sincronização atual. Observe que esse status será atrasado em cerca de 5 minutos e, se a sessão de sincronização for pequena o suficiente para ser concluída dentro desse período, ela não poderá ser relatada no Portal. 

# <a name="servertabserver"></a>[Servidor](#tab/server)
Examine o evento 9302 mais recente no log de telemetria no servidor (na Visualizador de Eventos, acesse aplicativos e serviços Logs\Microsoft\FileSync\Agent\Telemetry). Esse evento indica o estado da sessão de sincronização atual. TotalItemCount denota quantos arquivos devem ser sincronizados, AppliedItemCount o número de arquivos que foram sincronizados até o momento e PerItemErrorCount o número de arquivos que estão falhando na sincronização (consulte abaixo para saber como lidar com isso).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Como fazer saber se meus servidores estão sincronizados entre si?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Para cada servidor em um determinado grupo de sincronização, certifique-se de que:
- Os carimbos de data/hora da última tentativa de sincronização para upload e download são recentes.
- O status é verde para upload e download.
- O campo atividade de sincronização mostra muito poucos ou nenhum arquivo restante para sincronização.
- O campo arquivos não sincronizados é 0 para upload e download.

# <a name="servertabserver"></a>[Servidor](#tab/server)
Examine as sessões de sincronização concluídas, que são marcadas por eventos 9102 no log de eventos de telemetria para cada servidor (na Visualizador de Eventos, acesse `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. Em qualquer servidor específico, você deseja ter certeza de que as sessões de upload e download mais recentes foram concluídas com êxito. Para fazer isso, verifique se HResult e PerItemErrorCount são 0 para upload e download (o campo SyncDirection indica se uma sessão específica é uma sessão de upload ou de download). Observe que, se você não vir uma sessão de sincronização concluída recentemente, é provável que uma sessão de sincronização esteja em andamento, o que será esperado se você acabou de adicionar ou modificar uma grande quantidade de dados.
2. Quando um servidor estiver totalmente atualizado com a nuvem e não tiver nenhuma alteração para sincronizar em nenhuma das direções, você verá sessões de sincronização vazias. Eles são indicados por upload e download de eventos nos quais todos os campos Sync * (SyncFileCount, SyncDirCount, SyncTombstoneCount e SyncSizeBytes) são zero, o que significa que não houve nada para sincronizar. Observe que essas sessões de sincronização vazias podem não ocorrer em servidores de alta rotatividade, pois sempre há algo novo a ser sincronizado. Se não houver nenhuma atividade de sincronização, elas deverão ocorrer a cada 30 minutos. 
3. Se todos os servidores estiverem atualizados com a nuvem, o que significa que suas sessões de upload e download recentes são sessões de sincronização vazias, você pode dizer com certeza razoável que o sistema como um todo está em sincronia. 
    
Observe que, se você fez alterações diretamente no compartilhamento de arquivos do Azure, Sincronização de Arquivos do Azure não detectará essa alteração até que a enumeração de alteração seja executada, o que acontece uma vez a cada 24 horas. É possível que um servidor diga que ele está atualizado com a nuvem quando está na verdade faltam alterações recentes feitas diretamente no compartilhamento de arquivos do Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Como fazer ver se há arquivos ou pastas específicas que não estão sincronizando?
Se seu PerItemErrorCount no servidor ou os arquivos que não estão sincronizando a contagem no portal forem maiores que 0 para qualquer sessão de sincronização específica, isso significará que alguns itens não serão sincronizados. Arquivos e pastas podem ter características que os impeçam de sincronizar. Essas características podem ser persistentes e exigem ação explícita para retomar a sincronização, por exemplo removendo caracteres sem suporte do nome do arquivo ou da pasta. Eles também podem ser transitórios, o que significa que o arquivo ou a pasta retomará a sincronização automaticamente; por exemplo, arquivos com identificadores abertos continuarão a sincronização automaticamente quando o arquivo for fechado. Quando o mecanismo de Sincronização de Arquivos do Azure detecta esse problema, é produzido um log de erros que pode ser analisado para listar os itens que não estão atualmente sincronizando corretamente.

Para ver esses erros, execute o script do PowerShell **FileSyncErrorsReport. ps1** (localizado no diretório de instalação do agente do agente de sincronização de arquivos do Azure) para identificar os arquivos que não foram sincronizados devido a identificadores abertos, caracteres sem suporte ou outros problemas. O campo dopath informa o local do arquivo em relação ao diretório de sincronização raiz. Consulte a lista de erros comuns de sincronização abaixo para obter as etapas de correção.

> [!Note]  
> Se o script FileSyncErrorsReport.ps1 devoluções "Não foram encontrados erros de ficheiro" ou não enumera erros por item para o grupo de sincronização, a causa é:
>
>- Causa 1: A última sessão de sincronização concluída não teve erros por item. O portal deve ser atualizado em breve para mostrar 0 Ficheiros Não Sincronizados. 
>   - Verifique o [ID de evento 9102](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) no registo do evento Telemettry para confirmar que o PerItemErrorCount é 0. 
>
>- Causa 2: O registo do evento ItemResults no servidor embrulhado devido a demasiados erros por item e o registo do evento já não contém erros para este grupo de sincronização.
>   - Para evitar este problema, aumente o tamanho do registo do evento ItemResults. O registo do evento ItemResults pode ser encontrado em "Applications and Services Logs\Microsoft\FileSync\Agent" no Visualizador de Eventos. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Solução de problemas por erros de sincronização de arquivo/diretório
**Erros de sincronização de log por item de itens de resultados**  

| HRESULT | HRESULT (Decimal) | Cadeia do erro | Problema | Remediação |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | O arquivo em camadas no servidor não está acessível. Este problema ocorrerá se o ficheiro em camadas não tiver sido recuperado antes de eliminar um ponto final do servidor. | Para resolver esse problema, consulte [arquivos em camadas não podem ser acessados no servidor após a exclusão de um ponto de extremidade do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | A alteração de arquivo ou diretório ainda não pode ser sincronizada porque uma pasta dependente ainda não foi sincronizada. Este item será sincronizado depois que as alterações dependentes forem sincronizadas. | Nenhuma ação necessária. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | A alteração de arquivo ou diretório ainda não pode ser sincronizada porque uma pasta dependente ainda não foi sincronizada e a sessão de sincronização falhou. Este item será sincronizado depois que as alterações dependentes forem sincronizadas. | Nenhuma ação necessária. Se o erro persistir, investigue a falha da sessão de sincronização. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | O nome do arquivo ou diretório é inválido. | Renomeie o arquivo ou diretório em questão. Consulte [manipulando caracteres sem suporte](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | O nome do arquivo ou diretório é inválido. | Renomeie o arquivo ou diretório em questão. Consulte [manipulando caracteres sem suporte](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | O arquivo não pode ser sincronizado porque está em uso. O ficheiro será sincronizado quando já não estiver em utilização. | Nenhuma ação necessária. Sincronização de Arquivos do Azure cria um instantâneo VSS temporário uma vez por dia no servidor para sincronizar arquivos que têm identificadores abertos. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | O arquivo foi alterado, mas a alteração ainda não foi detectada pela sincronização. A sincronização será recuperada depois que essa alteração for detectada. | Nenhuma ação necessária. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | O arquivo foi excluído e a sincronização não está ciente da alteração. | Nenhuma ação necessária. A sincronização irá parar de registrar esse erro quando a detecção de alteração detectar que o arquivo foi excluído. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | A exclusão de um arquivo ou diretório não pode ser sincronizada porque o item já foi excluído no destino e a sincronização não está ciente da alteração. | Nenhuma ação necessária. A sincronização interromperá o registro desse erro quando a detecção de alteração for executada no destino e a sincronização detectar que o item foi excluído. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | O arquivo ou diretório foi ignorado, mas será sincronizado durante a próxima sessão de sincronização. Se esse erro for relatado durante o download do item, o nome do arquivo ou diretório é mais do que provavelmente inválido. | Nenhuma ação será necessária se esse erro for relatado ao carregar o arquivo. Se o erro for relatado durante o download do arquivo, renomeie o arquivo ou diretório em questão. Consulte [manipulando caracteres sem suporte](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | A criação de um arquivo ou diretório não pode ser sincronizada porque o item já existe no destino e a sincronização não está ciente da alteração. | Nenhuma ação necessária. A sincronização irá parar de registrar esse erro quando a detecção de alteração for executada no destino e a sincronização estiver ciente desse novo item. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Não é possível sincronizar o ficheiro porque o limite de partilha de ficheiros do Azure foi atingido. | Para resolver esse problema, consulte [a seção limite de armazenamento do compartilhamento de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) no guia de solução de problemas. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | O arquivo é criptografado por uma solução sem suporte (como o EFS do NTFS). | Descriptografe o arquivo e use uma solução de criptografia com suporte. Para obter uma lista de soluções suportadas, veja a secção [Soluções de encriptação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions) no guia de planeamento. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | O arquivo está localizado em uma pasta de replicação somente leitura do DFS-R. | O arquivo está localizado em uma pasta de replicação somente leitura do DFS-R. O Azure File Sync não suporta pontos finais do servidor nas pastas de replicação só de leitura do DFS-R. Consulte o [Guia de planejamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) para obter mais informações. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | O arquivo tem um estado de exclusão pendente. | Nenhuma ação necessária. O arquivo será excluído quando todos os identificadores de arquivos abertos forem fechados. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | O arquivo não pode ser sincronizado porque as configurações de firewall e rede virtual na conta de armazenamento estão habilitadas e o servidor não tem acesso à conta de armazenamento. | Adicione o endereço IP do servidor ou a rede virtual seguindo as etapas documentadas na seção [Configurar o firewall e as configurações de rede virtual](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) no guia de implantação. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | O arquivo não pode ser sincronizado porque o tamanho do descritor de segurança excede o limite de KiB de 64. | Para resolver este problema, remova as entradas de controlo de acesso (ACE) no ficheiro para reduzir o tamanho do descritor de segurança. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | O arquivo não pode ser sincronizado devido a um erro inesperado. | Se o erro persistir por vários dias, abra um caso de suporte. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | O arquivo não pode ser sincronizado porque está em uso. O ficheiro será sincronizado quando já não estiver em utilização. | Nenhuma ação necessária. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | O arquivo foi alterado durante a sincronização, portanto, ele precisa ser sincronizado novamente. | Nenhuma ação necessária. |
| 0x80070017 | -2147024873 | ERROR_CRC | O arquivo não pode ser sincronizado devido a um erro de CRC. Esse erro poderá ocorrer se um arquivo em camadas não tiver sido rechamado antes da exclusão de um ponto de extremidade do servidor ou se o arquivo estiver corrompido. | Para resolver esse problema, consulte [arquivos em camadas não podem ser acessados no servidor após a exclusão de um ponto de extremidade do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) para remover arquivos em camadas que estão órfãos. Se o erro continuar ocorrendo após a remoção de arquivos em camadas órfãs, execute [chkdsk](https://docs.microsoft.com/windows-server/administration/windows-commands/chkdsk) no volume. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | O arquivo não pode ser sincronizado porque o número máximo de arquivos de conflito foi atingido. O Sincronização de Arquivos do Azure dá suporte a arquivos de conflito 100 por arquivo. Para saber mais sobre conflitos de arquivo, consulte Sincronização de Arquivos do Azure [perguntas frequentes](https://docs.microsoft.com/azure/storage/files/storage-files-faq#afs-conflict-resolution). | Para resolver esse problema, reduza o número de arquivos de conflito. O arquivo será sincronizado assim que o número de arquivos de conflito for menor que 100. |

#### <a name="handling-unsupported-characters"></a>Manipulando caracteres sem suporte
Se o script do PowerShell **FileSyncErrorsReport. ps1** mostrar falhas devido a caracteres sem suporte (código de erro 0x8007007b ou 0x80c80255), você deverá remover ou renomear os caracteres com falha dos respectivos nomes de arquivo. O PowerShell provavelmente imprimirá esses caracteres como pontos de interrogação ou retângulos vazios, já que a maioria desses caracteres não tem nenhuma codificação Visual padrão. A [ferramenta de avaliação](storage-sync-files-planning.md#evaluation-cmdlet) pode ser usada para identificar caracteres que não têm suporte.

A tabela a seguir contém todos os caracteres Unicode Sincronização de Arquivos do Azure ainda não tem suporte.

| Conjunto de carateres | Contagem de caracteres |
|---------------|-----------------|
| <ul><li>0x0000009D (comando do sistema operacional OSC)</li><li>0x00000090 (cadeia de controle de dispositivo DCS)</li><li>0x0000008F (SS3 único Shift 3)</li><li>0x00000081 (predefinição de octeto alta)</li><li>0x0000007F (del Delete)</li><li>0x0000008D (alimentação de linha inversa ri)</li></ul> | 6 |
| 0x0000FDD0-0x0000FDEF (formulário de apresentação árabe-a) | 32 |
| 0x0000FFF0-0x0000FFFF (especial) | 16 |
| <ul><li>0x0001FFFE-0x0001FFFF = 2 (não caractere)</li><li>0x0002FFFE-0x0002FFFF = 2 (não caractere)</li><li>0x0003FFFE-0x0003FFFF = 2 (não caractere)</li><li>0x0004FFFE-0x0004FFFF = 2 (não caractere)</li><li>0x0005FFFE-0x0005FFFF = 2 (não caractere)</li><li>0x0006FFFE-0x0006FFFF = 2 (não caractere)</li><li>0x0007FFFE-0x0007FFFF = 2 (não caractere)</li><li>0x0008FFFE-0x0008FFFF = 2 (não caractere)</li><li>0x0009FFFE-0x0009FFFF = 2 (não caractere)</li><li>0x000AFFFE-0x000AFFFF = 2 (não caractere)</li><li>0x000BFFFE-0x000BFFFF = 2 (não caractere)</li><li>0x000CFFFE-0x000CFFFF = 2 (não caractere)</li><li>0x000DFFFE-0x000DFFFF = 2 (não caractere)</li><li>0x000EFFFE-0x000EFFFF = 2 (indefinido)</li><li>0x000FFFFE-0x000FFFFF = 2 (área de uso particular suplementar)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Erros comuns de sincronização
<a id="-2147023673"></a>**A sessão de sincronização foi cancelada.**  

| | |
|-|-|
| **RESULTADO** | 0x800704c7 |
| **HRESULT (Decimal)** | -2147023673 | 
| **Cadeia de caracteres de erro** | ERROR_CANCELLED |
| **Correção necessária** | Não |

As sessões de sincronização podem falhar por vários motivos, incluindo o servidor que está sendo reiniciado ou atualizado, instantâneos do VSS, etc. Embora esse erro pareça que ele requer acompanhamento, é seguro ignorar esse erro, a menos que ele persista em um período de várias horas.

<a id="-2147012889"></a>**Não foi possível estabelecer uma conexão com o serviço.**    

| | |
|-|-|
| **RESULTADO** | 0x80072ee7 |
| **HRESULT (Decimal)** | -2147012889 | 
| **Cadeia de caracteres de erro** | WININET_E_NAME_NOT_RESOLVED |
| **Correção necessária** | Sim |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**A solicitação do usuário foi limitada pelo serviço.**  

| | |
|-|-|
| **RESULTADO** | 0x80c8004c |
| **HRESULT (Decimal)** | -2134376372 |
| **Cadeia de caracteres de erro** | ECS_E_USER_REQUEST_THROTTLED |
| **Correção necessária** | Não |

Nenhuma ação é necessária; o servidor tentará novamente. Se este erro persistir durante várias horas, crie um pedido de suporte.

<a id="-2134364043"></a>**A sincronização será bloqueada até que a detecção de alteração seja concluída após a restauração**  

| | |
|-|-|
| **RESULTADO** | 0x80c83075 |
| **HRESULT (Decimal)** | -2134364043 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Correção necessária** | Não |

Não é necessário realizar qualquer ação. Quando um compartilhamento de arquivo ou arquivo (ponto de extremidade de nuvem) é restaurado usando o backup do Azure, a sincronização é bloqueada até que a detecção de alteração seja concluída no compartilhamento de arquivos do Azure. A deteção de alterações é executada imediatamente após o restauro ser concluído e a duração é baseada no número de ficheiros na partilha de ficheiros.

<a id="-2147216747"></a>**Falha na sincronização porque o banco de dados de sincronização foi descarregado.**  

| | |
|-|-|
| **RESULTADO** | 0x80041295 |
| **HRESULT (Decimal)** | -2147216747 |
| **Cadeia de caracteres de erro** | SYNC_E_METADATA_INVALID_OPERATION |
| **Correção necessária** | Não |

Este erro normalmente ocorre quando uma aplicação de cópia de segurança cria um instantâneo do VSS e a base de dados de sincronização é descarregada. Se este erro persistir durante várias horas, crie um pedido de suporte.

<a id="-2134364065"></a>**A sincronização não pode acessar o compartilhamento de arquivos do Azure especificado no ponto de extremidade de nuvem.**  

| | |
|-|-|
| **RESULTADO** | 0x80c8305f |
| **HRESULT (Decimal)** | -2134364065 |
| **Cadeia de caracteres de erro** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Correção necessária** | Sim |

Este erro ocorre porque o agente do Azure File Sync não consegue aceder à partilha de ficheiros do Azure, o que pode dever-se à partilha de ficheiros do Azure ou ao anfitrião da conta de armazenamento já não existir. Pode resolver este erro ao realizar os passos seguintes:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se o compartilhamento de arquivos do Azure existe.](#troubleshoot-azure-file-share)
3. [Verifique se Sincronização de Arquivos do Azure tem acesso à conta de armazenamento.](#troubleshoot-rbac)
4. [Verificar se as definições da rede virtual e da firewall na conta de armazenamento estão configuradas corretamente (se ativadas)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**Falha na sincronização porque a solicitação não está autorizada a executar esta operação.**  

| | |
|-|-|
| **RESULTADO** | 0x80c86044 |
| **HRESULT (Decimal)** | -2134351804 |
| **Cadeia de caracteres de erro** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Correção necessária** | Sim |

Esse erro ocorre porque o agente de Sincronização de Arquivos do Azure não está autorizado a acessar o compartilhamento de arquivos do Azure. Pode resolver este erro ao realizar os passos seguintes:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se o compartilhamento de arquivos do Azure existe.](#troubleshoot-azure-file-share)
3. [Verificar se as definições da rede virtual e da firewall na conta de armazenamento estão configuradas corretamente (se ativadas)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Verifique se Sincronização de Arquivos do Azure tem acesso à conta de armazenamento.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**O nome da conta de armazenamento usado não pôde ser resolvido.**  

| | |
|-|-|
| **RESULTADO** | 0x80C83060 |
| **HRESULT (Decimal)** | -2134364064 |
| **Cadeia de caracteres de erro** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Correção necessária** | Sim |

1. Verifique se você pode resolver o nome DNS de armazenamento do servidor.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
3. [Verificar se as definições da rede virtual e da firewall na conta de armazenamento estão configuradas corretamente (se ativadas)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Ocorreu um erro desconhecido ao acessar a conta de armazenamento.**  

| | |
|-|-|
| **RESULTADO** | 0x80c8308a |
| **HRESULT (Decimal)** | -2134364022 |
| **Cadeia de caracteres de erro** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Correção necessária** | Sim |

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verificar se as definições da rede virtual e da firewall na conta de armazenamento estão configuradas corretamente (se ativadas)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**Falha na sincronização devido à conta de armazenamento bloqueada.**  

| | |
|-|-|
| **RESULTADO** | 0x80c83092 |
| **HRESULT (Decimal)** | -2134364014 |
| **Cadeia de caracteres de erro** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Correção necessária** | Sim |

Esse erro ocorre porque a conta de armazenamento tem um bloqueio de [recurso](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)somente leitura. Para resolver esse problema, remova o bloqueio de recurso somente leitura na conta de armazenamento. 

<a id="-1906441138"></a>**Falha na sincronização devido a um problema com o banco de dados de sincronização.**  

| | |
|-|-|
| **RESULTADO** | 0x8e5e044e |
| **HRESULT (Decimal)** | -1906441138 |
| **Cadeia de caracteres de erro** | JET_errWriteConflict |
| **Correção necessária** | Sim |

Esse erro ocorre quando há um problema com o banco de dados interno usado pelo Sincronização de Arquivos do Azure. Quando esse problema ocorrer, crie uma solicitação de suporte e entraremos em contato para ajudá-lo a resolver esse problema.

<a id="-2134364053"></a>**Não há suporte para a versão do agente de Sincronização de Arquivos do Azure instalada no servidor.**  

| | |
|-|-|
| **RESULTADO** | 0x80C8306B |
| **HRESULT (Decimal)** | -2134364053 |
| **Cadeia de caracteres de erro** | ECS_E_AGENT_VERSION_BLOCKED |
| **Correção necessária** | Sim |

Este erro ocorrerá se a versão do Azure File Sync instalada no servidor não for suportada. Para resolver esse problema, [atualize]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) para uma [versão de agente com suporte]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Você atingiu o limite de armazenamento do compartilhamento de arquivos do Azure.**  

| | |
|-|-|
| **RESULTADO** | 0x80c8603e |
| **HRESULT (Decimal)** | -2134351810 |
| **Cadeia de caracteres de erro** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Correção necessária** | Sim |

Este erro ocorre quando o limite de armazenamento da partilha de ficheiros do Azure é atingido, o que poderá acontecer se uma quota for aplicada a uma partilha de ficheiros do Azure ou se a utilização exceder os limites de uma partilha de ficheiros do Azure. Para obter mais informações, consulte os [limites atuais para um compartilhamento de arquivos do Azure](storage-files-scale-targets.md).

1. Navegue até o grupo de sincronização no serviço de sincronização de armazenamento.
2. Selecione o ponto de extremidade de nuvem dentro do grupo de sincronização.
3. Observe o nome do compartilhamento de arquivos do Azure no painel aberto.
4. Selecione a conta de armazenamento ligada. Se esse link falhar, a conta de armazenamento referenciada foi removida.

    ![Uma captura de tela mostrando o painel de detalhes do ponto de extremidade da nuvem com um link para a conta de armazenamento.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Selecione **arquivos** para exibir a lista de compartilhamentos de arquivos.
6. Clique nos três pontos no final da linha do compartilhamento de arquivos do Azure referenciado pelo ponto de extremidade de nuvem.
7. Verifique se a **Utilização** está abaixo da**Quota**. Observação: a menos que uma cota alternativa tenha sido especificada, a cota corresponderá ao [tamanho máximo do compartilhamento de arquivos do Azure](storage-files-scale-targets.md).

    ![Uma captura de tela das propriedades de compartilhamento de arquivos do Azure.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Se a partilha de ficheiros estiver cheia e não tiver sido definida uma quota, uma forma possível de resolver este problema será tornar cada subpasta do ponto final do servidor atual no seu próprio ponto final do servidor nos seus próprios grupos de sincronização separados. Desta forma, cada subpasta será sincronizada com as partilhas de ficheiros do Azure individuais.

<a id="-2134351824"></a>**Não é possível encontrar o compartilhamento de arquivos do Azure.**  

| | |
|-|-|
| **RESULTADO** | 0x80c86030 |
| **HRESULT (Decimal)** | -2134351824 |
| **Cadeia de caracteres de erro** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Correção necessária** | Sim |

Este erro ocorre quando a partilha de ficheiros do Azure não está acessível. Para resolver os problemas:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se o compartilhamento de arquivos do Azure existe.](#troubleshoot-azure-file-share)

Se o compartilhamento de arquivos do Azure foi excluído, você precisará criar um novo compartilhamento de arquivos e recriar o grupo de sincronização. 

<a id="-2134364042"></a>**A sincronização está em pausa enquanto esta assinatura do Azure está suspensa.**  

| | |
|-|-|
| **RESULTADO** | 0x80C83076 |
| **HRESULT (Decimal)** | -2134364042 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Correção necessária** | Sim |

Este erro ocorre quando a subscrição do Azure é suspensa. A sincronização será reativada quando a subscrição do Azure for restaurada. Veja [por que minha assinatura do Azure está desabilitada e como reativá-la?](../../cost-management-billing/manage/subscription-disabled.md) para obter mais informações.

<a id="-2134364052"></a>**A conta de armazenamento tem um firewall ou redes virtuais configuradas.**  

| | |
|-|-|
| **RESULTADO** | 0x80c8306c |
| **HRESULT (Decimal)** | -2134364052 |
| **Cadeia de caracteres de erro** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Correção necessária** | Sim |

Este erro ocorre quando a partilha de ficheiros do Azure está inacessível devido a uma firewall da conta de armazenamento ou devido à conta de armazenamento pertencer a uma rede virtual. Verifique se as configurações de firewall e rede virtual na conta de armazenamento estão configuradas corretamente. Para obter mais informações, consulte [Configurar o firewall e as configurações de rede virtual](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**Falha na sincronização devido a um problema com o banco de dados de sincronização.**  

| | |
|-|-|
| **RESULTADO** | 0x80c80219 |
| **HRESULT (Decimal)** | -2134375911 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Correção necessária** | Não |

Este erro resolve-se normalmente de forma autónoma e poderá ocorrer se existir:

* Um alto número de alterações de arquivo nos servidores no grupo de sincronização.
* Um grande número de erros em arquivos e diretórios individuais.

Se esse erro persistir por mais de algumas horas, crie uma solicitação de suporte e entraremos em contato para ajudá-lo a resolver esse problema.

<a id="-2146762487"></a>**O servidor não pôde estabelecer uma conexão segura. O serviço de nuvem recebeu um certificado inesperado.**  

| | |
|-|-|
| **RESULTADO** | 0x800b0109 |
| **HRESULT (Decimal)** | -2146762487 |
| **Cadeia de caracteres de erro** | CERT_E_UNTRUSTEDROOT |
| **Correção necessária** | Sim |

Este erro poderá ocorrer se a sua organização estiver a utilizar um proxy de terminação SSL ou se uma entidade maliciosa estiver a intercetar o tráfego entre o servidor e o serviço Azure File Sync. Se tiver a certeza de que este comportamento é esperado (devido ao facto de a sua organização estar a utilizar um proxy de terminação SSL), poderá ignorar a verificação de certificados com uma substituição de registo.

1. Crie o valor do registro SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Reinicie o serviço de sincronização no servidor registrado.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ao definir este valor de registo, o agente do Azure File Sync aceitará qualquer certificado SSL fidedigno ao transferir dados entre o servidor e o serviço cloud.

<a id="-2147012894"></a>**Não foi possível estabelecer uma conexão com o serviço.**  

| | |
|-|-|
| **RESULTADO** | 0x80072ee2 |
| **HRESULT (Decimal)** | -2147012894 |
| **Cadeia de caracteres de erro** | WININET_E_TIMEOUT |
| **Correção necessária** | Sim |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Falha na sincronização devido a um problema com a autenticação.**  

| | |
|-|-|
| **RESULTADO** | 0x80c80300 |
| **HRESULT (Decimal)** | -2134375680 |
| **Cadeia de caracteres de erro** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Correção necessária** | Sim |

Este erro ocorre normalmente porque a hora do servidor está incorreta. Se o servidor estiver em execução em uma máquina virtual, verifique se a hora no host está correta.

<a id="-2134364040"></a>**Falha na sincronização devido à expiração do certificado.**  

| | |
|-|-|
| **RESULTADO** | 0x80c83078 |
| **HRESULT (Decimal)** | -2134364040 |
| **Cadeia de caracteres de erro** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Correção necessária** | Sim |

Este erro ocorre porque o certificado utilizado para a autenticação expirou.

Para confirmar se o certificado expirou, execute os seguintes passos:  
1. Abra o snap-in do MMC de certificados, selecione conta de computador e navegue até certificados (computador local) \Personal\Certificates.
2. Verifique se o certificado de autenticação do cliente expirou.

Se o certificado de autenticação de cliente tiver expirado, efetue os seguintes passos para resolver o problema:

1. Verifique se Sincronização de Arquivos do Azure versão do agente 4.0.1.0 ou posterior está instalado.
2. Execute o seguinte comando do PowerShell no servidor:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Falha na sincronização devido ao certificado de autenticação não encontrado.**  

| | |
|-|-|
| **RESULTADO** | 0x80c80228 |
| **HRESULT (Decimal)** | -2134375896 |
| **Cadeia de caracteres de erro** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Correção necessária** | Sim |

Este erro ocorre porque o certificado utilizado para a autenticação não foi localizado.

Para resolver este problema, realize os passos seguintes:

1. Verifique se Sincronização de Arquivos do Azure versão do agente 4.0.1.0 ou posterior está instalado.
2. Execute o seguinte comando do PowerShell no servidor:

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Falha na sincronização devido à identidade de autenticação não encontrada.**  

| | |
|-|-|
| **RESULTADO** | 0x80c83079 |
| **HRESULT (Decimal)** | -2134364039 |
| **Cadeia de caracteres de erro** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Correção necessária** | Sim |

Este erro ocorre porque a eliminação do ponto final do servidor falhou e o ponto final está agora num estado parcialmente eliminado. Para resolver este problema, repita a eliminação do ponto final do servidor.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**O volume em que o ponto de extremidade do servidor está localizado está com pouco espaço em disco.**  

| | |
|-|-|
| **RESULTADO** | 0x8e5e0211 |
| **HRESULT (Decimal)** | -1906441711 |
| **Cadeia de caracteres de erro** | JET_errLogDiskFull |
| **Correção necessária** | Sim |
| | |
| **RESULTADO** | 0x80c8031a |
| **HRESULT (Decimal)** | -2134375654 |
| **Cadeia de caracteres de erro** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Correção necessária** | Sim |

Este erro ocorre porque o volume ficou cheio. Este erro geralmente ocorre porque os ficheiros fora do ponto final do servidor estão a utilizar espaço no volume. Libere espaço no volume adicionando pontos de extremidade do servidor adicionais, movendo os arquivos para um volume diferente ou aumentando o tamanho do volume no qual o ponto final do servidor está.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**O serviço ainda não está pronto para sincronizar com este ponto de extremidade do servidor.**  

| | |
|-|-|
| **RESULTADO** | 0x80c8300f |
| **HRESULT (Decimal)** | -2134364145 |
| **Cadeia de caracteres de erro** | ECS_E_REPLICA_NOT_READY |
| **Correção necessária** | Não |

Esse erro ocorre porque o ponto de extremidade de nuvem foi criado com conteúdo já existente no compartilhamento de arquivos do Azure. Sincronização de Arquivos do Azure deve verificar o compartilhamento de arquivos do Azure para todo o conteúdo antes de permitir que o ponto de extremidade do servidor prossiga com sua sincronização inicial.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Falha na sincronização devido a problemas com muitos arquivos individuais.**  

| | |
|-|-|
| **RESULTADO** | 0x80c8023b |
| **HRESULT (Decimal)** | -2134375877 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Correção necessária** | Sim |
| | |
| **RESULTADO** | 0x80c8021c |
| **HRESULT (Decimal)** | -2134375908 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Correção necessária** | Sim |
| | |
| **RESULTADO** | 0x80c80253 |
| **HRESULT (Decimal)** | -2134375853 |
| **Cadeia de caracteres de erro** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Correção necessária** | Sim |

Nos casos em que há muitos erros por sincronização de arquivo, as sessões de sincronização podem começar a falhar. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> Sincronização de Arquivos do Azure cria um instantâneo VSS temporário uma vez por dia no servidor para sincronizar arquivos que têm identificadores abertos.

<a id="-2134376423"></a>**Falha na sincronização devido a um problema com o caminho do ponto de extremidade do servidor.**  

| | |
|-|-|
| **RESULTADO** | 0x80c80019 |
| **HRESULT (Decimal)** | -2134376423 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_INVALID_PATH |
| **Correção necessária** | Sim |

Certifique-se de que o caminho exista, esteja em um volume NTFS local e não seja um ponto de nova análise ou uma extremidade de servidor existente.

<a id="-2134375817"></a>**Falha na sincronização porque a versão do driver de filtro não é compatível com a versão do agente**  

| | |
|-|-|
| **RESULTADO** | 0x80C80277 |
| **HRESULT (Decimal)** | -2134375817 |
| **Cadeia de caracteres de erro** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Correção necessária** | Sim |

Este erro ocorre porque a versão do controlador de filtro de Arrumo na Cloud (StorageSync.sys) carregada não é compatível com o serviço do Agente de Sincronização de Armazenamento (FileSyncSvc). Se o agente do Azure File Sync tiver sido atualizado, reinicie o servidor para concluir a instalação. Se o erro persistir, desinstale o agente, reinicie o servidor e reinstale o agente do Azure File Sync.

<a id="-2134376373"></a>**O serviço não está disponível no momento.**  

| | |
|-|-|
| **RESULTADO** | 0x80c8004b |
| **HRESULT (Decimal)** | -2134376373 |
| **Cadeia de caracteres de erro** | ECS_E_SERVICE_UNAVAILABLE |
| **Correção necessária** | Não |

Este erro ocorre porque o serviço Azure File Sync está indisponível. Este erro será resolvido automaticamente quando o serviço do Azure File Sync estiver novamente disponível.

<a id="-2146233088"></a>**Falha na sincronização devido a uma exceção.**  

| | |
|-|-|
| **RESULTADO** | 0x80131500 |
| **HRESULT (Decimal)** | -2146233088 |
| **Cadeia de caracteres de erro** | COR_E_EXCEPTION |
| **Correção necessária** | Não |

Este erro ocorre porque a sincronização falhou devido a uma exceção. Se o erro persistir por várias horas, crie uma solicitação de suporte.

<a id="-2134364045"></a>**Falha na sincronização porque a conta de armazenamento fez failover para outra região.**  

| | |
|-|-|
| **RESULTADO** | 0x80c83073 |
| **HRESULT (Decimal)** | -2134364045 |
| **Cadeia de caracteres de erro** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Correção necessária** | Sim |

Este erro ocorreu porque a conta de armazenamento efetuou uma ativação pós-falha para outra região. O Azure File Sync não suporta a funcionalidade de ativação pós-falha da conta de armazenamento. As contas de armazenamento que contêm partilhas de ficheiros do Azure e estão a ser utilizadas como pontos finais da cloud no Azure File Sync não devem efetuar a ativação pós-falha. Se a fizer, fará com que a sincronização deixe de funcionar e poderá também causar perdas de dados inesperadas em caso de ficheiros com novo escalão. Para resolver este problema, mova a conta de armazenamento para a região primária.

<a id="-2134375922"></a>**Falha na sincronização devido a um problema transitório com o banco de dados de sincronização.**  

| | |
|-|-|
| **RESULTADO** | 0x80c8020e |
| **HRESULT (Decimal)** | -2134375922 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Correção necessária** | Não |

Esse erro ocorre devido a um problema interno na base de dados de sincronização. Este erro será resolvido automaticamente quando a tentar executar novamente a sincronização. Se esse erro continuar por um período de tempo excedido, crie uma solicitação de suporte e entraremos em contato para ajudá-lo a resolver esse problema.

<a id="-2134364024"></a>**Falha na sincronização devido à alteração no locatário Azure Active Directory**  

| | |
|-|-|
| **RESULTADO** | 0x80c83088 |
| **HRESULT (Decimal)** | -2134364024 | 
| **Cadeia de caracteres de erro** | ECS_E_INVALID_AAD_TENANT |
| **Correção necessária** | Sim |

Este erro ocorre porque o Azure File Sync atualmente não suporta mover a subscrição para um inquilino do Azure Active Directory diferente.
 
Para resolver este problema, realize uma das opções seguintes:

- **Opção 1 (recomendado)** : mover a assinatura de volta para o locatário de Azure Active Directory original
- **Opção 2**: exclua e recrie o grupo de sincronização atual. Se o arrumo na cloud tiver sido ativado no ponto final do servidor, elimine o grupo de sincronização e, em seguida, execute os passos documentados na [Secção Arrumo na Cloud]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) para remover os ficheiros em camadas órfãos antes de recriar os grupos de sincronização. 

<a id="-2134364010"></a>**Falha na sincronização devido a uma exceção de firewall e rede virtual não configurada**  

| | |
|-|-|
| **RESULTADO** | 0x80c83096 |
| **HRESULT (Decimal)** | -2134364010 | 
| **Cadeia de caracteres de erro** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Correção necessária** | Sim |

Esse erro ocorre se as configurações de firewall e rede virtual estiverem habilitadas na conta de armazenamento e a exceção "permitir que os serviços confiáveis da Microsoft acessem esta conta de armazenamento" não estiver marcada. Para resolver este problema, siga os passos documentados na secção [Configurar as definições da firewall e da rede virtual](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) no guia de implementação.

<a id="-2147024891"></a>**Falha na sincronização porque as permissões na pasta informações de volume do sistema estão incorretas.**  

| | |
|-|-|
| **RESULTADO** | 0x80070005 |
| **HRESULT (Decimal)** | -2147024891 |
| **Cadeia de caracteres de erro** | ERROR_ACCESS_DENIED |
| **Correção necessária** | Sim |

Este erro poderá ocorrer se a conta NT AUTHORITY\SYSTEM não tiver permissões para a pasta Informações de Volume do Sistema no volume onde o ponto final do servidor está localizado. Nota, se os ficheiros individuais não estiverem a sincronizar com ERROR_ACCESS_DENIED, execute os passos documentados na secção de erros de sincronização de [ficheiro/diretório.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors)

Para resolver este problema, realize os passos seguintes:

1. Transfira a ferramenta [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec).
2. Execute o seguinte comando em um prompt de comando elevado para iniciar um prompt de comando usando a conta do sistema: **PsExec. exe-i-s-d cmd** 
3. Na linha de comandos em execução na conta de sistema, execute o seguinte comando para confirmar que a conta NT AUTHORITY\SYSTEM não tem acesso à pasta Informações de Volume de Sistema: **cacls “"letra da unidade:\system volume information” /T /C**
4. Se a conta NT AUTHORITY\SYSTEM não tiver acesso à pasta Informações de Volume do Sistema, execute o seguinte comando: **cacls “letra da unidade:\system volume information” /T /E /G “NT AUTHORITY\SYSTEM:F”**
    - Se o passo 4 falhar com acesso negado, execute o seguinte comando para assumir a propriedade da pasta Informações de Volume do Sistema e, em seguida, repita o passo 4: **takeown /A /R /F “letra da unidade:\System Volume Information”**

<a id="-2134375810"></a>**Falha na sincronização porque o compartilhamento de arquivos do Azure foi excluído e recriado.**  

| | |
|-|-|
| **RESULTADO** | 0x80c8027e |
| **HRESULT (Decimal)** | -2134375810 |
| **Cadeia de caracteres de erro** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Correção necessária** | Sim |

Este erro ocorre porque o Azure File Sync não suporta a eliminação e a recriação de uma partilha de ficheiros do Azure no mesmo grupo de sincronização. 

Para resolver este problema, elimine e recrie o grupo de sincronização. Para tal, realize os seguintes passos:

1. Exclua todos os pontos de extremidade do servidor no grupo de sincronização.
2. Exclua o ponto de extremidade de nuvem. 
3. Exclua o grupo de sincronização.
4. Se a camada de nuvem foi habilitada em um ponto de extremidade do servidor, exclua os arquivos em camadas órfãos no servidor executando as etapas documentadas nos [arquivos em camadas não estão acessíveis no servidor após a exclusão de uma seção de ponto de extremidade do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) .
5. Recrie o grupo de sincronização.

<a id="-2145844941"></a>**Falha na sincronização porque a solicitação HTTP foi redirecionada**  

| | |
|-|-|
| **RESULTADO** | 0x80190133 |
| **HRESULT (Decimal)** | -2145844941 |
| **Cadeia de caracteres de erro** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Correção necessária** | Sim |

Esse erro ocorre porque Sincronização de Arquivos do Azure não dá suporte ao redirecionamento de HTTP (código de status 3xx). Para resolver esse problema, desabilite o redirecionamento HTTP no seu servidor proxy ou dispositivo de rede.

<a id="-2134364027"></a>**Ocorreu um tempo limite durante A transferência de dados offline, mas ele ainda está em andamento.**  

| | |
|-|-|
| **RESULTADO** | 0x80c83085 |
| **HRESULT (Decimal)** | -2134364027 |
| **Cadeia de caracteres de erro** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Correção necessária** | Não |

Esse erro ocorre quando uma operação de ingestão de dados excede o tempo limite. Esse erro poderá ser ignorado se a sincronização estiver fazendo progressos (AppliedItemCount é maior que 0). Consulte [como fazer monitorar o progresso de uma sessão de sincronização atual?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

### <a name="common-troubleshooting-steps"></a>Etapas comuns de solução de problemas
<a id="troubleshoot-storage-account"></a>**Verifique se a conta de armazenamento existe.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Navegue até o grupo de sincronização no serviço de sincronização de armazenamento.
2. Selecione o ponto de extremidade de nuvem dentro do grupo de sincronização.
3. Observe o nome do compartilhamento de arquivos do Azure no painel aberto.
4. Selecione a conta de armazenamento ligada. Se esse link falhar, a conta de armazenamento referenciada foi removida.
    ![Uma imagem mostrando o painel de detalhes do ponto final da nuvem com um link para a conta de armazenamento.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Verifique se o compartilhamento de arquivos do Azure existe.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Clique em **visão geral** no sumário à esquerda para retornar à página principal da conta de armazenamento.
2. Selecione **arquivos** para exibir a lista de compartilhamentos de arquivos.
3. Verifique se o compartilhamento de arquivos referenciado pelo ponto de extremidade de nuvem aparece na lista de compartilhamentos de arquivos (você deve ter observado isso na etapa 1 acima).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Verifique se Sincronização de Arquivos do Azure tem acesso à conta de armazenamento.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Clique em **controle de acesso (iam)** no sumário à esquerda.
1. Clique na guia **atribuições de função** para a lista de usuários e aplicativos (*entidades de serviço*) que têm acesso à sua conta de armazenamento.
1. Verifique se o **serviço sincronização de arquivos híbrido** aparece na lista com a função de **acesso de dados e leitor** . 

    ![Uma captura de tela da entidade de serviço do serviço de Sincronização de Arquivos híbrida na guia controle de acesso da conta de armazenamento](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Se o **serviço de sincronização de arquivos híbrido** não aparecer na lista, execute as seguintes etapas:

    - Clique em **Adicionar**.
    - No campo **função** , selecione **leitor e acesso a dados**.
    - No campo **selecionar** , digite **serviço de sincronização de arquivos híbrido**, selecione a função e clique em **salvar**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Hybrid File Sync Service" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Como fazer impedir que os usuários criem arquivos que contenham caracteres sem suporte no servidor?
Você pode usar [triagens de arquivo do File Server Resource Manager (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) para bloquear arquivos com caracteres sem suporte em seus nomes de serem criados no servidor. Talvez seja necessário fazer isso usando o PowerShell, pois a maioria dos caracteres sem suporte não é imprimível e, portanto, você precisa converter suas representações hexadecimais como caracteres primeiro.

Primeiro, crie um grupo de arquivos FSRM usando o [cmdlet New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Este exemplo define o grupo para conter apenas dois caracteres sem suporte, mas você pode incluir quantos caracteres forem necessários em seu grupo de arquivos.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Depois de definir um grupo de arquivos FSRM, você pode criar uma tela de arquivo FSRM usando o cmdlet New-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Observe que as triagens de arquivo só devem ser usadas para bloquear a criação de caracteres sem suporte pelo Sincronização de Arquivos do Azure. Se as triagens de arquivo forem usadas em outros cenários, a sincronização tentará continuamente baixar os arquivos do compartilhamento de arquivos do Azure para o servidor e será bloqueado devido à tela de arquivos, resultando em alta saída de dados. 

## <a name="cloud-tiering"></a>Disposição em camadas na cloud 
Há dois caminhos para falhas na camada de nuvem:

- Os arquivos podem falhar na camada, o que significa que Sincronização de Arquivos do Azure tentativas sem êxito de hierarquizar um arquivo para arquivos do Azure.
- Os arquivos podem falhar ao se recuperar, o que significa que o Sincronização de Arquivos do Azure filtro do sistema de arquivos (StorageSync. sys) falha ao baixar dados quando um usuário tenta acessar um arquivo que foi colocado em camadas.

Há duas classes principais de falhas que podem ocorrer por meio de um dos caminhos de falha:

- Falhas de armazenamento em nuvem
    - *Problemas de disponibilidade do serviço de armazenamento transitório*. Para obter mais informações, consulte o [contrato de nível de serviço (SLA) para o armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Compartilhamento de arquivos do Azure inacessível*. Essa falha normalmente acontece quando você exclui o compartilhamento de arquivos do Azure quando ele ainda é um ponto de extremidade de nuvem em um grupo de sincronização.
    - *Conta de armazenamento inacessível*. Essa falha normalmente acontece quando você exclui a conta de armazenamento enquanto ela ainda tem um compartilhamento de arquivos do Azure que é um ponto de extremidade de nuvem em um grupo de sincronização. 
- Falhas do servidor 
  - *Sincronização de arquivos do Azure filtro do sistema de arquivos (StorageSync. sys) não está carregado*. Para responder às solicitações de camadas/recuperação, o filtro do sistema de arquivos Sincronização de Arquivos do Azure deve ser carregado. O filtro não está sendo carregado pode ocorrer por vários motivos, mas o motivo mais comum é que um administrador o descarregou manualmente. O filtro do sistema de arquivos Sincronização de Arquivos do Azure deve ser carregado em todos os momentos para que Sincronização de Arquivos do Azure funcione corretamente.
  - *Ponto de nova análise ausente, corrompido ou*danificado. Um ponto de nova análise é uma estrutura de dados especial em um arquivo que consiste em duas partes:
    1. Uma marca de nova análise, que indica ao sistema operacional que o Sincronização de Arquivos do Azure filtro do sistema de arquivos (StorageSync. sys) pode precisar executar alguma ação na e/s do arquivo. 
    2. Reanálise de dados, que indica ao filtro do sistema de arquivos o URI do arquivo no ponto de extremidade de nuvem associado (o compartilhamento de arquivos do Azure). 
        
       O modo mais comum de um ponto de nova análise pode ser corrompido é se um administrador tenta modificar a marca ou seus dados. 
  - *Problemas de conectividade de rede*. Para reorganizar ou recuperar um arquivo, o servidor deve ter conectividade com a Internet.

As seções a seguir indicam como solucionar problemas de camadas de nuvem e determinar se um problema é um problema de armazenamento em nuvem ou um problema de servidor.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Como monitorar a atividade de camadas em um servidor  
Para monitorar a atividade de camadas em um servidor, use a ID de evento 9003, 9016 e 9029 no log de eventos de telemetria (localizado em Applications and Services\Microsoft\FileSync\Agent in Visualizador de Eventos).

- A ID do evento 9003 fornece a distribuição de erros para um ponto de extremidade do servidor. Por exemplo, contagem total de erros, ErrorCode, etc. Observe que um evento é registrado por código de erro.
- A ID de evento 9016 fornece resultados fantasmas para um volume. Por exemplo, a porcentagem de espaço livre é, o número de arquivos fantasmas na sessão, o número de arquivos com falha em fantasma, etc.
- A ID de evento 9029 fornece informações de sessão de fantasma para um ponto de extremidade do servidor. Por exemplo, o número de arquivos tentados na sessão, o número de arquivos em camadas na sessão, o número de arquivos já em camadas, etc.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Como monitorar a atividade de recuperação em um servidor
Para monitorar a atividade de recuperação em um servidor, use a ID de evento 9005, 9006, 9009 e 9059 no log de eventos de telemetria (localizado em Applications and Services\Microsoft\FileSync\Agent in Visualizador de Eventos).

- A ID do evento 9005 fornece a confiabilidade de recuperação para um ponto de extremidade do servidor. Por exemplo, total de arquivos exclusivos acessados, total de arquivos exclusivos com acesso com falha, etc.
- A ID do evento 9006 fornece distribuição de erro de recuperação para um ponto de extremidade do servidor. Por exemplo, total de solicitações com falha, ErrorCode, etc. Observe que um evento é registrado por código de erro.
- A ID do evento 9009 fornece informações da sessão de recuperação para um ponto de extremidade do servidor. Por exemplo, DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, etc.
- A ID de evento 9059 fornece a distribuição de recuperação de aplicativo para um ponto de extremidade do servidor. Por exemplo, Shareid, nome do aplicativo e TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Como solucionar problemas de arquivos que falham na camada
Se os arquivos falharem na camada para os arquivos do Azure:

1. Em Visualizador de Eventos, examine os logs de eventos Telemétrico, operacional e de diagnóstico, localizados em aplicativos e Services\Microsoft\FileSync\Agent. 
   1. Verifique se os arquivos existem no compartilhamento de arquivos do Azure.

      > [!NOTE]
      > Um arquivo deve ser sincronizado com um compartilhamento de arquivos do Azure antes que possa ser colocado em camadas.

   2. Verifique se o servidor tem conectividade com a Internet. 
   3. Verifique se os drivers de filtro de Sincronização de Arquivos do Azure (StorageSync. sys e StorageSyncGuard. sys) estão em execução:
       - Em um prompt de comandos com privilégios elevados, execute `fltmc`. Verifique se os drivers de filtro do sistema de arquivos StorageSync. sys e StorageSyncGuard. sys estão listados.

> [!NOTE]
> Uma ID de evento 9003 é registrada uma vez por hora no log de eventos de telemetria se um arquivo falhar na camada (um evento é registrado por código de erro). Os logs de eventos operacionais e de diagnóstico devem ser usados se forem necessárias informações adicionais para diagnosticar um problema.

### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Como solucionar problemas de arquivos que falham ao serem recuperados  
Se os arquivos falharem ao serem recuperados:
1. Em Visualizador de Eventos, examine os logs de eventos Telemétrico, operacional e de diagnóstico, localizados em aplicativos e Services\Microsoft\FileSync\Agent.
    1. Verifique se os arquivos existem no compartilhamento de arquivos do Azure.
    2. Verifique se o servidor tem conectividade com a Internet. 
    3. Abra o snap-in do MMC de serviços e verifique se o serviço de agente de sincronização de armazenamento (FileSyncSvc) está em execução.
    4. Verifique se os drivers de filtro de Sincronização de Arquivos do Azure (StorageSync. sys e StorageSyncGuard. sys) estão em execução:
        - Em um prompt de comandos com privilégios elevados, execute `fltmc`. Verifique se os drivers de filtro do sistema de arquivos StorageSync. sys e StorageSyncGuard. sys estão listados.

> [!NOTE]
> Uma ID de evento 9006 é registrada uma vez por hora no log de eventos de telemetria se um arquivo não for relembrado (um evento é registrado por código de erro). Marque a seção [erros de recuperação e correção](#recall-errors-and-remediation) para ver se as etapas de correção estão listadas para o código de erro.

### <a name="recall-errors-and-remediation"></a>Recuperar erros e correção

| HRESULT | HRESULT (Decimal) | Cadeia do erro | Problema | Remediação |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Falha ao recuperar o arquivo devido a um tempo limite de e/s. Esse problema pode ocorrer por vários motivos: restrições de recursos de servidor, conectividade de rede deficiente ou um problema de armazenamento do Azure (por exemplo, limitação). | Nenhuma ação necessária. Se o erro persistir durante várias horas, abra um pedido de suporte. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Falha ao recuperar o arquivo devido a um problema de rede.  | Se o erro persistir, verifique a conectividade de rede para o compartilhamento de arquivos do Azure. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Falha ao recuperar o arquivo porque o ponto de extremidade do servidor foi excluído. | Para resolver esse problema, consulte [arquivos em camadas não podem ser acessados no servidor após a exclusão de um ponto de extremidade do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Falha ao recuperar o arquivo devido a um erro de acesso negado. Esse problema pode ocorrer se as configurações de firewall e rede virtual na conta de armazenamento estiverem habilitadas e o servidor não tiver acesso à conta de armazenamento. | Para resolver esse problema, adicione o endereço IP do servidor ou a rede virtual seguindo as etapas documentadas na seção [Configurar o firewall e as configurações de rede virtual](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) no guia de implantação. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Falha ao recuperar o arquivo porque ele não está acessível no compartilhamento de arquivos do Azure. | Para resolver esse problema, verifique se o arquivo existe no compartilhamento de arquivos do Azure. Se o arquivo existir no compartilhamento de arquivos do Azure, atualize para a versão mais recente do [agente](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)de sincronização de arquivos do Azure. |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Falha ao recuperar o arquivo devido a uma falha de autorização na conta de armazenamento. | Para resolver esse problema, verifique se [sincronização de arquivos do Azure tem acesso à conta de armazenamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Falha ao recuperar o arquivo porque o compartilhamento de arquivos do Azure não está acessível. | Verifique se o compartilhamento de arquivos existe e está acessível. Se o compartilhamento de arquivos foi excluído e recriado, execute as etapas documentadas na [sincronização, pois a seção compartilhamento de arquivos do Azure foi excluída e recriada](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134375810) para excluir e recriar o grupo de sincronização. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Falha ao recuperar o arquivo devido a recursos do sistema insuffcient. | Se o erro persistir, investigue qual driver de modo kernel ou aplicativo está esgotando os recursos do sistema. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Falha ao recuperar o arquivo devido à memória insuffcient. | Se o erro persistir, investigue qual driver do modo kernel ou aplicativo está causando a condição de memória insuficiente. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Falha ao recuperar o arquivo devido a espaço em disco insuficiente. | Para resolver esse problema, libere espaço no volume movendo arquivos para um volume diferente, aumente o tamanho do volume ou Force os arquivos a serem nivelados usando o cmdlet Invoke-StorageSyncCloudTiering. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Arquivos em camadas não são acessíveis no servidor após a exclusão de um ponto de extremidade do servidor
Os arquivos em camadas em um servidor ficarão inacessíveis se os arquivos não forem recuperados antes de excluir um ponto de extremidade do servidor.

Erros registrados se os arquivos em camadas não estiverem acessíveis
- Ao sincronizar um ficheiro, o código de erro -2147942467 (0x80070043 - ERROR_BAD_NET_NAME) é registado no registo de eventos DoitResults
- Ao recordar um ficheiro, o código de erro -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) é registado no registo do evento RecallResults

O restauro do acesso aos ficheiros em camadas será possível se forem cumpridas as seguintes condições:
- O ponto final do servidor foi eliminado nos últimos 30 dias
- O ponto final da cloud não foi eliminado 
- A partilha de ficheiros não foi eliminada
- O grupo de sincronização não foi eliminado

Se as condições acima forem cumpridas, poderá restaurar o acesso aos ficheiros no servidor através da recriação do ponto final do servidor no mesmo caminho no servidor dentro do mesmo grupo de sincronização no prazo de 30 dias. 

Se as condições acima não forem cumpridas, não será possível restaurar o acesso, uma vez que estes ficheiros em camadas no servidor são, agora, órfãos. Siga as instruções abaixo para remover os arquivos órfãos em camadas.

**Notas**
- Quando arquivos em camadas não estiverem acessíveis no servidor, o arquivo completo ainda deverá estar acessível se você acessar o compartilhamento de arquivos do Azure diretamente.
- Para evitar arquivos em camadas órfãos no futuro, siga as etapas documentadas em [remover um ponto de extremidade do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint) ao excluir um ponto de extremidade do servidor.

<a id="get-orphaned"></a>**Como obter a lista de arquivos órfãos em camadas** 

1. Verifique se o agente Sincronização de Arquivos do Azure versão v 5.1 ou posterior está instalado.
2. Execute os seguintes comandos do PowerShell para listar arquivos órfãos em camadas:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Salve o arquivo de saída OrphanTieredFiles. txt em caso de arquivos que precisam ser restaurados do backup após serem excluídos.

<a id="remove-orphaned"></a>**Como remover arquivos em camadas órfãos** 

*Opção 1: excluir os arquivos em camadas órfãos*

Essa opção exclui os arquivos em camadas órfãos no Windows Server, mas exige a remoção do ponto de extremidade do servidor, se ele existir devido à recreação após 30 dias ou se estiver conectado a um grupo de sincronização diferente. Os conflitos de arquivo ocorrerão se os arquivos forem atualizados no Windows Server ou no compartilhamento de arquivos do Azure antes de o ponto de extremidade do servidor ser recriado.

1. Verifique se o agente Sincronização de Arquivos do Azure versão v 5.1 ou posterior está instalado.
2. Faça backup do compartilhamento de arquivos do Azure e do local do ponto de extremidade do servidor.
3. Remova o ponto de extremidade do servidor no grupo de sincronização (se existir) seguindo as etapas documentadas em [remover um ponto de extremidade do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint).

> [!Warning]  
> Se o ponto de extremidade do servidor não for removido antes de usar o cmdlet Remove-StorageSyncOrphanedTieredFiles, excluir o arquivo em camadas órfãos no servidor excluirá o arquivo completo no compartilhamento de arquivos do Azure. 

4. Execute os seguintes comandos do PowerShell para listar arquivos órfãos em camadas:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Salve o arquivo de saída OrphanTieredFiles. txt em caso de arquivos que precisam ser restaurados do backup após serem excluídos.
6. Execute os seguintes comandos do PowerShell para excluir arquivos em camadas órfãos:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Notas** 
- Os arquivos em camadas modificados no servidor que não estão sincronizados com o compartilhamento de arquivos do Azure serão excluídos.
- Os arquivos em camadas que são acessíveis (não órfãos) não serão excluídos.
- Arquivos não em camadas permanecerão no servidor.

7. Opcional: recrie o ponto de extremidade do servidor se excluído na etapa 3.

*Opção 2: montar o compartilhamento de arquivos do Azure e copiar os arquivos localmente que estão órfãos no servidor*

Essa opção não requer a remoção do ponto de extremidade do servidor, mas requer espaço em disco suficiente para copiar os arquivos completos localmente.

1. [Monte](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) o compartilhamento de arquivos do Azure no Windows Server que tem arquivos em camadas órfãos.
2. Execute os seguintes comandos do PowerShell para listar arquivos órfãos em camadas:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Use o arquivo de saída OrphanTieredFiles. txt para identificar arquivos em camadas órfãos no servidor.
4. Substitua os arquivos órfãos em camadas copiando o arquivo completo do compartilhamento de arquivos do Azure para o Windows Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Como solucionar problemas de arquivos rechamados inesperadamente em um servidor  
Antivírus, backup e outros aplicativos que lêem grandes quantidades de arquivos provocam recuperações indesejadas, a menos que respeitem o atributo Skip offline e ignorem a leitura do conteúdo desses arquivos. Ignorar os ficheiros offline nos produtos que suportam esta opção ajuda a evitar obtenções não pretendidas durante operações como análises de antivírus ou trabalhos de criação de cópias de segurança.

Consulte o fornecedor do seu software para aprender a configurar a solução do mesmo para ignorar os ficheiros offline.

Recuperações não intencionais também podem ocorrer em outros cenários, como quando você está procurando arquivos no explorador de arquivos. Abrir uma pasta que tenha ficheiros em camadas na cloud no Explorador de Ficheiros no servidor pode originar obtenções não pretendidas. Isto é ainda mais provável se uma solução de antivírus estiver ativada no servidor.

> [!NOTE]
>Use a ID de evento 9059 no log de eventos de telemetria para determinar quais aplicativos estão causando recuperações. Esse evento fornece a distribuição de recall de aplicativos para um ponto de extremidade do servidor e é registrado uma vez por hora.

## <a name="general-troubleshooting"></a>Solução de problemas gerais
Se você encontrar problemas com Sincronização de Arquivos do Azure em um servidor, comece concluindo as seguintes etapas:
1. Em Visualizador de Eventos, examine os logs de eventos de telemetria, operacionais e de diagnóstico.
    - Os problemas de sincronização, camadas e RECALL são registrados nos logs de telemetria, diagnóstico e eventos operacionais em aplicativos e Services\Microsoft\FileSync\Agent.
    - Problemas relacionados ao gerenciamento de um servidor (por exemplo, definições de configuração) são registrados nos logs de eventos operacionais e de diagnóstico em aplicativos e Services\Microsoft\FileSync\Management.
2. Verifique se o serviço de Sincronização de Arquivos do Azure está em execução no servidor:
    - Abra o snap-in do MMC de serviços e verifique se o serviço de agente de sincronização de armazenamento (FileSyncSvc) está em execução.
3. Verifique se os drivers de filtro de Sincronização de Arquivos do Azure (StorageSync. sys e StorageSyncGuard. sys) estão em execução:
    - Em um prompt de comandos com privilégios elevados, execute `fltmc`. Verifique se os drivers de filtro do sistema de arquivos StorageSync. sys e StorageSyncGuard. sys estão listados.

Se o problema não for resolvido, execute a ferramenta AFSDiag:
1. Crie um diretório no qual a saída de AFSDiag será salva (por exemplo, C:\Output).
    > [!NOTE]
    >AFSDiag excluirá todo o conteúdo do diretório de saída antes de coletar logs. Especifique um local de saída que não contenha dados.
2. Abra uma janela do PowerShell com privilégios elevados e execute os seguintes comandos (pressione ENTER após cada comando):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Para o Sincronização de Arquivos do Azure nível de rastreamento do modo kernel, insira **1** (a menos que especificado de outra forma, para criar rastreamentos mais detalhados) e pressione Enter.
4. Para o Sincronização de Arquivos do Azure nível de rastreamento do modo de usuário, insira **1** (a menos que especificado de outra forma, para criar rastreamentos mais detalhados) e pressione Enter.
5. Reproduza o problema. Quando tiver terminado, digite **D**.
6. Um arquivo. zip que contém logs e arquivos de rastreamento é salvo no diretório de saída que você especificou.

## <a name="see-also"></a>Ver também
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
- [Perguntas frequentes sobre arquivos do Azure](storage-files-faq.md)
- [Resolver problemas de Ficheiros do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Troubleshoot Azure Files problems in Linux](storage-troubleshoot-linux-file-connection-problems.md) (Resolver problemas relacionados com Ficheiros do Azure no Linux)
