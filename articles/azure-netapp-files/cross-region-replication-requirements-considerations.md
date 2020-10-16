---
title: Requisitos e considerações para a utilização da replicação transversal de volume do Azure NetApp Files Microsoft Docs
description: Descreve os requisitos e considerações para a utilização da funcionalidade de replicação transversal de volume dos Ficheiros Azure NetApp.
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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: f2a50872fdb71419a0c3f068712ec67523a098e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708783"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Requisitos e considerações para a utilização da replicação entre regiões 

Note os seguintes requisitos e considerações sobre a utilização da funcionalidade [de replicação transversal](cross-region-replication-create-peering.md) de volume dos Ficheiros Azure NetApp:  

## <a name="requirements-and-considerations"></a>Requisitos e considerações 

* A funcionalidade de replicação entre regiões está atualmente em visualização pública. Tem de submeter um pedido de lista de espera para aceder à funcionalidade através da [página de submissão da lista de espera de replicação da Azure NetApp Files](https://aka.ms/anfcrrpreviewsignup). Aguarde um e-mail oficial de confirmação da equipa do Azure NetApp Files antes de utilizar a funcionalidade de replicação entre regiões.
* A replicação do Azure NetApp Files só está disponível em certos pares de regiões fixas. Consulte [os pares da região apoiada.](cross-region-replication-introduction.md#supported-region-pairs) 
* Os volumes SMB são suportados juntamente com volumes NFS. A replicação de volumes SMB requer uma ligação ative directory nas contas NetApp de origem e destino. A ligação AD de destino deve ter acesso aos servidores DNS ou controladores de domínio ADDS que estejam acessíveis a partir da sub-rede delegada na região de destino. Para obter mais informações, consulte [requisitos para ligações de Diretório Ativo](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* A conta de destino deve ser numa região diferente da região de volume de origem. Também pode selecionar uma conta NetApp existente numa região diferente.  
* A replicação do Azure NetApp Files não suporta atualmente várias subscrições; todas as replicações devem ser efetuadas numa única subscrição.
* Pode configurar um máximo de cinco volumes para replicação numa única subscrição por região. Pode abrir um bilhete de apoio para solicitar um aumento da quota padrão de cinco volumes de destino de replicação (por subscrição numa região). 
* Pode haver um atraso até cinco minutos para a interface refletir uma nova imagem adicionada no volume de origem.  
* As topologias em cascata e de ventilação não são suportadas.
* A replicação de volume configurante para volumes de origem criados a partir de instantâneo não é suportada neste momento.
* Depois de configurar a replicação entre regiões, o processo de replicação cria *instantâneos* para fornecer referências entre o volume de origem e o volume de destino. Os instantâneos snapmirror são ciclamados automaticamente quando um novo é criado para cada transferência incremental. Não é possível eliminar instantâneos até que a relação de replicação e o volume sejam eliminados. 
* Pode eliminar instantâneos manuais no volume de origem de uma relação de replicação quando a relação de replicação está ativa ou quebrada, e também após a relação de replicação ser eliminada. Não é possível eliminar instantâneos manuais para o volume de destino até que a relação de replicação seja quebrada.

## <a name="next-steps"></a>Passos seguintes
* [Criar o peering de replicação](cross-region-replication-create-peering.md)
* [Apresentar o estado de funcionamento da relação de replicação](cross-region-replication-display-health-status.md)
* [Gerir a recuperação após desastre](cross-region-replication-manage-disaster-recovery.md)
* [Métricas de replicação de volume](azure-netapp-files-metrics.md#replication)
* [Resolver problemas da replicação entre regiões](troubleshoot-cross-region-replication.md)


