---
title: Visão geral dos clusters Azure Stream Analytics (Pré-visualização)
description: Saiba mais sobre a oferta dedicada do Stream Analytics Cluster.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: overview
ms.custom: mvc
ms.date: 09/22/2020
ms.openlocfilehash: 75a95fa4ad36b69ca920f527060644dcc5264ce8
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019538"
---
# <a name="overview-of-azure-stream-analytics-cluster-preview"></a>Visão geral do Azure Stream Analytics Cluster (Pré-visualização)

O Azure Stream Analytics Cluster oferece uma implantação de um único inquilino para cenários de streaming complexos e exigentes. Em plena escala, os clusters Stream Analytics podem processar mais de 200 MB/segundo em tempo real. Os trabalhos do Stream Analytics em clusters dedicados podem aproveitar todas as funcionalidades da oferta Standard e inclui suporte para conectividade de ligação privada às suas entradas e saídas.

Os clusters Stream Analytics são faturados por Unidades de Streaming (SUs) que representam a quantidade de CPU e recursos de memória atribuídos ao seu cluster. Uma Unidade de Streaming é a mesma em todas as ofertas standard e dedicadas. Você pode comprar 36, 72, 108, 144, 180 ou 216 SUs para cada cluster. Um cluster Stream Analytics pode servir como plataforma de streaming para a sua organização e pode ser partilhado por diferentes equipas que trabalham em vários casos de uso.

## <a name="what-are-stream-analytics-clusters"></a>O que são clusters Stream Analytics

Os clusters do Stream Analytics são alimentados pelo mesmo motor que alimenta os trabalhos do Stream Analytics em execução num ambiente de vários inquilinos. O cluster dedicado de inquilino único tem as seguintes funcionalidades:

* Alojamento de inquilino único sem ruído de outros inquilinos. Os recursos estão verdadeiramente “isolados” e têm melhor desempenho quando há picos de tráfego.

* Dimensione o seu cluster entre 36 a 216 SUs à medida que o seu uso de streaming aumenta ao longo do tempo.

* Suporte VNet que permite que os seus trabalhos stream Analytics se conectem a outros recursos de forma segura usando pontos finais privados.

* Capacidade de autor de funções definidas pelo utilizador C# e deserializadores personalizados em qualquer região.

* Custo de manutenção zero permite-lhe concentrar o seu esforço na construção de soluções de análise em tempo real.

## <a name="how-to-get-started"></a>Como começar

Pode [criar um cluster Stream Analytics](create-cluster.md) através do portal [Azure.](https://aka.ms/asaclustercreateportal) Se tiver alguma dúvida ou precisar de ajuda no embarque, pode contactar a [equipa stream Analytics](mailto:askasa@microsoft.com).

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-do-i-choose-between-a-stream-analytics-cluster-and-a-stream-analytics-job"></a>Como escolho entre um cluster Stream Analytics e um trabalho stream Analytics?

A maneira mais fácil de começar é criar e desenvolver um trabalho stream Analytics para se familiarizar com o serviço e ver como ele pode satisfazer os seus requisitos de análise.

Os trabalhos de Stream Analytics por si só não suportam VNets. Se as suas entradas ou saídas estiverem seguras atrás de uma firewall ou de uma Rede Virtual Azure, tem as seguintes duas opções:

* Se a sua máquina local tiver acesso aos recursos de entrada e saída protegidos por um VNet (por exemplo, Azure Event Hubs ou Azure SQL Database), pode [instalar ferramentas Azure Stream Analytics para o Visual Studio](stream-analytics-tools-for-visual-studio-install.md) na sua máquina local. Pode desenvolver e [testar trabalhos stream Analytics localmente](stream-analytics-live-data-local-testing.md) no seu dispositivo sem incorrer em qualquer custo. Assim que estiver pronto para usar o Stream Analytics na sua arquitetura, pode então criar um cluster Stream Analytics, configurar pontos finais privados e executar os seus trabalhos em escala.

* Você pode criar um cluster Stream Analytics, configurar o cluster com os pontos finais privados necessários para o seu pipeline, e executar seus trabalhos stream Analytics no cluster.

### <a name="what-performance-can-i-expect"></a>Que desempenho posso esperar?

Um SU é o mesmo em todas as ofertas standard e dedicadas. Um único trabalho que utilize um cluster de 36 SU completo pode alcançar aproximadamente 36 MB/segundo de produção com latência milissegundo. O número exato depende do formato dos eventos e do tipo de análise. Por ser dedicado, o cluster Stream Analytics oferece garantias de desempenho mais fiáveis. Todos os trabalhos que correm no teu agrupamento pertencem apenas a ti.

### <a name="can-i-scale-my-cluster"></a>Posso escalar o meu aglomerado?

Sim. Pode configurar facilmente a capacidade do seu cluster permitindo-lhe escalar para cima ou para [baixo,](scale-cluster.md) conforme necessário para satisfazer a sua procura em mudança.

### <a name="can-i-run-my-existing-jobs-on-these-new-clusters-ive-created"></a>Posso gerir os meus empregos nestes novos agrupamentos que criei?

Sim. Pode ligar os seus empregos existentes ao seu recém-criado cluster Stream Analytics e executá-lo como de costume. Não tens de recriar os teus trabalhos stream analytics do zero.

### <a name="how-much-will-these-clusters-cost-me"></a>Quanto é que estes agrupamentos me vão custar?

Os seus clusters Stream Analytics são carregados com base na capacidade de SU escolhida. Os agrupamentos são cobrados de hora a hora e não há taxas adicionais por trabalho a funcionar nestes agrupamentos. Consulte a [página de preços do Serviço de Ligação Privada](https://azure.microsoft.com/pricing/details/private-link/) para obter atualizações de faturação de ponto final privado.

### <a name="which-inputs-and-outputs-can-i-privately-connect-to-from-my-stream-analytics-cluster"></a>Quais as entradas e saídas a que posso ligar em privado do meu cluster Stream Analytics?

Stream Analytics suporta vários tipos de entrada e saída. Pode [criar pontos finais privados](private-endpoints.md) no seu cluster que permitam que os empregos acedam aos recursos de entrada e produção. Atualmente Azure SQL Database, Azure Storage, Azure Data Lake Storage Gen2, Azure Event Hub e Azure Service Bus são serviços apoiados e outros tipos serão adicionados em breve. 

## <a name="next-steps"></a>Próximos passos

Tem agora uma visão geral do cluster Azure Stream Analytics. Em seguida, pode criar o seu cluster e executar o seu trabalho stream Analytics: 

* [Criar um cluster Stream Analytics](create-cluster.md)
* [Gerir pontos finais privados num cluster Azure Stream Analytics](private-endpoints.md)
* [Gerir trabalhos de Stream Analytics num cluster Stream Analytics](manage-jobs-cluster.md)
* [Criar um trabalho stream analytics](stream-analytics-quick-create-portal.md)
