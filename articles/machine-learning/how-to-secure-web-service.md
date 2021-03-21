---
title: Serviços web seguros usando TLS
titleSuffix: Azure Machine Learning
description: Saiba como ativar HTTPS com a versão 1.2 do TLS para garantir um serviço web que seja implementado através do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: aashishb
author: aashishb
ms.date: 03/11/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 71cb2e9e112c49d77a2a0b47c24c49cabfa86589
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149023"
---
# <a name="use-tls-to-secure-a-web-service-through-azure-machine-learning"></a>Utilizar o TLS para proteger um serviço Web através do Azure Machine Learning


Este artigo mostra-lhe como garantir um serviço web que é implementado através do Azure Machine Learning.

Utiliza [HTTPS](https://en.wikipedia.org/wiki/HTTPS) para restringir o acesso aos serviços web e proteger os dados que os clientes submetem. HTTPS ajuda a garantir comunicações entre um cliente e um serviço web encriptando comunicações entre os dois. A encriptação utiliza [a Segurança da Camada de Transporte (TLS)](https://en.wikipedia.org/wiki/Transport_Layer_Security). TLS é por vezes ainda referido como *Camada de Tomadas Seguras* (SSL), que foi o antecessor de TLS.

> [!TIP]
> O Azure Machine Learning SDK utiliza o termo "SSL" para propriedades relacionadas com comunicações seguras. Isto não significa que o seu serviço web não use *TLS*. SSL é apenas um termo mais comumente reconhecido.
>
> Especificamente, os serviços web implementados através do suporte Azure Machine Learning TLS versão 1.2 para AKS e ACI. Para implementações ACI, se estiver na versão TLS mais antiga, recomendamos a recolocação para obter a versão TLS mais recente.
>
> A versão 1.3 do TLS para Azure Machine Learning - Inferência AKS não é suportada.

TLS e SSL ambos dependem de *certificados digitais,* que ajudam na encriptação e verificação de identidade. Para obter mais informações sobre como funcionam os certificados digitais, consulte o tema Wikipedia [Infraestruturas-chave públicas.](https://en.wikipedia.org/wiki/Public_key_infrastructure)

> [!WARNING]
> Se não utilizar HTTPS para o seu serviço web, os dados enviados de e para o serviço podem ser visíveis para outros na internet.
>
> HTTPS também permite ao cliente verificar a autenticidade do servidor a que se está a ligar. Esta funcionalidade protege os clientes contra ataques [man-in-the-middle.](https://en.wikipedia.org/wiki/Man-in-the-middle_attack)

Este é o processo geral para garantir um serviço web:

1. Arranja um nome de domínio.

2. Obter um certificado digital.

3. Implementar ou atualizar o serviço web com O TLS ativado.

4. Atualize o seu DNS para apontar para o serviço web.

> [!IMPORTANT]
> Se estiver a ser implantado no Serviço Azure Kubernetes (AKS), pode adquirir o seu próprio certificado ou utilizar um certificado fornecido pela Microsoft. Se utilizar um certificado da Microsoft, não precisa de obter um nome de domínio ou certificado TLS/SSL. Para mais informações, consulte o [Enable TLS e implemente](#enable) a secção deste artigo.

Há pequenas diferenças quando se assegura os alvos de [implantação](how-to-deploy-and-where.md).

## <a name="get-a-domain-name"></a>Obter um nome de domínio

Se já não possuir um nome de domínio, compre um de um registo de nome de *domínio.* O processo e o preço diferem entre os conservadores. O registrador fornece ferramentas para gerir o nome de domínio. Utiliza estas ferramentas para mapear um nome de domínio totalmente qualificado (FQDN) (como www \. contoso.com) para o endereço IP que acolhe o seu serviço web.

## <a name="get-a-tlsssl-certificate"></a>Obtenha um certificado TLS/SSL

Existem muitas formas de obter um certificado TLS/SSL (certificado digital). O mais comum é comprar um a uma autoridade de *certificados* (CA). Independentemente de onde obtém o certificado, precisa dos seguintes ficheiros:

* Um **certificado.** O certificado deve conter a cadeia de certificados completa, e deve ser "codificado por PEM".
* Uma **chave.** A chave também deve ser codificada por PEM.

Quando solicita um certificado, deve fornecer o FQDN do endereço que pretende utilizar para o serviço web (por exemplo, www \. contoso.com). O endereço que está estampado no certificado e o endereço que os clientes usam são comparados para verificar a identidade do serviço web. Se esses endereços não corresponderem, o cliente recebe uma mensagem de erro.

> [!TIP]
> Se a autoridade de certificados não puder fornecer o certificado e a chave como ficheiros codificados por PEM, pode utilizar um utilitário como [o OpenSSL](https://www.openssl.org/) para alterar o formato.

> [!WARNING]
> Utilize certificados *auto-assinados* apenas para desenvolvimento. Não os uses em ambientes de produção. Os certificados auto-assinados podem causar problemas nas aplicações do seu cliente. Para mais informações, consulte a documentação das bibliotecas de rede que a sua aplicação do cliente utiliza.

## <a name="enable-tls-and-deploy"></a><a id="enable"></a> Ativar TLS e implementar

**Para a implementação de AKS,** pode ativar a terminação do TLS quando [criar ou anexar um cluster AKS](how-to-create-attach-kubernetes.md) no espaço de trabalho AML. No tempo de implementação do modelo AKS, pode desativar a terminação TLS com o objeto de configuração de implementação, caso contrário, toda a implementação do modelo AKS por predefinição terá a terminação TLS ativada no cluster AKS criar ou anexar tempo.

Para a implementação do ACI, pode ativar a terminação do TLS no tempo de implementação do modelo com o objeto de configuração de implementação.


### <a name="deploy-on-azure-kubernetes-service"></a>Implantação no Serviço Azure Kubernetes

  > [!NOTE]
  > As informações nesta secção também se aplicam quando implementa um serviço web seguro para o designer. Se não está familiarizado com o uso do Python SDK, veja [o que é o Azure Machine Learning SDK para Python?](/python/api/overview/azure/ml/intro)

Quando [criar ou anexar um cluster AKS](how-to-create-attach-kubernetes.md) no espaço de trabalho AML, pode ativar a terminação do TLS com AksCompute.provisioning_configuration **[e](/python/api/azureml-core/azureml.core.compute.akscompute#provisioning-configuration-agent-count-none--vm-size-none--ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--location-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--service-cidr-none--dns-service-ip-none--docker-bridge-cidr-none--cluster-purpose-none--load-balancer-type-none--load-balancer-subnet-none-)** **[AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)** objetos de configuração. Ambos os métodos devolvem um objeto de configuração que tem um método **enable_ssl,** e pode utilizar **enable_ssl** método para ativar o TLS.

Pode ativar o TLS com certificado Microsoft ou com um certificado personalizado adquirido na AC. 

* **Quando utilizar um certificado da Microsoft,** deve utilizar o parâmetro *leaf_domain_label.* Este parâmetro gera o nome DNS para o serviço. Por exemplo, um valor de "contoso" cria um nome de domínio de "contoso \<six-random-characters> . \<azureregion> . cloudapp.azure.com", onde \<azureregion> está a região que contém o serviço. Opcionalmente, pode utilizar o parâmetro *overwrite_existing_domain* para substituir o *leaf_domain_label* existente . O exemplo a seguir demonstra como criar uma configuração que permite um TLS com certificado Microsoft:

    ```python
    from azureml.core.compute import AksCompute

    # Config used to create a new AKS cluster and enable TLS
    provisioning_config = AksCompute.provisioning_configuration()

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
    #  where "######" is a random series of characters
    provisioning_config.enable_ssl(leaf_domain_label = "contoso")


    # Config used to attach an existing AKS cluster to your workspace and enable TLS
    attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                          cluster_name = cluster_name)

    # Leaf domain label generates a name using the formula
    #  "<leaf-domain-label>######.<azure-region>.cloudapp.azure.com"
    #  where "######" is a random series of characters
    attach_config.enable_ssl(leaf_domain_label = "contoso")
    ```
    > [!IMPORTANT]
    > Quando utiliza um certificado da Microsoft, não precisa de adquirir o seu próprio certificado ou nome de domínio.

    > [!WARNING]
    > Se o seu cluster AKS estiver configurado com um balançador de carga interno, a utilização de um certificado fornecido pela Microsoft não é __suportada__ e tem de utilizar um certificado personalizado para ativar o TLS.

* **Quando utilizar um certificado personalizado que adquiriu,** utilize os *ssl_cert_pem_file,* *ssl_key_pem_file* e *ssl_cname* parâmetros. O exemplo a seguir demonstra como utilizar ficheiros .pem para criar uma configuração que utilize um certificado TLS/SSL que adquiriu:
 
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

Para obter mais informações sobre *enable_ssl*, consulte [AksProvisioningConfiguration.enable_ssl()](/python/api/azureml-core/azureml.core.compute.aks.aksprovisioningconfiguration#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-) e [AksAttachConfiguration.enable_ssl()](/python/api/azureml-core/azureml.core.compute.aks.aksattachconfiguration#enable-ssl-ssl-cname-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--leaf-domain-label-none--overwrite-existing-domain-false-).

### <a name="deploy-on-azure-container-instances"></a>Implementar no Azure Container Instances

Quando se implanta em Instâncias de Contentores Azure, fornece valores para parâmetros relacionados com o TLS, como mostra o seguinte corte de código:

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(
    ssl_enabled=True, ssl_cert_pem_file="cert.pem", ssl_key_pem_file="key.pem", ssl_cname="www.contoso.com")
```

Para mais informações, consulte [AciWebservice.deploy_configuration()](/python/api/azureml-core/azureml.core.webservice.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-).

## <a name="update-your-dns"></a>Atualize o seu DNS

Para a implementação de AKS com certificado personalizado ou implementação de ACI, tem de atualizar o seu registo DE DNS para indicar o endereço IP do ponto final de pontuação.

  > [!IMPORTANT]
  > Quando utiliza um certificado da Microsoft para a implementação de AKS, não precisa de atualizar manualmente o valor DNS para o cluster. O valor deve ser definido automaticamente.

Pode seguir os seguintes passos para atualizar o registo DNS para o seu nome de domínio personalizado:
* Obtenha o endereço IP do ponto final de pontuação URI, que normalmente está no formato de *http://104.214.29.152:80/api/v1/service/<service-name>/score* . 
* Utilize as ferramentas do registo de nomes de domínio para atualizar o registo DNS para o seu nome de domínio. O registo deve apontar para o endereço IP do ponto final de pontuação.
* Após a atualização do registo do DNS, pode validar a resolução DNS utilizando o comando *nslookup de nome de domínio personalizado.* Se o registo dns for corretamente atualizado, o nome de domínio personalizado apontará para o endereço IP do ponto final de pontuação.
* Pode haver um atraso de minutos ou horas antes que os clientes possam resolver o nome de domínio, dependendo do registrador e do "tempo de vida" (TTL) que está configurado para o nome de domínio.


## <a name="update-the-tlsssl-certificate"></a>Atualizar o certificado TLS/SSL

Os certificados TLS/SSL expiram e devem ser renovados. Normalmente isto acontece todos os anos. Utilize as informações nas seguintes secções para atualizar e renovar o certificado para modelos implantados no Serviço Azure Kubernetes:

### <a name="update-a-microsoft-generated-certificate"></a>Atualizar um certificado gerado pela Microsoft

Se o certificado foi originalmente gerado pela Microsoft (ao utilizar o *leaf_domain_label* para criar o serviço), **irá automaticamente renovar-se** quando necessário. Se quiser renová-lo manualmente, utilize um dos seguintes exemplos para atualizar o certificado:

> [!IMPORTANT]
> * Se o certificado existente ainda for válido, utilize `renew=True` (SDK) ou `--ssl-renew` (CLI) para forçar a configuração a renová-lo. Por exemplo, se o certificado existente ainda for válido por 10 dias e não `renew=True` utilizar, o certificado não poderá ser renovado.
> * Quando o serviço foi originalmente implantado, o `leaf_domain_label` é usado para criar um nome DNS usando o padrão `<leaf-domain-label>######.<azure-region>.cloudapp.azure.com` . Para preservar o nome existente (incluindo os 6 dígitos originalmente gerados), utilize o `leaf_domain_label` valor original. Não inclua os 6 dígitos que foram gerados.

**Use o SDK**

```python
from azureml.core.compute import AksCompute
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute.aks import SslConfiguration

# Get the existing cluster
aks_target = AksCompute(ws, clustername)

# Update the existing certificate by referencing the leaf domain label
ssl_configuration = SslConfiguration(leaf_domain_label="myaks", overwrite_existing_domain=True, renew=True)
update_config = AksUpdateConfiguration(ssl_configuration)
aks_target.update(update_config)
```

**Utilizar a CLI**

```azurecli
az ml computetarget update aks -g "myresourcegroup" -w "myresourceworkspace" -n "myaks" --ssl-leaf-domain-label "myaks" --ssl-overwrite-domain True --ssl-renew
```

Para mais informações, consulte os seguintes documentos de referência:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration)

### <a name="update-custom-certificate"></a>Atualizar certificado personalizado

Se o certificado tiver sido originalmente gerado por uma autoridade de certificados, utilize as seguintes etapas:

1. Utilize a documentação fornecida pela autoridade de certificados para renovar o certificado. Este processo cria novos ficheiros de certificados.

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

Para mais informações, consulte os seguintes documentos de referência:

* [SslConfiguration](/python/api/azureml-core/azureml.core.compute.aks.sslconfiguration)
* [AksUpdateConfiguration](/python/api/azureml-core/azureml.core.compute.aks.aksupdateconfiguration)

## <a name="disable-tls"></a>Desativar TLS

Para desativar o TLS para um modelo implantado no Serviço Azure Kubernetes, crie um `SslConfiguration` `status="Disabled"` com, em seguida, execute uma atualização:

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
+ [Isolamento de rede virtual e visão geral da privacidade](how-to-network-security-overview.md)