---
title: Detecção de ameaças para a camada de serviço do Azure na central de segurança do Azure | Microsoft Docs
description: Este tópico apresenta os alertas da camada de serviço do Azure disponíveis na central de segurança do Azure.
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
ms.date: 8/18/2019
ms.author: v-mohabe
ms.openlocfilehash: 70b43c65703316e5dee8e9cf2cf86fe982a49592
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624773"
---
# <a name="threat-detection-for-azure-service-layer-in-azure-security-center"></a>Detecção de ameaças para a camada de serviço do Azure na central de segurança do Azure

Este tópico apresenta os alertas da central de segurança disponíveis ao monitorar as seguintes camadas de serviço do Azure.

* [Camada de rede do Azure](#network-layer)
* [Camada de gerenciamento do Azure (Azure Resource Manager) (visualização)](#management-layer)

>[!NOTE]
>Usando a telemetria que a central de segurança aproveita de tocar em feeds internos do Azure, a análise fornecida abaixo é aplicável a todos os tipos de recursos.

## Camada de rede do Azure<a name="network-layer"></a>

A central de segurança rede – a análise de camada baseia-se nos [dados IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)de exemplo, que são os cabeçalhos de pacote coletados pelos roteadores do Azure Core. Com base neste feed de dados, os modelos de aprendizado de máquina da central de segurança identificam e sinalizam atividades de tráfego mal-intencionado. Para enriquecer os endereços IP, a central de segurança aproveita o banco de dados de inteligência contra ameaças da Microsoft.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Atividade de rede RDP de saída suspeita**|A análise de tráfego de rede de amostra detectou comunicação de saída anômala protocolo RDP (RDP) proveniente de um recurso em sua implantação. Essa atividade é considerada anormal para esse ambiente e pode indicar que o recurso foi comprometido e está sendo usado agora para um ponto de extremidade RDP externo de força bruta. Tenha em atenção que este tipo de atividade, possivelmente, pode fazer com que o IP seja sinalizados como malicioso por entidades externas.|
|**Atividade de rede RDP de saída suspeita para vários destinos**|A análise de tráfego de rede de amostra detectou comunicação de saída anômala protocolo RDP (RDP) proveniente de um recurso em sua implantação para vários destinos. Essa atividade é considerada anormal para esse ambiente e pode indicar que o recurso foi comprometido e está sendo usado agora para pontos de extremidade externos de RDP de força bruta. Tenha em atenção que este tipo de atividade, possivelmente, pode fazer com que o IP seja sinalizados como malicioso por entidades externas.|
|**Atividade de rede SSH de saída suspeita**|A análise de tráfego de rede de amostra detectou comunicação de SSH (Secure Shell de saída anormal) proveniente de um recurso em sua implantação. Essa atividade é considerada anormal para esse ambiente e pode indicar que o recurso foi comprometido e está sendo usado agora para ponto de extremidade SSH externo de força bruta. Tenha em atenção que este tipo de atividade, possivelmente, pode fazer com que o IP seja sinalizados como malicioso por entidades externas.|
|**Atividade de rede SSH de saída suspeita para vários destinos**|A análise de tráfego de rede de amostra detectou comunicação de SSH (Secure Shell de saída anômala) proveniente de um recurso em sua implantação para vários destinos. Essa atividade é considerada anormal para esse ambiente e pode indicar que o recurso foi comprometido e agora está sendo usado para pontos de extremidade SSH externos de força bruta. Tenha em atenção que este tipo de atividade, possivelmente, pode fazer com que o IP seja sinalizados como malicioso por entidades externas.|
|**Atividade de rede SSH de entrada suspeita de várias fontes**|A análise de tráfego de rede de amostra detectou comunicações de SSH de entrada anômala de várias fontes para um recurso em sua implantação. Vários IPs exclusivos que se conectam ao seu recurso são considerados anormais para esse ambiente. Essa atividade pode indicar uma tentativa de forçar a força bruta da interface SSH de vários hosts (botnet).|
|**Atividade de rede SSH de entrada suspeita**|A análise de tráfego de rede de amostra detectou comunicação de SSH de entrada anômala para um recurso em sua implantação. Um número relativamente alto de conexões de entrada para seu recurso é considerado anormal para esse ambiente. Essa atividade pode indicar uma tentativa de forçar a força bruta da sua interface SSH.
|**Atividade de rede RDP de entrada suspeita de várias fontes**|A análise de tráfego de rede de amostra detectou comunicações de RDP de entrada anômalas de várias fontes para um recurso em sua implantação. Vários IPs exclusivos que se conectam ao seu recurso são considerados anormais para esse ambiente. Essa atividade pode indicar uma tentativa de forçar a força bruta da interface RDP de vários hosts (botnet).|
|**Atividade de rede RDP de entrada suspeita**|A análise de tráfego de rede de amostra detectou comunicação de RDP de entrada anômala para um recurso em sua implantação. Um número relativamente alto de conexões de entrada para seu recurso é considerado anormal para esse ambiente. Essa atividade pode indicar uma tentativa de forçar a força bruta da sua interface SSH.|
|**A comunicação de rede com um endereço mal-intencionado foi detectada**|A análise de tráfego de rede de amostra detectou a comunicação proveniente de um recurso em sua implantação com um servidor de comando e controle (C & C) possível. Tenha em atenção que este tipo de atividade, possivelmente, pode fazer com que o IP seja sinalizados como malicioso por entidades externas.|

Para entender como a central de segurança pode usar sinais relacionados à rede para aplicar a proteção contra ameaças, confira [detecções heurísticas de DNS na central de segurança do Azure](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).
## Camada de gerenciamento do Azure (Azure Resource Manager) (visualização)<a name ="management-layer"></a>

>[!NOTE]
>A camada de proteção da central de segurança baseada no Azure Resource Manager está atualmente em versão prévia.

A central de segurança oferece uma camada adicional de proteção, aproveitando Azure Resource Manager eventos, que é considerado o plano de controle do Azure. Analisando os registros de Azure Resource Manager, a central de segurança detecta operações comuns ou potencialmente prejudiciais no ambiente de assinatura do Azure.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Execução do kit de ferramentas de microburst**|Foi detectada uma execução conhecida do kit de ferramentas de reconhecimento de ambiente de nuvem em seu ambiente. A ferramenta "microburst" (consulte https://github.com/NetSPI/MicroBurst) pode ser usada por um invasor (ou testador de penetração) para mapear seus recursos de assinatura, identificar configurações inseguras e vazar informações confidenciais.|
|**Execução do kit de ferramentas azurite**|Foi detectada uma execução conhecida do kit de ferramentas de reconhecimento de ambiente de nuvem em seu ambiente. A ferramenta "azurite" (consulte https://github.com/mwrlabs/Azurite) pode ser usada por um invasor (ou testador de penetração) para mapear seus recursos de assinatura e identificar configurações inseguras.|
|**Sessão de gerenciamento suspeito usando uma conta inativa**|A análise de logs de atividade de assinatura detectou um comportamento suspeito. Uma entidade que não estava em uso por um longo período de tempo agora está executando ações que podem proteger a persistência de um invasor.|
|**Sessão de gerenciamento suspeito usando o PowerShell**|A análise de logs de atividade de assinatura detectou um comportamento suspeito. Uma entidade que não usa regularmente o PowerShell para gerenciar o ambiente de assinatura, agora está usando o PowerShell e executando ações que podem proteger a persistência de um invasor.|
|**Uso de técnicas avançadas de persistência do Azure**|A análise de logs de atividade de assinatura detectou um comportamento suspeito. As funções personalizadas recebem entidades de identidade legitimized. Isso pode levar o invasor a obter persistência em um ambiente do cliente do Azure.|
|**Atividade de país pouco frequente**|A atividade de um local que não foi recentemente ou nunca foi visitada por nenhum usuário na organização ocorreu.<br/>Esta deteção considera-se em localizações de atividade para determinar localizações novas e pouco frequentes. O motor de deteção de anomalias armazena informações sobre localizações anteriores utilizadas por utilizadores na organização. 
|**Atividade a partir de endereços IP anónimos**|A atividade de usuários de um endereço IP que foi identificada como um endereço IP de proxy anônimo foi detectada. <br/>Estes proxies são utilizados por pessoas que pretendem ocultar o endereço IP do seu dispositivo e podem ser utilizadas para más intenções. Essa detecção aproveita um algoritmo de aprendizado de máquina que reduz os "falsos positivos", como endereços IP marcados com mis que são amplamente usados por usuários na organização.|
|**Viagem impossível detectada**|Duas atividades do usuário (são uma única ou várias sessões) ocorreram, originando-se de localizações geograficamente distantes dentro de um período de tempo menor do que o tempo que teria levado para o usuário viajar do primeiro local para o segundo. Isso indica que um usuário diferente está usando as mesmas credenciais. <br/>Essa detecção aproveita um algoritmo de aprendizado de máquina que ignora os "falsos positivos" claros que contribuem para as condições de viagem impossíveis, como VPNs e locais usados regularmente por outros usuários na organização. A deteção tem um período de aprendizagem inicial de sete dias durante o qual aprende a identificar o padrão de atividade de um novo utilizador.|

>[!NOTE]
> Várias das análises acima são alimentadas por Microsoft Cloud App Security (MCAS). Para se beneficiar dessas análises, uma licença MCAS ativada é necessária. Se você tiver uma licença do MCAS, esses alertas serão habilitados por padrão. Para desabilitá-los:
>
> 1. Na folha central de segurança, selecione **política de segurança**. Para a assinatura que você deseja alterar, clique em **Editar configurações**.
> 2. Clique em **detecção de ameaças**.
> 3. Em **habilitar integrações**, desmarque **permitir que Microsoft Cloud app Security acesse meus dados**e clique em **salvar**.

>[!NOTE]
>A central de segurança do Azure armazena dados de clientes relacionados à segurança na mesma área geográfica de seu recurso. Se a Microsoft ainda não tiver implantado a central de segurança do Azure na área geográfica do recurso, ele armazenará os dados no Estados Unidos. Quando Microsoft Cloud App Security (MCAS) está habilitado, essas informações são armazenadas de acordo com as regras de localização geográfica de MCAS. Consulte [armazenamento de dados para serviços não regionais para obter mais informações](https://azuredatacentermap.azurewebsites.net/).
