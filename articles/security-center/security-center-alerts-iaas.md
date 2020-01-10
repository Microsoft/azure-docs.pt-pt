---
title: Detecção de ameaças para VMs e servidores na central de segurança do Azure | Microsoft Docs
description: Este tópico apresenta os alertas de VM e servidor disponíveis na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.topic: conceptual
ms.date: 01/05/2020
ms.author: memildin
ms.openlocfilehash: a5ed91cef6e49fcb71c35f2262479be45a018651
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754304"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Detecção de ameaças para VMs e servidores na central de segurança do Azure

Este tópico apresenta os diferentes tipos de métodos de detecção e alertas disponíveis para VMs e servidores com os seguintes sistemas operacionais. Para obter uma lista de versões com suporte, consulte [plataformas e recursos com suporte na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

A central de segurança do Azure integra-se aos serviços do Azure para monitorar e proteger suas máquinas baseadas no Windows. A central de segurança apresenta os alertas e as sugestões de correção de todos esses serviços em um formato fácil de usar.

* **O Microsoft defender ATP** <a name="windows-atp"></a> -central de segurança estende suas plataformas de proteção de carga de trabalho de nuvem integrando-se com o Microsoft defender ATP (proteção avançada contra ameaças). Isso fornece recursos de EDR (detecção e resposta de ponto de extremidade) abrangentes.

    > [!NOTE]
    > O sensor do Microsoft defender ATP é habilitado automaticamente em servidores Windows que usam a central de segurança.

    Quando o Microsoft defender ATP detecta uma ameaça, ele dispara um alerta. O alerta é mostrado no painel da central de segurança. No painel, você pode dinamizar para o console do Microsoft defender ATP e executar uma investigação detalhada para descobrir o escopo do ataque. Para obter mais informações sobre o Microsoft defender ATP, consulte [servidores integrados ao serviço do Microsoft defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

* **Análise** <a name="windows-dump"></a> de despejo de memória – quando o software falha, um despejo de falha captura uma parte da mémoria no momento da falha.

    Uma falha pode ter sido causada por malware ou conter malware. Para evitar ser detectado por produtos de segurança, várias formas de malware usam um ataque de arquivo, o que evita a gravação em disco ou a criptografia de componentes de software gravados no disco. Esse tipo de ataque é difícil de detectar usando abordagens tradicionais baseadas em disco.

    No entanto, usando a análise de memória, você pode detectar esse tipo de ataque. Analisando a memória no despejo de falha, a central de segurança pode detectar as técnicas que o ataque está usando. Por exemplo, o ataque pode estar tentando explorar vulnerabilidades no software, acessar dados confidenciais e maneira furtiva persistir em um computador comprometido. A central de segurança faz isso funcionar com impacto mínimo sobre o desempenho dos hosts.

    Para obter detalhes dos alertas de análise de despejo de memória, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-windows).

* **Detecção** <a name="windows-fileless"></a> de ataque de não-arquivo-ataques de não-arquivo que visam seus pontos de extremidade são comuns. Para evitar a detecção, os ataques de arquivo insuficiente injetam cargas mal-intencionadas na memória. As cargas de invasor persistem na memória de processos comprometidos e executam uma ampla gama de atividades mal-intencionadas.

    Com a detecção de ataque sem arquivo, as técnicas forenses de memória automatizada identificam os kits de informática, técnicas e comportamentos de ataque sem arquivo. Essa solução verifica periodicamente seu computador em tempo de execução e extrai informações diretamente da memória de processos críticos de segurança.

    Ele encontra evidências de exploração, injeção de código e execução de cargas mal-intencionadas. A detecção de ataques de não-arquivo gera alertas de segurança detalhados para acelerar a triagem de alerta, correlação e tempo de resposta downstream. Essa abordagem complementa as soluções de EDR baseadas em eventos, fornecendo maior cobertura de detecção.

    Para obter detalhes dos alertas de detecção de ataque sem arquivo, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-windows).

> [!NOTE]
> Você pode simular alertas do Windows baixando o [guia estratégico da central de segurança do Azure: alertas de segurança](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046).

## Linux <a name="linux-machines"></a>

A central de segurança coleta registros de auditoria de computadores Linux usando **auditoria**, uma das estruturas de auditoria do Linux mais comuns. a auditoria reside no kernel principal. 

* **MMA (alertas de auditoria do Linux e integração** <a name="linux-auditd"></a> de Microsoft Monitoring Agent) – o sistema auditado consiste em um subsistema de nível de kernel, que é responsável por monitorar chamadas do sistema. Ele os filtra por um conjunto de regras especificado e grava mensagens para eles em um soquete. A central de segurança integra as funcionalidades do pacote auditado dentro do Microsoft Monitoring Agent (MMA). Essa integração habilita a coleta de eventos auditados em todas as distribuições Linux com suporte, sem nenhum pré-requisito.  

    os registros auditados são coletados, aprimorados e agregados em eventos usando o agente MMA do Linux. A central de segurança adiciona continuamente uma nova análise que usa sinais do Linux para detectar comportamentos mal-intencionados em máquinas Linux locais e na nuvem. Semelhante aos recursos do Windows, essas análises se estendem por processos suspeitos, tentativas de entrada duvidosa, carregamento de módulo kernel e outras atividades. Essas atividades podem indicar que um computador está sob ataque ou foi violado.  

    Veja a seguir vários exemplos de análises que abrangem diferentes estágios do ciclo de vida do ataque.

    Para obter uma lista dos alertas do Linux, consulte a [tabela de referência de alertas](alerts-reference.md#alerts-linux).

> [!NOTE]
> Você pode simular os alertas do Linux baixando o [guia estratégico da central de segurança do Azure: detecções do Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).

 
 ## <a name="next-steps"></a>Passos seguintes

Para obter exemplos e mais informações sobre a detecção da central de segurança, consulte:

* [Como a central de segurança do Azure automatiza a detecção de ataques cibernéticos](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Como a central de segurança do Azure detecta vulnerabilidades usando ferramentas administrativas](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)
* [Aproveite a central de segurança do Azure para detectar quando os ataques de computadores Linux comprometidos](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [A central de segurança do Azure pode detectar vulnerabilidades emergentes no Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)