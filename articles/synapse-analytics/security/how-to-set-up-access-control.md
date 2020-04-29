---
title: Proteja o seu espaço de trabalho Synapse (pré-visualização)
description: Este artigo irá ensiná-lo a usar papéis e controlo de acesso para controlar atividades e acesso a dados no espaço de trabalho synapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: ae8be848b5d12e01865fe6bd3b394b460252aa3e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606012"
---
# <a name="secure-your-synapse-workspace-preview"></a>Proteja o seu espaço de trabalho Synapse (pré-visualização)

Este artigo irá ensiná-lo a usar papéis e controlo de acesso para controlar atividades e acesso a dados. Seguindo estas instruções, o controlo de acesso no Azure Synapse Analytics é simplificado. Basta adicionar e remover os utilizadores a um dos três grupos de segurança.

## <a name="overview"></a>Descrição geral

Para garantir um espaço de trabalho Synapse (pré-visualização), seguirá um padrão de configuração dos seguintes itens:

- Funções azure (como as incorporadas como Owner, Contributor, etc.)
- Papéis sinapse – estes papéis são exclusivos da Synapse e não são baseados em papéis de Azure. Há três destes papéis:
  - Administração do espaço de trabalho sinapse
  - Administração Synapse SQL
  - Administração Synapse Spark
- Controlo de acesso a dados em Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Controlo de acesso para bases de dados Synapse SQL e Spark

## <a name="steps-to-secure-a-synapse-workspace"></a>Passos para garantir um espaço de trabalho sinapse

Este documento utiliza nomes padrão para simplificar as instruções. Substitua-os por quaisquer nomes à sua escolha.

|Definição | Valor de exemplo | Descrição |
| :------ | :-------------- | :---------- |
| **Espaço de trabalho sinapse** | WS1 |  O nome que o espaço de trabalho synapse terá. |
| **Conta ADLSGEN2** | STG1 | A conta ADLS para usar com o seu espaço de trabalho. |
| **Contentor** | CNT1 | O recipiente em STG1 que o espaço de trabalho utilizará por defeito. |
| **Inquilino de diretório ativo** | contoso | o nome de inquilino de diretório ativo.|
||||

## <a name="step-1-set-up-security-groups"></a>PASSO 1: Criar grupos de segurança

Crie e povoetrês grupos de segurança para o seu espaço de trabalho:

- **WS1\_WSAdmins** – para utilizadores que precisam de controlo total sobre o espaço de trabalho
- **WS1\_SparkAdmins** – para os utilizadores que precisam de controlo total sobre os aspetos spark do espaço de trabalho
- **WS1\_SQLAdmins** – para utilizadores que precisam de controlo total sobre os aspetos SQL do espaço de trabalho
- Adicione **WS1\_WSAdmins** à **WS1\_SQLAdmins**
- Adicione **WS1\_WSAdmins** à **WS1\_SparkAdmins**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>PASSO 2: Prepare a sua conta Gen2 de Armazenamento de Lago de Dados

Identifique esta informação sobre o seu armazenamento:

- A conta ADLSGEN2 para utilizar para o seu espaço de trabalho. Este documento chama-lhe STG1.  O STG1 é considerado a conta de armazenamento "primária" para o seu espaço de trabalho.
- O recipiente dentro do WS1 que o seu espaço de trabalho Synapse utilizará por defeito. Este documento chama-lhe CNT1.  Este recipiente é utilizado para:
  - Armazenar os ficheiros de dados de backup para tabelas Spark
  - Registos de execução para trabalhos de faísca

- Utilizando o portal Azure, atribua aos grupos de segurança as seguintes funções na CNT1

  - Atribuir **WS1\_WSAdmins** à função de Contribuinte de **Dados blob** de armazenamento
  - Atribuir **A WS1\_SparkAdmins** à função de Contribuinte de **Dados blob** de armazenamento
  - Atribuir **WS1\_SQLAdmins** à função de Contribuinte de **Dados blob de armazenamento**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>PASSO 3: Crie e configure o seu espaço de trabalho sinapse

No portal Azure, crie um espaço de trabalho sinapse:

- Nomeie o espaço de trabalho WS1
- Escolha STG1 para a conta de Armazenamento
- Escolha CNT1 para o recipiente que está a ser utilizado como "sistema de ficheiros".
- Open WS1 no Estúdio Synapse
- Selecione **Manage Access** > **Control** atribua os grupos de segurança às seguintes funções Synapse.
  - Atribuir **wS1\_WSAdmins** aos administradores do espaço de trabalho sinapse
  - Atribuir **wS1\_SparkAdmins** aos administradores da Synapse Spark
  - Atribuir **wS1\_SQLAdmins** a administradores Synapse SQL

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>PASSO 4: Configurar data lake storage Gen2 para uso por espaço de trabalho Synapse

O espaço de trabalho Synapse precisa de acesso ao STG1 e ao CNT1 para que possa executar gasodutos e executar tarefas do sistema.

- Abra o portal do Azure
- Localizar STG1
- Navegar para CNT1
- Certifique-se de que o MSI (Identidade de Serviço Gerido) para a WS1 é atribuído ao papel de Colaborador de **Dados da Blob Azure** na CNT1
  - Se não o vir atribuído, atribua-o.
  - O MSI tem o mesmo nome que o espaço de trabalho. Neste caso, seria &quot;wS1&quot;.

