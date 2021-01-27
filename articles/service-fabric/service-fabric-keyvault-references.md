---
title: Tecido de serviço Azure - Utilização da aplicação de tecido de serviço Referências KeyVault
description: Este artigo explica como usar o suporte de teclado keyVaultReference de tecido de serviço para segredos de aplicação.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898601"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>Suporte keyVaultReference para aplicações de tecido de serviço implantadas pela Azure

Um desafio comum ao construir aplicações em nuvem é como distribuir segredos de forma segura às suas aplicações. Por exemplo, é melhor utilizar uma chave de base de dados da sua aplicação sem expor a chave durante o pipeline ou no operador. O suporte de teclado de tecido de serviço Torna fácil a implementação de segredos nas suas aplicações simplesmente fazendo referência ao URL do segredo que está armazenado no Key Vault. A Service Fabric tratará de obter esse segredo em nome da identidade gerida da sua aplicação e ativar a aplicação com o segredo.

> [!NOTE]
> O suporte keyVaultReference para aplicações de tecido de serviço é GA (fora de pré-visualização) começando com a versão 7.2 CU5 do Tecido de Serviço. Recomenda-se que faça o upgrade para esta versão antes de utilizar esta funcionalidade.

> [!NOTE]
> O suporte keyVaultReference para aplicações de tecido de serviço suporta apenas segredos [versados.](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) Segredos sem versão não são suportados. O Cofre-Chave tem de estar na mesma subscrição que o seu conjunto de tecidos de serviço. 

## <a name="prerequisites"></a>Pré-requisitos

- Identidade gerida para aplicações de tecido de serviço

    O suporte de teclas de tecido de serviço Utiliza a Identidade Gerida de uma aplicação para obter segredos em nome da aplicação, pelo que a sua aplicação deve ser implementada através e atribuída uma identidade gerida. Siga este [documento](concepts-managed-identity.md) para ativar a identidade gerida para a sua aplicação.

- Central Secrets Store (CSS).

    Central Secrets Store (CSS) é o cache de segredos locais encriptados do Service Fabric. Esta funcionalidade utiliza O CSS para proteger e persistir segredos depois de serem recolhidos do Key Vault. Ativar este serviço de sistema opcional também é necessário consumir esta funcionalidade. Siga este [documento](service-fabric-application-secret-store.md) para ativar e configurar o CSS.

- Autorização de acesso à identidade gerida pelo pedido de subvenção ao teclado

    Consulte este [documento](how-to-grant-access-other-resources.md) para ver como conceder acesso de identidade gerido ao keyvault. Note também que se estiver a utilizar o sistema de identidade gerida atribuído, a identidade gerida é criada apenas após a implementação da aplicação. Isto pode criar condições de corrida onde a aplicação tenta aceder ao segredo antes que a identidade possa ter acesso ao cofre. O nome da identidade atribuída ao sistema será `{cluster name}/{application name}/{service name}` .
    
## <a name="use-keyvaultreferences-in-your-application"></a>Use KeyVaultReferences na sua aplicação
KeyVaultReferências podem ser consumidas de várias maneiras
- [Como uma variável ambiental](#as-an-environment-variable)
- [Montado como um ficheiro no seu recipiente](#mounted-as-a-file-into-your-container)
- [Como referência a uma senha de repositório de contentores](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>Como uma variável ambiental

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>Montado como um ficheiro no seu recipiente

- Adicione uma secção ao settings.xml

    Definir `MySecret` parâmetro com Tipo e `KeyVaultReference` Valor `<KeyVaultURL>`

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- Referenciar a nova secção em ApplicationManifest.xml em `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Consumir os segredos do código de serviço

    Cada parâmetro listado será `<Section  Name=MySecrets>` um ficheiro sob a pasta apontada por EnvironmentVariable SecretPath. O snippet de código abaixo C# mostra como ler MySecret a partir da sua aplicação.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > O MountPoint controla a pasta onde serão montados os ficheiros que contêm valores secretos.

### <a name="as-a-reference-to-a-container-repository-password"></a>Como referência a uma senha de repositório de contentores

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>Próximos passos

* [Documentação Azure KeyVault](../key-vault/index.yml)
* [Saiba mais sobre a Central Secret Store](service-fabric-application-secret-store.md)
* [Saiba mais sobre identidade gerida para aplicações de tecido de serviço](concepts-managed-identity.md)
