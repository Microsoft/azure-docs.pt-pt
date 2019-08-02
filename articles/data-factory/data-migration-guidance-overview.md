---
title: Use Azure Data Factory para migrar dados de seu data Lake e data warehouse para o Azure | Microsoft Docs
description: Use Azure Data Factory para migrar dados de seu data Lake e data warehouse para o Azure.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 7/30/2019
ms.openlocfilehash: 780b9ae6e4664af86fa655c9136193bed58526d9
ms.sourcegitcommit: 6ad03fa28a0f60cb6dce6144f728c2ceb56ff6e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68708501"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Use Azure Data Factory para migrar dados de seu data Lake ou data warehouse para o Azure 

Azure Data Factory pode ser a ferramenta para fazer a migração de dados quando você deseja migrar seu data Lake ou Enterprise data warehouse (EDW) para o Azure. A migração do data Lake e a migração de data warehouse estão relacionadas aos seguintes cenários: 

- Migração de carga de trabalho de Big data do AWS S3, sistema de arquivos do Hadoop local para o Azure. 
- EDW migração do Oracle Exadata, Netezza, Teradata, AWS redshift para o Azure. 

Azure Data Factory pode mover o nível de dados do PBs para migração do data Lake e dezenas de TB de dados para data warehouse migração. 

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Por que Azure Data Factory pode ser usado para a migração de dados 

- Azure Data Factory pode facilmente escalar verticalmente a quantidade de potência para mover dados de maneira sem servidor com alto desempenho, resiliência e escalabilidade e pagar apenas pelo que usar.  
  - Azure Data Factory não tem nenhuma limitação sobre o volume de dados e o número de arquivos.
  - Azure Data Factory pode 100% utilizar sua largura de banda de rede e armazenamento para obter a maior taxa de transferência de dados em seu ambiente.   
  - Azure Data Factory segue a estratégia pago pelo uso, para que você só precise pagar pelo tempo quando estiver usando Azure Data Factory para fazer a migração de dados para o Azure.  
- Azure Data Factory tem a capacidade de executar uma carga histórica única, bem como a carga incremental agendada. 
- O Azure Data Factory usa Azure IR para mover dados entre pontos de extremidade do data Lake/warehouse publicamente acessíveis ou, alternativamente, usar o IR auto-hospedado para mover dados para pontos de extremidade do data Lake/warehouse dentro da VNet ou atrás do firewall. 
- Azure Data Factory tem segurança de nível empresarial: Use a identidade de serviço ou MSI para integração segura de serviço a serviço ou aproveite a Azure Key Vault para o gerenciamento de credenciais. 
- O Azure Data Factory fornece uma experiência de criação sem código e um painel de monitoramento interno avançado.  

## <a name="online-vs-offline-data-migration"></a>Online versus migração de dados offline

O Azure Data Factory é uma ferramenta de migração de dados online típica para transferir dados pela rede (Internet, ER ou VPN), em que a migração de dados offline está permitindo que as pessoas enviem fisicamente os dispositivos de transferência de dados de sua organização para o Data Center do Azure.  

Há três considerações importantes ao selecionar a abordagem de migração online versus offline:  

- Tamanho dos dados a serem migrados. 
- Largura de banda da rede. 
- Janela de migração.   

Se você quiser concluir a migração de dados dentro de duas semanas (janela de migração), poderá ver uma linha recortar na imagem abaixo para mostrar quando é bom usar a ferramenta de migração online (Azure Data Factory) com tamanho de dados e largura de banda de rede diferentes.   

![online versus offline](media/data-migration-guidance-overview/online-offline.png)

> [!NOTE]
> O benefício da abordagem de migração online é que você pode obter o carregamento de dados históricos e os feeds incrementais de ponta a ponta em uma ferramenta.  Ao fazer isso, os dados podem ser mantidos sincronizados entre os repositórios novos e existentes durante toda a janela de migração para que você possa recriar sua lógica de ETL na nova loja com dados atualizados. 


## <a name="next-steps"></a>Passos Seguintes

- [Copiar arquivos de vários contêineres com Azure Data Factory](solution-template-copy-files-multiple-containers.md)