## <a name="step-5-configure-admin-access-for-sql-pools"></a>PASSO 5: Configure acesso administrativo para piscinas SQL

- Abra o portal do Azure
- Navegar para wS1
- Em **Definições,** clique em **administração de Diretório Ativo SQL**
- Clique **em Configurar** administrador\_e escolha WS1 SQLAdmins

## <a name="step-6-maintaining-access-control"></a>PASSO 6: Manutenção do controlo de acesso

A configuração está terminada.

Agora, para gerir o acesso dos utilizadores, pode adicionar e remover os utilizadores aos três grupos de segurança.

Embora possa atribuir manualmente os utilizadores às funções Synapse, se o fizer, não configurar as coisas de forma consistente. Em vez disso, adicione ou remova apenas os utilizadores aos grupos de segurança.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>PASSO 7: Verificar o acesso dos utilizadores nas funções

Os utilizadores de cada função precisam de completar os seguintes passos:

|   | Passo | Administradores de área de trabalho | Administradores de faíscas | Administradores da SQL |
| --- | --- | --- | --- | --- |
| 1 | Faça upload de um ficheiro parquet para cnt1 | SIM | SIM | SIM |
| 2 | Leia o ficheiro parquet usando sQL a pedido | SIM | NO | SIM |
| 3 | Criar uma piscina de faíscas | SIM [1] | SIM [1] | NO  |
| 4 | Lê o arquivo de parquet com um Caderno | SIM | SIM | NO |
| 5 | Crie um oleoduto a partir do Caderno e desencadeie o oleoduto para funcionar agora | SIM | NO | NO |
| 6 | Crie um SQL Pool e execute &quot;um script SQL como SELECT 1&quot; | SIM [1] | NO | SIM[1] |

> [!NOTE]
> [1] Para criar piscinas SQL ou Spark, o utilizador deve ter pelo menos uma função de Contribuinte no espaço de trabalho Synapse.
> [!TIP]
>
> - Alguns passos não serão deliberadamente permitidos dependendo do papel.
> - Tenha em mente que algumas tarefas podem falhar se a segurança não estiver totalmente configurada. Estas tarefas são anotadas na tabela.

## <a name="step-8-network-security"></a>PASSO 8: Segurança da Rede

Para configurar a firewall do espaço de trabalho, a rede virtual e o [Private Link.](../../sql-database/sql-database-private-endpoint-overview.md)

## <a name="step-9-completion"></a>PASSO 9: Conclusão

O seu espaço de trabalho está agora totalmente configurado e seguro.

## <a name="how-roles-interact-with-synapse-studio"></a>Como os papéis interagem com o Estúdio Synapse

O Estúdio Synapse comportar-se-á de forma diferente com base nas funções dos utilizadores. Alguns itens podem ser ocultados ou desativados se um utilizador não for atribuído a funções que dêem o acesso adequado. A tabela que se segue resume o efeito no Estúdio Synapse.

| Tarefa | Administradores do espaço de trabalho | Administradores de faíscas | Administradores da SQL |
| --- | --- | --- | --- |
| Open Synapse Studio | SIM | SIM | SIM |
| Ver home hub | SIM | SIM | SIM |
| Ver Centro de Dados | SIM | SIM | SIM |
| Data Hub / Ver contas e contentores ADLSGen2 ligados | SIM [1] | SIM[1] | SIM[1] |
| Data Hub / Ver Bases de Dados | SIM | SIM | SIM |
| Data Hub / Ver objetos em bases de dados | SIM | SIM | SIM |
| Data Hub / Dados de acesso nas bases de dados do pool SQL | SIM   | NO   | SIM   |
| Data Hub / Dados de acesso em bases de dados on-demand SQL | SIM [2]  | NO  | SIM [2]  |
| Data Hub / Dados de acesso nas bases de dados spark | SIM [2] | SIM [2] | SIM [2] |
| Use o hub Desenvolver | SIM | SIM | SIM |
| Desenvolver scripts SQL Hub / autor | SIM | NO | SIM |
| Desenvolver Definições de Trabalho de Hub / Autor | SIM | SIM | NO |
| Desenvolver Cadernos Hub /autor | SIM | SIM | NO |
| Desenvolver Hub / autor Dataflows | SIM | NO | NO |
| Use o hub de Orquestração | SIM | SIM | SIM |
| Centro de orquestração / usar pipelines | SIM | NO | NO |
| Use o Manage Hub | SIM | SIM | SIM |
| Gerir piscinas Hub / SQL | SIM | NO | SIM |
| Gerir piscinas hub / spark | SIM | SIM | NO |
| Gerir hub / gatilhos | SIM | NO | NO |
| Gerir os serviços hub / Linked | SIM | SIM | SIM |
| Gerir o Hub / Controlo de Acesso (atribuir utilizadores às funções do espaço de trabalho Synapse) | SIM | NO | NO |
| Gerir os tempos de execução do Hub / Integração | SIM | SIM | SIM |

> [!NOTE]
> [1] O acesso aos dados em contentores depende do controlo de acesso nas tabelas DeSQL OD e mesas Spark sql e spark armazenam os seus dados em ADLSGen2 e o acesso requer as permissões adequadas na ADLSGen2.

## <a name="next-steps"></a>Passos seguintes

Criar um espaço de [trabalho sinapse](../quickstart-create-workspace.md)
