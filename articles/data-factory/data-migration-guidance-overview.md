---
title: Migrar dados do lago de dados e do armazém de dados para Azure
description: Utilize a Azure Data Factory para migrar dados do seu lago de dados e do armazém de dados para Azure.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/30/2019
ms.openlocfilehash: 4e7421cb8ea4b0c14e4f4b59a688cdb1afe7d462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100367712"
---
# <a name="use-azure-data-factory-to-migrate-data-from-your-data-lake-or-data-warehouse-to-azure"></a>Utilize a Azure Data Factory para migrar dados do seu lago de dados ou armazém de dados para Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Se pretender migrar o seu lago de dados ou o armazém de dados da empresa (EDW) para o Microsoft Azure, considere utilizar a Azure Data Factory. A Azure Data Factory está bem adaptada aos seguintes cenários:

- Grande migração de carga de trabalho de dados do Amazon Simple Storage Service (Amazon S3) ou um sistema de ficheiros distribuídos Hadoop (HDFS) para Azure
- Migração EDW da Oracle Exadata, Netezza, Teradata ou Amazon Redshift para Azure

A Azure Data Factory pode mover petabytes (PB) de dados para a migração de datas lake, e dezenas de terabytes (TB) de dados para a migração de armazéns de dados.

## <a name="why-azure-data-factory-can-be-used-for-data-migration"></a>Por que a Azure Data Factory pode ser usada para migração de dados

- A Azure Data Factory pode facilmente aumentar a quantidade de poder de processamento para mover dados de forma sem servidor com alto desempenho, resiliência e escalabilidade. E só paga pelo que usa. Note também o seguinte: 
  - A Azure Data Factory não tem limitações no volume de dados ou no número de ficheiros.
  - A Azure Data Factory pode utilizar totalmente a sua rede e largura de banda de armazenamento para obter o maior volume de produção de movimento de dados no seu ambiente.
  - A Azure Data Factory utiliza um método pay-as-you-go, para que pague apenas pelo tempo que realmente utiliza para executar a migração de dados para Azure.  
- A Azure Data Factory pode realizar uma carga histórica única e cargas incrementais programadas.
- A Azure Data Factory utiliza o tempo de execução da integração do Azure (IR) para mover dados entre os pontos finais do lago de dados acessíveis ao público e os pontos finais do armazém. Também pode usar o IR auto-hospedado para mover dados para data lake e pontos finais de armazém dentro da Rede Virtual Azure (VNet) ou atrás de uma firewall.
- A Azure Data Factory tem segurança de nível empresarial: Pode utilizar o Instalador do Windows (MSI) ou a Identidade de Serviço para uma integração segura de serviço-a-serviço ou utilizar o Cofre da Chave Azure para gestão de credenciais.
- A Azure Data Factory proporciona uma experiência de autoria sem código e um painel de monitorização rico e incorporado.  

## <a name="online-vs-offline-data-migration"></a>Migração de dados online vs. offline

A Azure Data Factory é uma ferramenta padrão de migração de dados on-line para transferir dados através de uma rede (internet, ER ou VPN). Enquanto que, com a migração de dados offline, os utilizadores enviam fisicamente dispositivos de transferência de dados da sua organização para um Centro de Dados Azure.  

Existem três considerações fundamentais quando se escolhe entre uma abordagem de migração online e offline:  

- Tamanho dos dados a migrar
- Largura de banda de rede
- Janela de migração

Por exemplo, assuma que planeia utilizar a Azure Data Factory para completar a migração de dados dentro de duas semanas (a sua *janela de migração).* Note a linha de corte rosa/azul na tabela seguinte. A célula cor-de-rosa mais baixa para qualquer coluna mostra o tamanho de dados/rede de emparelhamento de largura de banda cuja janela de migração está mais próxima, mas menos de duas semanas. (Qualquer emparelhamento de tamanho/largura de banda numa célula azul tem uma janela de migração online de mais de duas semanas.) 

![online vs. offline ](media/data-migration-guidance-overview/online-offline.png) Esta tabela ajuda-o a determinar se pode cumprir a janela de migração pretendida através da migração online (Azure Data Factory) com base no tamanho dos seus dados e na largura de banda da rede disponível. Se a janela de migração on-line for superior a duas semanas, você vai querer usar a migração offline.

> [!NOTE]
> Ao utilizar a migração on-line, pode alcançar tanto o carregamento de dados históricos como os feeds incrementais de ponta a ponta através de uma única ferramenta.  Através desta abordagem, os seus dados podem ser mantidos sincronizados entre a loja existente e a nova loja durante toda a janela de migração. Isto significa que pode reconstruir a sua lógica ETL na nova loja com dados atualizados.


## <a name="next-steps"></a>Passos seguintes

- [Migrar dados do AWS S3 para o Azure](data-migration-guidance-s3-azure-storage.md)
- [Migrar dados do aglomerado de hadoop no local para Azure](data-migration-guidance-hdfs-azure-storage.md)
- [Migrar dados do servidor Netezza no local para o Azure](data-migration-guidance-netezza-azure-sqldw.md)
