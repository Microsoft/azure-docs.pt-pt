---
title: Troubleshoot Azure File Sync Microsoft Docs
description: Problemas comum com o Azure File Sync.
author: jeffpatt24
ms.service: storage
ms.topic: conceptual
ms.date: 1/22/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 11942a08d46f4b46dc5478fca4b64796b9ce0a7c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82176129"
---
# <a name="troubleshoot-azure-file-sync"></a>Resolver problemas da Sincronização de Ficheiros do Azure
Utilize o Azure File Sync para centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, NFS e FTPS. Podes ter as caches que precisares em todo o mundo.

Este artigo foi concebido para o ajudar a resolver problemas e a resolver problemas que poderá encontrar com a sua implementação de Sincronização de Ficheiros Azure. Também descrevemos como recolher registos importantes do sistema se for necessária uma investigação mais profunda sobre o assunto. Se não vir a resposta à sua pergunta, pode contactar-nos através dos seguintes canais (por ordem crescente):

1. Fórum de [Armazenamento Azure.](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Suporte da Microsoft. Para criar um novo pedido de suporte, no portal Azure, no separador **Ajuda,** selecione o botão **de suporte Ajuda +** e, em seguida, selecione Novo pedido de **suporte**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Estou tendo um problema com O Sincronizado de Ficheiros Azure no meu servidor (sincronização, nível de nuvem, etc.). Devo remover e recriar o ponto final do meu servidor?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Instalação do agente e registo do servidor
<a id="agent-installation-failures"></a>**Falhas na instalação do agente de resolução de problemas**  
Se a instalação do agente Dessincronização de ficheiros Azure falhar, a uma altura de comando elevada, execute o seguinte comando para ligar a exploração durante a instalação do agente:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Reveja o instalador.log para determinar a causa da falha de instalação.

<a id="agent-installation-on-DC"></a>**Instalação do agente falha no Controlador de Domínio do Diretório Ativo**  
Se tentar instalar o agente de sincronização num controlador de domínio ative diretório onde o titular da função PDC se encontra numa versão Do Windows Server 2008 R2 ou abaixo do SISTEMA, poderá atingir o problema em que o agente de sincronização não será instalado.

Para resolver, transfira a função PDC para outro controlador de domínio que execute o Windows Server 2012 R2 ou mais recente, em seguida, instale sincronização.

<a id="parameter-is-incorrect"></a>**Aceder a um volume no Windows Server 2012 R2 falha com erro: O parâmetro está incorreto**  
Depois de criar um ponto final do servidor no Windows Server 2012 R2, ocorre o seguinte erro ao aceder ao volume:

carta de condução:\ não é acessível.  
Parâmetro incorreto.

Para resolver, instale as mais recentes atualizações para o Windows Server 2012 R2 e reinicie o servidor.

<a id="server-registration-missing-subscriptions"></a>**Registo do Servidor não lista todas as subscrições do Azure**  
Ao registar um servidor utilizando serverRegistration.exe, faltam subscrições quando clica na queda da subscrição do Azure.

Este problema ocorre porque serverRegistration.exe não suporta atualmente ambientes multi-inquilinos. Este problema será corrigido numa futura atualização do agente Azure File Sync.

Para contornar este problema, utilize os seguintes comandos PowerShell para registar o servidor:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Registo do Servidor apresenta a seguinte mensagem: "Faltam pré-requisitos"**  
Esta mensagem aparece se o módulo PowerShell Az ou AzureRM não estiver instalado no PowerShell 5.1. 

> [!Note]  
> ServerRegistration.exe não suporta PowerShell 6.x. Pode utilizar o cmdlet Register-AzStorageSyncServer no PowerShell 6.x para registar o servidor.

Para instalar o módulo Az ou AzureRM no PowerShell 5.1, execute os seguintes passos:

1. Digite a **powershell** de um pedido de comando elevado e bata.
2. Instale o mais recente módulo Az ou AzureRM seguindo a documentação:
    - [Módulo Az (requer .NET 4.7.2)](https://go.microsoft.com/fwlink/?linkid=2062890)
    - [Módulo do AzureRM]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Executar ServerRegistration.exe e completar o assistente para registar o servidor com um Serviço de Sincronização de Armazenamento.

<a id="server-already-registered"></a>**Registo do Servidor apresenta a seguinte mensagem: "Este servidor já está registado"** 

![Uma imagem do diálogo de registo do servidor com a mensagem de erro "servidor já está registada"](media/storage-sync-files-troubleshoot/server-registration-1.png)

Esta mensagem aparece se o servidor foi previamente registado num Serviço de Sincronização de Armazenamento. Para desregistar o servidor do atual Serviço de Sincronização de Armazenamento e, em seguida, registar-se com um novo Serviço de Sincronização de Armazenamento, preencha os passos descritos no [Desregistro de um servidor com O Sincronizado de Ficheiros Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se o servidor não estiver listado nos **servidores registados** no Serviço de Sincronização de Armazenamento, no servidor que pretende desregistar, execute os seguintes comandos PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se o servidor fizer parte de um cluster, pode utilizar o parâmetro opcional *Reset-StorageSyncServer -CleanClusterRegistration* para remover também o registo do cluster.

<a id="web-site-not-trusted"></a>**Quando registo um servidor, vejo numerosas respostas de "site não confiável". Porquê?**  
Este problema ocorre quando a política de Segurança do Explorador de **Internet melhorada** é ativada durante o registo do servidor. Para obter mais informações sobre como desativar corretamente a política de segurança do Explorador de **Internet Melhorada,** consulte [o Prepare o Windows Server para utilizar com o Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) e como implementar o Sync de [Ficheiros Azure](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**O servidor não está listado em servidores registados no portal Azure**  
Se um servidor não estiver listado em **servidores registados** para um Serviço de Sincronização de Armazenamento:
1. Faça sessão no servidor que pretende registar.
2. Abra o Explorador de Ficheiros e, em seguida, vá ao diretório de instalação do Agente de Sincronização de Armazenamento (a localização padrão é C:\Program Files\Azure\StorageSyncAgent). 
3. Executar ServerRegistration.exe e completar o assistente para registar o servidor com um Serviço de Sincronização de Armazenamento.

## <a name="sync-group-management"></a>Gestão de gruposincronizado
<a id="cloud-endpoint-using-share"></a>**A criação de cloud endpoint falha, com este erro: "O específico Azure FileShare já está em uso por um CloudEndpoint diferente"**  
Este erro ocorrerá se a partilha de ficheiros do Azure já estiver a ser utilizada por outro ponto final da cloud. 

Se vir esta mensagem e a partilha de ficheiros Azure não estiver atualmente a ser utilizada por um ponto final na nuvem, complete os seguintes passos para limpar os metadados do Ficheiro Azure Sync na partilha de ficheiros Azure:

> [!Warning]  
> A desposição dos metadados numa partilha de ficheiros Azure que está atualmente a ser utilizada por um ponto final em nuvem faz com que as operações do Azure File Sync falhem. 

1. No portal Azure, vá à sua parte de ficheiro Sino.  
2. Clique na parte direita do ficheiro Azure e, em seguida, **selecione Metadados Editar**.
3. Clique no **SyncService**e, em seguida, **selecione Delete**.

<a id="cloud-endpoint-authfailed"></a>**Criação de cloud endpoint falha, com este erro: "Autorização Falhada"**  
Este erro ocorre se a sua conta de utilizador não tiver direitos suficientes para criar um ponto final na nuvem. 

Para criar um ponto final na nuvem, a sua conta de utilizador deve ter as seguintes permissões de Autorização da Microsoft:  
* Leia: Obter definição de papel
* Escrever: Criar ou atualizar definição de função personalizada
* Leia: Obter atribuição de funções
* Escrever: Criar atribuição de papéis

As seguintes funções incorporadas têm as permissões de Autorização da Microsoft necessárias:  
* Proprietário
* Administrador de Acesso de Utilizador

Para determinar se a sua função de conta de utilizador tem as permissões necessárias:  
1. No portal Azure, selecione **Grupos de Recursos**.
2. Selecione o grupo de recursos onde está a conta de armazenamento e, em seguida, selecione o controlo de **acesso (IAM)**.
3. Selecione o separador de **tarefas role.**
4. Selecione a **Função** (por exemplo, Proprietário ou Colaborador) para a sua conta de utilizador.
5. Na lista de Fornecedores de **Recursos,** selecione **Microsoft Authorization**. 
    * **A atribuição de papéis** deve ter **permissões de Leitura** e **Escrita.**
    * **A definição de papéis** deve ter **permissões de leitura** e **escrita.**

<a id="-2134375898"></a>**A criação de pontofinal do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2134375898 ou 0x80c80226)**  
Este erro ocorrerá se o caminho do ponto final do servidor estiver no volume de sistema e o arrumo na cloud estiver ativado. O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.

<a id="-2147024894"></a>**A criação de pontofinal do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2147024894 ou 0x80070002)**  
Este erro ocorrerá se o caminho do ponto final do servidor especificado não for válido. Confirme se o caminho do ponto final do servidor especificado é um volume NTFS ligado localmente. Nota: O Azure File Sync não suporta as unidades mapeadas como um caminho final do servidor.

<a id="-2134375640"></a>**A criação de pontofinal do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2134375640 ou 0x80c80328)**  
Este erro ocorre se o caminho de ponto final do servidor especificado não for um volume NTFS. Confirme se o caminho do ponto final do servidor especificado é um volume NTFS ligado localmente. Nota: O Azure File Sync não suporta as unidades mapeadas como um caminho final do servidor.

<a id="-2134347507"></a>**A criação de pontofinal do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2134347507 ou 0x80c8710d)**  
Este erro ocorre porque o Azure File Sync não suporta pontos finais de servidor em volumes que tenham uma pasta de Informações de Volume de Sistema comprimida. Para resolver este problema, descomprima a pasta de Informações de Volume de Sistema. Se a pasta de Informações de Volume de Sistema for a única pasta comprimida no volume, execute os seguintes passos:

