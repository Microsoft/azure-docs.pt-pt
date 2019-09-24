---
title: Protegendo os serviços de aplicativos na central de segurança do Azure | Microsoft Docs
description: Este artigo ajuda você a começar a proteger seus serviços de aplicativos na central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e8518710-fcf9-44a8-ae4b-8200dfcded1a
ms.service: security-center
ms.topic: conceptual
ms.date: 01/27/2019
ms.author: memildin
ms.openlocfilehash: 68f7c47f0a0f56085d632f1c1741318f440b41ee
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/23/2019
ms.locfileid: "71202477"
---
# <a name="protect-app-service-with-azure-security-center"></a>Proteger o serviço de aplicativo com a central de segurança do Azure
Este artigo ajuda você a usar a central de segurança do Azure para monitorar e proteger seus aplicativos em execução na parte superior do serviço de aplicativo.

O serviço de aplicativo permite que você crie e hospede aplicativos Web na linguagem de programação de sua escolha sem gerenciar a infraestrutura. O serviço de aplicativo oferece dimensionamento automático e alta disponibilidade, oferece suporte ao Windows e ao Linux, bem como implantações automatizadas do GitHub, Azure DevOps ou qualquer repositório git. 

Vulnerabilidades em aplicativos Web são frequentemente exploradas por invasores, pois têm uma interface comum e dinâmica para quase todas as organizações na Internet. Solicitações para aplicativos em execução na parte superior do serviço de aplicativo passam por vários gateways implantados em data centers do Azure em todo o mundo, responsável por rotear cada solicitação para seu aplicativo correspondente. 

A central de segurança do Azure pode executar avaliações e recomendações em seus aplicativos em execução no serviço de aplicativo nas áreas restritas em suas VMs ou instâncias sob demanda. Aproveitando a visibilidade que o Azure tem como provedor de nuvem, a central de segurança analisa os logs internos do serviço de aplicativo para monitorar ataques comuns de aplicativos Web que geralmente são executados em vários destinos.

A central de segurança aproveita a escala da nuvem para identificar ataques em seus aplicativos do serviço de aplicativo e se concentrar em ataques emergentes, enquanto os invasores estão na fase de reconhecimento, examinando para identificar vulnerabilidades em vários sites, hospedados no Azure. A central de segurança usa modelos de análise e aprendizado de máquina para abranger todas as interfaces que permitem que os clientes interajam com seus aplicativos, seja por HTTP ou por meio de métodos de gerenciamento. Além disso, como um serviço de terceiros no Azure, a central de segurança também está em uma posição única para oferecer análise de segurança baseada em host que abrange os nós de computação subjacentes para essa PaaS, permitindo que a central de segurança detecte ataques contra aplicativos Web que foram Já explorado.

## <a name="prerequisites"></a>Pré-requisitos

Para monitorizar e proteger o Serviço de Aplicações, precisa de um plano do Serviço de Aplicações associado a computadores dedicados. Estes planos são: Básico, Standard, Premium, Isolado ou Linux. O Centro de Segurança do Azure não suporta os planos Gratuito, Partilhado ou Consumo. Para obter mais informações, consulte [planos do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/plans/).

## <a name="security-center-protection"></a>Proteção da central de segurança

A central de segurança do Azure protege a instância de VM na qual seu serviço de aplicativo está sendo executado e a interface de gerenciamento. Ele também monitora solicitações e respostas enviadas de e para seus aplicativos em execução no serviço de aplicativo.

A central de segurança é integrada nativamente com o serviço de aplicativo, eliminando a necessidade de implantação e integração-ela é completamente transparente.



## <a name="enabling-monitoring-and-protection-of-app-service"></a>Habilitando o monitoramento e a proteção do serviço de aplicativo

1. No Azure, escolha central de segurança.
2. Vá para **preços & configurações** e escolha uma assinatura.
3. Em **tipo de preço**, na linha **serviço de aplicativo** , alterne o plano para **habilitado**.

![alternância do serviço de aplicativo](./media/security-center-app-services/app-services-toggle.png)

>[!NOTE]
> O número de instâncias listadas para a quantidade de recursos representa o número de instâncias relevantes do serviço de aplicativo ativo no momento em que você abriu a folha tipo de preço. Como esse número pode mudar com base nas opções de dimensionamento selecionadas, o número de instâncias para as quais você é cobrado será modificado de acordo.

Para desabilitar o monitoramento e as recomendações para o serviço de aplicativo, repita esse processo e alterne o plano do **serviço de aplicativo** para **desabilitado**.



## <a name="see-also"></a>Consulte também
Neste artigo, aprendeu a utilizar as capacidades de monitorização no Centro de Segurança do Azure. Para saber mais acerca do Centro de Segurança do Azure, consulte o seguinte:

* [Configurando políticas de segurança na central de segurança do Azure](tutorial-security-policy.md): Saiba como definir as configurações de segurança na central de segurança do Azure.
* [Gerenciando e respondendo a alertas de segurança na central de segurança do Azure](security-center-managing-and-responding-alerts.md): Saiba como gerir e responder a alertas de segurança.
* [Serviços de aplicativos](security-center-virtual-machine-protection.md#app-services):  Exiba uma lista de seus ambientes de serviço de aplicativo com resumos de integridade.
* [Monitorando soluções de parceiros com a central de segurança do Azure](security-center-partner-solutions.md): Saiba como monitorizar o estado de funcionamento das soluções dos seus parceiros.
* [Perguntas frequentes da central de segurança do Azure](security-center-faq.md): Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/): Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
