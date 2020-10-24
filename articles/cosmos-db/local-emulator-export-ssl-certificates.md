---
title: Exportar os certificados do Emulador do Azure Cosmos DB
description: Saiba como exportar o certificado emulador Azure Cosmos DB para uso com aplicações java, Python e Node.js. Os certificados devem ser exportados e utilizados para idiomas e ambientes de tempo de funcionação que não utilizem a Loja de Certificados do Windows.
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2020
author: deborahc
ms.author: dech
ms.custom: devx-track-python, devx-track-java, contperfq1
ms.openlocfilehash: f747cc6405cef07510766bcfa4c826aec9d9857b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92490412"
---
# <a name="export-the-azure-cosmos-db-emulator-certificates-for-use-with-java-python-and-nodejs-apps"></a>Exporte os certificados emuladores Azure Cosmos DB para uso com aplicativos Java, Python e Node.js

O emulador do Azure Cosmos DB fornece um ambiente local que emula o serviço do Azure Cosmos DB para fins de desenvolvimento. O emulador Azure Cosmos suporta apenas uma comunicação segura através de ligações TLS.

Os certificados no emulador local Azure Cosmos DB são gerados na primeira vez que você executar o emulador. Existem dois certificados. Um deles é usado para se ligar ao emulador local e o outro é usado para gerir a encriptação padrão dos dados do emulador dentro do emulador. O certificado que pretende exportar é o certificado de ligação com o nome amigável "DocumentDBEmulatorCertificate".

Quando utiliza o emulador para desenvolver aplicações em diferentes idiomas, como Java, Python ou Node.js, é necessário exportar o certificado emulador e importá-lo para a loja de certificados requerida.

