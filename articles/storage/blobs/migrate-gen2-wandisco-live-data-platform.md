---
title: Data Lake Storage e WANdisco LiveData Platform for Azure (pré-visualização)
description: Migrar no local dados Hadoop para Azure Data Lake Storage Gen2 utilizando a Plataforma LIVEData WANdisco para o Azure.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.reviewer: b-pauls
ms.date: 11/17/2020
ms.service: storage
ms.custom: references_regions
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: a0d02530ba2b8758b467b77ff639437675e4cc81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99508934"
---
# <a name="meet-demanding-migration-requirements-with-wandisco-livedata-platform-for-azure-preview"></a>Satisfaça requisitos de migração exigentes com a Plataforma LiveData waNdisco para o Azure (pré-visualização)

Migrar no local dados hadoop para Azure Data Lake Storage Gen2 utilizando [a Plataforma LIVEData WANdisco para o Azure](https://docs.wandisco.com/live-data-platform/docs/landing/). Esta plataforma elimina a necessidade de tempo de inatividade da aplicação, elimina a possibilidade de perda de dados e garante a consistência dos dados mesmo enquanto as operações continuam no local.  

> [!NOTE]
> A Plataforma WANdisco LiveData para a Azure está em pré-visualização pública. Para disponibilidade regional, consulte [regiões apoiadas.](https://docs.wandisco.com/live-data-platform/docs/prereq#supported-regions)

A plataforma é composta por dois serviços: [LiveData Migrador para a Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) para migrar dados ativamente utilizados de ambientes no local para armazenamento Azure, e [LiveData Plane for Azure,](https://www.wandisco.com/products/livedata-plane-for-azure) que garante que todos os dados modificados ou dados ingeridos são replicados de forma consistente. 

> [!div class="mx-imgBorder"]
> ![Ilustração geral da plataforma de dados ao vivo](./media/migrate-gen2-wandisco-live-data-platform/live-data-platform-overview.png)

Você pode gerir ambos os serviços usando o portal Azure e o Azure CLI, e ambos seguem o mesmo modelo de faturação medido, pay-as-you-go como todos os outros serviços Azure. A Plataforma LiveData para consumo de Azure aparecerá na mesma fatura mensal do Azure e fornecerá uma forma consistente e conveniente de rastrear e monitorizar o seu uso.

Ao contrário da migração _de_ dados [offline, copiando informações estáticas para a Azure Data Box](./data-lake-storage-migrate-on-premises-hdfs-cluster.md), ou utilizando ferramentas Hadoop como [o DistCp,](https://hadoop.apache.org/docs/current/hadoop-distcp/DistCp.html)pode manter o pleno funcionamento dos seus sistemas de negócio durante a migração _online_ com o WANdisco LiveData para o Azure. Mantenha os seus grandes ambientes de dados a funcionar mesmo enquanto muda os seus dados para Azure.

## <a name="key-features-of-wandisco-livedata-platform-for-azure"></a>Principais funcionalidades da Plataforma LiveData waNdisco para o Azure

[A WANdisco LiveData Platform for Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) utiliza um motor de consenso único e de vasta área capaz de obter consistência de dados e para realizar a replicação de dados em escala enquanto as aplicações podem continuar a modificar os dados em replicação. <br><br>

>[!VIDEO https://www.youtube.com/embed/KRrmcYPxEho] 

As principais características da plataforma incluem:

- Consistência dos **dados**: Resolva os desafios da migração de grandes volumes de dados entre ambientes e mantenha esses dados consistentes entre os sistemas de armazenamento através da migração de produção, mesmo estando em constante mudança. Empregue o único motor de consenso de ampla área da WANdisco, capaz de obter a consistência dos dados e migrar dados com garantias de consistência ao longo das mudanças de dados.

- **Manutenção de operações**: Como as aplicações podem continuar a criar, modificar, ler e apagar dados durante a migração, não há necessidade de perturbar as operações comerciais ou introduzir uma janela de paragem apenas para migrar big data para Azure. Continue a operar aplicações, infraestruturas de análise, ingerir empregos e outros processamentos.

- **Resultados validados**: A validação de ponta a ponta de que os seus dados podem ser utilizados eficazmente uma vez migrados para Azure requer que execute cargas de trabalho de aplicação de produção contra eles. Apenas um Serviço LiveData o fornece sem introduzir o risco de divergência de dados, mantendo a consistência dos dados independentemente de a mudança ocorrer na fonte ou no alvo da sua migração. Teste e valide o comportamento da aplicação sem risco ou alteração nos seus processos e sistemas.

- **Reduzir a complexidade**: Eliminar a necessidade de criar e gerir postos de trabalho programados para copiar dados através da migração de dados através da automação. Use a profunda integração com o Azure como um plano de controlo para gerir e monitorizar o progresso da migração, incluindo replicação seletiva de dados, metadados de Hive, segurança de dados e confidencialidade.

- **Eficiência**: Mantenha uma elevada produção e desempenho, e escala para grandes volumes de dados facilmente. Com o controlo do consumo de largura de banda, pode garantir que pode cumprir os seus objetivos de migração sem afetar outras operações do sistema.

## <a name="migrate-big-data-faster-without-risk"></a>Migrar big data mais rápido sem risco

O primeiro serviço da PLATAFORMA WANdisco LiveData para a Azure é [o LiveData Migrador para Azure;](https://www.wandisco.com/products/livedata-migrator-for-azure) uma solução para a migração de dados ativamente utilizados dos ambientes no local para o armazenamento de Azure. O Migrador LiveData para Azure é ateado e gerido inteiramente a partir do portal Azure ou Azure CLI, e opera ao lado do seu cluster Hadoop no local sem qualquer alteração de configuração, modificações de aplicações ou reinicia o serviço para começar a migrar dados imediatamente.

> [!div class="mx-imgBorder"]
> ![Migrador LiveData para Arquitetura Azure](./media/migrate-gen2-wandisco-live-data-platform/live-data-migrator-architecture.png)

As grandes migrações de dados podem ser complexas e desafiantes. A movimentação de petabytes de informação sem perturbar as operações comerciais tem sido impossível de conseguir com tecnologias offline de cópia de dados. [O LiveData Migrator for Azure](https://www.wandisco.com/products/livedata-migrator-for-azure) oferece uma implementação simples e pode estabelecer um Serviço LiveData com migração contínua de dados e replicação enquanto as aplicações lêem, escrevem e modificam os dados que estão a ser migrados.

Realizar uma migração é tão simples como estes três passos:

1. Fornecendo a instância migradora LiveData do portal Azure para o seu cluster Hadoop no local. Não é necessária qualquer alteração de cluster ou tempo de inatividade e as aplicações podem continuar a funcionar.

   > [!div class="mx-imgBorder"]
   >![Criar uma instância migradora LiveData](./media/migrate-gen2-wandisco-live-data-platform/create-live-data-migrator.png)

2. Defina a conta de armazenamento ativada por Azure Data Lake Gen2.

   > [!div class="mx-imgBorder"]
   >![Criar um alvo migrador LiveData](./media/migrate-gen2-wandisco-live-data-platform/create-target.png)

3. Defina a localização dos dados que pretende migrar, por exemplo: `/user/hive/warehouse` e inicie a migração.

   > [!div class="mx-imgBorder"]
   > ![Criar uma migração de migradores LiveData](./media/migrate-gen2-wandisco-live-data-platform/create-migration.png)

Monitorize o seu progresso de migração através da ferramenta padrão Azure, incluindo o portal Azure CLI e Azure, e continue a utilizar o seu ambiente no local em toda a parte. Antes de começar, reveja estes [pré-requisitos.](https://docs.wandisco.com/live-data-platform/docs/prereq/)

## <a name="replicate-data-under-active-change"></a>Replicar dados sob alteração ativa

As migrações em larga escala dos lagos de dados no local para o Azure precisam de testes e validação de aplicações. Ser capaz de o fazer sem o risco de introduzir alterações de dados que criem múltiplas fontes de verdade que não podem ser facilmente reconciliadas é fundamental para eliminar o risco e minimizar o custo de mudança para Azure. [O LiveData Plane for Azure](https://www.wandisco.com/products/livedata-plane-for-azure) utiliza a tecnologia de motor de coordenação da WANdisco para ultrapassar estas preocupações.

> [!div class="mx-imgBorder"]
> ![Plano LiveData para Arquitetura Azure](./media/migrate-gen2-wandisco-live-data-platform/live-data-plane-architecture.png)

Mantenha os seus dados consistentes em todos os locais dos clusters Hadoop e armazenamento Azure com o Plano LiveData para Azure após a migração inicial:

1. Fornecimento de Plano LiveData para Azure no local e em Azure, a partir do portal Azure. Não são necessárias alterações de candidatura.

2. Configure as regras de replicação que cobrem as localizações de dados que pretende manter consistentes, por exemplo: `/user/contoso/sales/region/WA` .

3. Executar aplicações que acedam e modificam dados em qualquer local como um sistema de ficheiros compatível com Hadoop conforme necessário.

O LiveData Plane for Azure mantém os seus dados consistentes sem impor uma sobrecarga significativa no funcionamento do cluster ou no desempenho da aplicação. Modifique ou ingere dados enquanto todas as alterações são replicadas de forma consistente.

## <a name="next-steps"></a>Passos seguintes

- [A Plataforma LiveData para Azure](https://docs.wandisco.com/live-data-platform/docs/landing/) for Azure é utilizada como qualquer outro recurso Azure, e já se encontra disponível em pré-visualização. 

- Compreenda os [pré-requisitos](https://docs.wandisco.com/live-data-platform/docs/prereq/), planeie a sua migração e complete rapidamente uma migração em larga escala com o LiveData Migrador para o Azure.

- Experimente o Migrador LiveData sem precisar de ter um cluster Hadoop no local utilizando a Caixa de [Areia HDFS](https://docs.wandisco.com/live-data-platform/docs/create-sandbox-intro/).

## <a name="see-also"></a>Ver também

- [Migrador LiveData para Azure no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=Overview)

- [Plano LiveData para Azure no Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=Overview)

- [Migrador LiveData para planos e preços da Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldm?tab=PlansAndPrice)

- [Plano LiveData para planos e preços da Azure](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.ldp?tab=PlansAndPrice) 

- [Plataforma LiveData para Perguntas Frequentes](https://docs.wandisco.com/live-data-platform/docs/faq/)

- [Problemas conhecidos com plataforma LiveData para Azure](https://docs.wandisco.com/live-data-platform/docs/known-issues/)

- [Introdução ao Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)