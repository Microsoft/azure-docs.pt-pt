---
title: Gerir a Azure Data Lake Analytics usando Azure Java SDK
description: Este artigo descreve como usar o Azure Java SDK para escrever apps que gerem os empregos do Data Lake Analytics, fontes de dados, & utilizadores.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: 07830b36-2fe3-4809-a846-129cf67b6a9e
ms.topic: how-to
ms.date: 08/20/2019
ms.custom: devx-track-java
ms.openlocfilehash: bac13f187c99166bc868112792d80833387a84dc
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87373781"
---
# <a name="manage-azure-data-lake-analytics-using-a-java-app"></a>Gerir a Azure Data Lake Analytics usando uma aplicação Java
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Este artigo descreve como gerir as contas do Azure Data Lake Analytics, fontes de dados, utilizadores e empregos usando uma aplicação escrita usando o Azure Java SDK. 

## <a name="prerequisites"></a>Pré-requisitos
* **Kit de Desenvolvimento de Java (JDK) 8** (utilizando a versão Java 1.8).
* **IntelliJ** ou outro ambiente adequado de desenvolvimento java. As instruções deste documento utilizam o IntelliJ.
* Criar uma aplicação do Azure Active Directory (AAD) e obtenha o **ID de Cliente**, o **ID do Inquilino** e a **Chave**. Para mais informações sobre as aplicações do AAD e instruções sobre como obter um ID de cliente, consulte [Criar aplicação e serviço principal do Active Directory utilizando o portal](../active-directory/develop/howto-create-service-principal-portal.md). O URI e Chave de Resposta está disponível no portal assim que tiver a aplicação criada e a chave gerada.

## <a name="authenticating-using-azure-active-directory"></a>Autenticação utilizando diretório ativo Azure

O código que se segue fornece código para a autenticação **não interativa,** onde a aplicação fornece as suas próprias credenciais.

## <a name="create-a-java-application"></a>Criar uma aplicação Java
1. Abra o IntelliJ e crie um projeto Java utilizando o modelo **de Aplicação da Linha de Comando.**
2. Clique com o botão direito no lado esquerdo do ecrã e clique em **Adicionar Suporte para Framework**. Escolha **Maven** e clique em **OK**.
3. Abra o ficheiro **"pom.xml"** recém-criado e adicione o seguinte corte de texto entre a **\</version>** etiqueta e a **\</project>** etiqueta:

```xml
<dependencies>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-client-authentication</artifactId>
        <version>1.6.12</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-client-runtime</artifactId>
        <version>1.6.12</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.rest</groupId>
        <artifactId>client-runtime</artifactId>
        <version>1.6.12</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-mgmt-datalake-store</artifactId>
        <version>1.22.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-mgmt-datalake-analytics</artifactId>
        <version>1.22.0</version>
    </dependency>
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.3.6</version>
        <exclusions>
            <exclusion>
                <groupId>com.fasterxml.jackson.core</groupId>
                <artifactId>jackson-core</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```

Vá a **Definições de > de Ficheiros > construir > execução > implementação**. Selecione **Build Tools > Maven > Importing**. Em seguida, verifique **automaticamente os projetos Import Maven**.

Abra `Main.java` e substitua o bloco de código existente pelo seguinte código:

