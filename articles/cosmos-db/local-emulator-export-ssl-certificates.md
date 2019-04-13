---
title: Exportar os certificados de emulador do Azure Cosmos DB
description: Ao programar em linguagens e runtimes que não utilizam a Loja de Certificados do Windows, terá de exportar e gerir os certificados SSL. Esta mensagem apresenta instruções passo a passo.
ms.service: cosmos-db
ms.topic: tutorial
ms.date: 06/06/2017
author: deborahc
ms.author: dech
ms.openlocfilehash: cf280dfb806399a8c09838d965d71e7b18cb905f
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521395"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs"></a>Exporte os certificados de Emulador do Azure Cosmos DB para utilizar com o Java, o Python e o Node.js

[**Transferir o Emulador**](https://aka.ms/cosmosdb-emulator)

O Emulador do Azure Cosmos DB apresenta um ambiente local que emula o serviço do Azure Cosmos DB para fins de desenvolvimento, incluindo a utilização de ligações SSL. Esta mensagem demonstra como exportar os certificados SSL para utilizar em linguagens e runtimes que não se integram com o Arquivo de Certificados do Windows, como o Java, que utiliza o seu próprio [arquivo de certificados](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html), o Python que utiliza [wrappers do socket](https://docs.python.org/2/library/ssl.html) e o Node.js que utiliza o [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback). Pode ler mais sobre o emulador em [Utilizar o Emulador do Azure Cosmos DB para desenvolvimento e teste](./local-emulator.md).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Rodar certificados
> * Exportar certificado SSL
> * Aprender a utilizar o certificado em Java, Python e Node.js

## <a name="certification-rotation"></a>Rotação de certificação

Os certificados no Emulador Local do Azure Cosmos DB são gerados pela primeira vez que o emulador é executado. Existem dois certificados. Um serve para ligar ao emulador local e outro para gerir segredos no emulador. O certificado que pretende exportar é o certificado de ligação com o nome amigável "DocumentDBEmulatorCertificate".

Ambos os certificados podem ser regenerados ao clicar em **Repor Dados** conforme mostrado abaixo no Emulador do Azure Cosmos DB em execução no Tabuleiro do Windows. Se regenerar os certificados e tiver instalado os mesmos no arquivo de certificados do Java ou utilizado noutro local, terá de os atualizar, caso contrário, a sua aplicação já não será ligada ao emulador local.

![Dados de reposição do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png)

## <a name="how-to-export-the-azure-cosmos-db-ssl-certificate"></a>Como exportar o certificado SSL do Azure Cosmos DB

1. Inicie o Gestor de certificados do Windows ao executar o certlm.msc e navegue para a pasta Pessoal->Certificados e abra o certificado com o nome amigável **DocumentDbEmulatorCertificate**.

    ![Passo 1 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png)

2. Clique em **Detalhes** e, em seguida, em **OK**.

    ![Passo 2 de exportação do emulador local do Azure Cosmos DB](./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png)

3. Clique em **Copiar para Ficheiro...** .

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

Ao executar aplicações Java ou aplicações do MongoDB que utilizam o cliente de Java é mais fácil instalar o certificado para o arquivo de certificados do padrão de Java que passar o `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` sinalizadores. Por exemplo, a [aplicação de Demonstração do Java](https://localhost:8081/_explorer/index.html) incluída depende do arquivo de certificados predefinido.

Siga as instruções em [Adicionar um Certificado ao Arquivo de Certificados de AC do Java](https://docs.microsoft.com/azure/java-add-certificate-ca-store) para importar o certificado X.509 para o arquivo de certificados do Java. Tenha em conta que irá trabalhar no diretório %JAVA_HOME% ao executar o keytool.

Assim que o certificado SSL "CosmosDBEmulatorCertificate" estiver instalado, a aplicação deve ser capaz de ligar e utilizar o Emulador do Azure Cosmos DB local. Se continuar a ter problemas, deverá ler o artigo [Depurar Ligações de SSL/TLS](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html). É muito provável que o certificado não esteja instalado no arquivo %JAVA_HOME%/jre/lib/security/cacerts. Por exemplo, se tiver várias versões instaladas do Java, a aplicação poderá estar a utilizar um arquivo de certificados de AC diferente do que o que atualizou.

## <a name="how-to-use-the-certificate-in-python"></a>Como utilizar o certificado no Python

Por predefinição o [SDK do Python (versão 2.0.0 ou superior)](sql-api-sdk-python.md) para a API do SQL não irá tentar e irá utilizar o certificado SSL ao ligar ao emulador local. Se, no entanto, pretender utilizar a validação de SSL, pode seguir os exemplos da documentação [Wrappers de socket do Python](https://docs.python.org/2/library/ssl.html).

## <a name="how-to-use-the-certificate-in-nodejs"></a>Como utilizar o certificado no Node.js

Por predefinição o [SDK do Node.js (versão 1.10.1 ou superior)](sql-api-sdk-node.md) para a API do SQL não irá tentar e irá utilizar o certificado SSL ao ligar ao emulador local. Se, no entanto, pretender utilizar a validação de SSL, pode seguir os exemplos da [documentação do Node.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, fez o seguinte:

> [!div class="checklist"]
> * Certificados rodados
> * Certificado SSL exportado
> * Aprendeu a utilizar o certificado em Java, Python e Node.js

Agora, pode avançar para a secção Conceitos para obter mais informações sobre o Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Tunable data consistency levels in Azure Cosmos DB](../cosmos-db/consistency-levels.md) (Níveis de consistência de dados ajustáveis no Azure Cosmos DB)
