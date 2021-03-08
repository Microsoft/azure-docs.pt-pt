---
title: Resolução de problemas Azure File Sync | Microsoft Docs
description: Resolver problemas comuns numa implementação no Azure File Sync, que pode utilizar para transformar o Windows Server numa cache rápida da sua partilha de ficheiros Azure.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 2/1/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: f54156ed8cc4c049e10bada77a66eee084f7c5fb
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453279"
---
# <a name="troubleshoot-azure-file-sync"></a>Resolver problemas da Sincronização de Ficheiros do Azure
Utilize o Azure File Sync para centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos dados localmente, incluindo SMB, NFS e FTPS. Podes ter o número de caches que precisares em todo o mundo.

Este artigo foi concebido para ajudá-lo a resolver problemas que poderá encontrar com a sua implementação de Azure File Sync. Também descrevemos como recolher registos importantes do sistema se for necessária uma investigação mais profunda do problema. Se não vir a resposta à sua pergunta, pode contactar-nos através dos seguintes canais (por ordem de escalada):

1. [Microsoft Q&Uma página de perguntas para o Azure Storage](/answers/products/azure?product=storage).
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Suporte microsoft. Para criar um novo pedido de suporte, no portal Azure, no separador **Ajuda,** selecione o botão **de suporte Ajuda +** e, em seguida, selecione Novo pedido de **suporte**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Estou tendo um problema com Azure File Sync no meu servidor (sincronização, nivelamento de nuvem, etc.). Devo remover e recriar o ponto final do meu servidor?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Instalação de agente e registo do servidor
<a id="agent-installation-failures"></a>**Falhas na instalação do agente de resolução de problemas**  
Se a instalação do agente Azure File Sync falhar, num pedido de comando elevado, executar o seguinte comando para ligar a registar registo durante a instalação do agente:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Reveja o instalador.log determinar a causa da falha de instalação.

<a id="agent-installation-on-DC"></a>**A instalação do agente falha no Controlador de Domínio do Diretório Ativo**  
Se tentar instalar o agente de sincronização num controlador de domínio ative directory onde o proprietário da função PDC se encontra numa versão R2 do Windows Server 2008 ou abaixo do SISTEMA, poderá atingir o problema onde o agente de sincronização não irá instalar.

Para resolver, transfira a função PDC para outro controlador de domínio que executa o Windows Server 2012 R2 ou mais recente, e depois instale sincronização.

<a id="parameter-is-incorrect"></a>**Aceder a um volume no Windows Server 2012 R2 falha com erro: O parâmetro está incorreto**  
Depois de criar um ponto final do servidor no Windows Server 2012 R2, ocorre o seguinte erro ao aceder ao volume:

driveletter:\ não é acessível.  
Parâmetro incorreto.

Para resolver este problema, instale [o KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) e reinicie o servidor. Se esta atualização não for instalada porque uma atualização posterior já está instalada, vá ao Windows Update, instale as mais recentes atualizações para o Windows Server 2012 R2 e reinicie o servidor.

<a id="server-registration-missing-subscriptions"></a>**O Registo do Servidor não lista todas as Subscrições do Azure**  
Ao registar um servidor utilizando ServerRegistration.exe, faltam subscrições quando clica na entrega da Subscrição Azure.

Esta questão ocorre porque ServerRegistration.exe só recuperará subscrições dos primeiros 5 inquilinos AD Azure. 

Para aumentar o limite de inusibilidade do servidor, crie um valor DWORD chamado ServerRegistrationTenantLimit em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync com um valor superior a 5.

Também pode resolver este problema utilizando os seguintes comandos PowerShell para registar o servidor:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**O Registo do Servidor apresenta a seguinte mensagem: "Faltam requisitos prévios"**  
Esta mensagem aparece se o módulo Az ou AzureRM PowerShell não estiver instalado no PowerShell 5.1. 

> [!Note]  
> ServerRegistration.exe não suporta PowerShell 6.x. Pode utilizar o cmdlet Register-AzStorageSyncServer no PowerShell 6.x para registar o servidor.

Para instalar o módulo Az ou AzureRM no PowerShell 5.1, execute os seguintes passos:

1. Tipo **de powershell** a partir de uma pronta de comando elevada e bater entrar.
2. Instale o módulo Az ou AzureRM mais recente seguindo a documentação:
    - [Módulo Az (requer .NET 4.7.2)](/powershell/azure/install-az-ps)
    - [Módulo do AzureRM](https://go.microsoft.com/fwlink/?linkid=856959)
3. Executar ServerRegistration.exe e completar o assistente para registar o servidor com um Serviço de Sincronização de Armazenamento.

<a id="server-already-registered"></a>**O Registo do Servidor apresenta a seguinte mensagem: "Este servidor já está registado"** 

![Uma imagem do diálogo de registo do servidor com a mensagem de erro "servidor já está registado"](media/storage-sync-files-troubleshoot/server-registration-1.png)

Esta mensagem aparece se o servidor foi previamente registado num Serviço de Sincronização de Armazenamento. Para desregistralr o servidor do atual Serviço de Sincronização de Armazenamento e, em seguida, registar-se com um novo Serviço de Sincronização de Armazenamento, complete os passos descritos em [Unregister um servidor com Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Se o servidor não estiver listado em **servidores registados** no Serviço de Sincronização de Armazenamento, no servidor que pretende não registar, execute os seguintes comandos PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Se o servidor fizer parte de um cluster, pode utilizar o parâmetro opcional *Reset-StorageSyncServer -CleanClusterRegistration* para também remover o registo do cluster.

<a id="web-site-not-trusted"></a>**Quando registo um servidor, vejo numerosas respostas de "site não confiável". Porquê?**  
Este problema ocorre quando a política de segurança do **Internet Explorer melhorada** é ativada durante o registo do servidor. Para obter mais informações sobre como desativar corretamente a política de segurança do **Explorador de Internet melhorada,** consulte Prepare o [Servidor do Windows para utilizar com o Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) e como implementar o [Azure File Sync](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**O servidor não está listado em servidores registados no portal Azure**  
Se um servidor não estiver listado em **servidores registados** para um Serviço de Sincronização de Armazenamento:
1. Inscreva-se no servidor que pretende registar.
2. Abra o Explorador de Ficheiros e, em seguida, vá ao diretório de instalação do Agente de Sincronização de Armazenamento (a localização predefinida é C:\Program Files\Azure\StorageSyncAgent). 
3. Executar ServerRegistration.exe e completar o assistente para registar o servidor com um Serviço de Sincronização de Armazenamento.

## <a name="sync-group-management"></a>Gestão do grupo Sync

### <a name="cloud-endpoint-creation-errors"></a>Erros de criação de ponto final em nuvem

<a id="cloud-endpoint-using-share"></a>**A criação de ponto final em nuvem falha, com este erro: "O Azure FileShare especificado já está a ser utilizado por um CloudEndpoint diferente"**  
Este erro ocorrerá se a partilha de ficheiros do Azure já estiver a ser utilizada por outro ponto final da cloud. 

Se vir esta mensagem e a partilha de ficheiros Azure não estiver atualmente a ser utilizada por um ponto final em nuvem, complete os seguintes passos para limpar os metadados Azure File Sync na partilha de ficheiros Azure:

> [!Warning]  
> A eliminação dos metadados numa partilha de ficheiros Azure que está atualmente a ser utilizada por um ponto final em nuvem faz com que as operações do Azure File Sync falhem. 

1. No portal Azure, vá à sua partilha de ficheiros Azure.  
2. Clique com o botão direito na partilha de ficheiros Azure e, em seguida, **selecione Editar metadados** de edição .
3. Clique com o botão direito **SyncService** e, em seguida, **selecione Delete**.

<a id="cloud-endpoint-authfailed"></a>**Criação de ponto final em nuvem falha, com este erro: "Autorização Falhada"**  
Este erro ocorre se a sua conta de utilizador não tiver direitos suficientes para criar um ponto final em nuvem. 

Para criar um ponto final na nuvem, a sua conta de utilizador deve ter as seguintes permissões de Autorização da Microsoft:  
* Leia: Obter definição de função
* Escrever: Criar ou atualizar a definição de função personalizada
* Leia: Obtenha atribuição de função
* Escrever: Criar atribuição de funções

As seguintes funções incorporadas têm as permissões de autorização da Microsoft necessárias:  
* Proprietário
* Administrador de Acesso dos Utilizadores

Para determinar se a sua função de conta de utilizador tem as permissões necessárias:  
1. No portal Azure, selecione **Grupos de Recursos**.
2. Selecione o grupo de recursos onde está localizada a conta de armazenamento e, em seguida, selecione **Access control (IAM)**.
3. Selecione o **separador Funções.**
4. Selecione a **Função** (por exemplo, Proprietário ou Contribuinte) para a sua conta de utilizador.
5. Na lista de **fornecedores de recursos,** selecione **a Autorização da Microsoft.** 
    * **A atribuição de funções** deve ter permissões **de Leitura** e **Escrita.**
    * **A definição de função** deve ter permissões **de Leitura** e **Escrita.**

### <a name="server-endpoint-creation-and-deletion-errors"></a>Erros de criação e eliminação de pontos finais do servidor

<a id="-2134375898"></a>**A criação do ponto final do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2134375898 ou 0x80c80226)**  
Este erro ocorrerá se o caminho do ponto final do servidor estiver no volume de sistema e o arrumo na cloud estiver ativado. O arrumo na cloud não é suportado no volume de sistema. Para criar um ponto final do servidor no volume de sistema, desative o arrumo na cloud ao criar o ponto final do servidor.

<a id="-2147024894"></a>**A criação do ponto final do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2147024894 ou 0x80070002)**  
Este erro ocorrerá se o caminho do ponto final do servidor especificado não for válido. Confirme se o caminho do ponto final do servidor especificado é um volume NTFS ligado localmente. Nota: O Azure File Sync não suporta unidades mapeadas como um caminho de ponto final do servidor.

<a id="-2134375640"></a>**A criação do ponto final do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2134375640 ou 0x80c80328)**  
Este erro ocorre se o caminho do ponto final do servidor especificado não for um volume NTFS. Confirme se o caminho do ponto final do servidor especificado é um volume NTFS ligado localmente. Nota: O Azure File Sync não suporta unidades mapeadas como um caminho de ponto final do servidor.

<a id="-2134347507"></a>**A criação do ponto final do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2134347507 ou 0x80c8710d)**  
Este erro ocorre porque o Azure File Sync não suporta pontos finais de servidor em volumes que tenham uma pasta de Informações de Volume de Sistema comprimida. Para resolver este problema, descomprima a pasta de Informações de Volume de Sistema. Se a pasta de Informações de Volume de Sistema for a única pasta comprimida no volume, execute os seguintes passos:

1. Descarregue a ferramenta [PsExec.](/sysinternals/downloads/psexec)
2. Executar o seguinte comando a partir de um pedido de comando elevado para lançar um pedido de comando que funciona sob a conta do sistema: **PsExec.exe -i-s -d cmd**
3. Na linha de comandos em execução na conta do sistema, escreva os seguintes comandos e prima enter:   
    **cd /d "drive letter:\System Volume Information"**  
    **compacto /u /s**

