---
title: Atualização automática do tempo de execução da integração auto-acolessão e a notificação de expiração
description: Saiba mais sobre a atualização automática do tempo de execução da integração auto-acolôda e a notificação de expiração
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
manager: shwang
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 6a6c897d92d469fd6247dd51f2bacb91032ac123
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98122665"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Atualização automática do tempo de execução da integração auto-acolessão e a notificação de expiração

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreverá como permitir uma auto-actualização automática do tempo de execução da integração auto-acolessão para a versão mais recente e como a ADF gere as versões do tempo de integração auto-hospedado.

## <a name="self-hosted-integration-runtime-auto-update"></a>Atualização automática do tempo de execução da integração auto-hospedada
Geralmente, quando instala um tempo de integração auto-hospedado na sua máquina local ou num VM Azure, tem duas opções para gerir a versão do tempo de funcionamento da integração auto-hospedada: atualização automática ou manutenção manual. Normalmente, a ADF lança duas novas versões de tempo de integração auto-hospedado todos os meses, que inclui nova versão de funcionalidade, correção de bugs ou melhoria. Por isso, recomendamos que os utilizadores atualizem a versão mais recente de forma a obterem a mais recente funcionalidade e melhoria.

A forma mais conveniente é ativar a atualização automática quando cria ou edita o tempo de execução da integração auto-hospedada. Em seguida, será automaticamente atualizado para a versão mais recente. Também pode agendar a atualização na faixa horária mais adequada que desejar.

![Ativar a atualização automática](media/create-self-hosted-integration-runtime/shir-auto-update.png)

Pode verificar a última atualização da data no seu cliente de execução de integração auto-hospedado.

![Ativar a atualização automática](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> Para garantir a estabilidade do tempo de integração auto-hospedado, embora lancemos duas versões, só a atualizaremos automaticamente uma vez por mês. Por isso, por vezes, verá que a versão auto-actualizada é a versão anterior da versão mais recente. Se quiser obter a versão mais recente, pode ir ao [download do Centro.](https://www.microsoft.com/download/details.aspx?id=39717)

## <a name="self-hosted-integration-runtime-expire-notification"></a>Notificação de prazo de execução de integração auto-hospedada
Se pretender controlar manualmente qual a versão do tempo de funcionação da integração auto-hospedada, pode desativar a definição de atualização automática e instalá-la manualmente. Cada versão do tempo de funcionação da integração auto-hospedada expirará dentro de um ano. A mensagem expirada é mostrada no portal ADF e no cliente de execução de integração auto-hospedado **90 dias** antes de expirar.

## <a name="next-steps"></a>Passos seguintes

- Rever [conceitos de tempo de execução de integração na Azure Data Factory.](./concepts-integration-runtime.md)

- Saiba como [criar um tempo de integração auto-hospedado no portal Azure](./create-self-hosted-integration-runtime.md).