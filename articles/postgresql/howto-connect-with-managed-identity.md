---
title: Conecte-se com identidade gerida - Base de Dados Azure para PostgreSQL - Servidor Único
description: Saiba como conectar e autenticar usando identidade gerida para autenticação com base de dados Azure para PostgreSQL
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 01a27a9c98c1c429cdc381ba0c1e9ef4186c9e7a
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83664726"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Conecte-se com identidade gerida à base de dados Azure para PostgreSQL

Este artigo mostra como usar uma identidade atribuída ao utilizador para uma Máquina Virtual Azure (VM) para aceder a uma Base de Dados Azure para servidor PostgreSQL. As Identidades de Serviço Geridas são geridas automaticamente pelo Azure e permitem-lhe fazer a autenticação em serviços que suportam a autenticação do Azure AD, sem ser necessário inserir as credenciais no seu código. Saiba como:

> [!div class="checklist"]
> * Conceda o seu acesso vM a uma Base de Dados Azure para servidor PostgreSQL
> * Criar um utilizador na base de dados que represente a identidade atribuída ao utilizador do VM
> * Obtenha um sinal de acesso usando a identidade VM e use-o para consultar uma Base de Dados Azure para servidor PostgreSQL
> * Implementar a recuperação simbólica numa aplicação de exemplo C#

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com a funcionalidade das identidades geridas para os recursos do Azure, veja esta [descrição geral](../../articles/active-directory/managed-identities-azure-resources/overview.md). Se não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para fazer a criação de recursos e gestão de funções necessárias, a sua conta necessita de permissões "Proprietário" no âmbito apropriado (a sua subscrição ou grupo de recursos). Se precisar de assistência com a atribuição de função, veja [Utilizar Controlo de Acesso Baseado em Funções para gerir o acesso aos recursos da sua subscrição do Azure](../../articles/role-based-access-control/role-assignments-portal.md).
- Você precisa de um VM Azure (por exemplo executando Ubuntu Linux) que você gostaria de usar para aceder à sua base de dados usando identidade gerida
- Precisa de uma Base de Dados Azure para servidor de base de dados PostgreSQL que tenha [autenticação Azure AD](howto-configure-sign-in-aad-authentication.md) configurada
- Para seguir o exemplo C#, complete primeiro o guia como [conectar com C#](connect-csharp.md)

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Criar uma identidade gerida atribuída ao utilizador para o seu VM

Crie uma identidade na sua subscrição usando a [identidade az criar](/cli/azure/identity?view=azure-cli-latest#az-identity-create) comando. Pode utilizar o mesmo grupo de recursos em que a sua máquina virtual funciona, ou outra.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Para configurar a identidade nos seguintes passos, utilize o comando de mostrar identidade [az](/cli/azure/identity?view=azure-cli-latest#az-identity-show) para armazenar o ID de recursos da identidade e identificação do cliente em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

Podemos agora atribuir a identidade atribuída ao VM com o comando de atribuição de [identidade az vm:](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign)

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

Para terminar a configuração, mostre o valor do ID do Cliente, que necessitará nos próximos passos:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Criar um utilizador PostgreSQL para a sua Identidade Gerida

Agora, ligue-se como administrador da AD Azure à sua base de dados PostgreSQL e execute as seguintes declarações SQL:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

A identidade gerida tem agora acesso ao autenticar o nome de utilizador `myuser` (substitua-o por um nome à sua escolha).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Recuperação do sinal de acesso do serviço de metadados de instância seletiva Azure

A sua aplicação pode agora recuperar um sinal de acesso do serviço de Metadados de Instância Seletiva e utilizá-lo para autenticar com a base de dados.

Esta recuperação simbólica é feita fazendo um pedido http e `http://169.254.169.254/metadata/identity/oauth2/token` passando os seguintes parâmetros:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID`(que recuperou mais cedo)

Você receberá de volta um resultado JSON que contém um `access_token` campo - este longo valor de texto é o token de acesso à Identidade Gerida, que deve usar como palavra-passe ao ligar-se à base de dados.

Para efeitos de teste, pode executar os seguintes comandos na sua concha. Note que `curl` `jq` precisa, e o `psql` cliente instalado.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Está agora ligado à base de dados que configuraste anteriormente.

## <a name="connecting-using-managed-identity-in-c"></a>Ligação utilizando identidade gerida em C #

Esta secção mostra como obter um sinal de acesso utilizando a identidade gerida atribuída pelo Utilizador vM e usá-la para ligar para a Base de Dados Azure para PostgreSQL. A Base de Dados Azure para PostgreSQL apoia de forma nativa a autenticação Azure AD, para que possa aceitar diretamente fichas de acesso obtidas utilizando identidades geridas para recursos Azure. Ao criar uma ligação ao PostgreSQL, passa o sinal de acesso no campo de palavra-passe.

Aqui está um exemplo de código .NET de abertura de uma ligação ao PostgreSQL usando um token de acesso. Este código deve ser executado no VM para aceder ao ponto final da identidade gerida atribuída pelo VM. .NET Quadro 4.6 ou superior ou .NET Core 2.2 ou superior é necessário utilizar o método do token de acesso. Substitua os valores de HOST, USER, DATABASE e CLIENT_ID.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static void Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";
            string accessToken = null;

            try
            {
                // Call managed identities for Azure resources endpoint.
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader and extract access token.
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

Quando executado, este comando dará uma saída como esta:

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>Passos seguintes

* Reveja os conceitos globais para [autenticação de Diretório Ativo Azure com Base de Dados Azure para PostgreSQL](concepts-aad-authentication.md)
