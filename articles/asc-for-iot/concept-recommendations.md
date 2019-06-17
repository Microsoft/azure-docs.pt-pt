---
title: Compreender o Centro de segurança do Azure para recomendações de segurança de IoT pré-visualização | Documentos da Microsoft
description: Saiba mais sobre o conceito de recomendações de segurança e como elas são usadas no Centro de segurança do Azure para IoT.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 122a1585abf43d1aedc7f329cd7e41a6228f8c3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65200676"
---
# <a name="security-recommendations"></a>Recomendações de segurança

> [!IMPORTANT]
> Centro de segurança do Azure para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

O Centro de segurança do Azure (ASC) para IoT analisa os seus dispositivos de IoT e de recursos do Azure e fornece recomendações de segurança para reduzir a superfície de ataque. Recomendações de segurança são passíveis de ação e têm como objetivo ajudar os clientes a respeitar os melhores práticas de segurança.

Neste artigo, encontrará uma lista de recomendações que pode ser acionada no seu IoT Hub e/ou dispositivos de IoT.

## <a name="recommendations-for-iot-devices"></a>Recomendações para dispositivos de IoT

Recomendações de dispositivos fornecem informações e sugestões para melhorar a postura de segurança do dispositivo. 

| Gravidade | Name                                                      | Origem de Dados | Descrição                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Médio   | Portas abertas no dispositivo                                      | Agente       | Um ponto de final de escuta foi encontrado no dispositivo                                                                                                                                                          |
| Médio   | Política de firewall permissiva encontrada das cadeias. | Agente       | Permitido a política de firewall foi encontrada (entrada/saída). Política de firewall deve negar todo o tráfego por predefinição e definem regras para permitir a comunicação necessária de/para o dispositivo.                               |
| Médio   | Foi encontrada a regra de firewall permissiva na cadeia de entrada     | Agente       | Foi encontrada uma regra de firewall que contém um padrão de permissivo para uma vasta gama de endereços IP ou as portas.                                                                                    |
| Médio   | Foi encontrada a regra de firewall permissiva na cadeia de saída    | Agente       | Foi encontrada uma regra de firewall que contém um padrão de permissivo para uma vasta gama de endereços IP ou as portas.                                                                                   |
| Médio   | Falha na validação de linha de base do sistema de operação           | Agente       | Dispositivo não está em conformidade com [benchmarks da CIS Linux](https://www.cisecurity.org/cis-benchmarks/)                                                                                                         |

### <a name="operational-recommendations-for-iot-devices"></a>Recomendações operacionais para dispositivos de IoT

Recomendações operacionais fornecem informações e sugestões para melhorar a configuração do agente de segurança.

| Gravidade | Name                                    | Origem de Dados | Descrição                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Baixa      | Agente envia mensagens unutilized          | Agente       | 10% ou mais das mensagens de segurança foram inferior a 4kb nas últimas 24 horas.  |
| Baixa      | Configuração de duplo de segurança não ideal | Agente       | Configuração de duplo de segurança não é ideal.                                        |
| Baixa      | Conflito de configuração de duplo de segurança    | Agente       | Conflitos foram identificados na configuração de duplo de segurança.                           |


## <a name="recommendations-for-iot-hub"></a>Recomendações para o IoT Hub

Os alertas de recomendação fornecem informações e sugestões para ações melhorar a postura de segurança do seu ambiente.  

| Gravidade | Name                                                     | Origem de Dados | Descrição                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Alta     | Credenciais de autenticação idênticos utilizadas por vários dispositivos | IoT Hub     | Credenciais de autenticação do IoT Hub são utilizadas por vários dispositivos. Isto pode indicar um dispositivo ilegítimo representando um dispositivo legítimo. Utilização da credencial duplicados aumenta o risco de representação do dispositivo por um ator malicioso. |
| Médio   | Política de filtro IP predefinido deve ser negar                  | IoT Hub     | Configuração de filtro IP deve ter regras definidas para o tráfego permitido e deve por predefinição, negam o tráfego de todos os outros por predefinição.                                                                                                     |
| Médio   | Regra de filtro de IP inclui grande intervalo IP                   | IoT Hub     | Um intervalo IP de origem de regra do permitir IP filtro é demasiado grande. Regras demasiado permissivas podem expor o seu hub IoT para atores maliciosos.                                                                                       |
| Baixa      | Ativar registos de diagnóstico do IoT Hub                       | IoT Hub     | Ativar os registos e mantenha-os para até um ano. Manter logs permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida.                                       |
|