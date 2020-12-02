---
title: Proteger a área de trabalho do Synapse
description: Este artigo irá ensiná-lo a usar funções e controlar o acesso a atividades de controlo e acesso a dados no espaço de trabalho da Synapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 25e191af919c5880045a6c4c7c79b675cf02520e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458721"
---
# <a name="secure-your-synapse-workspace"></a>Proteger a área de trabalho do Synapse 

Este artigo irá ensiná-lo a usar funções e controlar o acesso a atividades de controlo e acesso a dados. Seguindo estas instruções, o controlo de acesso em Azure Synapse Analytics é simplificado. Basta adicionar e remover os utilizadores a um dos três grupos de segurança.

## <a name="overview"></a>Descrição geral

Para garantir um espaço de trabalho da Sinapse, seguirá um padrão de configuração dos seguintes itens:

- Funções azure (como as incorporadas como Proprietário, Contribuinte, etc.)
- Papéis de sinapse – estes papéis são exclusivos da Sinapse e não são baseados em papéis de Azure. Há três destes papéis:
  - Administração do espaço de trabalho da Sinapse
  - Administração Sinapse SQL
  - Faísca Apache para administrador azure Synapse Analytics
- Controlo de acesso para dados em Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Controlo de acesso para bases de dados Synapse SQL e Spark

## <a name="steps-to-secure-a-synapse-workspace"></a>Passos para garantir um espaço de trabalho da Sinapse

Este documento utiliza nomes padrão para simplificar as instruções. Substitua-os por quaisquer nomes à sua escolha.

|Definição | Valor de exemplo | Descrição |
| :------ | :-------------- | :---------- |
| **Espaço de trabalho sinapse** | WS1 |  O nome que o espaço de trabalho da Sinapse terá. |
| **Conta ADLSGEN2** | STG1 | A conta ADLS para usar com o seu espaço de trabalho. |
| **Container** (Contentor) | CNT1 | O recipiente em STG1 que o espaço de trabalho utilizará por defeito. |
| **Inquilino de diretório ativo** | contoso | o nome de inquilino de diretório ativo.|
||||

## <a name="step-1-set-up-security-groups"></a>PASSO 1: Criar grupos de segurança

Crie e povoe três grupos de segurança para o seu espaço de trabalho:

- **WS1 \_ WSAdmins** – para utilizadores que precisam de controlo total sobre o espaço de trabalho
- **WS1 \_ SparkAdmins** – para aqueles utilizadores que precisam de controlo completo sobre os aspetos de Faísca do espaço de trabalho
- **WS1 \_ SQLAdmins** – para utilizadores que precisam de controlo total sobre os aspetos SQL do espaço de trabalho

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>PASSO 2: Prepare a sua conta De Armazenamento de Dados Lake Gen2

Identifique esta informação sobre o seu armazenamento:

- A conta ADLSGEN2 para usar para o seu espaço de trabalho. Este documento chama-lhe STG1.  O STG1 é considerado a conta de armazenamento "primária" para o seu espaço de trabalho.
- O recipiente dentro da WS1 que o seu espaço de trabalho Synapse utilizará por defeito. Este documento chama-lhe CNT1.  Este recipiente é utilizado para:
  - Armazenar os ficheiros de dados de suporte para as tabelas Spark
  - Registos de execução para trabalhos de faísca

- Utilizando o portal Azure, atribua aos grupos de segurança as seguintes funções na CNT1

  - Atribuir **WS1 \_ WSAdmins** à função **de contribuinte de dados** de armazenamento blob
  - Atribuir **WS1 \_ SparkAdmins** à função **de contribuinte de dados** do armazenamento blob
  - Atribuir **WS1 \_ SQLAdmins** à função **de contribuinte de dados** de armazenamento blob

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>PASSO 3: Criar e configurar o seu espaço de trabalho sinapse

 No portal Azure, crie um espaço de trabalho sinapse:

