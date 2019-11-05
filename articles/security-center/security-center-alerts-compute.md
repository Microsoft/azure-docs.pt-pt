---
title: Detecção de ameaças para computação nativa em nuvem na central de segurança do Azure | Microsoft Docs
description: Este artigo apresenta os alertas de computação nativos de nuvem disponíveis na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: f6fba7bc8e8b7d040805f0be62d436caebf638af
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73520912"
---
# <a name="threat-detection-for-cloud-native-computing-in-azure-security-center"></a>Detecção de ameaças para computação nativa em nuvem na central de segurança do Azure

Como uma solução nativa, a central de segurança do Azure tem visibilidade exclusiva dos logs internos para a identificação da metodologia de ataque em vários destinos. Este artigo apresenta os alertas disponíveis para os seguintes serviços do Azure:

* [Serviço de Aplicações do Azure](#app-services)
* [Contêineres do Azure](#azure-containers) 

> [!NOTE]
> Esses serviços não estão disponíveis no momento nas regiões do Azure governamental e do soberanas Cloud.

## Serviço de Azure App<a name="app-services"></a>

A central de segurança usa a escala da nuvem para identificar os ataques que visam os aplicativos em execução no serviço de aplicativo. Os invasores investigam aplicativos Web para encontrar e explorar pontos fracos. Antes de ser roteado para ambientes específicos, as solicitações para aplicativos em execução no Azure passam por vários gateways, onde são inspecionados e registrados. Esses dados são usados para identificar explorações e invasores e para aprender novos padrões que serão usados posteriormente.

Usando a visibilidade que o Azure tem como um provedor de nuvem, a central de segurança analisa os logs internos do serviço de aplicativo para identificar a metodologia de ataque em vários destinos. Por exemplo, a metodologia inclui verificação generalizada e ataques distribuídos. Esse tipo de ataque normalmente vem de um pequeno subconjunto de IPs e mostra padrões de rastreamento para pontos de extremidade semelhantes em vários hosts. Os ataques estão procurando uma página ou um plug-in vulnerável e não podem ser identificados no ponto de vista de um único host.

A central de segurança também tem acesso às áreas restritas e VMs subjacentes. Junto com a análise forense de memória, a infra-estrutura pode contar a história, desde um novo ataque que está circulando de forma a comprometimento nas máquinas dos clientes. Portanto, mesmo que a central de segurança seja implantada depois que um aplicativo Web for explorado, ele poderá detectar ataques contínuos.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Invocação suspeita de tema do WordPress detectada**|O log de atividades do serviço de aplicativo indica uma possível atividade de injeção de código no recurso do serviço de aplicativo.<br/> Essa atividade suspeita se assemelha à atividade que manipula um tema do WordPress para dar suporte à execução do código no lado do servidor, seguida de uma solicitação da Web direta para invocar o arquivo de tema manipulado. Esse tipo de atividade pode fazer parte de uma campanha de ataque sobre o WordPress.|
|**Conexão com a página da Web do endereço IP anômala detectado**|O log de atividades do serviço de aplicativo indica uma conexão com uma página da Web confidencial de um endereço de origem que nunca se conectou a ela antes. Isso pode indicar que alguém está tentando um ataque de força bruta em suas páginas de administração de aplicativo Web. Ele também pode ser o resultado de um usuário legítimo usando um novo endereço IP.|
|**Um IP que se conectou à interface de FTP do serviço de Azure App foi encontrado em inteligência contra ameaças**|A análise de logs de FTP do serviço de aplicativo detectou uma conexão de um endereço de origem que foi encontrado no feed de inteligência contra ameaças. Durante essa conexão, um usuário acessou as páginas listadas.|
|**Impressão digital da Web detectada**|O log de atividades do serviço de aplicativo indica uma possível atividade de impressão digital na Web no recurso do serviço de aplicativo. <br/>Essa atividade suspeita está associada a uma ferramenta chamada elefante cego. A ferramenta servidores Web de impressões digitais e tenta detectar os aplicativos instalados e suas versões. Os invasores geralmente usam essa ferramenta para investigar os aplicativos Web para encontrar vulnerabilidades.|
|**Acesso suspeito a uma página da Web possivelmente vulnerável detectada**|O log de atividades do serviço de aplicativo indica que uma página da Web que parece ser confidencial foi acessada. <br/>Essa atividade suspeita originada de um endereço de origem cujo padrão de acesso é semelhante ao de um scanner da Web. Esse tipo de atividade geralmente está associado a uma tentativa de um invasor verificar sua rede para tentar obter acesso a páginas da Web sensíveis ou vulneráveis.|
|**Arquivo PHP na pasta de carregamento**|O log de atividades do serviço de aplicativo indica que alguém acessou uma página do PHP suspeita localizada na pasta de carregamento. <br/>Esse tipo de pasta geralmente não contém arquivos PHP. A existência desse tipo de arquivo pode indicar uma exploração aproveitando vulnerabilidades de carregamento de arquivo arbitrários.|
|**Uma tentativa de executar comandos do Linux em um serviço de aplicativo do Windows**|A análise dos processos do serviço de aplicativo detectou uma tentativa de executar um comando do Linux em um serviço de aplicativo do Windows. Esta ação estava sendo executada pelo aplicativo Web. Esse comportamento geralmente é visto durante as campanhas que exploram uma vulnerabilidade em um aplicativo Web comum.|
|**Execução de PHP suspeita detectada**|Os logs de computador indicam que um processo PHP suspeito está em execução. A ação incluía uma tentativa de executar comandos do sistema operacional ou código PHP da linha de comando, usando o processo PHP. Embora esse comportamento possa ser legítimo, em aplicativos Web esse comportamento pode indicar atividades mal-intencionadas, como tentativas de infectar sites com shells da Web.|
|**Execução do processo da pasta temporária**|A análise de processos do serviço de aplicativo detectou uma execução de um processo da pasta temporária do aplicativo. Embora esse comportamento possa ser legítimo, em aplicativos Web esse comportamento pode indicar atividades mal-intencionadas.|
|**Tentativa de executar o comando de alto privilégio detectado**|A análise dos processos do serviço de aplicativo detectou uma tentativa de executar um comando que requer privilégios altos. O comando foi executado no contexto do aplicativo Web. Embora esse comportamento possa ser legítimo, em aplicativos Web esse comportamento pode indicar atividades mal-intencionadas.|

## Contêineres do Azure<a name="azure-containers"></a>

A central de segurança fornece detecção de ameaças em tempo real para seus ambientes em contêineres e gera alertas para atividades suspeitas. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores.

Detectamos ameaças em diferentes níveis: 

* **Nível de host** – agente da central de segurança (disponível na camada Standard, consulte [preços](security-center-pricing.md) para obter detalhes) monitora as atividades suspeitas no Linux. O agente dispara alertas para atividades suspeitas provenientes do nó ou de um contêiner em execução nele. Exemplos dessas atividades incluem a detecção e a conexão de Shell da Web com endereços IP suspeitos conhecidos. </br>
Para obter uma visão mais profunda da segurança do seu ambiente em contêiner, o agente monitora a análise específica do contêiner. Ele disparará alertas para eventos como criação de contêiner privilegiado, acesso suspeito a servidores de API e servidores Secure Shell (SSH) em execução dentro de um contêiner do Docker.

    >[!NOTE]
    > Se você optar por não instalar os agentes em seus hosts, receberá apenas um subconjunto dos benefícios e alertas de detecção de ameaças. Você ainda receberá alertas relacionados à análise de rede e comunicações com servidores mal-intencionados.

* Para o **nível de cluster AKs**, há monitoramento de detecção de ameaças com base na análise de logs de auditoria do kubernetes. Para habilitar esse monitoramento **sem agente** , adicione a opção kubernetes à sua assinatura na página de **configurações de & de preços** (consulte [preços](security-center-pricing.md)). Para gerar alertas nesse nível, a central de segurança monitora seus serviços gerenciados AKS usando os logs recuperados pelo AKS. Exemplos de eventos neste nível incluem painéis kubernetes expostos, criação de funções com alto privilégio e montagens confidenciais. 

    >[!NOTE]
    > A central de segurança gera alertas de detecção para ações e implantações do serviço kubernetes do Azure que ocorrem após a opção kubernetes ser habilitada nas configurações de assinatura. 

Além disso, nossa equipe global de pesquisadores de segurança monitora constantemente o panorama de ameaças. Eles adicionam alertas e vulnerabilidades específicos do contêiner à medida que são descobertos.