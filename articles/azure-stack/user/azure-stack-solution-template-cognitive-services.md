---
title: Implementar os serviços cognitivos do Azure para o Azure Stack | Documentos da Microsoft
description: Saiba como implementar os serviços cognitivos do Azure para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2018
ms.author: mabrigg
ms.reviewer: guanghu
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 8080355bebf00c9f37c28ae8ed54bba092f8dc17
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099938"
---
# <a name="deploy-azure-cognitive-services-to-azure-stack"></a>Implementar os serviços cognitivos do Azure para o Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

> [!Note]  
> Serviços cognitivos do Azure no Azure Stack está em pré-visualização.

Pode usar os serviços cognitivos do Azure com o suporte do contentor no Azure Stack. Suporte para contentores nos serviços cognitivos do Azure permite-lhe utilizar as mesmas APIs avançadas estão disponíveis no Azure. A utilização de contentores permite flexibilidade no onde implementar e hospedar os serviços fornecidos no [contentores do Docker](https://www.docker.com/what-container). Suporte do contentor está atualmente disponível em pré-visualização para um subconjunto de serviços cognitivos do Azure, incluindo partes da [de imagem digitalizada](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview), e [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), e [Compreensão de idiomas](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) (LUIS).

Contentorização é uma abordagem para distribuição de software em que uma aplicação ou serviço específico, incluindo suas dependências e a configuração, são empacotadas como uma imagem de contentor. Com poucas ou nenhuma modificação, pode implementar uma imagem para um anfitrião de contentor. Cada contentor é isolado de outros contentores e de sistema operacional subjacente. O sistema em si, apenas tem os componentes necessários para executar a sua imagem. Anfitrião de contentor tem um requisito de espaço mais pequeno do que uma máquina virtual. Além disso, pode criar contentores a partir de imagens para tarefas de curta duração e removidas quando já não for necessário.

## <a name="use-containers-with-cognitive-services-on-azure-stack"></a>Utilizar contentores com os serviços cognitivos no Azure Stack

- **Controlo sobre dados**  
  Permitir que os utilizadores da aplicação ter controlo sobre os seus dados ao utilizar os serviços cognitivos. Pode fornecer os serviços cognitivos para os utilizadores de aplicações que não é possível enviar dados para o global Azure ou na cloud pública.

- **Controlo sobre atualizações do modelo**  
  Fornece aos utilizadores da aplicação para a versão e atualização dos modelos implementados na sua solução.

- **Arquitetura de portáteis**  
  Permitir a criação de uma arquitetura de aplicação portátil, para que pode implantar sua solução para a cloud pública, uma privada na cloud no local ou no edge. Pode implementar o contentor no serviço Kubernetes do Azure, Azure Container Instances, ou num cluster de Kubernetes no Azure stack. Para obter mais informações, consulte [implementar o Kubernetes no Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

- **Débito elevado e baixa latência**  
   Forneça os utilizadores da aplicação a capacidade de dimensionar com picos de tráfego para um débito elevado e baixa latência. Ative os serviços cognitivos executar no Azure Kubernetes Service fisicamente perto dos seus dados e lógica do aplicativo.

Com o Azure Stack, implemente contentores de serviços cognitivos num cluster do Kubernetes, juntamente com os contentores de aplicação para elevada disponibilidade e dimensionamento elástico. Pode desenvolver seu aplicativo ao combinar os serviços cognitivos com componentes criados nos serviços de aplicações, funções, BLOBs, armazenamento, ou SQL ou mySQL bases de dados. 

Para obter mais detalhes sobre os contentores de serviços cognitivos, aceda a [suporte para contentores nos serviços cognitivos do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support).

## <a name="deploy-the-azure-face-api"></a>Implementar a API Face do Azure

Este artigo descreve como implementar a API Face do Azure num cluster de Kubernetes no Azure Stack. Pode utilizar a mesma abordagem para implementar outros contentores de serviços cognitivos no cluster de Kubernetes do Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, precisará para:

1.  Pedir acesso ao registo de contentor para solicitar imagens de contentor de rosto do registo de contentor de serviços cognitivos do Azure. Para obter detalhes, vá a secção [pedir acesso para o registo de contentor privado](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers#request-access-to-the-private-container-registry).

2.  Prepare um cluster do Kubernetes no Azure Stack. Pode seguir o artigo [implementar o Kubernetes no Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

## <a name="create-azure-resources"></a>Criar recursos do Azure

Crie um recurso de serviço cognitivo no Azure para pré-visualizar os contentores de rostos, LUIS ou reconhecer texto, respectivamente. Terá de utilizar o URL de ponto final e a chave de subscrição do recurso para instanciar os contentores do serviço cognitivo.

1. Crie um recurso do Azure no portal do Azure. Se quiser pré-visualizar os contentores de rostos, primeiro tem de criar um recurso de Face correspondente no portal do Azure. Para obter mais informações, consulte [início rápido: Criar uma conta dos serviços cognitivos no portal do Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account).

   > [!Note]
   >  O recurso Face ou de imagem digitalizada tem de utilizar o escalão de preço F0.

2. Obtenha a chave de subscrição e o URL de ponto final para o recurso do Azure. Depois de criar o recurso do Azure, tem de utilizar o URL de ponto final e a chave de subscrição desse recurso para instanciar o contentor de rostos, LUIS ou reconhecer texto correspondente para a pré-visualização.

## <a name="create-a-kubernetes-secret"></a>Crie um Kubernetes secreta 

Utilização de take do Kubectl criar comando secreto para aceder ao registo de contentor privado. Substitua **&lt;nome de utilizador&gt;** com o nome de utilizador e **&lt;palavra-passe&gt;** com a palavra-passe fornecida na credencial recebido do Azure Equipe de serviços cognitivo.

```bash  
    kubectl create secret docker-registry <secretName> \
        --docker-server='containerpreview.azurecr.io' \
        --docker-username='<username>' \
        --docker-password='<password>' 
```

## <a name="prepare-a-yaml-configure-file"></a>Preparar um YAML configurar ficheiro

Vai utilizar o YAML configurar o ficheiro para simplificar a implementação do serviço cognitivo num cluster de Kubernetes.

Eis um exemplo YAML configurar o ficheiro para implementar o serviço de Face ao Azure Stack:

```Yaml  
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: <deploymentName>
spec:
  replicas: <replicaNumber>
  template:
    metadata:
      labels:
        app: <appName>
    spec:
      containers:
      - name: <containersName>
        image: <ImageLocation>
        env: 
        - name: EULA
          value: accept 
        - name: Billing
          value: <billingURL> 
        - name: apikey
          value: <apiKey>
        tty: true
        stdin: true
        ports:
        - containerPort: 5000 
      imagePullSecrets:
        - name: <secretName>
---
apiVersion: v1
kind: Service
metadata:
  name: <LBName>
spec:
  type: LoadBalancer
  ports:
  - port: 5000
    targetPort : 5000
    name: <PortsName>
  selector:
    app: <appName>
```

Este YAML configurar ficheiro, utilize o segredo que utilizou para obter as imagens de contentor de serviços cognitivos do Azure Container Registry. E utilize o ficheiro de segredo para implementação de uma réplica específica do contentor. Também é criar um balanceador de carga para se certificar de que os utilizadores podem aceder este serviço externamente.

Detalhes sobre os campos de chave:

| Campo | Notas |
| --- | --- |
| replicaNumber | Define as réplicas iniciais de instâncias para criar. Com certeza pode dimensioná-lo mais tarde após a implementação. |
| ImageLocation | Indica a localização da imagem de contentor do serviço cognitivos específico no ACR. Por exemplo, o serviço de face: `aicpppe.azurecr.io/microsoft/cognitive-services-face` |
| BillingURL |O URL de ponto final que anotou no passo de [criar recursos do Azure](#create-azure-resources) |
| ApiKey | A chave de subscrição indicado no passo de [criar recursos do Azure](#create-azure-resources) |
| SecretName | O nome secreto que anotou no passo de Create secrete para aceder ao registo de contentor privado |

## <a name="deploy-the-cognitive-service"></a>Implementar o serviço cognitivo

Utilização do comando seguinte para implementar os contentores do serviço cognitivo

```bash  
    Kubectl apply -f <yamlFineName>
```
Utilização do comando seguinte para monitorizar a forma como implementa: 
```bash  
    Kubectl get pod – watch
```

## <a name="test-the-cognitive-service"></a>Testar o serviço cognitivo

Acesso a [especificação de OpenAPI](https://swagger.io/docs/specification/about/) (anteriormente conhecido como a especificação Swagger), que descreve as operações suportadas por um contentor instanciado, a partir do **/swagger** URI relativo para esse contentor. Por exemplo, o URI seguinte fornece acesso a especificação de OpenAPI para o contentor de análise de sentimentos foi instanciado no exemplo anterior:

```HTTP  
http:<External IP>:5000/swagger
```

Pode obter o endereço IP externo do seguinte comando: 

```bash  
    Kubectl get svc <LoadBalancerName>
```

## <a name="try-the-services-with-python"></a>Experimentar os serviços com Python

Pode tentar validar os serviços cognitivos no seu Azure Stack executando alguns scripts de Python simples. Existem exemplos oficiais de início rápido de Python para [de imagem digitalizada](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home), [Face](https://docs.microsoft.com/azure/cognitive-services/face/overview), e [análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview), e [compreensão de idiomas](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto) ( LUIS) para sua referência.

Existem duas coisas necessidade, ter em mente para tornar a aplicação de Python que executar os serviços executados em contentores: 
1. Os serviços cognitivos no contentor não é necessário sub chaves para autenticação, mas ainda temos de qualquer cadeia de caracteres de entrada como um marcador de posição para satisfazer o SDK. 
2. Substitua o base_URL com o endereço de IP do ponto final de serviço real 

Eis um exemplo Python script utilizado Face serviços SDK de Python para detetar e quadro rostos numa imagem. 

```Python  
import cognitive_face as CF

# Cognitive Services in container do not need sub keys for authentication
# Keep the invalid key to satisfy the SDK inputs requirement. 
KEY = '0'  #  (keeping the quotes in place).
CF.Key.set(KEY)

# Get your actual Ip Address of service endpoint of your cognitive service on Azure Stack
BASE_URL = 'http://<svc IP Address>:5000/face/v1.0/'  
CF.BaseUrl.set(BASE_URL)

# You can use this example JPG or replace the URL below with your own URL to a JPEG image.
img_url = 'https://raw.githubusercontent.com/Microsoft/Cognitive-Face-Windows/master/Data/detection1.jpg'
faces = CF.face.detect(img_url)
print(faces)

```

## <a name="next-steps"></a>Passos Seguintes

[Como instalar e executar contentores de API de imagem digitalizada.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/computer-vision-how-to-install-containers)

[Como instalar e executar contentores da Face API](https://docs.microsoft.com/azure/cognitive-services/face/face-how-to-install-containers)

[Como instalar e executar contentores de API de análise de texto](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers)

[Como instalar e executar contentores de compreensão de linguagem (LIUS)](https://docs.microsoft.com/azure/cognitive-services/luis/luis-container-howto)