- Selecione a sua subscrição
- Selecione o seu grupo de recursos - Tem de ter acesso a um grupo de recursos para o qual lhe foi atribuída a função **Proprietário.**
- Nomeie o espaço de trabalho WS1
- Escolha STG1 para a conta de Armazenamento -. Escolha CNT1 para o recipiente que está a ser utilizado como "sistema de ficheiros".
- Open WS1 no Estúdio Synapse
- **Selecione Gerir**  >  **o Controlo de Acesso** atribuir os grupos de segurança às seguintes funções de Sinapse.
  - Atribuir **WS1 \_ WSAdmins** aos administradores do Espaço de Trabalho da Sinapse
  - Atribuir **WS1 \_ SparkAdmins** aos administradores da Synapse Spark
  - Atribuir **WS1 \_ SQLAdmins** aos administradores Sinapse SQL

## <a name="step-4-configure-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>PASSO 4: Configurar data lake storage Gen2 para uso pelo espaço de trabalho Synapse

O espaço de trabalho synapse precisa de acesso a STG1 e CNT1 para que possa executar oleodutos e executar tarefas do sistema.

- Abra o portal do Azure
- Localizar STG1
- Navegue até CNT1
- Certifique-se de que o MSI (Identidade de Serviço Gerido) para o WS1 é atribuído à função **de contribuinte de dados** de armazenamento blob na CNT1
  - Se não o vir designado, atribua-o.
  - O MSI tem o mesmo nome que o espaço de trabalho. Neste caso, seria &quot; &quot; WS1.

## <a name="step-5-configure-admin-access-for-synapse-sql"></a>PASSO 5: Configurar acesso a administrador para Synapse SQL

- Abra o portal do Azure
- Navegue até WS1
- Em **Definições**, selecione **sql ative directy administrador**
- Selecione **Definir administração** e escolha WS1 \_ SQLAdmins

## <a name="step-6-maintain-access-control"></a>PASSO 6: Manter o controlo de acesso

A configuração está terminada.

Agora, para gerir o acesso dos utilizadores, pode adicionar e remover os utilizadores aos três grupos de segurança.

Embora possa atribuir manualmente os utilizadores às funções de Sinapse, se o fizer, não configurará as coisas de forma consistente. Em vez disso, basta adicionar ou remover os utilizadores aos grupos de segurança.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>PASSO 7: Verificar o acesso dos utilizadores nas funções

Os utilizadores de cada função precisam de completar os seguintes passos:

| Número | Passo | Administradores de área de trabalho | Administradores de faíscas | Administradores SQL |
| --- | --- | --- | --- | --- |
| 1 | Faça o upload de um ficheiro parquet para a CNT1 | SIM | SIM | SIM |
| 2 | Leia o ficheiro parquet usando a piscina SQL sem servidor | SIM | NO | SIM |
| 3 | Crie uma piscina Apache Spark sem servidor | SIM [1] | SIM [1] | NO  |
| 4 | Lê o arquivo parquet com um Caderno | SIM | SIM | NO |
| 5 | Crie um oleoduto a partir do Caderno e desencadeie o oleoduto para funcionar agora | SIM | NO | NO |
| 6 | Crie uma piscina SQL dedicada e execute um script SQL como &quot; SELECT 1&quot; | SIM [1] | NO | SIM[1] |

> [!NOTE]
> [1] Para criar piscinas SQL ou Spark, o utilizador deve ter pelo menos uma função contributiva no espaço de trabalho da Synapse.
>
 
>[!TIP]
> - Alguns passos deliberadamente não serão permitidos dependendo do papel.
> - Tenha em mente que algumas tarefas podem falhar se a segurança não estiver totalmente configurada. Estas tarefas são anotados na tabela.

## <a name="step-8-network-security"></a>PASSO 8: Segurança da rede