<a id="-2134376345"></a>**A criação do ponto final do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2134376345 ou 0x80C80067)**  
Este erro ocorrerá se o limite de pontos finais do servidor por servidor for atingido. O Azure File Sync suporta atualmente até 30 pontos finais por servidor. Para obter mais informações, consulte [os alvos da escala de sincronização de ficheiros Azure](./storage-files-scale-targets.md#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**A criação do ponto final do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2134376427 ou 0x80c80015)**  
Este erro ocorrerá se outro ponto final do servidor já estiver a sincronizar o caminho do ponto final do servidor especificado. O Azure File Sync não suporta múltiplos pontos finais de servidor a sincronizar o mesmo diretório ou volume.

<a id="-2160590967"></a>**A criação do ponto final do servidor falha, com este erro: "MgmtServerJobFailed" (Código de erro: -2160590967 ou 0x80c80077)**  
Este erro ocorre se o caminho do ponto final do servidor contiver ficheiros órfãs. Se um ponto final do servidor foi recentemente removido, aguarde até que a limpeza de ficheiros com camadas órfãs esteja concluída. Um ID de evento 6662 é registado no registo do evento de Telemetria assim que a limpeza de ficheiros com camadas órfãs começou. Um ID de evento 6661 é registado assim que a limpeza de ficheiros órfãs tiver concluído e um ponto final do servidor pode ser recriado usando o caminho. Se a criação do ponto final do servidor falhar após a limpeza dos ficheiros hierárquicos ter concluído ou se o ID 6661 do evento não puder ser encontrado no registo do evento de Telemetria devido à capotagem do registo de eventos, remova os ficheiros hieraranos órfãos executando os passos documentados nos [ficheiros Tiered não estão acessíveis no servidor depois de eliminar uma](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) secção de ponto final do servidor.

<a id="-2134347757"></a>**Falha na eliminação do ponto final do servidor, com este erro: "MgmtServerJobExpired" (Código de erro: -2134347757 ou 0x80c87013)**  
Este erro ocorrerá se o servidor estiver offline ou não tiver conectividade de rede. Se o servidor deixar de estar disponível, anule o registo do servidor no portal, o que eliminará os pontos finais do servidor. Para eliminar os pontos finais do servidor, siga os passos descritos em [Unregister um servidor com Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

### <a name="server-endpoint-health"></a>Saúde do ponto final do servidor

<a id="server-endpoint-provisioningfailed"></a>**Não é possível abrir a página de propriedades do ponto final do servidor ou atualizar a política de tiering da nuvem**  
Este problema pode ocorrer se uma operação de gestão no ponto final do servidor falhar. Se a página de propriedades do ponto final do servidor não abrir no portal Azure, atualizar o ponto final do servidor utilizando comandos PowerShell do servidor poderá corrigir este problema. 

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
<a id="server-endpoint-noactivity"></a>**O ponto final do servidor tem um estado de saúde de "No Activity" ou "Pendente" e o estado do servidor na lâmina dos servidores registados é "Aparece offline"**  

Este problema pode ocorrer se o processo de Monitor de Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) não estiver em funcionamento ou se o servidor não conseguir aceder ao serviço Azure File Sync.

No servidor que está a mostrar como "Aparece offline" no portal, veja o ID 9301 do Evento no registo de eventos de Telemetria (localizado em Aplicações e Serviços\Microsoft\FileSync\Agent in Event Viewer) para determinar por que razão o servidor não consegue aceder ao serviço Azure File Sync. 

- Se **o GetNextJob estiver concluído com o estado: 0** está registado, o servidor pode comunicar com o serviço Azure File Sync. 
    - Abra o Gestor de Tarefas no servidor e verifique se o processo Monitor da Sincronização de Armazenamento (AzureStorageSyncMonitor.exe) está em execução. Se não estiver em execução, experimente primeiro reiniciar o servidor. Se o reinício do servidor não resolver o problema, atualize para a [versão do agente](./storage-files-release-notes.md) do Azure File Sync mais recente. 

- Se **o GetNextJob estiver concluído com o estado: -2134347756** está registado, o servidor não consegue comunicar com o serviço Azure File Sync devido a uma configuração de ordem de firewall, proxy ou suíte de cifra TLS. 
    - Se o servidor estiver por trás de uma firewall, verifique se a porta 443 de saída é permitida. Se a firewall restringir o tráfego a domínios específicos, confirme que os domínios listados na [documentação](./storage-sync-files-firewall-and-proxy.md#firewall) firewall estão acessíveis.
    - Se o servidor estiver por detrás de um proxy, configufique as definições de procuração específicas para toda a máquina ou para aplicações seguindo os passos na [documentação](./storage-sync-files-firewall-and-proxy.md#proxy)Proxy .
    - Utilize o Test-StorageSyncNetworkConnectivity cmdlet para verificar a conectividade da rede nos pontos finais de serviço. Para saber mais, consulte [a conectividade da rede de teste para os pontos finais de serviço](./storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).
    - Se a ordem de suíte de cifra TLS estiver configurada no servidor, pode utilizar a política de grupo ou os cmdlets TLS para adicionar suítes de cifra:
        - Para utilizar a política de grupo, consulte [a Configuração da Ordem da Suíte Cifra TLS utilizando a Política de Grupo](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy).
        - Para utilizar cmdlets TLS, consulte [a Configuração da Ordem da Suíte Cifra TLS utilizando cmdlets TLS PowerShell](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets).
    
        A Azure File Sync suporta atualmente as seguintes suítes cifra para o protocolo TLS 1.2:  
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256  

- Se **o GetNextJob estiver concluído com o estado: -2134347764** estiver registado, o servidor não consegue comunicar com o serviço Azure File Sync devido a um certificado expirado ou eliminado.  
    - Executar o seguinte comando PowerShell no servidor para redefinir o certificado utilizado para a autenticação:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**O ponto final do servidor tem um estado de saúde de "No Activity" e o estado do servidor na lâmina dos servidores registados é "Online"**  

O estado de funcionamento de um ponto final do servidor “Sem Atividade” significa que o ponto final do servidor não registou atividade de sincronização nas últimas duas horas.

Para verificar a atividade de sincronização atual num servidor, veja [como monitorizo o progresso de uma sessão de sincronização atual?](#how-do-i-monitor-the-progress-of-a-current-sync-session)

Um ponto final do servidor pode não registar atividade sincronizada durante várias horas devido a um bug ou recursos insuficientes do sistema. Verifique se a versão mais recente do [agente](./storage-files-release-notes.md) Azure File Sync está instalada. Se a questão persistir, abra um pedido de apoio.

> [!Note]  
> Se o estado do servidor na lâmina dos servidores registados for "Aparece Offline", executar os passos documentados no ponto final do [Servidor tem um estado de saúde de "Nenhuma Atividade" ou "Pendente" e o estado do servidor na lâmina do servidor registado é "Aparece offline".](#server-endpoint-noactivity)

## <a name="sync"></a>Sync
<a id="afs-change-detection"></a>**Se criei um ficheiro diretamente na minha partilha de ficheiros Azure através do SMB ou através do portal, quanto tempo demora o ficheiro a sincronizar os servidores do grupo de sincronização?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**A saúde do ponto final do servidor está em estado pendente por várias horas**  
Este problema é esperado se criar um ponto final em nuvem e utilizar uma partilha de ficheiros Azure que contenha dados. O trabalho de enumeração de alteração que verifica alterações na partilha de ficheiros Azure deve ser concluído antes que os ficheiros possam sincronizar entre os pontos finais da nuvem e do servidor. O tempo para concluir o trabalho depende do tamanho do espaço de nome na partilha de ficheiros Azure. A saúde do ponto final do servidor deve ser atualizada assim que o trabalho de enumeração de alteração estiver concluído.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Como monitorizo a saúde sincronizada?
# <a name="portal"></a>[Portal](#tab/portal1)
Dentro de cada grupo de sincronização, pode perfurar os seus pontos finais individuais do servidor para ver o estado das últimas sessões de sincronização concluídas. Uma coluna de Saúde verde e um valor de sincronização de ficheiros de 0 indicam que a sincronização está a funcionar como esperado. Se este não for o caso, consulte abaixo uma lista de erros de sincronização comuns e como lidar com ficheiros que não estão a sincronizar. 

![Uma imagem do portal Azure](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Servidor](#tab/server)
Aceda aos registos de telemetria do servidor, que podem ser encontrados no Visualizador de Eventos em `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` . O Evento 9102 corresponde a uma sessão de sincronização concluída; para o estado mais recente da sincronização, procure o evento mais recente com o ID 9102. O SyncDirection diz-lhe se esta sessão foi um upload ou download. Se o HResult for 0, então a sessão de sincronização foi bem sucedida. Um HResult não-zero significa que houve um erro durante a sincronização; ver abaixo para uma lista de erros comuns. Se o PerItemErrorCount for superior a 0, isto significa que alguns ficheiros ou pastas não sincronizaram corretamente. É possível ter um HResult de 0 mas um PerItemErrorCount que seja maior que 0.

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

Inversamente, um upload mal sucedido pode ser assim:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Por vezes, as sessões de sincronização falham globalmente ou têm um PerItemErrorCount não-zero, mas ainda fazem progressos, com alguns ficheiros a sincronizarem-se com sucesso. Isto pode ser visto nos campos AppliedFileCount, AppliedDirCount, AppliedTombstoneCount e AppliedSizeBytes), que lhe dizem quanto da sessão está a ter sucesso. Se vir várias sessões de sincronização seguidas que estão a falhar mas que têm uma contagem deplicou mais, então deve dar tempo de sincronização para tentar novamente antes de abrir um bilhete de suporte.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Como posso monitorizar o progresso de uma sessão de sincronização atual?
# <a name="portal"></a>[Portal](#tab/portal1)
Dentro do seu grupo de sincronização, vá ao ponto final do servidor em questão e veja a secção De Atividade sincronizada para ver a contagem de ficheiros carregados ou descarregados na sessão de sincronização atual. Note que este estado será atrasado em cerca de 5 minutos e, se a sessão de sincronização for suficientemente curta para ser concluída dentro deste período, poderá não ser comunicada no portal. 

# <a name="server"></a>[Servidor](#tab/server)
Veja o mais recente evento 9302 no registo de telemetria no servidor (no Visualizador de Eventos, vá a Applications and Services Logs\Microsoft\FileSync\Agent\Telemetria). Este evento indica o estado da sessão de sincronização atual. A TotalItemCount denota quantos ficheiros devem ser sincronizados, a AppliedItemCount o número de ficheiros que foram sincronizados até agora, e a PerItemErrorCount o número de ficheiros que não estão a sincronizar (ver abaixo como lidar com isto).

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
Para cada servidor de um determinado grupo de sincronização, certifique-se de:
- Os cartões de tempo para o Último Sync tentado para o upload e download são recentes.
- O estado é verde tanto para o upload como para o download.
- O campo Sync Activity mostra muito poucos ou tão poucos ficheiros restantes para sincronizar.
- O campo De Ficheiros Não Sincronização é 0 para o upload e download.

# <a name="server"></a>[Servidor](#tab/server)
Veja as sessões de sincronização concluídas, que são marcadas por 9102 eventos no registo de eventos de telemetria para cada servidor (no Espectador de Eventos, vá a `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` ). 

1. Em qualquer servidor, pretende certificar-se de que as últimas sessões de upload e descarregamento foram concluídas com sucesso. Para isso, verifique se o HResult e o PerItemErrorCount são 0 para o upload e download (o campo SyncDirection indica se uma determinada sessão é uma sessão de upload ou download). Note que se não vir uma sessão de sincronização recentemente concluída, é provável que esteja em curso uma sessão de sincronização, o que é de esperar se apenas adicionar ou modificar uma grande quantidade de dados.
2. Quando um servidor estiver totalmente atualizado com a nuvem e não tiver alterações para sincronizar em nenhuma das direções, verá sessões de sincronização vazias. Estes são indicados por eventos de upload e download em que todos os campos SyncFileCount, SyncDirCount, SyncTombstoneCount e SyncSizeBytes) são zero, o que significa que não havia nada para sincronizar. Note que estas sessões de sincronização vazias podem não ocorrer em servidores de alta agitação, uma vez que há sempre algo novo para sincronizar. Se não houver atividade sincronizada, devem ocorrer a cada 30 minutos. 
3. Se todos os servidores estiverem atualizados com a nuvem, o que significa que as suas recentes sessões de upload e download são sessões de sincronização vazias, pode dizer com certeza razoável que o sistema como um todo está sincronizado. 
    
Note que se efe tiver feito alterações diretamente na sua partilha de ficheiros Azure, o Azure File Sync não detetará esta alteração até que a enumeração de alteração seja feita, o que acontece uma vez a cada 24 horas. É possível que um servidor diga que está atualizado com a nuvem quando, de facto, está a faltar alterações recentes feitas diretamente na partilha de ficheiros Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Como posso ver se existem pastas ou ficheiros específicos que não estão a sincronizar?
Se o perItemErrorCount no servidor ou na contagem de Ficheiros Não Sincronização no portal for superior a 0 para qualquer sessão de sincronização, isso significa que alguns itens não estão a sincronizar. Os ficheiros e as pastas podem ter características que os impedem de sincronizar. Estas características podem ser persistentes e requerer uma ação explícita para retomar a sincronização, por exemplo, remover caracteres não suportados do nome do ficheiro ou da pasta. Também podem ser transitórios, o que significa que o ficheiro ou a pasta retomarão automaticamente a sincronização; por exemplo, os ficheiros com pegas abertas retomam automaticamente a sincronização quando o ficheiro está fechado. Quando o motor Azure File Sync deteta tal problema, é produzido um registo de erro que pode ser analisado para listar os itens que não estão a sincronizar corretamente.

Para ver estes erros, execute **o** FileSyncErrorsReport.ps1script PowerShell (localizado no diretório de instalação do agente Azure File Sync) para identificar ficheiros que não sincronizaram devido a cabos abertos, caracteres não suportados ou outros problemas. O campo ItemPath indica-lhe a localização do ficheiro em relação ao diretório de sincronização de raiz. Consulte a lista de erros de sincronização comuns abaixo para obter medidas de reparação.

> [!Note]  
> Se o FileSyncErrorsReport.ps1 script retornar "Não foram encontrados erros de ficheiro" ou não enumera erros por item para o grupo de sincronização, a causa é:
>
>- Causa 1: A última sessão de sincronização concluída não teve erros por artigo. O portal deve ser atualizado em breve para mostrar 0 Ficheiros não sincronizados. 
>    - Verifique o [ID 9102](?tabs=server%252cazure-portal#broken-sync) do evento no registo do evento de Telemetria para confirmar que o PerItemErrorCount é 0. 
>
>- Causa 2: O registo de eventos ItemResults no servidor embrulhado devido a demasiados erros por item e o registo do evento já não contém erros para este grupo de sincronização.
>    - Para evitar este problema, aumente o tamanho do registo de eventos ItemResults. O registo de eventos ItemResults pode ser encontrado em "Applications and Services Logs\Microsoft\FileSync\Agent" no Event Viewer. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Resolução de problemas por erros de sincronização de ficheiros/diretórios
**ItemResults log - erros de sincronização por item**  

| HRESULT | HRESULT (decimal) | Cadeia do erro | Problema | Remediação |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | O ficheiro hierárquico do servidor não está acessível. Este problema ocorrerá se o ficheiro em camadas não tiver sido recuperado antes de eliminar um ponto final do servidor. | Para resolver este problema, ver [ficheiros Tiered não estão acessíveis no servidor depois de eliminar um ponto final do servidor](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | O ficheiro ou a alteração do diretório ainda não podem ser sincronizados porque uma pasta dependente ainda não está sincronizada. Este item sincroniza-se depois de as alterações dependentes serem sincronizadas. | Nenhuma ação necessária. Se o erro persistir durante vários dias, utilize o FileSyncErrorsReport.ps1 script PowerShell para determinar por que razão a pasta dependente ainda não está sincronizada. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | O ficheiro ou a alteração do diretório ainda não podem ser sincronizados porque uma pasta dependente ainda não está sincronizada. Este item sincroniza-se depois de as alterações dependentes serem sincronizadas. | Nenhuma ação necessária. Se o erro persistir durante vários dias, utilize o FileSyncErrorsReport.ps1 script PowerShell para determinar por que razão a pasta dependente ainda não está sincronizada. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | O ficheiro ou a alteração do diretório ainda não podem ser sincronizados porque uma pasta dependente ainda não está sincronizada e a sessão de sincronização falhou. Este item sincroniza-se depois de as alterações dependentes serem sincronizadas. | Nenhuma ação necessária. Se o erro persistir, investigue a falha da sessão de sincronização. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | O ficheiro ou o nome do diretório são inválidos. | Mude o nome do ficheiro ou diretório em questão. Consulte [o Manuseamento de caracteres não suportados](?tabs=portal1%252cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | O ficheiro ou o nome do diretório são inválidos. | Mude o nome do ficheiro ou diretório em questão. Consulte [o Manuseamento de caracteres não suportados](?tabs=portal1%252cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | O ficheiro não pode ser sincronizado porque está a ser utilizado. O ficheiro será sincronizado quando já não estiver em utilização. | Nenhuma ação necessária. O Azure File Sync cria uma imagem temporária do VSS uma vez por dia no servidor para sincronizar ficheiros que têm pegas abertas. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | O ficheiro mudou, mas a alteração ainda não foi detetada por sincronização. O Sync recuperará depois de ser detetada esta alteração. | Nenhuma ação necessária. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | O ficheiro foi apagado e a sincronização não está ciente da alteração. | Nenhuma ação necessária. O Sync deixará de registar este erro assim que a deteção de alterações detetar que o ficheiro foi eliminado. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | A eliminação de um ficheiro ou diretório não pode ser sincronizada porque o artigo já foi eliminado no destino e a sincronização não está ciente da mudança. | Nenhuma ação necessária. O Sync deixará de registar este erro assim que a deteção de alterações for executado no destino e a sincronização detete que o item foi eliminado. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | O ficheiro ou diretório foi ignorado, mas será sincronizado durante a próxima sessão de sincronização. Se este erro for reportado ao descarregar o item, o nome do ficheiro ou do diretório é mais do que provável inválido. | Não é necessária qualquer ação se este erro for reportado ao carregar o ficheiro. Se o erro for reportado ao descarregar o ficheiro, mude o nome do ficheiro ou do diretório em questão. Consulte [o Manuseamento de caracteres não suportados](?tabs=portal1%252cazure-portal#handling-unsupported-characters) para obter mais informações. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | A criação de um ficheiro ou diretório não pode ser sincronizada porque o item já existe no destino e a sincronização não está ciente da mudança. | Nenhuma ação necessária. O Sync deixará de registar este erro assim que a deteção de alterações for executado no destino e a sincronização esteja ciente deste novo item. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Não é possível sincronizar o ficheiro porque o limite de partilha de ficheiros do Azure foi atingido. | Para resolver este problema, consulte Chegou à secção [limite de armazenamento de ações de azure](?tabs=portal1%252cazure-portal#-2134351810) no guia de resolução de problemas. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | O ficheiro é encriptado por uma solução não suportada (como o NTFS EFS). | Desencriptar o ficheiro e utilizar uma solução de encriptação suportada. Para obter uma lista de soluções suportadas, veja a secção [Soluções de encriptação](./storage-sync-files-planning.md#encryption) no guia de planeamento. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | O ficheiro encontra-se numa pasta de replicação apenas de leitura DFS-R. | O ficheiro está localizado numa pasta de replicação apenas de leitura DFS-R. O Azure File Sync não suporta pontos finais do servidor nas pastas de replicação só de leitura do DFS-R. Consulte [o guia de planeamento](./storage-sync-files-planning.md#distributed-file-system-dfs) para obter mais informações. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | O ficheiro tem um estado pendente de exclusão. | Nenhuma ação necessária. O ficheiro será apagado assim que todas as pegas de ficheiro abertas estiverem fechadas. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | O ficheiro não pode ser sincronizado porque as definições de firewall e rede virtual na conta de armazenamento estão ativadas e o servidor não tem acesso à conta de armazenamento. | Adicione o endereço IP do Servidor ou a rede virtual seguindo os passos documentados na secção [de definições](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) de firewall e rede virtual no guia de implementação. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | O ficheiro não pode ser sincronizado porque o tamanho do descritor de segurança excede o limite de 64 KiB. | Para resolver este problema, remova as entradas de controlo de acesso (ACE) no ficheiro para reduzir o tamanho do descritor de segurança. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | O ficheiro não pode ser sincronizado devido a um erro inesperado. | Se o erro persistir durante vários dias, abra um caso de apoio. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | O ficheiro não pode ser sincronizado porque está a ser utilizado. O ficheiro será sincronizado quando já não estiver em utilização. | Nenhuma ação necessária. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | O ficheiro foi alterado durante a sincronização, por isso tem de ser sincronizado novamente. | Nenhuma ação necessária. |
| 0x80070017 | -2147024873 | ERROR_CRC | O ficheiro não pode ser sincronizado devido a um erro de CRC. Este erro pode ocorrer se um ficheiro hierarquizado não tiver sido recolhido antes de eliminar um ponto final do servidor ou se o ficheiro for corrupto. | Para resolver este problema, ver [ficheiros Tiered não estão acessíveis no servidor depois de eliminar um ponto final do servidor](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) para remover ficheiros hierárquicos que estão órfãos. Se o erro continuar a ocorrer após a remoção de ficheiros hierárquicos órfãos, execute [o chkdsk](/windows-server/administration/windows-commands/chkdsk) no volume. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | O ficheiro não pode ser sincronizado porque o número máximo de ficheiros de conflito foi atingido. O Azure File Sync suporta 100 ficheiros de conflito por ficheiro. Para saber mais sobre conflitos de ficheiros, consulte a Azure File Sync [FAQ](./storage-files-faq.md#afs-conflict-resolution). | Para resolver este problema, reduza o número de ficheiros de conflitos. O ficheiro sincronizará uma vez que o número de ficheiros de conflitos seja inferior a 100. |

#### <a name="handling-unsupported-characters"></a>Manuseamento de caracteres não suportados
Se o **FileSyncErrorsReport.ps1** script PowerShell mostrar erros de sincronização por item devido a caracteres não suportados (código de erro 0x8007007b ou 0x80c80255), deve remover ou renomear os caracteres em falta dos respetivos nomes de ficheiros. O PowerShell provavelmente imprimirá estes caracteres como pontos de interrogação ou retângulos vazios, uma vez que a maioria destes caracteres não têm codificação visual padrão. 
> [!Note]  
> A [Ferramenta de Avaliação](storage-sync-files-planning.md#evaluation-cmdlet) pode ser usada para identificar caracteres que não são suportados. Se o seu conjunto de dados tiver vários ficheiros com caracteres inválidos, utilize o script [ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) para renomear ficheiros que contenham caracteres não suportados.

A tabela abaixo contém todos os caracteres unicode Azure File Sync ainda não suporta.

| Conjunto de carateres | Contagem de caracteres |
|---------------|-----------------|
| <ul><li>0x0000009D (comando do sistema operativo osc)</li><li>0x00000090 (cadeia de controlo do dispositivo DCS)</li><li>0x0000008F (ss3 single shift três)</li><li>0x00000081 (predefinição de octeto alto)</li><li>0x0000007F (del delete)</li><li>0x0000008D (feed da linha inversa ri)</li></ul> | 6 |
| 0x0000FDD0 - 0x0000FDEF (formulários de apresentação árabe-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (especiais) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (noncharacter)</li><li>0x0002FFFE - 0x0002FFFF = 2 (noncharacter)</li><li>0x0003FFFE - 0x0003FFFF = 2 (noncharacter)</li><li>0x0004FFFE - 0x0004FFFF = 2 (noncharacter)</li><li>0x0005FFFE - 0x0005FFFF = 2 (noncharacter)</li><li>0x0006FFFE - 0x0006FFFF = 2 (noncharacter)</li><li>0x0007FFFE - 0x0007FFFF = 2 (noncharacter)</li><li>0x0008FFFE - 0x0008FFFF = 2 (noncharacter)</li><li>0x0009FFFE - 0x0009FFFF = 2 (noncharacter)</li><li>0x000AFFFE - 0x000AFFFF = 2 (noncharacter)</li><li>0x000BFFFE - 0x000BFFFF = 2 (noncharacter)</li><li>0x000CFFFE - 0x000CFFFF = 2 (noncharacter)</li><li>0x000DFFFE - 0x000DFFFF = 2 (noncharacter)</li><li>0x000EFFFE - 0x000EFFFF = 2 (indefinido)</li><li>0x000FFFFE - 0x000FFFFF = 2 (área de utilização privada suplementar)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Erros comuns de sincronização
<a id="-2147023673"></a>**A sessão de sincronização foi cancelada.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimal)** | -2147023673 | 
| **Cadeia do erro** | ERROR_CANCELLED |
| **Remediação necessária** | No |

As sessões de sincronização podem falhar por várias razões, incluindo o servidor ser reiniciado ou atualizado, instantâneos VSS, etc. Embora este erro pareça necessitar de acompanhamento, é seguro ignorar este erro a menos que persista durante um período de várias horas.

<a id="-2147012889"></a>**Não foi possível estabelecer uma ligação com o serviço.**    

| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (decimal)** | -2147012889 | 
| **Cadeia do erro** | WININET_E_NAME_NOT_RESOLVED |
| **Remediação necessária** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**O pedido do utilizador foi estrangulado pelo serviço.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (decimal)** | -2134376372 |
| **Cadeia do erro** | ECS_E_USER_REQUEST_THROTTLED |
| **Remediação necessária** | No |

Não é necessária qualquer ação; o servidor tentará novamente. Se este erro persistir durante várias horas, crie um pedido de suporte.

<a id="-2134364043"></a>**O sync está bloqueado até que a deteção de alteração complete a restauração do post**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (decimal)** | -2134364043 |
| **Cadeia do erro** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Remediação necessária** | No |

nenhuma ação necessária. Quando uma partilha de ficheiros ou ficheiros (ponto final da nuvem) é restaurada usando a Azure Backup, a sincronização fica bloqueada até que a deteção de alterações esteja concluída na partilha de ficheiros Azure. A deteção de alterações é executada imediatamente após o restauro ser concluído e a duração é baseada no número de ficheiros na partilha de ficheiros.

<a id="-2147216747"></a>**O Sync falhou porque a base de dados de sincronização foi descarregada.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (decimal)** | -2147216747 |
| **Cadeia do erro** | SYNC_E_METADATA_INVALID_OPERATION |
| **Remediação necessária** | No |

Este erro normalmente ocorre quando uma aplicação de cópia de segurança cria um instantâneo do VSS e a base de dados de sincronização é descarregada. Se este erro persistir durante várias horas, crie um pedido de suporte.

<a id="-2134364065"></a>**O Sync não pode aceder à partilha de ficheiros Azure especificada no ponto final da nuvem.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimal)** | -2134364065 |
| **Cadeia do erro** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Remediação necessária** | Yes |

Este erro ocorre porque o agente do Azure File Sync não consegue aceder à partilha de ficheiros do Azure, o que pode dever-se à partilha de ficheiros do Azure ou ao anfitrião da conta de armazenamento já não existir. Pode resolver este erro ao realizar os passos seguintes:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Certifique-se de que a partilha de ficheiros Azure existe.](#troubleshoot-azure-file-share)
3. [Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento.](#troubleshoot-rbac)
4. [Verifique se as definições da rede virtual e da firewall na conta de armazenamento estão configuradas corretamente (se ativadas)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**O Sync falhou porque o pedido não está autorizado a realizar esta operação.**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (decimal)** | -2134351804 |
| **Cadeia do erro** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Remediação necessária** | Yes |

Este erro ocorre porque o agente Azure File Sync não está autorizado a aceder à partilha de ficheiros Azure. Pode resolver este erro ao realizar os passos seguintes:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Certifique-se de que a partilha de ficheiros Azure existe.](#troubleshoot-azure-file-share)
3. [Verifique se as definições da rede virtual e da firewall na conta de armazenamento estão configuradas corretamente (se ativadas)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Certifique-se de que o Azure File Sync tem acesso à conta de armazenamento.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**O nome da conta de armazenamento utilizado não pôde ser resolvido.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (decimal)** | -2134364064 |
| **Cadeia do erro** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Remediação necessária** | Yes |

1. Verifique se pode resolver o nome DNS de armazenamento do servidor.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
3. [Verifique se as definições da rede virtual e da firewall na conta de armazenamento estão configuradas corretamente (se ativadas)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Ocorreu um erro desconhecido durante o acesso à conta de armazenamento.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (decimal)** | -2134364022 |
| **Cadeia do erro** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Remediação necessária** | Yes |

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Verifique se as definições da rede virtual e da firewall na conta de armazenamento estão configuradas corretamente (se ativadas)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**O Sync falhou devido ao bloqueio da conta de armazenamento.**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (decimal)** | -2134364014 |
| **Cadeia do erro** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Remediação necessária** | Yes |

Este erro ocorre porque a conta de armazenamento tem um bloqueio de [recursos](../../azure-resource-manager/management/lock-resources.md)apenas de leitura . Para resolver este problema, remova o bloqueio de recursos apenas de leitura na conta de armazenamento. 

<a id="-1906441138"></a>**O Sync falhou devido a um problema com a base de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (decimal)** | -1906441138 |
| **Cadeia do erro** | JET_errWriteConflict |
| **Remediação necessária** | Yes |

Este erro ocorre quando existe um problema com a base de dados interna utilizada pelo Azure File Sync. Quando este problema ocorrer, crie um pedido de apoio e entraremos em contato consigo para o ajudar a resolver este problema.

<a id="-2134364053"></a>**A versão do agente Azure File Sync instalada no servidor não é suportada.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (decimal)** | -2134364053 |
| **Cadeia do erro** | ECS_E_AGENT_VERSION_BLOCKED |
| **Remediação necessária** | Yes |

Este erro ocorrerá se a versão do Azure File Sync instalada no servidor não for suportada. Para resolver este problema, [atualize]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) para uma [versão de agente suportado]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Atingiu o limite de armazenamento de ações de arquivo Azure.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (decimal)** | -2134351810 |
| **Cadeia do erro** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Remediação necessária** | Yes |

Este erro ocorre quando o limite de armazenamento da partilha de ficheiros do Azure é atingido, o que poderá acontecer se uma quota for aplicada a uma partilha de ficheiros do Azure ou se a utilização exceder os limites de uma partilha de ficheiros do Azure. Para obter mais informações, consulte os [limites atuais para uma partilha de ficheiros Azure](storage-files-scale-targets.md).

1. Navegue para o grupo de sincronização dentro do Serviço de Sincronização de Armazenamento.
2. Selecione o ponto final da nuvem dentro do grupo de sincronização.
3. Note o nome da partilha do ficheiro Azure no painel aberto.
4. Selecione a conta de armazenamento ligada. Se esta ligação falhar, a conta de armazenamento referenciada foi removida.

    ![Uma imagem mostrando o painel de detalhes do ponto final da nuvem com um link para a conta de armazenamento.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Selecione **Ficheiros** para ver a lista de ações de ficheiros.
6. Clique nos três pontos no final da linha para a partilha de ficheiros Azure referenciada pelo ponto final da nuvem.
7. Verifique se a **Utilização** está abaixo da **Quota**. Nota, a menos que tenha sido especificada uma quota alternativa, a quota corresponderá ao [tamanho máximo da parte do ficheiro Azure](storage-files-scale-targets.md).

    ![Uma imagem das propriedades de partilha de ficheiros Azure.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Se a partilha de ficheiros estiver cheia e não tiver sido definida uma quota, uma forma possível de resolver este problema será tornar cada subpasta do ponto final do servidor atual no seu próprio ponto final do servidor nos seus próprios grupos de sincronização separados. Desta forma, cada subpasta será sincronizada com as partilhas de ficheiros do Azure individuais.

<a id="-2134351824"></a>**A partilha de ficheiros Azure não pode ser encontrada.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (decimal)** | -2134351824 |
| **Cadeia do erro** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Remediação necessária** | Yes |

Este erro ocorre quando a partilha de ficheiros do Azure não está acessível. Para resolver os problemas:

1. [Verifique se a conta de armazenamento existe.](#troubleshoot-storage-account)
2. [Certifique-se de que a partilha de ficheiros Azure existe.](#troubleshoot-azure-file-share)

Se a partilha de ficheiros Azure foi eliminada, tem de criar uma nova partilha de ficheiros e, em seguida, recriar o grupo de sincronização. 

<a id="-2134364042"></a>**O Sync é interrompido enquanto esta subscrição do Azure está suspensa.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (decimal)** | -2134364042 |
| **Cadeia do erro** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Remediação necessária** | Yes |

Este erro ocorre quando a subscrição do Azure é suspensa. A sincronização será reativada quando a subscrição do Azure for restaurada. Vê [porque é que a minha assinatura Azure está desativada e como a reativo para](../../cost-management-billing/manage/subscription-disabled.md) mais informações?

<a id="-2134375618"></a>**A conta de armazenamento tem uma firewall ou redes virtuais configuradas.**  

| | |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT (decimal)** | -2134375618 |
| **Cadeia do erro** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Remediação necessária** | Yes |

Este erro ocorre quando a partilha de ficheiros do Azure está inacessível devido a uma firewall da conta de armazenamento ou devido à conta de armazenamento pertencer a uma rede virtual. Verifique se as definições de firewall e rede virtual na conta de armazenamento estão configuradas corretamente. Para obter mais informações, consulte [as definições de firewall Configure e rede virtual](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**O Sync falhou devido a um problema com a base de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimal)** | -2134375911 |
| **Cadeia do erro** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Remediação necessária** | No |

Este erro resolve-se normalmente de forma autónoma e poderá ocorrer se existir:

* Um elevado número de alterações de ficheiros em todos os servidores do grupo de sincronização.
* Um grande número de erros em ficheiros e diretórios individuais.

Se este erro persistir por mais de algumas horas, crie um pedido de apoio e entraremos em contacto consigo para o ajudar a resolver este problema.

<a id="-2146762487"></a>**O servidor falhou em estabelecer uma ligação segura. O serviço de nuvem recebeu um certificado inesperado.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (decimal)** | -2146762487 |
| **Cadeia do erro** | CERT_E_UNTRUSTEDROOT |
| **Remediação necessária** | Yes |

Este erro pode ocorrer se a sua organização estiver a utilizar um proxy de terminação TLS ou se uma entidade maliciosa estiver a intercetar o tráfego entre o seu servidor e o serviço Azure File Sync. Se tiver a certeza de que isso é esperado (porque a sua organização está a usar um proxy de terminação TLS), não consegue verificar o certificado com uma substituição de registo.

1. Crie o valor de registo SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Reinicie o serviço de sincronização no servidor registado.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Ao definir este valor de registo, o agente Azure File Sync aceitará qualquer certificado TLS/SSL de confiança local ao transferir dados entre o servidor e o serviço de nuvem.

<a id="-2147012894"></a>**Não foi possível estabelecer uma ligação com o serviço.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (decimal)** | -2147012894 |
| **Cadeia do erro** | WININET_E_TIMEOUT |
| **Remediação necessária** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**O Sync falhou devido a um problema de autenticação.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (decimal)** | -2134375680 |
| **Cadeia do erro** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Remediação necessária** | Yes |

Este erro ocorre normalmente porque a hora do servidor está incorreta. Se o servidor estiver a funcionar numa máquina virtual, verifique se a hora do hospedeiro está correta.

<a id="-2134364040"></a>**O Sync falhou devido à expiração do certificado.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (decimal)** | -2134364040 |
| **Cadeia do erro** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Remediação necessária** | Yes |

Este erro ocorre porque o certificado utilizado para a autenticação expirou.

Para confirmar se o certificado expirou, execute os seguintes passos:  
1. Abra os certificados MMC snap-in, selecione Conta informática e navegue para Certificados (Computador Local)\Personal\Certificates.
2. Verifique se o certificado de autenticação do cliente expirou.

Se o certificado de autenticação de cliente tiver expirado, efetue os seguintes passos para resolver o problema:

1. Verifique se o agente Azure File Sync é a versão 4.0.1.0 ou posterior à instalação.
2. Execute o seguinte comando do PowerShell no servidor: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**O Sync falhou devido ao certificado de autenticação não encontrado.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (decimal)** | -2134375896 |
| **Cadeia do erro** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Remediação necessária** | Yes |

Este erro ocorre porque o certificado utilizado para a autenticação não foi localizado.

Para resolver este problema, realize os passos seguintes:

1. Verifique se o agente Azure File Sync é a versão 4.0.1.0 ou posterior à instalação.
2. Execute o seguinte comando do PowerShell no servidor: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**O Sync falhou devido à identidade de autenticação não encontrada.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (decimal)** | -2134364039 |
| **Cadeia do erro** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Remediação necessária** | Yes |

Este erro ocorre porque a eliminação do ponto final do servidor falhou e o ponto final está agora num estado parcialmente eliminado. Para resolver este problema, repita a eliminação do ponto final do servidor.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**O volume onde o ponto final do servidor está localizado é baixo no espaço do disco.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimal)** | -1906441711 |
| **Cadeia do erro** | JET_errLogDiskFull |
| **Remediação necessária** | Yes |

| | |
|-|-|
| **HRESULT** | 0x80c8031a |
| **HRESULT (decimal)** | -2134375654 |
| **Cadeia do erro** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Remediação necessária** | Yes |

Este erro ocorre porque o volume ficou cheio. Este erro geralmente ocorre porque os ficheiros fora do ponto final do servidor estão a utilizar espaço no volume. Liberte o espaço no volume adicionando pontos finais adicionais do servidor, movendo ficheiros para um volume diferente, ou aumentando o tamanho do volume em que o ponto final do servidor está ligado.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**O serviço ainda não está pronto para sincronizar com este ponto final do servidor.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (decimal)** | -2134364145 |
| **Cadeia do erro** | ECS_E_REPLICA_NOT_READY |
| **Remediação necessária** | No |

Este erro ocorre porque o ponto final da nuvem foi criado com conteúdo já existente na partilha de ficheiros Azure. O Azure File Sync deve digitalizar a partilha de ficheiros Azure para todos os conteúdos antes de permitir que o ponto final do servidor prossiga com a sua sincronização inicial.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**O Sync falhou devido a problemas com muitos ficheiros individuais.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimal)** | -2134375877 |
| **Cadeia do erro** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Remediação necessária** | Yes |

| | |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimal)** | -2134375908 |
| **Cadeia do erro** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Remediação necessária** | Yes |

| | |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimal)** | -2134375853 |
| **Cadeia do erro** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Remediação necessária** | Yes |

As sessões de sincronização falham com um destes erros quando existem muitos ficheiros que não estão a sincronizar com erros por item. Execute os passos documentados na [secção Como ver se existem ficheiros ou pastas específicas que não estão a sincronizar?](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) Para ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED de erro sincronizado, abra um caso de suporte.

> [!NOTE]
> O Azure File Sync cria uma imagem temporária do VSS uma vez por dia no servidor para sincronizar ficheiros que têm pegas abertas.

<a id="-2134376423"></a>**O Sync falhou devido a um problema com o caminho do ponto final do servidor.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (decimal)** | -2134376423 |
| **Cadeia do erro** | ECS_E_SYNC_INVALID_PATH |
| **Remediação necessária** | Yes |

Certifique-se de que o caminho existe, está num volume NTFS local, e não é um ponto de reparse ou ponto final do servidor existente.

<a id="-2134375817"></a>**Sync falhou porque a versão do controlador de filtro não é compatível com a versão do agente**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (decimal)** | -2134375817 |
| **Cadeia do erro** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Remediação necessária** | Yes |

Este erro ocorre porque a versão do controlador de filtro de Arrumo na Cloud (StorageSync.sys) carregada não é compatível com o serviço do Agente de Sincronização de Armazenamento (FileSyncSvc). Se o agente do Azure File Sync tiver sido atualizado, reinicie o servidor para concluir a instalação. Se o erro persistir, desinstale o agente, reinicie o servidor e reinstale o agente do Azure File Sync.

<a id="-2134376373"></a>**O serviço não se encontra disponível neste momento.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (decimal)** | -2134376373 |
| **Cadeia do erro** | ECS_E_SERVICE_UNAVAILABLE |
| **Remediação necessária** | No |

Este erro ocorre porque o serviço Azure File Sync está indisponível. Este erro será resolvido automaticamente quando o serviço do Azure File Sync estiver novamente disponível.

<a id="-2146233088"></a>**O Sync falhou devido a uma exceção.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (decimal)** | -2146233088 |
| **Cadeia do erro** | COR_E_EXCEPTION |
| **Remediação necessária** | No |

Este erro ocorre porque a sincronização falhou devido a uma exceção. Se o erro persistir durante várias horas, por favor crie um pedido de apoio.

<a id="-2134364045"></a>**O Sync falhou porque a conta de armazenamento falhou noutra região.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (decimal)** | -2134364045 |
| **Cadeia do erro** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Remediação necessária** | Yes |

Este erro ocorreu porque a conta de armazenamento efetuou uma ativação pós-falha para outra região. O Azure File Sync não suporta a funcionalidade de ativação pós-falha da conta de armazenamento. As contas de armazenamento que contêm partilhas de ficheiros do Azure e estão a ser utilizadas como pontos finais da cloud no Azure File Sync não devem efetuar a ativação pós-falha. Se a fizer, fará com que a sincronização deixe de funcionar e poderá também causar perdas de dados inesperadas em caso de ficheiros com novo escalão. Para resolver este problema, mova a conta de armazenamento para a região primária.

<a id="-2134375922"></a>**O Sync falhou devido a um problema transitório com a base de dados de sincronização.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimal)** | -2134375922 |
| **Cadeia do erro** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Remediação necessária** | No |

Esse erro ocorre devido a um problema interno na base de dados de sincronização. Este erro será resolvido automaticamente quando a tentar executar novamente a sincronização. Se este erro continuar por um longo período de tempo, crie um pedido de apoio e entraremos em contato consigo para o ajudar a resolver este problema.

<a id="-2134364024"></a>**Sync falhou devido à mudança no inquilino do Azure Ative Directory**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (decimal)** | -2134364024 | 
| **Cadeia do erro** | ECS_E_INVALID_AAD_TENANT |
| **Remediação necessária** | Yes |

Certifique-se de que tem o mais recente agente Azure File Sync. A partir do agente V10, a Azure File Sync suporta mover a subscrição para um inquilino diferente do Azure Ative Directory.
 
Uma vez que tenha a versão mais recente do agente, tem de dar acesso à aplicação Microsoft.StorageSync à conta de armazenamento (ver [Ensure Azure File Sync tem acesso à conta de armazenamento).](#troubleshoot-rbac)

<a id="-2134364010"></a>**O Sync falhou devido à firewall e à exceção da rede virtual não configurada**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (decimal)** | -2134364010 | 
| **Cadeia do erro** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Remediação necessária** | Yes |

Este erro ocorre se as definições de firewall e rede virtual estiverem ativadas na conta de armazenamento e a exceção "Permitir que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento" não é verificada. Para resolver este problema, siga os passos documentados na secção [Configurar as definições da firewall e da rede virtual](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) no guia de implementação.

<a id="-2147024891"></a>**O Sync falhou porque as permissões na pasta de Informação do Volume do Sistema estão incorretas.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (decimal)** | -2147024891 |
| **Cadeia do erro** | ERROR_ACCESS_DENIED |
| **Remediação necessária** | Yes |

Este erro poderá ocorrer se a conta NT AUTHORITY\SYSTEM não tiver permissões para a pasta Informações de Volume do Sistema no volume onde o ponto final do servidor está localizado. Note que se os ficheiros individuais não estiverem sincronizados com ERROR_ACCESS_DENIED, execute os passos documentados na secção de erros de [sincronização de ficheiros/diretórios.](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors)

Para resolver este problema, realize os passos seguintes:

1. Transfira a ferramenta [PsExec](/sysinternals/downloads/psexec).
2. Executar o seguinte comando a partir de um pedido de comando elevado para lançar um pedido de comando usando a conta do sistema: **PsExec.exe -i-s -d cmd** 
3. Na linha de comandos em execução na conta de sistema, execute o seguinte comando para confirmar que a conta NT AUTHORITY\SYSTEM não tem acesso à pasta Informações de Volume de Sistema: **cacls “"letra da unidade:\system volume information” /T /C**
4. Se a conta NT AUTHORITY\SYSTEM não tiver acesso à pasta Informações de Volume do Sistema, execute o seguinte comando: **cacls “letra da unidade:\system volume information” /T /E /G “NT AUTHORITY\SYSTEM:F”**
    - Se o passo 4 falhar com acesso negado, execute o seguinte comando para assumir a propriedade da pasta Informações de Volume do Sistema e, em seguida, repita o passo 4: **takeown /A /R /F “letra da unidade:\System Volume Information”**

<a id="-2134375810"></a>**O Sync falhou porque a partilha de ficheiros Azure foi eliminada e recriada.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (decimal)** | -2134375810 |
| **Cadeia do erro** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Remediação necessária** | Yes |

Este erro ocorre porque o Azure File Sync não suporta a eliminação e a recriação de uma partilha de ficheiros do Azure no mesmo grupo de sincronização. 

Para resolver este problema, elimine e recrie o grupo de sincronização. Para tal, realize os seguintes passos:

1. Elimine todos os pontos finais do servidor no grupo de sincronização.
2. Elimine o ponto final da nuvem. 
3. Elimine o grupo de sincronização.
4. Se o tiering da nuvem foi ativado num ponto final do servidor, elimine os ficheiros hierárquicos órfãos no servidor, executando os passos documentados nos [ficheiros Tiered, não estão acessíveis no servidor após a eliminação de uma](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) secção de ponto final do servidor.
5. Recrie o grupo de sincronização.

<a id="-2145844941"></a>**O Sync falhou porque o pedido HTTP foi redirecionado**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (decimal)** | -2145844941 |
| **Cadeia do erro** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Remediação necessária** | Yes |

Este erro ocorre porque o Azure File Sync não suporta a reorientação HTTP (código de estado 3xx). Para resolver este problema, desative o reorientação HTTP no seu servidor de procuração ou dispositivo de rede.

<a id="-2134364027"></a>**Um intervalo ocorreu durante a transferência de dados offline, mas ainda está em curso.**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (decimal)** | -2134364027 |
| **Cadeia do erro** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Remediação necessária** | No |

Este erro ocorre quando uma operação de ingestão de dados excede o tempo limite. Este erro pode ser ignorado se a sincronização estiver a progredir (o AppliedItemCount é superior a 0). Vês [como monitorizo o progresso de uma sessão de sincronização atual?](#how-do-i-monitor-the-progress-of-a-current-sync-session)

<a id="-2134375814"></a>**O Sync falhou porque o caminho do ponto final do servidor não pode ser encontrado no servidor.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027a |
| **HRESULT (decimal)** | -2134375814 |
| **Cadeia do erro** | ECS_E_SYNC_ROOT_DIRECTORY_NOT_FOUND |
| **Remediação necessária** | Yes |

Este erro ocorre se o diretório utilizado como o caminho do ponto final do servidor for renomeado ou eliminado. Se o diretório tiver sido renomeado, rebatize o diretório de volta ao nome original e reinicie o serviço de Agente de Sincronização de Armazenamento (FileSyncSvc).

Se o diretório foi eliminado, execute os seguintes passos para remover o ponto final do servidor existente e criar um novo ponto final do servidor utilizando um novo caminho:

1. Remova o ponto final do servidor no grupo de sincronização seguindo os passos documentados no [Remover um ponto final do servidor](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint).
2. Crie um novo ponto final do servidor no grupo de sincronização seguindo os passos documentados no [Add a server endpoint](./storage-sync-files-server-endpoint.md#add-a-server-endpoint).

### <a name="common-troubleshooting-steps"></a>Etapas comuns de resolução de problemas
<a id="troubleshoot-storage-account"></a>**Verifique se a conta de armazenamento existe.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navegue para o grupo de sincronização dentro do Serviço de Sincronização de Armazenamento.
2. Selecione o ponto final da nuvem dentro do grupo de sincronização.
3. Note o nome da partilha do ficheiro Azure no painel aberto.
4. Selecione a conta de armazenamento ligada. Se esta ligação falhar, a conta de armazenamento referenciada foi removida.
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
2. Selecione **Ficheiros** para ver a lista de ações de ficheiros.
3. Verifique se a partilha de ficheiros referenciada pelo ponto final da nuvem aparece na lista de ações de ficheiros (deveria ter anotado isto no passo 1 acima).

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
1. Clique no **controlo de acesso (IAM)** na tabela de conteúdos à esquerda.
1. Clique no **separador atribuições de Função** para a lista dos utilizadores e aplicações *(principais serviços)* que têm acesso à sua conta de armazenamento.
1. Verifique se **o Microsoft.StorageSync** ou **o Serviço de Sincronização de Ficheiros Híbridos** (nome de aplicação antiga) aparece na lista com a função **Reader e Data Access.** 

    ![Uma imagem do principal do serviço de serviço de sincronização de ficheiros híbridos no separador de controlo de acesso da conta de armazenamento](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Se **o Microsoft.StorageSync** ou **o Serviço de Sincronização de Ficheiros Híbridos** não aparecerem na lista, execute os seguintes passos:

    - Clique em **Adicionar**.
    - No campo **Role,** selecione **Reader and Data Access**.
    - No campo **Select,** **digite Microsoft.StorageSync,** selecione a função e clique em **Guardar**.

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
Pode utilizar [os ecrãs de ficheiros do Gestor de Recursos do Servidor de Ficheiros (FSRM)](/windows-server/storage/fsrm/file-screening-management) para bloquear ficheiros com caracteres não suportados nos seus nomes a serem criados no servidor. Podes ter de o fazer usando o PowerShell, uma vez que a maioria dos caracteres não suportados não são imprimíveis e por isso precisas de lançar as suas representações hexadémicas como personagens primeiro.

Primeiro criar um Grupo de Ficheiros FSRM utilizando o [cmdlet New-FsrmFileGroup](/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Este exemplo define o grupo para conter apenas dois dos caracteres não suportados, mas pode incluir o maior número de caracteres necessários no seu grupo de ficheiros.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Uma vez definido um Grupo de Ficheiros FSRM, pode criar um ecrã de ficheiro FSRM utilizando o New-FsrmFileScreen cmdlet.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Note que os ecrãs de ficheiros só devem ser utilizados para bloquear a criação de caracteres não suportados pelo Azure File Sync. Se os ecrãs de ficheiros forem utilizados noutros cenários, o sincronização tentará continuar a transferir os ficheiros da partilha de ficheiros Azure para o servidor e será bloqueado devido ao ecrã de ficheiros, resultando em alta margem de dados. 

## <a name="cloud-tiering"></a>Disposição em camadas na cloud 
Existem dois caminhos para falhas no nível da nuvem:

- Os ficheiros podem falhar no nível, o que significa que o Azure File Sync tenta, sem sucesso, nivelar um ficheiro para ficheiros Azure.
- Os ficheiros podem não conseguir ser recolhidos, o que significa que o filtro do sistema de ficheiros Azure File Sync (StorageSync.sys) não consegue descarregar dados quando um utilizador tenta aceder a um ficheiro que foi nivelado.

Existem duas classes principais de falhas que podem acontecer através de qualquer caminho de falha:

- Falhas de armazenamento em nuvem
    - *Problemas de disponibilidade de serviço de armazenamento transitório*. Para mais informações, consulte o [Acordo de Nível de Serviço (SLA) para armazenamento Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Partilha de ficheiros Inacessível Azure*. Esta falha ocorre normalmente quando elimina a partilha de ficheiros Azure quando ainda é um ponto final de nuvem num grupo de sincronização.
    - *Conta de armazenamento inacessível*. Esta falha ocorre normalmente quando elimina a conta de armazenamento enquanto ainda tem uma partilha de ficheiros Azure que é um ponto final em nuvem num grupo de sincronização. 
- Falhas do servidor 
  - *O filtro do sistema de ficheiros Azure File Sync (StorageSync.sys) não está carregado*. Para responder aos pedidos de tiering/recolha, o filtro do sistema de ficheiros Azure File Sync deve ser carregado. O filtro que não está a ser carregado pode acontecer por várias razões, mas a razão mais comum é que um administrador o descarregou manualmente. O filtro do sistema de ficheiros Azure File Sync deve ser sempre carregado para que o Azure File Sync funcione corretamente.
  - *Desaparecido, corrupto ou de outra forma quebrado ponto de reparse*. Um ponto de reparse é uma estrutura de dados especial num ficheiro que consiste em duas partes:
    1. Uma etiqueta de reparse, que indica ao sistema operativo que o filtro do sistema de ficheiros Azure File Sync (StorageSync.sys) pode ter de fazer alguma ação no IO ao ficheiro. 
    2. Reparse dados, que indicam para o sistema de ficheiros filtrar o URI do ficheiro no ponto final de nuvem associado (a partilha de ficheiros Azure). 
        
       A forma mais comum de um ponto de reparaso se corromper é se um administrador tentar modificar a etiqueta ou os seus dados. 
  - *Problemas de conectividade de rede*. Para hierarquar ou recordar um ficheiro, o servidor deve ter conectividade com a Internet.

As secções seguintes indicam como resolver problemas de nivelamento de nuvem e determinar se um problema é um problema de armazenamento na nuvem ou um problema de servidor.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Como monitorizar a atividade de camadas num servidor  
Para monitorizar a atividade de tiering num servidor, utilize o Evento ID 9003, 9016 e 9029 no registo de eventos de Telemetria (localizado em Aplicações e Serviços\Microsoft\FileSync\Agente no Espectador de Eventos).

- O ID 9003 do evento fornece distribuição de erros para um ponto final do servidor. Por exemplo, Contagem total de erros, ErrorCode, etc. Nota: um evento é registado por código de erro.
- O ID 9016 do evento fornece resultados fantasmagóricos para um volume. Por exemplo, Por exemplo, O espaço gratuito por cento é, Número de ficheiros fantasmagóricos em sessão, Número de ficheiros falhados em fantasmas, etc.
- O ID 9029 do evento fornece informações de sessão fantasmagórica para um ponto final do servidor. Por exemplo, Número de ficheiros tentados na sessão, Número de ficheiros hierárquicos na sessão, Número de ficheiros já hierárquicos, etc.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Como monitorizar a atividade de recuperação num servidor
Para monitorizar a atividade de recolha num servidor, utilize o Evento ID 9005, 9006, 9009 e 9059 no registo de eventos da Telemetria (localizado em Aplicações e Serviços\Microsoft\FileSync\Agente no Espectador de Eventos).

- O ID 9005 do evento proporciona fiabilidade de recuperação para um ponto final do servidor. Por exemplo, ficheiros únicos totais acedidos, ficheiros únicos totais com acesso falhado, etc.
- O ID 9006 do evento fornece uma distribuição de erro de recuperação para um ponto final do servidor. Por exemplo, Pedidos Falhados Totais, Código de Erro, etc. Nota: um evento é registado por código de erro.
- O ID 9009 do evento fornece informações de sessão de recuperação para um ponto final do servidor. Por exemplo, DuraçõesSegundos, CondeFilesRecallSucceeded, CondeFilesRecallFailed, etc.
- O ID 9059 do evento fornece distribuição de recolha de aplicações para um ponto final do servidor. Por exemplo, ShareId, Nome de Aplicação e TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Como resolver ficheiros que não conseguem nivelar
Se os ficheiros não forem nivelar para os Ficheiros Azure:

1. No Event Viewer, reveja os registos de eventos de telemetria, operacionais e de diagnóstico, localizados em Aplicações e Serviços\Microsoft\FileSync\Agent. 
   1. Verifique se os ficheiros existem na partilha de ficheiros Azure.

      > [!NOTE]
      > Um ficheiro deve ser sincronizado com uma partilha de ficheiros Azure antes de poder ser nivelado.

   2. Verifique se o servidor tem conectividade com a Internet. 
   3. Verifique se os controladores de filtro Azure File Sync (StorageSync.sys e StorageSyncGuard.sys) estão em execução:
       - Com um pedido de comando elevado, `fltmc` corra. Verifique se os controladores de filtros de sistema de ficheiros StorageSync.sys e StorageSyncGuard.sys estão listados.

> [!NOTE]
> Um ID de evento 9003 é registado uma vez por hora no registo do evento de Telemetria se um ficheiro não tiver o nível (um evento é registado por código de erro). Verifique os [erros de tiering e](#tiering-errors-and-remediation) a secção de reparação para ver se estão listadas as medidas de reparação para o código de erro.

### <a name="tiering-errors-and-remediation"></a>Erros de nivelamento e reparação

| HRESULT | HRESULT (decimal) | Cadeia do erro | Problema | Remediação |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | O ficheiro falhou no nível porque o upload inicial está em andamento. | Nenhuma ação necessária. O ficheiro será tiered uma vez que o upload inicial esteja concluído. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | O ficheiro falhou porque está a ser usado. | Nenhuma ação necessária. O ficheiro será hierárquico quando já não estiver a ser utilizado. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | O ficheiro falhou no nível porque está excluído por sincronização. | Nenhuma ação necessária. Os ficheiros da lista de exclusão de sincronização não podem ser nivelados. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | O ficheiro falhou no nível porque não foi encontrado no servidor. | Nenhuma ação necessária. Se o erro persistir, verifique se o ficheiro existe no servidor. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | O ficheiro não foi tiered porque foi eliminado na partilha de ficheiros Azure. | Nenhuma ação necessária. O ficheiro deve ser eliminado no servidor quando a próxima sessão de sincronização de descarregamento for executado. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | O ficheiro falhou no nível devido a um problema de rede. | Nenhuma ação necessária. Se o erro persistir, verifique a conectividade da rede com a partilha de ficheiros Azure. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | O ficheiro falhou no nível devido a um problema de rede. | Nenhuma ação necessária. Se o erro persistir, verifique a conectividade da rede com a partilha de ficheiros Azure. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | O ficheiro falhou no nível devido ao acesso a erro negado. Este erro pode ocorrer se o ficheiro estiver localizado numa pasta de replicação apenas de leitura DFS-R. | O Azure File Sync não suporta pontos finais do servidor nas pastas de replicação só de leitura do DFS-R. Consulte [o guia de planeamento](./storage-sync-files-planning.md#distributed-file-system-dfs) para obter mais informações. |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | O ficheiro falhou no nível devido a um problema de rede. | Nenhuma ação necessária. Se o erro persistir, verifique a conectividade da rede com a partilha de ficheiros Azure. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | O ficheiro falhou em nivelar porque o tamanho do ficheiro é inferior ao tamanho suportado. | Se a versão do agente for inferior a 9.0, o tamanho mínimo do ficheiro suportado é de 64kb. Se a versão do agente for 9.0 e mais recente, o tamanho mínimo do ficheiro suportado baseia-se no tamanho do cluster do sistema de ficheiros (tamanho do cluster do sistema de ficheiros duplo). Por exemplo, se o tamanho do cluster do sistema de ficheiros for de 4kb, o tamanho mínimo do ficheiro é de 8kb. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | O ficheiro falhou em nivelar devido a um problema de armazenamento Azure. | Se o erro persistir, abra um pedido de apoio. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | O ficheiro falhou porque foi recolhido ao mesmo tempo. | Nenhuma ação necessária. O ficheiro será hierárquico quando a recolha estiver concluída e o ficheiro deixar de ser utilizado. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | O ficheiro falhou no tier porque não sincronizou a partilha de ficheiros Azure. | Nenhuma ação necessária. O ficheiro será tierer uma vez sincronizado com a partilha de ficheiros Azure. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | O ficheiro não foi nivelado porque o controlador de filtro de nivelamento de nuvens (storagesync.sys) não está a funcionar. | Para resolver este problema, abra um pedido de comando elevado e executar o seguinte comando: `fltmc load storagesync`<br>Se o controlador de filtro de armazenamento não conseguir carregar ao executar o comando fltmc, desinstale o agente Azure File Sync, reinicie o servidor e reinstale o agente Azure File Sync. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | O ficheiro falhou na tier devido a um espaço de disco insuficiente no volume onde o ponto final do servidor está localizado. | Para resolver este problema, liberte pelo menos 100 MB de espaço em disco no volume onde se encontra o ponto final do servidor. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | O ficheiro falhou no tier porque não sincronizou a partilha de ficheiros Azure. | Nenhuma ação necessária. O ficheiro será tierer uma vez sincronizado com a partilha de ficheiros Azure. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | O ficheiro falhou porque é um ponto de reparse não suportado. | Se o ficheiro for um ponto de reparase de deduplica de dados, siga os passos no guia de [planeamento](./storage-sync-files-planning.md#data-deduplication) para permitir o suporte à deduplica de dados. Os ficheiros com pontos de re-pedrões que não sejam a Deduplica de Dados não são suportados e não serão nivelados.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | O ficheiro falhou no nível porque foi modificado. | Nenhuma ação necessária. O ficheiro será tiered uma vez que o ficheiro modificado tenha sincronizado com a partilha de ficheiros Azure. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | O ficheiro falhou no tier porque não sincronizou a partilha de ficheiros Azure. | Nenhuma ação necessária. O ficheiro será tierer uma vez sincronizado com a partilha de ficheiros Azure. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | O ficheiro falhou no nível devido a um problema de rede. | Nenhuma ação necessária. Se o erro persistir, verifique a conectividade da rede com a partilha de ficheiros Azure. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | O ficheiro falhou no nível porque foi modificado. | Nenhuma ação necessária. O ficheiro será tiered uma vez que o ficheiro modificado tenha sincronizado com a partilha de ficheiros Azure. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | O ficheiro falhou no nível devido a recursos insuficientes do sistema. | Se o erro persistir, investigue qual a aplicação ou o controlador do modo kernel que está a esgotar os recursos do sistema. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | O ficheiro falhou em nivelar devido a um erro de E/S ao escrever na base de dados de tiering de nuvem. | Se o erro persistir, corram o chkdsk no volume e verifiquem o hardware de armazenamento. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | O ficheiro falhou no nível porque a base de dados de tiering em nuvem não está em funcionamento. | Para resolver este problema, reinicie o serviço de FicheiroSyncSvc ou servidor. Se o erro persistir, corram o chkdsk no volume e verifiquem o hardware de armazenamento. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Como resolver ficheiros que não são recordados  
Se os ficheiros não forem recolhidos:
1. No Event Viewer, reveja os registos de eventos de telemetria, operacionais e de diagnóstico, localizados em Aplicações e Serviços\Microsoft\FileSync\Agent.
    1. Verifique se os ficheiros existem na partilha de ficheiros Azure.
    2. Verifique se o servidor tem conectividade com a Internet. 
    3. Abra o snap-in MMC dos serviços e verifique se o serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) está em execução.
    4. Verifique se os controladores de filtro Azure File Sync (StorageSync.sys e StorageSyncGuard.sys) estão em execução:
        - Com um pedido de comando elevado, `fltmc` corra. Verifique se os controladores de filtros de sistema de ficheiros StorageSync.sys e StorageSyncGuard.sys estão listados.

> [!NOTE]
> Um ID de evento 9006 é registado uma vez por hora no registo do evento de Telemetria se um ficheiro não se lembrar (um evento é registado por código de erro). Verifique os [erros de recolha e a](#recall-errors-and-remediation) secção de reparação para ver se estão listadas as medidas de reparação para o código de erro.

### <a name="recall-errors-and-remediation"></a>Relembram erros e reparação

| HRESULT | HRESULT (decimal) | Cadeia do erro | Problema | Remediação |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | O ficheiro não se recordou devido a um tempo limite de E/S. Este problema pode ocorrer por várias razões: restrições de recursos do servidor, conectividade de rede deficiente ou um problema de armazenamento Azure (por exemplo, estrangulamento). | Nenhuma ação necessária. Se o erro persistir durante várias horas, abra um pedido de suporte. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | O ficheiro não se recordou devido a um problema de rede.  | Se o erro persistir, verifique a conectividade da rede com a partilha de ficheiros Azure. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | O ficheiro não se recordou porque o ponto final do servidor foi eliminado. | Para resolver este problema, ver [ficheiros Tiered não estão acessíveis no servidor depois de eliminar um ponto final do servidor](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | O ficheiro não se relembarou devido a um erro negado de acesso. Este problema pode ocorrer se as definições de firewall e rede virtual na conta de armazenamento estiverem ativadas e o servidor não tiver acesso à conta de armazenamento. | Para resolver este problema, adicione o endereço IP do Servidor ou a rede virtual seguindo os passos documentados na secção [de definições](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) de firewall e rede virtual no guia de implementação. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | O ficheiro não se recordou porque não está acessível na partilha de ficheiros Azure. | Para resolver este problema, verifique se o ficheiro existe na partilha de ficheiros Azure. Se o ficheiro existir na partilha de ficheiros Azure, atualize para a versão mais recente do [agente](./storage-files-release-notes.md#supported-versions)Azure File Sync . |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | O ficheiro não se desendou devido à falha de autorização na conta de armazenamento. | Para resolver este problema, verifique se [o Azure File Sync tem acesso à conta de armazenamento.](?tabs=portal1%252cazure-portal#troubleshoot-rbac) |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | O ficheiro não se recordou porque a partilha de ficheiros Azure não está acessível. | Verifique se a partilha de ficheiros existe e está acessível. Se a partilha de ficheiros foi eliminada e recriada, execute os passos documentados no [Sync porque a partilha de ficheiros Azure foi eliminada e recriou](?tabs=portal1%252cazure-portal#-2134375810) a secção para eliminar e recriar o grupo de sincronização. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | O ficheiro não se recordou devido a recursos insuficientes do sistema. | Se o erro persistir, investigue qual a aplicação ou o controlador do modo kernel que está a esgotar os recursos do sistema. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | O ficheiro não se recordou devido a uma memória insuficiente. | Se o erro persistir, investigue qual a aplicação ou o controlador do modo kernel que está a causar a baixa condição de memória. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | O ficheiro não se recordou devido a um espaço insuficiente em disco. | Para resolver este problema, liberte o espaço do volume movendo os ficheiros para um volume diferente, aumente o tamanho do volume ou force os ficheiros a nivelar utilizando o Invoke-StorageSyncCloudTiering cmdlet. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Os ficheiros em camadas não são acessíveis no servidor após a eliminação de um ponto final do servidor
Os ficheiros hierárquicos num servidor tornar-se-ão inacessíveis se os ficheiros não forem recolhidos antes de eliminarem um ponto final do servidor.

Erros registados se os ficheiros hierárquicos não estiverem acessíveis
- Ao sincronizar um ficheiro, o código de erro -2147942467 (0x80070043 - ERROR_BAD_NET_NAME) é registado no registo de eventos ItemResults
- Ao relembr o ficheiro, o código de erro -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) é registado no registo de eventos RecallResults

O restauro do acesso aos ficheiros em camadas será possível se forem cumpridas as seguintes condições:
- O ponto final do servidor foi eliminado nos últimos 30 dias
- O ponto final da cloud não foi eliminado 
- A partilha de ficheiros não foi eliminada
- O grupo de sincronização não foi eliminado

Se as condições acima forem cumpridas, poderá restaurar o acesso aos ficheiros no servidor através da recriação do ponto final do servidor no mesmo caminho no servidor dentro do mesmo grupo de sincronização no prazo de 30 dias. 

Se as condições acima não forem cumpridas, não será possível restaurar o acesso, uma vez que estes ficheiros em camadas no servidor são, agora, órfãos. Siga as instruções abaixo para remover os ficheiros com camadas órfãs.

**Notas**
- Quando os ficheiros hierárquicos não estiverem acessíveis no servidor, o ficheiro completo deverá continuar a estar acessível se aceder diretamente à partilha de ficheiros Azure.
- Para evitar ficheiros com camadas órfãos no futuro, siga os passos documentados no [Remover um ponto final do servidor](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint) ao eliminar um ponto final do servidor.

<a id="get-orphaned"></a>**Como obter a lista de ficheiros escalão órfãos** 

1. Verifique a versão v5.1 ou posterior do agente Azure File Sync.
2. Executar os seguintes comandos PowerShell para listar ficheiros com camadas órfãs:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Guarde o ficheiro de saída OrphanTieredFiles.txt caso os ficheiros tenham de ser restaurados a partir de cópias de segurança após a sua desespoada.

<a id="remove-orphaned"></a>**Como remover ficheiros hierárquicos órfãos** 

*Opção 1: Eliminar os ficheiros hierárquicos órfãos*

Esta opção elimina os ficheiros hierárquicos órfãos no Servidor do Windows, mas requer a remoção do ponto final do servidor se existir devido a uma recreação após 30 dias ou se estiver ligado a um grupo de sincronização diferente. Os conflitos de ficheiros ocorrerão se os ficheiros forem atualizados no Servidor do Windows ou na partilha de ficheiros Azure antes de o ponto final do servidor ser recriado.

1. Verifique a versão v5.1 ou posterior do agente Azure File Sync.
2. Faça cópia de segurança da partilha de ficheiros Azure e da localização do ponto final do servidor.
3. Remova o ponto final do servidor no grupo de sincronização (se existir) seguindo os passos documentados no [Remover um ponto final do servidor](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint).

> [!Warning]  
> Se o ponto final do servidor não for removido antes de utilizar o cmdlet Remove-StorageSyncOrphanedTieredFiles, a eliminação do ficheiro hierarquizado órfão no servidor eliminará o ficheiro completo na partilha de ficheiros Azure. 

4. Executar os seguintes comandos PowerShell para listar ficheiros com camadas órfãs:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Guarde o ficheiro de saída OrphanTieredFiles.txt caso os ficheiros tenham de ser restaurados a partir de cópias de segurança após a sua desespoada.
6. Executar os seguintes comandos PowerShell para eliminar ficheiros com camadas órfãs:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Notas** 
- Os ficheiros hierárquicos modificados no servidor que não estejam sincronizados com a partilha de ficheiros Azure serão eliminados.
- Os ficheiros hierárquicos que estejam acessíveis (não órfãos) não serão eliminados.
- Os ficheiros não hierárquicos permanecerão no servidor.

7. Opcional: Recriar o ponto final do servidor se for eliminado no passo 3.

*Opção 2: Monte a partilha de ficheiros Azure e copie os ficheiros localmente órfãos no servidor*

Esta opção não requer a remoção do ponto final do servidor, mas requer espaço suficiente para copiar os ficheiros completos localmente.

1. [Monte](./storage-how-to-use-files-windows.md) a partilha de ficheiros Azure no Windows Server que tenha ficheiros órfãs.
2. Executar os seguintes comandos PowerShell para listar ficheiros com camadas órfãs:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Utilize o ficheiro de saída OrphanTieredFiles.txt para identificar ficheiros com camadas órfãs no servidor.
4. Substitua os ficheiros hierárquicos órfãos copiando o ficheiro completo da partilha de ficheiros Azure para o Servidor do Windows.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Como resolver ficheiros de resolução de problemas inesperadamente recordados num servidor  
Antivírus, cópia de segurança e outras aplicações que lêem um grande número de ficheiros causam chamadas não intencionais, a menos que respeitem o atributo offline e ignorem a leitura do conteúdo desses ficheiros. Ignorar os ficheiros offline nos produtos que suportam esta opção ajuda a evitar obtenções não pretendidas durante operações como análises de antivírus ou trabalhos de criação de cópias de segurança.

Consulte o fornecedor do seu software para aprender a configurar a solução do mesmo para ignorar os ficheiros offline.

As chamadas não intencionais também podem ocorrer noutros cenários, como quando está a navegar ficheiros no File Explorer. Abrir uma pasta que tenha ficheiros em camadas na cloud no Explorador de Ficheiros no servidor pode originar obtenções não pretendidas. Isto é ainda mais provável se uma solução de antivírus estiver ativada no servidor.

> [!NOTE]
>Utilize o ID 9059 do evento de Telemetria para determinar que aplicações estão a causar chamadas. Este evento fornece distribuição de recolha de aplicações para um ponto final do servidor e é registado uma vez por hora.

### <a name="tls-12-required-for-azure-file-sync"></a>TLS 1.2 necessário para sincronização de ficheiros Azure

Pode ver as definições de TLS no seu servidor olhando para as [definições de registo](/windows-server/security/tls/tls-registry-settings). 

Se estiver a utilizar um representante, consulte a documentação do seu representante e certifique-se de que está configurada para utilizar o TLS1.2.

## <a name="general-troubleshooting"></a>Resolução geral de problemas
Se encontrar problemas com o Azure File Sync num servidor, comece por completar os seguintes passos:
1. No Event Viewer, reveja os registos de telemetria, operacionais e de diagnóstico do evento.
    - Os problemas de sincronização, tiering e recolha são registados nos registos de telemetria, diagnóstico e eventos operacionais em Aplicações e Serviços\Microsoft\FileSync\Agent.
    - Os problemas relacionados com a gestão de um servidor (por exemplo, definições de configuração) são registados nos registos de eventos operacionais e de diagnóstico em Aplicações e Serviços\Microsoft\FileSync\Management.
2. Verifique se o serviço Azure File Sync está a funcionar no servidor:
    - Abra o snap-in MMC dos serviços e verifique se o serviço de Agente de Sincronização de Armazenamento (FileSyncSvc) está em execução.
3. Verifique se os controladores de filtro Azure File Sync (StorageSync.sys e StorageSyncGuard.sys) estão em execução:
    - Com um pedido de comando elevado, `fltmc` corra. Verifique se os controladores de filtros de sistema de ficheiros StorageSync.sys e StorageSyncGuard.sys estão listados.

Se o problema não for resolvido, execute a ferramenta AFSDiag e envie a sua saída de ficheiro .zip para o engenheiro de suporte atribuído ao seu caso para posterior diagnóstico.

Para executar AFSDiag, execute os passos abaixo.

Para a versão do agente v11 e posteriormente:
1. Abra uma janela PowerShell elevada e, em seguida, executar os seguintes comandos (prima Insira após cada comando):

    > [!NOTE]
    >O AFSDiag criará o diretório de saída e uma pasta temporária no mesmo antes de recolher registos e apagará a pasta temporária após a execução. Especifique um local de saída que não contenha dados.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Reproduza o problema. Quando terminar, insira **D.**
3. Um ficheiro .zip que contenha registos e ficheiros de rastreio é guardado no diretório de saída que especificou. 

Para a versão do agente v10 e mais cedo:
1. Crie um diretório onde a saída AFSDiag será guardada (por exemplo, C:\Output).
    > [!NOTE]
    >O AFSDiag eliminará todos os conteúdos do diretório de saídas antes de recolher registos. Especifique um local de saída que não contenha dados.
2. Abra uma janela PowerShell elevada e, em seguida, executar os seguintes comandos (prima Insira após cada comando):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Para o nível de vestígio do modo de kernel Azure File Sync, introduza **1** (salvo especificação em contrário, para criar mais vestígios verbosos) e, em seguida, prima Enter.
4. Para o nível de rastreio do modo de utilizador Azure File Sync, introduza **1** (salvo especificação em contrário, para criar mais vestígios verbosos) e, em seguida, prima Enter.
5. Reproduza o problema. Quando terminar, insira **D.**
6. Um ficheiro .zip que contenha registos e ficheiros de rastreio é guardado no diretório de saída que especificou.


## <a name="see-also"></a>Ver também
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
- [Ficheiros Azure frequentemente fazem perguntas](storage-files-faq.md)
- [Resolver problemas de Ficheiros do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Problemas na resolução de ficheiros Azure em Linux](storage-troubleshoot-linux-file-connection-problems.md)
