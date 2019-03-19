---
title: Resolver problemas da sincronização de ficheiros do Azure | Documentos da Microsoft
description: Resolva problemas comuns com o Azure File Sync.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: eeda1ed3181b8cc8f641ed731b7f00fac2d3fad6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005842"
---
# <a name="troubleshoot-azure-file-sync"></a>Resolver problemas da Sincronização de Ficheiros do Azure
Utilize o Azure File Sync para centralizar as partilhas de ficheiros da sua organização nos ficheiros do Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode usar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, NFS e FTPS. Pode ter o número de caches que precisar em todo o mundo.

Este artigo destina-se para o ajudar a resolver problemas que podem surgir com a sua implementação do Azure File Sync. Também descreveremos como recolher registos importantes do sistema, se for necessária uma investigação mais aprofundada do problema. Se não vir a resposta à sua pergunta, pode contactar-nos através dos canais seguintes (em ordem de cada vez maiores):

1. [Fórum de armazenamento do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata).
2. [UserVoice de ficheiros do Azure](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Suporte da Microsoft. Para criar um novo pedido de suporte, no portal do Azure, no **ajudar** separador, selecione a **ajuda + suporte** botão e, em seguida, selecione **novo pedido de suporte**.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Estou a ter um problema com o Azure File Sync em meu servidor (sincronização, na cloud a disposição em camadas, etc.). Posso remover e recriar o ponto final do meu servidor?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Registo de servidor e a instalação do agente
<a id="agent-installation-failures"></a>**Resolver problemas de falhas de instalação do agente**  
Se a instalação do agente do Azure File Sync falhar, numa linha de comandos elevada, execute o seguinte comando para ativar o registo durante a instalação do agente:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Reveja installer.log para determinar a causa da falha de instalação.

<a id="agent-installation-on-DC"></a>**Instalação do agente falhar no controlador de domínio do Active Directory**  
Se tentar instalar o agente de sincronização no controlador de domínio do Active Directory em que o proprietário de função PDC é num Windows Server 2008 R2 ou abaixo de versão do SO, pode atingir o problema em que o agente de sincronização não irá instalar.

Para resolver, transfira a função PDC para outro controlador de domínio com o Windows Server 2012 R2 ou mais recente, em seguida, instalar a sincronização.

<a id="server-registration-missing"></a>**Servidor não está listado em servidores registados no portal do Azure**  
Se um servidor não está listado em **servidores registados** para um serviço de sincronização de armazenamento:
1. Inicie sessão no servidor que pretende registar.
2. Abra o Explorador de ficheiros e, em seguida, vá para o diretório de instalação do agente de sincronização de armazenamento (a localização predefinida é c:\Programas\Microsoft Files\Azure\StorageSyncAgent). 
3. Execute ServerRegistration.exe e conclua o Assistente para registar o servidor com um serviço de sincronização de armazenamento.

<a id="server-already-registered"></a>**O registo do servidor apresenta a seguinte mensagem durante a instalação do agente do Azure File Sync: "Este servidor já está registado"** 

![Uma captura de ecrã da caixa de diálogo de registo do servidor com o erro "o servidor já está registado" mensagem](media/storage-sync-files-troubleshoot/server-registration-1.png)

Esta mensagem é apresentada se o servidor anteriormente foi registado com um serviço de sincronização de armazenamento. Para anular o registo do servidor do serviço de sincronização de armazenamento atual e, em seguida, registe-se com um novo serviço de sincronização de armazenamento, execute as etapas descritas [anular o registo de um servidor com o Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se o servidor não está listado em **servidores registados** no serviço de sincronização de armazenamento, no servidor que pretende anular o registo, execute os seguintes comandos do PowerShell:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se o servidor fizer parte de um cluster, pode utilizar o opcional *StorageSyncServer reposição - CleanClusterRegistration* parâmetro também deve remover o registo de cluster.

<a id="web-site-not-trusted"></a>**Quando eu registar um servidor, vejo várias respostas de "web site não fidedigno". Porquê?**  
Este problema ocorre quando o **segurança avançada do Internet Explorer** política for ativada durante o registo do servidor. Para obter mais informações sobre como desativar corretamente o **segurança avançada do Internet Explorer** política, veja [preparar o Windows Server para utilizar com o Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) e [como implementar os ficheiros do Azure Sincronização](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Gestão de grupo de sincronização
<a id="cloud-endpoint-using-share"></a>**Falha ao criar o ponto final na cloud, com este erro: "A partilha especificada do Azure FileShare já está em utilização por um CloudEndpoint diferente"**  
Este problema ocorre se a partilha de ficheiros do Azure já está a ser utilizada por outro ponto final da cloud. 

Se vir esta mensagem e a partilha de ficheiros do Azure atualmente não está em utilização por um ponto final da cloud, conclua os seguintes passos para limpar os metadados de sincronização de ficheiros do Azure na partilha de ficheiros do Azure:

> [!Warning]  
> A eliminar os metadados de uma partilha de ficheiros do Azure que está atualmente em utilização por um ponto final da cloud faz com que operações de sincronização de ficheiros do Azure efetuar a ativação. 

1. No portal do Azure, vá para a partilha de ficheiros do Azure.  
2. A partilha de ficheiros do Azure com o botão direito e, em seguida, selecione **Editar metadados**.
3. Com o botão direito **SyncService**e, em seguida, selecione **eliminar**.

<a id="cloud-endpoint-authfailed"></a>**Falha ao criar o ponto final na cloud, com este erro: "AuthorizationFailed"**  
Este problema ocorre se a sua conta de utilizador não tem direitos suficientes para criar um ponto final da cloud. 

Para criar um ponto final da cloud, sua conta de utilizador tem de ter as seguintes permissões de Authorization da Microsoft:  
* Leitura: Obter a definição de função
* Escreva: Criar ou atualizar uma definição de função personalizada
* Leitura: Obter a atribuição de função
* Escreva: Criar atribuição de função

As seguintes funções incorporadas têm as permissões necessárias do Authorization da Microsoft:  
* Proprietário
* Administrador de Acesso de Utilizador

Para determinar se a sua função de conta de utilizador tem as permissões necessárias:  
1. No portal do Azure, selecione **grupos de recursos**.
2. Selecione o grupo de recursos onde está localizada a conta de armazenamento e, em seguida, selecione **controlo de acesso (IAM)**.
3. Selecione o **atribuições de funções** separador.
4. Selecione o **função** (por exemplo, proprietário ou Contribuidor) para a sua conta de utilizador.
5. Na **fornecedor de recursos** lista, selecione **Microsoft Authorization**. 
    * **Atribuição de função** deve ter **leitura** e **escrever** permissões.
    * **Definição de função** deve ter **leitura** e **escrever** permissões.

<a id="server-endpoint-createjobfailed"></a>**Falha de criação de ponto final de servidor, com este erro: "MgmtServerJobFailed" (código de erro:-2134375898)**  
Este problema ocorre se o caminho do ponto final de servidor está no volume de sistema e na cloud em camadas está ativada. Na cloud em camadas não é suportada no volume do sistema. Para criar um ponto final do servidor no volume do sistema, desative a camada ao criar o ponto final do servidor de cloud.

<a id="server-endpoint-deletejobexpired"></a>**Eliminação de ponto final de servidor falha, com este erro: "MgmtServerJobExpired"**                
Este problema ocorre se o servidor está offline ou não tem conectividade de rede. Se o servidor já não estiver disponível, anular o registo do servidor no portal do que irá eliminar os pontos de extremidade do servidor. Para eliminar os pontos de extremidade do servidor, siga os passos descritos em [anular o registo de um servidor com o Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Não é possível abrir a página de propriedades do ponto final de servidor ou atualizar a política de camadas de cloud**  
Este problema pode ocorrer se uma operação de gestão, o ponto final de servidor falhar. Se a página de propriedades do ponto final de servidor não for aberto no portal do Azure, atualizar o ponto final de servidor utilizar comandos do PowerShell do servidor de pode corrigir este problema. 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzureRmStorageSyncServerEndpoint `
    -SubscriptionId mysubguid `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzureRmStorageSyncServerEndpoint `
    -Id serverendpointid `
    -CloudTiering true `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**Ponto final do servidor tem um Estado de funcionamento de "Sem atividade" ou "Pendente" e o estado do servidor no painel servidores registados é "Aparece offline"**  

Este problema pode ocorrer se o processo de Monitor de sincronização de armazenamento não está em execução ou o servidor não consegue comunicar com o serviço Azure File Sync devido a uma firewall ou proxy.

Para resolver este problema, realize os passos seguintes:

1. Abra o Gestor de Tarefas no servidor e verifique se o processo Monitor da Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) está em execução. Se não estiver em execução, experimente primeiro reiniciar o servidor. Se reiniciar o servidor não resolver o problema, atualize para o mais recente do Azure File Sync [versão do agente](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes).
2. Certifique-se de que as definições de Firewall e Proxy estão configuradas corretamente:
    - Se o servidor estiver por trás de uma firewall, verifique se a porta 443 de saída é permitida. Se a firewall restringe o tráfego a domínios específicos, certifique-se os domínios listados na Firewall [documentação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) estão acessíveis.
    - Se o servidor estiver atrás de um proxy, configure as definições de proxy de aplicações específicas ou todo o computador ao seguir os passos no Proxy [documentação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy).

<a id="endpoint-noactivity-sync"></a>**Ponto final do servidor tem um Estado de funcionamento das "Sem atividade" e o estado do servidor no painel servidores registados é "Online"**  

Um Estado de funcionamento do ponto final de servidor de "Sem atividade" significa que o ponto final do servidor não está conectada a atividade de sincronização nas últimas duas horas.

Um ponto final do servidor não poderá iniciar a atividade de sincronização pelos seguintes motivos:

- Versão do agente 4.3.0.0 ou mais antiga é instalada e o servidor tem uma sessão de sincronização ativa do VSS (SnapshotSync). Quando uma sessão de sincronização do VSS está ativa para um ponto final do servidor, os outros pontos de extremidade do servidor no mesmo volume não é possível iniciar uma sessão de sincronização inicial até que a sessão de sincronização VSS seja concluída. Para resolver este problema, instale a versão do agente 5.0.2.0 ou mais recente que suporta vários pontos finais de servidor que a sincronização num volume, quando a sessão de sincronização de um VSS está ativo.

    Para verificar a atividade de sincronização atual num servidor, consulte [como posso monitorizar o progresso de uma sessão de sincronização atual?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

- O servidor atingiu o número máximo de sessões de sincronização em simultâneo. 
    - Versão do agente 4.x e mais recentes: Limite varia com base nos recursos de sistema disponíveis.
    - Versão do agente 3.x: 2 sessões de sincronização do Active Directory por processador ou um máximo de 8 sessões de sincronização do Active Directory por servidor.

> [!Note]  
> Se o estado do servidor no painel servidores registados é "Aparece Offline", execute os passos documentados no [ponto final do servidor tem um Estado de funcionamento de "Sem atividade" ou "Pendente" e o estado do servidor no painel servidores registados é "Aparece offline" ](#server-endpoint-noactivity) secção.

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Se tiver criado um ficheiro diretamente na minha partilha de ficheiros do Azure através de SMB ou através do portal, quanto tempo é necessário para o ficheiro para sincronizar com servidores no grupo de sincronização?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Estado de funcionamento de ponto final de servidor está num estado pendente durante várias horas**  
Este problema é esperado se criar um ponto final da cloud e utiliza uma partilha de ficheiros do Azure que contém dados. A tarefa de enumeração de alteração que verifica a existência de alterações na partilha de ficheiros do Azure tem de concluir antes de se podem sincronizar arquivos entre os pontos finais de cloud e servidor. O tempo para concluir a tarefa é depende do tamanho do espaço de nomes na partilha de ficheiros do Azure. O estado de funcionamento do ponto final de servidor deve atualizar uma vez concluída a tarefa de enumeração de alteração.

### <a id="broken-sync"></a>Como posso monitorizar o estado de funcionamento de sincronização?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Em cada grupo de sincronização, pode desagregar para seus pontos de extremidade do servidor individual para ver o estado das sessões de última sincronização concluída. Uma coluna de estado de funcionamento verde e um valor não sincronizar ficheiros, de 0 indicam a sincronização está a funcionar conforme esperado. Se não for este o caso, veja a seguir uma lista de erros de sincronização comuns e como lidar com arquivos que não estão sincronizados. 

![Uma captura de ecrã do portal do Azure](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Servidor](#tab/server)
Aceda aos registos de telemetria do servidor, o que podem ser encontrados, além de eventos, Visualizador em `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`. Evento 9102 corresponde a uma sessão de sincronização concluída; Para obter o estado mais recente da sincronização, procure o evento mais recente com ID 9102. SyncDirection indica se esta sessão foi um carregamento ou transferência. Se o HResult for 0, a sessão de sincronização foi concluída com êxito. Um HResult diferente de zero, significa que ocorreu um erro durante a sincronização; Veja abaixo para obter uma lista dos erros comuns. Se o PerItemErrorCount for maior que 0, isso significa que alguns ficheiros ou pastas não foram sincronizados corretamente. É possível que tenha um HResult de 0, mas um PerItemErrorCount maior que 0.

Segue-se um exemplo de um carregamento com êxito. Por questão de brevidade, apenas alguns dos valores contidos em cada evento 9102 estão listados abaixo. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Por outro lado, um carregamento concluído com êxito, pode ser assim:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Às vezes, as sessões de sincronização falhar em geral ou ter um PerItemErrorCount diferente de zero, mas ainda manter o andamento, com alguns arquivos a sincronização com êxito. Isso pode ser visto nos aplicados * campos (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes), que lhe dizer quanto da sessão é que os sucedem. Se vir várias sessões de sincronização numa linha que estão a falhar, mas têm uma contagem de aplicados * cada vez maior, em seguida, deve dar tempo de sincronização para tentar novamente antes de abrir um pedido de suporte.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Como posso monitorizar o progresso de uma sessão de sincronização atual?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
No seu grupo de sincronização, vá para o ponto final do servidor em questão e veja a secção de atividade de sincronização para ver a contagem de ficheiros carregados ou transferidos na sessão de sincronização atual. Tenha em atenção que este estado será adiado cerca de 5 minutos e, se a sua sessão de sincronização é pequena o suficiente para ser concluída durante este período, pode não será possível reportar no portal. 

# <a name="servertabserver"></a>[Servidor](#tab/server)
Veja o que é mais recente 9302 na telemetria do registo de eventos no servidor (no Visualizador de eventos, vá para aplicações e serviços Logs\Microsoft\FileSync\Agent\Telemetry). Este evento indica o estado da sessão de sincronização atual. TotalItemCount indica quantos ficheiros estão a ser sincronizados, AppliedItemCount o número de ficheiros que foram sincronizados até agora e PerItemErrorCount o número de ficheiros que estão a falhar para sincronização (veja abaixo para saber como lidar com isso).

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

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Como posso saber se meus servidores estão sincronizadas entre si?
# <a name="portaltabportal1"></a>[Portal](#tab/portal1)
Para cada servidor num grupo de sincronização de determinado, certifique-se:
- Os carimbos de data / para a última tentativa de sincronização para carregamento e transferência é recentes.
- O estado está verde para carregamento e transferência.
- O campo de atividade de sincronização mostra muito poucos ou nenhum arquivo restantes para sincronização.
- O campo de ficheiros não sincronizar é 0 para carregamento e transferência.

# <a name="servertabserver"></a>[Servidor](#tab/server)
Veja as sessões de sincronização concluída, o que estão marcadas pelo 9102 eventos no registo de eventos de telemetria para cada servidor (no Visualizador de eventos, aceda a `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. Em qualquer determinado servidor, que pretende certificar-se de que o carregamento mais recente e baixe sessões foi concluídas com êxito. Para tal, verifique que o HResult e PerItemErrorCount são 0 para carregamento e transferência (o campo de SyncDirection indica se uma determinada sessão é uma sessão de carregamento ou transferência). Tenha em atenção que, se não vir uma sessão de sincronização concluídas recentemente, é provável que uma sessão de sincronização está atualmente em curso, o que é esperado se simplesmente adicionado ou modificado uma grande quantidade de dados.
2. Quando um servidor está completamente atualizado com a cloud e tiver sem alterações para sincronizar em ambas as direções, verá as sessões de sincronização está vazio. Estes são indicados por carregar e transferir eventos no qual todos os a sincronização * campos (SyncFileCount, SyncDirCount, SyncTombstoneCount e SyncSizeBytes) seja igual a zero, que significa que haveria nada para sincronizar. Tenha em atenção que estas sessões de sincronização vazio não podem ocorrer em servidores de elevado volume de alterações, há sempre Novidades para sincronização. Se não houver nenhuma atividade de sincronização, eles devem ocorrer a cada 30 minutos. 
3. Se todos os servidores estão atualizados com a nuvem, que significa que seu carregamento recente e sessões de download são sessões de sincronização vazio, pode dizer com certeza razoável que o sistema como um todo está em sincronização. 
    
Tenha em atenção que se tiver efetuado alterações diretamente na sua partilha de ficheiros do Azure, Azure File Sync não detetará esta alteração até execuções de enumeração de alteração, o que acontece a cada 24 horas. É possível que um servidor será apresentada a mensagem é atualizado com a cloud quando na verdade está em falta alterações recentes efetuadas diretamente na partilha de ficheiros do Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Como posso ver se existem ficheiros ou pastas que não estão sincronizados específicos?
Se sua PerItemErrorCount no servidor ou a sincronização de ficheiros não contabiliza no portal do são maiores que 0 para uma sessão de sincronização especificado, isso significa que alguns itens estão a falhar sincronizar. Ficheiros e pastas podem ter características que impedem que a sincronização. Essas características podem ser persistente e requerem uma ação explícita para retomar a sincronização, por exemplo, ao remover carateres não suportados do nome do ficheiro ou pasta. Também pode ser transitórios, o que significa que o ficheiro ou pasta retomará automaticamente a sincronização; Por exemplo, ficheiros com identificadores abertos retomará automaticamente a sincronização quando o arquivo é fechado. Quando o motor de sincronização de ficheiros do Azure Deteta um problema desse tipo, é produzido de um registo de erros que podem ser analisados para listar os itens que atualmente não sincronizar corretamente.

Para ver estes erros, execute o **FileSyncErrorsReport.ps1** script do PowerShell (localizado no diretório de instalação do agente do agente do Azure File Sync) identificar ficheiros que falharam na sincronização devido a identificadores abertos, não suportado carateres, ou outros problemas. O campo do caminho de item indica a localização do ficheiro em relação ao diretório de sincronização de raiz. Ver a lista de erros de sincronização comuns abaixo para obter os passos de remediação.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Resolução de problemas por erros de sincronização de diretório do ficheiro
**Registo de ItemResults - erros de sincronização por item**  

| HRESULT | HRESULT (decimal) | Cadeia do erro | Problema | Remediação |
|---------|-------------------|--------------|-------|-------------|
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Não é possível sincronizar ainda uma alteração de ficheiro ou diretório porque uma pasta dependente ainda não está sincronizada. Este item será sincronizado depois das alterações dependentes estarem sincronizadas. | Nenhuma ação necessária. |
| 0x7b | 123 | ERROR_INVALID_NAME | O nome de ficheiro ou diretório é inválido. | Mudar o nome de ficheiro ou diretório em questão. Ver [manipulação carateres não suportados](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x8007007b | -2147024773 | STIERR_INVALID_DEVICE_NAME | O nome de ficheiro ou diretório é inválido. | Mudar o nome de ficheiro ou diretório em questão. Ver [manipulação carateres não suportados](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Não é possível sincronizar um ficheiro porque está a ser utilizado. O ficheiro será sincronizado quando já não está a ser utilizado. | Nenhuma ação necessária. O Azure File Sync cria um instantâneo VSS temporário vez por dia no servidor para sincronizar ficheiros com identificadores abertos. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Um ficheiro foi alterado, mas a alteração ainda não foram detectada pelo sync. Sincronização irá recuperar após esta alteração é detetada. | Nenhuma ação necessária. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Não é possível sincronizar o ficheiro porque é atingido o limite de partilha de ficheiros do Azure. | Para resolver este problema, consulte [atingiu o limite de armazenamento da partilha de ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) secção no guia de resolução de problemas. |
| 0x80070005 | -2147024891 | E_ACCESSDENIED | Este erro pode ocorrer se o ficheiro é encriptado por uma solução não suportada (como o EFS de NTFS) ou o ficheiro tem uma exclusão estado pendente. | Se o ficheiro é encriptado por uma solução não suportada, descriptografar o arquivo e usar uma solução de encriptação suportados. Para obter uma lista de soluções de suporte, consulte [soluções de encriptação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption-solutions) secção no guia de planejamento. Se o ficheiro estiver numa exclusão estado pendente, o ficheiro será eliminado depois de todos os identificadores de ficheiros abertos são fechados. |
| 0x20 | 32 | ERROR_SHARING_VIOLATION | Não é possível sincronizar um ficheiro porque está a ser utilizado. O ficheiro será sincronizado quando já não está a ser utilizado. | Nenhuma ação necessária. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Um ficheiro foi alterado durante a sincronização, pelo que tem de ser sincronizado novamente. | Nenhuma ação necessária. |

#### <a name="handling-unsupported-characters"></a>Carateres de tratamento não suportado
Se o **FileSyncErrorsReport.ps1** script do PowerShell mostra falhas devido a carateres não suportados (0x7b de códigos de erro e 0x8007007b), deve remover ou mudar o nome de carateres com falha, os respetivo dos nomes de ficheiro. PowerShell provavelmente irá imprimir esses caracteres como pontos de interrogação ou retângulos vazios uma vez que a maior parte desses caracteres não têm nenhuma codificação visual padrão. O [ferramenta de avaliação](storage-sync-files-planning.md#evaluation-tool) pode ser utilizado para identificar os carateres que não são suportadas.

A tabela a seguir contém todos os caracteres unicode do Azure File Sync ainda não suporta.

| Conjunto de carateres | Contagem de carateres |
|---------------|-----------------|
| <ul><li>0x0000009D (comando do sistema operativo osc)</li><li>0x00000090 (cadeia de controlo do dispositivo de controladores de domínio)</li><li>0x0000008F (shift único do ss3 três)</li><li>0x00000081 (configuração predefinida de octeto alta)</li><li>0x0000007F (del delete)</li><li>0x0000008D (ri inversa avanço de linha)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (apresentação árabe formulários-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (specials) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (que não sejam caracteres)</li><li>0x0002FFFE - 0x0002FFFF = 2 (que não sejam caracteres)</li><li>0x0003FFFE - 0x0003FFFF = 2 (que não sejam caracteres)</li><li>0x0004FFFE - 0x0004FFFF = 2 (que não sejam caracteres)</li><li>0x0005FFFE - 0x0005FFFF = 2 (que não sejam caracteres)</li><li>0x0006FFFE - 0x0006FFFF = 2 (que não sejam caracteres)</li><li>0x0007FFFE - 0x0007FFFF = 2 (que não sejam caracteres)</li><li>0x0008FFFE - 0x0008FFFF = 2 (que não sejam caracteres)</li><li>0x0009FFFE - 0x0009FFFF = 2 (que não sejam caracteres)</li><li>0x000AFFFE - 0x000AFFFF = 2 (que não sejam caracteres)</li><li>0x000BFFFE - 0x000BFFFF = 2 (que não sejam caracteres)</li><li>0x000CFFFE - 0x000CFFFF = 2 (que não sejam caracteres)</li><li>0x000DFFFE - 0x000DFFFF = 2 (que não sejam caracteres)</li><li>0x000EFFFE - 0x000EFFFF = 2 (indefinido)</li><li>0x000FFFFE - 0x000FFFFF = 2 (área de uso particular suplementares)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Erros comuns de sincronização
<a id="-2147023673"></a>**A sessão de sincronização foi cancelada.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimal)** | -2147023673 | 
| **Cadeia de erro** | ERROR_CANCELLED |
| **Remediação necessária** | Não |

Sessões de sincronização poderão falhar por vários motivos, incluindo o servidor que está a ser reiniciado ou atualizado, instantâneos do VSS, etc. Embora este erro parece que necessita de seguimento, é seguro ignorar este erro, a menos que ele persiste durante um período de várias horas.

<a id="-2147012889"></a>**Não foi possível estabelecer uma ligação com o serviço.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (decimal)** | -2147012889 | 
| **Cadeia de erro** | WININET_E_NAME_NOT_RESOLVED |
| **Remediação necessária** | Sim |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**O pedido de utilizador foi limitado pelo serviço.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (decimal)** | -2134376372 |
| **Cadeia de erro** | ECS_E_USER_REQUEST_THROTTLED |
| **Remediação necessária** | Não |

Nenhuma ação é necessária; o servidor irá tentar novamente. Se este erro persistir durante mais de duas horas, crie um pedido de suporte.

<a id="-2134364065"></a>**Sincronização não é possível aceder à partilha de ficheiros do Azure especificada no ponto final da cloud.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimal)** | -2134364065 |
| **Cadeia de erro** | ECS_E_CANNOT_ACCESS_EXTERNAL_STORAGE_ACCOUNT |
| **Remediação necessária** | Sim |

Este erro ocorre porque o agente de sincronização de ficheiros do Azure não é possível aceder a partilha de ficheiros do Azure, o que poderá ser porque a partilha de ficheiros do Azure ou a conta de armazenamento que o aloja já não existe. Pode resolver este erro ao trabalhar com os seguintes passos:

1. [Certifique-se de que a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se que a conta de armazenamento não contém quaisquer regras de rede.](#troubleshoot-network-rules)
3. [Certifique-se de que existe a partilha de ficheiros do Azure.](#troubleshoot-azure-file-share)
4. [Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Não foi possível resolver o nome de conta de armazenamento utilizado.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (decimal)** | -2134364064 |
| **Cadeia de erro** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Remediação necessária** | Sim |

1. Verifique o que pode resolver o nome DNS de armazenamento do servidor.

    ```PowerShell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Certifique-se de que a conta de armazenamento existe.](#troubleshoot-storage-account)
3. [Verifique se que a conta de armazenamento não contém quaisquer regras de rede.](#troubleshoot-network-rules)

<a id="-1906441138"></a>**Falha na sincronização devido a um problema com a base de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (decimal)** | -1906441138 |
| **Cadeia de erro** | JET_errWriteConflict |
| **Remediação necessária** | Sim |

Este erro ocorre quando existe um problema com a base de dados interno utilizado pelo Azure File Sync. Quando ocorre este problema, crie um pedido de suporte e Entraremos em contacto consigo para o ajudar a resolver este problema.

<a id="-2134364053"></a>**Não é suportada a versão do agente de sincronização de ficheiros do Azure instalada no servidor.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (decimal)** | -2134364053 |
| **Cadeia de erro** | ECS_E_AGENT_VERSION_BLOCKED |
| **Remediação necessária** | Sim |

Este erro ocorre se a versão do agente de sincronização de ficheiros do Azure instalada no servidor não é suportada. Para resolver este problema, [atualizar]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) para um [suportada a versão do agente]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Atingiu o limite de armazenamento da partilha de ficheiros do Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (decimal)** | -2134351810 |
| **Cadeia de erro** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Remediação necessária** | Sim |

Este erro ocorre quando o limite de armazenamento da partilha de ficheiros do Azure foi atingido, que pode acontecer se uma quota é aplicada para uma partilha de ficheiros do Azure ou se a utilização exceder os limites para uma partilha de ficheiros do Azure. Para obter mais informações, consulte a [limites atuais para uma partilha de ficheiros do Azure](storage-files-scale-targets.md).

1. Navegue para o grupo de sincronização dentro do serviço de sincronização de armazenamento.
2. Selecione o ponto final da cloud dentro do grupo de sincronização.
3. Tenha em atenção o nome da partilha de ficheiros do Azure no painel aberto.
4. Selecione a conta de armazenamento ligada. Se esta ligação falhar, a conta de armazenamento referenciados foi removida.

    ![Uma captura de ecrã que mostra o painel de detalhes do ponto final de cloud com uma ligação para a conta de armazenamento.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Selecione **ficheiros** para ver a lista de partilhas de ficheiros.
6. Clique nos três pontos no final da linha para a partilha de ficheiros do Azure referenciada pelo ponto final da cloud.
7. Certifique-se de que o **utilização** é inferior a **Quota**. Tenha em atenção, a menos que foi especificada uma quota alternativa, a quota corresponderá a [tamanho máximo da partilha de ficheiros do Azure de](storage-files-scale-targets.md).

    ![Uma captura de ecrã das propriedades de partilha de ficheiros do Azure.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Se a partilha está cheio e de uma quota não está definida, uma forma possível de corrigir este problema é colocar cada subpasta do ponto de final de servidor atual em seu próprio ponto final do servidor em seus próprios grupos de sincronização separadas. Desta forma, cada subpasta serão sincronizados para partilhas de ficheiros do Azure individuais.

<a id="-2134351824"></a>**Não é possível encontrar a partilha de ficheiros do Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (decimal)** | -2134351824 |
| **Cadeia de erro** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Remediação necessária** | Sim |

Este erro ocorre quando a partilha de ficheiros do Azure não está acessível. Para resolver problemas:

1. [Certifique-se de que a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Certifique-se de que existe a partilha de ficheiros do Azure.](#troubleshoot-azure-file-share)

Se a partilha de ficheiros do Azure tiver sido eliminada, terá de criar uma nova partilha de ficheiros e, em seguida, recriar o grupo de sincronização. 

<a id="-2134364042"></a>**Sincronização é colocada em pausa enquanto esta subscrição do Azure é suspenso.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (decimal)** | -2134364042 |
| **Cadeia de erro** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Remediação necessária** | Sim |

Este erro ocorre quando a subscrição do Azure está suspensa. Sincronização irá ser reenabled quando a subscrição do Azure é restaurada. Ver [por que motivo a minha subscrição do Azure está desativada e como posso reativá-la?](../../billing/billing-subscription-become-disable.md) para obter mais informações.

<a id="-2134364052"></a>**A conta de armazenamento tem uma firewall ou redes virtuais configuradas.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (decimal)** | -2134364052 |
| **Cadeia de erro** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Remediação necessária** | Sim |

Este erro ocorre quando a partilha de ficheiros do Azure está inacessível devido a uma firewall de conta de armazenamento ou porque a conta de armazenamento pertence a uma rede virtual. O Azure File Sync ainda não tem suporte para esta funcionalidade. Para resolver problemas:

1. [Certifique-se de que a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se que a conta de armazenamento não contém quaisquer regras de rede.](#troubleshoot-network-rules)

Remova estas regras para corrigir este problema. 

<a id="-2134375911"></a>**Falha na sincronização devido a um problema com a base de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimal)** | -2134375911 |
| **Cadeia de erro** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Remediação necessária** | Não |

Este erro normalmente é resolvido em si e pode ocorrer se existirem:

* Um elevado número de alterações de ficheiros entre os servidores no grupo de sincronização.
* Um grande número de erros em ficheiros individuais e diretórios.

Se este erro persistir durante mais de algumas horas, crie um pedido de suporte e Entraremos em contacto consigo para o ajudar a resolver este problema.

<a id="-2146762487"></a>**O servidor não conseguiu estabelecer uma ligação segura. O serviço em nuvem recebeu um certificado inesperado.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (decimal)** | -2146762487 |
| **Cadeia de erro** | CERT_E_UNTRUSTEDROOT |
| **Remediação necessária** | Sim |

Este erro pode acontecer se a sua organização utilizar um proxy de terminação de SSL ou se uma entidade maliciosa esteja a intercetar o tráfego entre o servidor e o serviço de sincronização de ficheiros do Azure. Se tiver a certeza de que isso é esperado (porque a sua organização está a utilizar um proxy de terminação de SSL), se ignorar a verificação de certificado com uma substituição de registo.

1. Crie o valor de registo SkipVerifyingPinnedRootCertificate.

    ```PowerShell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Reinicie o serviço de sincronização no servidor registado.

    ```PowerShell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ao definir este valor de registo, o agente do Azure File Syn vai aceitar qualquer certificado de SSL fidedigno ao transferir dados entre o servidor e o serviço cloud.

<a id="-2147012894"></a>**Não foi possível estabelecer uma ligação com o serviço.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (decimal)** | -2147012894 |
| **Cadeia de erro** | WININET_E_TIMEOUT |
| **Remediação necessária** | Sim |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Falha na sincronização devido a um problema com a autenticação.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (decimal)** | -2134375680 |
| **Cadeia de erro** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Remediação necessária** | Sim |

Este erro pode dever-se:

- Hora do servidor está incorreta
- Falha na eliminação do ponto final de servidor
- Utilizado para autenticação de certificado está expirado. 
    Para verificar se o certificado está expirado, execute os seguintes passos:  
    1. Abra o snap-in MMC de certificados, selecione a conta de computador e navegue para \Personal\Certificates certificados (computador Local).
    2. Verifique se o certificado de autenticação de cliente expirou.

Se a hora do servidor estiver correta, execute os seguintes passos para resolver o problema:

1. Verifique se o agente do Azure File Sync versão 4.0.1.0 ou posterior está instalado.
2. Execute os seguintes comandos do PowerShell no servidor:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
    Login-AzureRmStorageSync -SubscriptionID <guid> -TenantID <guid>
    Reset-AzureRmStorageSyncServerCertificate -SubscriptionId <guid> -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**O volume onde está localizado o ponto final do servidor é baixo espaço em disco.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimal)** | -1906441711 |
| **Cadeia de erro** | JET_errLogDiskFull |
| **Remediação necessária** | Sim |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (decimal)** | -2134375654 |
| **Cadeia de erro** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Remediação necessária** | Sim |

Este erro ocorre porque o volume foi preenchido para cima. Este erro ocorre normalmente porque ficheiros fora do ponto final do servidor estiver a utilizar o espaço no volume. Liberte espaço no volume ao adicionar pontos finais de servidor adicionais, mover arquivos para um volume diferente ou aumentar o tamanho do volume do ponto final do servidor está.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**O serviço ainda não está pronto para sincronizar com este ponto final do servidor.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (decimal)** | -2134364145 |
| **Cadeia de erro** | ECS_E_REPLICA_NOT_READY |
| **Remediação necessária** | Não |

Este erro ocorre porque há o alterações na partilha de ficheiros do Azure diretamente e deteção de alteração está em curso. Sincronização cessa quando é concluída a deteção de alteração.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Falha na sincronização devido a problemas com muitos ficheiros individuais.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimal)** | -2134364145 |
| **Cadeia de erro** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Remediação necessária** | Sim |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimal)** | -2134375908 |
| **Cadeia de erro** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Remediação necessária** | Sim |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimal)** | -2134375853 |
| **Cadeia de erro** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Remediação necessária** | Sim |

Em casos em que há muitas por erros de sincronização de ficheiros, sessões de sincronização podem começar a falhar. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> O Azure File Sync cria um instantâneo VSS temporário vez por dia no servidor para sincronizar ficheiros com identificadores abertos.

<a id="-2134376423"></a>**Falha na sincronização devido a um problema com o caminho do ponto final de servidor.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (decimal)** | -2134376423 |
| **Cadeia de erro** | ECS_E_SYNC_INVALID_PATH |
| **Remediação necessária** | Sim |

Certifique-se de que o caminho existe, encontra-se num volume NTFS local e não é um ponto de reanálise ou ponto final de servidor existente.

<a id="-2134375817"></a>**Falha na sincronização porque a versão de driver de filtro não é compatível com a versão do agente**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (decimal)** | -2134375817 |
| **Cadeia de erro** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Remediação necessária** | Sim |

Este erro ocorre porque a versão de driver (StorageSync.sys) de filtro em camada de Cloud carregada não é compatível com o serviço de agente de sincronização de armazenamento (FileSyncSvc). Se o agente de sincronização de ficheiros do Azure foi atualizado, reinicie o servidor para concluir a instalação. Se o erro continuar a ocorrer, desinstale o agente, reinicie o servidor e reinstale o agente de sincronização de ficheiros do Azure.

<a id="-2134376373"></a>**O serviço está atualmente indisponível.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (decimal)** | -2134376373 |
| **Cadeia de erro** | ECS_E_SERVICE_UNAVAILABLE |
| **Remediação necessária** | Não |

Este erro ocorre porque o serviço de sincronização de ficheiros do Azure está disponível. Este erro irá resolver automaticamente quando o Azure File Sync service porque disponível novamente.

<a id="-2134375922"></a>**Falha na sincronização devido a um problema transitório com a base de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimal)** | -2134375922 |
| **Cadeia de erro** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Remediação necessária** | Não |

Este erro ocorre devido a um problema interno com a base de dados de sincronização. Este erro irá resolver automaticamente quando o Azure File Sync quando tenta repetir a sincronização. Se este erro persistir durante um período de expandir de tempo, crie um pedido de suporte e Entraremos em contacto consigo para o ajudar a resolver este problema.

### <a name="common-troubleshooting-steps"></a>Passos de resolução de problemas comuns
<a id="troubleshoot-storage-account"></a>**Certifique-se de que a conta de armazenamento existe.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Navegue para o grupo de sincronização dentro do serviço de sincronização de armazenamento.
2. Selecione o ponto final da cloud dentro do grupo de sincronização.
3. Tenha em atenção o nome da partilha de ficheiros do Azure no painel aberto.
4. Selecione a conta de armazenamento ligada. Se esta ligação falhar, a conta de armazenamento referenciados foi removida.
    ![Uma captura de ecrã que mostra o painel de detalhes do ponto final de cloud com uma ligação para a conta de armazenamento.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
# Variables for you to populate based on your configuration
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# Log into the Azure account and put the returned account information
# in a reference variable.
$acctInfo = Connect-AzAccount

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

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

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    –SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzureRmStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.DisplayName
}

if ($storageSyncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzureRmStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.DisplayName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzureRmStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $storageSyncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    Write-Host "The storage account referenced in the cloud endpoint does not exist."
}
```
---

<a id="troubleshoot-network-rules"></a>**Verifique se que a conta de armazenamento não contém quaisquer regras de rede.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Uma vez na conta de armazenamento, selecione **Firewalls e redes virtuais** no lado esquerdo da conta de armazenamento.
2. Dentro da conta de armazenamento, o **permitir o acesso de todas as redes** botão de opção deve ser selecionada.
    ![Uma captura de ecrã que mostra as armazenamento conta rede regras de firewall e desativadas.](media/storage-sync-files-troubleshoot/file-share-inaccessible-2.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
if ($storageAccount.NetworkRuleSet.DefaultAction -ne 
    [Microsoft.Azure.Commands.Management.Storage.Models.PSNetWorkRuleDefaultActionEnum]::Allow) {
    Write-Host ("The storage account referenced contains network " + `
        "rules which are not currently supported by Azure File Sync.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Certifique-se de que existe a partilha de ficheiros do Azure.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Clique em **descrição geral** na tabela esquerda do conteúdo para regressar à página da conta de armazenamento principal.
2. Selecione **ficheiros** para ver a lista de partilhas de ficheiros.
3. Certifique-se de que a partilha de ficheiros referenciada pelo ponto final da cloud é apresentada na lista de partilhas de ficheiros (que deve ter anotou isto no passo 1 acima).

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.StorageAccountShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    Write-Host "The Azure file share referenced by the cloud endpoint does not exist"
}
```
---

<a id="troubleshoot-rbac"></a>**Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento.**  
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Clique em **controlo de acesso (IAM)** na tabela esquerda do conteúdo.
1. Clique nas **atribuições de funções** separador à lista os utilizadores e aplicações (*principais de serviço*) que têm acesso à sua conta de armazenamento.
1. Certifique-se **serviço de sincronização de ficheiros de híbrida** é apresentado na lista com o **leitor e acesso a dados** função. 

    ![Uma captura de ecrã do principal de serviço do serviço de sincronização de ficheiros de híbrida no separador de controlo de acesso da conta de armazenamento](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Se **serviço de sincronização de ficheiros de híbrida** não aparecer na lista, execute os seguintes passos:

    - Clique em **Adicionar**.
    - Na **função** campo, selecione **leitor e acesso a dados**.
    - Na **selecionar** , digite **serviço de sincronização de ficheiros de híbrida**, selecione a função e clique em **guardar**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell    
$foundSyncPrincipal = $false
Get-AzRoleAssignment -Scope $storageAccount.Id | ForEach-Object { 
    if ($_.DisplayName -eq "Hybrid File Sync Service") {
        $foundSyncPrincipal = $true
        if ($_.RoleDefinitionName -ne "Reader and Data Access") {
            Write-Host ("The storage account has the Azure File Sync " + `
                "service principal authorized to do something other than access the data " + `
                "within the referenced Azure file share.")
        }

        break
    }
}

if (!$foundSyncPrincipal) {
    Write-Host ("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Como posso impedir que os utilizadores que contém carateres não suportados no servidor de ficheiros a criar?
Pode usar [filtragens de ficheiros do Gestor de recursos do servidor de ficheiros (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) para ficheiros de bloco com carateres não suportados nos respetivos nomes sejam criadas no servidor. Poderá ter de fazer isso com o PowerShell como a maioria dos caracteres não suportadas não é imprimíveis e para que precisa converter suas representações hexadecimais como caracteres pela primeira vez.

Primeiro, crie um grupo de arquivos do FSRM, utilizar o [cmdlet New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Este exemplo define o grupo que contém apenas duas dos carateres não suportados, mas pode incluir como muitos dos carateres conforme necessário no seu grupo de ficheiros.

```PowerShell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Depois de definir um grupo de arquivos do FSRM, pode criar uma tela de arquivos do FSRM utilizando o cmdlet New-FsrmFileScreen.

```PowerShell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Tenha em atenção que as filtragens de ficheiros só devem ser usadas para bloquear a criação de carateres não suportados pelo Azure File Sync. Se filtragens de ficheiros são usadas em outros cenários, sincronização continuamente tentará transferir os ficheiros da partilha de ficheiros do Azure para o servidor e será bloqueada devido a filtragem de ficheiros, resultando numa saída de dados de alta. 

## <a name="cloud-tiering"></a>Disposição em camadas na cloud 
Existem dois caminhos para falhas na cloud em camadas:

- Ficheiros podem falhar a uma camada, o que significa que o Azure File Sync sem êxito tenta a uma camada de um arquivo de ficheiros do Azure.
- Ficheiros poderão não conseguir recall, o que significa que o filtro de sistema de ficheiros do Azure File Sync (StorageSync.sys) não conseguirá baixar dados quando um usuário tenta acessar um arquivo que tem sido em camadas.

Existem duas classes principais de falhas que podem acontecer por meio de qualquer caminho de falha:

- Falhas de armazenamento na cloud
    - *Problemas de disponibilidade do serviço de armazenamento transitório*. Para obter mais informações, consulte a [contrato de nível de serviço (SLA) do armazenamento do Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Partilha de ficheiros do Azure inacessível*. Esta falha ocorre, normalmente, ao eliminar a partilha de ficheiros do Azure quando ainda é um ponto final da cloud num grupo de sincronização.
    - *Conta de armazenamento inacessível*. Esta falha ocorre, normalmente, ao eliminar a conta de armazenamento enquanto ele ainda tem uma partilha de ficheiros do Azure que é um ponto final da cloud num grupo de sincronização. 
- Falhas de servidor 
  - *Azure File Sync ficheiro filtro do sistema (StorageSync.sys) não está carregado*. Para poder responder a pedidos de criação de camadas/recolhimento, o filtro de sistema de ficheiros do Azure File Sync têm de ser carregado. O filtro que está a ser carregado, não pode ocorrer por diversos motivos, mas a razão mais comum é que o administrador descarregado-o manualmente. O filtro de sistema de ficheiros do Azure File Sync têm de ser carregado em todo o tempo para o Azure File Sync funcione corretamente.
  - *Ponto de reanálise quebrada em falta, danificado ou de outra forma*. Um ponto de reanálise é uma estrutura de dados especiais num arquivo que consiste em duas partes:
    1. Uma marca de reanálise, que indica ao sistema operativo que o filtro de sistema de ficheiros do Azure File Sync (StorageSync.sys) poderá ter de fazer alguma ação em e/s para o ficheiro. 
    2. Dados de reanálise, que indica para o filtro de sistema de ficheiros, o URI do ficheiro no ponto de final do cloud associados (a partilha de ficheiros do Azure). 
        
       A forma mais comum de que um ponto de reanálise poderão ficar danificado é se um administrador tenta modificar a marca ou seus dados. 
  - *Problemas de conectividade de rede*. Para o escalão ou lembre-se de um ficheiro, o servidor tem de ter conectividade à internet.

As secções seguintes indicam como resolver problemas de disposição em camadas na cloud e determinar se um problema é um problema de armazenamento na cloud ou um problema do servidor.

<a id="monitor-tiering-activity"></a>**Como monitorizar a atividade de disposição em camadas num servidor**  
Para monitorizar a atividade de disposição em camadas num servidor, utilize 9003 de ID de evento, 9016 e 9029 no registo de eventos de telemetria (localizado em Applications and Services\Microsoft\FileSync\Agent no Visualizador de eventos).

- ID de evento 9003 fornece distribuição de erros para um ponto final do servidor. Por exemplo, Contagem Total de erros, código de erro, etc. Tenha em atenção de que um evento é registado por código de erro.
- ID de evento 9016 fornece resultados à conversão em fantasma para um volume. Por exemplo, o espaço livre é de %, número de ficheiros fantasma na sessão, o número de ficheiros não conseguiu ghost, etc.
- ID de evento 9029 fornece informações de sessão à conversão em fantasma para um ponto final do servidor. Por exemplo, o número de ficheiros tentada a sessão, o número de ficheiros em camadas na sessão, número de ficheiros já em camadas, etc.

<a id="monitor-recall-activity"></a>**Como monitorizar a atividade de recolhimento num servidor**  
Para monitorizar a atividade de recolhimento num servidor, utilize 9005 de ID de evento, 9006, 9009 e 9059 no registo de eventos de telemetria (localizado em Applications and Services\Microsoft\FileSync\Agent no Visualizador de eventos).

- ID de evento 9005 fornece confiabilidade de recolhimento de um ponto de final do servidor. Por exemplo, Total de ficheiros exclusivos acedidos, Total de ficheiros exclusivo com acesso falhadas, etc.
- ID de evento 9006 fornece distribuição de erros de recolhimento de um ponto de final do servidor. Por exemplo, Total de pedidos falhados, código de erro, etc. Tenha em atenção de que um evento é registado por código de erro.
- ID de evento 9009 fornece informações de sessão de recolhimento de um ponto de final do servidor. Por exemplo, DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, etc.
- ID de evento 9059 fornece distribuição de solicitação de recolhimento de aplicativo para um ponto final do servidor. Por exemplo, ShareId, nome da aplicação e TotalEgressNetworkBytes.

<a id="files-fail-tiering"></a>**Resolver problemas de ficheiros que não obedeçam a uma camada**  
Se falharem ficheiros a uma camada para ficheiros do Azure:

1. No Visualizador de eventos, reveja a telemetria, registos de eventos operacionais e diagnóstico, localizados em Applications and Services\Microsoft\FileSync\Agent. 
   1. Certifique-se de que os ficheiros de existir na partilha de ficheiros do Azure.

      > [!NOTE]
      > Um ficheiro têm de ser sincronizado para uma partilha de ficheiros do Azure antes de pode em camadas.

   2. Certifique-se de que o servidor tem conectividade à internet. 
   3. Certifique-se de que os controladores de filtro do Azure File Sync (StorageSync.sys e StorageSyncGuard.sys) estão em execução:
       - Na linha de comandos elevada, execute `fltmc`. Certifique-se de que os controladores de filtro do sistema do ficheiro StorageSync.sys e StorageSyncGuard.sys estão listados.

> [!NOTE]
> Um 9003 de ID de evento é registado uma vez por hora no registo de eventos de telemetria, se falhar de um ficheiro a uma camada (um evento é registado por código de erro). O operacional e os registos de diagnóstico do evento deve ser usados se são precisas informações adicionais para diagnosticar um problema.

<a id="files-fail-recall"></a>**Resolver problemas de ficheiros que não obedeçam a ser recolhido**  
Se falharem ficheiros a ser recolhido:
1. No Visualizador de eventos, reveja a telemetria, registos de eventos operacionais e diagnóstico, localizados em Applications and Services\Microsoft\FileSync\Agent.
    1. Certifique-se de que os ficheiros de existir na partilha de ficheiros do Azure.
    2. Certifique-se de que o servidor tem conectividade à internet. 
    3. Abra o snap-in MMC de serviços e certifique-se de que o serviço de agente de sincronização de armazenamento (FileSyncSvc) está em execução.
    4. Certifique-se de que os controladores de filtro do Azure File Sync (StorageSync.sys e StorageSyncGuard.sys) estão em execução:
        - Na linha de comandos elevada, execute `fltmc`. Certifique-se de que os controladores de filtro do sistema do ficheiro StorageSync.sys e StorageSyncGuard.sys estão listados.

> [!NOTE]
> Um 9006 de ID de evento é registado, uma vez por hora no registo de eventos de telemetria, se não for possível Lembre-se de um ficheiro (um evento é registado por código de erro). O operacional e os registos de diagnóstico do evento deve ser usados se são precisas informações adicionais para diagnosticar um problema.

<a id="files-unexpectedly-recalled"></a>**Resolver problemas de fazer um Recall inesperadamente num servidor de ficheiros**  
Antivírus, cópia de segurança e outros aplicativos que lêem o grande número de ficheiros causam recolhas indesejadas, a menos que respeitem o atributo offline ignorar e ignorem a leitura do conteúdo desses ficheiros. Ignorar os ficheiros offline nos produtos que suportam esta opção ajuda a evitar obtenções não pretendidas durante operações como análises de antivírus ou trabalhos de criação de cópias de segurança.

Consulte o fornecedor do seu software para aprender a configurar a solução do mesmo para ignorar os ficheiros offline.

Recolhas indesejadas também podem ocorrer em outros cenários, como quando está a aceder a ficheiros no Explorador de ficheiros. Abrir uma pasta que tenha ficheiros em camadas na cloud no Explorador de Ficheiros no servidor pode originar obtenções não pretendidas. Isto é ainda mais provável se uma solução de antivírus estiver ativada no servidor.

> [!NOTE]
>Utilize 9059 de ID de evento no registo de eventos de telemetria para determinar a que aplicações está a causar recolhas. Este evento fornece distribuição de solicitação de recolhimento de aplicativo para um ponto final do servidor e é registado uma vez por hora.

## <a name="general-troubleshooting"></a>Resolução de problemas
Se tiver problemas com o Azure File Sync num servidor, comece por concluir os passos seguintes:
1. No Visualizador de eventos, reveja a telemetria, registos de eventos operacionais e de diagnóstico.
    - Sincronizar, disposição em camadas, e lembre-se problemas são registados na telemetria, registos de eventos de diagnóstico e operacionais em Services\Microsoft\FileSync\Agent e aplicações.
    - Problemas relacionados ao gerenciamento de um servidor (por exemplo, definições de configuração) são registados nos registos de eventos operacionais e de diagnóstico em Services\Microsoft\FileSync\Management e aplicações.
2. Certifique-se de que o serviço Azure File Sync está em execução no servidor:
    - Abra o snap-in MMC de serviços e certifique-se de que o serviço de agente de sincronização de armazenamento (FileSyncSvc) está em execução.
3. Certifique-se de que os controladores de filtro do Azure File Sync (StorageSync.sys e StorageSyncGuard.sys) estão em execução:
    - Na linha de comandos elevada, execute `fltmc`. Certifique-se de que os controladores de filtro do sistema do ficheiro StorageSync.sys e StorageSyncGuard.sys estão listados.

Se o problema não for resolvido, execute a ferramenta de AFSDiag:
1. Crie um diretório em que a saída de AFSDiag será guardada (por exemplo, C:\Output).
2. Abra uma janela elevada do PowerShell e, em seguida, execute os seguintes comandos (prima Enter após cada comando):

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Para o nível de rastreio do Azure File Sync kernel modo, introduza **1** (a menos que especificado em contrário, para criar mais detalhado), e, em seguida, prima Enter.
4. Para o nível de rastreio de modo do usuário de sincronização de ficheiros do Azure, introduza **1** (a menos que especificado em contrário, para criar mais detalhado), e, em seguida, prima Enter.
5. Reproduza o problema. Quando tiver terminado, introduza **1!d**.
6. Um ficheiro. zip que contém os registos e ficheiros de rastreio são guardados no diretório de saída que especificou.

## <a name="see-also"></a>Consulte também
- [Monitorizar a sincronização de ficheiros do Azure](storage-sync-files-monitoring.md)
- [Perguntas mais frequentes sobre os ficheiros do Azure](storage-files-faq.md)
- [Resolver problemas de Ficheiros do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Resolução de problemas de ficheiros do Azure no Linux](storage-troubleshoot-linux-file-connection-problems.md)
