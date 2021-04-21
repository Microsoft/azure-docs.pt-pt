---
title: Configure a cache conectada da Microsoft para atualização do dispositivo para a | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Visão geral da Cache Conectada da Microsoft para atualização de dispositivos para Azure IoT Hub
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 6e7b8d567034cc9557a2d9fcec4afbffa878cf75
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811836"
---
# <a name="configure-microsoft-connected-cache-for-device-update-for-azure-iot-hub"></a>Configure a cache conectada da Microsoft para atualização do dispositivo para o Azure IoT Hub

A Cache Conectada da Microsoft é implantada nas portas do Azure IoT Edge como módulo de borda Azure IoT. Tal como outros módulos Azure IoT Edge, as variáveis ambientais de implementação do módulo MCC e as opções de criação de contentores são usadas para configurar os módulos de Cache Conectados da Microsoft.  Esta secção define as variáveis ambientais e o contentor cria opções que são necessárias para que um cliente implemente com sucesso o módulo cache conectado da Microsoft para utilização por Device Update for Azure IoT Hub.

## <a name="microsoft-connected-cache-azure-iot-edge-module-deployment-details"></a>Detalhes de implementação do módulo Microsoft Connected Cache Azure IoT Edge

O nome do módulo Cache Ligado à Microsoft está ao critério do administrador. Não existem outros módulos ou interações de serviço que dependam do nome do módulo para comunicação. Além disso, a relação parental dos servidores Microsoft Connected Cache não depende deste nome do módulo, mas sim do endereço FQDN ou Ip do gateway Azure IoT Edge que foi configurado como discutido anteriormente.

As variáveis de ambiente do módulo de borda IoT connected da Microsoft São utilizadas para passar informações básicas de identidade do módulo e configurações de módulos funcionais para o recipiente.

| Nome da Variável                 | Formato de valor                           | Obrigatório/Opcional | Funcionalidade                                    |
| ----------------------------- | ---------------------------------------| ----------------- | ------------------------------------------------ |
| CUSTOMER_ID                   | Azure Subscrição ID GUID             | Necessário          | Esta é a chave do cliente, que fornece seguro<br>autenticação do nó de cache para a otimização da entrega<br>Os serviços.<br>Necessário para que o módulo funcione. |
| CACHE_NODE_ID                 | Cache Nó ID GUID                     | Necessário          | Identifica exclusivamente a Cache Conectada à Microsoft<br>nó para serviços de otimização de entrega.<br>Exigido em ordem<br> para o módulo funcionar. |
| CUSTOMER_KEY                  | Guia da Chave do Cliente                     | Necessário          | Esta é a chave do cliente, que fornece seguro<br>autenticação do nó de cache para serviços de otimização de entrega.<br>Necessário para que o módulo funcione.|
| STORAGE_ *N* _SIZE_GB           | Onde N é a unidade de cache   | Necessário          | Especifique até 9 unidades para o conteúdo da cache e especifique o espaço máximo em<br>Gigabytes para alocar para conteúdo em cada unidade de cache. Exemplos:<br>STORAGE_1_SIZE_GB = 150<br>STORAGE_2_SIZE_GB = 50<br>O número da unidade deve corresponder aos valores de ligação da unidade de cache especificados<br>no Recipiente Criar Opção MicrosoftConnectedCache *N* valor<br>O tamanho mínimo da cache é de 10GB.|
| UPSTREAM_HOST                 | FQDN/IP                                | Opcional          | Este valor pode especificar um Microsoft Ligado a montante<br>Nó cache que funciona como um proxy se o nó cache conectado<br> está desligado da internet. Esta definição é usada para suportar<br> o cenário de IoT aninhado.<br>**Nota:** O Microsoft Connected Cache ouve na porta padrão 80.|
| UPSTREAM_PROXY                | FQDN/IP:PORT                           | Opcional          | O representante da internet de saída.<br>Este também poderia ser o representante da OT DMZ se uma rede ISA 95. |
| CACHEABLE_CUSTOM_ *N* _HOST     | ANFITRIÃO/IP<br>FQDN                        | Opcional          | Obrigado a suportar repositórios de pacotes personalizados.<br>Os repositórios podem ser hospedados localmente ou na internet.<br>Não há limite para o número de anfitriões personalizados que podem ser configurados.<br><br>Exemplos:<br>Nome = valor CACHEABLE_CUSTOM_1_HOST = packages.foo.com<br> Nome = valor CACHEABLE_CUSTOM_2_HOST = packages.bar.com    |
| CACHEABLE_CUSTOM_ *N* _CANONICAL| Alias                                  | Opcional          | Obrigado a suportar repositórios de pacotes personalizados.<br>Este valor pode ser usado como pseudónimo e será usado pelo servidor cache para referência<br>diferentes nomes DNS. Por exemplo, o nome de anfitrião do conteúdo repositório pode ser packages.foo.com,<br>mas para diferentes regiões poderia haver um prefixo adicional que é adicionado ao nome hospedeiro<br>Como westuscdn.packages.foo.com e eastuscdn.packages.foo.com.<br>Ao definir o pseudónimo canónico, certifique-se de que o conteúdo não é duplicado<br>para conteúdos provenientes do mesmo anfitrião, mas diferentes fontes de CDN.<br>O formato do valor canónico não é importante, mas deve ser exclusivo do hospedeiro.<br>Pode ser mais fácil definir o valor para corresponder ao valor do hospedeiro.<br><br>Exemplos baseados em exemplos de anfitriões personalizados acima:<br>Nome = valor CACHEABLE_CUSTOM_1_CANONICAL = foopackages<br> Nome = valor CACHEABLE_CUSTOM_2_CANONICAL = packages.bar.com  |
| IS_SUMMARY_PUBLIC             | Verdadeiro ou Falso                          | Opcional          | Permite a visualização do relatório sumário na rede local ou na internet.<br>A utilização de uma chave API (discutida posteriormente) é necessária para ver o relatório de resumo se definido como verdadeiro. |
| IS_SUMMARY_ACCESS_UNRESTRICTED| Verdadeiro ou Falso                          | Opcional          | Permite a visualização de relatório sumário na rede local ou na internet sem<br>utilização da chave API a partir de qualquer dispositivo da rede. Use se não quiser bloquear o acesso<br>para visualizar os dados do resumo do servidor cache através do navegador. |
            
