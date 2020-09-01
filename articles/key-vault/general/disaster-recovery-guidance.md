---
title: O que fazer se uma rutura do serviço Azure afetar o Cofre da Chave Azure - Cofre da Chave Azure Microsoft Docs
description: Saiba o que fazer de uma rutura de serviço Azure afeta o Cofre da Chave Azure.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: sudbalas
ms.openlocfilehash: 35814f34550ac7bf4ad85a96d0838df62fe63be6
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89073187"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Disponibilidade e redundância do Azure Key Vault

O Azure Key Vault dispõe de várias camadas de redundância para garantir que as suas chaves e segredos permanecem disponíveis para a sua aplicação, mesmo que os componentes individuais do serviço falhem.

O conteúdo do seu cofre-chave é replicado dentro da região e numa região secundária a pelo menos 250 km de distância, mas dentro da mesma geografia para manter alta durabilidade das suas chaves e segredos. Consulte o documento das [regiões emparelhadas Azure](../../best-practices-availability-paired-regions.md) para obter detalhes sobre pares de regiões específicas.

Se os componentes individuais dentro do serviço de cofre chave falharem, os componentes alternativos dentro da região entram para servir o seu pedido para garantir que não há degradação da funcionalidade. Não precisa de tomar nenhuma medida para iniciar este processo, acontece automaticamente e será transparente para si.

No caso raro de uma região inteira de Azure não estar disponível, os pedidos que você faz do Azure Key Vault naquela região são automaticamente encaminhados (*falhados*) para uma região secundária. Quando a região primária está novamente disponível, os pedidos são encaminhados de volta (*falhados*de volta - para a região primária. Mais uma vez, não precisa de tomar nenhuma medida porque isto acontece automaticamente.

Através deste design de alta disponibilidade, o Azure Key Vault não requer tempo de inatividade para atividades de manutenção.

Há algumas ressalvas a ter em conta:

* Em caso de falha de uma região, pode levar alguns minutos para o serviço falhar. Os pedidos que são feitos durante este período antes do failover podem falhar.
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

