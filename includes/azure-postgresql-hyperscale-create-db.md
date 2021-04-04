---
title: incluir ficheiro
description: incluir ficheiro
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 12/16/2020
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: 26289f6bdfa086708548d17125a6dfdf50b02a8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97665196"
---
## <a name="create-a-hyperscale-citus-server-group"></a>Criar um grupo do servidor Hyperscale (Citus)

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).


Siga estes passos para criar uma Base de Dados do Azure para o servidor PostgreSQL:
1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Selecione **Bases de Dados**, na página **Nova**, e selecione **Base de Dados do Azure para o PostgreSQL**, na página **Bases de Dados**.
3. Para a opção de implementação, clique no botão **Criar** no **grupo servidor Hyperscale (Citus).**
4. Preencha o formulário com os detalhes do novo servidor com as seguintes informações:
   - Grupo de recursos: clique no novo link **Create** abaixo da caixa de texto para este campo. Insira um nome como **myresourcegroup**.
   - Nome do grupo do servidor: introduza um nome único para o novo grupo de servidores, que também será usado para um subdomínio do servidor.
   - Nome de utilizador do administrador: atualmente é necessário ser o **valor citus**, e não pode ser alterado.
   - Palavra-passe: deve ter pelo menos oito caracteres de comprimento e conter caracteres de três das seguintes categorias - letras maiúsculas inglesas, letras minúsculas inglesas, números (0-9) e caracteres não alfanuméricos (!, $, #, %, %, e assim por diante.)
   - Localização: utilize a localização mais próxima dos seus utilizadores para lhes dar o acesso mais rápido aos dados.

   > [!IMPORTANT]
   > A palavra-passe de administração do servidor que especifica aqui é necessária para iniciar sessão no servidor e nas suas bases de dados. Lembre-se ou grave estas informações para utilização posterior.

5. Clique **no grupo de servidores Configure**. Deixe as definições inalteradas nessa secção e clique em **Guardar**.
6. Clique **em seguida : >de rede** na parte inferior do ecrã.

7. No **separador 'Rede',** clique no botão de rádio **ponto final público.**
   ![Ponto final público selecionado](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Clique no link + Adicione o **endereço IP do cliente atual.**
   ![IP do cliente adicionado](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png)

   > [!NOTE]
   > O servidor PostgreSQL do Azure comunica através da porta 5432. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Em caso afirmativo, não é possível ligar-se ao seu cluster Hyperscale (Citus), a menos que o seu departamento de TI abra a porta 5432.
   >

9. Clique **em Rever + criar** e, em seguida, **criar** para provisão do servidor. O aprovisionamento demora alguns minutos.
10. A página irá redirecionar para a implementação do monitor. Quando o estado de vida mudar da **sua implementação** para a **sua implementação está concluído,** clique no item do menu **Saídas** à esquerda da página.
11. A página de saídas conterá um nome de anfitrião coordenador com um botão ao lado para copiar o valor para a área de transferência. Grave esta informação para utilização posterior.

### <a name="connect-to-the-database-using-psql"></a>Ligue-se à base de dados usando o psql

Quando cria a sua Base de Dados Azure para o servidor PostgreSQL, é criada uma base de dados padrão chamada **citus.** Para se ligar ao servidor de base de dados, precisa de uma cadeia de ligação e da senha de administração.

1. Obtenha a cadeia de ligação. Na página do grupo do servidor clique no item do menu **de cordas De Ligação.** (Está em **Definições**.) Encontre a corda marcada **psql**. Será do formulário:

   ```
   psql "host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```

   Copie a corda. Terá de substituir "{your \_ password}" pela palavra-passe administrativa que escolheu anteriormente. O sistema não armazena a sua palavra-passe de texto simples e por isso não pode exibi-la para si na cadeia de ligação.

2. Abra uma janela terminal no seu computador local.

3. No momento, ligue-se à sua Base de Dados Azure para o servidor PostgreSQL com o utilitário [psql.](https://www.postgresql.org/docs/current/app-psql.html) Passe a sua cadeia de ligação em aspas, tendo a certeza de que contém a sua palavra-passe:
   ```bash
   psql "host=..."
   ```

   Por exemplo, o seguinte comando liga-se ao nó coordenador do grupo de servidor **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