1. Baixe a ferramenta [PsExec.](https://docs.microsoft.com/sysinternals/downloads/psexec)
2. Executar o seguinte comando a partir de um pedido de comando elevado para lançar um pedido de comando em execução sob a conta do sistema: **PsExec.exe -i-s -s-d cmd**
3. Na linha de comandos em execução na conta do sistema, escreva os seguintes comandos e prima enter:   
    **cd /d "drive letter:\System Volume Information"**  
    **compacto /u /s**

<a id="-2134376345"></a>**A criação de pontofinal do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2134376345 ou 0x80C80067)**  
Este erro ocorrerá se o limite de pontos finais do servidor por servidor for atingido. O Azure File Sync suporta atualmente até 30 pontos finais por servidor. Para mais informações, consulte os alvos da [escala De sincronização de ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**A criação de pontofinal do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2134376427 ou 0x80c80015)**  
Este erro ocorrerá se outro ponto final do servidor já estiver a sincronizar o caminho do ponto final do servidor especificado. O Azure File Sync não suporta múltiplos pontos finais de servidor a sincronizar o mesmo diretório ou volume.

<a id="-2160590967"></a>**A criação de pontofinal do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2160590967 ou 0x80c80077)**  
Este erro ocorre se o caminho do ponto final do servidor contiver ficheiros órfãos. Se um ponto final do servidor foi recentemente removido, aguarde até que a limpeza de ficheiros órfãos esteja concluída. Um Id de evento 6662 é registado no registo do evento telemetria uma vez que a limpeza de ficheiros órfãos hierarquizados tenha começado. Um Id de evento 6661 é registado uma vez que a limpeza de ficheiros órfãos está concluída e um ponto final do servidor pode ser recriado usando o caminho. Se a criação do ponto final do servidor falhar após o registo de um Id 6661 do Evento, remova os ficheiros hierárquicos órfãos, executando os passos documentados nos [ficheiros Tiered, não estão acessíveis no servidor após a eliminação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) de uma secção de ponto final do servidor.

<a id="-2134347757"></a>**A eliminação do ponto final do servidor falha, com este erro: "MgmtServerJobExpired" (Código de erro: -2134347757 ou 0x80c87013)**  
Este erro ocorrerá se o servidor estiver offline ou não tiver conectividade de rede. Se o servidor deixar de estar disponível, anule o registo do servidor no portal, o que eliminará os pontos finais do servidor. Para eliminar os pontos finais do servidor, siga os passos descritos no [Desregistre um servidor com O Sincronizado de Ficheiros Azure](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Incapaz de abrir página de propriedades de ponto final do servidor ou atualizar a política de tiering de nuvem**  
Este problema pode ocorrer se uma operação de gestão no ponto final do servidor falhar. Se a página de propriedades do ponto final do servidor não abrir no portal Azure, atualizar o ponto final do servidor utilizando comandos PowerShell do servidor pode corrigir este problema. 

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
<a id="server-endpoint-noactivity"></a>**O ponto final do servidor tem um estado de saúde de "Nenhuma Atividade" ou "Pendente" e o estado do servidor na lâmina dos servidores registados é "Aparece offline"**  

Este problema pode ocorrer se o processo de Monitor de Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) não estiver em execução ou o servidor não conseguir aceder ao serviço Dessincronização de ficheiros Azure.

No servidor que está a mostrar como "Aparece offline" no portal, veja o Event ID 9301 no registo de eventos da Telemettry (localizado em Aplicações e Serviços\Microsoft\FileSync\Agent in Event Viewer) para determinar por que razão o servidor não consegue aceder ao serviço Dessincronização de Ficheiros Azure. 

- Se o **GetNextJob concluído com** o estado: 0 está registado, o servidor pode comunicar com o serviço Dessincronização de ficheiros Azure. 
    - Abra o Gestor de Tarefas no servidor e verifique se o processo Monitor da Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) está em execução. Se não estiver em execução, experimente primeiro reiniciar o servidor. Se o reinício do servidor não resolver o problema, atualize para a [versão do agente](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) do Azure File Sync mais recente. 

