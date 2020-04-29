---
title: Tecido de serviço Azure - Configure credenciais de repositório de recipientes
description: Configure credenciais de repositório para descarregar imagens do registo de contentores
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76934983"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Configure credenciais de repositório para a sua aplicação para descarregar imagens de contentores

Configure a autenticação do `RepositoryCredentials` registo `ContainerHostPolicies` de contentores adicionando à secção do seu manifesto de candidatura. Adicione a conta e a palavra-passe para o seu registo de contentores *(myregistry.azurecr.io* no exemplo abaixo), o que permite ao serviço descarregar a imagem do recipiente do repositório.

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

Recomenda-se que criptografe a palavra-passe do repositório utilizando um certificado de codificação que é implantado em todos os nós do cluster. Quando o Service Fabric implementa o pacote de serviço no cluster, o certificado de encriptação é utilizado para desencriptar o texto de cifra. O cmdlet Invoke-ServiceFabricEncryptText é utilizado para criar o texto de cifra para a palavra-passe, que é adicionado ao ficheiro ApplicationManifest.xml.
Consulte a [Secret Management](service-fabric-application-secret-management.md) para mais informações sobre certificados e semântica de encriptação.

## <a name="configure-cluster-wide-credentials"></a>Configure credenciais em todo o cluster

O Tecido de Serviço permite-lhe configurar credenciais de cluster que podem ser usadas como credenciais de repositório padrão por aplicações.

Esta funcionalidade pode ser ativada ou `UseDefaultRepositoryCredentials` desativada adicionando o atributo em `ContainerHostPolicies` ApplicationManifest.xml com um `true` ou `false` valor.

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

O Tecido de Serviço utiliza então as credenciais de repositório predefinidos que podem ser especificadas no ClusterManifest sob a `Hosting` secção.  Se `UseDefaultRepositoryCredentials` `true`estiver, o Tecido de Serviço lê os seguintes valores do ClusterManifest:

* PadrãoContainerRepositoryAccountName (cadeia)
* Palavra-passe por Predefinição DeRepositório de DefaultContainer (cadeia)
* IsDefaultContainerRepositoryPasswordEncriptado (bool)
* PredefiniçãoDeRepositoryPasswordType (cadeia)

Aqui está um exemplo do que `Hosting` pode ser adicionado dentro da secção no ficheiro ClusterManifestTemplate.json. A `Hosting` secção pode ser adicionada na criação de cluster ou posteriormente numa atualização de configuração. Para mais informações, consulte as definições de cluster do [tecido de serviço Change Azure](service-fabric-cluster-fabric-settings.md) e gere os segredos de [aplicação do Tecido de Serviço Azure](service-fabric-application-secret-management.md)

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

Service Fabric suporta usar fichas como credenciais para descarregar imagens para os seus contentores.  Esta funcionalidade aproveita a *identidade gerida* da escala de máquina virtual subjacente para autenticar o registo, eliminando a necessidade de gestão das credenciais do utilizador.  Consulte [identidades geridas para recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) para obter mais informações.  A utilização desta função requer os seguintes passos:

1. Certifique-se de que o *Sistema De Identidade Gerida Atribuído* está ativado para o VM.

    ![Portal Azure: Criar a opção de identidade de conjunto de máquina virtual](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Conceda permissões à escala de máquina virtual definida para puxar/ler imagens do registo. A partir da lâmina de Controlo de Acesso (IAM) do seu Registo de Contentores Azure no portal Azure, adicione uma atribuição de *funções* para a sua máquina virtual:

    ![Adicione o principal VM ao ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Em seguida, modifique o seu manifesto de candidatura. Na `ContainerHostPolicies` secção, adicione o `‘UseTokenAuthenticationCredentials=”true”`atributo .

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
    > A `UseDefaultRepositoryCredentials` bandeira definida `UseTokenAuthenticationCredentials` para ser verdade enquanto é verdade causará um erro durante a implantação.

## <a name="next-steps"></a>Passos seguintes

* Veja mais sobre a autenticação do registo de [contentores.](../container-registry/container-registry-authentication.md)
