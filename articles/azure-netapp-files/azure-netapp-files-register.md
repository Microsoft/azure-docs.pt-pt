---
title: Registe-se para os ficheiros NetApp do Azure | Documentos da Microsoft
description: Descreve como submeter um pedido para se inscrever no serviço de ficheiros do Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2018
ms.author: b-juche
ms.openlocfilehash: dc24165d3a5ee4d72c259753edd4028752828dce
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57772296"
---
# <a name="register-for-azure-netapp-files"></a>Registar nos Azure NetApp Files
Antes de utilizar ficheiros de NetApp do Azure, tem de submeter um pedido de inscrição no serviço de ficheiros do Azure NetApp.  Após a inscrição, em seguida, registe a utilizar o serviço.

## <a name="request-to-enroll-in-the-service"></a>Pedido de inscrição no serviço
Tem de ser parte do programa de pré-visualização pública e na lista de permissões para aceder ao fornecedor de recursos de Microsoft.NetApp. Para obter detalhes sobre como aderir ao programa de Pré-visualização Pública, veja a [página de inscrição na Pré-visualização Pública do Azure NetApp Files](https://aka.ms/nfspublicpreview). 


## <a name="register-the-netapp-resource-provider"></a>Registar o fornecedor de recursos de NetApp

Para utilizar o serviço, tem de registar o fornecedor de recursos do Azure para ficheiros de NetApp do Azure. 

1. No portal do Azure, clique no ícone do Azure Cloud Shell no canto superior direito:

      ![Ícone do Azure do Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Se tiver várias subscrições na sua conta do Azure, selecione aquele que tem sido na lista de permissões para ficheiros de NetApp do Azure:
    
        az account set --subscription <subscriptionId>

3. Na consola do Azure Cloud Shell, introduza o seguinte comando para verificar se a sua subscrição foram na lista de permissões:
    
        az feature list | grep NetApp

   A saída de comando será da seguinte forma:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/publicPreviewADC",  
       "name": "Microsoft.NetApp/publicPreviewADC" 
       
   `<SubID>` é o ID da subscrição.

4. Na consola do Azure Cloud Shell, introduza o seguinte comando para registar o fornecedor de recursos do Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait

   O `--wait` parâmetro ensina a consola a aguardar a conclusão do registo. O processo de registo pode demorar algum tempo a concluir.

5. Na consola do Azure Cloud Shell, introduza o seguinte comando para verificar que foi registado o fornecedor de recursos do Azure: 
    
        az provider show --namespace Microsoft.NetApp

  A saída de comando será da seguinte forma:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` é o ID da subscrição.  O `state` indica o valor do parâmetro `Registered`.

6. A partir do portal do Azure, clique nas **subscrições** painel.
7. No painel de subscrições, clique em seu ID de subscrição. 
8. Nas definições da subscrição, clique em **fornecedores de recursos** para verificar que o fornecedor de Microsoft.NetApp indica o estado registado: 

      ![Registered Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Passos Seguintes  

[Criar uma conta NetApp](azure-netapp-files-create-netapp-account.md)