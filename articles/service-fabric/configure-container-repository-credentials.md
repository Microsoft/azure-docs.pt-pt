---
title: Tecido de Serviço Azure - Credenciais de repositório de contentores configurantes
description: Configure credenciais de repositório para descarregar imagens do registo de contentores
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89421429"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Configure credenciais de repositório para a sua aplicação para descarregar imagens de contentores

Configure a autenticação do registo do contentor adicionando `RepositoryCredentials` à secção do seu manifesto de `ContainerHostPolicies` candidatura. Adicione a conta e a palavra-passe para o seu registo de contentores *(myregistry.azurecr.io* no exemplo abaixo), que permite ao serviço descarregar a imagem do recipiente a partir do repositório.

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

Recomenda-se que criptografe a palavra-passe do repositório utilizando um certificado de cifra que é implantado em todos os nós do cluster. Quando o Service Fabric implementa o pacote de serviço no cluster, o certificado de encriptação é utilizado para desencriptar o texto de cifra. O cmdlet Invoke-ServiceFabricEncryptText é utilizado para criar o texto de cifra para a palavra-passe, que é adicionado ao ficheiro ApplicationManifest.xml.
Consulte [a Gestão Secreta](service-fabric-application-secret-management.md) para obter mais informações sobre certificados e semântica de encriptação.

## <a name="configure-cluster-wide-credentials"></a>Configure credenciais em todo o cluster

O Tecido de Serviço permite-lhe configurar credenciais em todo o cluster que podem ser usadas como credenciais de repositório padrão por aplicações.

Esta funcionalidade pode ser ativada ou desativada adicionando o `UseDefaultRepositoryCredentials` atributo em ApplicationManifest.xml com um ou `ContainerHostPolicies` `true` `false` valor.

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

O Tecido de Serviço utiliza então as credenciais de repositório padrão que podem ser especificadas no ClusterManifest sob a `Hosting` secção.  Se `UseDefaultRepositoryCredentials` `true` for, o Service Fabric lê os seguintes valores do ClusterManifest:

* DefaultContainerRepositoryAccountName (cadeia)
* DefaultContainerRepositoryPassword (cadeia)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (cadeia)

Aqui está um exemplo do que pode ser adicionado dentro da `Hosting` secção na ClusterManifestTemplate.jsarquivado. A `Hosting` secção pode ser adicionada na criação do cluster ou posteriormente numa atualização de configuração. Para mais informações, consulte [as definições do cluster do change Azure Service Fabric](service-fabric-cluster-fabric-settings.md) e [gerencie os segredos da aplicação do Tecido de Serviço Azure](service-fabric-application-secret-management.md)

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

O Service Fabric suporta o uso de tokens como credenciais para descarregar imagens para os seus contentores.  Esta funcionalidade aproveita a *identidade gerida* da escala de máquina virtual subjacente definida para autenticar o registo, eliminando a necessidade de gestão das credenciais dos utilizadores.  Consulte [identidades geridas para obter recursos Azure](../active-directory/managed-identities-azure-resources/overview.md) para obter mais informações.  A utilização desta função requer os seguintes passos:

1. Certifique-se de que *a identidade gerida atribuída* pelo sistema está ativada para o VM.

    ![Portal Azure: Criar opção de identidade definida em escala de máquina virtual](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Conceder permissões à escala de máquina virtual definida para retirar/ler imagens do registo. A partir da lâmina do Controlo de Acesso (IAM) do seu Registo de Contentores Azure no portal Azure, adicione uma *atribuição de função* para a sua máquina virtual:

    ![Adicione o principal VM ao ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Em seguida, modifique o seu manifesto de candidatura. Na `ContainerHostPolicies` secção, adicione o atributo `‘UseTokenAuthenticationCredentials=”true”` .

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
    > A bandeira `UseDefaultRepositoryCredentials` definida como verdadeira, enquanto `UseTokenAuthenticationCredentials` é verdadeira, causará um erro durante a implantação.

## <a name="next-steps"></a>Passos seguintes

* Veja mais sobre [a autenticação do registo do contentor.](../container-registry/container-registry-authentication.md)
