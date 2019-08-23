---
title: Proteger serviços Web usando SSL
titleSuffix: Azure Machine Learning service
description: Saiba como proteger um serviço Web que é implantado por meio do serviço de Azure Machine Learning habilitando o HTTPS. O HTTPS protege os dados do por clientes usando o protocolo TLS, uma substituição para SSL (camadas de soquete seguro). Os clientes também usam HTTPS para verificar a identidade do serviço Web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: 5a2cab9dff4a075545d919cb41e72cf6e446e9d2
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69897335"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Usar SSL para proteger um serviço Web por meio do Azure Machine Learning

Este artigo mostra como proteger um serviço Web que é implantado por meio do serviço de Azure Machine Learning.

Você usa [https](https://en.wikipedia.org/wiki/HTTPS) para restringir o acesso aos serviços da Web e proteger os dados que os clientes enviam. O HTTPS ajuda a proteger as comunicações entre um cliente e um serviço Web criptografando as comunicações entre os dois. A criptografia usa [TLS (Transport Layer Security)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Às vezes, o TLS ainda é referido como *protocolo SSL* (SSL), que era o PREDECESSOR do TLS.

> [!TIP]
> O SDK do Azure Machine Learning usa o termo "SSL" para propriedades relacionadas a comunicações seguras. Isso não significa que o serviço Web não usa *TLS*. SSL é apenas um termo mais reconhecido.

O TLS e o SSL dependem de *certificados digitais*, que ajudam com a criptografia e a verificação de identidade. Para obter mais informações sobre como os certificados digitais funcionam, consulte o tópico da Wikipédia [infraestrutura de chave pública](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Se você não usar HTTPS para o serviço Web, os dados enviados de e para o serviço poderão estar visíveis para outras pessoas na Internet.
>
> O HTTPS também permite que o cliente Verifique a autenticidade do servidor ao qual ele está se conectando. Esse recurso protege os clientes contra ataques [Man-in-the-Middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) .

Esse é o processo geral para proteger um serviço Web:

1. Obtenha um nome de domínio.

2. Obter um certificado digital.

3. Implante ou atualize o serviço Web com SSL habilitado.

4. Atualize o DNS para apontar para o serviço web.

> [!IMPORTANT]
> Se estiver implantando no AKS (serviço kubernetes do Azure), você poderá comprar seu próprio certificado ou usar um certificado fornecido pela Microsoft. Se você usar um certificado da Microsoft, não precisará obter um nome de domínio ou certificado SSL. Para obter mais informações, consulte a seção [habilitar SSL e implantar](#enable) deste artigo.

Há pequenas diferenças quando você protege os serviços da Web entre os [destinos de implantação](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Obter um nome de domínio

Se você ainda não possui um nome de domínio, compre um de um registrador de *nome de domínio*. O processo e o preço são diferentes entre os registradores. O registrador fornece ferramentas para gerenciar o nome de domínio. Você usa essas ferramentas para mapear um FQDN (nome de domínio totalmente qualificado) (como\.www contoso.com) para o endereço IP que hospeda o serviço Web.

## <a name="get-an-ssl-certificate"></a>Obter um certificado SSL

Há várias maneiras de obter um certificado SSL (certificado digital). O mais comum é comprar um de uma *autoridade de certificação* (CA). Independentemente de onde você obtém o certificado, você precisa dos seguintes arquivos:

* R **certificado**. O certificado deve conter a cadeia de certificados completa e deve ser "codificado por PEM".
* R **chave**. A chave também deve ser codificada por PEM.

Ao solicitar um certificado, você deve fornecer o FQDN do endereço que planeja usar para o serviço Web (por exemplo, www\.contoso.com). O endereço que é carimbado no certificado e o endereço que os clientes usam são comparados para verificar a identidade do serviço Web. Se esses endereços não corresponderem, o cliente receberá uma mensagem de erro.

> [!TIP]
> Se a autoridade de certificação não puder fornecer o certificado e a chave como arquivos codificados por PEM, você poderá usar um utilitário como o [OpenSSL](https://www.openssl.org/) para alterar o formato.

> [!WARNING]
> Use certificados autoassinados somente para desenvolvimento. Não os use em ambientes de produção. Certificados autoassinados podem causar problemas no seu cliente de aplicativos. Para obter mais informações, consulte a documentação das bibliotecas de rede que seu aplicativo cliente usa.

## <a id="enable"></a>Habilitar SSL e implantar

Para implantar (ou reimplantar) o serviço com SSL habilitado, defina o parâmetro *ssl_enabled* como "true", onde for aplicável. Defina o parâmetro *ssl_certificate* para o valor do arquivo de *certificado* . Defina o *ssl_key* para o valor do arquivo de *chave* .

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Implantar em AKS e FPGA (matriz de portão programável por campo)

  > [!NOTE]
  > As informações nesta seção também se aplicam quando você implanta um serviço Web seguro para a interface visual. Se você não estiver familiarizado com o uso do SDK do Python, consulte [o que é o SDK do Azure Machine Learning para Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Ao implantar no AKS, você pode criar um novo cluster do AKS ou anexar um existente.
  
-  Se você criar um novo cluster, use **[AksCompute. provisionining_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none-)** .
- Se você anexar um cluster existente, use **[AksCompute. attach_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** . Ambos retornam um objeto de configuração que tem um método **Enable_ssl** .

O método **Enable_ssl** pode usar um certificado que é fornecido pela Microsoft ou um certificado que você compra.

  * Ao usar um certificado da Microsoft, você deve usar o parâmetro *leaf_domain_label* . Esse parâmetro gera o nome DNS para o serviço. Por exemplo, um valor "MyService" cria um nome de domínio de "MyService\<seis-Random-characters >\< . azureregion >. cloudapp. Azure. com ", em \<que azureregion > é a região que contém o serviço. Opcionalmente, você pode usar o parâmetro *overwrite_existing_domain* para substituir o *leaf_domain_label*existente.

    Para implantar (ou reimplantar) o serviço com SSL habilitado, defina o parâmetro *ssl_enabled* como "true", onde for aplicável. Defina o parâmetro *ssl_certificate* para o valor do arquivo de *certificado* . Defina o *ssl_key* para o valor do arquivo de *chave* .

    > [!IMPORTANT]
    > Ao usar um certificado da Microsoft, você não precisa comprar seu próprio certificado ou nome de domínio.

    O exemplo a seguir demonstra como criar uma configuração que habilita um certificado SSL da Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)
    attach_config.enable_ssl(leaf_domain_label = "myservice")
    ```

  * Ao usar *um certificado que você comprou*, use os parâmetros *ssl_cert_pem_file*, *ssl_key_pem_file*e *ssl_cname* . O exemplo a seguir demonstra como usar arquivos *. pem* para criar uma configuração que usa um certificado SSL que você comprou:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration()
    provisioning_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
    attach_config.enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

Para obter mais informações sobre *Enable_ssl*, consulte [AksProvisioningConfiguration. Enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) e [AksAttachConfiguration. Enable_ssl ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Implantar em instâncias de contêiner do Azure

Ao implantar em instâncias de contêiner do Azure, você fornece valores para parâmetros relacionados a SSL, como mostra o trecho de código a seguir:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Para obter mais informações, consulte [AciWebservice. deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-).

## <a name="update-your-dns"></a>Atualizar o seu DNS

Em seguida, tem de atualizar o DNS para apontar para o serviço web.

+ **Para instâncias de contêiner:**

  Use as ferramentas do seu registrador de nome de domínio para atualizar o registro DNS para seu nome de domínio. O registro deve apontar para o endereço IP do serviço.

  Pode haver um atraso de minutos ou horas antes que os clientes possam resolver o nome de domínio, dependendo do registrador e a TTL (vida útil) configurada para o nome de domínio.

+ **Para AKS:**

  > [!WARNING]
  > Se você usou o *leaf_domain_label* para criar o serviço usando um certificado da Microsoft, não atualize manualmente o valor DNS para o cluster. O valor deve ser definido automaticamente.

  Atualize o DNS do endereço IP público do cluster AKS na guia **configuração** em **configurações** no painel esquerdo. (Consulte a imagem a seguir.) O endereço IP público é um tipo de recurso que é criado no grupo de recursos que contém os nós do agente AKS e outros recursos de rede.

  [![Serviço de Azure Machine Learning: Protegendo serviços Web com SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)](./media/how-to-secure-web-service/aks-public-ip-address-expanded.png)

## <a name="update-the-ssl-certificate"></a>Atualizar o certificado SSL

Os certificados SSL expiram e devem ser renovados. Normalmente, isso acontece todos os anos. Use as informações nas seções a seguir para atualizar e renovar seu certificado para modelos implantados no serviço kubernetes do Azure:

### <a name="update-a-microsoft-generated-certificate"></a>Atualizar um certificado gerado pela Microsoft

Se o certificado foi originalmente gerado pela Microsoft (ao usar o *leaf_domain_label* para criar o serviço), use um dos exemplos a seguir para atualizar o certificado:

**Usar o SDK**

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

Para obter mais informações, consulte os documentos de referência a seguir:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

### <a name="update-custom-certificate"></a>Atualizar certificado personalizado

Se o certificado foi originalmente gerado por uma autoridade de certificação, use as seguintes etapas:

1. Use a documentação fornecida pela autoridade de certificação para renovar o certificado. Esse processo cria novos arquivos de certificado.

1. Use o SDK ou a CLI para atualizar o serviço com o novo certificado:

    **Usar o SDK**

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

Para obter mais informações, consulte os documentos de referência a seguir:

* [SslConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration?view=azure-ml-py)
* [AksUpdateConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration?view=azure-ml-py)

## <a name="disable-ssl"></a>Desabilitar SSL

Para desabilitar o SSL para um modelo implantado no serviço kubernetes do `SslConfiguration` Azure `status="Disabled"`, crie um com e, em seguida, execute uma atualização:

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Disable SSL
ssl_configuration = SslConfiguration(status="Disabled")
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

## <a name="next-steps"></a>Passos seguintes
Aprenda a:
+ [Consumir um modelo de aprendizado de máquina implantado como um serviço Web](how-to-consume-web-service.md)
+ [Executar experimentos e inferências com segurança dentro de uma rede virtual do Azure](how-to-enable-virtual-network.md)
