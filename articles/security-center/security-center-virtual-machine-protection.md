---
title: Proteja as suas máquinas e aplicações
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
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: bcf92838483fbb6b54802cc0d44cc44ea086d705
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430701"
---
# <a name="protect-your-machines-and-applications"></a>Proteja as suas máquinas e aplicações
Quando o Azure Security Center identifica potenciais vulnerabilidades de segurança, cria recomendações que o guiam através do processo de configuração dos controlos necessários para endurecer e proteger os seus recursos.

Este artigo explica a página **computada e apps** da secção de segurança de recursos do Security Center.

Para obter uma lista completa das recomendações que poderá ver nesta página, consulte recomendações de [Compute e apps](recommendations-reference.md#recs-computeapp).


## <a name="view-the-security-of-your-compute-and-apps-resources"></a>Veja a segurança dos seus recursos computacionais e apps

[painel do Centro de Segurança ![](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png)](./media/security-center-virtual-machine-recommendations/compute-and-apps-recs-overview.png#lightbox)

Para ver o estado dos recursos da sua computação e aplicações, a partir do painel esquerdo no Centro de Segurança, **selecione Compute & apps**. Estão disponíveis os seguintes separadores:

* **Resumo**: lista as recomendações para todos os recursos de computação e aplicações, bem como o seu estado de segurança atual 

* [**VMs e Servidores**](#vms-and-computers): lista as recomendações para os seus VMs, computadores e estado de segurança atual de cada

* [**Conjuntos de escala VM:** ](#vmscale-sets)lista as recomendações para os seus conjuntos de escala, 

* [**Serviços em nuvem**](#cloud-services): lista as recomendações para as suas funções web e trabalhadores monitorizadas pelo Security Center

* [**Serviços**](#app-services)de aplicações : lista as recomendações para os ambientes de serviço da sua App e o estado de segurança atual de cada

* [**Contentores**](#containers): lista as recomendações para os seus contentores e avaliação de segurança das suas configurações

* **Recursos computacionais**: lista as recomendações para os seus recursos computacionais, tais como clusters de tecido de serviço e centros de eventos

### <a name="whats-in-each-tab"></a>O que tem em cada separador?

Cada separador tem várias secções, e em cada secção, pode perfurar para ver detalhes adicionais sobre o item mostrado.

Em cada separador, você também verá recomendações para os recursos relevantes no seu ambiente monitorizado. A primeira coluna enumera a recomendação, a segunda mostra o número total de recursos afetados, e a terceira mostra a gravidade da questão.

Cada recomendação tem um conjunto de ações que pode realizar depois de a selecionar. Por exemplo, se selecionar atualizações do **sistema Missing,** o número de VMs e computadores que faltam patches, e a gravidade da atualização em falta aparece.

> [!NOTE]
> As recomendações de segurança são as mesmas que estão na página de **Recomendações,** mas aqui são filtradas para o tipo de recurso específico que selecionou. Para obter mais informações sobre como resolver recomendações, consulte [a implementação de recomendações](security-center-recommendations.md)de segurança no Centro de Segurança Azure.
>




### <a name="vms-and-computers"></a>VMs e Servidores
A secção vMs e computadores dá-lhe uma visão geral de todas as recomendações de segurança para os seus VMs e computadores. Estão incluídos quatro tipos de máquinas:

![Computador não pertencente ao Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png) Computador não Azure.

![VM do Azure Resource Manager](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png) VM do Azure Resource Manager.

![VM clássica do Azure](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png) VM clássica do Azure.

![VMs identificadas a partir da área de trabalho](./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png) VMs que só são identificadas a partir da área de trabalho que faz parte da subscrição vista. Isto inclui VMs de outras subscrições que reportam ao espaço de trabalho nesta subscrição, e VMs que foram instalados com o Agente Direto do Gestor de Operações, e não têm identificação de recursos.

O ícone apresentado em cada recomendação ajuda-o a identificar rapidamente a VM e o computador que precisa de atenção e o tipo de recomendação. Também pode utilizar os filtros para pesquisar a lista por **tipo de Recurso** e por **Severity**.

Para aprofundar as recomendações de segurança de cada VM, clique no VM.
Aqui verá os detalhes de segurança para o computador ou VM. No fundo, pode ver a ação recomendada e a gravidade de cada edição.

[Serviços ![Cloud](./media/security-center-virtual-machine-recommendations/recommendation-list.png)](./media/security-center-virtual-machine-recommendations/recommendation-list.png#lightbox)




### <a name="vmscale-sets"></a>Conjuntos de escala de máquina virtual
O Security Center descobre automaticamente se tem conjuntos de escala e recomenda que instale o Microsoft Monitoring Agent nos mesmos.

Para instalar o Agente de Monitorização da Microsoft: 

1. Selecione a recomendação **Instale o agente de monitorização no conjunto**de escala de máquina virtual . Obtém-se uma lista de conjuntos de escala não monitorizados.

1. Selecione um conjunto de escala pouco saudável. Siga as instruções para instalar o agente de monitorização utilizando um espaço de trabalho povoado existente ou criar um novo. Certifique-se de que define o [nível](security-center-pricing.md) de preços do espaço de trabalho se não estiver definido.

   ![Instalar MMS](./media/security-center-virtual-machine-recommendations/install-mms.png)

Para definir novos conjuntos de escala para instalar automaticamente o Agente de Monitorização da Microsoft:
1. Vá à Política Azure e clique em **Definições**.

1. Procure a política **Implementar o agente Log Analytics para conjuntos** de escala de máquinas virtuais do Windows e clique nele.

1. Clique em **Atribuir**.

1. **Detete** o espaço de trabalho scope e Log **Analytics** e clique em **Atribuir**.

Se pretender definir todos os conjuntos de escala existentes para instalar o Microsoft Monitoring Agent, na Política Azure, vá à **Remediation** e aplique a política existente aos conjuntos de escala existentes.





### <a name="cloud-services"></a>Serviços em nuvem
Para os serviços na nuvem, é criada uma recomendação quando a versão do sistema operativo está desatualizada.

![Serviços em nuvem](./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png)

Num cenário em que tenha uma recomendação, siga os passos na recomendação de atualização do sistema operativo. Quando uma atualização estiver disponível, terá um alerta (vermelho ou laranja - dependendo da gravidade do problema). Para obter uma explicação completa desta recomendação, clique na **versão 'Atualizar o SISTEMA'** na coluna **DESCRIPTION.**






### <a name="app-services"></a>Serviços de aplicações
Para visualizar as informações do Serviço de Aplicações, deve estar no nível de preços Standard do Security Center e ativar o Serviço de Aplicações na sua subscrição. Para obter instruções sobre a ativação desta funcionalidade, consulte [O Serviço de Aplicações Protect com o Azure Security Center](security-center-app-services.md).

No âmbito dos **serviços de Aplicação,** encontra uma lista dos ambientes de serviço da sua App e o resumo de saúde com base na avaliação realizada pelo Security Center.

![Serviços aplicacionais](./media/security-center-virtual-machine-recommendations/app-services.png)

Existem três tipos de serviços de aplicação apresentados:

![Ambiente de serviços de aplicações](./media/security-center-virtual-machine-recommendations/ase.png) Ambiente de serviços de aplicações

![Aplicação Web](./media/security-center-virtual-machine-recommendations/web-app.png) Aplicação Web

![Aplicação de função](./media/security-center-virtual-machine-recommendations/function-app.png) Aplicação de função

Se selecionar uma aplicação web, abre-se uma vista sumária com três separadores:

   - **Recomendações**: com base em avaliações realizadas pelo Centro de Segurança que falharam.
   - **Avaliações aprovadas**: lista de avaliações realizadas pelo Centro de Segurança que passou.
   - **Avaliações indisponíveis**: lista de avaliações que não foram executadas devido a um erro ou a recomendação não é relevante para o serviço app específico

   De acordo com **as Recomendações** está uma lista das recomendações para a aplicação web selecionada e gravidade de cada recomendação.

   ![Recomendações de Serviços de Aplicações](./media/security-center-virtual-machine-recommendations/app-services-rec.png)

Selecione uma recomendação para ver uma descrição da recomendação e uma lista de recursos não saudáveis, recursos saudáveis e recursos não digitalizados.

   - A coluna **de avaliações aprovada** mostra uma lista de avaliações aprovadas. Gravidade dessas avaliações é sempre verde.

   - Selecione uma avaliação com êxito da lista para obter uma descrição da avaliação, uma lista de recursos de mau e bom estado de funcionamento e uma lista de recursos não verificados. Há uma guia para os recursos de mau estado de funcionamento, mas essa lista está sempre vazia, uma vez que a avaliação passado.





### <a name="containers"></a>Contentores

Ao abrir o separador **Contentores,** dependendo do seu ambiente, poderá ver qualquer um dos três tipos de recursos:

![Hospedeiro de contentores](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png) Anfitriões de contentores - VMs executando estivador 

![Serviço Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png) Clusters do Serviço Azure Kubernetes (AKS)

![Registo de contentor](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png) Registos de Registo de Contentores Azure (ACR) - Mostrado apenas quando se está no nível de preços padrão e quando tem o pacote de registo de contentores Azure ativado.

Para obter instruções sobre como utilizar as características de segurança do contentor, consulte [monitorizar a segurança dos seus contentores](monitor-container-security.md).

Os benefícios do pacote de registo de contentores Azure são explicados [aqui](azure-container-registry-integration.md)

Os benefícios do pacote de serviços kubernetes são explicados [aqui](azure-kubernetes-service-integration.md)

[separador de recipientes ![](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png)](./media/security-center-virtual-machine-recommendations/container-recommendations-all-types.png#lightbox)

Para ver as recomendações de um recurso específico na lista, clique nesse recurso.

#### <a name="visibility-into-container-registries"></a>Visibilidade nos registos de contentores

Por exemplo, clicar no registo asc-demo ACR da lista mostrada no gráfico acima leva a esta página de detalhes:

[![Recomendações para um registo específico da ACR](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png)](./media/security-center-virtual-machine-recommendations/acr-registry-recs-list.png#lightbox)


#### <a name="visibility-into-containers-hosted-on-iaas-linux-machines"></a>Visibilidade em recipientes alojados em máquinas IaaS Linux

Quando clicar num dos VMs em execução, verá a página de detalhes com informações relacionadas com os contentores na máquina, como a versão DoDocker e o número de imagens em execução no anfitrião.

![Recomendações para um VM executando estivador](./media/security-center-virtual-machine-recommendations/docker-recommendation.png)


#### <a name="security-recommendations-based-on-cis-benchmark-for-docker"></a>Recomendações de segurança baseadas no referencial do CIS para Docker

O Centro de Segurança analisa as suas configurações do Docker e fornece-lhe visibilidade sobre as configurações incorretas ao disponibilizar uma lista de todas as regras com falhas que foram analisadas. O Centro de Segurança fornece diretrizes para ajudá-lo a resolver estes problemas rapidamente e economizar tempo. O Centro de Segurança avalia continuamente as configurações do Docker e fornece o estado mais recente.

![separador de recipiente](./media/security-center-container-recommendations/container-cis-benchmark.png)


## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre recomendações aplicáveis a outros tipos de recursos Azure, consulte os seguintes artigos:

* [Lista completa de referência das recomendações de segurança do Azure Security Center](recommendations-reference.md)
* [Monitor identity and access in Azure Security Center](security-center-identity-access.md) (Monitorizar a identidade e o acesso no Centro de Segurança do Azure)
* [Proteger a sua rede no Centro de Segurança do Azure](security-center-network-recommendations.md)
* [Proteger o seu serviço Azure SQL no Azure Security Center](security-center-sql-service-recommendations.md)