Para configurar a firewall do espaço de trabalho, a rede virtual e [o Private Link.](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-9-completion"></a>PASSO 9: Conclusão

O seu espaço de trabalho está agora totalmente configurado e seguro.

## <a name="how-roles-interact-with-synapse-studio"></a>Como os papéis interagem com o Synapse Studio

O Synapse Studio comportar-se-á de forma diferente com base nas funções dos utilizadores. Alguns itens podem ser escondidos ou desativados se um utilizador não estiver atribuído a funções que dêem o acesso adequado. A tabela seguinte resume o efeito no Synapse Studio.

| Tarefa | Administradores do espaço de trabalho | Administradores de faíscas | Administradores SQL |
| --- | --- | --- | --- |
| Open Synapse Studio | SIM | SIM | SIM |
| Ver Home hub | SIM | SIM | SIM |
| Ver Data Hub | SIM | SIM | SIM |
| Data Hub / Ver contas e contentores ADLS Gen2 ligados | SIM [1] | SIM[1] | SIM[1] |
| Data Hub / Ver Bases de Dados | SIM | SIM | SIM |
| Data Hub / Ver objetos em bases de dados | SIM | SIM | SIM |
| Data Hub / Dados de acesso em bases de dados Sinapse SQL | SIM   | NO   | SIM   |
| Data Hub / Dados de acesso em bases de dados de piscinas SQL sem servidor | SIM [2]  | NO  | SIM [2]  |
| Data Hub / Dados de acesso em bases de dados Spark | SIM [2] | SIM [2] | SIM [2] |
| Use o centro de desenvolvimento | SIM | SIM | SIM |
| Desenvolver scripts SQL hub / autor | SIM | NO | SIM |
| Desenvolver Hub / autor Spark Job Definições | SIM | SIM | NO |
| Desenvolver Blocos de Notas / Autor | SIM | SIM | NO |
| Desenvolver fluxos de dados hub /autor | SIM | NO | NO |
| Use o hub Orchestrate | SIM | SIM | SIM |
| Orquestrar hub / usar Pipelines | SIM | NO | NO |
| Utilize o Hub de Gestão | SIM | SIM | SIM |
| Gerir hub / Synapse SQL | SIM | NO | SIM |
| Gerir piscinas Hub / Spark | SIM | SIM | NO |
| Gerir hub / Gatilhos | SIM | NO | NO |
| Gerir o Hub / Serviços ligados | SIM | SIM | SIM |
| Gerir o Hub / Controlo de Acesso (atribuir aos utilizadores as funções de espaço de trabalho sinapse) | SIM | NO | NO |
| Gerir os tempos de funcionação do Hub / Integração | SIM | SIM | SIM |
| Utilize o Monitor Hub | SIM | SIM | SIM |
| Monitor Hub / Orquestração / Pipeline corre  | SIM | NO | NO |
| Monitor Hub / Orquestração / Gatilho corre  | SIM | NO | NO |
| Monitor Hub / Orquestração / Tempos de integração  | SIM | SIM | SIM |
| Monitor Hub / Atividades / Aplicações de faíscas | SIM | SIM | NO  |
| Monitor Hub / Atividades / SQL pedidos | SIM | NO | SIM |
| Monitor Hub / Atividades / Piscinas de faíscas | SIM | SIM | NO  |
| Monitor Hub / Gatilhos | SIM | NO | NO |
| Gerir o Hub / Serviços ligados | SIM | SIM | SIM |
| Gerir o Hub / Controlo de Acesso (atribuir aos utilizadores as funções de espaço de trabalho sinapse) | SIM | NO | NO |
| Gerir os tempos de funcionação do Hub / Integração | SIM | SIM | SIM |


> [!NOTE]
> [1] O acesso aos dados em contentores depende do controlo de acesso na ADLS Gen2. </br>
> [2] As tabelas SQL OD e as tabelas Spark armazenam os seus dados na ADLS Gen2 e o acesso requer as permissões adequadas na ADLS Gen2.

## <a name="next-steps"></a>Passos seguintes

Criar um [espaço de trabalho sinapse](../quickstart-create-workspace.md)
