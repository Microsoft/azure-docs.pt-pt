---
title: Configurar o seu centro de segurança do Azure para a solução de IoT pré-visualização | Documentos da Microsoft
description: Saiba como configurar a sua solução de IoT ponto a ponto com o Centro de segurança do Azure para IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/26/2019
ms.author: mlottner
ms.openlocfilehash: 64a04861b14e48eaa14a369546dd9d54bf59f019
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58861926"
---
# <a name="quickstart-configure-your-iot-solution"></a>Início rápido: Configurar a sua solução de IoT

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Este artigo fornece uma explicação de como realizar a configuração inicial da sua solução de segurança de IoT com o ASC para IoT. 

## <a name="azure-security-center-asc-for-iot"></a>O Centro de segurança do Azure (ASC) para IoT

ASC para IoT fornece segurança de ponto a ponto abrangentes para soluções de IoT baseada no Azure.

Com o ASC para IoT, pode monitorizar a sua solução de IoT inteira num dashboard, através de todos os seus dispositivos IoT, plataformas de IoT e recursos de back-end no Azure.

Depois de ativada no seu IoT Hub, o ASC para IoT identifica automaticamente a outros serviços do Azure, também ligado ao seu hub IoT e relacionados à sua solução de IoT.

Além de deteção de relações automática, pode também escolher quais outros recursos do Azure, a marca como parte da sua solução de IoT.
As suas seleções permitem-lhe adicionar subscrições todas, grupos de recursos ou recursos únicos.

Depois de definir todas as relações de recursos, ASC para IoT tira partido do Centro de segurança do Azure para lhe fornecer recomendações de segurança e alertas para estes recursos.

## <a name="add-azure-resources-to-your-iot-solution"></a>Adicionar recursos do Azure à sua solução de IoT

Ao adicionar o novo recurso à sua solução de IoT, faça o seguinte: 

1. Abra sua **IoT Hub** no portal do Azure. 
2. Selecione e abra **recursos** sob **segurança** no menu à esquerda. 
3. Selecione **adicionar recursos**.
4. Escolha os recursos que pertencem à sua solução de IoT.
5. Clique em **Adicionar**. 

Parabéns! Adicionar um novo recurso à sua solução de IoT.

ASC para IoT agora monitores que estiver recentemente adicionado alertas e recomendações de segurança relevantes superfícies e recursos, como parte da sua solução de IoT.

## <a name="next-steps"></a>Passos Seguintes

Avance para o artigo seguinte para saber como criar módulos de segurança...

> [!div class="nextstepaction"]
> [Criar módulos de segurança](quickstart-create-security-twin.md)