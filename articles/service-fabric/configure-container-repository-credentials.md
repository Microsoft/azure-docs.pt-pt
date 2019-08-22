---
title: Service Fabric do Azure – configurar credenciais de repositório de contêiner | Microsoft Docs
description: Configurar credenciais de repositório para baixar imagens do registro de contêiner
services: service-fabric
documentationcenter: .net
author: arya
manager: gkhanna
ms.assetid: b93d31e5-9e4c-4405-b266-c0efa4643d97
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 8/1/2019
ms.author: arya
ms.openlocfilehash: cfe212a150da0e5828f48de3bf2692ab2a44c672
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69657167"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Configurar credenciais de repositório para seu aplicativo para baixar imagens de contêiner

Configure a autenticação do registo de contentor ao adicionar `RepositoryCredentials` a `ContainerHostPolicies` do ficheiro ApplicationManifest.xml. Adicione a conta e a palavra-passe do registo de contentor myregistry.azurecr.io, o que permite ao serviço transferir a imagem de contentor a partir do repositório.

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

Esse recurso pode ser habilitado ou desabilitado adicionando o `UseDefaultRepositoryCredentials` atributo a `ContainerHostPolicies` em ApplicationManifest. XML com um `true` valor `false` ou.

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

Em seguida, Service Fabric usa as credenciais de repositório padrão que podem ser especificadas no ClusterManifest `Hosting` na seção.  Se `UseDefaultRepositoryCredentials` for`true`, Service Fabric lerá os seguintes valores do ClusterManifest:

* DefaultContainerRepositoryAccountName (string)
* DefaultContainerRepositoryPassword (cadeia de caracteres)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (cadeia de caracteres)---com suporte a partir do tempo de execução 6,4

Aqui está um exemplo do que pode ser adicionado dentro da `Hosting` seção no arquivo ClusterManifestTemplate. JSON. A `Hosting` seção pode ser adicionada na criação do cluster ou posteriormente em uma atualização de configuração. Para obter mais informações, consulte [alterar configurações de cluster do azure Service Fabric](service-fabric-cluster-fabric-settings.md) e [gerenciar segredos do aplicativo de Service Fabric do Azure](service-fabric-application-secret-management.md)

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

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>Aproveitando a identidade gerenciada do conjunto de dimensionamento de máquinas virtuais usando o MSI (serviço de identidade gerenciada)

O Service Fabric dá suporte ao uso de tokens como credenciais para baixar imagens para seus contêineres.  Esse recurso aproveita a identidade gerenciada do conjunto de dimensionamento de máquinas virtuais subjacente para autenticar no registro, eliminando a necessidade de gerenciar as credenciais do usuário.  Consulte [identidade de serviço gerenciada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) para obter mais informações sobre o MSI.  O uso desse recurso requer as seguintes etapas:

1.  Verifique se a identidade gerenciada atribuída pelo sistema está habilitada para a VM (Confira a captura de tela abaixo)

    ![Criar identidade do conjunto de dimensionamento de máquinas virtuais](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  Depois disso, conceda permissões à VM (SS) para extrair/ler imagens do registro.  Vá para controle de acesso (IAM) de seu ACR por meio da folha do Azure e dê à sua VM (SS) as permissões corretas, conforme mostrado abaixo:

    ![Adicionar entidade de segurança de VM ao ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  Depois que as etapas acima forem concluídas, modifique o arquivo ApplicationManifest. xml.  Localize a marca rotulada "ContainerHostPolicies" e adicione o atributo `‘UseTokenAuthenticationCredentials=”true”`.

    ```json
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

## <a name="next-steps"></a>Passos Seguintes

* Veja mais sobre a [autenticação do registro de contêiner](/azure/container-registry/container-registry-authentication).
