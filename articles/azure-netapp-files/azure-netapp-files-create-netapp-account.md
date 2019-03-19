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
ms.openlocfilehash: bb43a75b6a221c15c8724302797d04c22e04c8d2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113644"
---
# <a name="create-a-netapp-account"></a>Criar uma conta NetApp
Criar uma conta NetApp permite-lhe configurar um conjunto de capacidade e, em seguida, criar um volume. Pode utilizar o painel do Azure NetApp Files para criar uma nova conta NetApp.

## <a name="before-you-begin"></a>Antes de começar
Tem de ter registado a sua subscrição para utilizar o fornecedor de recursos de NetApp e a funcionalidade de pré-visualização pública.

[Registre-se para os ficheiros do Azure NetApp](azure-netapp-files-register.md)

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

## <a name="next-steps"></a>Passos Seguintes  

[Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)

