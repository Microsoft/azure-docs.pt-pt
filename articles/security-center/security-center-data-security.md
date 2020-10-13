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
ms.date: 05/21/2020
ms.author: memildin
ms.openlocfilehash: 8a999e2b5706c04b426f758ba46f49fb9f7e3057
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91438778"
---
# <a name="azure-security-center-data-security"></a>Segurança de Dados do Centro de Segurança do Azure
Para ajudar os clientes a prevenir, detetar e responder a ameaças, o Azure Security Center recolhe e processa dados relacionados com a segurança, incluindo informações de configuração, metadados, registos de eventos e muito mais. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço.

Este artigo explica como os dados são geridos e salvaguardados no Centro de Segurança do Azure.

## <a name="data-sources"></a>Origens de dados
O Centro de Segurança do Azure analisa dados das origens seguintes para proporcionar visibilidade para o estado da segurança, identificar vulnerabilidades e recomendar mitigações e detetar ameaças ativas:

- Serviços do Azure: utiliza informações sobre a configuração dos serviços do Azure que implementou através da comunicação com o fornecedor de recursos desses serviços.
- Tráfego de Rede: utiliza metadados de tráfego de rede de amostragem da infraestrutura da Microsoft, tais como porta/IP de origem/destino, tamanho do pacote e protocolo de rede.
- Soluções de Parceiros: utiliza alertas de segurança das soluções de parceiros integradas, tais como firewalls e soluções antimalware.
- As suas Máquinas Virtuais e Servidores: Utiliza informações de configuração e informações sobre eventos de segurança, tais como registos de eventos e auditorias do Windows, registos IIS e mensagens sislog das suas máquinas virtuais. Além disso, quando é criado um alerta, o Centro de Segurança do Azure pode gerar um instantâneo do disco da VM afetada e extrair artefactos da máquina relacionados com o alerta desse disco, como o ficheiro de registo, para fins forenses.


## <a name="data-protection"></a>Proteção de dados
**Segregação de dados**: os dados são mantidos separados de forma lógica em cada componente em todo o serviço. Todos os dados são etiquetados por organização. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço.

Acesso aos **dados**: Para fornecer recomendações de segurança e investigar potenciais ameaças à segurança, o pessoal da Microsoft pode aceder a informações recolhidas ou analisadas pelos serviços Azure, incluindo eventos de criação de processos, instantâneos de discos VM e artefactos, que podem incluir involuntariamente dados do Cliente ou dados pessoais das suas máquinas virtuais. Respeitamos os [Termos de Serviço Online da Microsoft e a Declaração de Privacidade](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), os quais estipulam que a Microsoft não utilizará Dados do Cliente ou derivará informações dos mesmos para qualquer fim de publicidade ou comercial semelhante. Apenas utilizamos os Dados do Cliente conforme necessário para lhe fornecer os serviços do Azure, incluindo fins compatíveis com o fornecimento desses serviços. O utilizador retém todos os direitos sobre os Dados do Cliente.

**Utilização de dados**: a Microsoft utiliza os padrões e as informações sobre ameaças presentes em vários inquilinos para melhorar as nossas capacidades de prevenção e deteção. Fazemo-lo em conformidade com os compromissos de privacidade descritos na nossa [Declaração de Privacidade](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).

## <a name="data-location"></a>Localização dos dados

**Seu(s) Workspace(s)**: Um espaço de trabalho é especificado para os seguintes Geos, e os dados recolhidos das suas máquinas virtuais Azure, incluindo alguns tipos de dados de alerta, são armazenados no espaço de trabalho mais próximo.

| Área Geográfica da VM                              | Área Geográfica da Área de Trabalho |
|-------------------------------------|---------------|
| Estados Unidos, Brasil, África do Sul | Estados Unidos |
| Canadá                              | Canadá        |
| Europa (excluindo o Reino Unido)   | Europa        |
| Reino Unido                      | Reino Unido |
| Ásia (excluindo a Índia, Japão, Coreia, China)   | Ásia-Pacífico  |
| Coreia                              | Ásia-Pacífico  |
| Índia                               | Índia         |
| Japão                               | Japão         |
| China                               | China         |
| Austrália                           | Austrália     |


