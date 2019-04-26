---
title: O que fazer em caso de um Azure service interrupção que afeta o Azure Key Vault, Azure Key Vault | Documentos da Microsoft
description: Saiba o que fazer em caso de interrupção de serviço do Azure que afeta o Azure Key Vault.
services: key-vault
author: barclayn
manager: barbkess
editor: ''
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: dd1d2af174f93cceabccd62a78d45a0e4fbed502
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60305446"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Redundância e disponibilidade de Cofre de chaves do Azure

O Azure Key Vault apresenta várias camadas de redundância para se certificar de que as suas chaves e segredos permanecem disponíveis para a sua aplicação, mesmo se falharam de componentes individuais do serviço.

O conteúdo do seu Cofre de chaves é replicado dentro da região e para uma região secundária, pelo menos, 150 quilómetros de distância, mas dentro da mesma geografia. Isso mantém uma elevada durabilidade das chaves e segredos. Consulte a [regiões emparelhadas do Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) documento para obter detalhes sobre os pares de região específica.

Caso de falham de componentes individuais no serviço Cofre de chaves, componentes alternativos dentro da região entrar para atender sua solicitação para se certificar de que não existe nenhuma degradação de funcionalidade. Não é necessário efetuar qualquer ação para acionar esta. Ele ocorre automaticamente e será transparente para.

No evento raro que toda uma região do Azure não está disponível, os pedidos que fazem do Azure Key Vault nessa região são automaticamente encaminhados (*a ativação pós-falha*) para uma região secundária. Quando a região primária estiver novamente disponível, os pedidos são encaminhados novamente (*realizarão a reativação pós-falha*) para a região primária. Novamente, não é necessário efetuar qualquer ação porque isto ocorre automaticamente.

Existem algumas limitações a ter em consideração:

* Em caso de uma ativação pós-falha de região, poderá demorar alguns minutos para que o serviço para efetuar a ativação pós-falha. Pedidos que são feitos durante este período poderão falhar até que conclua a ativação pós-falha.
* Após uma ativação pós-falha estiver concluída, o seu Cofre de chaves está em modo só de leitura. São pedidos que são suportados neste modo:
  * Cofres de chaves da lista
  * Obter propriedades de cofres de chaves
  * Lista os segredos
  * Obter segredos
  * Listar chaves
  * Obter chaves de (propriedades de)
  * Encriptar
  * Desencriptar
  * Encapsular
  * Anular a moldagem
  * Verificar
  * Assinar
  * Cópia de segurança
* Após uma ativação pós-falha é realizarão a reativação, todos os tipos de pedido (incluindo leitura *e* pedidos de escrita) estão disponíveis.

