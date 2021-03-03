---
title: Compreenda a atualização do dispositivo para a conformidade do Hub Azure IoT | Microsoft Docs
description: Compreenda como a atualização do dispositivo para Azure IoT Hub mede a conformidade com a atualização do dispositivo.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ac6094efde8a32b1fcc04c55bbc537afeb4166f7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663415"
---
# <a name="device-update-compliance"></a>Conformidade da atualização do dispositivo

Na Atualização do Dispositivo para o IoT Hub, a conformidade mede quantos dispositivos instalaram a atualização compatível com a versão mais alta. Um dispositivo é compatível se tiver instalado a versão mais alta disponível que seja compatível para o mesmo. 

Por exemplo, considere uma instância de Atualização do Dispositivo com as seguintes atualizações:

|Nome de atualização|Versão de atualização|Modelo de dispositivo compatível|
|-----------|--------------|-----------------------|
|Atualização1    |1.0    |Model1|
|Atualização2    |1.0    |Model2|
|Atualização3    |2.0    |Model1|

Digamos que foram criados os seguintes destacamentos:

|Nome de implantação    |Nome de atualização    |Grupo Direcionado|
|-----------|--------------|-------------------|
|Implantação1    |Atualização1    |Grupo1|
|Implantação2    |Atualização2    |Grupo 2|
|Implantação3    |Atualização3    |Grupo3|

Agora, considere os seguintes dispositivos, com as suas filiações de grupo e versões instaladas:

|DeviceId   |Modelo de dispositivo   |Versão de atualização instalada|Group |Conformidade|
|-----------|--------------|-----------------------|-----|---------|
|Dispositivo1    |Model1 |1.0    |Grupo1 |Novas atualizações disponíveis</span>|
|Dispositivo2    |Model1 |2.0    |Grupo3 |Na última atualização|
|Dispositivo3    |Model2 |1.0    |Grupo 2 |Na última atualização|
|Dispositivo4    |Model1 |1.0    |Grupo3 |Atualização em curso|

O Device1 e o Device4 não estão em conformidade porque têm a versão 1.0 instalada, mesmo que exista uma atualização de versão superior, Update3, compatível com o seu modelo na instância de Atualização do Dispositivo. O Device2 e o Device3 estão ambos em conformidade porque possuem as atualizações de versão mais altas compatíveis com os seus modelos instalados.

A conformidade não pondera se uma atualização é ou não implementada no grupo de um dispositivo; analisa quaisquer atualizações publicadas para a Atualização do Dispositivo. Assim, no exemplo acima, apesar de o Device1 ter instalado a atualização implementada no mesmo, é considerado incompatível. O Dispositivo1 continuará a ser considerado incompatível até que instale com sucesso o Update3. O estado de conformidade pode ajudá-lo a identificar se são necessárias novas implementações. 

Como mostrado acima, existem três estados de conformidade na Atualização do Dispositivo para o Hub IoT:

*   **Na mais recente atualização** – o dispositivo instalou a atualização compatível com a versão mais alta publicada para a Atualização do Dispositivo.
*   **Atualização em curso** – uma implementação ativa encontra-se em fase de entrega da versão mais alta compatível com o dispositivo.
*   **Novas atualizações disponíveis** – um dispositivo ainda não instalou a atualização mais alta compatível com a versão e não encontra-se numa implementação ativa para essa atualização.
