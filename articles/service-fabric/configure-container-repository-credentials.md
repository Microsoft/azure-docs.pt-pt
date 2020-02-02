---
title: Service Fabric do Azure – configurar credenciais do repositório de contêiner
description: Configurar credenciais de repositório para baixar imagens do registro de contêiner
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76934983"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Configurar credenciais de repositório para seu aplicativo para baixar imagens de contêiner

Configure a autenticação do registo de contentores adicionando `RepositoryCredentials` à secção `ContainerHostPolicies` do seu manifesto de candidatura. Adicione a conta e a palavra-passe para o seu registo de contentores *(myregistry.azurecr.io* no exemplo abaixo), o que permite ao serviço descarregar a imagem do recipiente do repositório.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

É recomendável criptografar a senha do repositório usando um certificado de codificação que é implantado em todos os nós do cluster. Quando o Service Fabric implementa o pacote de serviço no cluster, o certificado de encriptação é utilizado para desencriptar o texto de cifra. O cmdlet Invoke-ServiceFabricEncryptText é utilizado para criar o texto de cifra para a palavra-passe, que é adicionado ao ficheiro ApplicationManifest.xml.
Consulte [Gerenciamento de segredos](service-fabric-application-secret-management.md) para obter mais informações sobre certificados e semântica de criptografia.

## <a name="configure-cluster-wide-credentials"></a>Configurar credenciais em todo o cluster

Service Fabric permite que você configure credenciais em todo o cluster que podem ser usadas como credenciais de repositório padrão por aplicativos.

Esse recurso pode ser habilitado ou desabilitado pela adição do atributo `UseDefaultRepositoryCredentials` para `ContainerHostPolicies` em ApplicationManifest. XML com um valor `true` ou `false`.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Em seguida, Service Fabric usa as credenciais de repositório padrão que podem ser especificadas no ClusterManifest na seção `Hosting`.  Se `UseDefaultRepositoryCredentials` for `true`, Service Fabric lerá os seguintes valores do ClusterManifest:

* DefaultContainerRepositoryAccountName (string)
* DefaultContainerRepositoryPassword (cadeia de caracteres)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* PredefiniçãoDeRepositoryPasswordType (cadeia)

Aqui está um exemplo do que pode ser adicionado dentro da seção `Hosting` no arquivo ClusterManifestTemplate. JSON. A seção `Hosting` pode ser adicionada na criação do cluster ou posteriormente em uma atualização de configuração. Para obter mais informações, consulte [alterar configurações de cluster do azure Service Fabric](service-fabric-cluster-fabric-settings.md) e [gerenciar segredos do aplicativo de Service Fabric do Azure](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Use fichas como credenciais de registo

O Service Fabric dá suporte ao uso de tokens como credenciais para baixar imagens para seus contêineres.  Esta funcionalidade aproveita a *identidade gerida* da escala de máquina virtual subjacente para autenticar o registo, eliminando a necessidade de gestão das credenciais do utilizador.  Consulte [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) para obter mais informações.  O uso desse recurso requer as seguintes etapas:

1. Certifique-se de que o *Sistema De Identidade Gerida Atribuído* está ativado para o VM.

    ![Portal Azure: Criar a opção de identidade de conjunto de máquina virtual](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Conceda permissões à escala de máquina virtual definida para puxar/ler imagens do registo. A partir da lâmina de Controlo de Acesso (IAM) do seu Registo de Contentores Azure no portal Azure, adicione uma atribuição de *funções* para a sua máquina virtual:

    ![Adicionar entidade de segurança de VM ao ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Em seguida, modifique o seu manifesto de candidatura. Na secção `ContainerHostPolicies`, adicione o atributo `‘UseTokenAuthenticationCredentials=”true”`.

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > O sinalizador `UseDefaultRepositoryCredentials` definido como true enquanto `UseTokenAuthenticationCredentials` for true causará um erro durante a implantação.

## <a name="next-steps"></a>Passos seguintes

* Veja mais sobre a [autenticação do registro de contêiner](../container-registry/container-registry-authentication.md).
