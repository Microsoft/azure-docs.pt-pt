---
title: Segurança de Dados do Centro de Segurança do Azure | Microsoft Docs
description: Este documento explica como os dados são geridos e salvaguardados no Centro de Segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 33f2c9f4-21aa-4f0c-9e5e-4cd1223e39d7
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: rkarlin
ms.openlocfilehash: 7a327c963f32d6bf5b0cf7751557c61327fd26e9
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57902795"
---
# <a name="azure-security-center-data-security"></a>Segurança de Dados do Centro de Segurança do Azure
Para ajudar os clientes a evitar, detetar e responder a ameaças, o Centro de Segurança do Azure recolhe e processa dados relacionados com segurança, incluindo informações de configuração, metadados, registos de eventos, ficheiros de informação de falha de sistema e mais. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço.

Este artigo explica como os dados são geridos e salvaguardados no Centro de Segurança do Azure.

## <a name="data-sources"></a>Origens de dados
O Centro de Segurança do Azure analisa dados das origens seguintes para proporcionar visibilidade para o estado da segurança, identificar vulnerabilidades e recomendar mitigações e detetar ameaças ativas:

- Serviços do Azure: Utiliza informações sobre a configuração dos serviços do Azure que implementou através da comunicação com o fornecedor de recursos desses serviços.
- Tráfego de rede: Amostragem a utiliza metadados de tráfego de rede da infraestrutura da Microsoft, tais como porta/IP de origem/destino, tamanho do pacote e protocolo de rede.
- Soluções de parceiros: Utiliza alertas de segurança de soluções de parceiros integradas, tais como firewalls e soluções antimalware.
- As máquinas virtuais e servidores: Utiliza informações de configuração e informações sobre eventos de segurança, tais como o Windows eventos e registos de auditoria, registos do IIS, mensagens do syslog e ficheiros de informação de falha das suas máquinas virtuais. Além disso, quando é criado um alerta, o Centro de Segurança do Azure pode gerar um instantâneo do disco da VM afetada e extrair artefactos da máquina relacionados com o alerta desse disco, como o ficheiro de registo, para fins forenses.


## <a name="data-protection"></a>Proteção de dados
**Segregação de dados**: Dados são mantidos separados de forma lógica em cada componente em todo o serviço. Todos os dados são etiquetados por organização. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço.

**Acesso a dados**: Para fornecer recomendações de segurança e investigar potenciais ameaças de segurança, as equipas da Microsoft podem aceder a informações recolhidas ou analisadas pelos serviços do Azure, incluindo ficheiros de informação de falha de sistema, eventos de criação de processos, instantâneos de disco da VM e artefactos, que pode incluir, involuntariamente, dados do cliente ou dados pessoais das suas máquinas virtuais. Respeitamos os [Termos e a Declaração de Privacidade do Microsoft Online Services](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), os quais estipulam que a Microsoft não utilizará Dados do Cliente ou derivará informações dos mesmos para qualquer fim publicitário ou comercial semelhante. Apenas utilizamos os Dados do Cliente conforme necessário para lhe fornecer os serviços do Azure, incluindo fins compatíveis com o fornecimento desses serviços. O utilizador retém todos os direitos sobre os Dados do Cliente.

