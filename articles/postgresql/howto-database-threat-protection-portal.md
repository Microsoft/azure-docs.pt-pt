---
title: Utilizar proteção avançada de ameaças - Base de dados Azure para PostgreSQL - Servidor Único
description: A Proteção de Ameaças deteta atividades anómalas de bases de dados que indiquem potenciais ameaças à segurança na base de dados.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 5583e8423f0909936d9e55c6d87593835eded8f7
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489902"
---
# <a name="advanced-threat-protection-for-azure-database-for-postgresql---single-server"></a>Proteção avançada de ameaças para base de dados de Azure para PostgreSQL - Servidor Único

O Advanced Threat Protection da Base de Dados do Azure para PostgreSQL deteta atividades anómalas que indicam tentativas potencialmente perigosas e invulgares de acesso ou exploração das bases de dados.

A Advanced Threat Protection faz parte da oferta avançada de Segurança de Dados, que é um pacote unificado para capacidades avançadas de segurança. A Advanced Threat Protection pode ser acedida e gerida através do [portal Azure](https://portal.azure.com) e encontra-se atualmente em pré-visualização.

> [!NOTE]
> A funcionalidade de Proteção de Ameaças **Avançadas não** está disponível nas seguintes regiões de nuvens governamentais e soberanas: EUA Gov Texas, EUA Gov Arizona, US Gov Iowa, EUA, Gov Virginia, US DoD East, US DoD Central, Germany Central, Germany North, China East, China East 2. Visite [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/) para disponibilidade geral de produtos.
>

> [!NOTE]
> Esta funcionalidade está disponível em todas as regiões do Azure onde a Base de Dados Azure para PostgreSQL é implantada para servidores otimizados para fins gerais e memória.

## <a name="set-up-threat-detection"></a>Configurar a deteção de ameaças
1. Lançar o portal Azure em [https://portal.azure.com](https://portal.azure.com) .
2. Navegue para a página de configuração da Base de Dados Azure para o servidor PostgreSQL que pretende proteger. Nas definições de segurança, selecione **Advanced Threat Protection (Preview)**.
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

Advanced Threat Protection integra os seus alertas com [o Azure Security Center](https://azure.microsoft.com/services/security-center/). 

Clique em **alertas de segurança** no âmbito **da PROTEÇÃO DE AMEAÇAS** para lançar a página de alertas do Azure Security Center e obter uma visão geral das ameaças de SQL ativas detetadas na base de dados.

  :::image type="content" source="./media/howto-database-threat-protection-portal/threat-detection-alert-asc.png" alt-text="Configurar a deteção de ameaças":::

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o Azure Security Center](../security-center/security-center-introduction.md)
* Para obter mais informações sobre preços, consulte a [Base de Dados Azure para a página de preços pós-SQL](https://azure.microsoft.com/pricing/details/postgresql/)