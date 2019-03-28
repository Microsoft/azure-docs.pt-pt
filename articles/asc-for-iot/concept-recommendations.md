---
title: Compreender o ASC para recomendações de segurança de IoT pré-visualização | Documentos da Microsoft
description: Saiba mais sobre o conceito de recomendações de segurança e como eles são usados no ASC para IoT.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 02ced504-d3aa-4770-9d10-b79f80af366c
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 1e4582d93d1e3380ecdabdb241f27839d4da4565
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541861"
---
# <a name="security-recommendations"></a>Recomendações de segurança

> [!IMPORTANT]
> ASC para IoT está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Com base na análise de solução em curso, o ASC para IoT fornece as seguintes recomendações quando necessário para o ajudar a melhorar e proteger os seus dispositivos, o estado operacional e o ambiente geral do IoT Hub. 


## <a name="device-recommendations"></a>Recomendações de dispositivo

Recomendações de dispositivos fornecem informações e sugestões para melhorar a segurança do dispositivo e o comportamento. 

| Gravidade | Name                                                      | Origem de Dados | Descrição                                                                                                                                                                                           |
|----------|-----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Médio   | Portas abertas no dispositivo                                      | Agente       | Foi encontrado um ponto final de escuta no dispositivo                                                                                                                                                          |
| Médio   | Política de firewall permissiva encontrada das cadeias. | Agente       | Permitido a política de firewall foi encontrada (entrada/saída). Política de firewall deve negar todo o tráfego por predefinição e definem regras para permitir a comunicação necessária de/para o dispositivo.                               |
| Médio   | Foi encontrada a regra de firewall permissiva na cadeia de entrada     | Agente       | Foi encontrada uma regra de firewall que contém um padrão de permissivo para uma vasta gama de endereços IP ou as portas.                                                                                    |
| Médio   | Foi encontrada a regra de firewall permissiva na cadeia de saída    | Agente       | Foi encontrada uma regra de firewall que contém um padrão de permissivo para uma vasta gama de endereços IP ou as portas.                                                                                   |
| Médio   | Falha na validação de linha de base do sistema de operação           | Agente       | Dispositivo não está em conformidade com [benchmarks da CIS Linux](https://www.cisecurity.org/cis-benchmarks/)                                                                                                         |

### <a name="operational-recommendation"></a>Recomendação operacional

Recomendações operacionais fornecem informações e sugestões para melhorar a configuração do agente.

| Gravidade | Name                                    | Origem de Dados | Descrição                                                                       |
|----------|-----------------------------------------|-------------|-----------------------------------------------------------------------------------|
| Baixa      | Agente envia mensagens unutilized          | Agente       | 10% ou mais das mensagens de segurança foram inferior a 4kb nas últimas 24 horas.  |
| Baixa      | Configuração de duplo de segurança não ideal | Agente       | Configuração de duplo de segurança não é ideal.                                        |
| Baixa      | Conflito de configuração de duplo de segurança    | Agente       | Conflitos foram identificados na configuração de duplo de segurança.                           |


## <a name="iot-hub-recommendations"></a>Recomendações do IoT Hub

Os alertas de recomendação fornecem informações e sugestões para ações melhorar a postura de segurança do seu ambiente.  

| Gravidade | Name                                                     | Origem de Dados | Descrição                                                                                                                                                                                                             |
|----------|----------------------------------------------------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Elevado     | Credenciais de autenticação idênticos utilizadas por vários dispositivos | IoT Hub     | Credenciais de autenticação do IoT Hub são utilizadas por vários dispositivos. Isto pode indicar um dispositivo ilegítimo representando um dispositivo legítimo. Utilização da credencial duplicados aumenta o risco de representação do dispositivo por um ator malicioso. |
| Médio   | Política de filtro IP predefinido deve ser negar                  | IoT Hub     | Configuração de filtro IP deve ter regras definidas para o tráfego permitido e deve por predefinição, negam o tráfego de todos os outros por predefinição.                                                                                                     |
| Médio   | Regra de filtro de IP inclui grande intervalo IP                   | IoT Hub     | Um intervalo IP de origem de regra do permitir IP filtro é demasiado grande. Regras demasiado permissivas podem expor o seu hub IoT para atores maliciosos.                                                                                       |
| Baixa      | Ativar os registos de diagnóstico no Hub IoT                       | IoT Hub     | Ativar os registos e mantenha-os para até um ano. Manter logs permite-lhe recriar os registos de atividade para efeitos de investigação quando ocorrer um incidente de segurança ou a rede estiver comprometida.                                       |
|