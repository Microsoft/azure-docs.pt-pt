---
title: Apoio à Zona de Disponibilidade para Ambientes de Serviço de Aplicações
description: Saiba como implementar os seus Ambientes de Serviço de Aplicações para que as suas aplicações sejam redundantes em zonas.
author: ccompy
ms.assetid: 24e3e7eb-c160-49ff-8d46-e947818ef186
ms.topic: article
ms.date: 07/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 383b5bb5c7295fe54efda883e47b9b2338286de5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98624730"
---
# <a name="availability-zone-support-for-app-service-environments"></a>Apoio à Zona de Disponibilidade para Ambientes de Serviço de Aplicações

Os Ambientes de Serviço de Aplicações (ASE) podem ser implantados em Zonas de Disponibilidade (AZ).  Os clientes podem implantar um balançador interno de carga (ILB) ASEs numa AZ específica dentro de uma região do Azure. Se fixar o seu ILB ASE a um AZ específico, os recursos utilizados por um ILB ASE serão fixados ao AZ especificado ou implantados de forma redundante.  

Um ILB ASE que é explicitamente implantado num AZ é considerado um recurso zonal porque o ILB ASE está preso a uma zona específica. As seguintes dependências do ILB ASE serão fixadas à zona especificada:

- o endereço IP do equilibrador interno de carga do ASE
- os recursos computacional utilizados pela ASE para gerir e executar aplicações web

O armazenamento remoto de ficheiros para aplicações web implantados numa zonal ILB ASE utiliza o Zone Redundant Storage (ZRS).

A menos que os passos descritos neste artigo sejam seguidos, as ASEs ILB não são automaticamente implantadas de forma zonal. Não é possível fixar um ASE Externo com um endereço IP público a uma zona de disponibilidade específica. 

AsES zonais ILB podem ser criadas em qualquer uma das seguintes regiões:

- Leste da Austrália
- Canadá Central
- E.U.A. Central
- E.U.A. Leste
- E.U.A. Leste 2
- Leste dos EUA 2 (EUAP)
- França Central 
- Leste do Japão
- Europa do Norte
- Europa Ocidental
- Sudeste Asiático
- Sul do Reino Unido
- E.U.A. Oeste 2

As aplicações implantadas numa zonal ILB ASE continuarão a funcionar e a servir o tráfego nessa ASE, mesmo que outras zonas da mesma região sofram uma paragem.  É possível que comportamentos não-tempo de execução, incluindo; o escalonamento do plano de serviço de aplicação, criação de aplicações, configuração de aplicações e publicação de aplicações podem ainda ser impactados por uma paragem em outras zonas de disponibilidade. A implantação de zona-fixa de uma zonal ILB ASE apenas garante a continuação do tempo de utilização das aplicações já implementadas.

## <a name="how-to-deploy-an-app-service-environment-in-an-availability-zone"></a>Como implementar um ambiente de serviço de aplicações numa zona de disponibilidade ##

AsE zonal ILB devem ser criadas com modelos ARM. Uma vez que um ILB ASE zonal é criado através de um modelo ARM, pode ser visto e interagido através do portal Azure e CLI.  Um modelo ARM só é necessário para a criação inicial de um ILB ASE zonal.

A única alteração necessária num modelo ARM para especificar uma zonal ILB ASE é a nova ***zonas** _ propriedade. A propriedade _ *_zonas_** deve ser definida como um valor de "1", "2" ou "3" dependendo da zona de disponibilidade lógica a que o ILB ASE deve ser fixado.

O exemplo do corte do modelo ARM abaixo mostra a propriedade das novas ***zonas*** especificando que o ILB ASE deve ser fixado à zona 2.

```
   "resources": [
      {
         "type": "Microsoft.Web/hostingEnvironments",
         "kind": "ASEV2",
         "name": "yourASENameHere",
         "apiVersion": "2015-08-01",
         "location": "your location here",
         "zones": [
            "2"
         ],
         "properties": {
         "name": "yourASENameHere",
         "location": "your location here",
         "ipSslAddressCount": 0,
         "internalLoadBalancingMode": "3",
         "dnsSuffix": "contoso-internal.com",
         "virtualNetwork": {
             "Id": "/subscriptions/your-subscription-id-here/resourceGroups/your-resource-group-here/providers/Microsoft.Network/virtualNetworks/your-vnet-name-here",
             "Subnet": "yourSubnetNameHere"
          }
         }
      }
    ]
```

Para tornar a zona de aplicações redundante, precisa de implementar duas ASEs zonais do ILB. As duas ASEs zonais ILB devem estar em zonas de disponibilidade separadas. Em seguida, tem de implementar as suas aplicações em cada uma das ASEs ILB. Depois de as suas aplicações serem criadas, precisa de configurar uma solução de equilíbrio de carga. A solução recomendada é implantar uma [zona redundante Gateway de aplicação](../../application-gateway/application-gateway-autoscaling-zone-redundant.md) a montante das ASEs zonais do ILB. 

## <a name="in-region-data-residency"></a>Na Residência de Dados da Região ##

AsES ILB implantadas numa zona de disponibilidade apenas armazenarão dados de clientes na região onde o zonal ILB ASE foi implantado. Tanto o conteúdo de ficheiros do site como as configurações e segredos fornecidos pelo cliente armazenados no Serviço de Aplicações permanecem na região onde o zonal ILB ASE está implantado.

Os clientes asseguram a residência de dados de uma região única seguindo os passos descritos anteriormente na secção "Como Implementar um Ambiente de Serviço de Aplicações numa Zona de Disponibilidade". Ao configurar um Ambiente de Serviço de Aplicações de acordo com estas etapas, um Ambiente de Serviço de Aplicações implantado numa zona de disponibilidade satisfaz os requisitos de residência de dados da região, incluindo os especificados no [Azure Trust Center](https://azuredatacentermap.azurewebsites.net/).

Os clientes podem validar que um Ambiente de Serviço de Aplicações está devidamente configurado para armazenar dados numa única região, seguindo estes passos: 

1. Utilizando [o Resource Explorer](https://resources.azure.com), navegue para o recurso ARM para o Ambiente de Serviço de Aplicações.  AsEs estão listadas em *fornecedores/Microsoft.Web/hostingEnvironments*.
2. Se uma propriedade de *zonas* existe na vista da sintaxe ARM JSON, e contém uma única matriz JSON valorizada com um valor de "1", "2" ou "3", então o ASE é implementado zonalmente e os dados do cliente permanecem na mesma região.
2. Se uma propriedade *de zonas* não existir, ou se o imóvel não tiver valor de zona válido como especificado anteriormente, então o ASE não é implantado zonalmente, e os dados do cliente não são armazenados exclusivamente na mesma região.
