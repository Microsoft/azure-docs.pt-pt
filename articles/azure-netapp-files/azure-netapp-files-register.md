---
title: Registe-se para os ficheiros NetApp do Azure | Documentos da Microsoft
description: Descreve como registar-se a utilização do Azure dos ficheiros NetApp.
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
ms.date: 05/06/2019
ms.author: b-juche
ms.openlocfilehash: 6f5d84dea2e835fd12a062b628181354295ed9f6
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299219"
---
# <a name="register-for-azure-netapp-files"></a>Registar nos Azure NetApp Files

> [!IMPORTANT] 
> Antes de registar o fornecedor de recursos de ficheiros do Azure NetApp, tem de ter recebido uma mensagem de e-mail da equipa do Azure NetApp ficheiros que confirmam que lhe foi concedido acesso ao serviço. 

Neste artigo, saiba como registar-se para os ficheiros do Azure NetApp, para que pode começar a utilizar o serviço.

## <a name="waitlist"></a>Submeter um pedido de lista de espera para acessar o serviço

1. Submeter um pedido de lista de espera para acessar o serviço de ficheiros de NetApp do Azure através da [página de submissão de lista de espera de ficheiros do Azure NetApp](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    Inscrição de lista de espera não garante o acesso ao serviço de imediato. 

2. Aguarde um e-mail de confirmação oficial da equipa do Azure NetApp ficheiros antes de continuar com outras tarefas. 

## <a name="resource-provider"></a>Registar o fornecedor de recursos de NetApp

Para utilizar o serviço, tem de registar o fornecedor de recursos do Azure para ficheiros de NetApp do Azure.

> [!NOTE] 
> Será capaz de se registar com êxito o fornecedor de recursos de NetApp, mesmo sem lhes ser concedido acesso para o serviço. No entanto, sem a autorização de acesso, qualquer portal do Azure ou o pedido de API para criar uma conta de NetApp ou qualquer outro recurso de ficheiros do Azure NetApp será rejeitado com o seguinte erro:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. No portal do Azure, clique no ícone do Azure Cloud Shell no canto superior direito:

      ![Ícone do Azure do Cloud Shell](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Se tiver várias subscrições na sua conta do Azure, selecione aquele que tem sido na lista de permissões para ficheiros de NetApp do Azure:
    
        az account set --subscription <subscriptionId>

3. Na consola do Azure Cloud Shell, introduza o seguinte comando para verificar se a sua subscrição foram na lista de permissões:
    
        az feature list | grep NetApp

   A saída de comando será da seguinte forma:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>` é o ID da subscrição.

    Se não vir o nome da funcionalidade `Microsoft.NetApp/ANFGA`, não tem acesso ao serviço. Pare esta etapa. Siga as instruções em [submeter um pedido de lista de espera para acessar o serviço](#waitlist) para pedir acesso de serviço antes de continuar. 

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