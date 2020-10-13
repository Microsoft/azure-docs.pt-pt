---
title: Residência dos dados
description: Residência de dados e informações sobre servidores ativados do Azure Arc.
ms.topic: reference
ms.date: 10/08/2020
ms.custom: references_regions
ms.openlocfilehash: c5ece96acc3ee07ba2896279888363c7d52d737e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856454"
---
# <a name="azure-arc-enabled-servers-data-residency"></a>Servidores habilitados Azure Arc: Residência de dados

Este artigo explica o conceito de residência de dados e como se aplica aos servidores ativados do Azure Arc.

Os servidores azure Arc estão **[disponíveis](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** nos **Estados Unidos, Europa, Reino Unido, Austrália e Ásia-Pacífico.**

## <a name="data-residency"></a>Residência dos dados

Os servidores ativados Azure Arc armazenam as definições de [configuração de extensão Azure VM](manage-vm-extensions.md) (ou seja, valores de propriedade) a extensão requer especificação antes de tentar ativar a máquina conectada. Por exemplo, quando ativa a extensão VM do Log Analytics, pede o **ID** do espaço de trabalho log Analytics e **a chave primária**.

São também recolhidas informações sobre metadados sobre a máquina conectada. Especificamente:

* Nome e versão do sistema operativo
* Nome do computador
* Nome de domínio totalmente qualificado do computador (FQDN)
* Versão do agente da máquina conectada

Os servidores ativados pelo Arco permitem especificar a região onde os seus dados serão armazenados. A Microsoft pode replicar-se noutras regiões para a resiliência dos dados, mas a Microsoft não replica ou move dados para fora da geografia. Estes dados são armazenados na região onde o recurso da máquina Azure Arc está configurado. Por exemplo, se a máquina estiver registada com a Arc na região leste dos EUA, estes dados são armazenados na região dos EUA.

Para mais informações sobre a nossa resiliência regional e suporte à conformidade, consulte [a geografia Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o design para a [resiliência do Azure.](/azure/architecture/reliability/architect)