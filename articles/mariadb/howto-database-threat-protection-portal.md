---
title: Proteção avançada contra ameaças-banco de dados do Azure para MariaDB | Microsoft Docs
description: A proteção contra ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: f77d9f105d5c2ff10753f2b4b1ecc962c84eee6e
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869654"
---
# <a name="advanced-threat-protection-for-azure-database-for-mariadb"></a>Proteção avançada contra ameaças para o banco de dados do Azure para MariaDB

A proteção avançada contra ameaças para o banco de dados do Azure para MariaDB detecta atividades anormais que indicam tentativas incomuns e potencialmente prejudiciais de acessar ou explorar bancos de dados.

A proteção avançada contra ameaças faz parte da oferta de segurança de dados avançada, que é um pacote unificado para recursos avançados de segurança. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do [portal do Azure](https://portal.azure.com).

> [!IMPORTANT]
> A proteção avançada contra ameaças está em visualização pública. Esse recurso está disponível em todas as regiões do Azure em que o banco de dados do Azure para MariaDB é implantado para servidores Uso Geral e com otimização de memória.

> [!NOTE]
> O recurso proteção avançada contra ameaças **não** está disponível nas seguintes regiões do Azure governamental e do soberanas Cloud: US Gov Texas, US Gov Arizona, US Gov Iowa, US, gov Virgínia, US DoD Leste, US DoD Central, Alemanha central, Norte da Alemanha, Leste da China, Leste da China 2. Visite os [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade geral do produto.

## <a name="set-up-threat-detection"></a>Configurar a detecção de ameaças
1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a página de configuração do banco de dados do Azure para o servidor MariaDB que você deseja proteger. Em configurações de segurança, selecione **proteção contra ameaças avançadas (versão prévia)** .
3. Na página de configuração **proteção contra ameaças avançadas (versão prévia)** :

   - Habilite a proteção avançada contra ameaças no servidor.
   - Em **Configurações avançadas de proteção contra ameaças**, na caixa de texto **enviar alertas para** , forneça a lista de emails para receber alertas de segurança após a detecção de atividades anormais do banco de dados.
  
   ![Configurar a detecção de ameaças](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Explorar atividades anormais de banco de dados

Você recebe uma notificação por email após a detecção de atividades anormais do banco de dados. O email fornece informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anormais, o nome do banco de dados, o nome do servidor, o nome do aplicativo e a hora do evento. Além disso, o email fornece informações sobre possíveis causas e ações recomendadas para investigar e atenuar a ameaça em potencial para o banco de dados.
 
1. Clique no link **exibir alertas recentes** no email para iniciar o portal do Azure e mostrar a página de alertas da central de segurança do Azure, que fornece uma visão geral das ameaças ativas detectadas no banco de dados SQL.
    
    ![Relatório de atividade anômala](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Exibir ameaças ativas:

    ![Ameaças ativas](./media/howto-database-threat-protection-portal/active-threats.png)

2. Clique em um alerta específico para obter detalhes adicionais e ações para investigar essa ameaça e corrigir futuras ameaças.
    
    ![Alerta específico](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Explorar alertas de detecção de ameaças

A detecção de ameaças do banco de dados SQL integra seus alertas com a [central de segurança do Azure](https://azure.microsoft.com/services/security-center/). Os blocos de detecção de ameaças do SQL ao vivo no banco de dados e nas folhas do SQL ATP no portal do Azure acompanham o status das ameaças ativas.

Clique em **alerta de detecção de ameaças** para iniciar a página de alertas da central de segurança do Azure e obtenha uma visão geral das ameaças do SQL ativas detectadas no banco de dados.

   ![Alerta de detecção de ameaças](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)
   

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para obter mais informações sobre preços, consulte a [página de preços do banco de dados do Azure para MariaDB](https://azure.microsoft.com/pricing/details/mariadb/)  
