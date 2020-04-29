---
title: Adicione/remova um ponto final do servidor De sincronização de ficheiros Azure [ Microsoft Docs
description: Saiba o que considerar ao planear uma implementação de Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 684b30a24e049722cb531cbc84e3a2cd90912ec8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255109"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Adicione/remova um ponto final do servidor De sincronização de ficheiros Azure
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Fá-lo transformando os seus Servidores Windows numa cache rápida da partilha de ficheiros Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

Um *ponto final* do servidor representa uma localização específica num servidor *registado,* como uma pasta num volume de servidor ou a raiz do volume. Vários pontos finais do servidor podem existir no mesmo volume se os seus espaços de nome não se sobrepuserem (por exemplo, F:\sync1 e F:\sync2). Pode configurar as políticas de tiering em nuvem individualmente para cada ponto final do servidor. Se adicionar uma localização do servidor com um conjunto de ficheiros existente como ponto final do servidor a um grupo de sincronização, esses ficheiros serão fundidos com quaisquer outros ficheiros já em outros pontos finais do grupo de sincronização.

Consulte [como implementar o Azure File Sync](storage-sync-files-deployment-guide.md) para obter informações sobre como implementar o Azure File Sync de ponta a ponta.

## <a name="prerequisites"></a>Pré-requisitos
Para criar um ponto final do servidor, deve primeiro certificar-se de que os seguintes critérios são cumpridos: 
- O servidor tem o agente Dessincronização de ficheiros Azure instalado e foi registado. As instruções para a instalação do Agente de Sincronização de Ficheiros Azure podem ser encontradas no [Registo/desregistar um servidor com](storage-sync-files-server-registration.md) o artigo Do Sincron de Ficheiros Azure. 
- Certifique-se de que foi implantado um Serviço de Sincronização de Armazenamento. Consulte [como implementar o Azure File Sync](storage-sync-files-deployment-guide.md) para obter detalhes sobre como implementar um Serviço de Sincronização de Armazenamento. 
- Certifique-se de que foi implantado um grupo de sincronização. Saiba como [criar um grupo de sincronização.](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint)
- Certifique-se de que o servidor está ligado à internet e que o Azure está acessível. Utilizamos a porta 443 para toda a comunicação entre o servidor e o nosso serviço.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto final de servidor
Para adicionar um ponto final do servidor, navegue para o grupo de sincronização pretendido e selecione "Adicionar ponto final do servidor".

![Adicionar um ponto final de servidor novo no painel do grupo de sincronização](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

As seguintes informações são necessárias no ponto final do **servidor Add:**

- **Servidor registado**: O nome do servidor ou cluster para criar o ponto final do servidor ligado.
- **Caminho**: O caminho no Servidor do Windows a sincronizar como parte do grupo de sincronização.
- **Cloud Tiering**: Um interruptor para ativar ou desativar o tiering da nuvem. Quando ativado, o tiering em nuvem *irá diferenciar* ficheiros para as suas ações de ficheiro Sino. Isto converte as partilhas de ficheiros no local numa cache, em vez de uma cópia completa do conjunto de dados, para ajudá-lo a gerir a eficiência do espaço no seu servidor.
- **Espaço Livre**de Volume : a quantidade de espaço livre para reservar no volume que o ponto final do servidor reside. Por exemplo, se o espaço livre de volume for definido para 50% num volume com um único ponto final do servidor, cerca de metade da quantidade de dados será nivida para Ficheiros Azure. Independentemente de o tiering em nuvem estar ativado, a sua quota de ficheiroS Azure tem sempre uma cópia completa dos dados do grupo de sincronização.

Selecione **Criar** para adicionar o ponto final do servidor. Os ficheiros dentro de um espaço de nome de um grupo de sincronização serão agora mantidos em sincronização. 

## <a name="remove-a-server-endpoint"></a>Remover um ponto final do servidor
Se desejar descontinuar a utilização do Azure File Sync para um determinado ponto final do servidor, pode remover o ponto final do servidor. 

> [!Warning]  
> Não tente resolver problemas com sincronização, tiering em nuvem ou qualquer outro aspeto do Azure File Sync removendo e recriando o ponto final do servidor a menos que explicitamente instruído por um engenheiro da Microsoft. Remover um ponto final do servidor é uma operação destrutiva, e os ficheiros hierárquicos dentro do ponto final do servidor não serão "reconectados" às suas localizações na partilha de ficheiros Azure após a recriação do ponto final do servidor, o que resultará em erros de sincronização. Note também que os ficheiros hierárquicos que existam fora do espaço de nome final do servidor podem ser permanentemente perdidos. Os ficheiros hierárquicos podem existir dentro do ponto final do servidor, mesmo que o tiering de nuvem nunca tenha sido ativado.

Para garantir que todos os ficheiros hierárquicos são recolhidos antes de remover o ponto final do servidor, desative o tiering da nuvem no ponto final do servidor e, em seguida, execute o seguinte cmdlet PowerShell para relembrar todos os ficheiros hierárquicos dentro do espaço de nome final do servidor:

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -Order CloudTieringPolicy
```
Especificar `-Order CloudTieringPolicy` recordará primeiro os ficheiros mais recentemente modificados.
Outros parâmetros opcionais mas úteis a considerar são:
* `-ThreadCount`determina quantos ficheiros podem ser recolhidos em paralelo.
* `-PerFileRetryCount`determina com que frequência uma retirada será tentada de um ficheiro que está atualmente bloqueado.
* `-PerFileRetryDelaySeconds`determina o tempo em segundos entre a tentativa de relembrar as tentativas e deve ser sempre utilizado em combinação com o parâmetro anterior.

> [!Note]  
> Se o volume local que hospeda o servidor não tiver espaço `Invoke-StorageSyncFileRecall` livre suficiente para recordar todos os dados hierárquicos, o cmdlet falha.  

Para remover o ponto final do servidor:

1. Navegue para o Serviço de Sincronização de Armazenamento onde o seu servidor está registado.
2. Navegue para o grupo de sincronização desejado.
3. Remova o ponto final do servidor desejado no grupo de sincronização no Serviço de Sincronização de Armazenamento. Isto pode ser realizado clicando à direita no ponto final do servidor relevante no painel do grupo de sincronização.

    ![Remover um ponto final do servidor de um grupo de sincronização](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Passos seguintes
- [Registar/desregistar um servidor com Sincronização de Ficheiros Azure](storage-sync-files-server-registration.md)
- [Planear uma implementação da Sincronização de Ficheiros do Azure](storage-sync-files-planning.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