**A utilização de dados**: A Microsoft utiliza os padrões e informações sobre ameaças presentes em vários inquilinos para melhorar as nossas capacidades de prevenção e deteção; podemos fazê-lo de acordo com os compromissos de privacidade descritos na nossa [declaração de privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Localização dos dados

**Suas áreas de trabalho**: Uma área de trabalho está especificada para as seguintes áreas geográficas e os dados recolhidos das suas máquinas virtuais do Azure, incluindo informações de falhas e alguns tipos de dados de alertas, são armazenados na área de trabalho mais próxima.

| Área Geográfica da VM                        | Área Geográfica da Área de Trabalho |
|-------------------------------|---------------|
| Estados Unidos, Brasil, Canadá | Estados Unidos |
| Europa, Reino Unido        | Europa        |
| Ásia-Pacífico, Japão, Índia    | Ásia-Pacífico  |
| Austrália                     | Austrália     |


Instantâneos de disco da VM são armazenados na mesma conta de armazenamento como disco de VM.

Para máquinas virtuais e servidores em execução noutros ambientes, por exemplo, no local, pode especificar a área de trabalho e a região onde são armazenados os dados recolhidos.

**Armazenamento do Centro de segurança do Azure**: Informações sobre alertas de segurança, incluindo alertas de parceiros, são armazenadas regionalmente de acordo com a localização do recurso do Azure relacionado, ao passo que as informações sobre o estado de funcionamento de segurança e as recomendações são armazenadas centralmente nos Estados Unidos ou Europa, de acordo com local do cliente.
O Centro de Segurança do Azure recolhe cópias efémeras dos ficheiros de informação de falha de sistema e analisa-as para encontrar sinais de tentativas de exploração e casos em que a segurança tenha sido comprometida. O Centro de Segurança do Azure faz esta análise na mesma Geografia que a área de trabalho e elimina as cópias efémeras quando a análise estiver concluída.

Os artefactos das máquinas são armazenados centralmente na mesma região que as VMs.


## <a name="managing-data-collection-from-virtual-machines"></a>Gestão da recolha de dados provenientes de máquinas virtuais

Quando ativar o Centro de Segurança do Azure, a recolha de dados é ativada para cada uma das suas subscrições do Azure. Também pode ativar a recolha de dados para as suas subscrições na secção Política de Segurança do Centro de Segurança do Azure. Quando a Recolha de Dados é ativada, o Centro de Segurança do Azure aprovisiona o Agente de Monitorização da Microsoft em todas as máquinas virtuais do Azure existentes suportadas e quaisquer máquinas novas criadas.
O agente de Monitorização da Microsoft analisa várias configurações e eventos relacionados com a segurança em rastreios de [Rastreio de Eventos para o Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Além disso, o sistema operativo irá gerar eventos do registo de eventos durante a execução da máquina. Os exemplos destes dados incluem: tipo e versão do sistema operativo, registos de sistema operativo (registos de eventos do Windows), processos em execução, nome da máquina, endereços IP, utilizador com sessão iniciada e ID do inquilino. O Agente de Monitorização da Microsoft lê as entradas de registo de eventos e rastreios ETW e copia-as para as suas áreas de trabalho para análise. O Microsoft Monitoring Agent também copia os ficheiros de informação de falha para as suas áreas de trabalho e permite eventos de criação de processos e auditoria da linha de comandos.

Se estiver a utilizar o Centro de Segurança do Azure gratuito, também pode desativar a recolha de dados das máquinas virtuais na Política de Segurança. A Recolha de Dados é necessária para as subscrições no escalão Standard. A recolha de instantâneos e artefactos de discos de VM continua ativada, mesmo que a recolha de dados tenha sido desativada.

## <a name="data-consumption"></a>Consumo de dados

Os clientes podem consumir dados relacionados com o Centro de Segurança a partir de fluxos de dados diferentes, conforme mostrado abaixo:

* **Atividade do Azure**: todos os alertas de segurança, pedidos [atempados](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) aprovados pelo Centro de Segurança e todos os alertas gerados por [controlos de aplicação adaptável](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application).
* **Registos de Monitor do Azure**: todos os alertas de segurança.


> [!NOTE]
> As recomendações de segurança também podem ser consumidas pela API REST. Leia [Security Resource Provider REST API Reference (Referência da API REST do Fornecedor de Recursos de Segurança)](https://msdn.microsoft.com/library/mt704034(Azure.100).aspx) para obter mais informações.

## <a name="see-also"></a>Consulte também
Através deste documento aprendeu como os dados são geridos e salvaguardados no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, veja:

* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md) – Saiba como planear e compreender as considerações de conceção para adoção do Centro de Segurança do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de Segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança
* [Monitorizar soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure
