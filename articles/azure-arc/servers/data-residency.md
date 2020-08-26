---
title: Residência dos dados
description: Residência de dados e informações sobre servidores ativados Azure Arc (pré-visualização).
ms.topic: reference
ms.date: 08/25/2020
ms.custom: references_regions
ms.openlocfilehash: 028398c27dde9760192d366b9e8a91dca8e429cf
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88860975"
---
# <a name="azure-arc-enabled-servers-preview-data-residency"></a>Servidores ativados Azure Arc (pré-visualização): Residência de dados

Este artigo explica o conceito de residência de dados e como se aplica aos servidores ativados do Azure Arc (pré-visualização).

Os servidores ativados Azure Arc (pré-visualização) **[estão disponíveis em pré-visualização](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)** nos **Estados Unidos, Europa ou Ásia-Pacífico.**

## <a name="data-residency"></a>Residência dos dados

Azure Arc ativou servidores (pré-visualização) armazenar as definições de [configuração de extensão Azure VM](manage-vm-extensions.md) (isto é, valores de propriedade) a extensão requer especificar antes de tentar ativar a máquina conectada. Por exemplo, quando ativa a extensão VM do Log Analytics, pede o **ID** do espaço de trabalho log Analytics e **a chave primária**.

São também recolhidas informações sobre metadados sobre a máquina conectada. Especificamente:

* Nome e versão do sistema operativo
* Nome do computador
* Nome de domínio totalmente qualificado do computador (FQDN)
* Versão do agente da máquina conectada

Os servidores ativados pelo Arco (pré-visualização) permitem especificar a região onde os seus dados serão armazenados. A Microsoft pode replicar-se noutras regiões para a resiliência dos dados, mas a Microsoft não replica ou move dados para fora da geografia. Estes dados são armazenados na região onde o recurso da máquina Azure Arc está configurado. Por exemplo, se a máquina estiver registada com a Arc na região leste dos EUA, estes dados são armazenados na região dos EUA.

Para mais informações sobre a nossa resiliência regional e suporte à conformidade, consulte [a geografia Azure.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o design para a [resiliência do Azure.](/architecture/reliability/architect)