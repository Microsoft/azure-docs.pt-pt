---
title: Usar a proteção avançada contra ameaças-banco de dados do Azure para PostgreSQL-servidor único
description: A proteção contra ameaças detecta atividades anormais de banco de dados que indicam possíveis ameaças de segurança ao banco de dados.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 8b7f52ea318432e97a450a54526f6481b14139c9
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74776148"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql---single-server"></a>Proteção avançada contra ameaças para o banco de dados do Azure para PostgreSQL-servidor único

A Proteção Avançada Contra Ameaças para Base de Dados do Azure para PostgreSQL deteta atividades anómalas que indicam tentativas potencialmente perigosas e invulgares para aceder ou explorar as suas bases de dados.

A proteção avançada contra ameaças faz parte da oferta de segurança de dados avançada, que é um pacote unificado para recursos avançados de segurança. A proteção avançada contra ameaças pode ser acessada e gerenciada por meio do [portal do Azure](https://portal.azure.com) e está atualmente em versão prévia.

> [!NOTE]
> O recurso proteção avançada contra ameaças **não** está disponível nas seguintes regiões do Azure governamental e do soberanas cloud: US gov Texas, US gov Arizona, US gov Iowa, US, gov virgínia, US DoD Leste, US DoD central, Alemanha Central, Norte da Alemanha, Leste da China, leste da China 2. Visite os [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade geral do produto.
>

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure em que o banco de dados do Azure para PostgreSQL é implantado para servidores Uso Geral e com otimização de memória.

## <a name="set-up-threat-detection"></a>Configurar a detecção de ameaças
1. Inicie o portal do Azure em [https://portal.azure.com](https://portal.azure.com).
2. Navegue até a página de configuração do servidor do banco de dados do Azure para PostgreSQL que você deseja proteger. Em configurações de segurança, selecione **proteção contra ameaças avançadas (versão prévia)** .
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

A proteção avançada contra ameaças integra seus alertas à [central de segurança do Azure](https://azure.microsoft.com/services/security-center/). 

Clique em **alertas de segurança** em **proteção contra ameaças** para iniciar a página de alertas da central de segurança do Azure e obtenha uma visão geral das ameaças do SQL ativas detectadas no banco de dados.

  ![Proteção contra ameaças ASC](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para obter mais informações sobre preços, consulte a [página de preços do banco de dados do Azure para PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/)  
