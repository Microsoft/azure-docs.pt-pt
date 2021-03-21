---
title: Compreenda atualização do dispositivo para o IoT Hub que importa | Microsoft Docs
description: Conceitos-chave para importar uma nova atualização para a Atualização do Dispositivo para ioT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/10/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 4cd5e0c016b98a3dc9336237a5c1b14e6b0f5789
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102040588"
---
# <a name="importing-updates-into-device-update-for-iot-hub"></a>Importar atualizações para atualização de dispositivos para ioT hub
Para implementar uma atualização para dispositivos a partir de Device Update para IoT Hub, primeiro tem de _importar_ essa atualização para o serviço de Atualização de Dispositivos. Aqui está uma visão geral de alguns conceitos importantes para entender quando se trata de importar atualizações.

## <a name="import-manifest"></a>Manifesto de importação

Um manifesto de importação é um ficheiro JSON que define informações importantes sobre a atualização que está a importar. Submeterá tanto o seu manifesto de importação como ficheiros de atualização associados (como um pacote de atualização de firmware) como parte do processo de importação. Os metadados definidos no manifesto de importação são utilizados para ingerir a atualização. Alguns dos metadados também são utilizados no tempo de implementação - por exemplo, para validar se uma atualização foi instalada corretamente.

O manifesto de importação contém vários itens que representam uma atualização importante do dispositivo para conceitos IoT Hub. Estes conceitos estão descritos abaixo.

### <a name="update-identity-update-id"></a>Atualizar identidade (ID de atualização)

A identidade da atualização representa o identificador único de uma atualização. Define propriedades importantes sobre uma atualização que está a ser importada. A identidade de atualização é composta por três partes:
* Fornecedor: esta é a entidade que está a criar ou diretamente responsável pela atualização. Muitas vezes será um nome de empresa.
* Nome: um identificador para uma classe de atualizações. A aula pode ser o que quiser. Muitas vezes será um dispositivo ou nome de modelo.
* Versão: este é um número de versão que distingue esta atualização de outras que têm o mesmo Fornecedor e Nome. Esta versão é utilizada pela Atualização do Dispositivo para o serviço IoT Hub, podendo ou não coincidir com uma versão de um componente de software individual no dispositivo. 

### <a name="compatibility"></a>Compatibilidade

Para simplificar as implementações de atualização, a Atualização do Dispositivo para ioT Hub compara as propriedades de compatibilidade para uma atualização, que são definidas no manifesto de importação, com as propriedades correspondentes do dispositivo. Apenas as atualizações que tenham propriedades correspondentes serão devolvidas e disponíveis para implementação.

### <a name="installedcriteria"></a>Criteria instalada

A InstalaçãoCriteria é utilizada pelo agente de atualização num dispositivo para determinar se uma atualização foi instalada com sucesso.


## <a name="next-steps"></a>Passos seguintes

Se estiver pronto, experimente o [guia importe How-To,](./import-update.md)que o acompanhará passo a passo através do processo de importação.


