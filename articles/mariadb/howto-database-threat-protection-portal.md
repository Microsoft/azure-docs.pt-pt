---
title: Proteção avançada de ameaças - Portal Azure - Base de Dados Azure para MariaDB
description: A Proteção contra ameaças deteta atividades anómalas na base de dados que indicam potenciais ameaças à segurança na base de dados.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: de056f61337311642589ba937c6d9842d8354d36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530143"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Proteção avançada de ameaças para base de dados Azure para MariaDB

Advanced Threat Protection for Azure Database for MariaDB deteta atividades anómalas que indicam tentativas incomuns e potencialmente nocivas de acesso ou exploração de bases de dados.

A Advanced Threat Protection faz parte da oferta avançada de segurança de dados, que é um pacote unificado para capacidades de segurança avançadas. A Proteção Avançada de Ameaças pode ser acedida e gerida através do [portal Azure.](https://portal.azure.com)

> [!IMPORTANT]
> A Proteção Avançada de Ameaças está em pré-visualização pública. Esta funcionalidade está disponível em todas as regiões do Azure onde a Base de Dados Azure para MariaDB está implantada para servidores otimizados para Fins Gerais e Memória.

> [!NOTE]
> A funcionalidade de Proteção contra Ameaças **Avançadas não** está disponível nas seguintes regiões do governo azure e das nuvens soberanas: EUA Gov Texas, US Gov Arizona, US Gov Iowa, EUA, Gov Virginia, US DoD East, US DoD Central, Alemanha Central, Alemanha Norte, China Leste, China East 2. Visite [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para a disponibilidade geral do produto.

## <a name="set-up-threat-detection"></a>Configurar a deteção de ameaças
1. Lance o portal [https://portal.azure.com](https://portal.azure.com)Azure em .
2. Navegue para a página de configuração da Base de Dados Azure para o servidor MariaDB que pretende proteger. Nas definições de segurança, selecione **Advanced Threat Protection (Pré-visualização)**.
3. Na página de configuração de **Proteção de Ameaças Avançadas (Pré-visualização):**

   - Ativar a Proteção avançada de ameaças no servidor.
   - Em **Definições Avançadas de Proteção contra Ameaças,** nos **alertas enviar para** caixa de texto, forneça a lista de e-mails para receber alertas de segurança após a deteção de atividades anómalas na base de dados.
  
   ![Configurar a deteção de ameaças](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Explore atividades anómalas de base de dados

Recebe uma notificação por e-mail após a deteção de atividades anómalas na base de dados. O e-mail fornece informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anómalas, nome da base de dados, nome do servidor, nome da aplicação e a hora do evento. Além disso, o e-mail fornece informações sobre possíveis causas e ações recomendadas para investigar e mitigar a ameaça potencial para a base de dados.
 
1. Clique no link de **alertas recentes no** e-mail para lançar o portal Azure e mostrar a página de alertas do Centro de Segurança Azure, que fornece uma visão geral das ameaças ativas detetadas na base de dados SQL.
    
    ![Relatório de atividadeanómala](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Ver ameaças ativas:

    ![Ameaças ativas](./media/howto-database-threat-protection-portal/active-threats.png)

2. Clique num alerta específico para obter detalhes e ações adicionais para investigar esta ameaça e remediar ameaças futuras.
    
    ![Alerta específico](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Explore alertas de deteção de ameaças

A Deteção de Ameaças de Base de Dados SQL integra os seus alertas com o [Azure Security Center](https://azure.microsoft.com/services/security-center/). Um sql vivo de deteção de ameaças dentro da base de dados e lâminas ATP SQL no portal Azure rastreia o estado das ameaças ativas.

Clique em alerta de **deteção** de ameaças para lançar a página de alertas do Azure Security Center e obter uma visão geral das ameaças sQL ativas detetadas na base de dados.

   ![Alerta de deteção de ameaças](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Centro de Segurança Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para mais informações sobre preços, consulte a Base de [Dados Azure para a página de Preços MariaDB](https://azure.microsoft.com/pricing/details/mariadb/)  
