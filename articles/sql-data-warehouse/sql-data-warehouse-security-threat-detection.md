---
title: Deteção de ameaças - Azure SQL Data Warehouse | Documentos da Microsoft
description: Configurar a deteção de ameaças e explorar eventos suspeitos no Azure SQL Data Warehouse.
services: sql-data-warehouse
author: KavithaJonnakuti
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/17/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: a3204c2705ba7b83c4fe22ab6bdd15c11eeeeda5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182443"
---
# <a name="threat-detection-in-azure-sql-data-warehouse"></a>Deteção de ameaças do Azure SQL Data Warehouse
Configurar a deteção de ameaças e explorar eventos suspeitos no Azure SQL Data Warehouse.

## <a name="what-is-threat-detection"></a>O que é a deteção de ameaças
Deteção de ameaças Deteta atividades anómalas da base de dados, indicando potenciais ameaças de segurança para a base de dados. 

A Deteção de Ameaças oferece uma nova camada de segurança, o que permite aos clientes detetar e responder a potenciais ameaças à medida que estas ocorrem, ao fornecer alertas de segurança relativamente a atividades anómalas. Os utilizadores podem explorar os eventos suspeitos com [auditoria do Azure SQL Data Warehouse](sql-data-warehouse-auditing-overview.md) para determinar se estes resultam de uma tentativa de aceder, violar ou explorar dados no armazém de dados.
Deteção de ameaças facilita lidar com potenciais ameaças ao armazém de dados sem a necessidade de ser um especialista em segurança ou gerir sistemas de monitorização de segurança avançados.

Por exemplo, a Deteção de Ameaças deteta determinadas atividades anómalas da base de dados que indiciem potenciais tentativas de injeção de SQL. A injeção de SQL é um dos problemas comuns de segurança das aplicações Web na Internet, sendo utilizada para atacar aplicações condicionadas por dados. Os atacantes tiram partido das vulnerabilidades das aplicações para injetar instruções SQL maliciosas nos campos de entrada das aplicações, com o objetivo de violar ou modificar os dados contidos na base de dados.

## <a name="set-up-threat-detection-for-your-database"></a>Configurar a deteção de ameaças da base de dados
1. Iniciar o Portal do Azure no [ https://portal.azure.com ](https://portal.azure.com).
2. Navegue para o painel de configuração do SQL Data Warehouse que pretende monitorizar. No painel Definições, selecione **Auditoria e Deteção de Ameaças**.
   
    ![Painel de navegação](media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png)
3. Na **deteção de ameaças e auditoria** mão do painel de configuração **ON** auditoria, o que apresentará as definições de deteção de ameaças.
   
    ![Painel de navegação](media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png)
4. Ative **ON** deteção de ameaças.
5. Configure a lista de mensagens de e-mail que irá receber alertas de segurança após a deteção de atividades de armazém de dados anómalas.
6. Clique em **salvar** no **deteção de ameaças e auditoria** painel de configuração para guardar a auditoria nova ou atualizada e política de deteção de ameaças.
   
    ![Painel de navegação](media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png)

## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Explore as atividades de armazém de dados anómalas após a deteção de um evento suspeita
1. Receberá uma notificação por e-mail após a deteção de atividades anómalas da base de dados. <br/>
   A mensagem de e-mail fornecerá informações sobre o evento de segurança suspeito, incluindo a natureza das atividades anómalas, o nome da base de dados, o nome do servidor e a hora do evento. Além disso, fornecerá informações sobre as causas possíveis e as ações recomendadas para investigar e atenuar a potencial ameaça à base de dados.<br/>
   
    ![Painel de navegação](media/sql-data-warehouse-security-threat-detection/4_td_email.png)
2. No e-mail, clique nas **registo de auditoria de SQL do Azure** ligar, que irá iniciar o portal do Azure e mostrar os relevantes registos de auditoria por volta da hora do evento suspeito.
   
    ![Painel de navegação](media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png)
3. Clique nos registos de auditoria para ver mais detalhes sobre as atividades suspeitas da base de dados, tais como a instrução SQL, IP de cliente e o motivo da falha.
   
    ![Painel de navegação](media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png)
4. No painel Registos de Auditoria, clique em **Abrir no Excel** de modo a abrir um modelo do Excel pré-configurado para importar e executar uma análise mais aprofundada do registo de auditoria por volta da hora do evento suspeito.<br/>
   **Nota:** No Excel 2010 ou posterior, o Power Query e o **combinação rápida** definição é necessária
   
    ![Painel de navegação](media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png)
5. Para configurar a definição **Combinação Rápida**: no separador **POWER QUERY** do friso, selecione **Opções** para apresentar a caixa de diálogo Opções. Selecione a secção Privacidade e escolha a segunda opção, "Ignorar os Níveis de Privacidade e melhorar potencialmente o desempenho":
   
    ![Painel de navegação](media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png)
6. Para carregar os registos de auditoria de SQL, certifique-se de que os parâmetros no separador de definições estão especificados corretamente e, em seguida, selecione o friso "Dados" e clique no botão "Atualizar Tudo".
   
    ![Painel de navegação](media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png)
7. Os resultados são apresentados na folha **Registos de Auditoria de SQL**, o que lhe permite executar uma análise mais aprofundada das atividades anómalas detetadas e atenuar o impacto do evento de segurança na aplicação.

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações de segurança, consulte [proteger um data warehouse](sql-data-warehouse-overview-manage-security.md).
