---
title: Proteger serviços da web, utilizando SSL
titleSuffix: Azure Machine Learning service
description: Saiba como proteger um serviço web que é implementado através do serviço Azure Machine Learning ao ativar o HTTPS. HTTPS protege dados a partir de clientes utilizando a segurança de camada de transporte (TLS), um substituto para camadas de soquete seguro (SSL). Os clientes também utilizam HTTPS para verificar a identidade do serviço web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 779a34800057284ce77b6d76e030ddca5fadc25f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66393827"
---
# <a name="use-ssl-to-secure-a-web-service-through-azure-machine-learning"></a>Utilizar o SSL para proteger um serviço web através do Azure Machine Learning

Este artigo mostra-lhe como proteger um serviço web que é implementado através do serviço Azure Machine Learning.

Utilizar [HTTPS](https://en.wikipedia.org/wiki/HTTPS) para restringir o acesso a serviços da web e proteger os dados que os clientes enviam. HTTPS ajuda a proteger as comunicações entre um cliente e um serviço da web ao encriptar comunicações entre os dois. A criptografia usa [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS, às vezes, ainda é referido como *Secure Sockets Layer* (SSL), que foi o predecessor de TLS.

> [!TIP]
> O SDK do Azure Machine Learning utiliza o termo "SSL" para propriedades que estão relacionadas para proteger a comunicação. Isso não significa que o seu serviço web não usa *TLS*. SSL é apenas um termo mais comumente reconhecido.

TLS e SSL ambos dependem *certificados digitais*, que ser útil na verificação de encriptação e identidade. Para obter mais informações sobre como digital de certificados de trabalho, consulte o tópico da Wikipedia [infraestrutura de chaves públicas](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!WARNING]
> Se não utilizar HTTPS para o seu serviço web, os dados que são enviados de e para o serviço podem ser visíveis para outras pessoas na internet.
>
> HTTPS também permite que o cliente verificar a autenticidade do servidor que está a ligar a. Esta funcionalidade protege os clientes contra [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) ataques.

Este é o processo geral para proteger um serviço web:

1. Obtenha um nome de domínio.

2. Obtenha um certificado digital.

3. Implementar ou atualizar o serviço web com SSL ativado.

4. Atualize o DNS para apontar para o serviço web.

> [!IMPORTANT]
> Se estiver a implementar para o Azure Kubernetes Service (AKS), pode comprar seu próprio certificado ou utilize um certificado que é fornecido pela Microsoft. Se utilizar um certificado da Microsoft, não precisa de obter um certificado SSL ou o nome de domínio. Para obter mais informações, consulte a [ativar o SSL e implementar](#enable) seção deste artigo.

Existem pequenas diferenças quando protege serviços da web em [destinos de implementação](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Obter um nome de domínio

Se ainda não tem um nome de domínio, comprar um de uma *entidade de registo de nome de domínio*. O processo e o preço diferem entre entidades de registo. A entidade de registo fornece ferramentas para gerenciar o nome de domínio. Utilize estas ferramentas para mapear um nome de domínio completamente qualificado (FQDN) (por exemplo, www\.contoso.com) para o endereço IP que aloja o seu serviço web.

## <a name="get-an-ssl-certificate"></a>Obter um certificado SSL

Existem várias formas de obter um certificado SSL (certificado digital). A mais comum é comprar um de uma *autoridade de certificação* (AC). Independentemente de onde pode obter o certificado, terá dos seguintes ficheiros:

* R **certificado**. O certificado deve conter a cadeia de certificados completa, e tem de ser "PEM codificado."
* R **chave**. A chave também deve ser PEM codificado.

Quando pedir um certificado, tem de fornecer o FQDN do endereço que planeia utilizar para o serviço web (por exemplo, www\.contoso.com). O endereço que está marcado para o certificado e o endereço que os clientes utilizam são comparados para verificar a identidade do serviço web. Se esses endereços não corresponderem, o cliente obtém uma mensagem de erro.

> [!TIP]
> Se a autoridade de certificação não é possível fornecer o certificado e chave, como ficheiros PEM codificado, pode utilizar um utilitário como [OpenSSL](https://www.openssl.org/) para alterar o formato.

> [!WARNING]
> Uso *auto-assinado* certificados apenas para desenvolvimento. Não usá-los em ambientes de produção. Certificados autoassinados podem causar problemas no seu cliente de aplicativos. Para obter mais informações, consulte a documentação para as bibliotecas de rede que utiliza a aplicação cliente.

## <a id="enable"></a> Ativar o SSL e implementar

Para implementar (ou voltar a implementar) o serviço com SSL ativado, defina o *ssl_enabled* parâmetro como "True" seja onde for aplicável. Definir o *ssl_certificate* parâmetro para o valor da *certificado* ficheiro. Definir o *ssl_key* para o valor da *chave* ficheiro.

### <a name="deploy-on-aks-and-field-programmable-gate-array-fpga"></a>Implementar no AKS e matriz de porta de campos programáveis (FPGA)

  > [!NOTE]
  > As informações desta secção também se aplica ao implementar um serviço da web seguro para a interface visual. Se não estiver familiarizado com o SDK de Python, veja [o que é o Azure Machine Learning SDK para Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).

Quando implementar no AKS, pode criar um novo cluster do AKS ou anexar um já existente.
  
-  Se criar um novo cluster, utilize  **[AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-)** .
- Se anexar um cluster existente, utilize  **[AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-)** . Ambos retornam um objeto de configuração que tem um **enable_ssl** método.

O **enable_ssl** método pode usar um certificado que é fornecido pela Microsoft ou um certificado que comprar.

  * Quando utilizar um certificado da Microsoft, tem de utilizar o *leaf_domain_label* parâmetro. Este parâmetro gera o nome DNS para o serviço. Por exemplo, um valor de "myservice" cria um nome de domínio de "myservice\<seis caracteres aleatórios >.\< azureregion >. cloudapp.azure.com ", em que \<azureregion > é a região que contém o serviço. Opcionalmente, pode utilizar o *overwrite_existing_domain* parâmetro para substituir o existente *leaf_domain_label*.

    Para implementar (ou voltar a implementar) o serviço com SSL ativado, defina o *ssl_enabled* parâmetro como "True" seja onde for aplicável. Definir o *ssl_certificate* parâmetro para o valor da *certificado* ficheiro. Definir o *ssl_key* para o valor da *chave* ficheiro.

    > [!IMPORTANT]
    > Quando utiliza um certificado da Microsoft, não precisa de comprar o seu próprio certificado ou nome de domínio.

    O exemplo seguinte demonstra como criar uma configuração que permite que um certificado SSL da Microsoft:

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

  * Quando utiliza *um certificado que comprou*, que utiliza o *ssl_cert_pem_file*, *ssl_key_pem_file*, e *ssl_cname* parâmetros. O exemplo seguinte demonstra como usar *. pem* ficheiros para criar uma configuração que utiliza um certificado SSL que comprou:

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

Para obter mais informações sobre *enable_ssl*, consulte [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) e [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Implementar no Azure Container Instances

Quando implementar no Azure Container Instances, forneça valores para os parâmetros relacionados com SSL, como o fragmento de código seguinte mostra:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Para obter mais informações, consulte [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-).

## <a name="update-your-dns"></a>Atualizar o seu DNS

Em seguida, tem de atualizar o DNS para apontar para o serviço web.

+ **Para as instâncias de contentor:**

  Utilize as ferramentas da sua entidade de registo de nome de domínio para atualizar o registo DNS para o seu nome de domínio. O registo tem de apontar para o endereço IP do serviço.

  Pode haver um atraso de minutos ou horas antes dos clientes podem resolver o nome de domínio, dependendo da entidade de registo e o "tempo de duração" (TTL) que está configurado para o nome de domínio.

+ **Para o AKS:**

  > [!WARNING]
  > Se utilizou *leaf_domain_label* para criar o serviço utilizando um certificado da Microsoft, não de atualizar manualmente o valor DNS para o cluster. O valor deve ser definido automaticamente.

  Atualize o DNS no **configuração** separador do endereço IP público do AKS cluster. (Veja a imagem seguinte.) O endereço IP público é um tipo de recurso é criado sob o grupo de recursos que contém os nós de agente do AKS e outros recursos de rede.

  ![Azure Machine Learning service: Protegendo serviços da web com SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)

## <a name="next-steps"></a>Passos Seguintes
Aprenda a:
+ [Consumir um modelo implementado como um serviço web machine learning](how-to-consume-web-service.md)
+ [Executar com segurança experimentações e inferência dentro de uma rede virtual do Azure](how-to-enable-virtual-network.md)
