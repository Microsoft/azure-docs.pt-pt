---
title: incluir ficheiro
description: incluir ficheiro
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 05/14/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c07e352288d7dc1d0bf198fd74c8baaded3a2d23
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66154493"
---
Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Criar uma base de dados do Azure para PostgreSQL - Hiperescala (Citus)

Siga estes passos para criar uma Base de Dados do Azure para o servidor PostgreSQL:
1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Selecione **Bases de Dados**, na página **Nova**, e selecione **Base de Dados do Azure para o PostgreSQL**, na página **Bases de Dados**.
3. Para a opção de implementação, clique nas **Create** botão em **grupo de servidores de grande escala (Citus) - pré-visualização.**
4. Preencha o formulário com os detalhes do novo servidor com as seguintes informações:
   - Grupo de recursos: clique nas **criar novo** link abaixo da caixa de texto para este campo. Introduza um nome, tal como **myresourcegroup**.
   - Nome do grupo de servidor: introduza um nome exclusivo para o novo grupo de servidor, que também irá ser utilizado para um subdomínio de servidor.
   - Nome de utilizador administrador: atualmente necessária para ser o valor **citus**e não pode ser alterado.
   - Palavra-passe: tem de ser, pelo menos, oito carateres e conter carateres de três das categorias seguintes: letras em maiúscula letras, em minúscula inglesas, números (0-9) e carateres não alfanuméricos (!, $, #, % etc.)
   - Localização: Utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.

   > [!IMPORTANT]
   > A senha de administrador do servidor que especificar aqui é necessário para iniciar sessão no servidor e respetivas bases de dados. Lembre-se ou grave estas informações para utilização posterior.

5. Clique em **configurar grupo de servidor**. Deixe as definições, em que secção inalterada e clique em **guardar**.
6. Clique em **rever + criar** e, em seguida **criar** para aprovisionar o servidor. O aprovisionamento demora alguns minutos.
7. A página irá redirecionar para monitorizar a implementação. Quando o estado em direto é alterado de **sua implementação está em curso** para **sua implementação está completa**, clique nas **saídas** item de menu no lado esquerdo da página.
8. A página de saídas irá conter um nome de anfitrião do coordenador com um botão junto ao mesmo para copiar o valor para a área de transferência. Registe estas informações para uso posterior.

## <a name="configure-a-server-level-firewall-rule"></a>Configurar uma regra de firewall ao nível do servidor

O Azure de base de dados para o PostgreSQL – Hiperescala (Citus) (pré-visualização) serviço utiliza uma firewall ao nível do servidor. Por predefinição, o firewall impede que todas as aplicações e ferramentas externas estabeleçam uma ligação para o nó coordenador e quaisquer bases de dados dentro. Podemos tem de adicionar uma regra para abrir a firewall para um intervalo de endereços IP específico.

1. Do **saídas** secção onde copiou anteriormente o nome de anfitrião do nó de coordenador, clique em volta para o **descrição geral** item de menu.

2. Encontrar o nome da sua implementação grupo de servidor e clique no mesmo. (O nome do grupo de servidor serão *não* ter um sufixo. Itens com nomes que termine em, por exemplo, "-c", "-w0", ou "-w1" não são o grupo de servidor.)

3. Clique em **Firewall** sob **segurança** no menu da esquerda.

4. Clique no link **+ Adicionar regra de firewall para o endereço IP de cliente atual**.

5. Por último, clique a **guardar** botão.

   > [!NOTE]
   > O servidor PostgreSQL do Azure comunica através da porta 5432. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 5432.
   >

## <a name="connect-to-the-database-using-psql"></a>Ligar à base de dados com psql

Quando cria a base de dados do Azure para o servidor PostgreSQL, uma base de dados predefinida com o nome **citus** é criado. Para ligar ao seu servidor de base de dados, terá de uma cadeia de ligação e a palavra-passe de administrador.

1. Obter a cadeia de ligação. Na página de grupo de servidor clique a **cadeias de ligação** item de menu. (Está sob **definições**.) Localize a cadeia de marcado  **C++ (libpq)**. É o formato:

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Copie a cadeia de caracteres. Terá de substituir "{sua\_palavra-passe}" com a palavra-passe administrativa escolhidos anteriormente. O sistema não armazena a palavra-passe de texto sem formatação e por isso, não é possível apresentá-lo para na cadeia de ligação.

2. Abra uma janela de terminal no seu computador local.

3. Na linha de comandos, estabeleça ligação à sua base de dados do Azure para o servidor PostgreSQL com o [psql](https://www.postgresql.org/docs/current/app-psql.html) utilitário. Passa a cadeia de ligação aspas, certificando-se de que contém a palavra-passe:
   ```bash
   psql "{connection_string}"
   ```

   Por exemplo, o comando seguinte liga para o nó coordenador do grupo de servidor **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
