---
title: Criar uma conta NetApp para aceder ao Azure NetApp Files | Microsoft Docs
description: Descreve como aceder ao Azure NetApp Files e criar uma conta NetApp, para poder configurar um conjunto de capacidade e criar um volume.
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
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 25cae58663f6fa7ef27995c10509eb33e49dd4c7
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522821"
---
# <a name="create-a-netapp-account"></a>Criar uma conta NetApp
Criar uma conta NetApp permite-lhe configurar um conjunto de capacidade e, em seguida, criar um volume. Pode utilizar o painel do Azure NetApp Files para criar uma nova conta NetApp.

## <a name="before-you-begin"></a>Antes de começar
Deve receber uma mensagem de e-mail da equipa do Azure NetApp ficheiros que confirmam que lhe foi concedido acesso ao serviço. Ver [submeter um pedido de lista de espera para acessar o serviço](azure-netapp-files-register.md#waitlist).

Tem também de ter registado a sua subscrição para utilizar o fornecedor de recursos de NetApp. Ver [registar o fornecedor de recursos de NetApp](azure-netapp-files-register.md#resource-provider).

## <a name="steps"></a>Passos 

1. Inicie sessão no Portal do Azure. 
2. Aceda ao painel do Azure NetApp Files através de um dos seguintes métodos:  
   * Procure **Azure NetApp Files** na caixa de pesquisa do portal do Azure.  
   * Clique em **Todos os serviços** na navegação e, em seguida, filtre para Azure NetApp Files.  

   Pode adicionar o painel do Azure NetApp Files aos favoritos ao clicar no ícone de estrela junto ao mesmo. 

3. Clique em **+ Adicionar** para criar uma nova conta NetApp.  
   É apresentada a janela da nova conta NetApp.  

4. Forneça as seguintes informações para a conta NetApp: 
   * **Nome da conta**  
     Especifique um nome exclusivo para a subscrição.
   * **Subscrição**  
     Selecione uma subscrição a partir das subscrições existentes.
   * **Grupo de recursos**   
     Utilize um Grupo de Recursos existente ou crie um novo.
   * **Localização**  
     Selecione a região onde quer ter a conta e os respetivos recursos subordinados a localizar.  

     ![Nova conta NetApp](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. Clique em **Criar**.     
   A conta NetApp que criou aparece agora no painel do Azure NetApp Files. 

> [!NOTE] 
> Se não foi concedido acesso ao serviço ficheiros do Azure NetApp, receberá o seguinte erro ao tentar criar a primeira conta de NetApp:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>Passos Seguintes  

[Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)

