---
title: Práticas recomendadas de segurança do Internet das Coisas | Microsoft Docs
description: O artigo fornece uma lista organizada de práticas recomendadas de segurança do Microsoft Internet das Coisas e recomendações gerais.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 14f1905b1acd60acdf7b3dad0686e3fa34629953
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727045"
---
# <a name="internet-of-things-security-best-practices"></a>Práticas recomendadas de segurança Internet das Coisas

Proteger a infraestrutura de Internet das Coisas (IoT) é uma tarefa crítica para qualquer pessoa envolvida em soluções de IoT. Devido ao número de dispositivos envolvidos e à natureza distribuída desses dispositivos, o impacto de um evento de segurança relacionado ao comprometimento de milhões de dispositivos IoT não é trivial e pode ter um impacto generalizado.

Por esse motivo, a segurança de IoT precisa de uma abordagem de segurança aprofundada. Os dados precisam ser seguros na nuvem e, à medida que se movem por redes públicas e privadas. Os métodos precisam estar em vigor para provisionar com segurança os próprios dispositivos IoT. Cada camada, do dispositivo, à rede, ao back-end de nuvem, precisa de garantias de segurança fortes.

As práticas recomendadas de IoT podem ser categorizadas da seguinte maneira:

* Fabricante ou integrador de hardware de IoT
* Desenvolvedor de soluções de IoT
* Implantador de solução de IoT
* Operador de solução de IoT

Este artigo resume [Internet das coisas práticas recomendadas de segurança](/azure/iot-fundamentals/iot-security-best-practices). Veja esse artigo para obter informações mais detalhadas.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Fabricante ou integrador de hardware de IoT

Siga as práticas recomendadas abaixo se você for um fabricante de hardware IoT ou um integrador de hardware:

* **Escopo de hardware para requisitos mínimos**: o design de hardware deve incluir os recursos mínimos necessários para a operação do hardware e nada mais. 
* **Torne a prova**de adulteração de hardware: Crie mecanismos para detectar violação física de hardware, como abrir a tampa do dispositivo, remover uma parte do dispositivo, etc. 
* **Crie com base em hardware seguro**: se o [Cogs](https://en.wikipedia.org/wiki/Cost_of_goods_sold) permitir, crie recursos de segurança como o armazenamento seguro e criptografado e a funcionalidade de inicialização baseada no TPM (Trusted Platform Module).
* **Tornar as atualizações seguras**: o firmware de atualização durante o tempo de vida do dispositivo é inevitável.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções de IoT

Siga as práticas recomendadas abaixo se você for um desenvolvedor de solução de IoT:

* **Siga a metodologia de desenvolvimento de software seguro**: desenvolver software seguro requer uma visão de ponta sobre a segurança desde a concepção do projeto até sua implementação, teste e implantação.
* **Escolha software livre com cuidado**: o software livre oferece uma oportunidade de desenvolver soluções rapidamente.
* **Integre com cuidado**: muitas das falhas de segurança de software existem no limite de bibliotecas e APIs. 

## <a name="iot-solution-deployer"></a>Implantador de solução de IoT

Siga as práticas recomendadas abaixo se você for um implantador de solução de IoT:

* **Implante o hardware com segurança**: As implantações de IoT podem exigir que o hardware seja implantado em locais não seguros, como em espaços públicos ou localidades não supervisionadas.
* **Mantenha as chaves de autenticação seguras**: durante a implantação, cada dispositivo requer IDs de dispositivo e chaves de autenticação associadas geradas pelo serviço de nuvem. Mantenha essas chaves fisicamente seguras mesmo após a implantação. Qualquer chave comprometida pode ser usada por um dispositivo mal-intencionado para se disfarçar como um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução de IoT

Siga as práticas recomendadas abaixo se você for um operador de solução de IoT:

* **Manter os sistemas**atualizados: Verifique se os sistemas operacionais do dispositivo e todos os drivers de dispositivo foram atualizados para as versões mais recentes. 
* **Proteger contra atividades mal-intencionadas**: se o sistema operacional permitir, coloque os mais recentes recursos antivírus e antimalware em cada sistema operacional do dispositivo. 
* **Auditar frequentemente**: a auditoria da infraestrutura de IOT para problemas relacionados à segurança é a chave ao responder a incidentes de segurança.
* **Proteger fisicamente a infraestrutura de IOT**: os piores ataques de segurança contra a infraestrutura de IOT são iniciados usando o acesso físico aos dispositivos.
* **Proteger credenciais de nuvem**: as credenciais de autenticação de nuvem usadas para configurar e operar uma implantação de IOT possivelmente são a maneira mais fácil de obter acesso e comprometer um sistema de IOT. 

