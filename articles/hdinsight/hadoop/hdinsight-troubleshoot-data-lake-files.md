---
title: Não é possível aceder a ficheiros de armazenamento do Data Lake em Azure HDInsight
description: Não é possível aceder a ficheiros de armazenamento do Data Lake em Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/13/2019
ms.openlocfilehash: f4c5a23b604334952730fcc4cf1fcb3fcbed6237
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944390"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Não é possível aceder a ficheiros de armazenamento do Data Lake em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

## <a name="issue-acl-verification-failed"></a>Problema: A verificação da ACL falhou

Recebe uma mensagem de erro semelhante a:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Causa

O utilizador pode ter revogado permissões do principal serviço (SP) em ficheiros/pastas.

### <a name="resolution"></a>Resolução

1. Verifique se o SP tem permissões 'x' para atravessar ao longo do caminho. Para mais informações, consulte [permissões.](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html) Comando de amostra `dfs` para verificar o acesso a ficheiros/pastas na conta de armazenamento do Data Lake:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Configurar as permissões necessárias para aceder ao caminho com base na operação de leitura/escrita que está a ser realizada. Consulte aqui as permissões necessárias para várias operações do sistema de ficheiros.

---

## <a name="issue-service-principal-certificate-expiry"></a>Emissão: Expiração do certificado principal de serviço

Recebe uma mensagem de erro semelhante a:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Causa

O certificado previsto para o acesso principal do Serviço pode ter expirado.

1. SSH em cabeçanode. Verifique o acesso à conta de armazenamento utilizando o seguinte `dfs` comando:

    ```
    hdfs dfs -ls /
    ```

1. Confirme que a mensagem de erro é semelhante à seguinte saída:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. Pegue um dos urls de `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls` .

1. Executar o seguinte comando de caracóis para recuperar o símbolo de OAuth.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. A saída para um principal de serviço válido deve ser algo como:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Se o certificado principal de serviço tiver expirado, a saída será semelhante a isto:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Quaisquer outros erros relacionados com o Azure Ative Directory/erros relacionados com o certificado podem ser reconhecidos através do pinging do url gateway para obter o token OAuth.

1. Se estiver a obter o seguinte erro ao tentar aceder ao ADLS a partir do Cluster HDI. Verifique se o Certificado expirou seguindo os passos acima mencionados.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>Resolução

Crie um novo Certificado ou atribua o certificado existente utilizando o seguinte script PowerShell:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

Para a atribuição do certificado existente, crie um certificado, tenha o ficheiro .pfx e a palavra-passe pronta. Associe o certificado ao principal de serviço com o que o cluster foi criado, utilizando o AppId pronto.

Execute o comando PowerShell depois de substituir os parâmetros pelos valores reais.

## <a name="next-steps"></a>Próximos passos

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]