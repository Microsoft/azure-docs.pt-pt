---
title: Deteção de VMs e servidores no Centro de segurança do Azure de ameaças | Documentos da Microsoft
description: Este tópico apresenta as VM e o servidor de alertas disponíveis no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: dd2eb069-4c76-4154-96bb-6e6ae553ef46
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 5487b4f49f5dbf7b968cd45d40555c69b54c329a
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571585"
---
# <a name="threat-detection-for-vms--servers-in-azure-security-center"></a>Deteção de ameaças para VMs e servidores no Centro de segurança do Azure

Este tópico apresenta os diferentes tipos de métodos de deteção e alertas disponíveis para VMs e servidores com os seguintes sistemas de operação. Para obter uma lista de versões suportadas, consulte [plataformas e funcionalidades suportadas pelo centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-os-coverage).

* [Windows](#windows-machines)
* [Linux](#linux-machines)

## Windows <a name="windows-machines"></a>

Centro de segurança integra-se com serviços do Azure para monitorizar e proteger as suas máquinas com base em Windows.  Centro de segurança apresenta os alertas e as sugestões de remediação de todos estes serviços num formato de fácil de usar.

### Microsoft Server Defender ATP <a nanme="windows-atp"></a>

Centro de segurança do Azure expande suas plataformas de proteção de carga de trabalho na Cloud com a integração com o Windows Defender avançadas de proteção contra ameaças (ATP). Isso fornece funcionalidades completas de ponto final de deteção e resposta (EDR).

> [!NOTE]
> Sensor de servidor do Windows Defender ATP é ativada automaticamente nos servidores do Windows que são carregadas para o Centro de segurança do Azure.

Quando o servidor do Windows Defender ATP Deteta uma ameaça, aciona um alerta. O alerta é apresentado no dashboard do Centro de segurança. No dashboard, pode dinamizar para a consola do Windows Defender ATP para efetuar uma investigação detalhada para descobrir o escopo da invasão. Para obter mais informações sobre o servidor do Windows Defender ATP, consulte [carregar servidores para o serviço do Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints).

### Análise de despejo de falha de sistema <a nanme="windows-dump"></a>

Quando o software falha, uma informação de falha de sistema captura uma parte da memória no momento da falha.

Uma falha pode ter sido causada por software maligno ou conter malware. Para evitar a ser detectadas por produtos de segurança, várias formas de malware utilizam um ataque fileless, o que evita escrever no disco ou encriptar componentes de software escritos no disco. Este tipo de ataque é difícil de detetar com as abordagens tradicionais baseados em disco.

No entanto, esse tipo de ataque pode ser detetado através de análise de memória. Ao analisar a memória na informação de falha, o Centro de segurança pode detetar as técnicas de que ataque está a utilizar para explorar vulnerabilidades no software, aceder a dados confidenciais e persistir clandestinamente numa máquina afetada. Isso é feito com o Centro de segurança de back-end com impacto mínimo no desempenho aos anfitriões.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Injeção de código detetada**|A injeção de código é a inserção de módulos executáveis em processos ou threads em execução. Essa técnica é usada por software maligno para aceder a dados, enquanto se com êxito para impedir que está a ser encontrado e removido. <br/>Este alerta indica que está presente nas informações de falha de sistema um módulo injetado. Para diferenciar entre módulos maliciosos e não maliciosos injetados, o Centro de segurança verifica se o módulo injetado está em conformidade com um perfil de comportamento suspeito.|
|**Segmento de código suspeito detetado**|Indica que foi alocado um segmento de código através de métodos não padrão, como injeção reflexiva e hollowing do processo. O alerta fornece características adicionais do segmento que foram processadas para fornecer contexto para os recursos e comportamentos do segmento reportado.|
|**Shellcode detetado**|Shellcode é o payload executado depois de o software maligno explorar uma vulnerabilidade de software.<br/>Este alerta indica que a análise de despejo de falha de sistema detetou código executável que apresenta um comportamento normalmente tido por payloads maliciosos. Embora o software não malicioso também pode fazer esse comportamento, não é característico das práticas de desenvolvimento de normal software.|

### Deteção de ataques fileless <a nanme="windows-fileless"></a>

No Azure, podemos ver regularmente fileless ataques que visam pontos finais de nossos clientes.

Para evitar a detecção, ataques fileless injetar payloads maliciosos na memória. Cargas de atacante persistem dentro da memória de processos comprometidos e realizar uma ampla gama de atividades maliciosas.

Com a detecção de ataque fileless, técnicas de computação forense automatizada memória identificam ataque fileless Kits de ferramentas, técnicas e comportamentos. Periodicamente, esta solução analisa a sua máquina em tempo de execução e extrai informações diretamente da memória de processos críticos de segurança.

Ele localiza evidências de exploração, a injeção de código e a execução de payloads maliciosos. Detecção de ataque fileless gera alertas de segurança detalhadas para acelerar a triagem de alerta, correlação e tempo de resposta de downstream. Essa abordagem complementa soluções EDR baseado em evento fornecendo maior cobertura de deteção.

> [!NOTE]
> Pode simular alertas do Windows por transferência [Playbook de centro de segurança do Azure](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef): Alertas de segurança e siga as diretrizes fornecidas

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Técnica de ataque fileless detetada**|A memória do processo especificado abaixo contém um Kit de ferramentas de ataque fileless: Meterpreter. Kits de ferramentas de ataque fileless normalmente não têm uma presença no sistema de arquivos, dificultando a deteção pelo antivírus tradicionais.|

### <a name="further-reading"></a>Leitura adicional

Para obter exemplos e obter mais informações sobre a deteção do Centro de segurança:

* [Como o Centro de segurança do Azure automatiza a deteção de ataques informáticos](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)
* [Como o Centro de segurança do Azure Deteta vulnerabilidades usando ferramentas administrativas](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

## Linux <a name="linux-machines"></a>

Recolhe o Centro de segurança de auditoria registos a partir de máquinas Linux utilizar **auditd**, um de Linux mais comuns de estruturas de auditoria. auditd tem a vantagem de ter existe há muito tempo e está a viver no kernel do principal. 

### Alertas de auditd de Linux e integração do Microsoft Monitoring Agent (MMA) <a name="linux-auditd"></a>

O sistema de auditd consiste num subsistema de nível de kernel, que é responsável pela monitorização de chamadas de sistema, filtrá-los por um conjunto de regra fornecido e escrevendo mensagens para os mesmos para um soquete. Centro de segurança integra-se funcionalidades do pacote auditd dentro do Microsoft Monitoring Agent (MMA). Esta integração permite a recolha de eventos de auditd em todas as distribuições suportadas de Linux sem quaisquer pré-requisitos.  

dos registos de auditoria são recolhidos, enriquecidos e agregados em eventos com o agente MMA do Linux. Centro de segurança está trabalhando constantemente sobre a adição de nova análise, que tiram partido Linux sinaliza para detetar comportamentos maliciosos em cloud e máquinas do Linux no local. Semelhante às capacidades do Windows, estes dados analíticos abrangem várias processos suspeitos, tentativas de início de sessão duvidosa, carregamento de módulo de kernel e outras atividades que indicar uma máquina está sob ataque ou foi violada.  

Seguem-se vários exemplos de análise, que demonstram como iremos abranger várias diferentes fases do ciclo de vida do ataque.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Processo visto o acesso ao ficheiro de chaves autorizado de SSH, de forma invulgar**|Um arquivo de chaves autorizadas SSH tem sido acedido num método semelhante ao campanhas de software maligno conhecido. Este acesso pode indicar que um atacante está a tentar obter acesso persistente a uma máquina|
|**Tentativa de persistência detetado**|Análise de dados do anfitrião detetou que foi instalado um script de inicialização para o modo de utilizador único. <br/>Uma vez que é raro que qualquer processo legítimo seria necessários para executar nesse modo, isto pode indicar que um atacante tenha adicionado um processo malicioso para todos os níveis de execução para garantir a persistência.|
|**Manipulação de tarefas agendadas detetado**|Análise de dados do anfitrião detetou possível manipulação de tarefas agendadas. Os atacantes adicionar, muitas vezes, as tarefas agendadas para as máquinas que já comprometido para obter a persistência.|
|**Modificação do ficheiro suspeito timestamp**|Análise de dados do anfitrião detetou uma modificação de timestamp suspeita. Os atacantes copiar, muitas vezes, carimbos de ficheiros legítimos existentes para novas ferramentas para evitar a detecção desses arquivos ignorados recentemente|
|**Foi adicionado um novo utilizador ao grupo sudoers**|Análise de dados de anfitriões detetado que um utilizador foi adicionado ao grupo sudoers, o que permite que os membros executar comandos com privilégios elevados.|
|**Exploração provável de DynoRoot vulnerabilidade no cliente de dhcp**|Análise de dados do anfitrião detetou a execução de um comando invulgar com o processo principal do dhclient script.|
|**Módulo de kernel suspeito detetado**|Análise de dados de anfitrião detetou um ficheiro de objeto partilhado que está a ser carregado como um módulo de kernel. Isso poderia ser legítima atividade ou uma indicação de que uma das suas máquinas foi comprometida.|
|**Processo associado a extração de moeda digital detetada**|Análise de dados do anfitrião detetou a execução de um processo normalmente associada a extração de moeda digital|
|**Potenciais encaminhamento de porta para o endereço IP externo**|Análise de dados de anfitriões detetado o início da porta para um endereço IP externo de reencaminhamento.|

> [!NOTE]
> Pode simular alertas do Windows baixando [Playbook de centro de segurança do Azure: Alertas de segurança](https://gallery.technet.microsoft.com/Azure-Security-Center-0ac8a5ef) e siga as diretrizes fornecidas.


Para obter mais informações, veja estes artigos:  

* [Centro de segurança do Azure de tirar partido para detetar quando o ataque de máquinas do Linux comprometido](https://azure.microsoft.com/blog/leverage-azure-security-center-to-detect-when-compromised-linux-machines-attack/)

* [Centro de segurança do Azure pode detectar vulnerabilidades emergentes em Linux](https://azure.microsoft.com/blog/azure-security-center-can-detect-emerging-vulnerabilities-in-linux/)

 