Instantâneos de disco da VM são armazenados na mesma conta de armazenamento como disco de VM.

Para máquinas virtuais e servidores em execução noutros ambientes, por exemplo, no local, pode especificar a área de trabalho e a região onde são armazenados os dados recolhidos.

**Armazenamento do Centro de Segurança do Azure**: as informações sobre alertas de segurança, incluindo alertas de parceiros, são armazenadas regionalmente de acordo com a localização do recurso do Azure relacionado, enquanto que as informações sobre o estado de funcionamento de segurança e recomendações são armazenadas centralmente nos Estados Unidos ou na Europa, em conformidade com a localização do cliente. Os artefactos das máquinas são armazenados centralmente na mesma região que as VMs.

## <a name="manage-data-collection-from-virtual-machines"></a>Gerir a recolha de dados a partir de máquinas virtuais

Quando ativar o Centro de Segurança do Azure, a recolha de dados é ativada para cada uma das suas subscrições do Azure. Também pode ativar a recolha de dados para as suas subscrições na secção Política de Segurança do Centro de Segurança do Azure. Quando a recolha de dados é ligada, o Azure Security Center fornece o agente Log Analytics em todas as máquinas virtuais Azure suportadas existentes e quaisquer novas que sejam criadas.
O agente Log Analytics procura várias configurações e eventos relacionados com a segurança em [rastreios de eventos para](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) rastreios do Windows (ETW). Além disso, o sistema operativo irá gerar eventos do registo de eventos durante a execução da máquina. Os exemplos destes dados incluem: tipo e versão do sistema operativo, registos de sistema operativo (registos de eventos do Windows), processos em execução, nome da máquina, endereços IP, utilizador com sessão iniciada e ID do inquilino. O agente Log Analytics lê entradas de registo de eventos e vestígios de ETW e copia-os para o seu(s) espaço de trabalho para análise. O agente Log Analytics também permite eventos de criação de processos e auditoria de linha de comando.

Se não estiver a utilizar o Azure Defender, também pode desativar a recolha de dados de máquinas virtuais na Política de Segurança. A Recolha de Dados é necessária para subscrições protegidas pelo Azure Defender. A recolha de instantâneos e artefactos de discos de VM continua ativada, mesmo que a recolha de dados tenha sido desativada.

## <a name="data-consumption"></a>Consumo de dados

Os clientes podem consumir dados relacionados com o Centro de Segurança a partir de fluxos de dados diferentes, conforme mostrado abaixo:

* **Atividade Azul**: todos os alertas de segurança, pedidos aprovados do Centro de Segurança [just-in-time,](security-center-just-in-time.md) e todos os alertas gerados por [controlos de aplicação adaptativos](security-center-adaptive-application.md).
* **Registos do Monitor Azure:** todos os alertas de segurança.


> [!NOTE]
> As recomendações de segurança também podem ser consumidas pela API REST. Leia [Security Resource Provider REST API Reference (Referência da API REST do Fornecedor de Recursos de Segurança)](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx) para obter mais informações.

## <a name="see-also"></a>Consulte também
Através deste documento aprendeu como os dados são geridos e salvaguardados no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, veja:

* [Guia de Planeamento e Operações do Centro de Segurança Azure](security-center-planning-and-operations-guide.md) — Aprenda a planear e a compreender as considerações de design para adotar o Centro de Segurança Azure.
* [Monitorização de saúde de segurança no Azure Security Center](security-center-monitoring.md) — Saiba como monitorizar a saúde dos seus recursos Azure
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança
* [Monitorizar soluções de parceiros com o Azure Security Center](security-center-partner-solutions.md) — Saiba como monitorizar o estado de saúde das soluções do seu parceiro.
* [Blogue de Segurança do Azure](https://docs.microsoft.com/archive/blogs/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure
