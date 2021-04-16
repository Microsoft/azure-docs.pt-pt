---
title: Resolução de problemas do seu dispositivo IoT Plug and Play
description: Um guia de etapas recomendadas para resolução de problemas para os parceiros que certificam um dispositivo IoT Plug and Play.
author: nkuntjoro
ms.author: nikuntjo
ms.service: certification
ms.topic: how-to
ms.date: 04/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 9b406e489fb83083d47f01e1483160181601d518
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559312"
---
# <a name="troubleshoot-your-iot-plug-and-play-certification-project"></a>Resolução de problemas do seu projeto de certificação IoT Plug and Play

Durante a fase de teste do Connect & do seu projeto de certificação IoT Plug and Play, poderá encontrar alguns cenários que o impeçam de passar no teste do Azure for IoT Certification Service (AICS).

## <a name="prerequisites"></a>Pré-requisitos

- Deverá fazer o seu contrato e ter um projeto para o seu dispositivo criado no [portal Azure Certified Device](https://certify.azure.com). Para mais informações, consulte o [tutorial.](tutorial-01-creating-your-project.md)

## <a name="when-aics-tests-arent-passing"></a>Quando os testes da AICS não estão a passar

O teste AICS pode não passar devido a várias causas. Siga estes passos para verificar se há problemas comuns e resolva o seu dispositivo.

1. Verifique duas vezes se o código do dispositivo está a definir a carga de carga de ID do modelo durante o provisionamento de DPS. Este é um requisito para que a AICS valide o seu dispositivo.
1. Pode visualizar os registos de telemetria de ensaios anteriores, premindo o `View Logs` botão para identificar o que está a causar a falha do teste. Tanto as mensagens de teste como os dados brutos estão disponíveis para revisão.  

    ![Rever dados de teste](./media/images/review-logs.png)

1. Em alguns casos em que os registos `Failed to get Digital Twin Model ID of device xx due to DeviceNotConnected` indicam, tente reiniciar o dispositivo e reiniciar o processo de provisionamento do dispositivo.
1. Se os testes automatizados continuarem a falhar, então `request a manual review` podes substituir os resultados. Isto irá desencadear um pedido **de validação manual** com a equipa de Dispositivos Certificados Azure.  

    ![Solicitar revisão manual](./media/images/request-manual-review.png)

## <a name="when-you-see-passed-with-warnings"></a>Quando vires "Passado com avisos"

Durante a realização dos testes, se receber o resultado `Passed with warnings` de , isto significa que alguma telemetria não foi recebida durante o período de testes. Isto pode dever-se a uma dependência da telemetria em intervalos de tempo mais longos ou gatilhos externos que não estavam disponíveis. Pode proceder à submissão do seu dispositivo para revisão, durante o qual a equipa de revisão determinará se a **validação manual** é necessária no futuro.

## <a name="when-you-need-help-with-the-model-repository"></a>Quando precisar de ajuda com o repositório de modelos

Para questões de IoT Plug e Play relacionadas com o repositório do modelo, consulte a [nossa orientação docs sobre o repositório do modelo do dispositivo](https://docs.microsoft.com/azure/iot-pnp/concepts-model-repository).

## <a name="next-steps"></a>Passos seguintes

Esperemos que este guia o ajude a continuar com a sua jornada de certificação IoT Plug and Play! Depois de ter passado a AICS, pode então proceder aos nossos tutoriais para submeter e publicar o seu dispositivo.

- [Tutorial: Testar o seu dispositivo](tutorial-03-testing-your-device.md)
