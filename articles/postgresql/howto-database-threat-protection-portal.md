---
title: Proteção avançada contra ameaças - base de dados do Azure para PostgreSQL
description: Proteção contra ameaças Deteta atividades anómalas da base de dados, indicando potenciais ameaças de segurança para a base de dados.
author: bolzmj
ms.author: mbolz
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2019
ms.openlocfilehash: bbb48708d0e5a7cd07a3971a6966f40696107562
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60421338"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql"></a>Proteção avançada contra ameaças para a base de dados do Azure para PostgreSQL

A Proteção Avançada Contra Ameaças para Base de Dados do Azure para PostgreSQL deteta atividades anómalas que indicam tentativas potencialmente perigosas e invulgares para aceder ou explorar as suas bases de dados.

Proteção avançada contra ameaças faz parte da oferta de segurança de dados avançada, que é um pacote unificado para os recursos de segurança avançada. Proteção avançada contra ameaças podem ser acessada e gerenciada através da [portal do Azure](https://portal.azure.com) e está atualmente em pré-visualização.

> [!NOTE]
> A funcionalidade de proteção avançada contra ameaças está **não** disponíveis nas regiões de cloud soberana e seguir o Azure government: US Gov Texas, EUA Gov Arizona, US Gov Iowa, US, Gov Virginia, US DoD East, US DoD Central, Alemanha Central, Alemanha norte, leste da China, leste da China 2. Visite [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade geral do produto.
>

> [!NOTE]
> Esta funcionalidade está disponível em todas as regiões do Azure em que a base de dados do Azure para PostgreSQL é implementada para os servidores de fins gerais e memória otimizada.

## <a name="set-up-threat-detection"></a>Configurar a deteção de ameaças
1. Iniciar o portal do Azure no [ https://portal.azure.com ](https://portal.azure.com).
2. Navegue para a página de configuração da base de dados do Azure para o servidor PostgreSQL que pretende proteger. Nas definições de segurança, selecione **Advanced Threat Protection (pré-visualização)**.
3. Sobre o **Advanced Threat Protection (pré-visualização)** página de configuração:

   - Ative a proteção avançada contra ameaças no servidor.
   - Na **definições de proteção de ameaças avançadas**, na **enviar alertas para** texto caixa, forneça a lista de e-mails para receber alertas de segurança após a deteção de atividades anómalas da base de dados.
  
   ![Configurar a deteção de ameaças](./media/howto-database-threat-protection-portal/set-up-threat-protection.png)

## <a name="explore-anomalous-database-activities"></a>Explore a atividades anómalas da base de dados

Receber uma notificação por e-mail após a deteção de atividades anómalas da base de dados. O e-mail fornece informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anómalas, o nome de base de dados, nome do servidor, o nome da aplicação e a hora do evento. Além disso, o e-mail fornece informações sobre as causas possíveis e as ações recomendadas para investigar e mitigar a potencial ameaça à base de dados.
    
1. Clique nas **ver alertas recentes** ligação no e-mail para iniciar o portal do Azure e mostrar a página de alertas do Centro de segurança do Azure, que fornece uma visão geral do Active Directory ameaças detetadas na base de dados SQL.
    
    ![Relatório de atividade anómala](./media/howto-database-threat-protection-portal/anomalous-activity-report.png)

    Ameaças ativas de modo de exibição:

    ![Ameaças ativas](./media/howto-database-threat-protection-portal/active-threats.png)

2. Clique num alerta específico para obter detalhes adicionais e ações para esta ameaça a investigar e remediar ameaças futuras.
    
    ![Alerta específico](./media/howto-database-threat-protection-portal/specific-alert.png)

## <a name="explore-threat-detection-alerts"></a>Explore os alertas de deteção de ameaças

Proteção avançada contra ameaças integra seus alertas no [Centro de segurança do Azure](https://azure.microsoft.com/services/security-center/). 

Clique em **alertas de segurança** sob **PROTEÇÃO contra ameaças** para iniciar o Centro de segurança do Azure a alertas de página e obter uma visão geral do Active Directory SQL ameaças detetadas na base de dados.

  ![Asc de proteção de ameaças](./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png)

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro)
* Para obter mais informações sobre os preços, consulte o [base de dados do Azure para a página de preços do PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/)  
