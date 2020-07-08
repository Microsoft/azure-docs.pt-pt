---
title: Proteja as suas máquinas e aplicações
description: Este documento aborda recomendações no Security Center que o ajudam a proteger as suas máquinas e computadores virtuais e as suas aplicações web e ambientes de Serviço de Aplicações.
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
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: 8faff37e127e80447e0f066ef17e2885d4bcb597
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85799739"
---
# <a name="protect-your-machines-and-applications"></a>Proteja as suas máquinas e aplicações
Quando o Azure Security Center identifica potenciais vulnerabilidades de segurança, cria recomendações que o guiem através do processo de configuração dos controlos necessários para endurecer e proteger os seus recursos.

Este artigo explica a página **de Computação e Aplicações** da secção de segurança de recursos do Security Center.

Para obter uma lista completa das recomendações que pode ver nesta página, consulte [as recomendações do Compute e das aplicações.](recommendations-reference.md#recs-computeapp)


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Ver a segurança dos recursos do seu cálculo e aplicações

[![Dashboard Centro de Segurança](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Para ver o estado dos recursos do seu cálculo e aplicações, a partir do painel esquerdo no Centro de Segurança, **selecione Computação & aplicações**. Estão disponíveis os seguintes separadores:

* **Resumo :** lista as recomendações para todos os recursos de computação e aplicações, bem como o seu estado atual de segurança 

* [**VMs e Servidores**](#vms-and-computers): lista as recomendações para os seus VMs, computadores e estado de segurança atual de cada um

* [**Conjuntos de escala VM**](#vmscale-sets): lista as recomendações para os seus conjuntos de escala, 

* [**Serviços na nuvem**](#cloud-services): lista as recomendações para as suas funções web e de trabalho monitorizadas pelo Security Center

* [**Serviços de aplicações**](#app-services): lista as recomendações para os ambientes de serviço da App e o estado atual de segurança de cada um

* [**Contentores**](#containers): lista as recomendações para os seus contentores e avaliação de segurança das suas configurações

* **Recursos de cálculo**: lista as recomendações para os seus recursos de computação, tais como clusters de Tecidos de Serviço e centros de eventos

### <a name="whats-in-each-tab"></a>O que há em cada conta?

Cada separador tem várias secções e, em cada secção, pode perfurar para ver detalhes adicionais sobre o item mostrado.

Em cada separador, também verá recomendações para os recursos relevantes no seu ambiente monitorizado. A primeira coluna enumera a recomendação, a segunda mostra o número total de recursos afetados, e a terceira mostra a gravidade da questão.

Cada recomendação tem um conjunto de ações que pode executar depois de selecioná-la. Por exemplo, se selecionar **atualizações do sistema Missing**, o número de VMs e computadores que faltam patches e a gravidade da atualização em falta aparece.

> [!NOTE]
> As recomendações de segurança são as mesmas que estão na página **de Recomendações,** mas aqui são filtradas para o tipo de recurso específico que selecionou. Para obter mais informações sobre como resolver recomendações, consulte [as recomendações de segurança de implementação no Centro de Segurança Azure.](security-center-recommendations.md)
>




### <a name="vms-and-servers"></a><a name="vms-and-computers"></a>VMs e Servidores
A secção VMs e computadores dá-lhe uma visão geral de todas as recomendações de segurança para os seus VMs e computadores. Quatro tipos de máquinas estão incluídos:

![Computador não pertencente ao Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computador não Azure.

![Gestor de Recursos Azure VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) Azure Resource Manager VM.

![Azure Classic VM](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) Azure Classic VM.

![VMs identificados a partir do espaço de trabalho](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VMs que só são identificadas a partir da área de trabalho que faz parte da subscrição vista. Isto inclui VMs de outras subscrições que reportam ao espaço de trabalho nesta subscrição, e VMs que foram instalados com o agente direto do Gestor de Operações, e não têm identificação de recursos.

O ícone que aparece sob cada recomendação ajuda-o a identificar rapidamente o VM e o computador que precisa de atenção, e o tipo de recomendação. Também pode utilizar os filtros para pesquisar a lista por **tipo de recurso** e por **Severidade.**

Para aprofundar as recomendações de segurança para cada VM, clique no VM.
Aqui vê os detalhes de segurança do VM ou do computador. Na parte inferior, pode ver a ação recomendada e a gravidade de cada emissão.

[![Serviços na nuvem](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="virtual-machine-scale-sets"></a><a name="vmscale-sets"></a>Conjuntos de escala de máquina virtual
O Security Center descobre automaticamente se tem conjuntos de escala e recomenda a instalação do agente Log Analytics neles.

Para instalar o agente Log Analytics: 

1. Selecione a recomendação **Instale o agente de monitorização no conjunto de escala de máquina virtual**. Obtém-se uma lista de conjuntos de escala não monitorizados.

1. Selecione um conjunto de escala pouco saudável. Siga as instruções para instalar o agente de monitorização utilizando um espaço de trabalho povoado existente ou criar um novo. Certifique-se de definir o [nível de preços](security-center-pricing.md) do espaço de trabalho se não estiver definido.

   ![Instalar MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Para definir novos conjuntos de escala para instalar automaticamente o agente Log Analytics:
1. Vá à Política Azure e clique em **Definições**.

1. Procure a política Implementar o **agente 'Implementar's'analíticos para conjuntos de escala de máquina virtual do Windows** e clique nele.

1. Clique em **Atribuir**.

1. Desa estalido o **espaço de trabalho** **Scope** e Log Analytics e clique em **Atribuir**.

Se pretender definir todos os conjuntos de escala existentes para instalar o agente Log Analytics, na Política Azure, vá à **Remediação** e aplique a política existente nos conjuntos de escala existentes.





### <a name="cloud-services"></a><a name="cloud-services"></a>Serviços na nuvem
Para os serviços na nuvem, é criada uma recomendação quando a versão do sistema operativo está desatualizada.

![Serviços em nuvem](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Num cenário em que tenha uma recomendação, siga os passos da recomendação para atualizar o sistema operativo. Quando uma atualização estiver disponível, terá um alerta (vermelho ou laranja - dependendo da gravidade do problema). Para obter uma explicação completa desta recomendação, clique na **versão 'Atualizar OS'** na coluna **DESCRIPTION.**






### <a name="app-services"></a><a name="app-services"></a>Serviços de aplicações
Para ver as informações do Serviço de Aplicações, tem de estar no nível de preços Standard do Security Center e ativar o Serviço de Aplicações na sua subscrição. Para obter instruções para ativar esta função, consulte [protect App Service com O Centro de Segurança Azure](security-center-app-services.md).

Nos **serviços da App,** encontra uma lista dos ambientes do seu serviço de Aplicação e o resumo da saúde com base na avaliação realizada pelo Centro de Segurança.

![Serviços de aplicações](./media/security-center-virtual-machine-recommendations/app-services.png)

Existem três tipos de serviços de aplicação mostrados:

![Ambiente de serviços de aplicações](./media/security-center-virtual-machine-recommendations/ase.png) Ambiente de serviços de aplicações

![Aplicação Web](./media/security-center-virtual-machine-recommendations/web-app.png) Aplicação Web

![Aplicação de função](./media/security-center-virtual-machine-recommendations/function-app.png) Aplicação de função

Se selecionar uma aplicação web, uma vista de resumo abre com três separadores:

   - **Recomendações**: com base em avaliações realizadas pelo Centro de Segurança que falharam.
   - **Avaliações aprovadas**: lista de avaliações realizadas pelo Centro de Segurança que passou.
   - **Avaliações indisponíveis**: lista de avaliações que não foram executadas devido a um erro ou à recomendação não é relevante para o serviço de Aplicações específico

   Ao abrigo **de Recomendações** está uma lista das recomendações para a aplicação web selecionada e gravidade de cada recomendação.

   ![Recomendações dos Serviços de Aplicações](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Selecione uma recomendação para ver uma descrição da recomendação e uma lista de recursos não saudáveis, recursos saudáveis e recursos não testados.

   - A coluna **de avaliações aprovada** mostra uma lista de avaliações aprovadas. A gravidade destas avaliações é sempre verde.

   - Selecione uma avaliação aprovada da lista para uma descrição da avaliação, uma lista de recursos pouco saudáveis e saudáveis, e uma lista de recursos não identificados. Há um separador de recursos pouco saudáveis, mas essa lista está sempre vazia desde que a avaliação foi aprovada.





### <a name="containers"></a><a name="containers"></a>Contentores

Ao abrir o **separador Contentores,** dependendo do seu ambiente, poderá ver qualquer um dos três tipos de recursos:

![Hospedeiro de contentores](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Anfitriões de contentores - VMs running docker 

![Kubernetes serviço ](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Azure Kubernetes Service (AKS) clusters. [Saiba mais sobre o pacote AKS do Security Center](azure-kubernetes-service-integration.md)

![Registo de contentores ](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Registo do Contentor Azure (ACR). [Saiba mais sobre o pacote ACR do Security Center](azure-container-registry-integration.md)

Para obter instruções sobre como utilizar as características de segurança do recipiente, consulte [a segurança dos seus recipientes](monitor-container-security.md).



[![Separador de contentores](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Para ver as recomendações para um recurso específico na lista, clique nesse recurso.

#### <a name="visibility-into-container-registries"></a>Visibilidade nos registos de contentores

Por exemplo, clicar no registo ACR de demonstração de asc-demo da lista mostrada no gráfico acima leva a esta página de detalhes:

[![Recomendações para um registo específico de ACR](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Visibilidade em contentores alojados em máquinas IaaS Linux

Quando clicar num dos VMs em execução, verá a página de detalhes com informações relacionadas com os recipientes na máquina, como a versão Docker e o número de imagens em execução no anfitrião.

![Recomendações para um estivador de execução VM](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Recomendações de segurança baseadas no referencial cis para Docker

O Centro de Segurança analisa as suas configurações do Docker e fornece-lhe visibilidade sobre as configurações incorretas ao disponibilizar uma lista de todas as regras com falhas que foram analisadas. O Security Center fornece diretrizes para ajudá-lo a resolver estes problemas rapidamente e economizar tempo. O Centro de Segurança avalia continuamente as configurações do Docker e fornece o estado mais recente.

![separador de contentores](./media/security-center-virtual-machine-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Próximos passos
Para saber mais sobre recomendações aplicáveis a outros tipos de recursos Azure, consulte os seguintes artigos:

* [Lista completa de recomendações de segurança do Azure Security Center](recommendations-reference.md)
* [Monitorizar a identidade e o acesso no Centro de Segurança do Azure](security-center-identity-access.md)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
* [Proteger o seu serviço Azure SQL no Azure Security Center](security-center-sql-service-recommendations.md)