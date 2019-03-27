---
title: Adicionar ou remover um ponto de extremidade do servidor de sincronização de ficheiros do Azure | Documentos da Microsoft
description: Saiba o que considerar quando planear uma implementação de ficheiros do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: aa5f8aaef21967a23505c785eb8ef811cf5767cc
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486450"
---
# <a name="addremove-an-azure-file-sync-server-endpoint"></a>Adicionar ou remover um ponto de extremidade do servidor de sincronização de ficheiros do Azure
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Ele faz isso ao transformar os seus servidores do Windows numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

Uma *ponto final do servidor* representa uma localização específica num *servidor registado*, como uma pasta num volume do servidor ou na raiz do volume. Vários pontos de extremidade do servidor podem existir no mesmo volume se seus espaços de nomes não há sobreposição (por exemplo, F:\sync1 e F:\sync2). Pode configurar políticas de camadas de cloud individualmente para cada ponto de final do servidor. Se adicionar uma localização do servidor com um conjunto existente de ficheiros como um ponto de final de servidor para um grupo de sincronização, esses arquivos serão intercalados com quaisquer outros arquivos que já estão outros pontos de extremidade no grupo de sincronização.

Ver [como implementar o Azure File Sync](storage-sync-files-deployment-guide.md) para obter informações sobre como implementar o Azure File Sync ponto-a-ponto.

## <a name="prerequisites"></a>Pré-requisitos
Para criar um ponto final do servidor, primeiro tem de se certificar de que são cumpridos os seguintes critérios: 
- O servidor tiver instalado o agente de sincronização de ficheiros do Azure e foi registado. Instruções para instalar o agente de sincronização de ficheiros do Azure podem ser encontradas no [registar/anular o registo de um servidor com o Azure File Sync](storage-sync-files-server-registration.md) artigo. 
- Certifique-se de que um serviço de sincronização de armazenamento foi implementado. Ver [como implementar o Azure File Sync](storage-sync-files-deployment-guide.md) para obter detalhes sobre como implementar um serviço de sincronização de armazenamento. 
- Certifique-se de que um grupo de sincronização foi implementado. Saiba como [criar um grupo de sincronização](storage-sync-files-deployment-guide.md#create-a sync-group-and-a-cloud-endpoint).
- Certifique-se de que o servidor está ligado à internet e que o Azure está acessível. Podemos utilizar a porta 443 para todas as comunicações entre o servidor e o nosso serviço.

## <a name="add-a-server-endpoint"></a>Adicionar um ponto final de servidor
Para adicionar um ponto final do servidor, navegue para o grupo de sincronização pretendido e selecione "Adicionar ponto final do servidor".

![Adicionar um ponto final de servidor novo no painel do grupo de sincronização](media/storage-sync-files-server-endpoint/add-server-endpoint-1.png)

As seguintes informações são necessárias em **adicionar ponto final de servidor**:

- **Servidor registado**: O nome do servidor ou cluster para criar o ponto final do servidor.
- **Caminho**: O caminho no servidor do Windows sejam sincronizadas como parte do grupo de sincronização.
- **Em camada de cloud**: Um comutador para ativar ou desativar a cloud em camadas. Quando ativada, será camada da cloud *escalão* ficheiros para as partilhas de ficheiros do Azure. Desta forma, as partilhas de ficheiros no local para uma cache, em vez de uma cópia completa do conjunto de dados, para ajudar a gerir a eficiência de espaço no seu servidor.
- **Espaço livre do volume**: a quantidade de espaço livre para reservar no volume que reside o ponto final do servidor. Por exemplo, se o espaço livre de volume é definido como 50% num volume com um ponto de final de servidor único, aproximadamente metade a quantidade de dados será colocado em camadas para ficheiros do Azure. Independentemente de se cloud em camadas é ativada, a partilha de ficheiros do Azure tem sempre uma cópia completa dos dados no grupo de sincronização.

Selecione **criar** para adicionar o ponto final do servidor. Os ficheiros dentro de um espaço de nomes de um grupo de sincronização irão agora ser mantidos em sincronização. 

## <a name="remove-a-server-endpoint"></a>Remover um ponto final do servidor
Se desejar interromper a utilizar o Azure File Sync para um ponto de extremidade determinado servidor, pode remover o ponto final do servidor. 

> [!Warning]  
> Não tente resolver problemas com a sincronização, na cloud em camadas ou qualquer outro aspecto do Azure File Sync ao remover e recriar o ponto final do servidor, a menos que explicitamente instruído para por um engenheiro da Microsoft. Remover um ponto final do servidor é uma operação destrutiva e ficheiros em camadas no ponto de final do servidor serão não ser "novamente" para as respetivas localizações na partilha de ficheiros do Azure depois do ponto final do servidor é recriado, o que resultará em erros em sincronia. Observe também, os ficheiros em camadas existentes fora do espaço de nomes de ponto final de servidor podem ser permanentemente perdidos. Ficheiros em camada podem existir na se até mesmo do ponto final de servidor na cloud em camadas nunca foi ativada.

Para garantir que todos os ficheiros em camadas são removidos antes de remover o ponto final do servidor, desativar a camada do ponto final do servidor de cloud e, em seguida, execute o seguinte cmdlet do PowerShell para recuperar todos os ficheiros em camadas no seu espaço de nomes de ponto final de servidor:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```

> [!Note]  
> Se o volume local que aloja o servidor não tem espaço livre suficiente para lembrar todos os dados em camadas, o `Invoke-StorageSyncFileRecall` cmdlet falhar.  

Para remover o ponto final do servidor:

1. Navegue para o serviço de sincronização de armazenamento onde o servidor está registado.
2. Navegue para o grupo de sincronização pretendido.
3. Remova o ponto final do servidor desejado no grupo de sincronização no serviço de sincronização de armazenamento. Isso pode ser feito clicando com o ponto final de servidor relevante no painel do grupo de sincronização.

    ![Remover um ponto final do servidor de um grupo de sincronização](media/storage-sync-files-server-endpoint/remove-server-endpoint-1.png)

## <a name="next-steps"></a>Passos Seguintes
- [Registre-se/anular o registo de um servidor com o Azure File Sync](storage-sync-files-server-registration.md)
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Monitorizar a sincronização de ficheiros do Azure](storage-sync-files-monitoring.md)
