---
title: Proteção Avançada de Ameaças - Portal Azure - Base de Dados Azure para MariaDB
description: A Proteção de Ameaças para a Base de Dados de Azure para MariaDB deteta atividades anómalas de bases de dados que indiquem potenciais ameaças à segurança na base de dados.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 1b5a16a014892764ee26b524489a82744fe6ca82
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425961"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Proteção Avançada de Ameaças para Base de Dados de Azure para MariaDB

A Advanced Threat Protection for Azure Database for MariaDB deteta atividades anómalas que indiquem tentativas invulgares e potencialmente nocivas de aceder ou explorar bases de dados.

A Advanced Threat Protection faz parte da oferta avançada de Segurança de Dados, que é um pacote unificado para capacidades avançadas de segurança. A Advanced Threat Protection pode ser acedida e gerida através do [portal Azure](https://portal.azure.com).

> [!IMPORTANT]
> A Proteção Avançada de Ameaças está em visualização pública. Esta funcionalidade encontra-se disponível em todas as regiões do Azure onde a Base de Dados Azure para MariaDB é implantada para servidores otimizados para fins gerais e memória.

> [!NOTE]
> A funcionalidade de Proteção de Ameaças **Avançadas não** está disponível nas seguintes regiões de nuvens governamentais e soberanas: EUA Gov Texas, EUA Gov Arizona, US Gov Iowa, EUA, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Visite [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade geral de produtos.

## <a name="set-up-threat-detection"></a>Configurar a deteção de ameaças
1. Lançar o portal Azure em [https://portal.azure.com](https://portal.azure.com) .
2. Navegue para a página de configuração da Base de Dados Azure para o servidor MariaDB que pretende proteger. Nas definições de segurança, selecione **Advanced Threat Protection (Preview)**.
3. Na página de configuração **da Proteção de Ameaças Avançadas (Pré-visualização):**

   - Ativar a Proteção Avançada de Ameaças no servidor.
   - Nas **Definições avançadas de proteção contra ameaças,** nos **alertas enviar para a** caixa de texto, forneça a lista de e-mails para receber alertas de segurança após a deteção de atividades de base de dados anómalas.
  
   ![Configurar a deteção de ameaças](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Explore atividades anómalas de bases de dados

Você recebe uma notificação por e-mail após a deteção de atividades de base de dados anómalas. O e-mail fornece informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anómalas, nome da base de dados, nome do servidor, nome da aplicação e hora do evento. Além disso, o e-mail fornece informações sobre possíveis causas e ações recomendadas para investigar e mitigar a potencial ameaça à base de dados.
 
1. Clique no link **de alertas recentes** do Ver no e-mail para lançar o portal Azure e mostrar a página de alertas do Azure Security Center, que fornece uma visão geral das ameaças ativas detetadas na base de dados SQL.
    
    ![Relatório de atividades anómalas](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Ver ameaças ativas:

    ![Ameaças ativas](./media/howto-database-threat-protection-portal/active-threats.png)

2. Clique num alerta específico para obter detalhes e ações adicionais para investigar esta ameaça e remediar ameaças futuras.
    
    ![Alerta específico](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Explore alertas de deteção de ameaças

A Deteção de Ameaças de Base de Dados SQL integra os seus alertas com [o Azure Security Center](https://azure.microsoft.com/services/security-center/). Uma telha de deteção de ameaças SQL viva dentro da base de dados e lâminas ATP SQL no portal Azure rastreia o estado de ameaças ativas.

Clique **em alerta de deteção de ameaças** para lançar a página de alertas do Azure Security Center e obter uma visão geral das ameaças de SQL ativas detetadas na base de dados.

   ![Alerta de deteção de ameaças](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Azure Security Center](../security-center/security-center-introduction.md)
* Para obter mais informações sobre preços, consulte a [Base de Dados Azure para a página de preços da MariaDB](https://azure.microsoft.com/pricing/details/mariadb/)