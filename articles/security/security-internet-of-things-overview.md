---
title: Proteger a sua Internet das coisas (IoT) no Azure | Documentos da Microsoft
description: " Azure internet dos serviços de coisas (IoT) oferecem uma vasta gama de capacidades. Este artigo ajuda-o a compreender como proteger suas soluções de IoT no Azure. "
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: 1473c8dd-8669-48fb-86db-b3c50e2eaf59
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: db1c2b9c852479b9af3674f6c5e1f1135ee289f1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107446"
---
# <a name="internet-of-things-security-overview"></a>Descrição geral da segurança de Internet das coisas
Azure internet dos serviços de coisas (IoT) oferecem uma vasta gama de capacidades. Estes serviços de nível empresarial permitem-lhe:

* Recolher dados de dispositivos
* Analisar fluxos de dados em movimento
* Armazenar e consultar grandes conjuntos de dados
* Visualizar dados em tempo real e dados históricos
* Integrar com sistemas de back-office

Para disponibilizar esses recursos, o pacote de Aceleradores de solução de IoT do Azure em conjunto vários serviços do Azure com extensões personalizadas como soluções pré-configuradas. Estas soluções pré-configuradas são implementações de base de padrões comuns da solução IoT que ajudam a reduzir o tempo necessário para fornecer as suas soluções de IoT. Com os kits de desenvolvimento de software de IoT, pode personalizar e expandir essas soluções para satisfazer os seus próprios requisitos. Também pode utilizar estas soluções como exemplos ou modelos quando estiver a desenvolver novas soluções de IoT.

Os Aceleradores de solução de IoT do Azure são soluções poderosas para as suas necessidades de IoT. No entanto, é de importância upmost que suas soluções de IoT sejam criadas com segurança desde o início. Devido ao grande número de dispositivos IoT, qualquer incidente de segurança pode se tornar rapidamente um evento amplo com conseqüências significativos.

Para ajudar a compreender como proteger suas soluções de IoT, temos as seguintes informações.

## <a name="security-architecture"></a>Arquitetura de segurança
Quando um sistema, é importante compreender as ameaças potenciais para esse sistema e adicionar as defesas apropriadas da mesma forma, como o sistema é concebido e criado. É importante estruturar o produto desde o início com segurança em mente, porque a compreensão de como um invasor poderá comprometer um sistema de ajuda a tornar as atenuações apropriadas-se de que estão em vigor desde o início.

Pode saber mais sobre a arquitetura de segurança de IoT lendo [arquitetura de segurança Internet das coisas](/azure/iot-fundamentals/iot-security-architecture).

Este artigo aborda os seguintes tópicos:

* [Segurança começa com um modelo de ameaças](/azure/iot-fundamentals/iot-security-architecture#security-starts-with-a-threat-model)
* [Segurança de IoT](/azure/iot-fundamentals/iot-security-architecture#security-in-iot)
* [A arquitetura de referência do IoT do Azure de modelagem de ameaças](/azure/iot-fundamentals/iot-security-architecture)

## <a name="security-from-the-ground-up"></a>Segurança desde o início
O IoT apresenta desafios únicos de segurança, privacidade e conformidade para empresas em todo o mundo. Ao contrário de tecnologia de cibersegurança tradicional onde esses problemas giram em torno de software e como ele é implementado, IoT diz respeito o que acontece quando a informáticas e os mundos físicos convergem. Proteger soluções de IoT requer a garantia de seguro de aprovisionamento de dispositivos, conectividade segura entre estes dispositivos e a cloud e a proteção de proteger os dados na cloud durante o processamento e armazenamento. No entanto, trabalhar em relação a essa funcionalidade, são dispositivos de restrição de recursos, distribuição geográfica de implementações e número de dispositivos numa solução.

Pode saber como lidar com a segurança nas seguintes áreas lendo [segurança de Internet das coisas de cima a](/azure/iot-fundamentals/iot-security-ground-up).

O artigo aborda os seguintes tópicos:

* [Infraestrutura segura desde o início](/azure/iot-fundamentals/iot-security-ground-up#secure-infrastructure-from-the-ground-up)
* [Microsoft Azure – infraestrutura IoT segura para a sua empresa](/azure/iot-fundamentals/iot-security-ground-up#microsoft-azure---secure-iot-infrastructure-for-your-business)

## <a name="best-practices"></a>Melhores práticas
A proteção de uma infraestrutura IoT exige uma estratégia de segurança em profundidade rigorosa. De proteção de dados na cloud, a proteger a integridade dos dados em trânsito através da internet pública, de forma segura aprovisionamento de dispositivos, a cada camada baseia-se maior garantia de segurança numa infraestrutura global.

Pode saber mais sobre segurança de Internet das coisas melhores práticas, lendo [práticas recomendadas de segurança de Internet das coisas](/azure/iot-fundamentals/iot-security-best-practices).

O artigo aborda os seguintes tópicos:

* [Fabricante/integrador de hardware de IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-hardware-manufacturerintegrator)
* [Desenvolvedor de soluções de IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-developer)
* [Implementador de solução de IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-deployer)
* [Operador de solução de IoT](/azure/iot-fundamentals/iot-security-best-practices#iot-solution-operator)
