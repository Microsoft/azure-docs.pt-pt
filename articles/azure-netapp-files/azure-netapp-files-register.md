---
title: Registo de Ficheiros Azure NetApp [ Registo de Ficheiros Azure NetApp] Microsoft Docs
description: Descreve como se registar para utilizar ficheiros Azure NetApp.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79274063"
---
# <a name="register-for-azure-netapp-files"></a>Registar nos Azure NetApp Files

> [!IMPORTANT] 
> Antes de registar o fornecedor de recursos Do Azure NetApp Files, deve ter recebido um e-mail da equipa de Ficheiros Azure NetApp confirmando que lhe foi concedido acesso ao serviço. 

Neste artigo, aprenda a registar-se para o Azure NetApp Files para que possa começar a utilizar o serviço.

## <a name="waitlist"></a>Enviar um pedido de lista de espera para aceder ao serviço

1. Envie um pedido de lista de espera para aceder ao serviço Deficheiros Azure NetApp através da página de submissão da lista de espera do [Azure NetApp Files](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u). 

    A inscrição na lista de espera não garante o acesso imediato ao serviço. 

2. Aguarde um e-mail oficial de confirmação da equipa de Ficheiros Azure NetApp antes de continuar com outras tarefas. 

## <a name="resource-provider"></a>Registe o Fornecedor de Recursos NetApp

Para utilizar o serviço, tem de registar o Fornecedor de Recursos Azure para ficheiros Azure NetApp.

> [!NOTE] 
> Poderá registar com sucesso o Fornecedor de Recursos NetApp mesmo sem ter acesso ao serviço. No entanto, sem autorização de acesso, qualquer portal Azure ou pedido da API para criar uma conta NetApp ou qualquer outro recurso do Azure NetApp Files será rejeitado com o seguinte erro:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. A partir do portal Azure, clique no ícone Azure Cloud Shell no canto superior direito:

      ![Ícone de concha de nuvem azure](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Se tiver várias subscrições na sua conta Azure, selecione a que foi listada para Ficheiros Azure NetApp:
    
        az account set --subscription <subscriptionId>

3. Na consola Azure Cloud Shell, introduza o seguinte comando para verificar se a sua subscrição foi listada como branca:
    
        az feature list | grep NetApp

   A saída de comando aparece da seguinte forma:
   
       "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
       "name": "Microsoft.NetApp/ANFGA" 
       
   `<SubID>` é o seu ID de subscrição.

    Se não vir o nome da funcionalidade `Microsoft.NetApp/ANFGA`, não tem acesso ao serviço. Pare neste degrau. Siga as instruções em Enviar um pedido de lista de [espera para aceder ao serviço](#waitlist) para solicitar acesso ao serviço antes de continuar. 

4. Na consola Azure Cloud Shell, insira o seguinte comando para registar o Fornecedor de Recursos Azure: 
    
        az provider register --namespace Microsoft.NetApp --wait

   O parâmetro `--wait` instrui a consola a aguardar que o registo esteja concluído. O processo de registo pode levar algum tempo a concluir.

5. Na consola Azure Cloud Shell, insira o seguinte comando para verificar se o Fornecedor de Recursos Azure foi registado: 
    
        az provider show --namespace Microsoft.NetApp

   A saída de comando aparece da seguinte forma:
   
        {
        "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
        "namespace": "Microsoft.NetApp", 
        "registrationState": "Registered", 
        "resourceTypes": […. 

   `<SubID>` é o seu ID de subscrição.  O valor do parâmetro `state` indica `Registered`.

6. A partir do portal Azure, clique na lâmina **de Assinaturas.**
7. Na lâmina de Subscrição, clique no ID de subscrição. 
8. Nas definições da subscrição, clique em **fornecedores** de recursos para verificar se o Microsoft.NetApp Provider indica o estado registado: 

      ![Microsoft.NetApp registado](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Passos seguintes

[Criar uma conta NetApp](azure-netapp-files-create-netapp-account.md)