O idioma e o tempo de execução .NET utilizam a Loja de Certificados do Windows para ligar de forma segura ao emulador local Azure Cosmos DB quando a aplicação é executada num anfitrião do Windows OS. Outras linguagens têm o seu próprio método de gerir e utilizar certificados. Por exemplo, a Java utiliza a sua própria [loja de certificados,](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html)a Python usa [invólucros de tomadas,](https://docs.python.org/2/library/ssl.html)e Node.js usa [tlsSocket](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

Este artigo demonstra como exportar os certificados TLS/SSL para utilização em diferentes idiomas e ambientes de tempo de funcionação que não se integram com a Loja de Certificados do Windows. Pode ler mais sobre o emulador em [Utilizar o Emulador do Azure Cosmos DB para desenvolvimento e teste](./local-emulator.md).

## <a name="export-the-azure-cosmos-db-tlsssl-certificate"></a><a id="export-emulator-certificate"></a>Exportar o certificado Azure Cosmos DB TLS/SSL

É necessário exportar o certificado emulador para utilizar com sucesso o ponto final do emulador a partir de idiomas e ambientes de tempo de execução que não se integram na Loja de Certificados do Windows. Pode exportar o certificado utilizando o Gestor de Certificados do Windows. Utilize as seguintes instruções passo a passo para exportar o certificado "DocumentDBEmulatorCertificate" como um ficheiro BASE-64 codificado X.509 (.cer):

1. Inicie o Gestor de certificados do Windows ao executar o certlm.msc e navegue para a pasta Pessoal->Certificados e abra o certificado com o nome amigável **DocumentDbEmulatorCertificate**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-1.png" alt-text="Passo 1 de exportação do emulador local do Azure Cosmos DB":::

1. Clique em **Detalhes** e, em seguida, em **OK**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-2.png" alt-text="Passo 1 de exportação do emulador local do Azure Cosmos DB":::

1. Clique em **Copiar para Ficheiro... **.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-3.png" alt-text="Passo 1 de exportação do emulador local do Azure Cosmos DB":::

1. Clique em **Seguinte**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-4.png" alt-text="Passo 1 de exportação do emulador local do Azure Cosmos DB":::

1. Clique em **Não, não exportar a chave privada** e, em seguida, clique em **Seguinte**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-5.png" alt-text="Passo 1 de exportação do emulador local do Azure Cosmos DB":::

1. Clique em **X.509 codificado na Base-64 (.CER)** e, em seguida, em **Seguinte**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-6.png" alt-text="Passo 1 de exportação do emulador local do Azure Cosmos DB":::

1. Dê um nome ao certificado. Neste caso **documentdbemulatorcert** e, em seguida, clique em **Seguinte**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-7.png" alt-text="Passo 1 de exportação do emulador local do Azure Cosmos DB":::

1. Clique em **Concluir**.

    :::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-export-step-8.png" alt-text="Passo 1 de exportação do emulador local do Azure Cosmos DB":::

## <a name="use-the-certificate-with-java-apps"></a>Use o certificado com aplicativos Java

Ao executar aplicações Java ou aplicações MongoDB que usam um cliente baseado em Java, é mais fácil instalar o certificado na loja de certificados padrão java do que passar as `-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>"` bandeiras. Por exemplo, a aplicação de Demonstração java incluída `https://localhost:8081/_explorer/index.html` depende da loja de certificados predefinidos.

Siga as instruções na [Adição de um Certificado à Loja de Certificados java](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store) para importar o certificado X.509 na loja de certificados Java padrão. Tenha em mente que estará a trabalhar no diretório *%JAVA_HOME%* ao executar o keytool. Após a importação do certificado para a loja de certificados, os clientes da API da SQL e da Azure Cosmos DB para a MongoDB poderão ligar-se ao Emulador Azure Cosmos.

Em alternativa, pode executar o seguinte roteiro de bash para importar o certificado:

```bash
#!/bin/bash

# If emulator was started with /AllowNetworkAccess, replace the below with the actual IP address of it:
EMULATOR_HOST=localhost
EMULATOR_PORT=8081
EMULATOR_CERT_PATH=/tmp/cosmos_emulator.cert
openssl s_client -connect ${EMULATOR_HOST}:${EMULATOR_PORT} </dev/null | sed -ne '/-BEGIN CERTIFICATE-/,/-END CERTIFICATE-/p' > $EMULATOR_CERT_PATH
# delete the cert if already exists
sudo $JAVA_HOME/bin/keytool -cacerts -delete -alias cosmos_emulator
# import the cert
sudo $JAVA_HOME/bin/keytool -cacerts -importcert -alias cosmos_emulator -file $EMULATOR_CERT_PATH
```

Uma vez instalado o certificado TLS/SSL "CosmosDBEmulatorCertificate", a sua aplicação deverá ser capaz de ligar e utilizar o Emulador Azure Cosmos DB local. Se tiver algum problema, pode seguir o artigo [de ligações Debugging SSL/TLS.](https://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) Na maioria dos casos, o certificado não pode ser instalado na loja *%JAVA_HOME%/jre/lib/security/cacerts.* Por exemplo, se tiver várias versões instaladas da Java, a sua aplicação poderá estar a utilizar uma loja de cacerts diferente daquela que atualizou.

## <a name="use-the-certificate-with-python-apps"></a>Use o certificado com aplicativos Python

Ao ligar-se ao emulador a partir de aplicações Python, a verificação TLS é desativada. Por predefinição, o [Python SDK (versão 2.0.0 ou superior)](sql-api-sdk-python.md) para o SQL API não tentará utilizar o certificado TLS/SSL ao ligar-se ao emulador local. Se quiser utilizar a validação TLS, pode seguir os exemplos na documentação de invólucros de [tomadas Python.](https://docs.python.org/2/library/ssl.html)

## <a name="how-to-use-the-certificate-in-nodejs"></a>Como utilizar o certificado no Node.js

Ao ligar-se ao emulador de Node.js SDKs, a verificação TLS é desativada. Por predefinição, o [Node.js SDK (versão 1.10.1 ou superior)](sql-api-sdk-node.md) para a API SQL não tentará utilizar o certificado TLS/SSL ao ligar-se ao emulador local. Se quiser utilizar a validação TLS, pode seguir os exemplos na [ documentaçãoNode.js](https://nodejs.org/api/tls.html#tls_tls_connect_options_callback).

## <a name="rotate-emulator-certificates"></a>Certificados de emulador rotativo

Pode forçar a regeneração dos certificados do emulador selecionando **dados de reset** a partir do emulador Azure Cosmos DB em execução no Tabuleiro do Windows. Note que esta ação também eliminará todos os dados armazenados localmente pelo emulador.

:::image type="content" source="./media/local-emulator-export-ssl-certificates/database-local-emulator-reset-data.png" alt-text="Passo 1 de exportação do emulador local do Azure Cosmos DB":::

Se instalou o certificado na loja de certificados Java ou os utilizou noutro local, tem de os importar novamente utilizando os certificados atuais. A sua aplicação não pode ligar-se ao emulador local até atualizar os certificados.

## <a name="next-steps"></a>Passos seguintes

* [Utilize parâmetros de linha de comando e comandos PowerShell para controlar o emulador](emulator-command-line-parameters.md)
* [Problemas de depurar com o emulador](troubleshoot-local-emulator.md)