## <a name="microsoft-connected-cache-azure-iot-edge-module-container-create-options"></a>Recipiente de módulos Microsoft Connected Cache Azure IoT Edge cria opções

O contentor cria opções para a implantação do módulo MCC, proporcionando o controlo das definições relacionadas com o armazenamento e as portas utilizadas pelo módulo MCC. Esta é a lista de variáveis criadas por contentores necessários para implantar MCC.

### <a name="container-to-host-os-drive-mappings"></a>Recipiente para hospedar mapeamentos de unidade de SO

É necessário mapear o local de armazenamento do contentor para o local de armazenamento no disco.< até nove locais podem ser especificados.

>[!Note]
>O número da unidade deve corresponder aos valores de ligação da unidade de cache especificados na variável ambiente STORAGE_ *valor N* _SIZE_GB, ```/MicrosoftConnectedCache*N*/:/nginx/cache*N*/```

### <a name="container-to-host-tcp-port-mappings"></a>Recipiente para hospedar mapeamentos portuários TCP

Esta opção especifica a porta http da máquina externa que a MCC ouve para pedidos de conteúdo. O HostPort predefinido é a porta 80 e outras portas não são suportadas neste momento, uma vez que o cliente da ADU faz pedidos no porto 80 hoje. A porta 8081 da TCP é a porta de contentores interna que o MCC ouve e não pode ser alterada.

### <a name="container-service-tcp-port-mappings"></a>Mapeamentos de porta TCP de serviço de contentores

O módulo Cache Conectado microsoft tem um serviço .NET Core, que é utilizado pelo motor de cache para várias funções.

>[!Note]
>Para suportar a Azure IoT Nested Edge, o HostPort não deve ser definido para 5000 porque o módulo de procuração de registo já está a ouvir na porta de acolhimento 5000.


Opções de criação de recipiente de amostra

```json
{
    "HostConfig": {
        "Binds": [
            "/microsoftConnectedCache1/:/nginx/cache1/"
        ],
        "PortBindings": {
            "8081/tcp": [
                {
                    "HostPort": "80"
                }
            ],
            "5000/tcp": [
                {
                    "HostPort": "5100"
                }
            ]
        }
    }
}
```

## <a name="microsoft-connected-cache-summary-report"></a>Relatório de resumo da Cache conectado da Microsoft

O relatório resumo é atualmente a única maneira de um cliente visualizar dados de cache para as instâncias microsoft Connected Cache implementadas nas portas de gateways Azure IoT Edge. O relatório é gerado em intervalos de 15 segundos e inclui estatísticas médias para o período, bem como estatísticas agregadas para a vida útil do módulo. As principais estatísticas em que os clientes estarão interessados são:

* **hitBytes** - Esta é a soma dos bytes entregues que vieram diretamente de cache.
* **missBytes** - Esta é a soma dos bytes entregues que o Microsoft Connected Cache teve de descarregar a partir do CDN para ver a cache.
* **eggressBytes** - Esta é a soma de hitBytes e missBytes e é o bytes total entregue aos clientes.
* **hitRatioBytes** - Esta é a razão de hitBytes para egressBytes.  Se 100% dos eggressBytes entregues num período fossem iguais aos hitBytes este seria 1, por exemplo.


O relatório resumo está disponível no `http://<FQDN/IP of Azure IoT Edge Gateway hosting MCC>:5001/summary` Substitua \<Azure IoT Edge Gateway IP\> pelo endereço IP ou nome de anfitrião do seu gateway IoT Edge. (ver detalhes variáveis ambientais para informações sobre a visibilidade deste relatório).