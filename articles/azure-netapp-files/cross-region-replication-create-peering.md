---
title: Criar redes de replicação para ficheiros Azure NetApp / Microsoft Docs
description: Descreve como criar a replicação de volume a espreitar os Ficheiros Azure NetApp para configurar a replicação transversal.
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: df70f8a37a7223119068afd323583ea6126ca542
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708823"
---
# <a name="create-replication-peering-for-azure-netapp-files"></a>Criar olhando para a replicação para ficheiros Azure NetApp

> [!IMPORTANT]
> A funcionalidade de replicação entre regiões está atualmente em visualização pública. Tem de submeter um pedido de lista de espera para aceder à funcionalidade através da [página de submissão da lista de espera de replicação da Azure NetApp Files](https://aka.ms/anfcrrpreviewsignup). Aguarde um e-mail oficial de confirmação da equipa do Azure NetApp Files antes de utilizar a funcionalidade de replicação entre regiões.

Este artigo mostra-lhe como configurar a replicação transversal através da criação de um olhar de replicação. 

A configuração do estomo de replicação permite-lhe replicar assíncronamente os dados de um volume de Ficheiros Azure NetApp (fonte) para outro volume de Ficheiros Azure NetApp (destino). O volume de origem e o volume de destino devem ser implantados em regiões distintas. O nível de serviço para o pool de capacidade de destino pode corresponder ao do pool de capacidade de origem, ou pode selecionar um nível de serviço diferente.   

A replicação do Azure NetApp Files não suporta atualmente várias subscrições; todas as replicações devem ser efetuadas numa única subscrição.

Antes de começar, certifique-se de que reviu os [requisitos e considerações para a utilização da replicação entre regiões](cross-region-replication-requirements-considerations.md).  

## <a name="locate-the-source-volume-resource-id"></a>Localizar o ID do recurso de volume de origem  

Tem de obter o ID de recursos do volume de origem que pretende replicar. 

1. Aceda ao volume de origem e selecione **Propriedades** em Definições para exibir o ID do recurso de volume de origem.   
    ![Localizar iD de recursos de volume de origem](../media/azure-netapp-files/cross-region-replication-source-volume-resource-id.png)
 
2. Copie a identificação do recurso para a área de transferência.  Precisará dele mais tarde.

## <a name="create-the-data-replication-volume-the-destination-volume"></a>Criar o volume de replicação de dados (o volume de destino)

É necessário criar um volume de destino onde pretende que os dados do volume de origem sejam replicados.  Antes de poder criar um volume de destino, precisa de ter uma conta NetApp e um pool de capacidade na região de destino. 

1. A conta de destino deve ser numa região diferente da região de volume de origem. Se necessário, crie uma conta NetApp na região de Azure para ser utilizada para replicação seguindo os passos na [Criação de uma conta NetApp](azure-netapp-files-create-netapp-account.md).   
Também pode selecionar uma conta NetApp existente numa região diferente.  

2. Se necessário, crie um pool de capacidade na conta NetApp recém-criada, seguindo os passos na [Configuração de um pool de capacidade](azure-netapp-files-set-up-capacity-pool.md).   

    Também pode selecionar um pool de capacidade existente para hospedar o volume de destino de replicação.  

    O nível de serviço para o pool de capacidade de destino pode corresponder ao do pool de capacidade de origem, ou pode selecionar um nível de serviço diferente.

3. Delege uma sub-rede na região para ser usada para replicação seguindo os passos em [Delegado uma sub-rede para Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

4. Crie o volume de replicação de dados selecionando **Volumes** em Serviço de Armazenamento na conta de destino NetApp. Em seguida, clique no botão **+ Adicionar replicação de dados.**  

    ![Adicionar replicação de dados](../media/azure-netapp-files/cross-region-replication-add-data-replication.png)
 
5. Na página Criar um Volume que aparece, complete os seguintes campos no separador **Básicos:**
    * Nome do volume
    * Piscina de capacidade
    * Quota de volume
        > [!NOTE] 
        > Recomenda-se que o tamanho da quota de volume seja o do volume de volume.
    * Rede virtual 
    * Sub-rede

    Para obter mais informações sobre os campos, consulte [Criar um volume NFS](azure-netapp-files-create-volumes.md#create-an-nfs-volume). 

6. No separador **Protocolo,** selecione o mesmo protocolo que o volume de origem.  
Para o protocolo NFS, certifique-se de que as regras de política de exportação satisfazem os requisitos de quaisquer anfitriões da rede remota que acedam à exportação.  

7. No separador **Tags,** crie pares chave/valor, se necessário.  

8. No separador **Replication,** cole no ID do recurso de volume de origem que obteve no [Localizar o ID do recurso de volume de origem](#locate-the-source-volume-resource-id)e, em seguida, selecione o calendário de replicação pretendido. As opções para o horário de replicação incluem: a cada 10 minutos, de hora a hora, diariamente, semanal e mensal.  

    ![Criar replicação de volume](../media/azure-netapp-files/cross-region-replication-create-volume-replication.png)

9. Clique **em Rever + Criar**e, em seguida, clique em **Criar** para criar o volume de replicação de dados.   

    ![Rever e criar replicação](../media/azure-netapp-files/cross-region-replication-review-create-replication.png)

## <a name="authorize-replication-from-the-source-volume"></a>Autorizar a replicação do volume de origem  

Para autorizar a replicação, é necessário obter o ID de recursos do volume de destino de replicação e colá-lo no campo Desígnio do volume de fonte de replicação. 

1. No portal Azure, navegue para os Ficheiros Azure NetApp.

2. Vá para a conta de destino NetApp e piscina de capacidade de destino onde o volume de destino de replicação está localizado.

3. Selecione o volume de destino de replicação, vá a **Propriedades** em Definições e localize o **ID** de recurso do volume de destino. Copie o ID do recurso de volume de destino para a área de transferência.

    ![ID de recursos de propriedades](../media/azure-netapp-files/cross-region-replication-properties-resource-id.png) 
 
4. Nos Ficheiros Azure NetApp, vá à conta de fonte de replicação e ao pool de capacidade de origem. 

5. Localize o volume de fonte de replicação e selecione-o. Vá à **Replicação** no Serviço de Armazenamento e clique **em "Autorizo".**

    ![Autorizar a replicação](../media/azure-netapp-files/cross-region-replication-authorize.png) 

6. No campo Authorize, cole o ID do recurso de replicação de destino que obteve no Passo 3 e, em seguida, clique em **OK**.

## <a name="next-steps"></a>Passos seguintes  

* [Replicação entre regiões](cross-region-replication-introduction.md)
* [Requisitos e considerações para a utilização da replicação entre regiões](cross-region-replication-requirements-considerations.md)
* [Apresentar o estado de funcionamento da relação de replicação](cross-region-replication-display-health-status.md)
* [Métricas de replicação de volume](azure-netapp-files-metrics.md#replication)
* [Gerir a recuperação após desastre](cross-region-replication-manage-disaster-recovery.md)
* [Resolução de problemas transversal à replicação da região](troubleshoot-cross-region-replication.md)

