---
title: Como usar o Text Analytics para a saúde
titleSuffix: Azure Cognitive Services
description: Saiba como extrair e rotular informações médicas de texto clínico não estruturado com Text Analytics para a saúde.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: d9517eef8976e79db21fbe552861d0d59923e8ba
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173954"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Como: Utilizar o Text Analytics para a saúde (pré-visualização)

> [!NOTE]
> O Text Analytics para o recipiente de saúde foi recentemente atualizado. Veja [as novidades](../whats-new.md) para mais informações sobre as recentes alterações. Lembre-se de puxar o recipiente mais recente para utilizar as atualizações listadas.

> [!IMPORTANT] 
> Text Analytics for health é uma capacidade de pré-visualização fornecida "AS IS" e "WITH ALL FAULTS". Como tal, **o Text Analytics for health (pré-visualização) não deve ser implementado ou implantado em qualquer utilização de produção.** Texto Analytics para a saúde não é destinado ou disponibilizado para uso como dispositivo médico, suporte clínico, ferramenta de diagnóstico ou outra tecnologia destinada a ser usada no diagnóstico, cura, mitigação, tratamento ou prevenção de doenças ou outras condições, e nenhuma licença ou direito é concedido pela Microsoft para usar esta capacidade para tais fins. Esta capacidade não é concebida ou destina-se a ser implementada ou implementada como um substituto de aconselhamento médico profissional ou de opinião de cuidados de saúde, diagnóstico, tratamento ou julgamento clínico de um profissional de saúde, e não deve ser usada como tal. O cliente é o único responsável por qualquer utilização de Text Analytics para a saúde. A Microsoft não garante que o Text Analytics para a saúde ou quaisquer materiais fornecidos em conexão com a capacidade seja suficiente para qualquer finalidade médica ou de outra forma satisfaça os requisitos de saúde ou médico de qualquer pessoa. 


Text Analytics for health é um serviço contentorizado que extrai e rotula informações médicas relevantes de textos não estruturados, tais como notas de médico, resumos de descarga, documentos clínicos e registos de saúde eletrónicos.  

## <a name="features"></a>Funcionalidades

O Text Analytics for health container realiza atualmente Reconhecimento de Entidade Nomeada (NER), extração de relação, negação de entidade e ligação de entidade para texto em língua inglesa no seu próprio ambiente de desenvolvimento que satisfaz os seus requisitos específicos de segurança e governação de dados.

#### <a name="named-entity-recognition"></a>[Reconhecimento de Entidades Nomeadas](#tab/ner)

O Reconhecimento de Entidades com nome deteta palavras e frases mencionadas em texto não estruturado que podem ser associadas a um ou mais tipos semânticos, tais como diagnóstico, nome da medicação, sintoma/sinal ou idade.

