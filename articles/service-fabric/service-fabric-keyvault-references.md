---
title: Tecido de serviço Azure - Utilização de referências keyVault de aplicação de tecido de serviço
description: Este artigo explica como usar suporte keyVaultReference de tecido de serviço para segredos de aplicação.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f7d8a083ea5ec4b66c29d392ee98927915465875
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76545488"
---
#  <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Suporte KeyVaultReference para aplicações de tecido de serviço (pré-visualização)

Um desafio comum ao construir aplicações em nuvem é como armazenar de forma segura os segredos exigidos pela sua aplicação. Por exemplo, é melhor armazenar as credenciais de repositório de contentores no cofre de chaves e remetê-la no manifesto de aplicação. Chave de serviçoVaultReference utiliza identidade gerida por tecido de serviço e facilita a referência a segredos do cofre de chaves. O restante deste artigo detalha como usar o Service Fabric KeyVaultReference e inclui alguma utilização típica.

## <a name="prerequisites"></a>Pré-requisitos

- Identidade Gerida para Aplicação (MIT)
    
    O suporte de chave de tecido de serviçoVaultReference utiliza a Identidade Gerida da aplicação e, portanto, as aplicações que planeiam utilizar keyVaultReferences devem utilizar a Identidade Gerida. Siga este [documento](concepts-managed-identity.md) para ativar a identidade gerida para a sua aplicação.

- Central Secrets Store (CSS).

    Central Secrets Store (CSS) é a cache de segredos locais encriptado da Service Fabric. CSS é uma cache de loja secreta local que guarda dados confidenciais, tais como uma palavra-passe, fichas e chaves, encriptadas na memória. KeyVaultReference, uma vez recolhido, são cached em CSS.

    Adicione a abaixo à `fabricSettings` configuração do cluster abaixo para ativar todas as funcionalidades necessárias para o suporte KeyVaultReference.

    ```json
    "fabricSettings": 
    [
        ...
    {
                "name":  "CentralSecretService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
                ]
            },
            {
                "name":  "ManagedIdentityTokenService",
                "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                }
                ]
            }
            ]
    ```

    > [!NOTE] 
    > É recomendado usar um certificado de encriptação separado para CSS. Pode adicioná-lo na secção "CentralSecretService".
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
Para que as alterações entrem em vigor, também terá de alterar a política de atualização para especificar um reinício vigoroso do tempo de execução do Tecido de Serviço em cada nó à medida que a atualização progride através do cluster. Este reinício garante que o serviço de sistema recém-activado é iniciado e funcionando em cada nó. No corte abaixo, forceRestart é a definição essencial; utilize os valores existentes para o resto das definições.
```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```
- Autorização de acesso de identidade gerida pelo pedido de subvenção ao cofre de chaves

    Consulte este [documento](how-to-grant-access-other-resources.md) para ver como conceder acesso de identidade gerido ao keyvault. Note também que se estiver a utilizar o Sistema De Identidade Gerida Atribuída, a identidade gerida só é criada após a implementação da aplicação.

## <a name="keyvault-secret-as-application-parameter"></a>Segredo do cofre como parâmetro de aplicação
Digamos que a aplicação precisa de ler a palavra-passe da base de dados de backend armazenada no cofre do keyvault, suporte de keyvaultde de tecido de serviço facilita. Abaixo lê-se `DBPassword` o segredo do cofre de chaves utilizando suporte de referência de chave de tecido de serviçoVaultReference.

- Adicione uma secção às definições.xml

    Definir `DBPassword` parâmetro com `KeyVaultReference` tipo e valor`<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Consulte a nova secção em ApplicationManifest.xml em`<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="ttkappuser" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Utilização de KeyVaultReference na sua aplicação

    O Tecido de Serviço na instantiação do serviço resolverá o parâmetro KeyVaultReference utilizando a identidade gerida da aplicação. Cada parâmetro listado `<Section  Name=dbsecrets>` em baixo será um ficheiro sob a pasta indicada pelo EnvironmentVariable SecretPath. Abaixo c# código snippet mostra como ler DBPassword na sua aplicação.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Para o cenário do recipiente, pode utilizar `secrets` o MountPoint para controlar onde o será montado.

## <a name="keyvault-secret-as-environment-variable"></a>Segredo do cofre como variável ambiental

As variáveis ambientais do tecido de serviço suportam agora o tipo KeyVaultReference, abaixo do exemplo mostra como ligar uma variável ambiental a um segredo armazenado no KeyVault.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Segredo do cofre como palavra-passe de repositório de contentores
KeyVaultReference é um tipo suportado para repositórios de recipientes Credenciais, abaixo do exemplo mostra como usar uma referência de cofre de chave como palavra-passe de repositório de recipientes.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>FAQ
- A identidade gerida precisa de ser ativada para o suporte keyVaultReference, a sua ativação de aplicação falhará se o KeyVaultReference for utilizado sem ativar a Identidade Gerida.

- Se estiver a usar a identidade atribuída ao sistema, só é criada após a implementação da aplicação e isso cria uma dependência circular. Assim que a sua aplicação for implementada, pode conceder ao sistema a permissão de acesso de identidade atribuída ao cofre de chaves. Pode encontrar a identidade atribuída pelo nome {cluster}/{nome de serviço}/{servicename}

- O cofre precisa de estar na mesma subscrição que o seu cluster de tecido de serviço. 

## <a name="next-steps"></a>Passos seguintes

* [Documentação Azure KeyVault](https://docs.microsoft.com/azure/key-vault/)
