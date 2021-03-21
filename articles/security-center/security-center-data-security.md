---
title: Segurança de Dados do Centro de Segurança do Azure | Microsoft Docs
description: Este documento explica como os dados são geridos e salvaguardados no Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2020
ms.author: memildin
ms.openlocfilehash: 0c21c916d152188031212b47704dfb41d3f64a82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596277"
---
# <a name="azure-security-center-data-security"></a>Segurança de dados do Azure Security Center

Para ajudar os clientes a prevenir, detetar e responder a ameaças, o Azure Security Center recolhe e processa dados relacionados com a segurança, incluindo informações de configuração, metadados, registos de eventos e muito mais. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço.

Este artigo explica como os dados são geridos e salvaguardados no Centro de Segurança.

## <a name="data-sources"></a>Origens de dados
O Security Center analisa dados das seguintes fontes para fornecer visibilidade ao seu estado de segurança, identificar vulnerabilidades e recomendar mitigações e detetar ameaças ativas:

- **Serviços Azure**: Utiliza informações sobre a configuração dos serviços Azure que implementou comunicando com o fornecedor de recursos desse serviço.
- **Tráfego de rede**: Utiliza metadados de tráfego de rede amostrados da infraestrutura da Microsoft, tais como fonte/destino IP/porta, tamanho do pacote e protocolo de rede.
- **Soluções parceiras**: Utiliza alertas de segurança de soluções parceiras integradas, tais como firewalls e soluções antimalware.
- **As suas máquinas**: Utiliza detalhes de configuração e informações sobre eventos de segurança, tais como registos de eventos e auditorias do Windows, e mensagens sislog das suas máquinas.


## <a name="data-protection"></a>Proteção de dados

### <a name="data-segregation"></a>Segregação de dados
Os dados são mantidos logicamente separados em cada componente durante todo o serviço. Todos os dados são etiquetados por organização. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço.

### <a name="data-access"></a>Acesso a dados
Para fornecer recomendações de segurança e investigar potenciais ameaças à segurança, o pessoal da Microsoft pode aceder a informações recolhidas ou analisadas pelos serviços Azure, incluindo eventos de criação de processos, e outros artefactos, que podem incluir involuntariamente dados de clientes ou dados pessoais das suas máquinas. 

Aderimos à [Adenda de Proteção de Dados dos Serviços Online](https://www.microsoftvolumelicensing.com/Downloader.aspx?DocumentId=17880)da Microsoft, que afirma que a Microsoft não utilizará dados do cliente nem obterá informações dos mesmos para qualquer publicidade ou fins comerciais semelhantes. Apenas utilizamos os Dados do Cliente conforme necessário para lhe fornecer os serviços do Azure, incluindo fins compatíveis com o fornecimento desses serviços. O utilizador retém todos os direitos sobre os Dados do Cliente.

### <a name="data-use"></a>Utilização de dados
A Microsoft usa padrões e inteligência de ameaças vistas em vários inquilinos para melhorar as nossas capacidades de prevenção e deteção; fazemos isso de acordo com os compromissos de privacidade descritos na nossa [Declaração de Privacidade.](https://privacy.microsoft.com/privacystatement)

## <a name="manage-data-collection-from-machines"></a>Gerir a recolha de dados a partir de máquinas
Quando ativar o Centro de Segurança do Azure, a recolha de dados é ativada para cada uma das suas subscrições do Azure. Também pode ativar a recolha de dados para as suas subscrições no Security Center. Quando a recolha de dados está ativada, o Security Center fornece o agente Log Analytics em todas as máquinas virtuais Azure suportadas existentes e quaisquer novas que sejam criadas.

O agente Log Analytics procura várias configurações e eventos relacionados com a segurança em [rastreios de eventos para](/windows/win32/etw/event-tracing-portal) rastreios do Windows (ETW). Além disso, o sistema operativo irá gerar eventos do registo de eventos durante a execução da máquina. Os exemplos destes dados incluem: tipo e versão do sistema operativo, registos de sistema operativo (registos de eventos do Windows), processos em execução, nome da máquina, endereços IP, utilizador com sessão iniciada e ID do inquilino. O agente Log Analytics lê entradas de registo de eventos e vestígios de ETW e copia-os para o seu(s) espaço de trabalho para análise. O agente Log Analytics também permite eventos de criação de processos e auditoria de linha de comando.

Se não estiver a utilizar o Azure Defender, também pode desativar a recolha de dados de máquinas virtuais na Política de Segurança. A Recolha de Dados é necessária para subscrições protegidas pelo Azure Defender. A recolha de instantâneos e artefactos de discos de VM continua ativada, mesmo que a recolha de dados tenha sido desativada.

Pode especificar o espaço de trabalho e a região onde os dados recolhidos das suas máquinas são armazenados. O padrão consiste em armazenar dados recolhidos das suas máquinas no espaço de trabalho mais próximo, como mostra o quadro seguinte:

| Área Geográfica da VM                                      | Área Geográfica da Área de Trabalho  |
|---------------------------------------------|----------------|
| Estados Unidos, Brasil, África do Sul         | Estados Unidos  |
| Canadá                                      | Canadá         |
| Europa (excluindo o Reino Unido)           | Europa         |
| Reino Unido                              | Reino Unido |
| Ásia (excluindo a Índia, Japão, Coreia, China) | Ásia-Pacífico   |
| Coreia                                       | Ásia-Pacífico   |
| Índia                                       | Índia          |
| Japão                                       | Japão          |
| China                                       | China          |
| Austrália                                   | Austrália      |
|                                             |                |

> [!NOTE]
> **O Azure Defender for Storage** armazena artefactos regionalmente de acordo com a localização do recurso Azure. Saiba mais em [Introdução ao Azure Defender para Armazenamento](defender-for-storage-introduction.md).


## <a name="data-consumption"></a>Consumo de dados

Os clientes podem aceder aos dados relacionados com o Security Center a partir dos seguintes fluxos de dados:


| Fluxo                                                                                | Tipos de dados                                                                                                                                                                                                          |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Log de atividades Azure](../azure-monitor/essentials/activity-log.md)                       | Todos os alertas de segurança, pedidos de acesso aprovados do Centro de Segurança [just-in-time,](security-center-just-in-time.md) e todos os alertas gerados por [controlos de aplicações adaptativos](security-center-adaptive-application.md).|
| [Registos do Azure Monitor](../azure-monitor/data-platform.md)                      | Todos os alertas de segurança.                                                                                                                                                                                                |
| [Azure Resource Graph](../governance/resource-graph/overview.md)                      | Alertas de segurança, recomendações de segurança, resultados de avaliação de vulnerabilidades, informações de pontuação seguras, estado das verificações de conformidade, e muito mais.                                                                       |
| [Azure Security Center REST API](/rest/api/securitycenter/) (API REST do Centro de Segurança do Azure) | Alertas de segurança, recomendações de segurança, e muito mais.                                                                                                                                                                |
|                                                                                       |                                                                                                                                                                                                                     |

## <a name="next-steps"></a>Passos seguintes

Através deste documento aprendeu como os dados são geridos e salvaguardados no Centro de Segurança do Azure. 

Para saber mais sobre o Azure Security Center, veja [o que é o Centro de Segurança Azure?](security-center-introduction.md)