```java
import com.microsoft.azure.CloudException;
import com.microsoft.azure.credentials.ApplicationTokenCredentials;
import com.microsoft.azure.datalake.store.*;
import com.microsoft.azure.datalake.store.oauth2.*;
import com.microsoft.azure.management.datalake.analytics.implementation.*;
import com.microsoft.azure.management.datalake.store.*;
import com.microsoft.azure.management.datalake.store.implementation.*;
import com.microsoft.azure.management.datalake.store.models.*;
import com.microsoft.azure.management.datalake.analytics.*;
import com.microsoft.azure.management.datalake.analytics.models.*;
import com.microsoft.rest.credentials.ServiceClientCredentials;

import java.io.*;
import java.nio.charset.Charset;
import java.util.ArrayList;
import java.util.UUID;
import java.util.List;

public class Main {
    private static String _adlsAccountName;
    private static String _adlaAccountName;
    private static String _resourceGroupName;
    private static String _location;

    private static String _tenantId;
    private static String _subscriptionId;
    private static String _clientId;
    private static String _clientSecret;

    private static DataLakeStoreAccountManagementClient _adlsClient;
    private static ADLStoreClient _adlsStoreClient;
    private static DataLakeAnalyticsAccountManagementClient _adlaClient;
    private static DataLakeAnalyticsJobManagementClient _adlaJobClient;

    public static void main(String[] args) throws Exception {
        _adlsAccountName = "<DATA-LAKE-STORE-NAME>";
        _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>";
        _resourceGroupName = "<RESOURCE-GROUP-NAME>";
        _location = "East US 2";

        _tenantId = "<TENANT-ID>";
        _subscriptionId =  "<SUBSCRIPTION-ID>";
        _clientId = "<CLIENT-ID>";

        _clientSecret = "<CLIENT-SECRET>";

        String localFolderPath = "C:\\local_path\\";

        // ----------------------------------------
        // Authenticate
        // ----------------------------------------
        ApplicationTokenCredentials creds = new ApplicationTokenCredentials(_clientId, _tenantId, _clientSecret, null);
        SetupClients(creds);

        // ----------------------------------------
        // List Data Lake Store and Analytics accounts that this app can access
        // ----------------------------------------
        System.out.println(String.format("All ADL Store accounts that this app can access in subscription %s:", _subscriptionId));
        List<DataLakeStoreAccountBasic> adlsListResult = _adlsClient.accounts().list();
        for (DataLakeStoreAccountBasic acct : adlsListResult) {
            System.out.println(acct.name());
        }

        System.out.println(String.format("All ADL Analytics accounts that this app can access in subscription %s:", _subscriptionId));
        List<DataLakeAnalyticsAccountBasic> adlaListResult = _adlaClient.accounts().list();
        for (DataLakeAnalyticsAccountBasic acct : adlaListResult) {
            System.out.println(acct.name());
        }
        WaitForNewline("Accounts displayed.", "Creating files.");

        // ----------------------------------------
        // Create a file in Data Lake Store: input1.csv
        // ----------------------------------------
        CreateFile("/input1.csv", "123,abc", true);
        WaitForNewline("File created.", "Submitting a job.");

        // ----------------------------------------
        // Submit a job to Data Lake Analytics
        // ----------------------------------------
        String script = "@input = EXTRACT Row1 string, Row2 string FROM \"/input1.csv\" USING Extractors.Csv(); OUTPUT @input TO @\"/output1.csv\" USING Outputters.Csv();";
        UUID jobId = SubmitJobByScript(script, "testJob");
        WaitForNewline("Job submitted.", "Getting job status.");

        // ----------------------------------------
        // Wait for job completion and output job status
        // ----------------------------------------
        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
        System.out.println("Waiting for job completion.");
        WaitForJob(jobId);
        System.out.println(String.format("Job status: %s", GetJobStatus(jobId)));
        WaitForNewline("Job completed.", "Downloading job output.");

        // ----------------------------------------
        // Download job output from Data Lake Store
        // ----------------------------------------
        DownloadFile("/output1.csv", localFolderPath + "output1.csv");
        WaitForNewline("Job output downloaded.", "Deleting file.");

        DeleteFile("/output1.csv");
        WaitForNewline("File deleted.", "Done.");
    }

    public static void SetupClients(ServiceClientCredentials creds) {
        _adlsClient = new DataLakeStoreAccountManagementClientImpl(creds);
        _adlaClient = new DataLakeAnalyticsAccountManagementClientImpl(creds);
        _adlaJobClient = new DataLakeAnalyticsJobManagementClientImpl(creds);
        _adlsClient.withSubscriptionId(_subscriptionId);
        _adlaClient.withSubscriptionId(_subscriptionId);

        String authEndpoint = "https://login.microsoftonline.com/" + _tenantId + "/oauth2/token";
        AccessTokenProvider provider = new ClientCredsTokenProvider(authEndpoint, _clientId, _clientSecret);
        _adlsStoreClient = ADLStoreClient.createClient(_adlsAccountName + ".azuredatalakestore.net", provider);
    }

    public static void WaitForNewline(String reason, String nextAction) {
        if (nextAction == null)
            nextAction = "";

        System.out.println(reason + "\r\nPress ENTER to continue...");
        try {
            System.in.read();
        } catch (Exception e) {
        }

        if (!nextAction.isEmpty()) {
            System.out.println(nextAction);
        }
    }

    // Create accounts
    public static void CreateAccounts() throws InterruptedException, CloudException, IOException {
        // Create ADLS account
        CreateDataLakeStoreAccountParameters adlsParameters = new CreateDataLakeStoreAccountParameters();
        adlsParameters.withLocation(_location);

        _adlsClient.accounts().create(_resourceGroupName, _adlsAccountName, adlsParameters);

        // Create ADLA account
        AddDataLakeStoreWithAccountParameters adlsInfo = new AddDataLakeStoreWithAccountParameters();
        adlsInfo.withName(_adlsAccountName);

        List<AddDataLakeStoreWithAccountParameters> adlsInfoList = new ArrayList<>();
        adlsInfoList.add(adlsInfo);

        CreateDataLakeAnalyticsAccountParameters adlaParameters = new CreateDataLakeAnalyticsAccountParameters();
        adlaParameters.withLocation(_location);
        adlaParameters.withDefaultDataLakeStoreAccount(_adlsAccountName);
        adlaParameters.withDataLakeStoreAccounts(adlsInfoList);

        _adlaClient.accounts().create(_resourceGroupName, _adlaAccountName, adlaParameters);
    }

    // Create a file
    public static void CreateFile(String path, String contents, boolean force) throws IOException, CloudException {
        byte[] bytesContents = contents.getBytes();

        ADLFileOutputStream stream = _adlsStoreClient.createFile(path, IfExists.OVERWRITE, "777", force);
        stream.write(bytesContents);
        stream.close();
    }

    // Delete a file
    public static void DeleteFile(String filePath) throws IOException, CloudException {
        _adlsStoreClient.delete(filePath);
    }

    // Download a file
    private static void DownloadFile(String srcPath, String destPath) throws IOException, CloudException {
        ADLFileInputStream stream = _adlsStoreClient.getReadStream(srcPath);

        PrintWriter pWriter = new PrintWriter(destPath, Charset.defaultCharset().name());

        String fileContents = "";
        if (stream != null) {
            Writer writer = new StringWriter();

            char[] buffer = new char[1024];
            try {
                Reader reader = new BufferedReader(
                        new InputStreamReader(stream, "UTF-8"));
                int n;
                while ((n = reader.read(buffer)) != -1) {
                    writer.write(buffer, 0, n);
                }
            } finally {
                stream.close();
            }
            fileContents = writer.toString();
        }

        pWriter.println(fileContents);
        pWriter.close();
    }

    // Submit a U-SQL job
    private static UUID SubmitJobByScript(String script, String jobName) throws IOException, CloudException {
        UUID jobId = java.util.UUID.randomUUID();
        CreateJobProperties properties = new CreateUSqlJobProperties();
        properties.withScript(script);
        CreateJobParameters parameters = new CreateJobParameters();
        parameters.withName(jobName);
        parameters.withType(JobType.USQL);
        parameters.withProperties(properties);

        JobInformation jobInfo = _adlaJobClient.jobs().create(_adlaAccountName, jobId, parameters);

        return jobInfo.jobId();
    }

    // Wait for job completion
    private static JobResult WaitForJob(UUID jobId) throws IOException, CloudException {
        JobInformation jobInfo = _adlaJobClient.jobs().get(_adlaAccountName, jobId);
        while (jobInfo.state() != JobState.ENDED) {
            jobInfo = _adlaJobClient.jobs().get(_adlaAccountName, jobId);
        }
        return jobInfo.result();
    }

    // Retrieve job status
    private static String GetJobStatus(UUID jobId) throws IOException, CloudException {
        JobInformation jobInfo = _adlaJobClient.jobs().get(_adlaAccountName, jobId);
        return jobInfo.state().toString();
    }
}
```

Fornecer os valores para os parâmetros indicados no corte de código:
* `localFolderPath`
* `_adlaAccountName`
* `_adlsAccountName`
* `_resourceGroupName`
* `_tenantId`
* `_subId`
* `_clientId`
* `_clientSecret`

## <a name="next-steps"></a>Passos seguintes

* Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL de Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md) e [Referência de linguagem U-SQL](https://docs.microsoft.com/u-sql/).
* Para tarefas de gestão, consulte [Manage Azure Data Lake Analytics utilizando o portal Azure](data-lake-analytics-manage-use-portal.md).
* Para uma descrição geral da Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).
