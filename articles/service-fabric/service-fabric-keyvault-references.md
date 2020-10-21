---
title: Tecido de serviço Azure - Utilização da aplicação de tecido de serviço Referências KeyVault
description: Este artigo explica como usar o suporte de teclado keyVaultReference de tecido de serviço para segredos de aplicação.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: f2221bb3e8e3ee3181b2cff70107dccc203954cf
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92313799"
---
# <a name="keyvaultreference-support-for-service-fabric-applications-preview"></a>Suporte keyVaultReference para aplicações de Tecido de Serviço (pré-visualização)

Um desafio comum ao construir aplicações em nuvem é como armazenar de forma segura os segredos exigidos pela sua aplicação. Por exemplo, é melhor guardar as credenciais de repositório do recipiente no teclado e faz referência a ela no manifesto de aplicação. Service Fabric KeyVaultReference usa A Identidade Gerida do Tecido de Serviço e facilita a referência aos segredos de teclado. O restante deste artigo detalha como usar o Service Fabric KeyVaultReference e inclui alguma utilização típica.

> [!IMPORTANT]
> A utilização desta função de pré-visualização não é recomendada em ambientes de produção.

> [!NOTE]
> A funcionalidade de pré-visualização de referência keyVault suporta apenas segredos [versados.](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) Segredos sem versão não são suportados.

## <a name="prerequisites"></a>Pré-requisitos

- Identidade gerida para aplicação (MIT)
    
    O suporte de teclas de tecido de serviçoVaultReference utiliza a Identidade Gerida da aplicação e, portanto, as aplicações que planeiam utilizar KeyVaultReferences devem utilizar identidade gerida. Siga este [documento](concepts-managed-identity.md) para ativar a identidade gerida para a sua aplicação.

- Central Secrets Store (CSS).

    Central Secrets Store (CSS) é o cache de segredos locais encriptados do Service Fabric. CSS é uma cache de loja secreta local que mantém dados sensíveis, tais como uma palavra-passe, fichas e chaves, encriptados na memória. KeyVaultReference, uma vez recolhido, está em cache em CSS.

    Adicione o abaixo à configuração do cluster `fabricSettings` para ativar todas as funcionalidades necessárias para o suporte keyVaultReference.

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
    > Recomenda-se a utilização de um certificado de encriptação separado para CSS. Pode adicioná-lo na secção "CentralSecretService".
    

    ```json
        {
            "name": "EncryptionCertificateThumbprint",
            "value": "<EncryptionCertificateThumbprint for CSS>"
        }
    ```
Para que as alterações entrem em vigor, também terá de alterar a política de atualização para especificar um reinício vigoroso do tempo de funcionamento do Tecido de Serviço em cada nó à medida que a atualização progride através do cluster. Este reinício garante que o serviço de sistema recentemente ativado seja iniciado e em funcionamento em cada nó. No corte abaixo, forceRestart é o cenário essencial; utilize os valores existentes para o resto das definições.
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
- Autorização de acesso à identidade gerida pelo pedido de subvenção ao teclado

    Consulte este [documento](how-to-grant-access-other-resources.md) para ver como conceder acesso de identidade gerido ao keyvault. Note também que se estiver a utilizar a Identidade Gerida Atribuída do Sistema, a identidade gerida é criada apenas após a implementação da aplicação.

## <a name="keyvault-secret-as-application-parameter"></a>Segredo keyvault como parâmetro de aplicação
Digamos que a aplicação precisa de ler a palavra-passe da base de dados de backend armazenada no teclado, o suporte de teclado de tecido de serviço Torna-a mais fácil. A seguir, o exemplo `DBPassword` lê-se em segredo do keyvault utilizando o suporte de teclado do tecido de serviçoVaultReferência.

- Adicione uma secção ao settings.xml

    Definir `DBPassword` parâmetro com Tipo e `KeyVaultReference` Valor `<KeyVaultURL>`

    ```xml
    <Section Name="dbsecrets">
        <Parameter Name="DBPassword" Type="KeyVaultReference" Value="https://vault200.vault.azure.net/secrets/dbpassword/8ec042bbe0ea4356b9b171588a8a1f32"/>
    </Section>
    ```
- Referenciar a nova secção em ApplicationManifest.xml em `<ConfigPackagePolicies>`

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

    O tecido de serviço na instantânea de serviço resolverá o Parâmetro KeyVaultReference utilizando a identidade gerida da aplicação. Cada parâmetro listado será `<Section  Name=dbsecrets>` um ficheiro sob a pasta apontada por EnvironmentVariable SecretPath. Abaixo C# o código snippet mostra como ler DBPassword na sua aplicação.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "DBPassword"))) 
    {
        string dbPassword =  sr.ReadToEnd();
        // dbPassword to connect to DB
    }
    ```
    > [!NOTE] 
    > Para o cenário do recipiente, pode utilizar o MountPoint para controlar onde o `secrets` será montado.

## <a name="keyvault-secret-as-environment-variable"></a>Segredo keyvault como variável ambiental

As variáveis ambientais do tecido de serviço agora suportam o tipo KeyVaultReference, abaixo o exemplo mostra como ligar uma variável ambiental a um segredo armazenado em KeyVault.

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="EventStorePassword" Type="KeyVaultReference" Value="https://ttkvault.vault.azure.net/secrets/clustercert/e225bd97e203430d809740b47736b9b8"/>
</EnvironmentVariables>
```

```C#
string eventStorePassword =  Environment.GetEnvironmentVariable("EventStorePassword");
```
## <a name="keyvault-secret-as-container-repository-password"></a>Segredo keyvault como senha de repositório de contentores
KeyVaultReference é um tipo suportado para repositórios de contentores, abaixo o exemplo mostra como usar uma referência keyvault como senha de repositório de contentores.
```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="user1" Type="KeyVaultReference" Password="https://ttkvault.vault.azure.net/secrets/containerpwd/e225bd97e203430d809740b47736b9b8"/>
      </ContainerHostPolicies>
```
## <a name="faq"></a>FAQ
- A identidade gerida precisa de ser ativada para suporte à KeyVaultReference, a ativação da sua aplicação falhará se a KeyVaultReference for utilizada sem permitir a Identidade Gerida.

- Se estiver a usar a identidade atribuída ao sistema, só é criada depois de a aplicação ser implementada e isso cria uma dependência circular. Uma vez que a sua aplicação é implementada, você pode conceder ao sistema permissão de acesso de identidade atribuído ao keyvault. Pode encontrar a identidade atribuída pelo sistema {cluster}/{nome da aplicação}/{nome de serviço}

- O keyvault precisa de estar na mesma subscrição que o seu cluster de tecido de serviço. 

## <a name="next-steps"></a>Passos seguintes

* [Documentação Azure KeyVault](../key-vault/index.yml)