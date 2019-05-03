---
title: Proteja os serviços Web com SSL
titleSuffix: Azure Machine Learning service
description: Saiba como proteger um serviço web implementado com o serviço Azure Machine Learning ao ativar o HTTPS. HTTPS protege os dados submetidos por clientes que utilizam a segurança de camada de transporte (TLS), um substituto para camadas de soquete seguro (SSL). Também é utilizado pelos clientes para verificar a identidade do serviço web.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: aashishb
author: aashishb
ms.date: 04/29/2019
ms.custom: seodec18
ms.openlocfilehash: 50e42172af6ca6b966f9f60d3e037f9ae3dc5cbe
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023770"
---
# <a name="use-ssl-to-secure-web-services-with-azure-machine-learning-service"></a>Utilizar o SSL para proteger serviços da web com o serviço Azure Machine Learning

Neste artigo, ficará a saber como proteger um serviço web implementado com o serviço Azure Machine Learning. Pode restringir o acesso a serviços da web e proteger os dados submetidos por clientes que utilizam [Hypertext Transfer Protocol segura (HTTPS)](https://en.wikipedia.org/wiki/HTTPS).

HTTPS é utilizado para proteger as comunicações entre um cliente e o serviço da web ao encriptar comunicações entre os dois. Encriptação é processada através de [Transport Layer Security (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). Por vezes, TLS ainda é conhecido como proteger o SSL (Sockets Layer), que foi o predecessor TLS.

> [!TIP]
> O SDK do Azure Machine Learning utiliza o termo "SSL' para propriedades relacionado com a ativação comunicações seguras. Isso não significa que o TLS não é utilizado pelo seu serviço web, apenas esse SSL é o termo mais reconhecível para muitos leitores.

TLS e SSL ambos dependem __certificados digitais__, quais são utilizados para efetuar a verificação de encriptação e identidade. Para obter mais informações sobre como digital de certificados de trabalho, consulte a entrada na Wikipedia sobre [infraestrutura de chaves públicas (PKI)](https://en.wikipedia.org/wiki/Public_key_infrastructure).

> [!Warning]
> Se não ativar e utilizar HTTPS para o seu serviço web, dados enviados de e para o serviço poderão estar visíveis aos outros na internet.
>
> HTTPS também permite que o cliente verificar a autenticidade do servidor que está a ligar a. Esta ação protege os clientes contra [man-in-the-middle](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) ataques.

O processo geral de proteger um serviço web novo ou existente é o seguinte:

1. Obtenha um nome de domínio.

2. Obtenha um certificado digital.

3. Implementar ou atualizar o serviço web com a definição de SSL ativada.

4. Atualize o DNS para apontar para o serviço web.

> [!IMPORTANT]
> Se estiver a implementar para o Azure Kubernetes Service (AKS), pode fornecer seu próprio certificado ou utilizar um certificado fornecido pela Microsoft. Se utilizar o Microsoft certificado fornecido, não é necessário obter um nome de domínio ou o certificado SSL. Para obter mais informações, consulte a [ativar o SSL e implementar](#enable) secção.

Existem pequenas diferenças quando protegendo serviços da web entre as [destinos de implementação](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Obter um nome de domínio

Se já não possui um nome de domínio, pode comprar um de uma __entidade de registo de nome de domínio__. O processo é diferente entre entidades de registo, como faz o custo. A entidade de registo também fornece ferramentas para gerenciar o nome de domínio. Estas ferramentas são utilizadas para mapear um nome de domínio completamente qualificado (por exemplo, www\.contoso.com) para o endereço IP que aloja o serviço da web.

## <a name="get-an-ssl-certificate"></a>Obter um certificado SSL

Existem várias formas de obter um certificado SSL (certificado digital). A mais comum é comprar um de uma __autoridade de certificação__ (AC). Independentemente de onde obter o certificado, terá dos seguintes ficheiros:

* R __certificado__. O certificado tem de conter a cadeia de certificados completa e tem de ser PEM codificado.
* R __chave__. A chave tem de ser PEM codificado.

Quando pedir um certificado, tem de fornecer o nome de domínio completamente qualificado (FQDN) do endereço que pretende utilizar para o serviço web. Por exemplo, www\.contoso.com. O endereço marcados para o certificado e o endereço utilizado pelos clientes são comparados ao validar a identidade do serviço web. Se os endereços não corresponderem, os clientes irão receber um erro.

> [!TIP]
> Se a autoridade de certificação não é possível fornecer o certificado e chave, como ficheiros PEM codificado, pode utilizar um utilitário como [OpenSSL](https://www.openssl.org/) para alterar o formato.

> [!WARNING]
> Certificados autoassinados devem ser usados apenas para desenvolvimento. Eles não devem ser usados na produção. Certificados autoassinados podem causar problemas no seu cliente de aplicativos. Para obter mais informações, consulte a documentação para as bibliotecas de rede utilizadas na sua aplicação de cliente.

## <a id="enable"></a> Ativar o SSL e implementar

Para implementar (ou voltar a implementar) o serviço com SSL ativado, defina o `ssl_enabled` parâmetro `True`, sempre que aplicável. Definir o `ssl_certificate` parâmetro para o valor da __certificado__ ficheiro e o `ssl_key` para o valor da __chave__ ficheiro.

+ **Visual interface - criar seguro do Azure Kubernetes Service (AKS) para a implementação** 
    
    Consulte este se estiver a tentar criar a computação segura de implementação para visual interface. Durante o aprovisionamento do cluster do AKS, fornecer valores para parâmetros relacionados com SSL, em seguida, criar um novo AKS.  Veja abaixo o trecho de código:
    

    > [!TIP]
    >  Se não estiver familiarizado com o SDK de Python, iniciar a partir de [descrição de geral do Azure Machine Learning Python SDK.](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)


    ```python
    from azureml.core.compute import AksCompute, ComputeTarget

    # Provide SSL-related parameters when provisioning the AKS cluster
    prov_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")   
 
    aks_name = 'secure-aks'
    # Create the cluster
    aks_target = ComputeTarget.create(workspace = ws,
                                        name = aks_name,
                                        provisioning_configuration = prov_config)
    
    # Wait for the create process to complete
    aks_target.wait_for_completion(show_output = True)
    print(aks_target.provisioning_state)
    print(aks_target.provisioning_errors)
    ```
    
   

+ **Implementar no serviço Kubernetes do Azure (AKS) e FPGA**

  Ao implementar no AKS, pode criar um novo cluster do AKS ou anexar um já existente. Criar um novo cluster utiliza [AksCompute.provisionining_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none-) enquanto anexar um cluster existente utiliza [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none-). Ambos retornam um objeto de configuração que tenha um `enable_ssl` método.

  O `enable_ssl` método pode utilizar um certificado fornecido pela Microsoft ou que fornecer.

  * Quando utilizar um certificado __fornecida pela Microsoft__, tem de utilizar o `leaf_domain_label` parâmetro. Utilizar este parâmetro, irá criar o serviço usando um certificado fornecido pela Microsoft. O `leaf_domain_label` é usado para gerar o nome DNS para o serviço. Por exemplo, um valor de `myservice` cria um nome de domínio `myservice<6-random-characters>.<azureregion>.cloudapp.azure.com`, onde `<azureregion>` é a região que contém o serviço. Opcionalmente, pode utilizar o `overwrite_existing_domain` parâmetro para substituir a etiqueta de domínio de folha existente.

    Para implementar (ou voltar a implementar) o serviço com SSL ativado, defina o `ssl_enabled` parâmetro `True`, sempre que aplicável. Definir o `ssl_certificate` parâmetro para o valor da __certificado__ ficheiro e o `ssl_key` para o valor da __chave__ ficheiro.

    > [!IMPORTANT]
    > Quando utilizar um certificado fornecido pela Microsoft, não é necessário comprar seu próprio certificado ou nome de domínio.

    O exemplo seguinte demonstra como criar configurações que permitem um certificado SSL criado pela Microsoft:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration().enable_ssl(leaf_domain_label = "myservice")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name).enable_ssl(leaf_domain_label = "myservice")
    ```

  * Ao usar __um certificado comprado__, utilize o `ssl_cert_pem_file`, `ssl_key_pem_file`, e `ssl_cname` parâmetros.  O exemplo seguinte demonstra como criar configurações que usem um certificado SSL é fornecer usando `.pem` ficheiros:

    ```python
    from azureml.core.compute import AksCompute
    # Config used to create a new AKS cluster and enable SSL
    provisioning_config = AksCompute.provisioning_configuration(ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    provisioning_config = AksCompute.provisioning_configuration().enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    # Config used to attach an existing AKS cluster to your workspace and enable SSL
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name).enable_ssl(ssl_cert_pem_file="cert.pem",
                                        ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  Para obter mais informações sobre `enable_ssl`, consulte [AksProvisioningConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) e [AksAttachConfiguration.enable_ssl()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration?view=azure-ml-py#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

+ **Implementar no Azure Container Instances (ACI)**

  Durante a implementação no ACI, fornece valores para parâmetros relacionados com SSL, conforme mostrado no fragmento de código:

    ```python
    from azureml.core.webservice import AciWebservice

    aci_config = AciWebservice.deploy_configuration(ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
    ```

  Para obter mais informações, consulte [AciWebservice.deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none-).

## <a name="update-your-dns"></a>Atualizar o seu DNS

Em seguida, tem de atualizar o DNS para apontar para o serviço web.

+ **Para o ACI**:

  Utilize as ferramentas fornecidas pelo sua entidade de registo de nome de domínio para atualizar o registo DNS para o seu nome de domínio. O registo tem de apontar para o endereço IP do serviço.

  Consoante a entidade de registo e a hora para live (TTL) configurada para o nome de domínio, pode demorar vários minutos a várias horas para que os clientes podem resolver o nome de domínio.

+ **Para o AKS**:

  > [!WARNING]
  > Se tiver utilizado o `leaf_domain_label` para criar o serviço com um certificado fornecido pela Microsoft, não de atualizar manualmente o valor DNS para o cluster. O valor deve ser definido automaticamente.

  Atualize o DNS no separador "Configuração" das "Endereço IP público" do cluster AKS conforme mostrado na imagem. Pode encontrar o endereço IP público como um dos tipos de recursos criados no grupo de recursos que contém os nós de agente do AKS e outros recursos de rede.

  ![Azure Machine Learning service: Protegendo serviços da web com SSL](./media/how-to-secure-web-service/aks-public-ip-address.png)



## <a name="next-steps"></a>Passos Seguintes
Aprenda a:
+ [Consumir um modelo implementado como um serviço web machine learning](how-to-consume-web-service.md)
+ [Executar com segurança inferência dentro de uma rede Virtual do Azure e de experimentações](how-to-enable-virtual-network.md)

