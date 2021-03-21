---
title: Adicionar/remover um ponto final do servidor Azure File Sync | Microsoft Docs
description: Aprenda a adicionar ou remover um ponto final do servidor com Azure File Sync. Um ponto final do servidor é uma determinada localização num servidor registado, como uma pasta num volume de servidor.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f75f0d1ae12db11590f8ce62f3c7b4c0f3e12817
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013943"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Adicionar/remover um ponto final do servidor Azure File Sync
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Fá-lo transformando os seus Servidores Windows numa cache rápida da sua partilha de ficheiros Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

Um *ponto final do servidor* representa uma localização específica num servidor *registado*, como uma pasta num volume de servidor ou a raiz do volume. Vários pontos finais do servidor podem existir no mesmo volume se os seus espaços de nome não estiverem sobrepostos (por exemplo, F:\sync1 e F:\sync2) e cada ponto final estiver sincronizado com um grupo de sincronização único. Pode configurar as políticas de tiering de nuvem individualmente para cada ponto final do servidor. Se adicionar uma localização do servidor com um conjunto de ficheiros existente como ponto final do servidor a um grupo de sincronização, esses ficheiros serão fundidos com quaisquer outros ficheiros já em outros pontos finais do grupo de sincronização.

Veja [como implementar o Azure File Sync](storage-sync-files-deployment-guide.md) para obter informações sobre como implementar o Azure File Sync de ponta a ponta.

## <a name="prerequisites"></a>Pré-requisitos
Para criar um ponto final do servidor, deve primeiro certificar-se de que os seguintes critérios são cumpridos: 
- O servidor tem o agente Azure File Sync instalado e foi registado. As instruções para a instalação do Agente de Sincronização de Ficheiros Azure podem ser encontradas no [Registo/não registro um servidor com](storage-sync-files-server-registration.md) o artigo Azure File Sync. 
- Certifique-se de que foi implantado um Serviço de Sincronização de Armazenamento. Veja [como implementar o Azure File Sync](storage-sync-files-deployment-guide.md) para obter detalhes sobre como implementar um Serviço de Sincronização de Armazenamento. 
- Certifique-se de que foi implantado um grupo de sincronização. Saiba como [criar um grupo de sincronização.](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint)
- Certifique-se de que o servidor está ligado à internet e que o Azure está acessível. Utilizamos a porta 443 para toda a comunicação entre o servidor e o nosso serviço.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto final de servidor
Para adicionar um ponto final do servidor, navegue para o grupo de sincronização pretendido e selecione "Adicionar ponto final do servidor".

![Adicionar um ponto final de servidor novo no painel do grupo de sincronização](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

As seguintes informações são necessárias no **ponto final do servidor Add:**

- **Servidor registado**: O nome do servidor ou cluster para criar o ponto final do servidor ligado.
- **Caminho**: O caminho no Servidor do Windows a ser sincronizado como parte do grupo de sincronização.
- **Cloud Tiering**: Um interruptor para ativar ou desativar o nível da nuvem. Quando ativado, o tiering em nuvem irá *tiering* ficheiros para as suas ações de ficheiroS Azure. Isto converte as ações de ficheiros no local numa cache, em vez de uma cópia completa do conjunto de dados, para ajudá-lo a gerir a eficiência do espaço no seu servidor.
- **Volume Espaço Livre**: a quantidade de espaço livre para reservar no volume em que reside o ponto final do servidor. Por exemplo, se o espaço livre de volume estiver definido para 50% num volume com um único ponto final do servidor, cerca de metade da quantidade de dados será diferenciada para Ficheiros Azure. Independentemente de o tiering de nuvem estar ativado, a sua partilha de ficheiros Azure tem sempre uma cópia completa dos dados do grupo de sincronização.

Selecione **Criar** para adicionar o ponto final do servidor. Os ficheiros dentro de um espaço de nome de um grupo de sincronização serão agora mantidos em sincronização. 

## <a name="remove-a-server-endpoint"></a>Remover um ponto final do servidor
Se desejar descontinuar a utilização do Azure File Sync para um determinado ponto final do servidor, pode remover o ponto final do servidor. 

> [!Warning]  
> Não tente resolver problemas com sincronização, nivelamento em nuvem ou qualquer outro aspeto do Azure File Sync removendo e recriando o ponto final do servidor, a menos que seja explicitamente instruído por um engenheiro da Microsoft. A remoção de um ponto final do servidor é uma operação destrutiva, e os ficheiros hierárquicos dentro do ponto final do servidor não serão "reconectados" às suas localizações na partilha de ficheiros Azure após a recriação do ponto final do servidor, o que resultará em erros de sincronização. Note também que os ficheiros hierárquicos que existem fora do espaço de nome do ponto final do servidor podem ser permanentemente perdidos. Podem existir ficheiros tiered dentro do ponto final do servidor, mesmo que o tiering da nuvem nunca tenha sido ativado.

Para garantir que todos os ficheiros hierárquicos são recolhidos antes de remover o ponto final do servidor, desative o nível de nuvem no ponto final do servidor e, em seguida, execute o seguinte cmdlet PowerShell para recordar todos os ficheiros hierárquicos dentro do espaço de nome do ponto final do servidor:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
Especificar `-Order CloudTieringPolicy` irá recordar primeiro os ficheiros modificados mais recentemente.
Outros parâmetros opcionais mas úteis a considerar são:
* `-ThreadCount` determina quantos ficheiros podem ser recolhidos em paralelo.
* `-PerFileRetryCount`determina quantas vezes uma recuperação será tentada de um ficheiro que está atualmente bloqueado.
* `-PerFileRetryDelaySeconds`determina o tempo em segundos entre a tentativa de recuperação e deve ser sempre utilizado em combinação com o parâmetro anterior.

> [!Note]  
> Se o volume local que hospeda o servidor não tiver espaço livre suficiente para recordar todos os dados hierárquicos, o `Invoke-StorageSyncFileRecall` cmdlet falha.  

Para remover o ponto final do servidor:

1. Navegue para o Serviço de Sincronização de Armazenamento onde o seu servidor está registado.
2. Navegue para o grupo de sincronização desejado.
3. Remova o ponto final do servidor que deseja no grupo de sincronização no Serviço de Sincronização de Armazenamento. Isto pode ser conseguido clicando à direita no ponto final do servidor relevante no painel de grupo sincronizado.

    ![Remover um ponto final do servidor de um grupo de sincronização](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Passos seguintes
- [Registar/não registar um servidor com Azure File Sync](storage-sync-files-server-registration.md)
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
