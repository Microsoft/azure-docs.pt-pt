---
title: Exportar os certificados do Emulador do Azure Cosmos DB
description: Ao desenvolver em idiomas e tempos de execução que não utilizem a Loja de Certificados windows terá de exportar e gerir os certificados TLS/SSL. Esta mensagem apresenta instruções passo a passo.
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
author: deborahc
ms.author: dech
ms.openlocfilehash: c72dbf24df850d8b0f7e5f26a873b78f5664c9e0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200943"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exporte os certificados de Emulador do Azure Cosmos DB para utilizar com o Java, o Python e o Node.js

[**Transferir o Emulador**](https://aka.ms/cosmosdb-emulator)

O Emulador DB Azure Cosmos proporciona um ambiente local que emudece o serviço Azure Cosmos DB para fins de desenvolvimento, incluindo a utilização de ligações TLS. Esta publicação demonstra como exportar os certificados TLS/SSL para utilização em idiomas e tempos de execução que não se integram com a Loja de Certificados windows, como a Java, que utiliza a sua própria loja de [certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) e python que utiliza invólucros de [tomadas](https://docs.python.org/2/library/ssl.html) e Node.js que utiliza [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Pode ler mais sobre o emulador em [Utilizar o Emulador do Azure Cosmos DB para desenvolvimento e teste](./local-emulator.md).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Rodar certificados
> * Certificado TLS/SSL de exportação
> * Aprender a utilizar o certificado em Java, Python e Node.js

## <a name="certification-rotation"></a>Rotação de certificação

Os certificados no Emulador Local do Azure Cosmos DB são gerados pela primeira vez que o emulador é executado. Existem dois certificados. Um serve para ligar ao emulador local e outro para gerir segredos no emulador. O certificado que pretende exportar é o certificado de ligação com o nome amigável "DocumentDBEmulatorCertificate".

Ambos os certificados podem ser regenerados ao clicar em **Repor Dados** conforme mostrado abaixo no Emulador do Azure Cosmos DB em execução no Tabuleiro do Windows. Se regenerar os certificados e tiver instalado os mesmos no arquivo de certificados do Java ou utilizado noutro local, terá de os atualizar, caso contrário, a sua aplicação já não será ligada ao emulador local.

![Dados de reposição do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-tlsssl-certificate"></a>Como exportar o certificado Azure Cosmos DB TLS/SSL

1. Inicie o Gestor de certificados do Windows ao executar o certlm.msc e navegue para a pasta Pessoal->Certificados e abra o certificado com o nome amigável **DocumentDbEmulatorCertificate**.

    ![Passo 1 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Clique em **Detalhes** e, em seguida, em **OK**.

    ![Passo 2 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Clique em **Copiar para Ficheiro... **.

    ![Passo 3 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png)

4. Clique em **Seguinte**.

    ![Passo 4 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png)

5. Clique em **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.

    ![Passo 5 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png)

6. Clique em **X.509 codificado na Base-64 (.CER)** e, em seguida, em **Seguinte**.

    ![Passo 6 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png)

7. Dê um nome ao certificado. Neste caso **documentdbemulatorcert** e, em seguida, clique em **Seguinte**.

    ![Passo 7 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png)

8. Clique em **Concluir**.

    ![Passo 8 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>Como utilizar o certificado no Java

Ao executar aplicações Java ou aplicações MongoDB que utilizam o cliente Java é mais `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` fácil instalar o certificado na loja de certificados padrão Java do que passar as bandeiras. Por exemplo, a aplicação`https://localhost:8081/_explorer/index.html`Java Demo incluída depende da loja de certificados por defeito.

Siga as instruções em [Adicionar um Certificado ao Arquivo de Certificados de AC do Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store) para importar o certificado X.509 para o arquivo de certificados do Java. Tenha em conta que irá trabalhar no diretório %JAVA_HOME% ao executar o keytool.

Uma vez instalado o certificado TLS/SSL "CosmosDBEmulatorCertificate" a sua aplicação deverá poder ligar e utilizar o Emulador DB Azure Cosmos local. Se continuar a ter problemas, deverá ler o artigo [Depurar Ligações de SSL/TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). É muito provável que o certificado não esteja instalado no arquivo %JAVA_HOME%/jre/lib/security/cacerts. Por exemplo, se tiver várias versões instaladas do Java, a aplicação poderá estar a utilizar um arquivo de certificados de AC diferente do que o que atualizou.

## <a name="how-to-use-the-certificate-in-python"></a>Como utilizar o certificado no Python

Por predefinição, o [Python SDK (versão 2.0.0 ou superior)](sql-api-sdk-python.md) para a API SQL não tentará utilizar o certificado TLS/SSL quando estiver ligado ao emulador local. Se quiser utilizar a validação TLS, pode seguir os exemplos na documentação dos invólucros da [tomada Python.](https://docs.python.org/2/library/ssl.html)

## <a name="how-to-use-the-certificate-in-nodejs"></a>Como utilizar o certificado no Node.js

Por predefinição, o [Node.js SDK (versão 1.10.1 ou superior)](sql-api-sdk-node.md) para a API SQL não tentará utilizar o certificado TLS/SSL quando estiver ligado ao emulador local. Se quiser utilizar a validação TLS, pode seguir os exemplos na [documentação Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Certificados rodados
> * Exportado o certificado TLS/SSL
> * Aprendeu a utilizar o certificado em Java, Python e Node.js

Agora, pode avançar para a secção Conceitos para obter mais informações sobre o Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Tunable data consistency levels in Azure Cosmos DB](../cosmos-db/consistency-levels.md) (Níveis de consistência de dados ajustáveis no Azure Cosmos DB)
