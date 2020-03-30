---
title: Serviços web seguros usando TLS
titleSuffix: Azure Machine Learning
description: Saiba como ativar https de forma a garantir um serviço web que é implantado através do Azure Machine Learning. O Azure Machine Learning utiliza a versão TLS 1.2 para proteger os modelos implementados como serviços web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: a58b0120feaba907c62bc646f4f85d9185227fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80287344"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Use tLS para garantir um serviço web através de Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo mostra-lhe como garantir um serviço web que é implantado através do Azure Machine Learning.

Utiliza o [HTTPS](https://en.wikipedia.org/wiki/HTTPS) para restringir o acesso aos serviços web e proteger os dados que os clientes submetem. HTTPS ajuda a garantir comunicações entre um cliente e um serviço web encriptando comunicações entre os dois. A encriptação utiliza segurança da [camada de transporte (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS é por vezes ainda referido como *Secure Sockets Layer* (SSL), que foi o antecessor do TLS.

> [!TIP]
> O Azure Machine Learning SDK utiliza o termo "SSL" para propriedades relacionadas com comunicações seguras. Isto não significa que o seu serviço web não use *TLS*. SSL é apenas um termo mais comumente reconhecido.
>
> Especificamente, os serviços web implantados através do Azure Machine Learning suportam apenas a versão TLS 1.2.

TLS e SSL dependem ambos de *certificados digitais,* que ajudam na encriptação e na verificação de identidade. Para obter mais informações sobre como funcionam os certificados digitais, consulte o tema wikipédia [Infraestrutura de chave pública.](https://en.wikipedia.org/wiki/Public_key_infrastructure)

> [!WARNING]
> Se não utilizar HTTPS para o seu serviço web, os dados enviados de e para o serviço podem ser visíveis para outros na internet.
>
> Https também permite ao cliente verificar a autenticidade do servidor a que está ligado. Esta funcionalidade protege os clientes contra ataques [man-in-the-middle.](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)

Este é o processo geral para garantir um serviço web:

1. Arranja um nome de domínio.

2. Pegue um certificado digital.

3. Implementar ou atualizar o serviço web com tLS ativado.

4. Atualize o seu DNS para apontar para o serviço web.

> [!IMPORTANT]
> Se estiver a ser implantado no Serviço Azure Kubernetes (AKS), pode adquirir o seu próprio certificado ou utilizar um certificado fornecido pela Microsoft. Se utilizar um certificado da Microsoft, não precisa de obter um nome de domínio ou certificado TLS/SSL. Para mais informações, consulte o [Enable TLS e implemente](#enable) a secção deste artigo.

Há pequenas diferenças quando se protege através dos alvos de [implantação](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Obter um nome de domínio

Se ainda não possuir um nome de domínio, compre um a partir de um registo de *nome de domínio.* O processo e o preço diferem entre os conservadores. O registrador fornece ferramentas para gerir o nome de domínio. Utiliza estas ferramentas para mapear um nome de domínio totalmente\.qualificado (FQDN) (como www contoso.com) para o endereço IP que acolhe o seu serviço web.

## <a name="get-a-tlsssl-certificate"></a>Obtenha um certificado TLS/SSL

Existem muitas formas de obter um certificado TLS/SSL (certificado digital). O mais comum é comprar um a uma autoridade de *certificados* (CA). Independentemente de onde obtenha o certificado, precisa dos seguintes ficheiros:

* Um **certificado.** O certificado deve conter a cadeia de certificados completa, devendo ser "codificada por PEM".
* Uma **chave.** A chave também deve ser codificada por PEM.

Quando solicita um certificado, deve fornecer ao FQDN o endereço que pretende utilizar para\.o serviço web (por exemplo, www contoso.com). O endereço que está estampado no certificado e o endereço que os clientes usam são comparados para verificar a identidade do serviço web. Se esses endereços não corresponderem, o cliente recebe uma mensagem de erro.

> [!TIP]
> Se a autoridade do certificado não puder fornecer o certificado e a chave como ficheiros codificados por PEM, pode utilizar um utilitário como o [OpenSSL](https://www.openssl.org/) para alterar o formato.

> [!WARNING]
> Utilize certificados *auto-assinados* apenas para desenvolvimento. Não os use em ambientes de produção. Os certificados auto-assinados podem causar problemas nas suas aplicações de clientes. Para mais informações, consulte a documentação para as bibliotecas de rede que a sua aplicação de cliente utiliza.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a>Ativar TLS e implementar

Para implantar (ou reimplantar) o serviço com TLS ativado, defina o *parâmetro ssl_enabled* para "True" onde quer que seja aplicável. Defina o parâmetro *ssl_certificate* ao valor do ficheiro de *certificado.* Detete o *ssl_key* ao valor do ficheiro *chave.*

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Implantação em AKS e matriz de porta programável em campo (FPGA)

  > [!NOTE]
  > As informações nesta secção também se aplicam quando implementa um serviço web seguro para o designer. Se não está familiarizado com o uso do Python SDK, veja [o que é o SDK de Aprendizagem automática Azure para Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)

Quando se desdobra no AKS, pode criar um novo cluster AKS ou anexar um existente. Para obter mais informações sobre a criação ou ligação de um cluster, consulte [Implementar um modelo para um cluster de serviço Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md).
  
-  Se criar um novo cluster, utilize **[aksCompute.provisioning_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)**.
- Se anexar um cluster existente, **[utiliza-se aksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)**. Ambos devolvem um objeto de configuração que tem um método **enable_ssl.**

O método **enable_ssl** pode usar um certificado fornecido pela Microsoft ou um certificado que você compra.

  * Quando utilizar um certificado da Microsoft, deve utilizar o parâmetro *leaf_domain_label.* Este parâmetro gera o nome DNS para o serviço. Por exemplo, um valor de "contoso" cria\<um nome de domínio de "contoso seis caracteres aleatórios>. \<azureregion>.cloudapp.azure.com", \<onde a região azul> é a região que contém o serviço. Opcionalmente, pode utilizar o parâmetro *overwrite_existing_domain* para substituir a *leaf_domain_label*existente .

    Para implantar (ou reimplantar) o serviço com TLS ativado, defina o *parâmetro ssl_enabled* para "True" onde quer que seja aplicável. Defina o parâmetro *ssl_certificate* ao valor do ficheiro de *certificado.* Detete o *ssl_key* ao valor do ficheiro *chave.*

    > [!IMPORTANT]
    > Quando utiliza um certificado da Microsoft, não precisa de adquirir o seu próprio certificado ou nome de domínio.

    O exemplo que se segue demonstra como criar uma configuração que permite um certificado TLS/SSL da Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.net"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```

  * Quando utiliza *um certificado que adquiriu,* utiliza os *parâmetros ssl_cert_pem_file,* *ssl_key_pem_file*e *ssl_cname.* O exemplo que se segue demonstra como utilizar ficheiros *.pem* para criar uma configuração que utiliza um certificado TLS/SSL que adquiriu:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Para obter mais informações sobre *enable_ssl*, consulte [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) e [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Implantação em instâncias de contentores azure

Quando se implanta em Instâncias de Contentores Azure, fornece valores para parâmetros relacionados com TLS, como mostra o seguinte código:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Para mais informações, consulte [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Atualize o seu DNS

Em seguida, tem de atualizar o seu DNS para apontar para o serviço web.

+ **Para casos de contentores:**

  Utilize as ferramentas do registo de nome de domínio para atualizar o registo DNS para o seu nome de domínio. O registo deve indicar o endereço IP do serviço.

  Pode haver um atraso de minutos ou horas antes que os clientes possam resolver o nome de domínio, dependendo do registrador e do "tempo para viver" (TTL) que está configurado para o nome de domínio.

+ **Para aks:**

  > [!WARNING]
  > Se usou *leaf_domain_label* para criar o serviço utilizando um certificado da Microsoft, não atualize manualmente o valor DNS para o cluster. O valor deve ser definido automaticamente.

  Atualize o DNS do endereço IP público do cluster AKS no separador **Configuração** em **Definições** no painel esquerdo. (Ver a seguinte imagem.) O Endereço IP Público é um tipo de recurso que é criado sob o grupo de recursos que contém os nódosos do agente AKS e outros recursos de networking.

  [![Azure Machine Learning: Garantir serviços web com TLS](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-tlsssl-certificate"></a>Atualizar o certificado TLS/SSL

Os certificados TLS/SSL expiram e devem ser renovados. Normalmente isto acontece todos os anos. Utilize as informações nas seguintes secções para atualizar e renovar o seu certificado para modelos implantados no Serviço Azure Kubernetes:

### <a name="update-a-microsoft-generated-certificate"></a>Atualizar um certificado gerado pela Microsoft

Se o certificado foi originalmente gerado pela Microsoft (quando utilizar o *leaf_domain_label* para criar o serviço), utilize um dos seguintes exemplos para atualizar o certificado:

**Use o SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Utilizar a CLI**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True
```

Para mais informações, consulte os seguintes pontos de referência:

* [SslConfiguração](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [Configuração AksUpdate](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Atualizar certificado personalizado

Se o certificado tiver sido originalmente gerado por uma autoridade de certificados, utilize os seguintes passos:

1. Utilize a documentação fornecida pela autoridade do certificado para renovar o certificado. Este processo cria novos ficheiros de certificados.

1. Utilize o SDK ou o CLI para atualizar o serviço com o novo certificado:

    **Use o SDK**

    ```python
    from azureml.core.compute import AksCompute
    from azureml.core.compute.aks import AksUpdateConfiguration
    from azureml.core.compute.aks import SslConfiguration
    
    # Read the certificate file
    def get_content(file_name):
        with open(file_name, 'r') as f:
            return f.read()

    # Get the existing cluster
    aks_target = AksCompute(ws, clustername)
    
    # Update cluster with custom certificate
    ssl_configuration = SslConfiguration(cname="myaks", cert=get_content('cert.pem'), key=get_content('key.pem'))
    update_config = AksUpdateConfiguration(ssl_configuration)
    aks_target.update(update_config)
    ```

    **Utilizar a CLI**

    ```azurecli
    az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-cname "myaks"--ssl-cert-file "cert.pem" --ssl-key-file "key.pem"
    ```

Para mais informações, consulte os seguintes pontos de referência:

* [SslConfiguração](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [Configuração AksUpdate](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-tls"></a>Desativar TLS

Para desativar o TLS para um modelo implantado no `SslConfiguration` `status="Disabled"`Serviço Azure Kubernetes, crie um com, em seguida, execute uma atualização:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable TLS
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Passos seguintes
Aprenda a:
+ [Consumir um modelo de aprendizagem automática implementado como um serviço web](how-to-consume-web-service.md)
+ [Executar experiências e inferência sintetizar de forma segura dentro de uma rede virtual Azure](how-to-enable-virtual-network.md)
