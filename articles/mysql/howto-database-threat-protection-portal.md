---
title: Proteção Avançada de Ameaças - Portal Azure - Base de Dados Azure para o MySQL
description: Saiba como configurar a Advanced Threat Protection para detetar atividades anómalas de bases de dados que indiquem potenciais ameaças de segurança à base de dados.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 96f6993f92192a1149e3b243c68fdf5fe96fdc66
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541456"
---
# <a name="advanced-threat-protection-for-azure-database-for-mysql"></a>Proteção Avançada de Ameaças para Base de Dados Azure para O MySQL

A Advanced Threat Protection para Base de Dados do Azure para MySQL deteta atividades anómalas que indicam tentativas potencialmente perigosas e invulgares para aceder ou explorar as suas bases de dados.

A Advanced Threat Protection faz parte da oferta avançada de Segurança de Dados, que é um pacote unificado para capacidades avançadas de segurança. A Advanced Threat Protection pode ser acedida e gerida através do [portal Azure](https://portal.azure.com) e encontra-se atualmente em pré-visualização.

> [!NOTE]
> A funcionalidade de Proteção de Ameaças **Avançadas não** está disponível nas seguintes regiões de nuvens governamentais e soberanas: EUA Gov Texas, EUA Gov Arizona, US Gov Iowa, EUA, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Visite [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade geral de produtos.
>

> [!NOTE]
> Esta funcionalidade encontra-se disponível em todas as regiões do Azure onde a Base de Dados Azure para o MySQL é implantada para servidores otimizados para fins gerais e memória.

## <a name="set-up-threat-detection"></a>Configurar a deteção de ameaças
1. Lançar o portal Azure em [https://portal.azure.com](https://portal.azure.com) .
2. Navegue para a página de configuração da Base de Dados Azure para o servidor MySQL que pretende proteger. Nas definições de segurança, selecione **Advanced Threat Protection (Preview)** .
3. Na página de configuração **da Proteção de Ameaças Avançadas (Pré-visualização):**

   - Ativar a Proteção Avançada de Ameaças no servidor.
   - Nas **Definições avançadas de proteção contra ameaças,** nos **alertas enviar para a** caixa de texto, forneça a lista de e-mails para receber alertas de segurança após a deteção de atividades de base de dados anómalas.
  
   :::image type="content" source="./media/howto-database-threat-protection-portal/set-up-threat-protection.png" alt-text="Configurar a deteção de ameaças":::

## <a name="explore-anomalous-database-activities"></a>Explore atividades anómalas de bases de dados

Você recebe uma notificação por e-mail após a deteção de atividades de base de dados anómalas. O e-mail fornece informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anómalas, nome da base de dados, nome do servidor, nome da aplicação e hora do evento. Além disso, o e-mail fornece informações sobre possíveis causas e ações recomendadas para investigar e mitigar a potencial ameaça à base de dados.
 
1. Clique no link **de alertas recentes** do Ver no e-mail para lançar o portal Azure e mostrar a página de alertas do Azure Security Center, que fornece uma visão geral das ameaças ativas detetadas na base de dados SQL.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/anomalous-activity-report.png" alt-text="Configurar a deteção de ameaças":::

    Ver ameaças ativas:

    :::image type="content" source="./media/howto-database-threat-protection-portal/active-threats.png" alt-text="Configurar a deteção de ameaças":::

2. Clique num alerta específico para obter detalhes e ações adicionais para investigar esta ameaça e remediar ameaças futuras.
    
    :::image type="content" source="./media/howto-database-threat-protection-portal/specific-alert.png" alt-text="Configurar a deteção de ameaças":::

## <a name="explore-threat-detection-alerts"></a>Explore alertas de deteção de ameaças

A Deteção de Ameaças de Base de Dados SQL integra os seus alertas com [o Azure Security Center](https://azure.microsoft.com/services/security-center/). Um azulejo de deteção de ameaças SQL ao vivo rastreia o estado de ameaças ativas na base de dados e páginas ATP SQL no portal Azure.

Clique **em alerta de deteção de ameaças** para lançar a página de alertas do Azure Security Center e obter uma visão geral das ameaças de SQL ativas detetadas na base de dados.

   :::image type="content" source="./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png" alt-text="Configurar a deteção de ameaças":::
   

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Azure Security Center](../security-center/security-center-introduction.md)
* Para obter mais informações sobre preços, consulte a [Base de Dados Azure para a página de preços mySQL](https://azure.microsoft.com/pricing/details/mysql/)