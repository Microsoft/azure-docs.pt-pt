---
title: Proteja seus computadores e aplicativos
description: Este documento aborda recomendações no Centro de segurança que o ajudam a proteger as suas máquinas virtuais e computadores e suas aplicações web e ambientes de serviço de aplicações.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 47fa1f76-683d-4230-b4ed-d123fef9a3e8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: memildin
ms.openlocfilehash: b7e5b0286cdd15834b84e4fd3e619c6555054823
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553006"
---
# <a name="protect-your-machines-and-applications"></a>Proteja seus computadores e aplicativos
Quando a central de segurança do Azure identifica possíveis vulnerabilidades de segurança, ele cria recomendações que orientam você pelo processo de configuração dos controles necessários para proteger e proteger seus recursos.

Este artigo explica a página **computação e aplicativos** da seção segurança de recursos da central de segurança.

Para obter uma lista completa das recomendações que podem ser exibidas nesta página, consulte [recomendações de armazenamento e dados](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Exibir a segurança de seus recursos de computação e aplicativos

[painel da central de segurança ![](./media/security-center-virtual-machine-recommendations/overview.png)](./media/security-center-virtual-machine-recommendations/overview.png#lightbox)

Para exibir o status dos seus recursos de computação e aplicativos, selecione **computação & aplicativos** em **recursos** na barra lateral da central de segurança. As seguintes guias estão disponíveis:

* **Visão geral**: lista as recomendações para todos os recursos de computação e aplicativos, bem como seu status de segurança atual 

* [**VMs e computadores**](#vms-and-computers): lista as recomendações para suas VMs, computadores e o estado de segurança atual de cada

* [**Conjuntos de dimensionamento de VM**](#vmscale-sets): lista as recomendações para seus conjuntos de dimensionamento, 

* [**Serviços de nuvem**](#cloud-services): lista as recomendações para suas funções Web e de trabalho monitoradas pela central de segurança

* [**Serviços de aplicativos**](#app-services): lista as recomendações para seus ambientes de serviço de aplicativo e o estado de segurança atual de cada um

* **Contêineres**: lista as recomendações para seus contêineres e avaliação de segurança de suas configurações

* **Recursos de computação**: lista as recomendações para seus recursos de computação, como clusters de Service Fabric e hubs de eventos

### <a name="whats-in-each-tab"></a>O que há em cada guia?

Cada guia tem várias seções e, em cada seção, você pode fazer uma busca detalhada para ver detalhes adicionais sobre o item mostrado.

Em cada guia, você também verá as recomendações para os recursos relevantes em seu ambiente monitorado. A primeira coluna lista a recomendação, a segunda mostra o número total de recursos afetados e a terceira mostra a gravidade do problema.

Cada recomendação tem um conjunto de ações que pode realizar depois de a selecionar. Por exemplo, se você selecionar **atualizações do sistema ausentes**, o número de VMs e computadores com patches ausentes e a severidade da atualização ausente será exibida.

> [!NOTE]
> As recomendações de segurança são as mesmas da página **recomendações** , mas aqui são filtradas para o tipo de recurso específico que você selecionou. Para obter mais informações sobre como resolver recomendações, consulte [implementando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md).
>

### <a name="vms-and-computers"></a>VMs e computadores
A seção VMs e computadores fornece uma visão geral de todas as recomendações de segurança para suas VMs e computadores. Quatro tipos de computadores estão incluídos:

![Computador não pertencente ao Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computador não Azure.

![VM do Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) VM do Azure Resource Manager.

![VM clássica do Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) VM clássica do Azure.

![VMs identificadas a partir da área de trabalho](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VMs que só são identificadas a partir da área de trabalho que faz parte da subscrição vista. Isso inclui VMs de outras assinaturas que são relatadas para o espaço de trabalho nessa assinatura e VMs que foram instaladas com o Operations Manager agente direto e não têm nenhuma ID de recurso.

O ícone apresentado em cada recomendação ajuda-o a identificar rapidamente a VM e o computador que precisa de atenção e o tipo de recomendação. Você também pode usar os filtros para pesquisar a lista por **tipo de recurso** e por **severidade**.

Para fazer uma busca detalhada nas recomendações de segurança para cada VM, clique na VM.
Aqui verá os detalhes de segurança para o computador ou VM. Na parte inferior, você pode ver a ação recomendada e a gravidade de cada problema.

[serviços de nuvem ![](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)

### <a name="cloud-services"></a>Serviços de nuvem
Para serviços de nuvem, uma recomendação é criada quando a versão do sistema operacional está desatualizada.

![Serviços Cloud](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Em um cenário em que você tem uma recomendação, siga as etapas na recomendação para atualizar o sistema operacional. Quando uma atualização estiver disponível, você terá um alerta (vermelho ou laranja, dependendo da gravidade do problema). Para obter uma explicação completa dessa recomendação, clique em **Atualizar versão do sistema operacional** na coluna **Descrição** .

### <a name="app-services"></a>Serviços de aplicativos
Para exibir as informações do serviço de aplicativo, você deve estar no tipo de preço Standard da central de segurança e habilitar o serviço de aplicativo em sua assinatura. Para obter instruções sobre como habilitar esse recurso, consulte [proteger o serviço de aplicativo com a central de segurança do Azure](security-center-app-services.md).


Sob **dos serviços de aplicações**, encontrar uma lista dos seus ambientes de serviço de aplicações e o estado de funcionamento resumo com base na avaliação do Centro de segurança efetuada.

![Serviços aplicacionais](./media/security-center-virtual-machine-recommendations/app-services.png)

Há três tipos de serviços de aplicativo mostrados:

![Ambiente de serviços de aplicações](./media/security-center-virtual-machine-recommendations/ase.png) Ambiente de serviços de aplicações

![Aplicação Web](./media/security-center-virtual-machine-recommendations/web-app.png) Aplicação Web

![Aplicação de função](./media/security-center-virtual-machine-recommendations/function-app.png) Aplicação de função

Se você selecionar um aplicativo Web, uma exibição de resumo será aberta com três guias:

   - **Recomendações**: com base em avaliações executadas pela central de segurança que falharam.
   - **Passados avaliações**: lista de avaliações realizadas pelo centro de segurança passado.
   - **Avaliações indisponíveis**: lista de avaliações de que não foi possível executar devido a um erro ou a recomendação não é relevante para o serviço de aplicações específico

   Sob **recomendações** é uma lista das recomendações para a aplicação web selecionada e a gravidade de cada recomendação.

   ![Recomendações de serviços de aplicativos](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Selecione uma recomendação para ver uma descrição da recomendação e uma lista de recursos não íntegros, recursos íntegros e recursos não verificados.

   - A coluna **avaliações passadas** mostra uma lista de avaliações aprovadas. Gravidade dessas avaliações é sempre verde.

   - Selecione uma avaliação com êxito da lista para obter uma descrição da avaliação, uma lista de recursos de mau e bom estado de funcionamento e uma lista de recursos não verificados. Há uma guia para os recursos de mau estado de funcionamento, mas essa lista está sempre vazia, uma vez que a avaliação passado.

### <a name="vmscale-sets"></a>Conjuntos de dimensionamento de máquinas virtuais
A central de segurança descobre automaticamente se você tem conjuntos de dimensionamento e recomenda que você instale o Microsoft Monitoring Agent neles.

Para instalar o Microsoft Monitoring Agent: 

1. Selecione a recomendação **instalar o agente de monitoramento no conjunto de dimensionamento de máquinas virtuais**. Você Obtém uma lista de conjuntos de dimensionamento não monitorados.

1. Selecione um conjunto de dimensionamento não íntegro. Siga as instruções para instalar o agente de monitoramento usando um espaço de trabalho preenchido existente ou crie um novo. Certifique-se de definir o [tipo de preço](security-center-pricing.md) do espaço de trabalho se ele não estiver definido.

   ![Instalar o MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Para definir novos conjuntos de dimensionamento para instalar automaticamente o Microsoft Monitoring Agent:
1. Vá para Azure Policy e clique em **definições**.

1. Procure a política **implantar log Analytics agente para conjuntos de dimensionamento de máquinas virtuais do Windows** e clique nele.

1. Clique em **Atribuir**.

1. Defina o **escopo** e **log Analytics espaço de trabalho** e clique em **atribuir**.

Se você quiser definir todos os conjuntos de dimensionamento existentes para instalar o Microsoft Monitoring Agent, em Azure Policy, vá para **correção** e aplique a política existente aos conjuntos de dimensionamento existentes.


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre as recomendações que se aplicam a outros tipos de recursos do Azure, consulte os seguintes artigos:

* [Lista de referências completa das recomendações de segurança da central de segurança do Azure](recommendations-reference.md)
* [Monitor identity and access in Azure Security Center](security-center-identity-access.md) (Monitorizar a identidade e o acesso no Centro de Segurança do Azure)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
* [Proteger o seu serviço do SQL do Azure no Centro de segurança do Azure](security-center-sql-service-recommendations.md)