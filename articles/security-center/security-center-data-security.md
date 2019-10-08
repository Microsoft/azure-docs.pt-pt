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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: memildin
ms.openlocfilehash: 3c63283a1a13e2deb178a53dfc5045d371d6a28a
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996928"
---
# <a name="azure-security-center-data-security"></a>Segurança de Dados do Centro de Segurança do Azure
Para ajudar os clientes a evitar, detetar e responder a ameaças, o Centro de Segurança do Azure recolhe e processa dados relacionados com segurança, incluindo informações de configuração, metadados, registos de eventos, ficheiros de informação de falha de sistema e mais. A Microsoft respeita diretrizes rigorosas de conformidade e segurança, desde a codificação à operação de um serviço.

Este artigo explica como os dados são geridos e salvaguardados no Centro de Segurança do Azure.

## <a name="data-sources"></a>Origens de dados
O Centro de Segurança do Azure analisa dados das origens seguintes para proporcionar visibilidade para o estado da segurança, identificar vulnerabilidades e recomendar mitigações e detetar ameaças ativas:

- Serviços do Azure: Usa informações sobre a configuração dos serviços do Azure que você implantou comunicando-se com o provedor de recursos do serviço.
- Tráfego de rede: Usa metadados de exemplo de tráfego de rede da infraestrutura da Microsoft, como IP/porta de origem/destino, tamanho do pacote e protocolo de rede.
- Soluções de parceiros: O usa alertas de segurança de soluções de parceiros integradas, como firewalls e soluções antimalware.
- Suas máquinas virtuais e servidores: O usa informações de configuração e informações sobre eventos de segurança, como logs de eventos e auditoria do Windows, logs do IIS, mensagens de syslog e arquivos de despejo de memória de suas máquinas virtuais. Além disso, quando é criado um alerta, o Centro de Segurança do Azure pode gerar um instantâneo do disco da VM afetada e extrair artefactos da máquina relacionados com o alerta desse disco, como o ficheiro de registo, para fins forenses.


## <a name="data-protection"></a>Proteção de dados
**Segregação de dados**: Os dados são mantidos separados logicamente em cada componente em todo o serviço. Todos os dados são etiquetados por organização. Este tipo de etiquetagem persiste por todo o ciclo de vida dos dados e é imposto em cada camada do serviço.

**Acesso a dados**: Para fornecer recomendações de segurança e investigar possíveis ameaças de segurança, o pessoal da Microsoft pode acessar as informações coletadas ou analisadas pelos serviços do Azure, incluindo arquivos de despejo de memória, eventos de criação de processos, instantâneos de disco de VM e artefatos, que pode incluir involuntariamente dados do cliente ou dados pessoais de suas máquinas virtuais. Respeitamos os [Termos e a Declaração de Privacidade do Microsoft Online Services](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31), os quais estipulam que a Microsoft não utilizará Dados do Cliente ou derivará informações dos mesmos para qualquer fim publicitário ou comercial semelhante. Apenas utilizamos os Dados do Cliente conforme necessário para lhe fornecer os serviços do Azure, incluindo fins compatíveis com o fornecimento desses serviços. O utilizador retém todos os direitos sobre os Dados do Cliente.

**Uso de dados**: A Microsoft usa padrões e inteligência contra ameaças vistos em vários locatários para aprimorar nossos recursos de prevenção e detecção; fazemos isso de acordo com os compromissos de privacidade descritos em nossa [política de privacidade](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx).

## <a name="data-location"></a>Localização de dados

**Seus espaços de trabalho**: Um espaço de trabalho é especificado para os seguintes áreas geográficas, e os dados coletados de suas máquinas virtuais do Azure, incluindo despejos de memória, e alguns tipos de dados de alerta, são armazenados no espaço de trabalho mais próximo.

| Área Geográfica da VM                              | Área Geográfica da Área de Trabalho |
|-------------------------------------|---------------|
| Estados Unidos, Brasil, África do Sul | Estados Unidos |
| Canadá                              | Canadá        |
| Europa (excluindo o Reino Unido)   | Europa        |
| Reino Unido                      | Reino Unido |
| Ásia (exceto Índia, Japão, Coreia, China)   | Ásia-Pacífico  |
| Coreia                              | Ásia-Pacífico  |
| Índia                               | Índia         |
| Japão                               | Japão         |
| China                               | China         |
| Austrália                           | Austrália     |


Instantâneos de disco da VM são armazenados na mesma conta de armazenamento como disco de VM.

Para máquinas virtuais e servidores em execução noutros ambientes, por exemplo, no local, pode especificar a área de trabalho e a região onde são armazenados os dados recolhidos.

**Armazenamento da central de segurança do Azure**: As informações sobre alertas de segurança, incluindo alertas de parceiros, são armazenadas de forma regional, de acordo com o local do recurso relacionado do Azure, enquanto as informações sobre o status de integridade da segurança e a recomendação são armazenadas centralmente no Estados Unidos ou Europa de acordo com o local do cliente.
O Centro de Segurança do Azure recolhe cópias efémeras dos ficheiros de informação de falha de sistema e analisa-as para encontrar sinais de tentativas de exploração e casos em que a segurança tenha sido comprometida. O Centro de Segurança do Azure faz esta análise na mesma Geografia que a área de trabalho e elimina as cópias efémeras quando a análise estiver concluída.

Os artefactos das máquinas são armazenados centralmente na mesma região que as VMs.


## <a name="managing-data-collection-from-virtual-machines"></a>Gestão da recolha de dados provenientes de máquinas virtuais

Quando ativar o Centro de Segurança do Azure, a recolha de dados é ativada para cada uma das suas subscrições do Azure. Também pode ativar a recolha de dados para as suas subscrições na secção Política de Segurança do Centro de Segurança do Azure. Quando a Recolha de Dados é ativada, o Centro de Segurança do Azure aprovisiona o Agente de Monitorização da Microsoft em todas as máquinas virtuais do Azure existentes suportadas e quaisquer máquinas novas criadas.
O agente de Monitorização da Microsoft analisa várias configurações e eventos relacionados com a segurança em rastreios de [Rastreio de Eventos para o Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). Além disso, o sistema operativo irá gerar eventos do registo de eventos durante a execução da máquina. Os exemplos destes dados incluem: tipo e versão do sistema operativo, registos de sistema operativo (registos de eventos do Windows), processos em execução, nome da máquina, endereços IP, utilizador com sessão iniciada e ID do inquilino. O Agente de Monitorização da Microsoft lê as entradas de registo de eventos e rastreios ETW e copia-as para as suas áreas de trabalho para análise. O Microsoft Monitoring Agent também copia os ficheiros de informação de falha para as suas áreas de trabalho e permite eventos de criação de processos e auditoria da linha de comandos.

Se estiver a utilizar o Centro de Segurança do Azure gratuito, também pode desativar a recolha de dados das máquinas virtuais na Política de Segurança. A Recolha de Dados é necessária para as subscrições no escalão Standard. A recolha de instantâneos e artefactos de discos de VM continua ativada, mesmo que a recolha de dados tenha sido desativada.

## <a name="data-consumption"></a>Consumo de dados

Os clientes podem consumir dados relacionados com o Centro de Segurança a partir de fluxos de dados diferentes, conforme mostrado abaixo:

* **Atividade do Azure**: todos os alertas de segurança, solicitações [just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time) aprovadas da central de segurança e todos os alertas gerados por [controles de aplicativo adaptáveis](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application).
* **Logs de Azure monitor**: todos os alertas de segurança.


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
