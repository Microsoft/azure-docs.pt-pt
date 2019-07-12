---
title: Deteção para a computação nativa da cloud no Centro de segurança do Azure de ameaças | Documentos da Microsoft
description: Este tópico apresenta a cloud alertas de computação nativa disponíveis no Centro de segurança do Azure. no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: 5aa5efcf-9f6f-4aa1-9f72-d651c6a7c9cd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: bc3cb66d43e71777e06c6bd63dcff35e2ff19df8
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571689"
---
# <a name="threat-detection-for-cloud-native-compute-in-azure-security-center"></a>Deteção de ameaças para a cloud computação nativa no Centro de segurança do Azure

Como fornecedor de cloud, o Centro de segurança aproveita a visibilidade exclusiva, que ele deve analisar os registos de internos e identificar a metodologia de ataque em vários destinos. Este tópico apresenta os alertas disponíveis para os serviços do Azure seguintes:

* [Serviço de Aplicações do Azure](#app-services)
* [Contentores](#azure-containers) 

## Serviço de aplicações do Azure <a name="app-services"></a>

Centro de segurança tira partido da escala da cloud para identificar ataques que visam as aplicações de clientes em execução ao longo do App Service do Azure. Com aplicativos da web que está sendo praticamente em qualquer rede moderna, os atacantes de sonda para localizar e explorar pontos fracos. Antes de ser encaminhado para ambientes específicos, os pedidos para aplicações em execução no Azure são enviados através de vários gateways em que são inspecionados e com sessão iniciadas. Estes dados, em seguida, são utilizados para identificar a explorações, os invasores e para aprender novos padrões que serão utilizados mais tarde.

Ao tirar partido da visibilidade que o Azure tem como um fornecedor de cloud, o Centro de segurança analisa registos internos do serviço de aplicações para identificar a metodologia de ataque em vários destinos. Por exemplo, análise ampla e ataques distribuídos. Normalmente, este tipo de ataque é proveniente de um pequeno subconjunto de IPs e exibe os padrões de rastreamento para pontos de extremidade semelhante em vários anfitriões, procurando por uma página vulnerável ou o plug-in. Isso pode ser detetado com a nuvem, mas não é possível identificar do ponto de vista de um único host.

Centro de segurança também tem acesso a áreas de segurança e VMs subjacentes. Em conjunto com forense de memória, a infraestrutura pode informar a história, de um novo ataque circulando no terreno para comprometimentos nas máquinas de cliente. Por conseguinte, o Centro de segurança pode detetar ataques contra aplicativos da web muito depois de ser exploradas.

> [!div class="mx-tableFixed"]

|Alerta|Descrição|
|---|---|
|**Invocação de tema WordPress suspeita detetada**|O registo de atividade do App Service do Azure indica uma atividade de injeção de código possível no seu recurso de serviço de aplicações.<br/> Esta atividade suspeita é semelhante a atividade que manipula um tema de WordPress para suportar a execução de lado do servidor do código, seguido de uma solicitação da web direto para invocar o arquivo de tema manipulado. Este tipo de atividade tem sido visto no passado como parte de uma campanha de ataque ao longo do WordPress.|
|**Ligação para a página da web de endereço IP anómala detetada**|O registo de atividade do App Service do Azure indica uma ligação para uma página da web confidenciais de um endereço de origem que nunca conectados a ele antes. Isto pode indicar que alguém está a tentar um ataque de força bruta em suas páginas de administração de aplicação Web. Também pode ser o resultado de um novo endereço IP a ser utilizado por um usuário legítimo.|
|**Não foi encontrado um IP que ligado à sua Interface de FTP do serviço de aplicações do Azure no informações sobre ameaças**|Análise de registos de FTP do serviço de aplicações do Azure detetou uma ligação de um endereço de origem foi encontrado no feed de inteligência de ameaças. Durante esta ligação, um utilizador aceder as páginas listadas abaixo.|
|**Impressão digital de Web detetado**|O registo de atividade do App Service do Azure indica uma impressão digital de atividade no seu recurso de serviço de aplicações de web possível. <br/>Esta atividade suspeita detetada está associada a uma ferramenta chamada elefante cego. A ferramenta de impressões digitais servidores web e tenta detetar as aplicações instaladas e suas versões. Os atacantes utilizam frequentemente esta ferramenta para as aplicações web de pesquisa para encontrar vulnerabilidades.|
|**Acesso suspeito a página da web vulnerável, possivelmente, detetado**|O registo de atividade do App Service do Azure indica que foi acedida a uma página da web que parece ser confidenciais. <br/>Esta atividade suspeita originada de um endereço de origem, cujo padrão de acesso é semelhante ao de um scanner de web. Esse tipo de atividade, muitas vezes, está associado uma tentativa por um atacante para analisar a sua rede para tentar obter acesso a páginas da web vulnerável ou confidenciais.|
|**Ficheiro PHP na pasta de carregamento**|O registo de atividade do App Service do Azure indica que algo acedeu a uma página PHP suspeita localizada na pasta de carregamento. <br/>Este tipo de pasta não contém, normalmente, os ficheiros PHP. A existência desse tipo de arquivo pode indicar uma exploração tirar partido das vulnerabilidades de carregamento de ficheiros arbitrários.|
|**Uma tentativa de executar os comandos de Linux num serviço de aplicações do Windows**|Análise de processos do serviço de aplicações detetou uma tentativa de executar um comando do Linux num serviço de aplicações do Windows. Esta ação estava a ser executado pelo aplicativo web. Este comportamento é normalmente visto durante as campanhas que explorar uma vulnerabilidade num aplicativo da web comuns.|
|**Execução de PHP suspeita detetada**|Registos da máquina indicam uma que um suspeito processo PHP está em execução. A ação incluída uma tentativa de executar comandos do sistema operacional ou o código PHP a partir da linha de comando usando o processo PHP. Embora esse comportamento possa ser legítimo, em aplicativos web esse comportamento é também observado em atividades maliciosas, como as tentativas de infectar o Web sites com shells de web.|
|**Execução de processo da pasta temporária**|Análise de processos do serviço de aplicações detetou uma execução de um processo da pasta temporária da aplicação. Embora esse comportamento possa ser legítimo, em aplicativos web, que esse comportamento também é observado em atividades maliciosas.|
|**Tentativa de execução do comando de privilégio alto detetado**|Análise de processos do serviço de aplicações detetou uma tentativa de executar um comando que exige privilégios elevados. O comando foi executado no contexto do aplicativo web. Embora esse comportamento possa ser legítimo, em aplicativos web, que esse comportamento também é observado em atividades maliciosas.|

> [!NOTE]
> Deteção de ameaças do Centro de segurança para o serviço de aplicações não está atualmente disponível nas regiões de cloud soberana e do Azure government.

Para obter mais informações sobre a deteção de ameaças do serviço de aplicações alertas visite proteger o serviço de aplicações com o Centro de segurança do Azure e veja como ativar a monitorização e a proteção das suas cargas de trabalho do serviço de aplicações.

## Contentores <a name="azure-containers"></a>

Centro de segurança fornece deteção de ameaças em tempo real para os seus contentores em máquinas do Linux com base na estrutura auditd. Os alertas identificam várias atividades suspeitas do Docker como, por exemplo, a criação de um contentor com privilégios num anfitrião, uma indicação de servidor de Secure Shell (SSH) em execução dentro de um contentor de Docker ou o uso dos extratores de criptografia. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores. Além de deteções do Linux, o Centro de segurança também oferece análises que são mais específicas para implementações de contentores.
