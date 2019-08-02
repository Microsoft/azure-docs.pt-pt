---
title: Adicionar/remover um ponto de extremidade do servidor Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba o que deve ser considerado ao planejar uma implantação de arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3a4a77a9b4cdd30c04de4c4eb9d8731c1ea0616c
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699255"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Adicionar/remover um ponto de extremidade do Sincronização de Arquivos do Azure Server
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Ele faz isso transformando os servidores do Windows em um cache rápido do compartilhamento de arquivos do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

Um *ponto de extremidade do servidor* representa um local específico em um *servidor registrado*, como uma pasta em um volume do servidor ou a raiz do volume. Vários pontos de extremidade de servidor podem existir no mesmo volume se seus namespaces não estiverem sobrepostos (por exemplo, F:\sync1 e F:\sync2). Você pode configurar políticas de camadas de nuvem individualmente para cada ponto de extremidade do servidor. Se você adicionar um local de servidor com um conjunto existente de arquivos como um ponto de extremidade de servidor a um grupo de sincronização, esses arquivos serão mesclados com outros arquivos que já estão em outros pontos de extremidade no grupo de sincronização.

Consulte [como implantar sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md) para obter informações sobre como implantar sincronização de arquivos do Azure de ponta a ponta.

## <a name="prerequisites"></a>Pré-requisitos
Para criar um ponto de extremidade do servidor, primeiro você deve garantir que os critérios a seguir sejam atendidos: 
- O servidor tem o agente de Sincronização de Arquivos do Azure instalado e foi registrado. As instruções para instalar o agente de Sincronização de Arquivos do Azure podem ser encontradas no artigo [registrar/cancelar o registro de um servidor com o sincronização de arquivos do Azure](storage-sync-files-server-registration.md) . 
- Verifique se um serviço de sincronização de armazenamento foi implantado. Consulte [como implantar sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md) para obter detalhes sobre como implantar um serviço de sincronização de armazenamento. 
- Verifique se um grupo de sincronização foi implantado. Saiba como [criar um grupo de sincronização](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Verifique se o servidor está conectado à Internet e se o Azure está acessível. Usamos a porta 443 para toda a comunicação entre o servidor e nosso serviço.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto final de servidor
Para adicionar um ponto de extremidade do servidor, navegue até o grupo de sincronização desejado e selecione "Adicionar ponto de extremidade do servidor".

![Adicionar um ponto final de servidor novo no painel do grupo de sincronização](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

As informações a seguir são necessárias em **Adicionar ponto de extremidade do servidor**:

- **Servidor registrado**: O nome do servidor ou cluster no qual criar o ponto de extremidade do servidor.
- **Caminho**: O caminho no Windows Server a ser sincronizado como parte do grupo de sincronização.
- **Camadas de nuvem**: Uma opção para habilitar ou desabilitar a disposição em camadas de nuvem. Quando habilitado, a disposição em camadas da *nuvem irá* recriar os arquivos para seus compartilhamentos de arquivos do Azure. Isso converte os compartilhamentos de arquivos locais em um cache, em vez de uma cópia completa do conjunto de recursos, para ajudá-lo a gerenciar a eficiência do espaço em seu servidor.
- **Espaço livre no volume**: a quantidade de espaço livre a ser reservada no volume que o ponto de extremidade do servidor reside. Por exemplo, se o espaço livre do volume for definido como 50% em um volume com um único ponto de extremidade do servidor, aproximadamente metade da quantidade de dados será em camadas para os arquivos do Azure. Independentemente de a disposição em camadas da nuvem estar habilitada, o compartilhamento de arquivos do Azure sempre terá uma cópia completa dos dados no grupo de sincronização.

Selecione **criar** para adicionar o ponto de extremidade do servidor. Os arquivos dentro de um namespace de um grupo de sincronização agora serão mantidos em sincronia. 

## <a name="remove-a-server-endpoint"></a>Remover um ponto de extremidade do servidor
Se desejar interromper o uso de Sincronização de Arquivos do Azure para um determinado ponto de extremidade do servidor, você poderá remover o ponto de extremidade do servidor. 

> [!Warning]  
> Não tente solucionar problemas com sincronização, camadas de nuvem ou qualquer outro aspecto de Sincronização de Arquivos do Azure removendo e recriando o ponto de extremidade do servidor, a menos que seja explicitamente instruído por um engenheiro da Microsoft. A remoção de um ponto de extremidade do servidor é uma operação destrutiva, e os arquivos em camadas no ponto de extremidade do servidor não serão "reconectados" aos seus locais no compartilhamento de arquivos do Azure depois que o ponto de extremidade do servidor for recriado, o que resultará em erros de sincronização. Observe também que os arquivos em camadas que existem fora do namespace do ponto de extremidade do servidor podem ser permanentemente perdidos. Arquivos em camadas podem existir no ponto de extremidade do servidor, mesmo se a camada de nuvem nunca tiver sido habilitada.

Para garantir que todos os arquivos em camadas sejam recuperados antes de remover o ponto de extremidade do servidor, desabilite a camada de nuvem no ponto de extremidade do servidor e execute o seguinte cmdlet do PowerShell para recuperar todos os arquivos em camadas no namespace do ponto de extremidade do servidor:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Note]  
> Se o volume local que hospeda o servidor não tiver espaço livre suficiente para recuperar todos os dados em camadas, o `Invoke-StorageSyncFileRecall` cmdlet falhará.  

Para remover o ponto de extremidade do servidor:

1. Navegue até o serviço de sincronização de armazenamento no qual o servidor está registrado.
2. Navegue até o grupo de sincronização desejado.
3. Remova o ponto de extremidade do servidor que você deseja no grupo de sincronização no serviço de sincronização de armazenamento. Isso pode ser feito clicando com o botão direito do mouse no ponto de extremidade do servidor relevante no painel grupo de sincronização.

    ![Removendo um ponto de extremidade do servidor de um grupo de sincronização](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Passos Seguintes
- [Registrar/cancelar o registro de um servidor com o Sincronização de Arquivos do Azure](storage-sync-files-server-registration.md)
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
