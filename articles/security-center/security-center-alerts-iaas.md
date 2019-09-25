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
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: 50d4023b09eb14fcfafe752ca60b8e888acc1fef
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273892"
---
# <a name="threat-detection-for-vms-and-servers-in-azure-security-center"></a>Detecção de ameaças para VMs e servidores na central de segurança do Azure

Este tópico apresenta os diferentes tipos de métodos de detecção e alertas disponíveis para VMs e servidores com os seguintes sistemas operacionais. Para obter uma lista de versões com suporte, consulte [plataformas e recursos com suporte na central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

A central de segurança do Azure integra-se aos serviços do Azure para monitorar e proteger suas máquinas baseadas no Windows. A central de segurança apresenta os alertas e as sugestões de correção de todos esses serviços em um formato fácil de usar.

### Microsoft defender ATP<a nanme="windows-atp"></a>

A central de segurança estende suas plataformas de proteção de carga de trabalho de nuvem integrando-se à ATP (proteção avançada contra ameaças) do Microsoft defender. Isso fornece recursos de EDR (detecção e resposta de ponto de extremidade) abrangentes.

> [!NOTE]
> O sensor do Microsoft defender ATP é habilitado automaticamente em servidores Windows que usam a central de segurança.

Quando o Microsoft defender ATP detecta uma ameaça, ele dispara um alerta. O alerta é mostrado no painel da central de segurança. No painel, você pode dinamizar para o console do Microsoft defender ATP e executar uma investigação detalhada para descobrir o escopo do ataque. Para obter mais informações sobre o Microsoft defender ATP, consulte [servidores integrados ao serviço do Microsoft defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Análise de despejo de memória<a nanme="windows-dump"></a>

Quando o software falha, uma informação de falha de sistema captura uma parte da memória no momento da falha.

Uma falha pode ter sido causada por malware ou conter malware. Para evitar ser detectado por produtos de segurança, várias formas de malware usam um ataque de arquivo, o que evita a gravação em disco ou a criptografia de componentes de software gravados no disco. Esse tipo de ataque é difícil de detectar usando abordagens tradicionais baseadas em disco.

No entanto, usando a análise de memória, você pode detectar esse tipo de ataque. Analisando a memória no despejo de falha, a central de segurança pode detectar as técnicas que o ataque está usando. Por exemplo, o ataque pode estar tentando explorar vulnerabilidades no software, acessar dados confidenciais e maneira furtiva persistir em um computador comprometido. A central de segurança faz isso funcionar com impacto mínimo sobre o desempenho dos hosts.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Injeção de código descoberta**|A injeção de código é a inserção de módulos executáveis em processos ou threads em execução. Essa técnica é usada por malware para acessar dados, enquanto se oculta com êxito para evitar que sejam encontradas e removidas. <br/>Este alerta indica que está presente nas informações de falha de sistema um módulo injetado. Para diferenciar entre módulos injetados mal-intencionados e não mal-intencionados, a central de segurança verifica se o módulo injetado está em conformidade com um perfil de comportamento suspeito.|
|**Segmento de código suspeito descoberto**|Indica que um segmento de código foi alocado usando métodos não padrão, como injeção reflexiva e cancelamento de processo. O alerta fornece características adicionais do segmento de código que foram processados para fornecer contexto para os recursos e comportamentos do segmento de código relatado.|
|**Shellcode descoberto**|Shellcode é o payload executado depois de o software maligno explorar uma vulnerabilidade de software.<br/>Esse alerta indica que a análise de despejo de memória detectou código executável que exibe o comportamento normalmente executado por cargas mal-intencionadas. Embora o software não mal-intencionado também possa executar esse comportamento, não é típico das práticas normais de desenvolvimento de software.|

### Detecção de ataque de não-arquivo<a nanme="windows-fileless"></a>

Ataques de não-arquivo que visam seus pontos de extremidade são comuns. Para evitar a detecção, os ataques de arquivo insuficiente injetam cargas mal-intencionadas na memória. As cargas de invasor persistem na memória de processos comprometidos e executam uma ampla gama de atividades mal-intencionadas.

Com a detecção de ataque sem arquivo, as técnicas forenses de memória automatizada identificam os kits de informática, técnicas e comportamentos de ataque sem arquivo. Essa solução verifica periodicamente seu computador em tempo de execução e extrai informações diretamente da memória de processos críticos de segurança.

Ele encontra evidências de exploração, injeção de código e execução de cargas mal-intencionadas. A detecção de ataques de não-arquivo gera alertas de segurança detalhados para acelerar a triagem de alerta, correlação e tempo de resposta downstream. Essa abordagem complementa as soluções de EDR baseadas em eventos, fornecendo maior cobertura de detecção.

> [!NOTE]
> Você pode simular alertas do Windows [baixando o guia estratégico da central de segurança do Azure: Alertas](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)de segurança.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Técnica de ataque de não-arquivo detectada**|A memória do processo especificado contém um kit de ferramentas de ataque de não-arquivo: Meterpreter. Os kits de programas de ataque sem arquivo normalmente não têm uma presença no sistema de arquivos, dificultando a detecção por software antivírus tradicional.|

### <a name="further-reading"></a>Leitura adicional

Para obter exemplos e mais informações sobre a detecção da central de segurança, consulte:

* [Como a central de segurança do Azure automatiza a detecção de ataques cibernéticos](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Como a central de segurança do Azure detecta vulnerabilidades usando ferramentas administrativas](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

A central de segurança coleta registros de auditoria de computadores Linux usando **auditoria**, uma das estruturas de auditoria do Linux mais comuns. a auditoria reside no kernel principal. 

### MMA (alertas de auditoria do Linux e integração de Microsoft Monitoring Agent)<a name="linux-auditd"></a>

O sistema auditado consiste em um subsistema de nível de kernel, que é responsável por monitorar chamadas do sistema. Ele os filtra por um conjunto de regras especificado e grava mensagens para eles em um soquete. A central de segurança integra as funcionalidades do pacote auditado dentro do Microsoft Monitoring Agent (MMA). Essa integração habilita a coleta de eventos auditados em todas as distribuições Linux com suporte, sem nenhum pré-requisito.  

os registros auditados são coletados, aprimorados e agregados em eventos usando o agente MMA do Linux. A central de segurança adiciona continuamente uma nova análise que usa sinais do Linux para detectar comportamentos mal-intencionados em máquinas Linux locais e na nuvem. Semelhante aos recursos do Windows, essas análises se estendem por processos suspeitos, tentativas de entrada duvidosa, carregamento de módulo kernel e outras atividades. Essas atividades podem indicar que um computador está sob ataque ou foi violado.  

Veja a seguir vários exemplos de análises que abrangem diferentes estágios do ciclo de vida do ataque.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Processo visto acessando o arquivo de chaves autorizadas SSH de maneira incomum**|Um arquivo de chaves autorizadas SSH foi acessado em um método semelhante a campanhas de malware conhecidas. Esse acesso pode indicar que um invasor está tentando obter acesso persistente a um computador.|
|**Tentativa de persistência detectada**|A análise de dados do host detectou que um script de inicialização para o modo de usuário único foi instalado. <br/>Como é raro que qualquer processo legítimo seja necessário para ser executado nesse modo, isso pode indicar que um invasor adicionou um processo mal-intencionado a cada nível de execução para garantir a persistência.|
|**Manipulação de tarefas agendadas detectadas**|A análise de dados do host detectou uma possível manipulação de tarefas agendadas. Os invasores geralmente adicionam tarefas agendadas a computadores que estão comprometidos a obter persistência.|
|**Modificação de carimbo de hora de arquivo suspeito**|A análise de dados do host detectou uma modificação suspeita de carimbo de hora Os invasores geralmente copiam carimbos de data/hora de arquivos legítimos existentes para novas ferramentas para evitar a detecção desses arquivos recentemente descartados.|
|**Um novo usuário foi adicionado ao grupo sudoers**|A análise de dados do host detectou que um usuário foi adicionado ao grupo sudoers, que permite que seus membros executem comandos com altos privilégios.|
|**Provável exploração da vulnerabilidade do DynoRoot no cliente DHCP**|A análise de dados do host detectou a execução de um comando incomum, com um processo pai de script dhclient.|
|**Módulo de kernel suspeito detectado**|A análise de dados do host detectou um arquivo de objeto compartilhado sendo carregado como um módulo de kernel. Isso pode ser uma atividade legítima ou uma indicação de que uma de suas máquinas foi comprometida.|
|**Processo associado à mineração de moeda digital detectado**|A análise de dados do host detectou a execução de um processo que normalmente está associado à mineração de moeda digital.|
|**Potencial encaminhamento de porta para endereço IP externo**|A análise de dados do host detectou o início do encaminhamento de porta para um endereço IP externo.|

> [!NOTE]
> Você pode simular alertas do Windows [baixando o guia estratégico da central de segurança do Azure: Detecções do Linux](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef).


Para obter mais informações, consulte:  

* [Aproveite a central de segurança do Azure para detectar quando os ataques de computadores Linux comprometidos](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [A central de segurança do Azure pode detectar vulnerabilidades emergentes no Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 