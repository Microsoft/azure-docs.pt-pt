---
title: Proteger seu Internet das Coisas (IoT) no Azure | Microsoft Docs
description: " Os serviços de IoT (Internet das coisas) do Azure oferecem uma ampla gama de recursos. Este artigo ajuda você a entender como proteger suas soluções de IoT no Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 670fcc5e92cdb9937026d91ee85b7e8e856a3025
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727039"
---
# <a name="internet-of-things-security-overview"></a>Visão geral da segurança do Internet das Coisas
Os serviços de IoT (Internet das coisas) do Azure oferecem uma ampla gama de recursos. Estes serviços de nível empresarial permitem-lhe:

* Recolher dados de dispositivos
* Analisar fluxos de dados em movimento
* Armazenar e consultar grandes conjuntos de dados
* Visualizar dados em tempo real e dados históricos
* Integrar com sistemas de back-office

Para fornecer esses recursos, os aceleradores de solução do Azure IoT agrupam vários serviços do Azure com extensões personalizadas como soluções pré-configuradas. Estas soluções pré-configuradas são implementações de base de padrões comuns da solução IoT que ajudam a reduzir o tempo necessário para fornecer as suas soluções de IoT. Usando os kits de desenvolvimento de software IoT, você pode personalizar e estender essas soluções para atender às suas necessidades. Também pode utilizar estas soluções como exemplos ou modelos quando estiver a desenvolver novas soluções de IoT.

Os aceleradores de solução do Azure IoT são soluções poderosas para suas necessidades de IoT. No entanto, é de maior importância que suas soluções de IoT foram projetadas tendo em mente a segurança desde o início. Devido ao número enorme de dispositivos IoT, qualquer incidente de segurança pode rapidamente se tornar um evento generalizado com consequências significativas.

Para ajudá-lo a entender como proteger suas soluções de IoT, temos as seguintes informações.

## <a name="security-architecture"></a>Arquitetura de segurança
Ao criar um sistema, é importante compreender as possíveis ameaças ao sistema e adicionar as defesas apropriadas de acordo, à medida que o sistema é projetado e arquitetado. É importante projetar o produto desde o início com segurança em mente, pois entender como um invasor pode ser capaz de comprometer um sistema ajuda a garantir que as atenuações apropriadas estejam em vigor desde o início.

Você pode aprender sobre a arquitetura de segurança de IoT lendo [Internet das coisas arquitetura de segurança](/azure/iot-fundamentals/iot-security-architecture).

Este artigo aborda os seguintes tópicos:

* [A segurança começa com um modelo de ameaça](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [Segurança na IoT](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [Modelagem de ameaças da arquitetura de referência do Azure IoT](/azure/iot-fundamentals/iot-security-architecture)

## <a name="security-from-the-ground-up"></a>Segurança desde o início
A IoT traz desafios exclusivos de segurança, privacidade e conformidade para as empresas no mundo todo. Ao contrário da tecnologia Cyber tradicional onde esses problemas giram em relação ao software e como eles são implementados, a IoT se preocupa com o que acontece quando os mundos virtuais e da CyberSource convergem. A proteção das soluções de IoT exige a garantia de um provisionamento seguro de dispositivos, a conectividade segura entre esses dispositivos e a nuvem e a proteção segura de dados na nuvem durante o processamento e o armazenamento. No entanto, trabalhar com essas funcionalidades são dispositivos com restrição de recursos, distribuição geográfica de implantações e muitos dispositivos em uma solução.

Você pode aprender a lidar com a segurança nessas áreas, lendo [Internet das coisas segurança desde o início](/azure/iot-fundamentals/iot-security-ground-up).

O artigo aborda os seguintes tópicos:

* [Proteger a infraestrutura desde o início](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure – infraestrutura de IoT segura para sua empresa](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Melhores Práticas
Proteger uma infraestrutura de IoT exige uma rigorosa estratégia de segurança aprofundada. Desde a proteção de dados na nuvem, a proteção da integridade dos dados está em trânsito pela Internet pública, para o provisionamento seguro de dispositivos, cada camada cria uma garantia de segurança maior na infraestrutura geral.

Você pode aprender sobre as práticas recomendadas de segurança Internet das Coisas lendo [Internet das coisas práticas recomendadas de segurança](/azure/iot-fundamentals/iot-security-best-practices).

O artigo aborda os seguintes tópicos:

* [Fabricante/integrador de hardware de IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [Desenvolvedor de soluções de IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [Implantador de solução de IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [Operador de solução de IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
