---
title: Disponibilidade e redundância do Cofre chave Azure - Azure Key Vault | Microsoft Docs
description: Saiba mais sobre a disponibilidade e redundância do Azure Key Vault.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: sudbalas
ms.openlocfilehash: d66fe736936963e601aad7cba7bdaa94f0c3ec3f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96518452"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Disponibilidade e redundância do Azure Key Vault

O Azure Key Vault dispõe de várias camadas de redundância para garantir que as suas chaves e segredos permanecem disponíveis para a sua aplicação, mesmo que os componentes individuais do serviço falhem.

> [!NOTE]
> Este guia aplica-se a cofres. As piscinas HSM geridas utilizam um modelo diferente de alta disponibilidade e recuperação de desastres. Consulte [o Guia de Recuperação de Desastres gerido do HSM](../managed-hsm/disaster-recovery-guide.md) para obter mais informações.

O conteúdo do seu cofre-chave é replicado dentro da região e numa região secundária a pelo menos 250 km de distância, mas dentro da mesma geografia para manter alta durabilidade das suas chaves e segredos. Para obter detalhes sobre pares de regiões específicas, consulte [regiões emparelhadas Azure.](../../best-practices-availability-paired-regions.md) A exceção ao modelo de regiões emparelhadas é o Brasil Sul, que permite apenas manter os dados residentes no Brasil Sul. O Brasil Sul usa o armazenamento redundante da zona (ZRS) para replicar os seus dados três vezes dentro da única localização/região.   

Se os componentes individuais dentro do serviço de cofre chave falharem, os componentes alternativos dentro da região entram para servir o seu pedido para garantir que não há degradação da funcionalidade. Não precisa de tomar nenhuma medida para iniciar este processo, acontece automaticamente e será transparente para si.

No caso raro de uma região inteira de Azure estar indisponível, os pedidos que você faz do Azure Key Vault naquela região são automaticamente encaminhados (*falhados*) para uma região secundária, exceto no caso da região Sul do Brasil. Quando a região primária está novamente disponível, os pedidos são encaminhados de volta (*falhados* de volta - para a região primária. Mais uma vez, não precisas de tomar nenhuma ação porque isto acontece automaticamente.

Na região Do Brasil Sul, você deve planear a recuperação dos seus cofres chave Azure em um cenário de falha da região. Para apoiar e restaurar o seu cofre de chaves Azure para uma região à sua escolha, complete os passos que são detalhados no [cofre de azure Key Vault](backup.md). 

Através deste design de alta disponibilidade, o Azure Key Vault não requer tempo de inatividade para atividades de manutenção.

Há algumas ressalvas a ter em conta:

* Em caso de falha de uma região, pode levar alguns minutos para o serviço falhar. Os pedidos que são feitos durante este período antes do failover podem falhar.
* Se estiver a utilizar um link privado para ligar ao cofre da chave, pode demorar até 20 minutos para que a ligação seja restabelecida em caso de falha. 
* Durante o failover, o seu cofre chave está apenas em modo de leitura. Os pedidos que são suportados neste modo são:
  * Certificados de lista
  * Obter certificados
  * Listar segredos
  * Obter segredos
  * Chaves da lista
  * Obtenha (propriedades de) chaves
  * Encriptar
  * Desencriptar
  * Embrulhar
  * Desembrulhar
  * Verificação
  * Assinar
  * Backup

* Durante o failover, não poderá fazer alterações nas propriedades do cofre chave. Não será possível alterar a política de acesso ou configurações e configurações de firewall.

* Depois de uma falha de failover, todos os tipos de pedido (incluindo pedidos de leitura e escrita) *estão* disponíveis.
