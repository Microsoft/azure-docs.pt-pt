---
title: Azure Defender para servidores - os benefícios e funcionalidades
description: Conheça os benefícios e funcionalidades do Azure Defender para servidores.
author: memildin
ms.author: memildin
ms.date: 9/23/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: b7457e258cf8a9936e9a63ab9eec9edc0b54d3eb
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98797730"
---
# <a name="introduction-to-azure-defender-for-servers"></a>Introdução ao Azure Defender para servidores

O Azure Defender para servidores adiciona deteção de ameaças e defesas avançadas para as suas máquinas Windows e Linux.

Para o Windows, o Azure Defender integra-se com os serviços Azure para monitorizar e proteger as suas máquinas baseadas no Windows. O Centro de Segurança apresenta as sugestões de alerta e remediação de todos estes serviços num formato fácil de usar.

Para o Linux, o Azure Defender recolhe registos de auditoria de máquinas Linux utilizando **auditados,** um dos quadros de auditoria linux mais comuns. vidas auditadas no núcleo principal. 


## <a name="what-are-the-benefits-of-azure-defender-for-servers"></a>Quais são os benefícios do Azure Defender para servidores?

As capacidades de deteção e proteção de ameaças fornecidas com o Azure Defender para servidores incluem:

- **Licença integrada para o Microsoft Defender para Endpoint (apenas Windows)** - O Azure Defender para servidores inclui  [o Microsoft Defender para Endpoint](https://www.microsoft.com/microsoft-365/security/endpoint-defender). Em conjunto, fornecem capacidades abrangentes de deteção e resposta de pontos finais (EDR). [Saiba mais](security-center-wdatp.md).

    Quando o Defender for Endpoint deteta uma ameaça, desencadeia um alerta. O alerta é mostrado no Centro de Segurança. A partir do Security Center, também podes orientar-te para a consola Defender for Endpoint, e realizar uma investigação detalhada para descobrir o alcance do ataque. Saiba mais sobre o Microsoft Defender para Endpoint.

    > [!IMPORTANT]
    > O Microsoft Defender para o sensor **Endpoint** está automaticamente ativado nos servidores do Windows que utilizam o Security Center.

- **Pesquisa de avaliação de vulnerabilidade para VMs** - O scanner de vulnerabilidade incluído no Azure Security Center é alimentado pela Qualys. 

    O scanner qualys é uma das principais ferramentas para a identificação em tempo real de vulnerabilidades nas suas Máquinas Virtuais Azure. Você não precisa de uma licença Qualys ou mesmo uma conta Qualys - tudo é tratado perfeitamente dentro do Centro de Segurança. [Saiba mais](deploy-vulnerability-assessment-vm.md).

- **Acesso de máquina virtual (VM) just-in-time (VM)** - Os atores da ameaça caçam ativamente máquinas acessíveis com portas de gestão abertas, como RDP ou SSH. Todas as suas máquinas virtuais são alvos potenciais para um ataque. Quando um VM é comprometido com sucesso, é usado como ponto de entrada para atacar mais recursos dentro do seu ambiente.

    Quando ativa o Azure Defender para servidores, pode utilizar o acesso VM just-in-time para bloquear o tráfego de entrada nos seus VMs, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário. [Saiba mais](just-in-time-explained.md).

- **Monitorização da integridade dos ficheiros (FIM)** - Monitorização da integridade dos ficheiros (FIM), também conhecida como monitorização de alterações, examina ficheiros e registos de sistema operativo, software de aplicação e outros para alterações que possam indicar um ataque. Um método de comparação é usado para determinar se o estado atual do ficheiro é diferente da última digitalização do ficheiro. Pode aproveitar esta comparação para determinar se foram feitas modificações válidas ou suspeitas nos seus ficheiros.

    Quando ativa o Azure Defender para servidores, pode utilizar o FIM para validar a integridade dos ficheiros Windows, dos seus registos Windows e dos ficheiros Linux. [Saiba mais](security-center-file-integrity-monitoring.md).

- **Controlos de aplicação adaptativos (AAC)** - Os controlos de aplicação adaptativa são uma solução inteligente e automatizada para definir listas de aplicações conhecidas e seguras para as suas máquinas.

    Quando tiver ativado e configurado controlos de aplicação adaptativa, receberá alertas de segurança se qualquer aplicação correr além das que definiu como seguras. [Saiba mais](security-center-adaptive-application.md).

- **Endurecimento adaptativo da rede (ANH)** - Aplicação de grupos de segurança de rede (NSG) para filtrar o tráfego de e para os recursos, melhora a sua postura de segurança de rede. No entanto, ainda pode haver alguns casos em que o tráfego real que flui através do NSG é um subconjunto das regras do NSG definidos. Nestes casos, melhorar ainda mais a postura de segurança pode ser alcançado endurecendo as regras do NSG, com base nos padrões reais de tráfego.

    O Endurecimento da Rede Adaptativa fornece recomendações para endurecer ainda mais as regras do NSG. Usa um algoritmo de aprendizagem automática que fatores no tráfego real, configuração confiável conhecida, inteligência de ameaça e outros indicadores de compromisso, e depois fornece recomendações para permitir o tráfego apenas a partir de tuples IP/port específico. [Saiba mais](security-center-adaptive-network-hardening.md).

- **Docker host hardening** - Azure Security Center identifica contentores não geridos hospedados em IaaS Linux VMs, ou outras máquinas Linux que executam contentores Docker. O Centro de Segurança avalia continuamente as configurações destes contentores. Em seguida, compara-os com o Center for Internet Security (CIS) Docker Benchmark. O Centro de Segurança inclui todo o conjunto de regras do CIS Docker Benchmark e alerta-o se os seus contentores não satisfizerem nenhum dos controlos. [Saiba mais](harden-docker-hosts.md).

- **Deteção de ataques sem ficheiros (apenas Windows)** - Ataques sem ficheiros injetam cargas maliciosas na memória para evitar a deteção por técnicas de digitalização baseadas em discos. A carga útil do intruso persiste então na memória de processos comprometidos e executa uma ampla gama de atividades maliciosas.

  Com a deteção de ataques sem ficheiros, as técnicas forenses de memória automatizada identificam kits de ferramentas de ataque sem ficheiros, técnicas e comportamentos. Esta solução analisa periodicamente a sua máquina em tempo de execução e extrai insights diretamente da memória dos processos. Informações específicas incluem a identificação de: 

  - Kits de ferramentas bem conhecidos e software de mineração de cripto 

  - Shellcode, que é um pequeno pedaço de código normalmente usado como carga útil na exploração de uma vulnerabilidade de software.

  - Injetado malicioso executável na memória do processo

  A deteção de ataques sem ficheiros gera alertas de segurança detalhados que contenham as descrições com metadados de processo adicionais, como a atividade da rede. Isto acelera a triagem de alerta, a correlação e o tempo de resposta a jusante. Esta abordagem complementa as soluções EDR baseadas em eventos e proporciona uma maior cobertura de deteção.

  Para obter mais detalhes sobre os alertas de deteção de ataques sem ficheiro, consulte a [tabela de alertas de referência](alerts-reference.md#alerts-windows).

- **Linux auditado alertas e integração de agente log analytics (apenas Linux)** - O sistema auditado é composto por um subsistema de nível kernel, responsável pela monitorização das chamadas do sistema. Filtra-os por um conjunto de regras especificado e escreve mensagens para eles numa tomada. O Security Center integra funcionalidades a partir do pacote auditado dentro do agente Log Analytics. Esta integração permite a recolha de eventos auditados em todas as distribuições apoiadas do Linux, sem quaisquer requisitos.

    os registos auditados são recolhidos, enriquecidos e agregados em eventos utilizando o agente Log Analytics para o agente Linux. O Security Center adiciona continuamente novas análises que usam sinais Linux para detetar comportamentos maliciosos em máquinas Linux em nuvem e no local. Semelhantes às capacidades do Windows, estas análises estendem-se por processos suspeitos, tentativas duvidosas de iniciar súm, carregamento de módulos de kernel e outras atividades. Estas atividades podem indicar que uma máquina está a ser atacada ou foi violada.  

    Para obter uma lista dos alertas linux, consulte a [tabela de alertas de referência.](alerts-reference.md#alerts-linux)


## <a name="simulating-alerts"></a>Simulação de alertas

Pode simular alertas descarregando um dos seguintes livros de reprodução:

- Para Windows: [Azure Security Center Playbook: Alertas de segurança](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Security%20Alerts%20Playbook_v2.pdf)

- Para Linux: [Azure Security Center Playbook: Linux Detections](https://github.com/Azure/Azure-Security-Center/blob/master/Simulations/Azure%20Security%20Center%20Linux%20Detections_v2.pdf).




## <a name="next-steps"></a>Próximos passos

Neste artigo, aprendeu sobre o Azure Defender para servidores. 

Para obter material relacionado, consulte os seguintes artigos: 

- Se um alerta é gerado pelo Security Center, ou recebido pelo Security Center a partir de um produto de segurança diferente, você pode exportá-lo. Para exportar os seus alertas para a Azure Sentinel, qualquer SIEM de terceiros, ou qualquer outra ferramenta externa, siga as instruções em [alertas de exportação para um SIEM](continuous-export.md).

- > [!div class="nextstepaction"]
    > [Ativar o Azure Defender](security-center-pricing.md#enable-azure-defender)