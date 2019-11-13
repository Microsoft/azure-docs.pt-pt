---
title: incluir ficheiro
description: incluir ficheiro
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 09/12/2019
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: c20159d0583e18d0f5e71152fdb600d03db43224
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "74000449"
---
Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Iniciar sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql---hyperscale-citus"></a>Criar um banco de dados do Azure para PostgreSQL-Citus (hiperescala)

Siga estes passos para criar uma Base de Dados do Azure para o servidor PostgreSQL:
1. Clique em **Criar um recurso**, no canto superior esquerdo do portal do Azure.
2. Selecione **Bases de Dados**, na página **Nova**, e selecione **Base de Dados do Azure para o PostgreSQL**, na página **Bases de Dados**.
3. Para a opção de implantação, clique no botão **criar** em **grupo de servidores de hiperescala (Citus).**
4. Preencha o formulário com os detalhes do novo servidor com as seguintes informações:
   - Grupo de recursos: clique no link **criar novo** abaixo da caixa de texto deste campo. Insira um nome como **myresourceattribute**.
   - Nome do grupo de servidores: Insira um nome exclusivo para o novo grupo de servidores, que também será usado para um subdomínio de servidor.
   - Nome de usuário do administrador: atualmente, ele precisa ser o valor **citus**e não pode ser alterado.
   - Senha: deve ter pelo menos oito caracteres e conter caracteres de três das categorias a seguir – letras maiúsculas em inglês, letras minúsculas, números (0-9) e caracteres não alfanuméricos (!, $, #,% e assim por diante).
   - Local: Use o local mais próximo de seus usuários para dar a eles o acesso mais rápido aos dados.

   > [!IMPORTANT]
   > A senha de administrador do servidor que você especificar aqui é necessária para fazer logon no servidor e em seus bancos de dados. Lembre-se ou grave estas informações para utilização posterior.

5. Clique em **Configurar grupo de servidores**. Deixe as configurações dessa seção inalteradas e clique em **salvar**.
6. Clique em **Avançar: rede >** na parte inferior da tela.

7. Na guia **rede** , clique no botão de opção **ponto de extremidade público** .
   ![ponto de extremidade público selecionado](./media/azure-postgresql-hyperscale-create-db/network-public-endpoint.png)
8. Clique no link **+ Adicionar endereço IP do cliente atual**.
   ![](./media/azure-postgresql-hyperscale-create-db/network-add-client-ip.png) IP do cliente adicionado

   > [!NOTE]
   > O servidor PostgreSQL do Azure comunica através da porta 5432. Se estiver a tentar ligar a partir de uma rede empresarial, o tráfego de saída através da porta 5432 poderá não ser permitido pela firewall da rede. Se assim for, não poderá ligar ao servidor da Base de Dados SQL do Azure, a menos que o departamento de TI abra a porta 5432.
   >

9. Clique em **revisar + criar** e em **criar** para provisionar o servidor. O aprovisionamento demora alguns minutos.
10. A página será redirecionada para monitorar a implantação. Quando o status ao vivo for alterado de **sua implantação estiver em andamento** para a **implantação ser concluída**, clique no item de menu **saídas** à esquerda da página.
11. A página de saídas conterá um nome de host de coordenador com um botão ao lado dele para copiar o valor para a área de transferência. Registre essas informações para uso posterior.

## <a name="connect-to-the-database-using-psql"></a>Conectar-se ao banco de dados usando psql

Quando você cria o banco de dados do Azure para o servidor PostgreSQL, um banco de dados padrão chamado **citus** é criado. Para se conectar ao servidor de banco de dados, você precisa de uma cadeia de conexão e da senha de administrador.

1. Obtenha a cadeia de conexão. Na página grupo de servidores, clique no item de menu **cadeias de conexão** . (Está em **configurações**.) Localize a cadeia de caracteres marcada  **C++ (libpq)** . Ele estará no formato:

   ```
   host=hostname.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require
   ```

   Copie a cadeia de caracteres. Será necessário substituir "{Your\_password}" pela senha administrativa que você escolheu anteriormente. O sistema não armazena sua senha de texto não criptografado e, portanto, não pode exibi-la para você na cadeia de conexão.

2. Abra uma janela de terminal no computador local.

3. No prompt, conecte-se ao banco de dados do Azure para o servidor PostgreSQL com o utilitário [psql](https://www.postgresql.org/docs/current/app-psql.html) . Passe sua cadeia de conexão entre aspas, certificando-se de que ela contém sua senha:
   ```bash
   psql "{connection_string}"
   ```

   Por exemplo, o comando a seguir se conecta ao nó de coordenador do grupo de servidores **mydemoserver**:

   ```bash
   psql "host=mydemoserver-c.postgres.database.azure.com port=5432 dbname=citus user=citus password={your_password} sslmode=require"
   ```
