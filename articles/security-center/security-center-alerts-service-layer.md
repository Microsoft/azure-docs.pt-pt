---
title: Detecção de ameaças da camada de serviço do Azure – central de segurança do Azure
description: Este tópico apresenta os alertas da camada de serviço do Azure disponíveis na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 08/25/2019
ms.author: memildin
ms.openlocfilehash: 7db9f50b4fb1a9309737f05db13a914f414372ed
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186509"
---
# <a name="threat-detection-for-the-azure-service-layer-in-azure-security-center"></a>Detecção de ameaças para a camada de serviço do Azure na central de segurança do Azure

Este tópico apresenta os alertas da central de segurança do Azure disponíveis ao monitorar as seguintes camadas de serviço do Azure:

* [Camada de rede do Azure](#network-layer)
* [Camada de gerenciamento do Azure (Azure Resource Manager) (visualização)](#management-layer)
* [Cofre de Chaves do Azure](#azure-keyvault)

>[!NOTE]
>A análise a seguir é aplicável a todos os tipos de recurso. Eles usam a telemetria que a central de segurança fornece ao tocar em feeds internos do Azure.

## Camada de rede do Azure<a name="network-layer"></a>

A central de segurança rede – a análise de camada baseia-se nos [dados IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export)de exemplo, que são os cabeçalhos de pacote coletados pelos roteadores do Azure Core. Com base neste feed de dados, os modelos de aprendizado de máquina da central de segurança identificam e sinalizam atividades de tráfego mal-intencionado. Para enriquecer os endereços IP, a central de segurança usa o banco de dados de inteligência contra ameaças da Microsoft.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Atividade de rede RDP de saída suspeita**|A análise de tráfego de rede de amostra detectou comunicação de saída anômala protocolo RDP (RDP), originando-se de um recurso em sua implantação. Essa atividade é considerada anormal para esse ambiente. Isso pode indicar que o recurso foi comprometido e agora está sendo usado para atacar a força bruta de um ponto de extremidade RDP externo. Esse tipo de atividade pode fazer com que o IP seja sinalizado como mal-intencionado por entidades externas.|
|**Atividade de rede RDP de saída suspeita para vários destinos**|A análise de tráfego de rede de amostra detectou comunicação de RDP de saída anômala, originando-se de um recurso em sua implantação para vários destinos. Essa atividade é considerada anormal para esse ambiente. Isso pode indicar que o recurso foi comprometido e agora está sendo usado para ataques de força bruta de pontos de extremidade RDP externos. Esse tipo de atividade pode fazer com que o IP seja sinalizado como mal-intencionado por entidades externas.|
|**Atividade de rede SSH de saída suspeita**|A análise de tráfego de rede de amostra detectou comunicação de SSH (Secure Shell de saída anormal), originando-se de um recurso em sua implantação. Essa atividade é considerada anormal para esse ambiente. Isso pode indicar que o recurso foi comprometido e agora está sendo usado para atacar a força bruta de um ponto de extremidade de SSH externo. Esse tipo de atividade pode fazer com que o IP seja sinalizado como mal-intencionado por entidades externas.|
|**Atividade de rede SSH de saída suspeita para vários destinos**|A análise de tráfego de rede de amostra detectou comunicação SSH de saída anômala, originando-se de um recurso em sua implantação para vários destinos. Essa atividade é considerada anormal para esse ambiente. Isso pode indicar que o recurso foi comprometido e agora está sendo usado para atacar pontos de extremidade de SSH externos de ataque de força bruta. Esse tipo de atividade pode fazer com que o IP seja sinalizado como mal-intencionado por entidades externas.|
|**Atividade de rede SSH de entrada suspeita de várias fontes**|A análise de tráfego de rede de amostra detectou comunicações de SSH de entrada anômala de várias fontes para um recurso em sua implantação. Vários IPs exclusivos que se conectam ao seu recurso são considerados anormais para esse ambiente. Essa atividade pode indicar uma tentativa de ataque de força bruta à interface SSH de vários hosts (botnet).|
|**Atividade de rede SSH de entrada suspeita**|A análise de tráfego de rede de amostra detectou comunicação de SSH de entrada anômala para um recurso em sua implantação. Um número relativamente alto de conexões de entrada para seu recurso é considerado anormal para esse ambiente. Essa atividade pode indicar uma tentativa de ataque de força bruta à sua interface SSH.
|**Atividade de rede RDP de entrada suspeita de várias fontes**|A análise de tráfego de rede de amostra detectou comunicações de RDP de entrada anômalas de várias fontes para um recurso em sua implantação. Vários IPs exclusivos que se conectam ao seu recurso são considerados anormais para esse ambiente. Essa atividade pode indicar uma tentativa de ataque de força bruta de sua interface RDP de vários hosts (botnet).|
|**Atividade de rede RDP de entrada suspeita**|A análise de tráfego de rede de amostra detectou comunicação de RDP de entrada anômala para um recurso em sua implantação. Um número relativamente alto de conexões de entrada para seu recurso é considerado anormal para esse ambiente. Essa atividade pode indicar uma tentativa de ataque de força bruta à sua interface SSH.|
|**A comunicação de rede com um endereço mal-intencionado foi detectada**|A análise de tráfego de rede de amostra detectou a comunicação proveniente de um recurso em sua implantação com um servidor de comando e controle (C & C) possível. Esse tipo de atividade poderia fazer com que o IP fosse sinalizado como mal-intencionado por entidades externas.|

Para entender como a central de segurança pode usar sinais relacionados à rede para aplicar a proteção contra ameaças, consulte [detecções de heurística do DNS na central de segurança do Azure](https://azure.microsoft.com/blog/heuristic-dns-detections-in-azure-security-center/).

>[!NOTE]
>Os alertas de detecção de ameaças da camada de rede do Azure, na central de segurança do Azure, são gerados somente em máquinas virtuais que receberam o mesmo endereço IP para a hora inteira durante a qual ocorreu uma comunicação suspeita. Isso se aplica a máquinas virtuais, bem como a máquinas virtuais que são criadas na assinatura do cliente como parte de um serviço gerenciado (por exemplo, AKS, databricks).

## Camada de gerenciamento do Azure (Azure Resource Manager) (visualização)<a name ="management-layer"></a>

>[!NOTE]
>A camada de proteção da central de segurança baseada no Azure Resource Manager está atualmente em versão prévia.

A central de segurança oferece uma camada adicional de proteção usando eventos Azure Resource Manager, que é considerada o plano de controle do Azure. Analisando os registros de Azure Resource Manager, a central de segurança detecta operações comuns ou potencialmente prejudiciais no ambiente de assinatura do Azure.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Execução do kit de ferramentas de microburst**|Foi detectada uma execução conhecida do kit de ferramentas de reconhecimento de ambiente de nuvem em seu ambiente. A ferramenta [microburst](https://github.com/NetSPI/MicroBurst) pode ser usada por um invasor (ou testador de penetração) para mapear os recursos de suas assinaturas, identificar configurações inseguras e vazar informações confidenciais.|
|**Execução do kit de ferramentas azurite**|Foi detectada uma execução conhecida do kit de ferramentas de reconhecimento de ambiente de nuvem em seu ambiente. A ferramenta [azurite](https://github.com/mwrlabs/Azurite) pode ser usada por um invasor (ou testador de penetração) para mapear os recursos de suas assinaturas e identificar configurações inseguras.|
|**Sessão de gerenciamento suspeito usando uma conta inativa**|A análise de logs de atividade de assinatura detectou um comportamento suspeito. Uma entidade de segurança que não está em uso por um longo período de tempo agora está executando ações que podem proteger a persistência de um invasor.|
|**Sessão de gerenciamento suspeito usando o PowerShell**|A análise de logs de atividade de assinatura detectou um comportamento suspeito. Uma entidade que não usa regularmente o PowerShell para gerenciar o ambiente de assinatura agora está usando o PowerShell e executando ações que podem proteger a persistência de um invasor.|
|**Uso de técnicas avançadas de persistência do Azure**|A análise de logs de atividade de assinatura detectou um comportamento suspeito. As funções personalizadas recebem entidades de identidade legitimized. Isso pode levar o invasor a obter persistência em um ambiente do cliente do Azure.|
|**Atividade de um país infrequente**|A atividade de um local que não foi recentemente ou visitado por qualquer usuário na organização ocorreu.<br/>Esta deteção considera-se em localizações de atividade para determinar localizações novas e pouco frequentes. O motor de deteção de anomalias armazena informações sobre localizações anteriores utilizadas por utilizadores na organização. 
|**Atividade de endereços IP anônimos**|A atividade de usuários de um endereço IP que foi identificada como um endereço IP de proxy anônimo foi detectada. <br/>Esses proxies são usados por pessoas que desejam ocultar o endereço IP do dispositivo e podem ser usados para uma intenção mal-intencionada. Essa detecção usa um algoritmo de aprendizado de máquina que reduz os falsos positivos, como endereços IP com marca de tipo incorreta que são amplamente usados por usuários na organização.|
|**Viagem impossível detectada**|Duas atividades do usuário (em uma única ou várias sessões) ocorreram, provenientes de locais geograficamente distantes. Isso ocorre dentro de um período de tempo menor do que o tempo que levaria o usuário a viajar do primeiro local para o segundo. Isso indica que um usuário diferente está usando as mesmas credenciais. <br/>Essa detecção usa um algoritmo de aprendizado de máquina que ignora os falsos positivos óbvios que contribuem para as condições de viagem impossíveis, como VPNs e locais usados regularmente por outros usuários na organização. A detecção tem um período de aprendizado inicial de sete dias, durante o qual ele aprende o padrão de atividade de um novo usuário.|

>[!NOTE]
> Várias das análises anteriores são alimentadas por Microsoft Cloud App Security. Para se beneficiar dessas análises, você deve ativar uma licença de Cloud App Security. Se você tiver uma licença Cloud App Security, esses alertas serão habilitados por padrão. Para desabilitá-los:
>
> 1. Na folha **central de segurança** , selecione **política de segurança**. Para a assinatura que você deseja alterar, selecione **Editar configurações**.
> 2. Selecione **detecção de ameaças**.
> 3. Em **habilitar integrações**, desmarque **permitir que Microsoft Cloud app Security acesse meus dados**e selecione **salvar**.

>[!NOTE]
>A central de segurança armazena dados de clientes relacionados à segurança na mesma área geográfica de seu recurso. Se a Microsoft ainda não tiver implantado a central de segurança na área geográfica do recurso, ele armazenará os dados no Estados Unidos. Quando Cloud App Security está habilitado, essas informações são armazenadas de acordo com as regras de localização geográfica do Cloud App Security. Para obter mais informações, consulte [armazenamento de dados para serviços não regionais](https://azuredatacentermap.azurewebsites.net/).

## Azure Key Vault<a name="azure-keyvault"></a>

Azure Key Vault é um serviço de nuvem que protege chaves de criptografia e segredos como certificados, cadeias de conexão e senhas. 

A central de segurança do Azure inclui proteção de ameaças avançada e nativa do Azure para Azure Key Vault, fornecendo uma camada adicional de inteligência de segurança. A central de segurança detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de Key Vault. Essa camada de proteção permite que você resolva as ameaças sem ser um especialista em segurança e sem a necessidade de gerenciar sistemas de monitoramento de segurança de terceiros.  

Quando ocorrem atividades anômalas, a central de segurança mostra alertas e, opcionalmente, os envia por email aos administradores de assinatura. Esses alertas incluem os detalhes da atividade suspeita e recomendações sobre como investigar e corrigir ameaças. 

> [!NOTE]
> Esse serviço não está disponível no momento nas regiões do Azure governamental e do soberanas Cloud.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Acesso de um nó de saída do TOR a um Key Vault**|O Key Vault foi acessado por alguém usando o sistema de anonimato de IP TOR para ocultar seu local. Os atores mal-intencionados muitas vezes tentam ocultar seu local ao tentar obter acesso não autorizado a recursos conectados à Internet.|
|**Alteração de política suspeita e consulta de segredo em um Key Vault**|Uma alteração de política de Key Vault foi feita e as operações para listar e/ou obter segredos ocorreram. Além disso, esse padrão de operação normalmente não é executado pelo usuário nesse cofre. Isso é altamente um indício de que a Key Vault está comprometida e os segredos dentro foram roubados por um ator mal-intencionado.|
|**Listagem e consulta secreta suspeitas em um Key Vault**|Uma operação de lista secreta foi seguida por muitas operações Get secretas. Além disso, esse padrão de operação normalmente não é executado pelo usuário nesse cofre. Isso indica que alguém pode estar despejando os segredos armazenados no Key Vault para fins potencialmente mal-intencionados.|
|**Um par de aplicativos de usuário incomum acessou um Key Vault**|O Key Vault foi acessado por um emparelhamento de aplicativo de usuário que normalmente não o acessa. Essa pode ser uma tentativa de acesso legítima (por exemplo, após uma atualização de infraestrutura ou código). Isso também é uma possível indicação de que sua infraestrutura está comprometida e um ator mal-intencionado está tentando acessar seu Key Vault.|
|**Aplicativo incomum acessado um Key Vault**|O Key Vault foi acessado por um aplicativo que normalmente não o acessa. Essa pode ser uma tentativa de acesso legítima (por exemplo, após uma atualização de infraestrutura ou código). Isso também é uma possível indicação de que sua infraestrutura está comprometida e um ator mal-intencionado está tentando acessar seu Key Vault.|
|**Um usuário incomum acessou um Key Vault**|O Key Vault foi acessado por um usuário que normalmente não o acessa. Essa pode ser uma tentativa de acesso legítima (por exemplo, um novo usuário que precisa de acesso ingressou na organização). Isso também é uma possível indicação de que sua infraestrutura está comprometida e um ator mal-intencionado está tentando acessar seu Key Vault.|
|**Padrão de operação incomum em um Key Vault**|Um conjunto incomum de operações de Key Vault foi realizado em comparação com os dados históricos. Key Vault atividade geralmente é a mesma ao longo do tempo. Essa pode ser uma alteração legítima na atividade. Como alternativa, sua infraestrutura pode estar comprometida e investigações adicionais são necessárias.|
|**Alto volume de operações em um Key Vault**|Um volume maior de operações de Key Vault foi realizado em comparação com os dados históricos. Key Vault atividade geralmente é a mesma ao longo do tempo. Essa pode ser uma alteração legítima na atividade. Como alternativa, sua infraestrutura pode estar comprometida e investigações adicionais são necessárias.|
|**O usuário acessou alto volume de cofres de chaves**|O número de cofres que um usuário ou aplicativo acessa foi alterado em comparação com os dados históricos. Key Vault atividade geralmente é a mesma ao longo do tempo. Essa pode ser uma alteração legítima na atividade. Como alternativa, sua infraestrutura pode estar comprometida e investigações adicionais são necessárias.|