- Se o **GetNextJob concluído com o estado: -2134347756,** o servidor não pode comunicar com o serviço Dessincronização de ficheiros Azure devido a uma firewall ou procuração. 
    - Se o servidor estiver por trás de uma firewall, verifique se a porta 443 de saída é permitida. Se a firewall restringir o tráfego a domínios específicos, confirme que os domínios listados na [documentação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#firewall) firewall estão acessíveis.
    - Se o servidor estiver por detrás de um proxy, configure as definições de procuração específicas para a máquina ou para aplicações, seguindo os passos na [documentação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#proxy)proxy .
    - Utilize o cmdlet de conectividade Deteste-StorageSyncNetwork para verificar a conectividade da rede aos pontos finais do serviço. Para saber mais, consulte a [conectividade da rede de teste para os pontos finais de serviço](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints).

- Se o **GetNextJob concluído com o estado: -2134347764,** o servidor não pode comunicar com o serviço Dessincronização de ficheiros Azure devido a um certificado expirado ou eliminado.  
    - Executar o seguinte comando PowerShell no servidor para redefinir o certificado utilizado para a autenticação:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**O ponto final do servidor tem um estado de saúde de "No Activity" e o estado do servidor na lâmina dos servidores registados é "Online"**  

Um estado de saúde de ponto final do servidor de "Nenhuma Atividade" significa que o ponto final do servidor não registou a atividade de sincronização nas últimas duas horas.

Para verificar a atividade sincronizada atual num servidor, veja [como monitorizo o progresso de uma sessão de sincronização atual?](#how-do-i-monitor-the-progress-of-a-current-sync-session)

Um ponto final do servidor pode não registar a atividade de sincronização durante várias horas devido a um bug ou recursos insuficientes do sistema. Verifique se a mais recente versão do [agente](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes) DoMS está instalada. Se a questão persistir, abra um pedido de apoio.

> [!Note]  
> Se o estado do servidor na lâmina dos servidores registadofor "Appears Offline", execute os passos documentados no ponto final do [Servidor tem um estado de saúde de "Nenhuma Atividade" ou "Pendente" e o estado do servidor na lâmina dos servidores registados é a secção "Aparece offline".](#server-endpoint-noactivity)

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Se criei um ficheiro diretamente no meu ficheiro Azure partilhar sobre o SMB ou através do portal, quanto tempo leva para o ficheiro sincronizar servidores no grupo de sincronização?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**A saúde do ponto final do servidor está em estado pendente por várias horas**  
Este problema é esperado se criar um ponto final em nuvem e utilizar uma partilha de ficheiros Azure que contenha dados. O trabalho de enumeração de alteração que digitaliza as alterações na partilha de ficheiros Azure deve ser concluído antes que os ficheiros possam sincronizar entre os pontos finais da nuvem e do servidor. O tempo para completar o trabalho depende do tamanho do espaço de nome na partilha de ficheiros Azure. A saúde do ponto final do servidor deve atualizar assim que o trabalho de enumeração de alteração estiver concluído.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Como posso monitorizar o estado de funcionamento de sincronização?
# <a name="portal"></a>[Portal](#tab/portal1)
Dentro de cada grupo de sincronização, pode perfurar os seus pontos finais do servidor individual para ver o estado das últimas sessões de sincronização concluídas. Uma coluna de Saúde verde e um valor de 0 ficheiros não sincronizados indicam que a sincronização está a funcionar como esperado. Se não for esse o caso, consulte abaixo uma lista de erros de sincronização comuns e como lidar com ficheiros que não estejam a sincronizar. 

![Uma imagem do portal Azure](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Servidor](#tab/server)
Aceda aos registos de telemetria do servidor, que `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`podem ser encontrados no Espectador de Eventos em . O evento 9102 corresponde a uma sessão de sincronização concluída; para o mais recente estado de sincronização, procure o evento mais recente com ID 9102. A SyncDirection diz-lhe se esta sessão foi um upload ou download. Se o HResult é 0, então a sessão de sincronização foi bem sucedida. Um HResult não-zero significa que houve um erro durante a sincronização; ver abaixo para uma lista de erros comuns. Se o PerItemErrorCount for superior a 0, isto significa que alguns ficheiros ou pastas não sincronizaram corretamente. É possível ter um HResult de 0 mas um PerItemErrorCount que é maior que 0.

Abaixo está um exemplo de um upload bem sucedido. Por uma questão de brevidade, apenas alguns dos valores contidos em cada evento 9102 estão listados abaixo. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Inversamente, um upload mal sucedido pode parecer assim:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Por vezes, as sessões de sincronização falham no geral ou têm um PerItemErrorCount não-zero, mas ainda fazem progressos para a frente, com alguns ficheiros a sincronizarem-se com sucesso. Isto pode ser visto nos campos Applied* (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes), que lhe dizem quanto da sessão está a ter sucesso. Se vir várias sessões de sincronização seguidas que estão a falhar mas que têm uma contagem crescente de Applied* então deve dar tempo de sincronização para tentar novamente antes de abrir um bilhete de apoio.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Como posso monitorizar o progresso de uma sessão de sincronização atual?
# <a name="portal"></a>[Portal](#tab/portal1)
Dentro do seu grupo de sincronização, vá ao ponto final do servidor em questão e consulte a secção Sync Activity para ver a contagem de ficheiros carregado ou descarregado na sessão de sincronização atual. Note que este estado será adiado por cerca de 5 minutos, e se a sua sessão de sincronização for pequena o suficiente para ser concluída dentro deste período, poderá não ser reportada no portal. 

# <a name="server"></a>[Servidor](#tab/server)
Veja o mais recente evento 9302 no registo de telemetria no servidor (no Observador de Eventos, vá a Registos de Aplicações e Serviços\Microsoft\FileSync\Agent\Telemetria). Este evento indica o estado da atual sessão de sincronização. TotalItemCount denota quantos ficheiros devem ser sincronizados, AppliedItemCount o número de ficheiros que foram sincronizados até agora, e PerItemErrorCount o número de ficheiros que não estão a sincronizar (ver abaixo como lidar com isto).

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

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Como posso saber se os meus servidores estão sincronizados entre si?
# <a name="portal"></a>[Portal](#tab/portal1)
Para cada servidor num determinado grupo de sincronização, certifique-se de:
- Os carimbos de tempo para o Último Sincronia para o upload e o download são recentes.
- O estado é verde tanto para upload como para download.
- O campo Sync Activity mostra muito poucos ou nenhum ficheiro restante para sincronizar.
- O campo Dessincronização de Ficheiros Não sincronizado é 0 para o upload e o download.

# <a name="server"></a>[Servidor](#tab/server)
Veja as sessões de sincronização concluídas, que são marcadas por 9102 eventos no `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`registo de eventos de telemetria para cada servidor (no Espectador de Eventos, vá a ). 

1. Em qualquer servidor, você deve certificar-se de que as mais recentes sessões de upload e download concluídas com sucesso. Para tal, verifique se o HResult e o PerItemErrorCount são 0 para o upload e o download (o campo SyncDirection indica se uma determinada sessão é uma sessão de upload ou download). Note que se não vir uma sessão de sincronização recentemente concluída, é provável que esteja em curso uma sessão de sincronização, o que é de esperar se apenas adicionar ou modificar uma grande quantidade de dados.
2. Quando um servidor estiver totalmente atualizado com a nuvem e não tiver alterações para sincronizar em qualquer direção, verá sessões de sincronização vazias. Estes são indicados por eventos de upload e download em que todos os campos Sync* (SyncFileCount, SyncDirCount, SyncTombstoneCount e SyncSizeBytes) são zero, o que significa que não havia nada a sincronizar. Note que estas sessões de sincronização vazias podem não ocorrer em servidores de alta agitação, uma vez que há sempre algo novo para sincronizar. Se não houver atividade sincronizada, devem ocorrer a cada 30 minutos. 
3. Se todos os servidores estiverem atualizados com a nuvem, o que significa que as suas recentes sessões de upload e descarregamento são sessões de sincronização vazias, pode dizer com toda a certeza que o sistema no seu conjunto está sincronizado. 
    
Note que se tiver feito alterações diretamente na sua quota de ficheiros Azure, o Azure File Sync não detetará esta alteração até que a enumeração de alteração seja feita, o que acontece uma vez a cada 24 horas. É possível que um servidor diga que está atualizado com a nuvem quando, de facto, está a perder alterações recentes feitas diretamente na partilha de ficheiros Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Como posso ver se existem pastas ou ficheiros específicos que não estão a sincronizar?
Se o perItemErrorCount no servidor ou ficheiros não sincronizar no portal for superior a 0 para qualquer sessão de sincronização, isso significa que alguns itens estão a falhar na sincronização. Ficheiros e pastas podem ter características que os impedem de sincronizar. Estas características podem ser persistentes e requerem uma ação explícita para retomar a sincronização, por exemplo, removendo caracteres não suportados do nome do ficheiro ou da pasta. Também podem ser transitórios, o que significa que o ficheiro ou a pasta retomam automaticamente a sincronização; por exemplo, os ficheiros com pegas abertas retomam automaticamente a sincronização quando o ficheiro está fechado. Quando o motor Azure File Sync deteta tal problema, é produzido um registo de erro que pode ser analisado para listar os itens que atualmente não estão a sincronizar corretamente.

Para ver estes erros, execute o script **FileSyncErrorsReport.ps1** PowerShell (localizado no diretório de instalação do agente Do agente Dessincronização de ficheiros) para identificar ficheiros que não sincronizaram devido a cabos abertos, caracteres não suportados ou outros problemas. O campo ItemPath indica-lhe a localização do ficheiro em relação ao diretório de sincronização de raiz. Consulte a lista de erros de sincronização comuns abaixo para os passos de reparação.

> [!Note]  
> Se o script FileSyncErrorsReport.ps1 devoluções "Não foram encontrados erros de ficheiro" ou não enumera erros por item para o grupo de sincronização, a causa é:
>
>- Causa 1: A última sessão de sincronização concluída não teve erros por item. O portal deve ser atualizado em breve para mostrar 0 Ficheiros Não Sincronizados. 
>    - Verifique o [ID de evento 9102](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) no registo do evento Telemettry para confirmar que o PerItemErrorCount é 0. 
>
>- Causa 2: O registo do evento ItemResults no servidor embrulhado devido a demasiados erros por item e o registo do evento já não contém erros para este grupo de sincronização.
>    - Para evitar este problema, aumente o tamanho do registo do evento ItemResults. O registo do evento ItemResults pode ser encontrado em "Applications and Services Logs\Microsoft\FileSync\Agent" no Visualizador de Eventos. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Resolução de problemas por erros de sincronização de ficheiro/diretório
**Registo de Resultados do artigo - erros de sincronização por item**  

| HRESULT | HRESULT (decimal) | Cadeia do erro | Problema | Remediação |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | O ficheiro hierárquico no servidor não é acessível. Este problema ocorrerá se o ficheiro em camadas não tiver sido recuperado antes de eliminar um ponto final do servidor. | Para resolver este problema, consulte os [ficheiros Tiered não estarem acessíveis no servidor depois](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)de apagar um ponto final do servidor . |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | A alteração do ficheiro ou do diretório ainda não pode ser sincronizada porque uma pasta dependente ainda não está sincronizada. Este item sincronizar-se-á após a sincronização das alterações dependentes. | Nenhuma ação necessária. Se o erro persistir durante vários dias, utilize o script FileSyncErrorsReport.ps1 PowerShell para determinar por que razão a pasta dependente ainda não está sincronizada. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | A alteração do ficheiro ou do diretório ainda não pode ser sincronizada porque uma pasta dependente ainda não está sincronizada e a sessão de sincronização falhou. Este item sincronizar-se-á após a sincronização das alterações dependentes. | Nenhuma ação necessária. Se o erro persistir, investigue a falha da sessão de sincronização. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | O nome do ficheiro ou do diretório é inválido. | Mude o nome do ficheiro ou diretório em questão. Consulte [o manuseamento de caracteres não suportados](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | O nome do ficheiro ou do diretório é inválido. | Mude o nome do ficheiro ou diretório em questão. Consulte [o manuseamento de caracteres não suportados](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | O ficheiro não pode ser sincronizado porque está a ser utilizado. O ficheiro será sincronizado quando já não estiver em utilização. | Nenhuma ação necessária. O Azure File Sync cria uma imagem de VSS temporária uma vez por dia no servidor para sincronizar ficheiros que tenham cabos abertos. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | O ficheiro mudou, mas a alteração ainda não foi detetada por sincronização. O Sync recuperará depois de ser detetada esta alteração. | Nenhuma ação necessária. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | O ficheiro foi apagado e a sincronização não está ciente da alteração. | Nenhuma ação necessária. O Sync deixará de registar este erro assim que a deteção de alteração detetar que o ficheiro foi eliminado. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | A eliminação de um ficheiro ou diretório não pode ser sincronizada porque o item já foi apagado no destino e a sincronização não está ciente da alteração. | Nenhuma ação necessária. O Sync deixará de registar este erro assim que a deteção de alterações for executado no destino e a sincronização detetar o item foi eliminado. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | O ficheiro ou diretório foi ignorado, mas será sincronizado durante a próxima sessão de sincronização. Se este erro for reportado ao descarregar o item, o ficheiro ou o nome do diretório são mais do que provavelmente inválidos. | Não é necessária qualquer ação se este erro for reportado ao carregar o ficheiro. Se o erro for reportado ao descarregar o ficheiro, mude o nome do ficheiro ou do diretório em questão. Consulte [o manuseamento de caracteres não suportados](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | A criação de um ficheiro ou diretório não pode ser sincronizada porque o item já existe no destino e a sincronização não está ciente da alteração. | Nenhuma ação necessária. O Sync deixará de registar este erro assim que a deteção de alterações for no destino e a sincronização estiver ciente deste novo item. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Não é possível sincronizar o ficheiro porque o limite de partilha de ficheiros do Azure foi atingido. | Para resolver este problema, consulte a secção de armazenamento de partilha de [ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134351810) no guia de resolução de problemas. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | O ficheiro é encriptado por uma solução não suportada (como ntFS EFS). | Desencriptar o ficheiro e usar uma solução de encriptação suportada. Para obter uma lista de soluções suportadas, veja a secção [Soluções de encriptação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#encryption) no guia de planeamento. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | O ficheiro está localizado numa pasta de replicação apenas de leitura DFS-R. | O ficheiro está localizado numa pasta de replicação apenas de leitura DFS-R. O Azure File Sync não suporta pontos finais do servidor nas pastas de replicação só de leitura do DFS-R. Consulte o [guia de planeamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) para mais informações. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | O ficheiro tem um estado pendente de exclusão. | Nenhuma ação necessária. O ficheiro será eliminado assim que todas as pegas de ficheiro saem fechadas. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | O ficheiro não pode ser sincronizado porque as definições de firewall e rede virtual na conta de armazenamento estão ativadas e o servidor não tem acesso à conta de armazenamento. | Adicione o endereço IP do Servidor ou a rede virtual seguindo os passos documentados na secção de configuração de [firewall configure e de configurações de rede virtual](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) no guia de implementação. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | O ficheiro não pode ser sincronizado porque o tamanho do descritor de segurança excede o limite de 64 KiB. | Para resolver este problema, remova as entradas de controlo de acesso (ACE) no ficheiro para reduzir o tamanho do descritor de segurança. |
| 0x8000ff | -2147418113 | E_UNEXPECTED | O ficheiro não pode ser sincronizado devido a um erro inesperado. | Se o erro persistir durante vários dias, por favor abra um caso de suporte. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | O ficheiro não pode ser sincronizado porque está a ser utilizado. O ficheiro será sincronizado quando já não estiver em utilização. | Nenhuma ação necessária. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | O ficheiro foi alterado durante a sincronização, por isso tem de ser sincronizado novamente. | Nenhuma ação necessária. |
| 0x80070017 | -2147024873 | ERROR_CRC | O ficheiro não pode ser sincronizado devido a erro de CRC. Este erro pode ocorrer se um ficheiro hierárquico não tiver sido recolhido antes de apagar um ponto final do servidor ou se o ficheiro for corrupto. | Para resolver este problema, consulte os [ficheiros Tiered não estarem acessíveis no servidor depois](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) de eliminar um ponto final do servidor para remover ficheiros hierárquicos que estejam órfãos. Se o erro continuar a ocorrer após a remoção de ficheiros órfãos, execute o [chkdsk](https://docs.microsoft.com/windows-server/administration/windows-commands/chkdsk) no volume. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | O ficheiro não pode ser sincronizado porque o número máximo de ficheiros de conflito foi atingido. O Azure File Sync suporta 100 ficheiros de conflito por ficheiro. Para saber mais sobre conflitos de ficheiros, consulte o Azure File Sync [FAQ](https://docs.microsoft.com/azure/storage/files/storage-files-faq#afs-conflict-resolution). | Para resolver esta questão, reduza o número de ficheiros de conflito. O ficheiro sincronizará assim que o número de ficheiros de conflito for inferior a 100. |

#### <a name="handling-unsupported-characters"></a>Manuseamento de caracteres não suportados
Se o script **FileSyncErrorsReport.ps1** PowerShell mostrar falhas devido a caracteres não suportados (código de erro 0x8007007b ou 0x80c80255), deve remover ou renomear os caracteres com falhas dos respetivos nomes de ficheiros. A PowerShell provavelmente imprimirá estes caracteres como pontos de interrogação ou retângulos vazios, uma vez que a maioria destes caracteres não tem codificação visual padrão. A Ferramenta de [Avaliação](storage-sync-files-planning.md#evaluation-cmdlet) pode ser usada para identificar caracteres que não são suportados.

A tabela abaixo contém todos os caracteres unicódigo Azure File Sync ainda não suporta.

| Conjunto de carateres | Contagem de caracteres |
|---------------|-----------------|
| <ul><li>0x00000009D (comando do sistema operativo osc)</li><li>0x00000090 (cadeia de controlo de dispositivos dcs)</li><li>0x0000008F (ss3 single shift three)</li><li>0x00000081 (predefinição de octeto superior)</li><li>0x00000007F (del delete)</li><li>0x00000008D (feed de linha inversa ri)</li></ul> | 6 |
| 0x0000FD0 - 0x0000FDEF (formulários de apresentação árabe-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (especiais) | 16 |
| <ul><li>0x0001FFFE - 0x0001FF = 2 (não-carácter)</li><li>0x0002FFFE - 0x0002FF = 2 (não-carácter)</li><li>0x0003FFFE - 0x0003FF = 2 (não-carácter)</li><li>0x0004FFFE - 0x0004FF = 2 (não-carácter)</li><li>0x0005FFFE - 0x0005FF = 2 (não-carácter)</li><li>0x0006FFFE - 0x0006FF = 2 (não-carácter)</li><li>0x0007FFFE - 0x0007FF = 2 (não-carácter)</li><li>0x0008FFFE - 0x0008FF = 2 (não-carácter)</li><li>0x0009FFFE - 0x0009FF = 2 (não-carácter)</li><li>0x000AFFFE - 0x000AFF = 2 (não-carácter)</li><li>0x000BFFFE - 0x000BFFFF = 2 (não-carácter)</li><li>0x000CFFFE - 0x000CFF = 2 (não-carácter)</li><li>0x000DFFFE - 0x000DFF = 2 (não-carácter)</li><li>0x000EFFFE - 0x000EFFFF = 2 (indefinido)</li><li>0x000FFFfE - 0x000FFF = 2 (área de utilização privada suplementar)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FF | 2 |

### <a name="common-sync-errors"></a>Erros de sincronização comuns
<a id="-2147023673"></a>**A sessão de sincronização foi cancelada.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimal)** | -2147023673 | 
| **Cadeia do erro** | ERROR_CANCELLED |
| **Reparação necessária** | Não |

As sessões de sincronização podem falhar por várias razões, incluindo o reinício ou atualização do servidor, instantâneos VSS, etc. Embora este erro pareça necessitar de acompanhamento, é seguro ignorar este erro a menos que persista durante um período de várias horas.

<a id="-2147012889"></a>**Não foi possível estabelecer uma ligação com o serviço.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (decimal)** | -2147012889 | 
| **Cadeia do erro** | WININET_E_NAME_NOT_RESOLVED |
| **Reparação necessária** | Sim |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**O pedido do utilizador foi acelerado pelo serviço.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (decimal)** | -2134376372 |
| **Cadeia do erro** | ECS_E_USER_REQUEST_THROTTLED |
| **Reparação necessária** | Não |

Não é necessária qualquer ação; o servidor tentará novamente. Se este erro persistir durante várias horas, crie um pedido de suporte.

<a id="-2134364043"></a>**O Sync está bloqueado até que a deteção de alterações complete a restauração do poste**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (decimal)** | -2134364043 |
| **Cadeia do erro** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Reparação necessária** | Não |

nenhuma ação necessária. Quando uma partilha de ficheiros ou ficheiros (cloud endpoint) é restaurada utilizando o Azure Backup, a sincronização é bloqueada até que a deteção de alterações esteja concluída na partilha de ficheiros Azure. A deteção de alterações é executada imediatamente após o restauro ser concluído e a duração é baseada no número de ficheiros na partilha de ficheiros.

<a id="-2147216747"></a>**O Sync falhou porque a base de dados de sincronização foi descarregada.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (decimal)** | -2147216747 |
| **Cadeia do erro** | SYNC_E_METADATA_INVALID_OPERATION |
| **Reparação necessária** | Não |

Este erro normalmente ocorre quando uma aplicação de cópia de segurança cria um instantâneo do VSS e a base de dados de sincronização é descarregada. Se este erro persistir durante várias horas, crie um pedido de suporte.

<a id="-2134364065"></a>**O Sync não consegue aceder à partilha de ficheiros Azure especificada no ponto final da nuvem.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimal)** | -2134364065 |
| **Cadeia do erro** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Reparação necessária** | Sim |

Este erro ocorre porque o agente do Azure File Sync não consegue aceder à partilha de ficheiros do Azure, o que pode dever-se à partilha de ficheiros do Azure ou ao anfitrião da conta de armazenamento já não existir. Pode resolver este erro ao realizar os passos seguintes:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Certifique-se de que a partilha de ficheiros Azure existe.](#troubleshoot-azure-file-share)
3. [Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento.](#troubleshoot-rbac)
4. [Verifique se as definições da rede virtual e da firewall na conta de armazenamento estão configuradas corretamente (se ativadas)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**A Sincronização falhou porque o pedido não está autorizado a realizar esta operação.**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (decimal)** | -2134351804 |
| **Cadeia do erro** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Reparação necessária** | Sim |

Este erro ocorre porque o agente Dessincronização de ficheiros Azure não está autorizado a aceder à parte do ficheiro Azure. Pode resolver este erro ao realizar os passos seguintes:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Certifique-se de que a partilha de ficheiros Azure existe.](#troubleshoot-azure-file-share)
3. [Verifique se as definições da rede virtual e da firewall na conta de armazenamento estão configuradas corretamente (se ativadas)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**O nome da conta de armazenamento utilizado não pôde ser resolvido.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (decimal)** | -2134364064 |
| **Cadeia do erro** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Reparação necessária** | Sim |

1. Verifique se pode resolver o nome DNS de armazenamento a partir do servidor.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
3. [Verifique se as definições da rede virtual e da firewall na conta de armazenamento estão configuradas corretamente (se ativadas)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Ocorreu um erro desconhecido ao aceder à conta de armazenamento.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (decimal)** | -2134364022 |
| **Cadeia do erro** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Reparação necessária** | Sim |

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se as definições da rede virtual e da firewall na conta de armazenamento estão configuradas corretamente (se ativadas)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**A sincronização falhou devido à conta de armazenamento bloqueada.**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (decimal)** | -2134364014 |
| **Cadeia do erro** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Reparação necessária** | Sim |

Este erro ocorre porque a conta de armazenamento tem um bloqueio de [recursos](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources)apenas para leitura . Para resolver este problema, remova o bloqueio de recursos apenas para leitura na conta de armazenamento. 

<a id="-1906441138"></a>**Sync falhou devido a um problema na base de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (decimal)** | -1906441138 |
| **Cadeia do erro** | JET_errWriteConflict |
| **Reparação necessária** | Sim |

Este erro ocorre quando existe um problema com a base de dados interna utilizada pelo Azure File Sync. Quando este problema ocorrer, crie um pedido de apoio e entraremos em contato consoá-lo para ajudá-lo a resolver este problema.

<a id="-2134364053"></a>**A versão do agente Dosídeo de Ficheiros Azure instalada no servidor não é suportada.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (decimal)** | -2134364053 |
| **Cadeia do erro** | ECS_E_AGENT_VERSION_BLOCKED |
| **Reparação necessária** | Sim |

Este erro ocorrerá se a versão do Azure File Sync instalada no servidor não for suportada. Para resolver este problema, [atualize]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) para uma [versão de agente suportado]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Atingiu o limite de armazenamento de ações de ficheiros Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (decimal)** | -2134351810 |
| **Cadeia do erro** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Reparação necessária** | Sim |

Este erro ocorre quando o limite de armazenamento da partilha de ficheiros do Azure é atingido, o que poderá acontecer se uma quota for aplicada a uma partilha de ficheiros do Azure ou se a utilização exceder os limites de uma partilha de ficheiros do Azure. Para mais informações, consulte os [limites atuais para uma partilha de ficheiros Azure](storage-files-scale-targets.md).

1. Navegue para o grupo de sincronização dentro do Serviço de Sincronização de Armazenamento.
2. Selecione o ponto final da nuvem dentro do grupo de sincronização.
3. Note o nome da partilha de ficheiros Azure no painel aberto.
4. Selecione a conta de armazenamento ligada. Se este link falhar, a conta de armazenamento referenciada foi removida.

    ![Uma imagem mostrando o painel de detalhes do ponto final da nuvem com um link para a conta de armazenamento.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Selecione **Ficheiros** para visualizar a lista de partilhas de ficheiros.
6. Clique nos três pontos no final da linha para a partilha de ficheiros Azure referenciada pelo ponto final da nuvem.
7. Verifique se a **Utilização** está abaixo da**Quota**. Nota a menos que tenha sido especificada uma quota alternativa, a quota corresponderá ao [tamanho máximo da parte do ficheiro Azure](storage-files-scale-targets.md).

    ![Uma imagem do ficheiro Azure partilha propriedades.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Se a partilha de ficheiros estiver cheia e não tiver sido definida uma quota, uma forma possível de resolver este problema será tornar cada subpasta do ponto final do servidor atual no seu próprio ponto final do servidor nos seus próprios grupos de sincronização separados. Desta forma, cada subpasta será sincronizada com as partilhas de ficheiros do Azure individuais.

<a id="-2134351824"></a>**A parte do ficheiro Azure não pode ser encontrada.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (decimal)** | -2134351824 |
| **Cadeia do erro** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Reparação necessária** | Sim |

Este erro ocorre quando a partilha de ficheiros do Azure não está acessível. Para resolver os problemas:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Certifique-se de que a partilha de ficheiros Azure existe.](#troubleshoot-azure-file-share)

Se a parte do ficheiro Azure foi eliminada, é necessário criar uma nova partilha de ficheiros e, em seguida, recriar o grupo de sincronização. 

<a id="-2134364042"></a>**O Sync é interrompido enquanto esta subscrição do Azure está suspensa.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (decimal)** | -2134364042 |
| **Cadeia do erro** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Reparação necessária** | Sim |

Este erro ocorre quando a subscrição do Azure é suspensa. A sincronização será reativada quando a subscrição do Azure for restaurada. Vê porque é que a [minha subscrição azure está desativada e como a reativo](../../cost-management-billing/manage/subscription-disabled.md) para mais informações?

<a id="-2134364052"></a>**A conta de armazenamento tem uma firewall ou redes virtuais configuradas.**  

| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (decimal)** | -2134364052 |
| **Cadeia do erro** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Reparação necessária** | Sim |

Este erro ocorre quando a partilha de ficheiros do Azure está inacessível devido a uma firewall da conta de armazenamento ou devido à conta de armazenamento pertencer a uma rede virtual. Verifique se as definições de firewall e rede virtual na conta de armazenamento estão corretamente configuradas. Para mais informações, consulte [configurar a firewall e as definições de rede virtual](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**Sync falhou devido a um problema na base de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimal)** | -2134375911 |
| **Cadeia do erro** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Reparação necessária** | Não |

Este erro resolve-se normalmente de forma autónoma e poderá ocorrer se existir:

* Um elevado número de alterações de ficheiros nos servidores do grupo de sincronização.
* Um grande número de erros em ficheiros e diretórios individuais.

Se este erro persistir por mais de algumas horas, crie um pedido de apoio e entraremos em contacto consigo para o ajudar a resolver este problema.

<a id="-2146762487"></a>**O servidor não conseguiu estabelecer uma ligação segura. O serviço de nuvem recebeu um certificado inesperado.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (decimal)** | -2146762487 |
| **Cadeia do erro** | CERT_E_UNTRUSTEDROOT |
| **Reparação necessária** | Sim |

Este erro pode acontecer se a sua organização estiver a usar um proxy de terminação de TLS ou se uma entidade maliciosa estiver a intercetar o tráfego entre o seu servidor e o serviço Dessincronização de Ficheiros Azure. Se tem a certeza de que isso é esperado (porque a sua organização está a usar um representante de terminação de TLS), ignora a verificação de certificadocom uma sobreposição de registo.

1. Crie o valor do registo SkipCheckingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Reiniciar o serviço de sincronização no servidor registado.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ao definir este valor de registo, o agente Dessincronização de Ficheiros Azure aceitará qualquer certificado TLS/SSL de confiança local ao transferir dados entre o servidor e o serviço de nuvem.

<a id="-2147012894"></a>**Não foi possível estabelecer uma ligação com o serviço.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (decimal)** | -2147012894 |
| **Cadeia do erro** | WININET_E_TIMEOUT |
| **Reparação necessária** | Sim |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**A sincronização falhou devido a um problema de autenticação.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (decimal)** | -2134375680 |
| **Cadeia do erro** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Reparação necessária** | Sim |

Este erro ocorre normalmente porque a hora do servidor está incorreta. Se o servidor estiver a funcionar numa máquina virtual, verifique se a hora do hospedeiro está correta.

<a id="-2134364040"></a>**A sincronização falhou devido à expiração do certificado.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (decimal)** | -2134364040 |
| **Cadeia do erro** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Reparação necessária** | Sim |

Este erro ocorre porque o certificado utilizado para a autenticação expirou.

Para confirmar se o certificado expirou, execute os seguintes passos:  
1. Abra o snap-in do MMC dos Certificados, selecione conta de computador e navegue para Certificados (Computador Local)\Certificados Pessoais\.
2. Verifique se o certificado de autenticação do cliente está caducado.

Se o certificado de autenticação de cliente tiver expirado, efetue os seguintes passos para resolver o problema:

1. Verifique se a versão 4.0.1.0 do agente do Ficheiro Azure está instalada.
2. Execute o seguinte comando do PowerShell no servidor: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**A Sincronização falhou devido ao certificado de autenticação não encontrado.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (decimal)** | -2134375896 |
| **Cadeia do erro** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Reparação necessária** | Sim |

Este erro ocorre porque o certificado utilizado para a autenticação não foi localizado.

Para resolver este problema, realize os passos seguintes:

1. Verifique se a versão 4.0.1.0 do agente do Ficheiro Azure está instalada.
2. Execute o seguinte comando do PowerShell no servidor: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Sync falhou devido à identidade de autenticação não encontrada.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (decimal)** | -2134364039 |
| **Cadeia do erro** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Reparação necessária** | Sim |

Este erro ocorre porque a eliminação do ponto final do servidor falhou e o ponto final está agora num estado parcialmente eliminado. Para resolver este problema, repita a eliminação do ponto final do servidor.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**O volume onde o ponto final do servidor está localizado é baixo no espaço do disco.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimal)** | -1906441711 |
| **Cadeia do erro** | JET_errLogDiskFull |
| **Reparação necessária** | Sim |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (decimal)** | -2134375654 |
| **Cadeia do erro** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Reparação necessária** | Sim |

Este erro ocorre porque o volume ficou cheio. Este erro geralmente ocorre porque os ficheiros fora do ponto final do servidor estão a utilizar espaço no volume. Liberte espaço no volume adicionando pontos finais adicionais do servidor, movendo ficheiros para um volume diferente ou aumentando o tamanho do volume em que o ponto final do servidor está.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**O serviço ainda não está pronto para sincronizar com este ponto final do servidor.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (decimal)** | -2134364145 |
| **Cadeia do erro** | ECS_E_REPLICA_NOT_READY |
| **Reparação necessária** | Não |

Este erro ocorre porque o ponto final da nuvem foi criado com conteúdo já existente na partilha de ficheiros Azure. O Azure File Sync deve digitalizar a parte do ficheiro Azure para todos os conteúdos antes de permitir que o ponto final do servidor prossiga com a sua sincronização inicial.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Sync falhou devido a problemas com muitos ficheiros individuais.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimal)** | -2134375877 |
| **Cadeia do erro** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Reparação necessária** | Sim |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimal)** | -2134375908 |
| **Cadeia do erro** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Reparação necessária** | Sim |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimal)** | -2134375853 |
| **Cadeia do erro** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Reparação necessária** | Sim |

Nos casos em que existam muitos erros de sincronização de ficheiros, as sessões de sincronização podem começar a falhar. <!-- To troubleshoot this state, see [Troubleshooting per file/directory sync errors]().-->

> [!NOTE]
> O Azure File Sync cria uma imagem de VSS temporária uma vez por dia no servidor para sincronizar ficheiros que tenham cabos abertos.

<a id="-2134376423"></a>**O Sync falhou devido a um problema no caminho final do servidor.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (decimal)** | -2134376423 |
| **Cadeia do erro** | ECS_E_SYNC_INVALID_PATH |
| **Reparação necessária** | Sim |

Certifique-se de que o caminho existe, está num volume NTFS local, e não é um ponto de reparso ou ponto final do servidor existente.

<a id="-2134375817"></a>**Sync falhou porque a versão do controlador de filtro não é compatível com a versão do agente**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (decimal)** | -2134375817 |
| **Cadeia do erro** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Reparação necessária** | Sim |

Este erro ocorre porque a versão do controlador de filtro de Arrumo na Cloud (StorageSync.sys) carregada não é compatível com o serviço do Agente de Sincronização de Armazenamento (FileSyncSvc). Se o agente do Azure File Sync tiver sido atualizado, reinicie o servidor para concluir a instalação. Se o erro persistir, desinstale o agente, reinicie o servidor e reinstale o agente do Azure File Sync.

<a id="-2134376373"></a>**O serviço não está disponível.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (decimal)** | -2134376373 |
| **Cadeia do erro** | ECS_E_SERVICE_UNAVAILABLE |
| **Reparação necessária** | Não |

Este erro ocorre porque o serviço Azure File Sync está indisponível. Este erro será resolvido automaticamente quando o serviço do Azure File Sync estiver novamente disponível.

<a id="-2146233088"></a>**Sync falhou devido a uma exceção.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (decimal)** | -2146233088 |
| **Cadeia do erro** | COR_E_EXCEPTION |
| **Reparação necessária** | Não |

Este erro ocorre porque a sincronização falhou devido a uma exceção. Se o erro persistir durante várias horas, por favor crie um pedido de apoio.

<a id="-2134364045"></a>**Sync falhou porque a conta de armazenamento falhou noutra região.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (decimal)** | -2134364045 |
| **Cadeia do erro** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Reparação necessária** | Sim |

Este erro ocorreu porque a conta de armazenamento efetuou uma ativação pós-falha para outra região. O Azure File Sync não suporta a funcionalidade de ativação pós-falha da conta de armazenamento. As contas de armazenamento que contêm partilhas de ficheiros do Azure e estão a ser utilizadas como pontos finais da cloud no Azure File Sync não devem efetuar a ativação pós-falha. Se a fizer, fará com que a sincronização deixe de funcionar e poderá também causar perdas de dados inesperadas em caso de ficheiros com novo escalão. Para resolver este problema, mova a conta de armazenamento para a região primária.

<a id="-2134375922"></a>**Sync falhou devido a um problema transitório com a base de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimal)** | -2134375922 |
| **Cadeia do erro** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Reparação necessária** | Não |

Esse erro ocorre devido a um problema interno na base de dados de sincronização. Este erro será resolvido automaticamente quando a tentar executar novamente a sincronização. Se este erro continuar por um longo período de tempo, crie um pedido de apoio e entraremos em contacto consigo para ajudá-lo a resolver este problema.

<a id="-2134364024"></a>**Sync falhou devido a mudança no inquilino do Diretório Ativo azure**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (decimal)** | -2134364024 | 
| **Cadeia do erro** | ECS_E_INVALID_AAD_TENANT |
| **Reparação necessária** | Sim |

Este erro ocorre porque o Azure File Sync atualmente não suporta mover a subscrição para um inquilino do Azure Active Directory diferente.
 
Para resolver este problema, realize uma das opções seguintes:

- **Opção 1 (recomendado)**: Mover a subscrição de volta para o inquilino original do Diretório Ativo Azure
- **Opção 2**: Eliminar e recriar o grupo de sincronização atual. Se o arrumo na cloud tiver sido ativado no ponto final do servidor, elimine o grupo de sincronização e, em seguida, execute os passos documentados na [Secção Arrumo na Cloud]( https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) para remover os ficheiros em camadas órfãos antes de recriar os grupos de sincronização. 

<a id="-2134364010"></a>**Sync falhou devido à firewall e à exceção da rede virtual não configurada**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (decimal)** | -2134364010 | 
| **Cadeia do erro** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Reparação necessária** | Sim |

Este erro ocorre se as definições de firewall e rede virtual estiverem ativadas na conta de armazenamento e a exceção "Permitir que os serviços da Microsoft fidedignos acedam a esta conta de armazenamento". Para resolver este problema, siga os passos documentados na secção [Configurar as definições da firewall e da rede virtual](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) no guia de implementação.

<a id="-2147024891"></a>**O Sync falhou porque as permissões na pasta de Informação de Volume do Sistema estão incorretas.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (decimal)** | -2147024891 |
| **Cadeia do erro** | ERROR_ACCESS_DENIED |
| **Reparação necessária** | Sim |

Este erro poderá ocorrer se a conta NT AUTHORITY\SYSTEM não tiver permissões para a pasta Informações de Volume do Sistema no volume onde o ponto final do servidor está localizado. Nota, se os ficheiros individuais não estiverem a sincronizar com ERROR_ACCESS_DENIED, execute os passos documentados na secção de erros de sincronização de [ficheiro/diretório.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshooting-per-filedirectory-sync-errors)

Para resolver este problema, realize os passos seguintes:

1. Transfira a ferramenta [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec).
2. Executar o seguinte comando a partir de um pedido de comando elevado para lançar um pedido de comando usando a conta do sistema: **PsExec.exe -i-s -s-d cmd** 
3. Na linha de comandos em execução na conta de sistema, execute o seguinte comando para confirmar que a conta NT AUTHORITY\SYSTEM não tem acesso à pasta Informações de Volume de Sistema: **cacls “"letra da unidade:\system volume information” /T /C**
4. Se a conta NT AUTHORITY\SYSTEM não tiver acesso à pasta Informações de Volume do Sistema, execute o seguinte comando: **cacls “letra da unidade:\system volume information” /T /E /G “NT AUTHORITY\SYSTEM:F”**
    - Se o passo 4 falhar com acesso negado, execute o seguinte comando para assumir a propriedade da pasta Informações de Volume do Sistema e, em seguida, repita o passo 4: **takeown /A /R /F “letra da unidade:\System Volume Information”**

<a id="-2134375810"></a>**A Sync falhou porque a parte do ficheiro Azure foi eliminada e recriada.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (decimal)** | -2134375810 |
| **Cadeia do erro** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Reparação necessária** | Sim |

Este erro ocorre porque o Azure File Sync não suporta a eliminação e a recriação de uma partilha de ficheiros do Azure no mesmo grupo de sincronização. 

Para resolver este problema, elimine e recrie o grupo de sincronização. Para tal, realize os seguintes passos:

1. Elimine todos os pontos finais do servidor no grupo de sincronização.
2. Apague o ponto final da nuvem. 
3. Apague o grupo de sincronização.
4. Se o tiering de nuvem estiver ativado num ponto final do servidor, elimine os ficheiros hierárquicos órfãos no servidor, executando os passos documentados [nos ficheiros Tiered, não estão acessíveis no servidor após a eliminação](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) de uma secção de ponto final do servidor.
5. Recrie o grupo de sincronização.

<a id="-2145844941"></a>**Sync falhou porque o pedido HTTP foi redirecionado**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (decimal)** | -2145844941 |
| **Cadeia do erro** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Reparação necessária** | Sim |

Este erro ocorre porque o Azure File Sync não suporta a reorientação http (código de estado 3xx). Para resolver este problema, desative o redirecionamento http no seu servidor de procuração ou dispositivo de rede.

<a id="-2134364027"></a>**O tempo limite ocorreu durante a transferência de dados offline, mas ainda está em curso.**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (decimal)** | -2134364027 |
| **Cadeia do erro** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Reparação necessária** | Não |

Este erro ocorre quando uma operação de ingestão de dados excede o tempo limite. Este erro pode ser ignorado se a sincronização estiver a progredir (AppliedItemCount é superior a 0). Vêcomo [posso monitorizar o progresso de uma sessão de sincronização atual?](#how-do-i-monitor-the-progress-of-a-current-sync-session)

### <a name="common-troubleshooting-steps"></a>Passos comuns de resolução de problemas
<a id="troubleshoot-storage-account"></a>**Verifique se a conta de armazenamento existe.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navegue para o grupo de sincronização dentro do Serviço de Sincronização de Armazenamento.
2. Selecione o ponto final da nuvem dentro do grupo de sincronização.
3. Note o nome da partilha de ficheiros Azure no painel aberto.
4. Selecione a conta de armazenamento ligada. Se este link falhar, a conta de armazenamento referenciada foi removida.
    ![Uma imagem mostrando o painel de detalhes do ponto final da nuvem com um link para a conta de armazenamento.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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

<a id="troubleshoot-azure-file-share"></a>**Certifique-se de que a partilha de ficheiros Azure existe.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Clique em **visão geral** na tabela de conteúdos à esquerda para voltar à página principal da conta de armazenamento.
2. Selecione **Ficheiros** para visualizar a lista de partilhas de ficheiros.
3. Verifique se a parte de ficheiro referenciada pelo ponto final da nuvem aparece na lista de ações de ficheiros (deve ter notado isso no passo 1 acima).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
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

<a id="troubleshoot-rbac"></a>**Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Clique no controlo de **acesso (IAM)** na tabela de conteúdos à esquerda.
1. Clique no separador **de atribuições de Role** para a lista dos utilizadores e aplicações *(diretores*de serviço) que têm acesso à sua conta de armazenamento.
1. Verifique o **Microsoft.StorageSync** ou **Hybrid File Sync Service** (nome antigo da aplicação) aparece na lista com a função Reader e Data **Access.** 

    ![Uma imagem do serviço de serviço de sincronização de ficheiros híbrido sintetizador no separador de controlo de acesso da conta de armazenamento](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Se o **Microsoft.StorageSync** ou **Hybrid File Sync Service** não aparecerem na lista, execute os seguintes passos:

    - Clique em **Adicionar**.
    - No campo **Role,** selecione **Reader e Data Access**.
    - No campo **Select,** digite **Microsoft.StorageSync,** selecione a função e clique em **Guardar**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Como posso impedir que os utilizadores criem ficheiros que contenham caracteres não suportados no servidor?
Pode utilizar os Ficheiros do Gestor de Recursos do [Servidor de Ficheiros (FSRM)](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) para bloquear ficheiros com caracteres não suportados nos seus nomes a partir da criação no servidor. Podes ter de fazer isto usando o PowerShell, já que a maioria dos personagens não suportados não são imprimíveis e por isso precisas de lançar as suas representações hexadecimais como personagens primeiro.

Primeiro crie um Grupo de Ficheiros FSRM utilizando o [cmdlet New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Este exemplo define o grupo para conter apenas dois dos caracteres não suportados, mas você pode incluir o número de caracteres necessários no seu grupo de ficheiros.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Depois de definir um Grupo de Ficheiros FSRM, pode criar um ecrã de ficheiro SSRM utilizando o cmdlet New-FsrmFileScreen.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Note que os ecrãs de ficheiros só devem ser utilizados para bloquear a criação de caracteres não suportados pelo Azure File Sync. Se os ecrãs de ficheiros forem utilizados noutros cenários, o sync tentará continuamente descarregar os ficheiros da partilha de ficheiros Azure para o servidor e será bloqueado devido ao ecrã de ficheiros, resultando em elevada suver. 

## <a name="cloud-tiering"></a>Disposição em camadas na cloud 
Existem dois caminhos para falhas no tiering de nuvens:

- Os ficheiros podem falhar na nivelado, o que significa que o Azure File Sync tenta, sem sucesso, nivelado um ficheiro para ficheiros Azure.
- Os ficheiros podem não conseguir a recolha, o que significa que o filtro de ficheiros Do Ficheiro Sincron (StorageSync.sys) não consegue descarregar dados quando um utilizador tenta aceder a um ficheiro que foi nivelado.

Existem duas classes principais de falhas que podem acontecer através de qualquer caminho de falha:

- Falhas no armazenamento em nuvem
    - *Problemas transitórios de disponibilidade*do serviço de armazenamento. Para mais informações, consulte o Acordo de [Nível de Serviço (SLA) para armazenamento de Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Partilha de ficheiros Azure inacessível.* Esta falha ocorre normalmente quando elimina a partilha de ficheiros Azure quando ainda é um ponto final em nuvem num grupo de sincronização.
    - *Conta de armazenamento inacessível.* Esta falha ocorre normalmente quando elimina a conta de armazenamento enquanto ainda tem uma partilha de ficheiros Azure que é um ponto final em nuvem num grupo de sincronização. 
- Falhas no servidor 
  - O filtro do sistema de *ficheiros Azure File Sync (StorageSync.sys) não está carregado*. Para responder aos pedidos de tiering/recolha, o filtro de ficheiros Do Sistema de Ficheiros Azure Sync deve ser carregado. O filtro não está a ser carregado pode acontecer por várias razões, mas a razão mais comum é que um administrador o descarregou manualmente. O filtro de ficheiros Do Ficheiro Sincronia Azure deve ser carregado sempre para que o Azure File Sync funcione corretamente.
  - *Ponto de reparse desaparecido, corrupto ou quebrado*de outra forma. Um ponto de reparse é uma estrutura especial de dados num ficheiro que consiste em duas partes:
    1. Uma etiqueta reparse, que indica ao sistema operativo que o filtro de ficheiros Do Ficheiro Sincron (StorageSync.sys) pode ter de fazer alguma ação em IO no ficheiro. 
    2. Os dados reparse, que indicam para o sistema de ficheiros filtram o URI do ficheiro no ponto final da nuvem associada (a parte do ficheiro Azure). 
        
       A forma mais comum de um ponto de reparso poder ser corrompido é se um administrador tentar modificar a etiqueta ou os seus dados. 
  - *Problemas de conectividade de rede.* Para tierar ou recordar um ficheiro, o servidor deve ter conectividade com a Internet.

As seguintes secções indicam como resolver problemas de nível de nuvem e determinar se um problema é um problema de armazenamento na nuvem ou um problema de servidor.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Como monitorizar a atividade de camadas num servidor  
Para monitorizar a atividade de tiering num servidor, utilize o Event ID 9003, 9016 e 9029 no registo de eventos da Telemettry (localizado em Aplicações e Serviços\Microsoft\FileSync\Agent no Espectador de Eventos).

- O ID do evento 9003 fornece a distribuição de erros para um ponto final do servidor. Por exemplo, Contagem total de erros, ErrorCode, etc. Nota, um evento é registado por código de erro.
- O ID do evento 9016 fornece resultados fantasmagóricas para um volume. Por exemplo, o espaço livre por cento é, número de ficheiros fantasmagórico em sessão, número de ficheiros não conseguiu fantasma, etc.
- O ID do evento 9029 fornece informações de sessão fantasmagórica para um ponto final do servidor. Por exemplo, número de ficheiros tentados na sessão, Número de ficheiros hierarquizados na sessão, Número de ficheiros já nividaizados, etc.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Como monitorizar a atividade de recuperação num servidor
Para monitorizar a atividade de recolha num servidor, utilize o Event ID 9005, 9006, 9009 e 9059 no registo de eventos da Telemetria (localizado em Aplicações e Serviços\Microsoft\FileSync\Agent no Espectador de Eventos).

- O ID do evento 9005 proporciona a fiabilidade da recuperação de um ponto final do servidor. Por exemplo, total de ficheiros únicos acedidos, total de ficheiros únicos com acesso falhado, etc.
- O ID do evento 9006 fornece a distribuição de erros de recolha para um ponto final do servidor. Por exemplo, Total De pedidos falhados, ErrorCode, etc. Nota, um evento é registado por código de erro.
- O ID do evento 9009 fornece informações de sessão de recolha para um ponto final do servidor. Por exemplo, DuraçõesSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, etc.
- O ID do evento 9059 fornece a distribuição de recolha de aplicações para um ponto final do servidor. Por exemplo, ShareId, Nome de Aplicação e TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Como resolver ficheiros que não conseguem ser nividamente
Se os ficheiros não forem nividares aos Ficheiros Azure:

1. No Observador de Eventos, reveja os registos de eventos de telemetria, operacional e diagnóstico, localizados em Aplicações e Serviços\Microsoft\FileSync\Agent. 
   1. Verifique se os ficheiros existem na partilha de ficheiros Azure.

      > [!NOTE]
      > Um ficheiro deve ser sincronizado com uma parte de ficheiro Azure antes de ser nivelado.

   2. Verifique se o servidor tem conectividade com a Internet. 
   3. Verifique se os controladores de filtro Sincronia de Ficheiros Azure (StorageSync.sys e StorageSyncGuard.sys) estão em execução:
       - Num pedido de comando `fltmc`elevado, corra. Verifique se os controladores de filtro de ficheiros StorageSync.sys e StorageSyncGuard.sys estão listados.

> [!NOTE]
> Um Id de evento 9003 é registado uma vez por hora no registo de eventos da Telemetria se um ficheiro não conseguir tier (um evento é registado por código de erro). Verifique os erros de tiering e a secção de [reparação](#tiering-errors-and-remediation) para ver se as medidas de reparação estão listadas para o código de erro.

### <a name="tiering-errors-and-remediation"></a>Erros de tiering e remediação

| HRESULT | HRESULT (decimal) | Cadeia do erro | Problema | Remediação |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | O ficheiro falhou na nivelamento porque está a ser utilizado. | Nenhuma ação necessária. O ficheiro será niveado quando já não estiver a ser utilizado. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | O ficheiro falhou na nivelamento porque é excluído por sincronização. | Nenhuma ação necessária. Os ficheiros na lista de exclusão de sincronização não podem ser nividados. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | O ficheiro falhou em ser nivelado porque não foi encontrado no servidor. | Nenhuma ação necessária. Se o erro persistir, verifique se o ficheiro existe no servidor. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | O ficheiro não foi nivelado porque foi eliminado na parte de ficheiro sinuosa do Azure. | Nenhuma ação necessária. O ficheiro deve ser eliminado no servidor quando a próxima sessão de sincronização de descarregamento for reparada. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | O ficheiro não foi niveado devido a um problema de rede. | Nenhuma ação necessária. Se o erro persistir, verifique a conectividade da rede com a partilha de ficheiros Azure. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | O ficheiro não foi niveado devido a um problema de rede. | Nenhuma ação necessária. Se o erro persistir, verifique a conectividade da rede com a partilha de ficheiros Azure. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | O ficheiro não foi niveado devido ao erro negado pelo acesso. Este erro pode ocorrer se o ficheiro estiver localizado numa pasta de replicação de leitura DFS-R. | O Azure File Sync não suporta pontos finais do servidor nas pastas de replicação só de leitura do DFS-R. Consulte o [guia de planeamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#distributed-file-system-dfs) para mais informações. |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | O ficheiro não foi niveado devido a um problema de rede. | Nenhuma ação necessária. Se o erro persistir, verifique a conectividade da rede com a partilha de ficheiros Azure. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | O ficheiro não foi nivealporque o tamanho do ficheiro é inferior ao tamanho suportado. | Se a versão do agente for inferior a 9.0, o tamanho mínimo de ficheiro suportado é de 64kb. Se a versão do agente for 9.0 e mais recente, o tamanho mínimo de ficheiro suportado baseia-se no tamanho do cluster do sistema de ficheiros (tamanho de cluster do sistema de ficheiros duplo). Por exemplo, se o tamanho do cluster do sistema de ficheiros for de 4kb, o tamanho mínimo do ficheiro é de 8kb. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | O ficheiro não foi nivelado devido a um problema de armazenamento do Azure. | Se o erro persistir, abra um pedido de apoio. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | O ficheiro não foi niveal porque foi recolhido ao mesmo tempo. | Nenhuma ação necessária. O ficheiro será niveado quando a recolha estiver concluída e o ficheiro já não estiver a ser utilizado. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | O ficheiro não foi nivelado porque não se sincronizou com a parte do ficheiro Azure. | Nenhuma ação necessária. O ficheiro será nivelado uma vez que tenha sincronizado com a parte de ficheiro Azure. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | O ficheiro não foi nivelheiro porque o controlador de filtro de nível de nuvem (storagesync.sys) não está a funcionar. | Para resolver este problema, abra um pedido de comando elevado e execute o seguinte comando:`fltmc load storagesync`<br>Se o controlador de filtro de armazenamento sincronizado não carregar ao executar o comando fltmc, desinstale o agente Desinstalação de Ficheiros Azure, reinicie o servidor e reinstale o agente Dessincronização de Ficheiros Azure. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | O ficheiro não foi nivelado devido a um espaço de disco insuficiente no volume onde o ponto final do servidor está localizado. | Para resolver este problema, liberte pelo menos 100 MB de espaço em disco no volume onde o ponto final do servidor está localizado. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | O ficheiro não foi nivelado porque não se sincronizou com a parte do ficheiro Azure. | Nenhuma ação necessária. O ficheiro será nivelado uma vez que tenha sincronizado com a parte de ficheiro Azure. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | O ficheiro falhou porque é um ponto de reparse não suportado. | Se o ficheiro for um ponto de reanálise da deduplicação de dados, siga os passos no guia de [planeamento](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#data-deduplication) para ativar o suporte à desduplicação de dados. Os ficheiros com pontos de reparse que não a Deduplicação de Dados não são suportados e não serão nividados.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | O ficheiro não foi niveado porque foi modificado. | Nenhuma ação necessária. O ficheiro será nivelado assim que o ficheiro modificado tiver sincronizado com a parte do ficheiro Azure. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | O ficheiro não foi nivelado porque não se sincronizou com a parte do ficheiro Azure. | Nenhuma ação necessária. O ficheiro será nivelado uma vez que tenha sincronizado com a parte de ficheiro Azure. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | O ficheiro não foi niveado devido a um problema de rede. | Nenhuma ação necessária. Se o erro persistir, verifique a conectividade da rede com a partilha de ficheiros Azure. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | O ficheiro não foi niveado porque foi modificado. | Nenhuma ação necessária. O ficheiro será nivelado assim que o ficheiro modificado tiver sincronizado com a parte do ficheiro Azure. |
| 0x800705aaa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | O ficheiro não foi niveado devido a recursos insuficientes do sistema. | Se o erro persistir, investigue qual a aplicação ou o condutor do modo kernel está a esgotar os recursos do sistema. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Como resolver ficheiros que não sejam recordados  
Se os ficheiros não forem recolhidos:
1. No Observador de Eventos, reveja os registos de eventos de telemetria, operacional e diagnóstico, localizados em Aplicações e Serviços\Microsoft\FileSync\Agent.
    1. Verifique se os ficheiros existem na partilha de ficheiros Azure.
    2. Verifique se o servidor tem conectividade com a Internet. 
    3. Abra o snap-in do MMC de serviços e verifique se o serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) está em execução.
    4. Verifique se os controladores de filtro Sincronia de Ficheiros Azure (StorageSync.sys e StorageSyncGuard.sys) estão em execução:
        - Num pedido de comando `fltmc`elevado, corra. Verifique se os controladores de filtro de ficheiros StorageSync.sys e StorageSyncGuard.sys estão listados.

> [!NOTE]
> Um Id de evento 9006 é registado uma vez por hora no registo de eventos da Telemetria se um ficheiro não se lembrar (um evento é registado por código de erro). Verifique os erros de recolha e a secção de [reparação](#recall-errors-and-remediation) para ver se as medidas de reparação estão listadas para o código de erro.

### <a name="recall-errors-and-remediation"></a>Relembrar erros e remediação

| HRESULT | HRESULT (decimal) | Cadeia do erro | Problema | Remediação |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | O ficheiro não se lembrou devido a um intervalo de tempo. Este problema pode ocorrer por várias razões: restrições de recursos do servidor, fraca conectividade da rede ou um problema de armazenamento Azure (por exemplo, estrangulamento). | Nenhuma ação necessária. Se o erro persistir durante várias horas, abra um pedido de suporte. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | O ficheiro não se reteve devido a um problema de rede.  | Se o erro persistir, verifique a conectividade da rede com a partilha de ficheiros Azure. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | O ficheiro não se relembrou porque o ponto final do servidor foi eliminado. | Para resolver este problema, consulte os [ficheiros Tiered não estarem acessíveis no servidor depois](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)de apagar um ponto final do servidor . |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | O ficheiro não se reteve devido a um erro de acesso negado. Este problema pode ocorrer se as definições de firewall e rede virtual na conta de armazenamento estiverem ativadas e o servidor não tiver acesso à conta de armazenamento. | Para resolver este problema, adicione o endereço IP do Servidor ou a rede virtual seguindo os passos documentados na secção de definições de [firewall configure e rede virtual](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings) no guia de implementação. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | O ficheiro não se lembrou porque não é acessível na partilha de ficheiros Azure. | Para resolver este problema, verifique se o ficheiro existe na parte de ficheiro sinuosa do Azure. Se o ficheiro existir na partilha de ficheiros Azure, atualize para a mais recente versão do [agente](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)DoMS de Ficheiros . |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | O ficheiro não se reteve devido à falha de autorização na conta de armazenamento. | Para resolver este problema, verifique se [o Azure File Sync tem acesso à conta de armazenamento.](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#troubleshoot-rbac) |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | O ficheiro não se relembrou porque a partilha de ficheiros Azure não é acessível. | Verifique se a parte do ficheiro existe e está acessível. Se a parte do ficheiro foi eliminada e recriada, executar as etapas documentadas no [Sync falhou porque a parte do ficheiro Azure foi eliminada e recriada](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#-2134375810) para eliminar e recriar o grupo de sincronização. |
| 0x800705aaa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | O ficheiro não se reteve devido à insuficiência de recursos do sistema. | Se o erro persistir, investigue qual a aplicação ou o condutor do modo kernel está a esgotar os recursos do sistema. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | O ficheiro não se recordou devido à falta de memória. | Se o erro persistir, investigue qual a aplicação ou o controlador do modo kernel que está a causar a baixa condição de memória. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | O ficheiro não se reteve devido a um espaço de disco insuficiente. | Para resolver este problema, liberte espaço no volume movendo ficheiros para um volume diferente, aumente o tamanho do volume ou force os ficheiros a nimar utilizando o cmdlet Invoke-StorageSyncCloudTiering. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Os ficheiros em camadas não são acessíveis no servidor após a eliminação de um ponto final do servidor
Os ficheiros hierárquicos num servidor tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de apagar um ponto final do servidor.

Erros registados se ficheiros hierárquicos não forem acessíveis
- Ao sincronizar um ficheiro, o código de erro -2147942467 (0x80070043 - ERROR_BAD_NET_NAME) é registado no registo de eventos DoitResults
- Ao recordar um ficheiro, o código de erro -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) é registado no registo do evento RecallResults

O restauro do acesso aos ficheiros em camadas será possível se forem cumpridas as seguintes condições:
- O ponto final do servidor foi eliminado nos últimos 30 dias
- O ponto final da cloud não foi eliminado 
- A partilha de ficheiros não foi eliminada
- O grupo de sincronização não foi eliminado

Se as condições acima forem cumpridas, poderá restaurar o acesso aos ficheiros no servidor através da recriação do ponto final do servidor no mesmo caminho no servidor dentro do mesmo grupo de sincronização no prazo de 30 dias. 

Se as condições acima não forem cumpridas, não será possível restaurar o acesso, uma vez que estes ficheiros em camadas no servidor são, agora, órfãos. Siga as instruções abaixo para remover os ficheiros órfãos.

**Notas**
- Quando os ficheiros hierárquicos não estiverem acessíveis no servidor, o ficheiro completo deve ainda estar acessível se aceder diretamente à partilha de ficheiros Azure.
- Para evitar ficheiros órfãos no futuro, siga os passos documentados no [Remover um ponto final](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint) do servidor ao eliminar um ponto final do servidor.

<a id="get-orphaned"></a>**Como obter a lista de ficheiros órfãos** 

1. Verifique se a versão v5.1 do agente Dessincronizado do Ficheiro Azure está instalada.
2. Executar os seguintes comandos PowerShell para listar ficheiros órfãos:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Guarde o ficheiro de saída OrphanTieredFiles.txt no caso de os ficheiros precisarem de ser restaurados a partir da cópia de segurança após a sua eliminação.

<a id="remove-orphaned"></a>**Como remover ficheiros órfãos** 

*Opção 1: Eliminar os ficheiros órfãos*

Esta opção elimina os ficheiros hierárquicos órfãos no Servidor do Windows, mas requer a remoção do ponto final do servidor se existir devido à recreação após 30 dias ou estiver ligado a um grupo de sincronização diferente. Os conflitos de ficheiros ocorrerão se os ficheiros forem atualizados na partilha de ficheiros Do Windows Server ou Azure antes de o ponto final do servidor ser recriado.

1. Verifique se a versão v5.1 do agente Dessincronizado do Ficheiro Azure está instalada.
2. Faça backup da partilha de ficheiros Azure e da localização final do servidor.
3. Remova o ponto final do servidor no grupo de sincronização (se existir) seguindo os passos documentados no [Ponto final do servidor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-server-endpoint#remove-a-server-endpoint).

> [!Warning]  
> Se o ponto final do servidor não for removido antes de utilizar o cmdlet Remove-StorageSyncOrphanedTieredFiles, a eliminação do ficheiro hierarquizado órfão no servidor eliminará o ficheiro completo na partilha de ficheiros Azure. 

4. Executar os seguintes comandos PowerShell para listar ficheiros órfãos:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Guarde o ficheiro de saída OrphanTieredFiles.txt no caso de os ficheiros precisarem de ser restaurados a partir da cópia de segurança após a sua eliminação.
6. Executar os seguintes comandos PowerShell para eliminar ficheiros órfãos:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Notas** 
- Serão eliminados ficheiros hierárquicos modificados no servidor que não estejam sincronizados com a parte do ficheiro Azure.
- Os ficheiros hierárquicos acessíveis (não órfãos) não serão eliminados.
- Os ficheiros não nivantes permanecerão no servidor.

7. Opcional: Recrie o ponto final do servidor se for eliminado no passo 3.

*Opção 2: Monte a partilha de ficheiros Azure e copie os ficheiros localmente órfãos no servidor*

Esta opção não requer a remoção do ponto final do servidor, mas requer espaço suficiente para copiar os ficheiros completos localmente.

1. [Monte](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) a partilha de ficheiros Azure no Windows Server que tem ficheiros órfãos.
2. Executar os seguintes comandos PowerShell para listar ficheiros órfãos:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Utilize o ficheiro de saída OrphanTieredFiles.txt para identificar ficheiros órfãos no servidor.
4. Repor os ficheiros hierárquicos órfãos copiando o ficheiro completo da parte do ficheiro Azure para o Windows Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Como resolver ficheiros inesperadamente recordados num servidor  
Antivírus, backup e outras aplicações que lêem um grande número de ficheiros causam recordações não intencionais, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Ignorar os ficheiros offline nos produtos que suportam esta opção ajuda a evitar obtenções não pretendidas durante operações como análises de antivírus ou trabalhos de criação de cópias de segurança.

Consulte o fornecedor do seu software para aprender a configurar a solução do mesmo para ignorar os ficheiros offline.

Chamadas não intencionais também podem ocorrer noutros cenários, como quando está a navegar ficheiros no File Explorer. Abrir uma pasta que tenha ficheiros em camadas na cloud no Explorador de Ficheiros no servidor pode originar obtenções não pretendidas. Isto é ainda mais provável se uma solução de antivírus estiver ativada no servidor.

> [!NOTE]
>Utilize o ID 9059 do evento de evento sinuoso para determinar que aplicações estão a causar recordações. Este evento fornece distribuição de recolha de aplicações para um ponto final do servidor e é registado uma vez por hora.

### <a name="tls-12-required-for-azure-file-sync"></a>TLS 1.2 necessário para sincronização de ficheiros Azure

Pode visualizar as definições de TLS no seu servidor olhando para as [definições de registo](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings). 

Se estiver a utilizar um representante, consulte a documentação do seu representante e certifique-se de que está configurado para utilizar o TLS1.2.

## <a name="general-troubleshooting"></a>Resolução geral de problemas
Se encontrar problemas com o Azure File Sync num servidor, comece por completar os seguintes passos:
1. No Espectador de Eventos, reveja os registos de eventos de telemetria, operacional e diagnóstico.
    - Os problemas de sincronização, tiering e recordação estão registados nos registos de eventos de telemetria, diagnóstico e operacional no âmbito de Aplicações e Serviços\Microsoft\FileSync\Agent.
    - Os problemas relacionados com a gestão de um servidor (por exemplo, configurações de configuração) são registados nos registos de eventos operacionais e de diagnóstico em Aplicações e Serviços\Microsoft\FileSync\Management.
2. Verifique se o serviço Desincronização de Ficheiros Azure está a funcionar no servidor:
    - Abra o snap-in do MMC de serviços e verifique se o serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) está em execução.
3. Verifique se os controladores de filtro Sincronia de Ficheiros Azure (StorageSync.sys e StorageSyncGuard.sys) estão em execução:
    - Num pedido de comando `fltmc`elevado, corra. Verifique se os controladores de filtro de ficheiros StorageSync.sys e StorageSyncGuard.sys estão listados.

Se o problema não for resolvido, execute a ferramenta AFSDiag:
1. Crie um diretório onde a saída AFSDiag será guardada (por exemplo, C:\Output).
    > [!NOTE]
    >A AFSDiag eliminará todos os conteúdos do diretório de saída antes de recolher registos. Especifique um local de saída que não contenha dados.
2. Abra uma janela PowerShell elevada e, em seguida, executar os seguintes comandos (prima Entrar após cada comando):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Para o nível de traço do modo Kernel do modo Kernel do Ficheiro Azure, introduza **1** (salvo especificação em contrário, para criar mais vestígios verbosos) e, em seguida, prima Enter.
4. Para o nível de rastreio do modo de utilizador Do modo Dessincronizado do Ficheiro Azure, introduza **1** (salvo especificação em contrário, para criar mais vestígios verbosos) e, em seguida, prima Enter.
5. Reproduza o problema. Quando terminar, entre em **D.**
6. Um ficheiro .zip que contém registos e ficheiros de rastreio é guardado no diretório de saída que especificou.

## <a name="see-also"></a>Consulte também
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
- [Ficheiros Azure frequentemente fazem perguntas](storage-files-faq.md)
- [Resolver problemas de Ficheiros do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Problemas de suturação de ficheiros Azure em Linux](storage-troubleshoot-linux-file-connection-problems.md)
