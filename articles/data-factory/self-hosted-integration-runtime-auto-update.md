---
title: Atualização automática e notificação de expiração do runtime de integração autoalojado
description: Saiba mais sobre a atualização automática do tempo de execução da integração auto-acolôda e a notificação de expiração
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 12/25/2020
ms.openlocfilehash: 972015f0f42a8a869de0edcc8f0e921ae7278cd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376263"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Atualização automática e notificação de expiração do runtime de integração autoalojado

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo descreverá como permitir uma auto-actualização automática do tempo de execução da integração auto-acolessão para a versão mais recente e como a ADF gere as versões do tempo de integração auto-hospedado.

## <a name="self-hosted-integration-runtime-auto-update"></a>Atualização automática do tempo de execução da integração auto-hospedada
Geralmente, quando instala um tempo de integração auto-hospedado na sua máquina local ou num VM Azure, tem duas opções para gerir a versão do tempo de funcionamento da integração auto-hospedada: atualização automática ou manutenção manual. Normalmente, a ADF lança duas novas versões de tempo de integração auto-hospedado todos os meses, que inclui nova versão de funcionalidade, correção de bugs ou melhoria. Por isso, recomendamos que os utilizadores atualizem a versão mais recente de forma a obterem a mais recente funcionalidade e melhoria.

A forma mais conveniente é ativar a atualização automática quando cria ou edita o tempo de execução da integração auto-hospedada. Em seguida, será automaticamente atualizado para a versão mais recente. Também pode agendar a atualização na faixa horária mais adequada que desejar.

![Ativar a atualização automática](media/create-self-hosted-integration-runtime/shir-auto-update.png)

Pode verificar a última atualização da data no seu cliente de execução de integração auto-hospedado.

![Screenshot de verificar o tempo de atualização](media/create-self-hosted-integration-runtime/shir-auto-update-2.png)

> [!NOTE]
> Para garantir a estabilidade do tempo de integração auto-hospedado, embora lancemos duas versões, só a atualizaremos automaticamente uma vez por mês. Por isso, por vezes, verá que a versão auto-actualizada é a versão anterior da versão mais recente. Se quiser obter a versão mais recente, pode ir ao [download do Centro.](https://www.microsoft.com/download/details.aspx?id=39717)

## <a name="self-hosted-integration-runtime-expire-notification"></a>Notificação de prazo de execução de integração auto-hospedada
Se pretender controlar manualmente qual a versão do tempo de funcionação da integração auto-hospedada, pode desativar a definição de atualização automática e instalá-la manualmente. Cada versão do tempo de funcionação da integração auto-hospedada expirará dentro de um ano. A mensagem expirada é mostrada no portal ADF e no cliente de execução de integração auto-hospedado **90 dias** antes de expirar.

## <a name="next-steps"></a>Passos seguintes

- Rever [conceitos de tempo de execução de integração na Azure Data Factory.](./concepts-integration-runtime.md)

- Saiba como [criar um tempo de integração auto-hospedado no portal Azure](./create-self-hosted-integration-runtime.md).
