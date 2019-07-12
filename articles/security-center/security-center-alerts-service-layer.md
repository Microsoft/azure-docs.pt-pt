---
title: Deteção para a camada de serviço do Azure no Centro de segurança do Azure de ameaças | Documentos da Microsoft
description: Este tópico apresenta os alertas de camada o serviço do Azure disponíveis no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 8c1733877834f82d9ee2524cf8bf54f532e7d9c4
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571728"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Deteção de ameaças para a camada de serviço do Azure no Centro de segurança do Azure

Este tópico apresenta os alertas do Centro de segurança disponíveis, ao monitorizar as camadas de serviço do Azure seguinte.

* [Camada de rede do Azure](#network-layer)
* [Camada de gestão do Azure (Azure Resource Manager) (pré-visualização)](#management-layer)

>[!NOTE]
>A análise fornecida abaixo com a telemetria que tira partido do Centro de segurança de tocar em feeds internos do Azure, é aplicável a todos os tipos de recursos.

## Camada de rede do Azure<a name="network-layer"></a>

Análise de camada de rede do Centro de segurança baseiam-se de amostra [dados IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), que são recolhidos por routers de base do Azure de cabeçalhos de pacotes. Modelos de machine learning do Centro de segurança com base neste feed de dados, identifique e sinalizador atividades de tráfego malicioso. Para enriquecer os endereços IP, o Centro de segurança tira partido sobre ameaças da base de dados da Microsoft.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Suspeita atividade de rede RDP de saída**|Análise de tráfego de rede de amostragem detetou comunicações de protocolo RDP (Remote Desktop) de saída anómalas provenientes de um recurso na sua implementação. Esta atividade é considerada anormal para este ambiente e pode indicar que o seu recurso foi comprometido e agora está a ser utilizado para o ponto final RDP de força bruta do externo. Tenha em atenção que este tipo de atividade, possivelmente, pode fazer com que o IP seja sinalizados como malicioso por entidades externas.|
|**Atividade de rede RDP para vários destinos de saída suspeita**|Análise de tráfego de rede de amostragem detetou comunicações de protocolo RDP (Remote Desktop) de saída anómalas provenientes de um recurso na sua implementação para vários destinos. Esta atividade é considerada anormal para este ambiente e pode indicar que o seu recurso foi comprometido e agora está a ser utilizado para pontos finais RDP de força bruta do externos. Tenha em atenção que este tipo de atividade, possivelmente, pode fazer com que o IP seja sinalizados como malicioso por entidades externas.|
|**Suspeita atividade de rede SSH de saída**|Análise de tráfego de rede de amostragem detetou comunicações de Secure Shell (SSH) de saída anómalas provenientes de um recurso na sua implementação. Esta atividade é considerada anormal para este ambiente e pode indicar que o seu recurso foi comprometido e agora está a ser utilizado para o ponto final SSH externo força bruta. Tenha em atenção que este tipo de atividade, possivelmente, pode fazer com que o IP seja sinalizados como malicioso por entidades externas.|
|**Atividade de rede SSH para vários destinos de saída suspeita**|Análise de tráfego de rede de amostragem detetou comunicações de Secure Shell (SSH) de saída anómalas provenientes de um recurso na sua implementação para vários destinos. Esta atividade é considerada anormal para este ambiente e pode indicar que o seu recurso foi comprometido e agora está a ser utilizado para a força bruta SSH os pontos finais externos. Tenha em atenção que este tipo de atividade, possivelmente, pode fazer com que o IP seja sinalizados como malicioso por entidades externas.|
|**Entrada SSH atividade de rede suspeita de várias origens**|Análise de tráfego de rede de amostragem detetada anómalas entradas SSH comunicações de várias origens para um recurso na sua implementação. Vários IPs exclusivos a ligar ao seu recurso é considerada anormal para este ambiente. Esta atividade pode indicar uma tentativa de força bruta sua interface SSH a partir de vários anfitriões (Botnet).|
|**Suspeita atividade de rede SSH de entrada**|Análise de tráfego de rede de amostragem detetou comunicações de SSH de entrada anómalas a um recurso na sua implementação. Relativamente elevado número de ligações de entrada para o recurso é considerado anormal para este ambiente. Esta atividade pode indicar uma tentativa de força bruta sua interface SSH.
|**Entrada RDP atividade de rede suspeita de várias origens**|Análise de tráfego de rede de amostragem detetada anómalas entradas RDP comunicações de várias origens para um recurso na sua implementação. Vários IPs exclusivos a ligar ao seu recurso é considerada anormal para este ambiente. Esta atividade pode indicar uma tentativa de força bruta sua interface RDP a partir de vários anfitriões (Botnet).|
|**Suspeita atividade de rede RDP de entrada**|Análise de tráfego de rede de amostragem detetou comunicações de RDP de entrada anómalas a um recurso na sua implementação. Relativamente elevado número de ligações de entrada para o recurso é considerado anormal para este ambiente. Esta atividade pode indicar uma tentativa de força bruta sua interface SSH.|

Para compreender como o Centro de segurança pode utilizar a rede relacionadas com sinais para aplicar a proteção contra ameaças, consulte [detecções de DNS heurística no Centro de segurança do Azure](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).
## Camada de gestão do Azure (Azure Resource Manager) (pré-visualização)<a name ="management-layer"></a>

>[!NOTE]
>Camada de proteção do Centro de segurança com base no Azure Resource Manager está atualmente em pré-visualização.

Centro de segurança oferece uma camada adicional de proteção ao tirar partido de eventos do Azure Resource Manager, que é considerado o plano de controlo para o Azure. Ao analisar os registos do Azure Resource Manager, o Centro de segurança Deteta operações potencialmente prejudiciais ou invulgares no ambiente de subscrição do Azure.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Kit de ferramentas de microBurst executar**|Foi detetado um Kit de ferramentas de reconhecimento do ambiente de cloud conhecidos executar no seu ambiente. A ferramenta "MicroBurst" (consulte https://github.com/NetSPI/MicroBurst) pode ser utilizado por um invasor (ou o testador de penetração) para mapear os seus recursos de subscrição (ões), identificar configurações inseguras e perda de informações confidenciais.|
|**Kit de ferramentas de azurite executar**|Foi detetado um Kit de ferramentas de reconhecimento do ambiente de cloud conhecidos executar no seu ambiente. A ferramenta "Azurite" (consulte https://github.com/mwrlabs/Azurite) pode ser utilizado por um invasor (ou o testador de penetração) para mapear seus recursos de subscrição (ões) e identificar configurações inseguras.|
|**Sessão de gestão suspeita com uma conta inativa**|Análise de registos de atividade de subscrição detetou um comportamento suspeito. Um principal de que não estava em utilização por um longo período de tempo, está a efetuar ações que podem proteger a persistência para um atacante.|
|**Sessão de gestão suspeitas com o PowerShell**|Análise de registos de atividade de subscrição detetou um comportamento suspeito. Um principal de que não utiliza regularmente o PowerShell para gerir o ambiente de subscrição, agora é com o PowerShell e efetuar ações que podem proteger a persistência para um atacante.|
|**Uso de técnicas avançadas de persistência do Azure**|Análise de registos de atividade de subscrição detetou um comportamento suspeito. Funções personalizadas tem recebidas uma identidade legitimized entidades. Isto pode levar o invasor obtenha persistência num ambiente de cliente do Azure.|
|**Atividade de país pouco frequente**|Atividade a partir de uma localização que não foi recentemente ou nunca visitada por nenhum utilizador da organização tenha ocorrido.<br/>Esta deteção considera-se em localizações de atividade para determinar localizações novas e pouco frequentes. O motor de deteção de anomalias armazena informações sobre localizações anteriores utilizadas por utilizadores na organização. 
|**Atividade a partir de endereços IP anónimos**|Atividade de utilizadores de um endereço IP que foi identificado como foi detetado um endereço IP de proxy anónimo. <br/>Estes proxies são utilizados por pessoas que pretendem ocultar o endereço IP do seu dispositivo e podem ser utilizadas para más intenções. Esta deteção tira partido de um algoritmo de machine learning que reduz o "falsos positivos", como endereços IP marcados incorretamente, que são amplamente utilizados por utilizadores na organização.|
|**Deslocação impossível detetada**|Duas atividades de utilizador (é uma única ou múltiplas sessões) ocorreram, provenientes de localizações distantes num período de tempo mais curto do que o tempo levaria ao utilizador que viajam de primeira localização para o segundo. Isto indica que um utilizador diferente está a utilizar as mesmas credenciais. <br/>Esta deteção tira partido de um algoritmo de machine learning que ignora "positivos falsos óbvios" que contribuem para as condições de deslocação impossível, como as VPNs e localizações regularmente utilizadas por outros utilizadores na organização. A deteção tem um período de aprendizagem inicial de sete dias durante o qual aprende a identificar o padrão de atividade de um novo utilizador.|

>[!NOTE]
> Vários dos analytics acima ligados ao Microsoft Cloud App Security (MCAS). Para beneficiar destes dados analíticos, é necessária uma licença MCAS ativada. Se tiver uma licença MCAS, em seguida, estes alertas estão ativados por predefinição. Para desativá-las:
>
> 1. No painel do Centro de segurança, selecione **política de segurança**. Para a subscrição que pretende alterar, clique em **editar as definições de**.
> 2. Clique em **deteção de ameaças**.
> 3. Sob **permitir integrações**, desmarque a opção **permitir que o Microsoft Cloud App Security a aceder aos meus dados**e clique em **guardar**.

>[!NOTE]
>Centro de segurança do Azure armazena dados relacionados com a segurança dos clientes na mesma geografia que o seu recurso. Se a Microsoft ainda não tiver implementado Centro de segurança do Azure numa área geográfica do recurso, em seguida, ele armazena os dados nos Estados Unidos. Quando a segurança de aplicação na Cloud da Microsoft (MCAS) está ativada, estas informações são armazenadas em conformidade com as regras de localização geográfica do MCAS. Ver [armazenamento de dados para os serviços para obter mais informações, independente de região](http://azuredatacentermap.azurewebsites.net/).
