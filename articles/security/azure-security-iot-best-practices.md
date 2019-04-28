---
title: Internet das coisas de segurança recomendados | Documentos da Microsoft
description: O artigo fornece uma lista organizada do Microsoft Internet das coisas segurança melhores práticas e recomendações gerais.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 9413c0503c1b78550776d1c2f6ab8239205a788b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121629"
---
# <a name="internet-of-things-security-best-practices"></a>Internet das coisas de segurança recomendados

Protegendo a infra-estrutura de Internet das coisas (IoT) é uma tarefa essencial para todos os envolvidos com soluções de IoT. Por causa o número de dispositivos envolvidos e a natureza distribuída desses dispositivos, o impacto de um evento de segurança relacionados para comprometer de milhões de dispositivos IoT não é simples e pode ter impacto amplo.

Por esse motivo, a segurança de IoT precisa uma abordagem de segurança em profundidade. Dados tem de ser seguras na cloud e quando são transmitidos através de redes privadas e públicas. Métodos tem de estar implementada para aprovisionar em segurança os próprios dispositivos IoT. Cada camada, de dispositivo, a rede, para a cloud de back-end precisa de garantias de segurança forte.

Melhores práticas de IoT podem ser categorizadas da seguinte forma:

* Fabricante de hardware de IoT ou integrador de
* Desenvolvedor de soluções de IoT
* Implementador de solução de IoT
* Operador de solução de IoT

Este artigo resume [Internet das coisas melhores práticas de segurança](../iot-suite/iot-security-best-practices.md). Veja esse artigo para obter informações mais detalhadas.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Fabricante de hardware de IoT ou integrador de

Se for um fabricante de hardware de IoT ou um integrador de hardware, siga as melhores práticas abaixo:

* **Definir o âmbito de requisitos mínimos de hardware**: o design de hardware deve incluir recursos mínimos necessários para a operação de hardware e nada mais. 
* **Torna o hardware à prova**: criar em mecanismos para detetar a adulteração física de hardware, como abrir a tampa de dispositivo, removendo a parte do dispositivo, etc. 
* **Criar em todo o hardware de segurança**: se [COGS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) permitir, criar recursos de segurança, como o armazenamento seguro e criptografado e a funcionalidade de arranque baseadas no Trusted Platform Module TPM.
* **Fazer com que as atualizações seguras**: a atualização de firmware durante o ciclo de vida do dispositivo é inevitável.

## <a name="iot-solution-developer"></a>Desenvolvedor de soluções de IoT

Se for um desenvolvedor de soluções de IoT, siga as melhores práticas abaixo:

* **Siga a metodologia de desenvolvimento de software seguro**: desenvolvimento de software seguro requer início pensar em segurança desde o início do projeto até à sua implementação, teste e implantação.
* **Escolha o software de código-fonte aberto com cuidado**: software de código aberto fornece uma oportunidade para desenvolver soluções rapidamente.
* **Integrar com cuidado**: muitas das falhas de segurança de software existem no limite do bibliotecas e APIs. 

## <a name="iot-solution-deployer"></a>Implementador de solução de IoT

Se for um implementador de solução de IoT, siga as melhores práticas abaixo:

* **Implementar hardware de forma segura**: Implementações de IoT podem exigir hardware para ser implantado em localizações não seguras, como espaços públicos ou localidades não supervisionadas.
* **Manter as chaves de autenticação seguro**: durante a implementação, cada dispositivo requer identificações de dispositivo e associados a chaves de autenticação geradas pelo serviço em nuvem. Manter essas chaves fisicamente protegidos, mesmo após a implementação. Qualquer chave comprometida pode ser utilizado por um dispositivo de mal-intencionado se disfarçasse como um dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de solução de IoT

Se for um operador de solução de IoT, siga as melhores práticas abaixo:

* **Manter os sistemas atualizados**: Certifique-se de que todos os drivers de dispositivo e de sistemas operativos de dispositivos são atualizados para as versões mais recentes. 
* **Proteger contra atividades maliciosas**: se permite que o sistema operativo, de colocar as capacidades mais recentes de antivírus e antimalware em cada sistema operativo do dispositivo. 
* **Auditoria com frequência**: auditoria infraestrutura IoT para segurança relacionados com problemas é a chave em resposta a incidentes de segurança.
* **Proteger fisicamente a infraestrutura de IoT**: os pior ataques de segurança em relação a infraestrutura IoT são iniciados com acesso físico aos dispositivos.
* **Proteger credenciais de cloud**: credenciais de autenticação em nuvem utilizadas para configurar e operar uma implementação de IoT são possivelmente a maneira mais fácil de obter acesso e comprometer um sistema de IoT. 

