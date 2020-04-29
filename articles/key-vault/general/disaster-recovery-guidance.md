---
title: O que fazer em caso de interrupção do serviço Azure que afeta o Cofre chave Azure - Cofre chave Azure [ Azure Key Vault ] Microsoft Docs
description: Saiba o que fazer em caso de uma interrupção de serviço Azure que afete o Cofre chave Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 96929dcbe3d51589b0c3c0df89671dadb20e37cc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422950"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Disponibilidade e redundância do Cofre-Chave Azure

O Azure Key Vault apresenta várias camadas de redundância para garantir que as suas chaves e segredos permanecem disponíveis para a sua aplicação, mesmo que os componentes individuais do serviço falhem.

O conteúdo do seu cofre chave é replicado dentro da região e para uma região secundária a pelo menos 250 km de distância, mas dentro da mesma geografia. Isto mantém alta durabilidade das suas chaves e segredos. Consulte o documento das [regiões emparelhadas do Azure](../../best-practices-availability-paired-regions.md) para obter detalhes sobre pares específicos da região.

Se os componentes individuais dentro do serviço de cofre saem, os componentes alternativos dentro da região interem para cumprir o seu pedido para se certificar de que não há degradação da funcionalidade. Não precisas de tomar nenhuma ação para desencadear isto. Acontece automaticamente e será transparente para si.

No caso raro de toda uma região de Azure não estar disponível, os pedidos que faz do Cofre-Chave Azure naquela região são automaticamente encaminhados *(falhados*) para uma região secundária. Quando a região primária está novamente disponível, os pedidos são encaminhados de volta *(falhados*de volta ) para a região primária. Mais uma vez, não precisa de tomar qualquer medida porque isto acontece automaticamente.

Através deste design de alta disponibilidade, o Azure Key Vault não necessita de tempo de paragem para atividades de manutenção.

Há algumas ressalvas para estar atento:

* Em caso de falha na região, pode levar alguns minutos para o serviço falhar. Os pedidos que são feitos durante este período podem falhar até que a falha esteja concluída.
* Depois de uma falha estar completa, o seu cofre chave está em modo de leitura. Os pedidos que são suportados neste modo são:
  * Lista de cofres chave
  * Obtenha propriedades de cofres chave
  * Lista de segredos
  * Obter segredos
  * Lista de chaves
  * Obtenha (propriedades de) chaves
  * Encriptar
  * Desencriptar
  * Embrulho
  * Desembrulhar
  * Verificar
  * Assinar
  * Cópia de segurança
* Depois de uma falha de volta, todos os tipos de pedidos (incluindo pedidos de leitura *e* escrita) estão disponíveis.

