---
title: O que fazer no caso de uma interrupção de serviço do Azure afetar o Azure Key Vault Azure Key Vault | Microsoft Docs
description: Saiba o que fazer no caso de uma interrupção de serviço do Azure afetar Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: c0fed80f4ba9815cee49bc9968d542f168570986
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976375"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Disponibilidade e redundância de Azure Key Vault

O Azure Key Vault apresenta várias camadas de redundância para garantir que suas chaves e segredos permaneçam disponíveis para seu aplicativo, mesmo se os componentes individuais do serviço falharem.

O conteúdo do cofre de chaves é replicado dentro da região e para uma região secundária com pelo menos 150 milhas de distância, mas na mesma geografia. Isso mantém a alta durabilidade de suas chaves e segredos. Consulte o documento [regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md) para obter detalhes sobre pares de regiões específicas.

Se os componentes individuais dentro do serviço de cofre de chaves falharem, os componentes alternativos dentro da região entrarão em para atender à sua solicitação para garantir que não haja degradação da funcionalidade. Você não precisa realizar nenhuma ação para disparar isso. Ele ocorre automaticamente e será transparente para você.

No caso raro em que uma região inteira do Azure não esteja disponível, as solicitações que você faz de Azure Key Vault nessa região são automaticamente roteadas (*failover*) para uma região secundária. Quando a região primária estiver disponível novamente, as solicitações serão roteadas de volta (*failback*) para a região primária. Novamente, você não precisa realizar nenhuma ação porque isso ocorre automaticamente.

Por meio desse design de alta disponibilidade, Azure Key Vault não requer nenhum tempo de inatividade para atividades de manutenção.

Há algumas limitações a serem consideradas:

* No caso de um failover de região, pode levar alguns minutos para que o serviço faça failover. As solicitações feitas durante esse tempo podem falhar até que o failover seja concluído.
* Depois que um failover for concluído, o cofre de chaves estará no modo somente leitura. As solicitações com suporte neste modo são:
  * Listar cofres de chaves
  * Obter propriedades de cofres de chaves
  * Listar segredos
  * Obter segredos
  * Listar chaves
  * Obter (Propriedades de) chaves
  * Encriptar
  * Desencriptar
  * Haja
  * Unwrap
  * Validar
  * Assinar
  * Criar cópia de segurança
* Após o failback do failover, todos os tipos de solicitação (incluindo solicitações *de leitura e* gravação) estarão disponíveis.