> [!div class="mx-imgBorder"]
> ![NER de Saúde](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[Extração de relação](#tab/relation-extraction)

A extração de relação identifica ligações significativas entre conceitos mencionados em texto. Por exemplo, uma relação de "tempo de condição" é encontrada associando um nome de condição com uma hora. 

> [!div class="mx-imgBorder"]
> ![Saúde RE](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[Ligar à Entidade](#tab/entity-linking)

Entidade Que Liga entidades distintas associando entidades nomeadas mencionadas em texto a conceitos encontrados numa base de dados pré-indefinida de conceitos. Por exemplo, o Sistema Unificado de Língua Médica (UMLS).

> [!div class="mx-imgBorder"]
> ![Saúde EL](../media/ta-for-health/health-entity-linking.png)

Texto Analytics para suportes de saúde ligados aos vocabulários biomédicos e de saúde encontrados no Sistema De Língua Médica Unificada[(UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)) Metathesaurus Knowledge Source.

#### <a name="negation-detection"></a>[Deteção de Negação](#tab/negation-detection) 

O significado do conteúdo médico é altamente afetado por modificadores como a negação, que pode ter implicações críticas se for diagnosticado erradamente. Texto Analytics para saúde suporta a deteção de negação para as diferentes entidades mencionadas no texto. 

> [!div class="mx-imgBorder"]
> ![Saúde NEG](../media/ta-for-health/health-negation.png)

---

Consulte as [categorias de entidades devolvidas](../named-entity-types.md?tabs=health) pela Text Analytics para obter uma lista completa de entidades apoiadas.

## <a name="supported-languages"></a>Linguagens suportadas

O Texto Analytics para a saúde só suporta documentos em inglês.

## <a name="request-access-to-the-container-registry"></a>Solicitar acesso ao registo de contentores

Preencha e envie o formulário de [pedido de solicitação dos recipientes dos Serviços Cognitivos](https://aka.ms/csgate) para solicitar o acesso ao contentor. Atualmente não será cobrado para Text Analytics para uso de saúde. 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>Instale o recipiente

Existem várias formas de instalar e executar o recipiente. 

- Utilize o [portal Azure](text-analytics-how-to-install-containers.md?tabs=healthcare) para criar um recurso Text Analytics e use o Docker para obter o seu recipiente.
- Utilize os seguintes scripts PowerShell e [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) para automatizar a configuração do contentor de implantação de recursos.

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Instale o recipiente utilizando a Azure Web App para contentores

A azure [Web App for Containers](https://azure.microsoft.com/services/app-service/containers/) é um recurso Azure dedicado a executar contentores na nuvem. Traz capacidades fora da caixa, tais como autoscaling, suporte de contentores estivadores e composição de estiva, suporte HTTPS e muito mais.

> [!NOTE]
> Utilizando a App Web Azure, obterá automaticamente um domínio sob a forma de `<appservice_name>.azurewebsites.net`

Execute este script PowerShell utilizando o CLI Azure para criar uma Aplicação Web para Contentores, utilizando a sua subscrição e a imagem do recipiente em HTTPS. Aguarde que o script esteja completo (aproximadamente 25-30 minutos) antes de submeter o primeiro pedido.

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Instale o recipiente utilizando a instância do contentor Azure

Também pode utilizar uma Instância de Contentores Azure (ACI) para facilitar a implantação. O ACI é um recurso que permite executar contentores Docker a pedido num ambiente Azure gerido e sem servidor. 

Veja [como utilizar as instâncias do contentor Azure](text-analytics-how-to-use-container-instances.md) para etapas na implantação de um recurso ACI utilizando o portal Azure. Também pode utilizar o script abaixo do PowerShell utilizando o Azure CLI, que criará um ACI na sua subscrição utilizando a imagem do recipiente.  Aguarde que o script esteja completo (aproximadamente 25-30 minutos) antes de submeter o primeiro pedido.  Devido ao limite do número máximo de CPUs por recurso ACI, não selecione esta opção se espera submeter mais de 5 documentos grandes (aproximadamente 5000 caracteres cada) por pedido.
Consulte o artigo de apoio regional da [ACI](https://docs.microsoft.com/azure/container-instances/container-instances-region-availability) para obter informações sobre a disponibilidade. 

> [!NOTE] 
> As instâncias do recipiente Azure não incluem suporte HTTPS para os domínios construídos. Se precisar de HTTPS, terá de o configurar manualmente, incluindo a criação de um certificado e o registo de um domínio. Pode encontrar instruções para o fazer com o NGINX abaixo.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Conectividade ACI segura

Por predefinição, não existe qualquer garantia quando se utiliza a ACI com API de contentores. Isto porque normalmente os contentores funcionam como parte de uma cápsula que é protegida do exterior por uma ponte de rede. No entanto, pode modificar um recipiente com um componente frontal, mantendo o ponto final do recipiente em privado. Os exemplos a seguir usam [o NGINX](https://www.nginx.com) como porta de entrada para suportar a autenticação HTTPS/SSL e o certificado de cliente.

> [!NOTE]
> NGINX é um servidor HTTP de código aberto e de alto desempenho e procuração. Um recipiente NGINX pode ser utilizado para pôr termo a uma ligação TLS para um único recipiente. São também possíveis soluções de rescisão TLS baseadas em ingresss em NGINX mais complexas.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Configurar o NGINX como um portal de entrada

O NGINX utiliza [ficheiros de configuração](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) para ativar funcionalidades em tempo de execução. Para permitir a rescisão de TLS para outro serviço, deve especificar um certificado SSL para encerrar a ligação TLS e  `proxy_pass` especificar um endereço para o serviço. Uma amostra é fornecida abaixo.


> [!NOTE]
> `ssl_certificate` espera que um caminho seja especificado dentro do sistema de ficheiros local do contentor NGINX. O endereço especificado `proxy_pass` deve estar disponível na rede do contentor NGINX.

O recipiente NGINX carregará todos os ficheiros no `_.conf_` que são montados `/etc/nginx/conf.d/` no caminho de configuração HTTP.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Exemplo Docker compor arquivo

O exemplo abaixo mostra como um ficheiro [de composição de estiva](https://docs.docker.com/compose/reference/overview) pode ser criado para implantar o NGINX e o Text Analytics para recipientes de saúde:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Para iniciar este ficheiro de composição do Docker, execute o seguinte comando a partir de uma consola ao nível da raiz do ficheiro:

```bash
docker-compose up
```

Para mais informações, consulte a documentação da NGINX sobre a rescisão do [NGINX SSL](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).


## <a name="example-api-request"></a>Exemplo pedido de API
O contentor fornece APIs de ponto final de predição de consulta com base em REST.  Também fornecemos uma ferramenta de visualização no recipiente que é acessível por anexar a **demonstração** ao ponto final do recipiente, por exemplo:

```bash
http://<serverURL>:5000/demo
```

Utilize o pedido de cURL de exemplo abaixo para submeter uma consulta ao recipiente que implementou substituindo a `serverURL` variável pelo valor adequado.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

O seguinte JSON é um exemplo de um ficheiro JSON anexado ao Text Analytics para o corpo POSTAL do pedido de API de saúde:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>Corpo de resposta da API

O seguinte JSON é um exemplo do Text Analytics para o organismo de resposta da API em saúde:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>Saída de deteção de negação

Ao utilizar a deteção de negação, em alguns casos, um único termo de negação pode abordar vários termos ao mesmo tempo. A negação de uma entidade reconhecida está representada na produção JSON pelo valor booleano da `isNegated` bandeira:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Saída de extração de relação

A saída de extração de relação contém referências URI à *origem* da relação, e o seu *alvo*. As entidades com o papel de relação `ENTITY` são atribuídas ao `target` campo. As entidades com o papel de relação `ATTRIBUTE` são atribuídas ao `source` campo. As relações de abreviatura contêm campos e campos bidirecionais, `source` `target` e `bidirectional` serão definidas para `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Ver também

* [Descrição geral da Análise de Texto](../overview.md)
* [Categorias de entidades nomeadas](../named-entity-types.md)
* [Novidades](../whats-new.md)
