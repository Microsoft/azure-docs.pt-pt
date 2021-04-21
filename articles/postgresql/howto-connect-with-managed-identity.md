---
title: Conecte-se com identidade gerida - Base de dados Azure para PostgreSQL - Servidor Único
description: Saiba como conectar e autenticar usando identidade gerida para autenticação com Base de Dados Azure para PostgreSQL
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: beb5930e98a5c5498349dc3aa773423ee5d281bd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792476"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>Ligar à Base de Dados do Azure para PostgreSQL com a Identidade Gerida

Este artigo mostra-lhe como usar uma identidade atribuída ao utilizador para uma Máquina Virtual Azure (VM) para aceder a uma Base de Dados Azure para servidor PostgreSQL. As Identidades de Serviço Geridas são geridas automaticamente pelo Azure e permitem-lhe fazer a autenticação em serviços que suportam a autenticação do Azure AD, sem ser necessário inserir as credenciais no seu código. 

Saiba como:
- Conceda o acesso do seu VM a uma Base de Dados Azure para servidor PostgreSQL
- Criar um utilizador na base de dados que represente a identidade atribuída pelo utilizador do VM
- Obtenha um token de acesso usando a identidade VM e use-o para consultar uma Base de Dados Azure para servidor PostgreSQL
- Implementar a recuperação de fichas numa aplicação de exemplo C#

## <a name="prerequisites"></a>Pré-requisitos

- Se não estiver familiarizado com a funcionalidade das identidades geridas para os recursos do Azure, veja esta [descrição geral](../../articles/active-directory/managed-identities-azure-resources/overview.md). Se não tiver uma conta do Azure, [inscreva-se numa conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para fazer a criação de recursos e gestão de funções necessárias, a sua conta necessita de permissões "Owner" no âmbito apropriado (a sua subscrição ou grupo de recursos). Se precisar de assistência com a atribuição de funções, consulte [as funções de Assign Azure para gerir o acesso aos seus recursos de subscrição Azure](../../articles/role-based-access-control/role-assignments-portal.md).
- Precisa de um Azure VM (por exemplo, executando Ubuntu Linux) que gostaria de usar para aceder à sua base de dados usando identidade gerida
- Precisa de uma base de dados Azure para servidor de base de dados PostgreSQL que tenha [a autenticação AD AD](howto-configure-sign-in-aad-authentication.md) configurada
- Para seguir o exemplo C#, primeiro complete o guia como [conectar-se com C#](connect-csharp.md)

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>Criar uma identidade gerida atribuída ao utilizador para o seu VM

Crie uma identidade na sua subscrição utilizando o comando [de criação de identidade az.](/cli/azure/identity#az_identity_create) Pode utilizar o mesmo grupo de recursos em que a sua máquina virtual funciona, ou um outro.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

Para configurar a identidade nos seguintes passos, utilize o comando [de demonstração de identidade az](/cli/azure/identity#az_identity_show) para armazenar o ID de recursos da identidade e o ID do cliente em variáveis.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

Podemos agora atribuir a identidade atribuída ao VM com o comando [de atribuição de identidade az vm:](/cli/azure/vm/identity#az_vm_identity_assign)

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

Para terminar a configuração, mostre o valor do ID do Cliente, que necessitará nos próximos passos:

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>Criar um utilizador PostgreSQL para a sua Identidade Gerida

Agora, conecte-se como utilizador do administrador AD AZure à sua base de dados PostgreSQL e execute as seguintes declarações SQL:

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

A identidade gerida tem agora acesso ao autenticar com o nome de utilizador `myuser` (substitua-a por um nome à sua escolha).

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Recuperação do token de acesso do serviço Azure Instance Metadados

A sua aplicação pode agora obter um token de acesso do serviço Azure Instance Medata e usá-lo para autenticar com a base de dados.

Esta recuperação simbólica é feita fazendo um pedido HTTP `http://169.254.169.254/metadata/identity/oauth2/token` e passando os seguintes parâmetros:

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID` (que recuperou mais cedo)

Você receberá de volta um resultado JSON que contém um `access_token` campo - este valor de texto longo é o token de acesso de identidade gerida, que deve usar como palavra-passe ao ligar à base de dados.

Para efeitos de teste, pode executar os seguintes comandos na sua concha. Nota que `curl` `jq` precisa, e o `psql` cliente instalado.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

Está agora ligado à base de dados que configuraste anteriormente.

## <a name="connecting-using-managed-identity-in-c"></a>Ligação utilizando identidade gerida em C #

Esta secção mostra como obter um token de acesso usando a identidade gerida atribuída pelo utilizador do VM e usá-lo para ligar para a Base de Dados de Azure para PostgreSQL. A Azure Database for PostgreSQL suporta de forma nativa a autenticação Azure AD, para que possa aceitar diretamente os tokens de acesso obtidos usando identidades geridas para recursos Azure. Ao criar uma ligação ao PostgreSQL, você passa o token de acesso no campo de senha.

Aqui está um exemplo de código .NET de abertura de uma ligação ao PostgreSQL usando um token de acesso. Este código deve ser executado no VM para aceder ao ponto final da identidade gerida pelo utilizador do VM. .Net Framework 4.6 ou superior ou .NET Core 2.2 ou superior é necessário para utilizar o método de token de acesso. Substitua os valores de HOST, USER, DATABASE e CLIENT_ID.

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

* Reveja os conceitos globais de [autenticação do Azure Ative Directory com Azure Database for PostgreSQL](concepts-aad-authentication.md)
