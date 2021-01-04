---
title: Registre-se para ficheiros Azure NetApp / Microsoft Docs
description: Saiba como se registar nos Ficheiros Azure NetApp, submetendo um pedido de lista de espera e registando o Fornecedor de Recursos Azure para ficheiros Azure NetApp.
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
ms.topic: how-to
ms.date: 06/09/2020
ms.author: b-juche
ms.openlocfilehash: a61d6ba1f908a7dae56db066bfae329fc26f1c5c
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696130"
---
# <a name="register-for-azure-netapp-files"></a>Registar nos Azure NetApp Files

> [!IMPORTANT] 
> Antes de registar o fornecedor de recursos Azure NetApp Files, deve ter recebido um e-mail da equipa do Azure NetApp Files confirmando que lhe foi concedido acesso ao serviço. 

Neste artigo, saiba como se registar nos Ficheiros Azure NetApp para que possa começar a utilizar o serviço.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Submeter um pedido de lista de espera para aceder ao serviço

1. Vá à página seguinte e envie um pedido de lista de espera para aceder ao serviço Azure NetApp Files:  
    [**Página de submissão da lista de espera Azure NetApp Files**](https://aka.ms/azurenetappfiles) 

    A inscrição na lista de espera não garante o acesso imediato ao serviço. 

2. Aguarde um e-mail oficial de confirmação da equipa do Azure NetApp Files antes de continuar com outras tarefas. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>Registar o Fornecedor de Recursos NetApp

Para utilizar o serviço, tem de registar o Fornecedor de Recursos Azure para ficheiros Azure NetApp.

> [!NOTE] 
> Poderá registar com sucesso o Fornecedor de Recursos NetApp, mesmo sem ter acesso ao serviço. No entanto, sem autorização de acesso, qualquer portal Azure ou pedido de API para criar uma conta NetApp ou qualquer outro recurso Azure NetApp Files será rejeitado com o seguinte erro:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. A partir do portal Azure, clique no ícone Azure Cloud Shell no canto superior direito:

      ![Ícone de concha de nuvem de azure](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Se tiver várias subscrições na sua conta Azure, selecione a que foi aprovada para ficheiros Azure NetApp:
    
    ```azurepowershell
    az account set --subscription <subscriptionId>
    ```

3. Na consola Azure Cloud Shell, insira o seguinte comando para verificar se a sua subscrição foi aprovada:
    
    ```azurepowershell
    az feature list | grep NetApp
    ```

   A saída do comando aparece da seguinte forma:
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` é o seu ID de assinatura.

    Se não vir o nome da `Microsoft.NetApp/ANFGA` funcionalidade, não tem acesso ao serviço. Pare neste degrau. Siga as instruções em [Enviar um pedido de lista de espera para aceder ao serviço](#waitlist) para solicitar o acesso do serviço antes de continuar. 

4. Na consola Azure Cloud Shell, insira o seguinte comando para registar o Fornecedor de Recursos Azure: 
    
    ```azurepowershell
    az provider register --namespace Microsoft.NetApp --wait
    ```

   O `--wait` parâmetro instrui a consola a aguardar a conclusão do registo. O processo de registo pode demorar algum tempo a ser concluído.

5. Na consola Azure Cloud Shell, insira o seguinte comando para verificar se o Fornecedor de Recursos Azure foi registado: 
    
    ```azurepowershell
    az provider show --namespace Microsoft.NetApp
    ```

   A saída do comando aparece da seguinte forma:
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` é o seu ID de assinatura.  O `state` valor do parâmetro indica `Registered` .

6. A partir do portal Azure, clique na lâmina **de Assinaturas.**
7. Na lâmina de subscrições, clique no ID de subscrição. 
8. Nas definições da subscrição, clique nos **fornecedores** de Recursos para verificar se o Fornecedor Microsoft.NetApp indica o estado registado: 

      ![Registado Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Passos seguintes

[Criar uma conta NetApp](azure-netapp-files-create-netapp-account.md)
