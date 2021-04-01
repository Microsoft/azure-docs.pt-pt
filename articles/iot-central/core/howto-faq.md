---
title: Azure IoT Central frequentemente fez perguntas | Microsoft Docs
description: Azure IoT Central frequentemente fez perguntas (FAQ) e respostas
author: dominicbetts
ms.author: dobett
ms.date: 12/20/2020
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: acabaf843f8acfe7bc0b5e9456dee09bde74bef7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97796045"
---
# <a name="frequently-asked-questions-for-iot-central"></a>Perguntas frequentes para ioT Central

**Como posso verificar se há problemas de credencial se um dispositivo não está ligado à minha aplicação IoT Central?**

A [resolução de problemas por que os dados dos seus dispositivos não aparecem no Azure IoT Central](troubleshoot-connection.md) inclui medidas para diagnosticar problemas de conectividade para dispositivos.

**Como posso arquivar um bilhete com apoio ao cliente?**

Se precisar de ajuda, pode apresentar um [bilhete de apoio Azure.](https://portal.azure.com/#create/Microsoft.Support)

Para obter mais informações, incluindo outras opções de suporte, consulte o [suporte do Azure IoT e opções de ajuda.](../../iot-fundamentals/iot-support-help.md)

**Como desbloqueio um dispositivo?**

Quando um dispositivo está bloqueado, não pode enviar dados para a sua aplicação IoT Central. Os dispositivos bloqueados têm um estado de **bloqueio** na página **dispositivos** na sua aplicação. Um operador deve desbloquear o dispositivo antes de poder retomar o envio de dados:

:::image type="content" source="media/howto-faq/blocked.png" alt-text="Screenshot mostrando dispositivo bloqueado":::

Quando um operador desbloqueia um dispositivo, o estado retorna ao seu valor anterior, **Registado** ou **Provisionado**.

**Como aprovo um dispositivo?**

Se o estado do dispositivo estiver **à espera de aprovação** na página dispositivos, significa que a opção **de aprovação automática** está **desativada:**

:::image type="content" source="media/howto-faq/auto-approve.png" alt-text="Imagem de imagem mostrando a aplicação com opção de aprovação automática desativada.":::

Um operador deve aprovar explicitamente um dispositivo antes de começar a enviar dados. Os dispositivos não registados manualmente na página **dispositivos,** mas ligados a credenciais válidas terão o estado do dispositivo **à espera de aprovação**. Os operadores podem aprovar estes dispositivos a partir da página **dispositivos** utilizando o botão **Aprovar:**

:::image type="content" source="media/howto-faq/approve-device.png" alt-text="Screenshot mostrando como aprovar um dispositivo":::

**Como posso associar um dispositivo a um modelo de dispositivo?**

Se o estado do dispositivo não estiver **associado,** significa que o dispositivo que se liga à IoT Central não tem um modelo de dispositivo associado. Esta situação acontece tipicamente nos seguintes cenários:

- Um conjunto de dispositivos é adicionado usando **importe** na página **dispositivos** sem especificar o modelo do dispositivo.
- Um dispositivo foi registado manualmente na página **dispositivos** sem especificar o modelo do dispositivo. O dispositivo então ligado com credenciais válidas.  

O operador pode associar um dispositivo a um modelo de dispositivo a partir da página **dispositivos** utilizando o botão **Migrar.** Para saber mais, consulte [Gerir os dispositivos na sua aplicação Azure IoT Central > dispositivos migratórios para um modelo](howto-manage-devices.md).

**Onde posso aprender mais sobre o IoT Hub?**

A Azure IoT Central utiliza o Azure IoT Hub como porta de entrada em nuvem que permite a conectividade do dispositivo. O IoT Hub permite:

- Ingestão de dados em escala na nuvem.
- Gestão de dispositivos.
- Conectividade segura do dispositivo.

Para saber mais sobre o IoT Hub, consulte [o Azure IoT Hub.](../../iot-hub/index.yml)

**Onde posso saber mais sobre o Serviço de Provisionamento de Dispositivos (DPS)?**

A Azure IoT Central utiliza DPS para permitir que os dispositivos se conectem à sua aplicação. Para saber mais sobre o papel que o DPS desempenha na ligação dos dispositivos à IoT Central, consulte [Get connected to Azure IoT Central](concepts-get-connected.md). Para saber mais sobre o DPS, consulte [dispositivos de provisionamento com o Serviço de Provisionamento de Dispositivos Azure IoT Hub](../../iot-dps/about-iot-dps.md).